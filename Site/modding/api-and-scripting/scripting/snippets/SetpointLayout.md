---
sidebar_position: 4
id: setpointlayout
title: Setpoint Layout
sidebar_label: Setpoint Layout
---

A setpoint groups the mechanism targets for a robot pose. Instead of assigning every joint throughout the state logic, select a named setpoint and apply its targets from one place.

## Defining the Layout

```cs
[Serializable]
private struct ExampleSetpoint
{
    [Tooltip("Degrees")] public float armAngleDegrees;
    [Tooltip("Degrees")] public float wristAngleDegrees;
    [Tooltip("Inches")] public float slideExtensionInches;
}

[Header("Mechanisms")]
[SerializeField] private GenericJoint armJoint;
[SerializeField] private GenericJoint wristJoint;
[SerializeField] private GenericJoint slideJoint;

[Header("Robot Setpoints")]
[SerializeField] private ExampleSetpoint stow;
[SerializeField] private ExampleSetpoint intake;
[SerializeField] private ExampleSetpoint place;

private float targetArmAngleDegrees;
private float targetWristAngleDegrees;
private float targetSlideExtensionInches;

private void SetSetpoint(ExampleSetpoint setpoint)
{
    targetArmAngleDegrees = setpoint.armAngleDegrees;
    targetWristAngleDegrees = setpoint.wristAngleDegrees;
    targetSlideExtensionInches = setpoint.slideExtensionInches;
}

private void UseSetpoint()
{
    armJoint.SetTargetAngle(targetArmAngleDegrees).withAxis(JointAxis.X);
    wristJoint.SetTargetAngle(targetWristAngleDegrees).withAxis(JointAxis.X);
    slideJoint.SetLinearTarget(targetSlideExtensionInches).withAxis(JointAxis.Y);
}
```

Configure `stow`, `intake`, and `place` in the Inspector, and initialize each joint's PID using the robot's normal setup. Call `SetSetpoint(stow)` during setup so the target fields start at the intended pose.

`SetSetpoint` selects target values. `UseSetpoint` commands the mechanisms and must run regularly in the physics update. Angular targets use degrees; `GenericJoint.SetLinearTarget` uses inches. Document units in the layout so a linear distance is not mistaken for an angle or metre value.

## Using It with States

With [Command Robots](../../CommandRobots.md), select and apply the targets in `.OnMechanisms()`:

```cs
InState<StowState>()
    .OnMechanisms(() =>
    {
        SetSetpoint(stow);
        UseSetpoint();
    });

InState<IntakeState>()
    .OnMechanisms(() =>
    {
        SetSetpoint(intake);
        UseSetpoint();
    });

InState<PlaceState>()
    .OnMechanisms(() =>
    {
        SetSetpoint(place);
        UseSetpoint();
    });
```

If the robot already runs `UseSetpoint` from a shared physics callback, keep only `SetSetpoint` in the state callbacks. Apply the joint targets once per physics step through the chosen arrangement.

## Extending the Layout

Add one field for each mechanism target and copy it in `SetSetpoint`. The struct is a value type, so changing the working target fields does not modify the saved setpoint. This is useful for temporary adjustments such as changing only the wrist after selecting a pose.

Selecting a setpoint does not mean the robot has arrived. Use the robot's joint position checks and tolerances before feeding or releasing a piece. If mechanisms need a particular movement order, add intermediate poses or transition conditions; this basic layout commands all targets together.

Keep piece movement in `.OnGamePiece()` callbacks using the game piece controller. A mechanism setpoint describes joint targets; a `GamePieceState` describes the piece's target location.
