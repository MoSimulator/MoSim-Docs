---
sidebar_position: 5
id: menumodel
title: Main Menu Model
sidebar_label: Main Menu Model
---


There are two model prefabs needed in addition to the main one that runs in game:

`Main Menu` and `Spawn`

## Main Menu 
The root of the object should have an animator attached.

<img src="/img/mason/MainMenu.png" alt="Main Menu" width="60%"/>

Drag your main game prefab into the scene, right click it in the hierarchy and select unpack. Then right click and select strip components. Then reposition mechanisms as you wish and click save.

<img src="/img/mason/MainMenuFinished.png" alt="Finished Main Menu" width="100%"/>

## Spawn
The root object should have a Rigidbody with 
| Parameter | Value | Description |
| --- | --- | --- |
| Mass | 98 | Inertia and weight |
| Drag | 4 | Linear dampening |
| Angular Drag | 5 | Rotational dampening |
| Center of Mass | "(0, 0, 0)" | Manual override |

Perform the same steps as the main menu model, but, use strip components (keep colliders), and keep the robot in its stowed configuration.

On the root add the bumper meshes to the bumper meshes list, and set the material index to recolor, (should be 0 for most bumpers).

Add the bumper tag to the bumper colliders.

<img src="/img/mason/SpawnFinished.png" alt="Finished Spawn" width="100%"/>
