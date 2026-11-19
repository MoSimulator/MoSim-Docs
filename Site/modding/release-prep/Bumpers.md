---
sidebar_position: 1
id: bumpers
title: Bumpers
sidebar_label: Bumpers
---

## Alliance Colors

Bumper colors are set by the robot base on spawn. On the robot script open the `bumpers` dropdown.

| Component | Description |
| --- | --- |
| `Bumper Meshes` | Every Mesh that should change color. This should include every piece of the bumper that is or would be covered by fabric. |
| `Material Index` | The material slot that is the bumper fabric, the same index is used for every mesh in the list. Normally this will be 0, but if the CAD has numbers included in the bumpers, there is a chance slot 0 will be the numbers and this might need to be 1. |
| `Blue`/`Red` `Bumper Material` | Either use the shared materials in `Assets/Materials`, or create your own material by copying a pre-existing material and changing the values. |

:::note
The spawn select prefab has its own `SpawnpointSelectionSettings` component; assign its `Bumper Meshes` and `Material Index` there as well.
:::

## Vanity

Vanity bumpers let the player use the team's real vanity bumpers in singleplayer (if they exist in real life). They are set up in the `Vanity` dropdown under `bumpers`.

`Allow Vanity` enables the `Use Vanity` toggle in the singleplayer menu. This must be true for vanity bumpers to be used at all.

| Vanity Type | Description |
| --- | --- |
| `No Alliance Colors` | Keeps the material on the mesh in the prefab. With this, it simply won't override the material that you have on the bumper meshes in the editor. You would need to set the material on the bumper mesh to the custom material. |
| `Custom Material` | Uses `Vanity Material`. You would set `Vanity Material` to the custom material. |
| `Custom Color` | This is the simplest way to have a Vanity Bumper but it is also the most limiting in what you can do. You set `Vanity Color` to the color you want, and that's it. If no color is set, it will be defaulted to be blue. |

`Disable On Vanity` objects are hidden when vanity is enabled. If the Vanity Bumpers don't have the bumper numbers on them, you could add the numbers' GameObjects into `Disable On Vanity` and they would be hidden when vanity is enabled.

`Enable On Vanity` objects are shown when vanity is on. This is used for teams, such as 118, who have a custom image on their bumpers. This could also be used for things such as googly eyes.

For vanity images, add an inactive `Vanity` object under the bumper with the image GameObjects childed to it. Then add it to `Enable On Vanity`. See 118 for an example.
