---
sidebar_position: 4
id: initialsetup
title: 🤖 Initial Robot Setup
sidebar_label: 🤖 Initial Robot Setup
---

Goes over model import, setup, and colliders

:::important 
**Prerequisites**
* [Unity Manual: Prefabs & Unpacking](https://docs.unity3d.com/Manual/CreatingPrefabs.html)
* [Unity Manual: Materials & Mesh Renderers](https://docs.unity3d.com/Manual/class-MeshRenderer.html)
* [Unity Manual: The Hierarchy Window](https://docs.unity3d.com/Manual/BuildingScenes.html)
* [Unity Manual: Introduction to Colliders](https://docs.unity3d.com/Manual/CollidersOverview.html)
* [Unity Manual: Mesh Colliders](https://docs.unity3d.com/Manual/class-MeshCollider.html)
:::

## Starting with a basic setup
Open your empty robot prefab then drag your model into the Scene Hierarchy, right-click the model thats now in the Hierarchy, and select Unpack Completely.

## Color

<img src="/img/mason/initsetup/initial.png" alt="Initial" width="100%"/>

If the robot was colored in CAD, use the recolor tool: select `Tools > Replace Colors`. Ensure Excluded Materials is not (0) (click Reset to Defaults if it is). Scroll down in the preview to locate a main color, then adjust Light Offset until it matches a material in your folder.

<img src="/img/mason/initsetup/replace.png" alt="Replace" width="100%"/>

The tool isn't perfect, so manual touch-ups may be needed. Open your `Materials` folder and drag materials directly onto the parts you want to recolor.

<img src="/img/mason/initsetup/recolor.png" alt="Recolor" width="60%"/>

If many parts share an incorrect color (e.g., gray instead of black), select one, right-click, and select `Select > AllOfMaterial`. You can then mass-recolor them from the Mesh Renderer in the Inspector.

## Hierarchy

Extract subsystems from the initial model import and delete the original CAD container structure.

<img src="/img/mason/initsetup/initial_hierarchy.png" alt="Initial Hierarchy" width="40%"/>

Select the extracted subsystems, right-click in the Hierarchy, and select Auto Organize.

<img src="/img/mason/initsetup/auto_organize.png" alt="Auto Organize" width="40%"/>

This cleans up the hierarchy, strips unnecessary parent objects, and nests geometry into a clean Model child object.

## Subsystem Setup
Rename your subsystems to clear names (e.g., Drive, Intake, Shooter). Hide everything except the subsystem you are actively working on.

<img src="/img/mason/initsetup/hide_subsystems.png" alt="Hide Subsystems" width="100%"/>

for Internal/Hidden parts (e.g., frame rails) right-click and select `Mesh > Guess Generic Shape` to generate a generic shape model which are more performant than exported meshes.

For General colliders, select all subsystem parts, right-click, and select `Collider > Guess Generic Colliders`. Inspect the new Collider component at the bottom of the Inspector. Press `Shift + Alt + C` to enter Collider Mode to adjust or delete colliders manually. 

:::note
When guessing colliders for systems with rollers, use the "box only" variant of the guess generic colliders tool to avoid generating colliders on rollers
:::

<img src="/img/mason/initsetup/guess_generic_colliders.png" alt="Guess Generic Colliders" width="50%"/>

For subsystems that move, you can move the model out of the subsystem, then align the subsystem root for your movement type (see [Understanding Joint Setup](/modding/physics/generic-joints)). Move stationary parent models to a "Stationary" subsystem if needed.

`Guess Generic Mesh` on hollow core rollers is particularly effective for performance and highly recommended.

<img src="/img/mason/initsetup/intake.png" alt="Intake" width="50%"/>

When guessing colliders for complicated parts you may notice it doing a particularly bad job. in those cases, you can delete the collider and then select the offending part.

<img src="/img/mason/initsetup/bad_guess.png" alt="Bad Guess" width="50%"/>

If automated guessing does a poor job on complex geometry, delete the collider, select the problematic parts, and add a Per-Triangle Mesh Collider. Adjust settings for a reasonable collider count, then click Generate Colliders.

<img src="/img/mason/initsetup/per_triangle.png" alt="Per Triangle" width="100%"/>
<img src="/img/mason/initsetup/good_guess.png" alt="Good Guess" width="50%"/>

## Initial Physics

For rollers that move together, select the group, right-click, and select Collider > Guess Interaction Roller (see [Physics Setup](../physics/physics-tuning/GeneralInteractionRollerTuning.md)).

<img src="/img/mason/initsetup/IrollerGuess.png" alt="IrollerGuess" width="60%"/>

Re-run Auto Organize on all subsystems at the end to lock in a clean structure.

(If your robot has a net, see [Net Setup](../physics/Nets.md) for details.)
