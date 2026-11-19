---
sidebar_position: 3
id: reefscapecommandrobotbase
title: Reefscape Command Robot Base API
sidebar_label: Reefscape Command Robot Base
---

## Get Individual Input Button Values Directly

Each available input is set up as a `MoSimInput` object. This means it has all of the following methods:

- `IsPressed()`
- `WasTriggered()`
- `ChangeTriggered(bool value)`
- `ChangeWasPressed(bool value)`
- `GetWasPressed()`

Using the `MoSimInput` object directly as a boolean value (such as in an if statement) returns the same value as `IsPressed()`. Here is the list of inputs available for Reefscape:

- `L4Action`
- `L3Action`
- `L2Action`
- `L1Action`
- `ClimbAction`
- `AutoAlignLeftAction`
- `AutoAlignRightAction`
- `RobotModeToggleAction`
- `IntakeModeToggleAction`
- `RobotSpecialAction`
- `StowAction`
- `IntakeAction`
- `OuttakeAction`

## Robot Modes

### `CurrentRobotMode`

Returns the robot's current selected game piece mode. Can be:

- `ReefscapeRobotMode.Coral`
- `ReefscapeRobotMode.Algae`

### `CurrentIntakeMode`

Returns the robot's current selected *coral* intake mode. Can be:

- `ReefscapeIntakeMode.Normal`
- `ReefscapeIntakeMode.L1`

### `RequestedIntakeMode`

Returns the coral intake mode the user has requested - Since `CurrentIntakeMode` doesn't change while holding a coral, `RequestedIntakeMode` caches it until no coral is held. Can hold the same values as [`CurrentIntakeMode`](#currentintakemode).

### `SetRobotMode(ReefscapeRobotMode mode)`

When given a `ReefscapeRobotMode`, sets the robot mode for the robot. Returns nothing.

### `SetIntakeMode(ReefscapeIntakeMode mode)`

When given a `ReefscapeIntakeMode`, sets the [requested](#requestedintakemode) and [current](#currentintakemode) coral intake mode. If a coral is being held, the previous value will override [`CurrentIntakeMode`](#currentintakemode), until the robot no longer holds the coral and the cached value in [`RequestedIntakeMode`](#requestedintakemode) will be applied.

## Robot Position/Rotation

### `FacingReef`

A boolean containing whether the robot is facing the reef or not.

### `GetFacingReef()`

Returns the value of [FacingReef](#facingreef)

### `FacingBarge`

A boolean containing whether the robot is facing the barge or not

### `GetFacingBarge()`

Returns the value of [FacingBarge](#facingbarge)

### `CheckFacingReef()`

Updates the value stored in `FacingReef`. This is called automatically when entering any of the robot states.

### `CheckFacingBarge()`

Updates the value stored in `FacingBarge`. This is called automatically when entering any of the algae robot states.

### `GetDistanceToBarge()`

Returns a float containing the distance to the closest point on the barge, in inches.

## Robot States

### `Stowed()`

Returns a boolean for whether the current robot state is the stow state.

### `Intaking()`

Returns a boolean for whether the current robot state is the intake state.

### `Climb()`

Returns a boolean for whether the current robot state is the climb state.

### `Climbed()`

Returns a boolean for whether the current robot state is the climbed state.

### `Processor()`

Returns a boolean for whether the current robot state is the processor state.

## Miscellaneous

### `GetReef()`

Returns the `Reef` object the robot references, which is the reef on the robot's alliance.

### `AtSetpoint()`

This method must be overridden by a method with the same name and return type with the `override` keyword in your robot script. Used by advanced features in the auto align script.
