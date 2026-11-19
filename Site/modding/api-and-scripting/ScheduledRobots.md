---
sidebar_position: 1
id: scheduledrobots
title: Scheduled Robots
sidebar_label: Scheduled Robots
---

`ScheduledRobotBase` organizes robot updates into registered functions. Instead of putting all mechanism and game piece logic into one update method, register each function with the scheduler and choose when it runs.

Game-specific bases can build on this system. `RebuiltRobotBase` inherits from it directly, while [Command Robots](./CommandRobots.md) uses it through `StateCommandRobotBase`.

## Usage

Override `Init()` to configure components and register subsystem functions. The base creates `Scheduler` before calling `Init()`, so the scheduler is ready there.

The following members go inside a robot derived from `RebuiltRobotBase`. Use `UnityEngine`, `RobotFramework.Components`, `RobotFramework.Controllers.PidSystems`, and `RobotFramework.Enums`.

```cs
[SerializeField] private GenericJoint intakeJoint;
[SerializeField] private PidConstants intakePidConstants;
[SerializeField] private float intakeAngle;
[SerializeField] private float stowAngle;

private float targetIntakeAngle;

protected override void Init()
{
    base.Init();

    intakeJoint.SetPid(intakePidConstants);
    targetIntakeAngle = stowAngle;

    Scheduler.AddSubsystems(UpdateTargets, UpdateJoints);
    Scheduler.AddLateRecursive(UpdatePids);
}

private void UpdateTargets()
{
    targetIntakeAngle = IntakeAction.IsPressed() ? intakeAngle : stowAngle;
}

private void UpdateJoints()
{
    intakeJoint.SetTargetAngle(targetIntakeAngle).withAxis(JointAxis.X);
}

private void UpdatePids()
{
    intakeJoint.UpdatePid(intakePidConstants);
}
```

`AddSubsystems` accepts multiple no-argument functions. They run in registration order, so `UpdateTargets` runs before `UpdateJoints`. Separate registration calls append to the same list.

Register functions once during setup. Registering the same function again adds another invocation; there is no duplicate filtering or public removal method in this scheduler.

### Choosing an Update

| Registration | When it runs | Typical use |
| --- | --- | --- |
| `AddSubsystems(...)` | Each `FixedUpdate`, while the game manager's robot state is `Enabled`. | Mechanism targets, intake, shooter, and piece handling. |
| `AddProtectedSystem(...)` | Each `FixedUpdate`, after normal subsystems, regardless of robot enabled state. | State management or bookkeeping that must continue while disabled. |
| `AddRecursive(...)` | Each `Update`, after `RobotBase.Update()`, without a robot-state check. | Logic that specifically needs a render-frame update. |
| `AddLateRecursive(...)` | Each `LateUpdate`, without a robot-state check. | Late-frame updates; robot implementations commonly use this for PID tuning. |

"Protected" means the callback still runs while the robot is disabled. Component-level disabled checks still apply to any joint or motor commands it makes.

"Recursive" means the function runs again on future Unity updates. These callbacks run synchronously on the calling Unity update; they do not create background tasks or invoke themselves recursively. Use physics subsystems for mechanism control rather than depending on rendering frame rate.

## Advanced Usage

### Initialization Order

`Init()` runs from `ScheduledRobotBase.Start()`. Use it for PID setup, initial values, and scheduler registration. Preserve `base.Init()` when overriding it so an intermediate base class can perform its own setup.

Use `LateInit()` for setup that needs other active components to finish their `Start()`. It runs once on the robot's first physics update, before either scheduler physics list runs, even if the robot is disabled.

For example, this Rebuilt dye rotor configures collision ignores in `LateInit()` because joints and rollers build physics components during startup. This adapted example assumes `rotorJoint` and `turretJoint` are assigned `GenericJoint` fields, and uses `MoSimLib.Utils`:

```cs
protected override void LateInit()
{
    base.LateInit();

    Rigidbody rotorBody = rotorJoint.GetRigidbody();
    Rigidbody turretBody = turretJoint.GetRigidbody();
    MoSimLib.Utils.IgnoreCollisionsBetween(rotorBody, turretBody);
}
```

The physics update order is: gyro handling, `LateInit()` on the first step, enabled subsystems, then protected systems. Registration order controls execution within each list; it does not move a protected callback ahead of the enabled subsystem list.

### Sharing Logic and Changing Behavior

Callbacks can also be lambdas. Capture fields when the function runs so it uses the robot's current values:

```cs
// Inside Init(), as an alternative to registering UpdateJoints above.
Scheduler.AddSubsystems(() =>
    intakeJoint.SetTargetAngle(targetIntakeAngle).withAxis(JointAxis.X));
```

For behavior that can be toggled, register one function and check the controlling flag inside it. Re-registering functions every time the mode changes would accumulate callbacks.

If you override `Start`, `FixedUpdate`, `Update`, or `LateUpdate`, preserve the corresponding base call. Those methods create and drive the scheduler; overriding one without its base call skips that part of the lifecycle. A scheduled callback already called by the base should not also be called manually from your override.
