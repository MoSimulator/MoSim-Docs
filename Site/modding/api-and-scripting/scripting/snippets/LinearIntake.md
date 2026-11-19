---
sidebar_position: 1
id: linearintake
title: Linear Intake
sidebar_label: Linear Intake
---

A sliding intake uses a linear target instead of a pivot angle.

The basic snippet below keeps just the deploy/retract movement and rollers.

## Setting Up

Use `UnityEngine`, `RobotFramework.Components`, `RobotFramework.Enums`, and `RobotFramework.Controllers.PidSystems`.

```cs
[Header("Linear Intake")]
[SerializeField] private GenericJoint intakeJoint;
[SerializeField] private PidConstants intakePidConstants;
[SerializeField] private RollerGroup intakeRollers;
[SerializeField] private JointAxis intakeAxis = JointAxis.X;

[SerializeField, Tooltip("Inches")]
private float intakeStowed = 0f;

[SerializeField, Tooltip("Inches")]
private float intakeExtended = 0.35f;

[SerializeField] private float intakeRpm = 2000f;

private void InitializeLinearIntake()
{
    intakeJoint.SetPid(intakePidConstants);
}

private void UpdateLinearIntake(bool deployed, bool runRollers)
{
    float targetInches = deployed
        ? intakeExtended
        : intakeStowed;

    intakeJoint.SetLinearTarget(targetInches)
        .withAxis(intakeAxis);

    intakeRollers.SetRpm(runRollers ? intakeRpm : 0f);
}
```

Call `InitializeLinearIntake()` once during initialization. Call the update from the mechanism's regular physics update or scheduler callback:

```cs
bool intakeRequested = IntakeAction.IsPressed();
UpdateLinearIntake(intakeRequested, intakeRequested);
```

This example retracts when the input is released. For a latched deployment, pass your stored deployment boolean instead. Movement and roller inputs are separate so the intake can remain deployed with the rollers stopped.

## Units and Tuning

`SetLinearTarget` takes **inches**. `GetAxisLocation(intakeAxis)` returns **meters**.

The example extension and RPM are placeholders. Configure the joint's sliding axis, starting offset, travel limits, PID values, and roller direction for the mechanism. The target coordinates must agree with the joint's offset and direction settings.

For live PID tuning, call `intakeJoint.UpdatePid(intakePidConstants)` from the existing PID update callback. Keep commanding the target each physics update; setting the target once does not replace that update loop.
