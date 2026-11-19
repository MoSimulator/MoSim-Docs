---
sidebar_position: 1
id: linktake
title: Creating 'Linktakes'
sidebar_label: Creating 'Linktakes'
---
:::warning
This assumes you have already setup your hierarchy and colliders
:::

## First Steps
Start by hiding all but your intake and "kicker" or other connected linkages

<img src="/img/mason/linkages/IntakeOnly.png" alt="Intake Only" width="70%"/>

Ensure your root objects are setup correctly, such that the root objects with the joints are pointed correctly.

:::important
Ensure linear joints have a parent setting their local position to 0,0,0
:::

Add a generic joint to the root of your intake, set all axes on the joint except the one you want loose to `Locked` and set the connected body to the body you want to attach to.

<img src="/img/mason/linkages/GenericJoint.png" alt="Generic Joint" width="90%"/>
{/* TODO: blockout/crop the file view */}

Now on each of your `linkages` add a hinge joint and connect it to the `Generic Joint`.

<img src="/img/mason/linkages/LinkageHinge.png" alt="Linkage Hinge" width="90%"/>

:::info
if your linkage is not stowed by default, now is the time to rotate it to stow.
:::

## Code
Below is the code structure for a simple linktake,
```cs
    [Header("Joints")]
    [SerializeField] private GenericJoint intake;
    
    [Header("PID Constants")]
    [SerializeField] private PIDConstants intakePid;
    
    [Header("Tuning Values")]
    [Tooltip("Inches"), SerializeField, Range(0, 15f)] private float intakeStowedDistance;
    [Tooltip("Inches"), SerializeField, Range(0, 15f)] private float intakeDeployedDistance;    
    
    private float intakeDistance = 0;
    
    protected override void Init() {
        intakeDistance = intakeStowedDistance;
        intake.SetPid(intakePid);
        Scheduler.AddSubsystems(RunIntake);
        Scheduler.AddProtectedSystem(UpdateJoints);
        Scheduler.AddLateRecursive(UpdatePids);
    }
    
    private void UpdatePids()
    {
        intake.UpdatePid(intakePid);
    }
    
    /// <summary>
    /// Run the intake
    /// </summary>
    private void RunIntake() {
        if (IntakeAction) intakeDistance = intakeDeployedDistance;
    }
    
    private void UpdateJoints()
    {
        intake.SetLinearTarget(intakeDistance).withAxis(JointAxis.Z);
    }
```

After setting it up in the inspector, it should hopefully work, however you may need to disable robot collisions on the linkages to get the intake to move.
If you have to do that, you have a collision issue and need to trace that. Otherwise, if you find it flaps around too much you can enable the spring on the hinge joint to dampen the motion. small numbers for spring and damper tend to work best.

:::note
What unity reports as -40 in the transform is 40 in joint space.
:::