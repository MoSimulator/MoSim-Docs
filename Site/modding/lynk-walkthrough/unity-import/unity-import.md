---
sidebar_position: 1
---

# Unity Import

Create a new folder under `Prefabs/Reefscape/Robots/Mods` for your mod and name it what you wish to name your pack

:::note

This is the overall PACK, not the robot itself

:::

Create a new `ReefscapeModpackMetadata` under `Games/Reefscape` and fill out the fields (picture is incorrect)

![Create Metadata](@site/static/img/lynk/createmetadata.png)

![Modpack Info](@site/static/img/lynk/modpackinfo.png)

Copy the existing 2910 folder under `Prefabs/Reefscape/Robots` to your new folder; we will use this as an example to follow along.

To make the next step not painful, I recommend clicking the lock in the top right of the inspector menu.

![Lock](@site/static/img/lynk/lockopen.png)

This locks the inspector object allowing you to click freely

Now open the 2910 folder you copied and drag the `JackInTheBot` Scriptable object into the robots list
Disable the lock.

Then rename the 2910 things to 9496 and Lynk, after doing so, re-fill out the 9496 SO (scriptable Object) as the fields are outdated.

Now go to the `Imports/Reefscape/Models/Robots` folder add a 9496 folder and import `9496(2025).fbx`

With that done we can go to the 9496 prefab we made in our folder, and delete everything but the drivetrain.

![Empty Prefab](@site/static/img/lynk/emptyprefab.png)

Now drag the 9496 model from the import into the hierarchy and we are ready to start
