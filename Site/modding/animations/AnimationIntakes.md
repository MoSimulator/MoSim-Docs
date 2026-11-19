---
sidebar_position: 1
id: animintakes
title: Animation Intakes
sidebar_label: Animation Intakes
---

How to set up animation-based intakes

:::important
This is based on the LYNK Reefscape example
:::

create an empty named Coral Intake Childed to whatever it should move with.

<img src="/img/mason/animations/stg2_hier.png" alt="Hierarchy" width="30%"/>

Add a collider box to the object. And set the size such that it covers the intake region. 

<img src="/img/mason/animations/collider.png" alt="Box Collider" width="50%"/>

:::note
You may put the box on a separate object, so long as the collider's center is 0,0,0
:::

Now add a Reefscape Game Piece Intake to the object, set the collider you just added to the intake collider field, then set an intake speed of 118, a force of 80, a max distance of 10 and an accuracy of 2. Also check smooth handoff.

<img src="/img/mason/animations/component.png" alt="Intake Component" width="50%"/>

### Simple Mode Definitions

- `intake speed` is the maximum velocity the physics forces are allowed to pull the object at prior to entering an animation state

- `intake force` how hard the intake can pull an object up to the target `intake speed`

- `Max Distance` how far from the center the object must go after entering the `intake colliders` bounds before it is disengaged again

- `accuracy` the distance a piece must reach from center to center with the `Target` to be considered intaken. This is visualized via a gizmo sphere.

- `Smooth Handoff` tells the intake to NOT snap the piece to center on success, instead promising that the animation system already has a **DIFFERENT** target set to animate to instead.

### Advanced Mode Definitions

- `intake speed` is the maximum velocity the physics forces are allowed to pull the object at prior to entering an animation state

- `intake force` how hard the intake can pull an object up to the target `intake speed

- `Use Planar Tolerancing` when enabled, instead of using a distance sphere to secure a game piece to the animation world, you will instead use a defined rectangular region.

- `Box Extents` defines the box region in inches that will be used to grab game pieces from the dynamic world into the animation world

- `Max Distance` Is a size multiplier to the Planar tolerancing for how far from the center the object must go after entering the `intake colliders` bounds before it is disengaged again

- `Accuracy` is disabled in this mode

- `Lock X/Y/Z` these allow you to prevent the intake from applying linear velocity forces in the axis as defined by your `Target`

- `Smooth Handoff` tells the intake to NOT snap the piece to center on success, instead promising that the animation system already has a **DIFFERENT** target set to animate to instead.

<img src="/img/mason/animations/lynkex.png" alt="LYNK example" width="50%"/>

Reference [Piece Animations](./AnimationSystem.md) for how to program/code the intake.