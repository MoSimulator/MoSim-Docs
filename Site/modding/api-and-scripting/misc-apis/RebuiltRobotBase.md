---
sidebar_position: 4
id: rebuiltrobotbase
title: Rebuilt Robot Base API
sidebar_label: Rebuilt Robot Base
---

## Get Individual Input Button Values Directly

Each available input is set up as a `MoSimInput` object. This means it has all of the following methods:

- `IsPressed()`
- `WasTriggered()`
- `ChangeTriggered(bool value)`
- `ChangeWasPressed(bool value)`
- `GetWasPressed()`

Using the `MoSimInput` object directly as a boolean value (such as in an if statement) returns the same value as `IsPressed()`. Here is the list of inputs available for Rebuilt:

- `FaceDown`
- `ClimbAction`
- `ManualShoot`
- `AutoPass`
- `DpadUp`
- `DpadL`
- `DpadR`
- `RobotSpecial`
- `RobotSpecial2`
- `RobotSpecial3`
- `OuttakeAction`
- `IntakeAction`
- `StowAction`

## Zone/Location Helpers

### `InNeutralZone()`

Returns a boolean for whether the robot is in the neutral zone or not

### `UnderTrench(Transform point, float distance)`

Returns a boolean for whether the robot is under the trench or not. Both parameters are optional.

- `point` - check another location for being under the trench, often used for shooters that are not centered in the robot. Not setting it or setting it to `null` uses the robot root's transform.
- `distance` - the radius around the given point (or robot root) to check in meters. This is commonly used for turrets. Defaults to 0.

### `InAllianceZone()`

Returns a boolean for whether the robot is in its alliance zone.

## Miscellaneous

### `ShiftActive()`

Returns a boolean for whether the alliance's hub is currently active or not.
