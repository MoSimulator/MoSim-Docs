---
sidebar_position: 3
id: foldersetup
title: 📂 Modpack Setup
sidebar_label: 📂 Modpack Setup
---

Set up the modpack to contain your robot mods

:::important 
**Note:**
You are expected to have a basic understanding of Unity before starting to mod a robot. We heavily encourage you to walk through Unity's [Getting Started](https://unity.com/learn/get-started#beyond-beginners) documentation and doing at least the [Unity Essentials](https://learn.unity.com/pathway/unity-essentials) pathway before you continue. We will provide minimal assistance in basic Unity understanding and will direct you to their documentation and tutorials if you have not read them already.
:::

## Initial Setup
To get started making a mod, open the `Create Modpack Folder` menu under `Tools\Modding\Create Modpack Folder`. Set the game to the correct game for your mod then fill out the modpack name and mod author (will be displayed in the mod browser). Then enter the team names and numbers for your mod's robots along with any alternate robots and their names. Once all of the info is entered, click `Create`.

:::note
If creating a robot with a turret for Rebuilt, select the `Turret` checkbox for that robot
:::

## Modpack Setup
Once the modpack is created, open the folder for your first mod and click on the robot asset. Fill out the rest of the information for the robot including the `Robot Image`, and the `Preload Game Piece`. You can rename your robot prefabs if desired as well to clean them up.

### Alternate Robot Setup
This step only applies to teams who have multiple robots for the same game.

The modpack folder generation may not generate alternate robots correctly, so you often need to clean these up. Enter the desired display name or leave it blank if you wish for it to auto generate the name using version semantic naming (v2, v3, etc.). You can also rename the prefab names to clean them up if desired. Finally, give each alternate robot a `Preload Game Piece` as you did for the main robot.