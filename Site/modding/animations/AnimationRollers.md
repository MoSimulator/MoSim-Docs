---
sidebar_position: 2
id: animrollers
title: Animation Rollers
sidebar_label: Animation Rollers
---

How to make rollers spin visually

## Generic Animation Joint

The generic animation joint is a psuedo legacy component that is used for making mechanism animations. Common uses are Rollers and climbers.

### Rollers

For rollers they are useful for any situation in which an `InteractionRoller` is not present as it supports animations internally. Simply align it with a set of objects and child them to the object you have this on. 

:::note
See [Custom Tooling](../misc/workflow-tools/CustomTools.md) for a list of tools that may help align objects
:::

<img src="/img/mason/animations/2910ex.png" alt="2910 example" width="70%"/>

No Inspector Values need assigned for these

```cs
[Header("Animation Joints (Wheels)")]
[SerializeField] private GenericAnimationJoint

private void runWheels() {
   wheel.VelocityRoller(0).useAxis(JointAxis.X);
}        
```

### Climbers

For climbers they are used as the primary passive latching system as joints are generally unable to handle the force needs. so we use "clickers".

<img src="/img/mason/animations/climber.png" alt="Climber" width="70%"/>

These require `Move Away` colliders to be assigned which should be set to `is trigger`

```cs
[Header("Clicker Joints")]
[SerializeField] private GenericAnimationJoint clicker;

private void Update()
{
  clicker.SpringLoaded().AllowedDirection(1).RotationSpeed(ClickerSpeed);
}
```

## Interaction Rollers

Interaction Rollers can also handle roller animations for you, 

<img src="/img/mason/animations/spectrum.png" alt="Interaction Animation Sample" width="50%"/>

If you create a roller using the `Guess Interaction Roller` it will be automatically added to the models section.

<img src="/img/mason/animations/mode.png" alt="Guess" width="40%"/>

These can be manually assigned or automatically added using the `Hierarchy` -> `add object as roller model` followed by selecting the correct interaction roller. When set the model will rotate at the same speed as the interaction roller.


