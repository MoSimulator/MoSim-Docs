---
sidebar_position: 5
id: bufferedsetpoints
title: Buffered Setpoints
sidebar_label: Buffered Setpoints
---

When a driver selects a scoring pose during an intake handoff, moving the mechanisms immediately can interrupt the sequence. Buffer the requested setpoint, finish the handoff, then apply the request automatically.

Reefscape Orbit and Robonauts use this pattern. It stores one pending request: if the driver selects another scoring pose before the sequence finishes, the newer request replaces the previous one.

## Setting Up

This example is adapted for a robot derived from `ReefscapeRobotBase`, using its `CurrentSetpoint` and `SetState(ReefscapeSetpoints)` API. Add `using Games.Reefscape.Enums;` and place these members inside the robot class.

```cs
private ReefscapeSetpoints? bufferedSetpoint;
private bool intakeSequenceRunning;

private void UpdateBufferedSetpoint()
{
    if (intakeSequenceRunning)
    {
        // Save scoring requests before forcing the handoff pose.
        // Internal Intake/Stow updates must not overwrite the request.
        if (CurrentSetpoint != ReefscapeSetpoints.Intake &&
            CurrentSetpoint != ReefscapeSetpoints.Stow)
        {
            bufferedSetpoint = CurrentSetpoint;
        }

        if (CurrentSetpoint != ReefscapeSetpoints.Intake)
        {
            SetState(ReefscapeSetpoints.Intake);
        }

        return;
    }

    if (!bufferedSetpoint.HasValue) return;

    ReefscapeSetpoints requested = bufferedSetpoint.Value;
    bufferedSetpoint = null;

    if (CurrentSetpoint != requested)
    {
        SetState(requested);
    }
}

private void ResetSetpointBuffer()
{
    bufferedSetpoint = null;
    intakeSequenceRunning = false;
}
```

The nullable enum holds either one pending setpoint or `null`. Clear it when applying the request so it does not keep reapplying the old pose. The equality checks avoid calling `SetState` repeatedly for the same state, which would also overwrite `LastSetpoint` in this base class.

This example temporarily uses the Intake pose, following Robonauts. Use the pose required by the robot's actual handoff sequence.

## Connecting the Intake Sequence

Set `intakeSequenceRunning = true` once when the handoff begins. Do not set it every update merely because the robot holds a piece; a held piece may later move to a scoring state.

In the physics update, check whether the handoff has finished, then process the buffer before selecting the mechanism targets:

```cs
// coralController and coralStowState are existing robot fields.
if (intakeSequenceRunning &&
    coralController.HasPiece() &&
    coralController.currentStateNum == coralStowState.stateNum &&
    coralController.atTarget)
{
    intakeSequenceRunning = false;
}

UpdateBufferedSetpoint();

// Continue with the existing mechanism/setpoint selection here.
```

The existing game piece update still needs to advance the piece through the handoff. The buffer only delays the mechanism pose request; it does not move the piece itself.

Process driver state requests before the buffer captures them. Process the buffer before the mechanisms consume `CurrentSetpoint`. Checking both the expected piece state and `atTarget` prevents resuming a scoring pose while the piece is still moving.

## Example Behavior

1. A piece enters the intake and the handoff starts.
2. The driver selects L3. The buffer saves L3 and keeps the robot in Intake.
3. The driver selects L4 before the handoff finishes. L4 replaces L3.
4. The piece reaches its stow target. The sequence finishes, L4 is applied, and the buffer is cleared.

If no request was buffered, this helper leaves the current pose unchanged. Let the existing state logic select the normal pose after intake.

## Cancellation and Priorities

Call `ResetSetpointBuffer()` during initialization and when the sequence is explicitly cancelled. If a piece is lost or ejected before the completion condition can become true, end the sequence and clear the buffer through that cancellation path. Also reset the actual intake sequence's own tracking as needed.

In this example, Intake and Stow updates do not cancel a pending scoring request. If pressing Stow should cancel it, clear the buffer from the explicit Stow input handler; an internal Stow update cannot distinguish that intent by itself.

Give Climb, emergency actions, and other priority transitions an explicit bypass: cancel the buffer before applying that transition. Narrow which states are eligible for buffering if the robot supports actions that must run immediately.

## Buffers with Command Based

With [Command Robots](../../CommandRobots.md), buffer the next command state and keep the current state running until its sequence is complete. This is an alternative to the enum-based implementation above. The examples here belong inside a robot derived from `ReefscapeCommandRobotBase`; add `using RobotFramework;` for `IRobotState` and `RobotState`.

The transition hooks have different roles:

| Hook | Behavior |
| --- | --- |
| `.OnAdditionalTransition()` | Extends the state when its default or replacement check returns `null`. It cannot block an earlier transition. |
| `.OnCheckTransition()` | Replaces the default transition check. Use it to hold the state during a handoff and consume a buffered request afterward. |

The built-in `IntakeState` normally returns to Stow when Intake is released. An additional transition cannot prevent that, because the default has already selected Stow. Replace the check when the handoff must continue after button release.

### Replacing the Transition Behavior

Keep the existing `.OnMechanisms()` and `.OnGamePiece()` bindings that advance the intake sequence. Add the replacement check to that state's setup:

```cs
InState<IntakeState>()
    .OnCheckTransition(CheckBufferedIntakeTransition)
```

```cs
private IRobotState? bufferedCommandState;
private bool intakeSequenceRunning;

private IRobotState? CheckBufferedIntakeTransition()
{
    // Priority actions cancel the pending request and leave Intake.
    if (ClimbAction.WasTriggered() &&
        !RightStickModifierAction.IsPressed())
    {
        bufferedCommandState = null;
        intakeSequenceRunning = false;
        return new ClimbState(this);
    }

    if (StowAction.WasTriggered())
    {
        bufferedCommandState = null;
        intakeSequenceRunning = false;
        return new StowState(this);
    }

    // A newer scoring request replaces the pending state.
    if (L1Action.WasTriggered()) bufferedCommandState = new L1State(this);
    if (L2Action.WasTriggered()) bufferedCommandState = new L2State(this);
    if (L3Action.WasTriggered()) bufferedCommandState = new L3State(this);
    if (L4Action.WasTriggered()) bufferedCommandState = new L4State(this);

    // Null keeps Intake active, including its mechanism/piece updates.
    if (intakeSequenceRunning) return null;

    if (bufferedCommandState != null)
    {
        IRobotState next = bufferedCommandState;
        bufferedCommandState = null;
        return next;
    }

    return IntakeAction.IsPressed() ? null : new StowState(this);
}
```

Define `intakeSequenceRunning` only once if the robot already tracks it. Set it when the real handoff starts and clear it after the expected piece state reaches its target, as in the completion check earlier on this page. Clear `bufferedCommandState` during initialization and cancellation too. A cancellation that needs physical cleanup must also reset the sequence's own tracking.

This example handles coral L1–L4 requests. Carry over the robot's mode selection, facing checks, and other priority actions where required. In particular, the standard Reefscape transitions can select algae states instead of coral scoring states.

The command tick checks transitions before running mechanism and piece callbacks. Returning `null` lets those callbacks continue. Returning a state performs the transition and skips the normal updates for that tick. If a callback marks the handoff complete, the next transition check consumes the buffer. Do not return a new Intake state every tick to hold it: that would repeatedly exit and enter the state.

### Extending the Sequence with a Custom State

For a sequence with extra stages, create a custom state and keep its stage tracking there. The built-in Reefscape `IntakeState` is sealed, so reuse its bound callbacks through composition when needed. This alternative wraps those callbacks while owning the transition behavior:

```cs
// Replace ExampleRobot with the name of the containing robot class.
protected sealed class BufferedIntakeState : RobotState<ExampleRobot>
{
    private readonly IntakeState intake;

    public BufferedIntakeState(ExampleRobot robot) : base(robot)
    {
        intake = new IntakeState(robot);
    }

    public override IRobotState? CheckTransition() =>
        Robot.CheckBufferedIntakeTransition();

    public override void OnEnter() => intake.OnEnter();
    public override void OnExit()
    {
        Robot.bufferedCommandState = null;
        intake.OnExit();
    }
    public override void OnMechanismUpdate() => intake.OnMechanismUpdate();
    public override void OnGamePieceUpdate() => intake.OnGamePieceUpdate();
}
```

Use this wrapper instead of the Intake transition-hook registration above. Return `new BufferedIntakeState(this)` from the transition that starts the sequence. If the default transition already selects `IntakeState`, replace that entry decision with `.OnCheckTransition()` and preserve its other rules; an additional hook cannot intercept that default selection.

The forwarded entry, mechanism, and game piece methods use the bindings registered with `InState<IntakeState>()`. The built-in Intake state has no exit-binding override, so add any sequence cleanup directly to the wrapper's `OnExit`. The wrapper calls the buffer helper directly for transitions, so it owns that decision. To replace the sequence itself, implement the custom state's mechanism and game piece methods instead of forwarding them, and advance its stages using measured arrival checks. Custom methods must explicitly invoke any fluent bindings they intend to support.

Both approaches produce the same buffer behavior: L3 can be requested during the handoff, L4 can replace it, and the selected state starts after completion. Keep the buffering decision in the transition check so the sequence callbacks can continue running while the request waits.
