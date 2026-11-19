---
sidebar_position: 2
id: sprunglinkage
title: Creating Sprung Linkages
sidebar_label: Creating Sprung Linkages
---

:::warning
This assumes you have already setup your hierarchy and colliders
:::

Start by hiding all but your pivoting system

<img src="/img/mason/linkages/IndexerOnly.png" alt="Indexer Only" width="70%"/>

Rotate the linkage to its starting position, then add a hinge joint, 

<img src="/img/mason/linkages/HingeJoint.png" alt="Hinge Joint" width="90%"/>

Enable `Use Spring` set spring to 15, damper to 5, and target to the current transform angle * -1.
If you want a hard stop on the robot frame, enable the `Enable Collision` checkbox to collide with the Connected Bodies colliders.

:::warning
Enabling collision is highly recommended against, you are more likely to have issues with that than you are with setting a limit on the hinge joint.
:::

If you continue to have issues, inspect your colliders very carefully and ensure there are no intercollision issues.
