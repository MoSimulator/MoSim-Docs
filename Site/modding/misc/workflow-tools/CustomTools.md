---
sidebar_position: 1
id: customtools
title: Custom Tools
sidebar_label: Custom Tools
---

We have an expansive set of custom tools you can utilize to improve your workflow and make robots faster. A significant number of these are introduced in [Initial Robot Setup](../../getting-started/InitialRobotSetup.md).

## Coloring

<img src="/img/mason/tools/coloring.png" alt="Coloring tools" width="50%"/>

- `Replace Colors`: found under `Tools` → `Replace Colors`. Used to mass replace colors that are shade-shifted, such as in an Onshape export.

## Organization

- `Auto Organize`: found under the right-click menu of the **hierarchy**, NOT the scene view. Can be used to quickly format a subsystem to follow a set pattern.

### Mesh Operations

<img src="/img/mason/tools/mesh_ops.png" alt="Mesh operations" width="50%"/>

- `Merge`/`Unmerge`: these should be ignored, as they typically cause more problems than they are worth.
- `Guess Generic Shape`: will attempt to replace the selected object with a Unity generic, which is generally more performant.
- `Re-Instance Meshes`: fixes mesh drift such that most duplicate meshes are no longer used and are replaced with one instance. Makes selecting all of a mesh more useful.

### Collider Operations

<img src="/img/mason/tools/collider_ops.png" alt="Collider operations" width="50%"/>

- `Guess Generic Collider`: allows you to select a mesh and guess a generic collider that approximates its shape.
- `Guess Generic Collider (Box Only)`: allows you to select a mesh and guess a generic collider that approximates its shape. Will reject any mesh that does not resolve to a "box" generic.
- `Split Colliders`: allows you to easily remove a collider from a mesh component without having to redo the collider. Attaching colliders to meshes is considered bad practice.
- `Guess Interaction Roller`: will guess an interaction roller that fits the selected shape. Will also add the mesh to its animation slot.
- `Guess Interaction Roller (Merge Inline)`: will guess an interaction roller that fits the selected shape and merge multiple inline shapes into a single roller. Will also add the mesh to its animation slot.
- `Set Roller Length`: lets you resize the length of a roller by dragging with your mouse.
- `Set Roller Diameter`: lets you resize the diameter of a roller by dragging with your mouse.

### Cleanup Operations

<img src="/img/mason/tools/cleanup_ops.png" alt="Cleanup operations" width="50%"/>

- `Strip Components`: used to set up menu models, which will throw errors if components are left present.
- `Strip Components (Keep Colliders)`: used to set up spawn select models, which will throw errors if components are left but require colliders to be present.

### Hierarchy

<img src="/img/mason/tools/hierarchy_ops.png" alt="Hierarchy operations" width="50%"/>

- `Create Parent At Center of Mass`: creates a parent object at the mesh's center of mass, not its transform origin.
- `Create Roller Group`: creates a new roller group from all selected rollers.
- `Remove Empty Parents`: removes parents that have no components. Useful when cleaning a fresh model import.
- `Add To Roller Group`: allows you to add a roller to a group.
- `Create Offset Parent`: creates a parent at the selected object's transform origin, forcing its local position to 0,0,0.
- `Add Object as Roller Model`: puts you into collider select mode, where the next roller you select will get the previously selected object added as a model to rotate with it.
- `Snap to Mesh Center`: allows you to snap a selected object to the center of mass of another object.

### Select

<img src="/img/mason/tools/select_ops.png" alt="Select operations" width="50%"/>

- `All Instances of Mesh`: selects all objects that are using the mesh of the currently selected object.
- `All of Material`: selects all objects using the same mesh material as the currently selected object.
- `Collider Select Mode`: allows you to directly select colliders and interaction rollers in the scene view instead of from the hierarchy.

### Measure

<img src="/img/mason/tools/measure_ops.png" alt="Measure operations" width="50%"/>

- `Roller or Collider Gap`: puts you into measure mode, where you can get the closest distance between two colliders, a roller and a collider, or two rollers. The roller distance is adjusted based on the roller's max squish.

## Shortcut Table

:::note
Alt is replaced with Cmd on macOS.
:::

| Tool / action                                   | Shortcut                        |
| ----------------------------------------------- | ------------------------------- |
| Tool Palette                                    | **Alt+Shift+T**                 |
| Collider Select Mode                            | **Alt+Shift+C**                 |
| Merge Meshes                                    | **Alt+Shift+M**                 |
| Unmerge Meshes                                  | **Alt+Shift+U**                 |
| Guess Generic Shape                             | **Alt+Shift+G**                 |
| Guess Generic Collider                          | **Alt+Shift+B**                 |
| Guess Interaction Roller                        | **Alt+Shift+R**                 |
| Split Colliders                                 | **Alt+Shift+X**                 |
| Select All Instances of Mesh                    | **Alt+Shift+I**                 |
| Select All of Material                          | **Alt+Shift+A**                 |
| Create Parent at Center of Mass                 | **Alt+Shift+P**                 |
| Create Offset Parent                            | **Alt+Shift+O**                 |
| Create Roller Group                             | **Alt+Shift+K**                 |
| Add to Roller Group                             | **Alt+Shift+J**                 |
| Auto Organize                                   | **Alt+Shift+H**                 |
| Measure Roller or Collider Gap                  | **Alt+Shift+D**                 |
| Add Object as Roller Model                      | **Alt+Shift+V**                 |
| Drag to Set Length and Diameter                 | **Alt+Shift+L**                 |
| Snap to Mesh Center                             | **Alt+Shift+S**                 |
| Guess Collider: Box Only / Roller: Merge Inline | **Palette or right-click menu** |
| Re-Instance Meshes / Remove Empty Parents       | **Palette or right-click menu** |
| Strip Components / Keep Colliders variant       | **Palette or right-click menu** |
