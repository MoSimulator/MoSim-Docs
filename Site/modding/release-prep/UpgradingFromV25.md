---
sidebar_position: 8
id: modupgrading-v25
title: Upgrading v25 Mods
sidebar_label: Upgrading v25 Mods
---

Since v25, the modding system has changed in several ways. Most of these changes affect the APIs and project structure, so not every mod will need every update. Review the sections below and update your mod with the changes that are relevant to it.

## Drivetrain Changes (Required)
The drivetrain physics system has changed and every mod will need its robot's drivetrain to be re-tuned. Update and tune your robot's drivetrain using the [drivetrain setup guide](../mechanisms/DriveSetup.md).

## API Changes
The largest API changes has been the addition of the new [Generic Roller](../api-and-scripting/misc-apis/RollerInterfaces.md) and changes to the [Reefscape Auto Align](../api-and-scripting/misc-apis/ReefscapeAutoAlign.md). Please review the relevant documentation for both of these if they pertain to your robot(s).

## Modpack Folder and Project Setup
Most modpack folders remain the same, however, please make the following changes:
- Move the game's mods folder from `Reefscape/Robots/Mods` to `Games/<GameName>/Robots/Mods`.
- Use one [Assembly Definition File](https://docs.unity3d.com/2023.1/Documentation/Manual//cus-asmdef.html) for the entire modpack, rather than one per robot.
- Add a robot spawn prefab for the robot spawn selection menu. See the [spawn prefab instructions](./menumodel#spawn).
- Update the robot metadata file by re-adding the robot prefabs, preload game piece, and tutorial cards.
- Put each alternate robot in a subfolder within the team's folder. Add an entry for each alternate robot and assign its prefabs as you did for the main robot. If you wish to use a custom name, enter one into the textbox, otherwise the a version name will be auto assigned (`v2`, `v3`, etc.).
