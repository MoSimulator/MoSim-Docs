---
sidebar_position: 0
id: drivesetup
title: Drivetrain Setup
sidebar_label: Drivetrain Setup
---

The first step is to add the `DriveTrain` prefab to the robot's prefab.

<img src="/img/mason/drive/drivetrain_prefab.png" alt="Drivetrain prefab" width="50%"/>

:::note
For Rebuilt, this should be the `Drivetrain Variant` prefab to prevent fuel phasing through wheels.
:::

The next step is to add the `DriveController` script to the root.

<img src="/img/mason/drive/drive_controller.png" alt="Drive Controller component" width="50%"/>

| Field | Parameter |
| --- | --- |
|`Drivetrain Parent` | should be set to the instance of the prefab you added previously. |
| `Drivetrain Type` | should always be set to Swerve *at this point in time*. |
| `Max Velocity` | the max ground speed *reachable*, not the theoretical max speed. |
| `Max Omega` | can be used to set a maximum rotation speed that is below the real max rotation speed of a robot. |
| `Motor Type` | set this to the drive motor the team uses. Use `_FOC` for teams that use FOC. |
| `Config` | contains motor configuration settings, such as the velocity PID and current limits. |
| `Real World Weight Lbs` | can be used to make robots have more consistent drive behavior. Should be the match weight (battery + bumper), not inspection weight. |
| `Motors Per Powertrain` | should be set to 1. |
| `Gear Ratio` | set this to the real swerve ratio the team ran. |
| `Wheel Diameter Inches` | the diameter of the wheel in inches. |
| `Wheel Rotational Inertia` | leave this as is. |
| `Mechanical Loss Percent` | set it to 1.5-3.5 based on which feels more correct. |
| `Friction Coefficient` | this should be 1.3 for all wheel types. |
| `Bite Factor` | this is "conditional" traction. Griplock/spiked style are 0.35, nitrile is 0.32, and colson is 0. |
| `Contact Patch Width Inches` | set this to the wheel width, 1-2 based on the modules. |
| `Contact Tolerance` | leave at 0.01. |
| `Lateral Grip Response` | should be left at 0.1. |

## Tuning

If you wish to tune the drive PID precisely, you can use `Tools` → `Debug Plotter`. By enabling a debug box on the drive controller, you are able to see a line plot of outputs.

<img src="/img/mason/drive/debug_plotter.png" alt="Debug plotter" width="80%"/>
