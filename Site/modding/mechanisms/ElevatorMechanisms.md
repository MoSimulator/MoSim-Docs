---
sidebar_position: 1
id: elevmech
title: Elevator Mechanisms
sidebar_label: Elevator Mechanisms
---

How to create elevators for your mods

## Continuous

your hierarchy should look as follows:

```
- Root
-> Elevator
 -> Stationary
  - Model
  - Physics
 -> Stage 1 (Generic Joint connected to Root)
  - Model
  - Physics
 -> Stage 2 (Generic Joint connected to Root)
  - Model
  - Physics
```

## Cascade

your hierarchy should look as follows

```
- Root
-> Elevator
 -> Stationary
  - Model
  - Physics
 -> Stage 1 (Generic Joint connected to Root)
  - Model
  - Physics
  -> Stage 2 (Generic Joint connected to Stage 1)
   - Model
   - Physics
```

:::note
Reference [linear joint setup](../physics/generic-joints/LinearGenericJoint.md) if you are unsure how each joint should be done
:::

For all of them the stage objects should be located at the linear point of 0,0,0 relative to the `Elevator` Object

<img src="/img/mason/mechs/lynk_side.png" alt="Lynk Elevator Side" width="20%"/>

On your root robot add a generic Elevator and drag Stage1 followed by Stage2 into the stages dropdown (order matters, that's how it determines following). Set the type to continuous. PIDs to 100, 0, and 0.1.

<img src="/img/mason/mechs/pid_1.png" title="" alt="PID 1" width="40%" />

- `Pid Constants` is used for non-following stages, which on continuous is only the carriage. For cascade, all stages use it

- `Stages Pid Constants` is used for following stages on continuous mode, these must be more aggressive so that they do not lag the carriage stage

- `Elevator axis` sets the axis each generic joint will move in.

- `Flipped` allows you to flip the direction of motion the joints move

- `Stage Overlap` `Stage Height` and `Carriage Height` are used in continuous mode to determine stage positions that match the kinematics of a continuous elevator

- `Stage Overrides` can be applied to specific stages to make patch fixes where needed. If you setup the elevator correctly these should not be strictly needed.

## Programming

We are now done in the inspector and can move to scripting. They show only the elevator parts of that script. Add them inside your robot class and merge them into any existing methods with the same name.

Start by setting up the Generic Elevator reference and a target height.

```cs
  [Header("Joints")]
  [SerializeField] private GenericElevator elevator;

  private float _elevatorTargetHeight;

  protected override void Init()
  {
    _elevatorTargetHeight = 0;
  }
```

Drag the Generic Elevator component into the robot script's `Elevator` field in the inspector.

The Generic Elevator sets up its stages' PIDs in its own `Start` and updates them in `LateUpdate`. Tune `Pid Constants` and `Stages Pid Constants` on that component as described above.

The height is linear travel in inches, so do not convert it to meters before passing it to `SetTarget`.

Next select a setpoint and send its height to the elevator each physics update.

```cs
  protected override void Init()
  {
    Scheduler.AddProtectedSystem(UpdateSetpoints);
  }
  
  private void UpdateSetpoints()
  {
    elevator.SetTarget(_elevatorTargetHeight);
  }
```
