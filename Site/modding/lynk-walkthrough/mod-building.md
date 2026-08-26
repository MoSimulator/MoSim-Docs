---
sidebar_position: 9
---

# Mod Building

## Automatically Building

- For easy building, [use this tool](https://github.com/MoSim-Modding-Fun/v25-Mod-Builder) made by [Seb](https://github.com/dfn-slxxp). Follow the docs on the tool to use it.

## Manual Building

- Go to `Window/Asset Management/Addressables/Groups`

![Find Groups Here](@site/static/img/lynk/addressablegroup.png)

- Create a `New/Packaged Assets`. This is an addressable group and will be used to bundle your mod

![Create Addressable Group](@site/static/img/lynk/packedassets.png)

- Drag in your mod folder under this new asset

- Expand the dropdown and make sure that all of your folders and files are in there

- Add the `modpack_metadata` label to your modpack folder

![Add Metadata Label to Folder](@site/static/img/lynk/modpackasset.png)

- Now click on your modpack asset and go to `Tools/Addressables/Set Custom Addressables Path`. This should add custom Build and Load Paths for your mod

![Set Custom Paths](@site/static/img/lynk/customaddressable.png)

- Select those new paths and then make sure that Include in Build is checked.

![Build Settings](@site/static/img/lynk/broncobots.png)

- Deselect `Include in Build` for every other asset

- Now go to `Build/New Build/Default Build Script`. This should build your mod to the Build Path shown in the inspector.

![Build to Script Parameters](@site/static/img/lynk/newbuild.png)

- Navigate to your base Unity MoSim install.

- Under that folder you should see a `Mods` folder and then your custom mod folder. Inside there should be a `.bundle` file. This is your mod’s assets.

- Under your Unity MoSim folder you should see a Library/com.unity.addressables/aa/Windows. There should be these files: catalog.hash, catalog.json, settings.json.

- You should also find a dll file for your mod under Library/ScriptAssemblies/YOURMOD.dll

- Copy all of these files to a folder **specifically labelled your addressable group name**.

- Finally, add this folder to your mods folder (insert mods folder locations for reference) and then test by running MoSim.

![Place DLL](@site/static/img/lynk/dll.png)

- If this works, you are good to go! Zip the folder and have fun!
