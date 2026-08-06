---
sidebar_position: 5
---

# Code

Open the Lynk.cs File and ctrlA backspace.

The following is a blank template for a robot:

```cs title="/src/components/Lynk.cs"
namespace Prefabs.Reefscape.Robots._9496
{
    public class Lynk: ReefscapeRobotBase
    {
        protected override void Start()
        {
            base.Start();
        }

        private void FixedUpdate()
        {
            switch (CurrentSetpoint)
            {
                case ReefscapeSetpoints.Stow:
                    break;
                case ReefscapeSetpoints.Intake:
                    break;
                case ReefscapeSetpoints.Place:
                    break;
                case ReefscapeSetpoints.L1:
                    break;
                case ReefscapeSetpoints.Stack:
                    break;
                case ReefscapeSetpoints.L2:
                    break;
                case ReefscapeSetpoints.LowAlgae:
                    break;
                case ReefscapeSetpoints.L3:
                    break;
                case ReefscapeSetpoints.HighAlgae:
                    break;
                case ReefscapeSetpoints.L4:
                    break;
                case ReefscapeSetpoints.Processor:
                    break;
                case ReefscapeSetpoints.Barge:
                    break;
                case ReefscapeSetpoints.RobotSpecial:
                    break;
                case ReefscapeSetpoints.Climb:
                    break;
                case ReefscapeSetpoints.Climbed:
                    break;
            }
        }
    }
}
```

Now in `lynkSetpoint`,

Make it look like:

```cs

namespace Prefabs.Reefscape.Robots.Mods.TestingMod._9496
{
   [CreateAssetMenu(fileName = "Setpoint", menuName = "Robot/Lynk Setpoint", order = 0)]
   public class LynkSetpoint : ScriptableObject
   {
       [Tooltip("Inches")] public float elevatorHeight;
   }
}
```

You may notice that there is no setpoint for the algae arm, we have something different planned for that.

Returning to `Lynk.cs` we want to create the basics for a joint. Put the following at the top of the class

```cs
[SerializeField] private GenericElevator elevator;
[SerializeField] private GenericJoint algaeArm;
[SerializeField] private GenericJoint funnelFlap;
[SerializeField] private GenericJoint climberBar;
[SerializeField] private GenericJoint climberFlap;

[SerializeField] private PidConstants algaeArmPid;
[SerializeField] private PidConstants funnelFlapPid;
[SerializeField] private PidConstants climberBarPid;
[SerializeField] private PidConstants climberFlapPid;
```

`[SerializeField]` pushes a private field to the inspector, `GenericElevator` is a script that will handle animating elevator joints for you, and `GenericJoint` is the basic “motor controller” of MoSim.

The correct naming scheme is `lowerFirstCamelCase` for serialized private/public, `UpperFirstCamelCase` for non serialized public, `_lowerFirstCamel` for private.

Next we will add a `LateUpdate` event function and initialize our pids. 

:::info

Until you initialize the pid in a generic joint it CAN NOT be used. 

:::

```cs
protected override void Start()
{
   base.Start();
  
   algaeArm.SetPid(algaeArmPid);
   funnelFlap.SetPid(funnelFlapPid);
   climberBar.SetPid(climberBarPid);
   climberFlap.SetPid(climberFlapPid);
}

private void LateUpdate()
{
   algaeArm.UpdatePid(algaeArmPid);
   funnelFlap.UpdatePid(funnelFlapPid);
   climberBar.UpdatePid(climberBarPid);
   climberFlap.UpdatePid(climberFlapPid);
}
```

The LateUpdate section allows you to update the Pid Values at runtime

Now we want to add our setpoints for all states.

```cs
[SerializeField] private LynkSetpoint stow;
[SerializeField] private LynkSetpoint intake;
[SerializeField] private LynkSetpoint l1;
[SerializeField] private LynkSetpoint l2;
[SerializeField] private LynkSetpoint l3;
[SerializeField] private LynkSetpoint l4;
[SerializeField] private LynkSetpoint l4Place;
[SerializeField] private LynkSetpoint lowAlgae;
[SerializeField] private LynkSetpoint highAlgae;
[SerializeField] private LynkSetpoint bargePrep;
[SerializeField] private LynkSetpoint bargePlace;
```

You should note that for both barge and l4 the robot has a secondary setpoint. They also have one intake setpoint for both station and lollipop algae. We also are going to handle their climb setpoints in a different manner.

Now we want to set up the infrastructure to make our lives easy.

```cs
private float _elevatorTargetHeight;
private float _flapTargetAngle;
private float _climbBarTargetAngle;
private float _climbFlapTargetAngle;

Private override void Start() {
…

_elevatorTargetHeight = 0;
_flapTargetAngle = 0;
_climbBarTargetAngle = 0;
_climbFlapTargetAngle = 0;
}

private void SetSetpoint(LynkSetpoint setpoint)
{
   _elevatorTargetHeight = setpoint.elevatorHeight;
}

private void UpdateSetpoints()
{
   elevator.SetTarget(_elevatorTargetHeight);
   algaeArm.SetTargetAngle(0).withAxis(JointAxis.X);
   funnelFlap.SetTargetAngle(_flapTargetAngle).withAxis(JointAxis.X);
   climberBar.SetTargetAngle(_climbBarTargetAngle).withAxis(JointAxis.X);
   climberFlap.SetTargetAngle(_climbFlapTargetAngle).withAxis(JointAxis.X);
}
```

Note the builder pattern used here. If you want to do something such as flip direction, tell a joint to not pass through an angle or tell it to 0 on start you can expand the builder pattern.

```cs
Private void FixedUpdate() {
	…
	
	UpdateSetpoints()
}
```

Now we can fill out some of the switch statement.

```cs
switch (CurrentSetpoint)
{
   case ReefscapeSetpoints.Stow:
       SetSetpoint(stow);
       break;
   case ReefscapeSetpoints.Intake:
       SetSetpoint(intake);
       break;
   case ReefscapeSetpoints.Place:
      
       break;
   case ReefscapeSetpoints.L1:
       SetSetpoint(l1);
       break;
   case ReefscapeSetpoints.Stack:
       SetSetpoint(intake);
       break;
   case ReefscapeSetpoints.L2:
       SetSetpoint(l2);
       break;
   case ReefscapeSetpoints.LowAlgae:
       SetSetpoint(lowAlgae);
       break;
   case ReefscapeSetpoints.L3:
       SetSetpoint(l3);
       break;
   case ReefscapeSetpoints.HighAlgae:
       SetSetpoint(highAlgae);
       break;
   case ReefscapeSetpoints.L4:
       SetSetpoint(l4);
       break;
   case ReefscapeSetpoints.Processor:
       SetSetpoint(stow);
       break;
   case ReefscapeSetpoints.Barge:
       SetSetpoint(bargePrep);
       break;
   case ReefscapeSetpoints.RobotSpecial:
       break;
   case ReefscapeSetpoints.Climb:
       break;
   case ReefscapeSetpoints.Climbed:
       break;
}
```

Now we can get into some of the specific tools you have access to with `ReefscapeRobotBase`.
The first is a reference called `LastSetpoint`. 

:::tip

This can **only** be used reliably in the place setpoint but makes complex logic easier.

:::

```cs
case ReefscapeSetpoints.Place:
   if (LastSetpoint == ReefscapeSetpoints.Barge)
   {
       SetSetpoint(bargePlace);
   }
   else if (LastSetpoint == ReefscapeSetpoints.L4)
   {
       SetSetpoint(l4Place);
   }
   break;
```

Inside the unused `robotSpecial` case we want to override the state machine and force it back to stow. 

```cs
case ReefscapeSetpoints.RobotSpecial:
   SetState(ReefscapeSetpoints.Stow);
   Break;
```

This is **generally** reliable but should generally be done before the state machine is “consumed” in the switch. (The state machine is updated at a different rate than the robot code)

We will worry about the climber last, so moving on to game pieces.

Add the following to the top

```cs
[SerializeField] private ReefscapeGamePieceIntake coralIntake;
[SerializeField] private ReefscapeGamePieceIntake algaeIntake;

[SerializeField] private GamePieceState coralStowState;
[SerializeField] private GamePieceState algaeStowState;

private RobotGamePieceController<ReefscapeGamePiece, ReefscapeGamePieceData>.GamePieceControllerNode _coralController;
private RobotGamePieceController<ReefscapeGamePiece, ReefscapeGamePieceData>.GamePieceControllerNode _algaeController;
```

In the bottom of the start function we will again access a `ReefscapeRobotBase` feature in `RobotGamePieceController`. 

```cs
RobotGamePieceController.SetPreload(coralStowState);
_coralController = RobotGamePieceController.GetPieceByName(ReefscapeGamePieceType.Coral.ToString());
_algaeController = RobotGamePieceController.GetPieceByName(ReefscapeGamePieceType.Algae.ToString());

_coralController.gamePieceStates = new[]
{
   coralStowState
};
_coralController.intakes.Add(coralIntake);

_algaeController.gamePieceStates = new[] { algaeStowState };
_algaeController.intakes.Add(algaeIntake);
```

This effectively “sets up” the game piece controller. 

At the top of `FixedUpdate` add

```cs
   bool hasAlgae = _algaeController.HasPiece();
   bool hasCoral = _coralController.HasPiece();
```

Below add

```cs
_algaeController.SetTargetState(algaeStowState);
_coralController.SetTargetState(coralStowState);
```

Since there is only one state per game piece we can let the controller internally handle everything and always request animations to the targets.

Inside the intake state add.

```cs
_algaeController.RequestIntake(algaeIntake, CurrentRobotMode == ReefscapeRobotMode.Algae && !hasAlgae);
_coralController.RequestIntake(coralIntake, CurrentRobotMode == ReefscapeRobotMode.Coral && !hasCoral);
```

`CurrentRobotMode` returns the `gamePieceMode` of the robot and can optionally be set using `SetRobotMode()` with the same warnings as `SetState()`.

In stack add

```cs
case ReefscapeSetpoints.Stack:
   SetSetpoint(intake);
   _algaeController.RequestIntake(algaeIntake, IntakeAction.IsPressed() && !hasAlgae);
   _coralController.RequestIntake(coralIntake, false);
```

:::info

Many buttons are directly accessible through `ReefscapeRobotBase`, It is extremely important you **do not** use `.triggered` on them though as it updates at a different time than fixed update. Intake state is also “sticky” so you need to set it to false to stop attempting to intake from that controller.

:::

Add this to the high and low algae setpoints as well.

Add the following function then call it in place.

```cs
private void PlacePiece()
{
   if (CurrentRobotMode == ReefscapeRobotMode.Algae)
   {
       _algaeController.ReleaseGamePieceWithForce(new Vector3(0, 0, 0));
   }
   else
   {
       if (LastSetpoint == ReefscapeSetpoints.L4)
       {
           _coralController.ReleaseGamePieceWithForce(new Vector3(0, 0, 0));
       }
       else
       {
           _coralController.ReleaseGamePieceWithForce(new Vector3(0, 0, 0));
       }
   }
}
```

Now back to the editor…
