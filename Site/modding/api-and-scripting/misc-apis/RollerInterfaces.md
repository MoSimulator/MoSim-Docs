---
sidebar_position: 7
id: rollerinterfaces
title: IRoller Interfaces
sidebar_label: Roller Interfaces
---

The core interface used by all roller varieties (generic, interaction, and groups).

It contains:

```cs
//flips the rollers rpm for 1 frame then restores to the last hard rpm set
MoSimResult<bool> FlipRpm();  
//stops the rollers for 1 frame then restores to the last hard rpm set
MoSimResult<MoSimLib.Void> Stop();  
//sets the rpm for 1 frame then restores to the last hard rpm set
MoSimResult<bool> OverrideRpm(float rpm);  
//sets the hard rpm of the roller
MoSimResult<MoSimLib.Void> SetRpm(float rpm); 
//divides the rpm for 1 frame then restores the last hard rpm set  
MoSimResult<bool> DivideSpeed(float divider);  
//gets motor by array id, only used by groups
MoSimResult<IRoller> GetRoller(int id);  
//changes the motor used by the roller. Sets all on groups
MoSimResult<MoSimLib.Void> ChangeMotor(InteractionRoller.RollerMotor selectedMotor);
```

`InteractionRoller` also contains:

```cs
//used to force a roller to not make audio. Resets at the end of every frame
void MuteRollerAudio();
```
