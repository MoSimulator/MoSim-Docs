---
sidebar_position: 8
id: runninglocally
title: Running Mods Locally
sidebar_label: Running Mods Locally
---

Once you have your robot prefab setup, you can now test it locally. The easiest way to do this is open the `Robot Switcher` tool under `Tools` → `Modding` → `Robot Variant Switcher`. This tool allows you to select a robot metadata file and robot variant if your robot has alternate variants and will spawn that robot when you start a match without needing the main menu.

<img src="/img/mod-uploading/robot_switcher_tool.png" alt="Robot Switcher Tool" width="50%"/>

Once you have your robot mostly complete, another option to test running your mod is by making a local build under `Tools` → `Modding` → `Build Selected Modpack (Assets + Code)`. Click on your modpack's metadata file and then click that button to make a local build. This will then make a local build to the `BuiltMods` folder in the root folder of your MoSimulator project. You can then move this built folder to your local mods folder, which you can find by launching the game then going to `Settings` → `Misc.` → `Open Log File Location`. This is also how you would install and play mods without having Steam open or launching through Steam.