---
sidebar_position: 1
id: reefscapeautoalign
title: Reefscape Auto Align
sidebar_label: Reefscape Auto Align
---

The Auto Align script for Reefscape is based on the core Auto Align system, which features a handful of different alignment modes.

<img src="/img/mason/apis/auto_align.png" alt="Reefscape Auto Align component" width="50%"/>

It features a standard PID-based align and aim control, a standard S-curve profile, a hybrid system which uses both the PID and the profile simultaneously, alongside a "predictive" mode which attempts to predict the max acceleration/deceleration at a given point in time.

## Tuning

| Method | What to Do | Why One Over Another |
| --- | --- | --- |
| PID | Follow standard PID tuning procedures. | Simple but often insufficient. |
| Profiled | Increase the deceleration until the robot slightly overshoots, then lower max jerk until it stops. | Simple but competent in most cases. |
| Hybrid | Start with an overdamped PID gain and increase the profiled constants until it behaves correctly. | Allows you to maintain consistent damping behavior throughout the profile; may struggle on some robots. |
| Predictive | Tune the approach and braking safety factor until it works. | A last resort if other methods continue to refuse to work; often worse than hybrid on many robots. |

## Legacy Mode

If your robot has a non-centered end effector, you might prefer to use the legacy offset mode. In legacy mode, offsets behave like they did in MoSim V25 - based off the reef pipe your robot is aligning to, instead of always relative to the center of the reef face like in the updated implementation. In order to use legacy mode, check the `Use Legacy Offsets` checkbox and enter the offsets under the `Legacy Offset` dropdown instead of the main X and Y values. 
