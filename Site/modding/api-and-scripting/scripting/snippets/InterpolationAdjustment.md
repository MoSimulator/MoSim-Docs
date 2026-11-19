---
sidebar_position: 2
id: interpolation
title: Interpolation Adjustment
sidebar_label: Interpolation Adjustment
---

Use interpolation when a shooter needs a different speed or hood angle at different distances. Each `DistanceValue` contains a distance and the output to use there. The system blends between points, so you can tune the shot at a few measured distances.

This page covers table tuning and an optional output trim. The sampling pattern comes from Rebuilt Hightide and Overcharge; the scale and offset fields below are a reusable example added for small per-robot adjustments.

## Setting Up

Add these members inside a `RebuiltRobotBase`-derived robot. Use `UnityEngine`, `Games.Rebuilt.Components`, `RobotFramework.Components`, and `RobotFramework.Enums`.

```cs
[Header("Shot Interpolation")]
[SerializeField] private RebuiltInterpolation flywheelInterpolation;
[SerializeField] private RebuiltInterpolation hoodInterpolation;
[SerializeField] private RollerGroup shooterRollers;
[SerializeField] private GenericJoint hoodJoint;

[Header("Interpolation Adjustment")]
[SerializeField, Min(0f)] private float flywheelScale = 1f;
[SerializeField] private float flywheelOffsetRpm = 0f;
[SerializeField] private float hoodOffsetDegrees = 0f;

private void InitializeShotInterpolation()
{
    flywheelInterpolation.Initialize(this);
    hoodInterpolation.Initialize(this);
}

private void UpdateInterpolatedShot(Vector3 aimTarget)
{
    // Set this step's target before sampling either table.
    flywheelInterpolation.SetCustomTarget(aimTarget);
    hoodInterpolation.SetCustomTarget(aimTarget);

    float baseRpm = flywheelInterpolation.Interpolate();
    float baseAngle = hoodInterpolation.Interpolate();

    float targetRpm = baseRpm * flywheelScale + flywheelOffsetRpm;
    float targetAngle = baseAngle + hoodOffsetDegrees;

    shooterRollers.SetRpm(targetRpm);
    hoodJoint.SetTargetAngle(targetAngle).withAxis(JointAxis.X);
}
```

Initialize both interpolation objects once during robot setup. The hood joint still needs its normal PID initialization. Call `UpdateInterpolatedShot` from the shooting physics update, passing the current world-space aim target. Preserve the robot's existing shooter stop and feed logic.

Populate each object's `interpolationMap` in the Inspector. `RebuiltInterpolation` measures 3D distance in Unity metres, from `transformOverride` when assigned or the robot transform otherwise. Use the same measurement origin when collecting calibration points.

## Adjusting the Table

For the flywheel table, store RPM as each point's `value`. For the hood table, store degrees. These units come from how this snippet consumes the outputs; interpolation itself does not assign output units.

Tune a shot at a fixed distance, record its working output, then repeat at other distances. Use distinct distances and add more points where the output changes quickly. The lookup sorts the table, blends linearly between neighboring points, and holds the nearest endpoint value outside the recorded range. An empty table produces `0`.

While playing, edits to a populated table are picked up by its physics-step cache update. Allow that update before evaluating the change. Transfer useful Play Mode tuning back to the saved configuration after testing.
