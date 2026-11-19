---
sidebar_position: 6
id: turrettuning
title: Turret Tuning
sidebar_label: Turret Tuning
---

Turrets are typically a pain to get working right. Here are a few tips to help get them working right. As per usual, if you do it right there are no silver bullets.

## The Issue

Close-up turrets are usually fine, however as you back up and increase RPM they tend to start shooting really poorly. There are a handful of techniques we have developed to resolve this.

## Tune It Like Normal

Your first step should always be to just tune the system the same as [Drum Tuning](./drumTuning.md) or [Interaction Roller Tuning](./GeneralInteractionRollerTuning.md). In addition, with turrets (especially top roller turrets) the hood roller reduction/upduction is extremely important, and minor changes there can make all the difference.

<img src="/img/mason/turret-tuning/simple_turret.png" alt="Simple turret" width="50%"/>

If you're lucky, this will be enough.

### Things to Try

- Increase/decrease compression
- Try using different combinations of unstable/shooter/fullsize motors
- Try lowering scrub forces to ~15
- Try lowering `MotorSlipResponse` to ~0.35
- Try increasing compression force on the main roller
- Try using friction coupled to normal with a friction coefficient of 15+
- Try oversizing rollers
- Try different reductions

## Other Options

If all else fails, you have two remaining options.

### Cope Hoods

These are something that was discovered by accident and found to be a really useful tool for certain turrets. If you are fighting vertical spread, this *may* be the best option you have available.

<img src="/img/mason/turret-tuning/cope_hood.png" alt="Cope hood" width="50%"/>

The idea is to add a collider that extends beyond the endline of your actual hood to force the balls to settle onto the collider and normalize their ballistic arc and velocity.

For short robots, you may want to go into the exclude layers section and exclude field, default, and robot to prevent clipping the trench.

### Heading Rails

Similarly discovered by accident, the idea here is to gently center the fuel while still giving the rollers final say in position. These are annoying to tune as compression is very sensitive, but it is the single most effective way to get a turret shooting straight if you couldn't otherwise.

<img src="/img/mason/turret-tuning/heading_rails.png" alt="Heading rails" width="50%"/>
