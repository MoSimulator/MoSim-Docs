---
sidebar_position: 2
id: commandrobots
title: Command Robots
sidebar_label: Command Robots
---

This is the system replacing the `legacy robot base`. It is currently supported in Reefscape.

## Usage

Each game's unique `CommandRobotBase` will come with a set of default `states` that can be used. They will possess an `OnMechanisms`, an `OnGamePiece`, an `OnEnter`, and an `OnExit`. These all take in a lambda function. This allows for a cleaner setup and ownership structure.

```cs
InState<StowState>()
 .OnMechanisms(() => SetSetpoint(stow))
 .OnGamePiece(() => StowPieces());

InState<IntakeState>()
 .OnMechanisms(() => SetSetpoint(intake))
 .OnGamePiece(() => StowPieces(IntakeAction));

InState<PlaceState>()
  .OnMechanisms(() =>
    {
      if (WasLastState<BargeState>())
      {
         SetSetpoint(bargePlace);
      } 
      else if (WasLastState<L4State>())
      {
         SetSetpoint(l4Place);
      } else if (WasLastState<L1State>())
      {
         SetSetpoint(l1Place);
      }
    })
  .OnGamePiece(PlacePiece);
  ...
```

## Advanced Usage

Because of the way the system is set up, you are also able to easily customize the state system with custom transitions and new states.

Custom state transitions are achieved through the `.OnCheckTransition()` or `.OnAdditionalTransition()` lambdas. `OnCheckTransition` overrides the default transition behavior completely, while `OnAdditionalTransition` runs **after** the default state transition checks.

```cs
.OnCheckTransition(() =>
 {
   if (L4Action) return new L4State(this);
   if (L3Action) return new L3State(this);
   ...
```

Custom states altogether are created using the following custom class layout.

```cs
protected sealed class CustomState: RobotState<ReefscapeCommandRobotBase>
{

  public CustomState(ReefscapeCommandRobotBase robot) : base(robot)
  {
  }

  public override IRobotState CheckTransition() 
  {   
  }

  public override void OnEnter()
  {
  }


   public override void OnMechanismUpdate()
   {
   }

   public override void OnGamePieceUpdate()
   {
   }
}
```

These must have their functions defined here, as you will not be able to use the `.` chaining to set them up. You can then transition into the state using custom state transitions as described above.
