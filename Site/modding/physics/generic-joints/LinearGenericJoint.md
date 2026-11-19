---
sidebar_position: 1
id: linearjoint
title: Linear Generic Joint Setup
sidebar_label: Linear Generic Joints
---

When setting up a linear joint, the first step is to align the object's axis with its linear motion path. You can do this by unchilding all of the models and physics and then rotating the object. It is **very** important that the axis on which you are moving reads 0 when at rest. It is also important that the local rotation be 0,0,0. If it is not, you can fix this by right-clicking and selecting `Create Offset Parent`.

<img src="/img/mason/joints/linear_initial.png" alt="Initial joint setup" width="50%"/>

You should ideally always rely on the Y axis (the yellow line), however you are not required to do so.

Next, set up the joint. In the inspector, add a `GenericJoint` script. This will add a Rigidbody and a Configurable Joint to the inspector. We will ignore the Rigidbody, though you may want to increase the weight depending on your use case. For the Configurable Joint, drag the parent Rigidbody onto the connected joint field. Then scroll down and change every axis except the one you want to move on to `Locked`.

<img src="/img/mason/joints/linear_joint.png" alt="Joint setup" width="50%"/>

If you need additional force, you can open the `___ Drive` dropdown and set the damper to 9e+30.

If this is a climber, you will want to enable `Lock Joint On Disable` on the generic joint script.

If you need to set an axis to limited, the limit is 0 → whatever you want, but negative.

<img src="/img/mason/joints/linear_settings.png" alt="Settings" width="50%"/>

We are now done in the inspector and can move to scripting.

```cs
  [Header("Joints")] 
  [SerializeField] private GenericJoint intakeSlide;

  [Header("Pids")] 
  [SerializeField] private PidConstants intakeSlidePid;

  protected override void Init()
  {
    Scheduler.AddLateRecursive(updatePids);
  }

  private void updatePids()
  {
    intakeSlide.UpdatePid(intakeSlidePid);
  }
```

These are the setup requirements for any generic joint.

```cs 
  private float intakeTarget;

  protected override void Init()
  {
    intakeTarget = 0;

    Scheduler.AddSubsystems(Intake);
    Scheduler.AddProtectedSystem(UpdateTarget);
  }

  private void Intake()
  {
    if (IntakeAction)
    {
        intakeTarget = 12;
    }
  }

  private void UpdateTarget()
  {
    intakeSlide.SetLinearTarget(intakeTarget).withAxis(JointAxis.Z);
  }  
```

This is what a simple intake slide would look like. You can use the `.` operator to chain and discover operations that can be done to linear joints.

Now you can assign it in the editor for your script and it should function as expected.

<img src="/img/mason/joints/linear_final.png" alt="Final" width="50%"/>

Don't forget to set up your PID; units are inches / second.
