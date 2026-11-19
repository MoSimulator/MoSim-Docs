---
sidebar_position: 2
id: turretmech
title: Turret Mechanisms
sidebar_label: Turret Mechanisms
---

How to create turrets for your mods

Start by following [Angular Generic Joint Setup](../physics/generic-joints/AngularGenericJoint.md) but use the y axis not the X for your rotation point.

<img src="/img/mason/mechs/tur_init.png" title="" alt="turret" width="50%" />

Next you want to disable the automatic center of mass on the rigidbody. Depending on animation/physics based you may or may not want to increase the weight to at least 5.

Next, add a Generic Turret script to the object.

<img src="/img/mason/mechs/ins_init.png" title="" alt="inspector before" width="60%" />

For the fields you have a handful that are important to set. Reference point allows you to set a custom targeting point that is not the turrets center. This is useful for setting up simple SWM or having an offset target point. Steer feed forward can be increased to have the turret rotate to counter the robots rotation as PID alone will not keep up. 

Wrapping can be set to have a turret obey wire travel restictions. Set these to the number of degrees each direction before needing to "unwrap". It is recommended to leave track continuously enabled.

Angular offset can be used if the turret's forward does not align with the z axis' forward. (the blue arrow)

<img src="/img/mason/mechs/ins_final.png" title="" alt="inspector after" width="60%" />

We are now done in the inspector and can move to scripting. These snippets use `RebuiltTurretRobot` which your robot script should inherit from. 

Start by setting up the Generic Turret and its PIDs

```cs
  [Header("Joints")]
  [SerializeField] private GenericTurret turret;

  [Header("PID")]
  [SerializeField] private PidConstants turretPID;

  protected override void Init()
  {
    base.Init();
    turret.SetPid(turretPID);

    Scheduler.AddLateRecursive(UpdatePid);
    Scheduler.AddSubsystems(UpdateJoints);
  }

  private void UpdatePid()
  {
    turret.UpdatePid(turretPID);
  }
```

Next we need to choose where the turret should aim.

```cs
  protected override void AllianceZone()
  {
    turret.SetDefaultMap(GenericTurret.DefaultMap.Hub);
  }

  protected override void NeutralZone()
  {
    turret.SetPassMapClosest();
  }

  private void UpdateJoints()
  {
    turret.Align(RotateAction.ReadValue<float>());
  }
```

This is what a simple hub and passing setup looks like: `RebuiltTurretRobot` calls `AllianceZone` while you are in your alliance zone and `NeutralZone` otherwise. The turret selects the hub in your alliance zone and the closest passing target outside it. The field needs correctly named target objects on the `AutoAngleNodes` layer for these maps. The turret initializes its maps using its parent robot and filters out the other alliance's targets.

`SetDefaultMap` and `SetPassMapClosest` choose the target. `Align` drives the joint toward it. Passing in `RotateAction.ReadValue<float>()` gives the turret the robot's steering input for steer feed forward.

You can also aim at a transform or a custom world position instead of a field map. These are alternatives to the zone targeting above, which would otherwise replace your custom target on the next update.

```cs
  // Follow a moving transform. Use your own Transform reference here.
  turret.SetTarget(targetTransform);

  // Or aim at a fixed world position. Use your own Vector3 here.
  turret.SetCustomTarget(targetPosition);

  // Stop tracking the selected target.
  turret.ClearTarget();
```

Use these calls individually inside your own targeting method. Clearing the target does not command a return to zero; it stops further alignment to that target.

Next we can prevent the shooter from feeding until the turret is pointed at its target.

```cs
  protected override bool Aligned()
  {
    return Mathf.Abs(turret.GetError()) < 5;
  }
```

This allows shooting when the turret is within 5 degrees of its target. `RebuiltTurretRobot` checks this before calling `Shooting` or `IntakingAndShooting`. You still need to implement those methods for your shooter and feeder. Make sure a valid target is selected, as `GetError()` returns zero when there is no target.

For simple shooting while moving, assign the inherited `Swm Ref` and `Swm Settings` fields on your robot script. Use a child transform whose parent is at your normal aiming reference, then assign that child to the turret's `Reference Point` as well. The base class moves this reference ahead of the robot using its smoothed velocity and the configured time of flight.

```cs
  // Already provided by RebuiltTurretRobot; do not add a second copy.
  protected virtual void HandleSwm()
  {
    Vector3 robotVelocity = rb.velocity;
    _smoothedVelocity = Vector3.Lerp(
      _smoothedVelocity, robotVelocity, swmSettings.velocityDampeningValue);
    swmRef.position = swmRef.parent.position
      + (_smoothedVelocity * swmSettings.timeOfFlight);
  }
```

This excerpt shows how the inherited handler works; `_smoothedVelocity` is private to the base class. Set `timeOfFlight` for your shot and tune `velocityDampeningValue` for the velocity smoothing. The handler runs from the base setup, so assign `Swm Ref`, its parent, and `Swm Settings` even when starting with stationary aiming. A time of flight of zero removes the velocity offset.

Finally, if you have travel limits you can use `turret.IsWrapping()` to check whether it is unwinding or the joint reports wrapping.