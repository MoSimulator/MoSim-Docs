---
sidebar_position: 8
id: pidtuning
title: PID Tuning
sidebar_label: PID Tuning
---

{/* TODO: Angular section references images/lynk-angular-joints.svg, which was not attached in Discord */}

## PID Explained

A PID controller moves a joint toward a target. It checks the difference between the target and the current position, then tells the joint how fast to move.

<img src="/img/pid/pid_feedback_loop.svg" alt="PID feedback loop" width="70%"/>

- **P** makes the joint move toward its target. Too much P can cause overshoot or shaking.
- **I** helps correct a small error that remains over time. Leave it at zero to start.
- **D** slows the joint as it moves, helping reduce overshoot. Too much D can make movement sluggish.
- **Max** limits the controller's movement command. It does not limit the joint's travel.

For angular joints, the controller takes the shortest turn to the target. PID settings depend on the joint, so use the LYNK values below only as starting points for this robot.

:::tip
Test one mechanism at a time with a small movement. Adjust P first, then D if it overshoots. Only try I if a small error consistently remains; when enabling I, set a small `Isaturation` too. Test both directions and stay within the mechanism's travel limits.
:::

## Elevator / Linear PID Tuning

The 2025 9496 LYNK has a two-stage elevator that moves along the Y axis. Targets are entered in inches. **Stage 2** (the final stage) uses **Pid Constants**; **Stage 1** uses **Stages Pid Constants**.

<img src="/img/pid/elevator_robot.png" alt="9496 robot with the two-stage elevator and Generic Elevator inspector" width="70%"/>

### Starting Settings

| Inspector setting | P | I | D | Max | Isaturation |
|---|---:|---:|---:|---:|---:|
| Pid Constants (Stage 2) | 15 | 0 | 0.1 | 3 | 0 |
| Stages Pid Constants (Stage 1) | 100 | 0 | 0.1 | 20 | 0 |

These are the LYNK simulation's starting values. I and `Isaturation` are both zero, so integral correction is off. `Max` limits the movement command, not the elevator height.

<img src="/img/pid/elevator_inspector.png" alt="Generic Elevator inspector showing the primary and stage PID settings" width="70%"/>

### Basic Tuning Steps

1. Test a small move, such as **Stow (0 in)** to **L1 (6 in)**. Check that the elevator moves the right way and stays within its travel range.
2. Keep I at zero. Raise P a little at a time for a quicker response. If the elevator overshoots or shakes, lower P.
3. If it still overshoots, raise D slightly. Adjust `Max` only to limit how fast the controller can command movement.
4. Test moving up and down. Once stable, try higher targets such as L3 (32 in) and L4 (55.5 in).

Change one PID group at a time in the `GenericElevator` inspector. Only try I if the elevator repeatedly stops short of its target; if you enable it, use a small `Isaturation` and retest carefully.

## Angular PID Tuning

The 9496 LYNK uses angular PID control for four joints: **AlgaeArm**, **FunnelPivot**, **ClimbBar**, and **ClimbWdge**. Each starts with P **0.5**, I **0**, D **0.1**, `Max` **4**, and `Isaturation` **0**.

The inspector uses slightly different labels for these settings: **Algae Arm Pid**, **Funnel Flap Pid**, **Climber Bar Pid**, and **Climber Flap Pid**.

<img src="/img/pid/angular_inspector.png" alt="CommandLynk inspector showing the four angular PID groups" width="70%"/>

The controller turns the shortest way to the target angle. `Max` limits the movement command; it does not limit the joint's rotation. I and `Isaturation` are zero, so integral correction is off.

### Basic Tuning Steps

1. Tune one joint at a time with a small angle change. Check that it moves in the expected direction.
2. Raise P a little at a time for a quicker response. If the joint overshoots or shakes, lower P.
3. If needed, raise D slightly to reduce overshoot. Keep `Max` low while testing.
4. Test both directions, then try a larger move. The climb sequence includes targets around -110° to -120°; verify the robot has clearance.
5. Repeat for each joint. Change I only if the joint consistently stops short; if you enable it, use a small `Isaturation` and retest carefully.

Adjust the matching PID group in the 9496 `CommandLynk` inspector. Different joints may need different values.
