---
sidebar_position: 2
id: angularjoint
title: Angular Generic Joint Setup
sidebar_label: Angular Generic Joints
---

When setting up an angular joint, the first step is to align the object's axis with its rotational origin point. You can do this by unchilding all of the models and physics and then moving the object. It is very important that the local rotation be 0 on all but your rotational axis. If it is not, or you want to re-origin your rotation, you can do this by right-clicking and selecting `Create Offset Parent`.

<img src="/img/mason/joints/angular_initial.png" alt="Initial joint setup" width="50%"/>

You should ideally always rely on the X axis (the red line), however you are not required to do so.

Next, set up the joint. In the inspector, add a `GenericJoint` script. This will add a Rigidbody and a Configurable Joint to the inspector. We will ignore the Rigidbody, though you may want to increase the weight depending on your use case. For the Configurable Joint, drag the parent Rigidbody onto the connected joint field. Then scroll down and change every axis except the one you want to move on to `Locked`.

<img src="/img/mason/joints/angular_joint.png" alt="Joint setup" width="50%"/>

If you need additional force, you can open the `Angular ___ Drive` dropdown and set the damper to 9e+30.

If this is a climber, you will want to enable `Lock Joint On Disable` on the generic joint script.

If you need to set an axis to limited, the limit is 0 → whatever you want, but negative.

<img src="/img/mason/joints/angular_settings.png" alt="Settings" width="50%"/>

We are now done in the inspector and can move to scripting.

```cs
  [Header("Joints")] 
  [SerializeField] private GenericJoint hood;

  [Header("Pids")] 
  [SerializeField] private PidConstants hoodPid;

  protected override void Init()
  {
    Scheduler.AddLateRecursive(updatePids);
  }

  private void updatePids()
  {
    hood.UpdatePid(hoodPid);
  }
```

These are the setup requirements for any generic joint.

```cs 
  private float hoodTarget;

  protected override void Init()
  {
    hoodTarget = 0;

    Scheduler.AddSubsystems(Shooter);
    Scheduler.AddProtectedSystem(UpdateTarget);
  }

  private void Shooter()
  {
    if (OuttakeAction)
    {
        hoodTarget = 12;
    }
  }

  private void UpdateTarget()
  {
    hood.SetTargetAngle(hoodTarget).withAxis(JointAxis.X).flipDirection();
  }  
```

This is what a simple hood/arm system would look like. You can use the `.` operator to chain and discover operations that can be done to angular joints.

Now you can assign it in the editor for your script and it should function as expected.

<img src="/img/mason/joints/angular_final.png" alt="Final" width="50%"/>

Don't forget to set up your PID; units are degrees / second.
