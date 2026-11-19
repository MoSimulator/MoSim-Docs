---
sidebar_position: 3
id: csharptemplates
title: Starting Scripts
sidebar_label: C# Templates
---

We provide a handful of script templates that can be used as starting places to work from. These can be accessed from the `Assets` → `Create` → `MoSimulator` menu.

<img src="/img/mason/scripting/templates_menu.png" alt="Templates menu" width="70%"/>

## RebuiltTemplate

Comes with the following functions scheduled correctly and ready for you to fill out.

```cs
private void UpdatePids()  
private void Climber()  
private void Intake()  
private void Hopper()  
private void HandleAutoAim()  
private void Shooter()  
private void SetInterpMap()  
private void UpdateJoints()  
private void WobbleIntake()  
```

## RebuiltTurretTemplate

Comes with the following functions for you to implement.

```cs
protected override void Init()
private void UpdatePids()
private void UpdateJoints()
protected override void HandleSwm()
protected override void AllianceZone()
protected override void NeutralZone()
protected override void Intaking()
protected override void IntakingAndShooting()
protected override void Shooting()
protected override void Idling()
protected override bool Aligned()
```

This template is intended for the standard turret robot. It comes with SWM handling for you, though it is relatively basic and may not suffice for some robots. It also handles state transitions for you, moving between `Intaking`, `Idle`, `Shooting`, and `IntakingAndShooting`. `AllianceZone` and `NeutralZone` can be used to set interp modes. Overall, you should be using a more explicit layout for code written in this template.

## ReefscapeTemplate

This script can be used as a starting place for Reefscape robots. I highly recommend reading [Command Robots](../CommandRobots.md) and [Reefscape Command Robot Base](../misc-apis/ReefscapeCommandRobotBase.md), as there is a lot in this template based on those.
