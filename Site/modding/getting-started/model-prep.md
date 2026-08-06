---
sidebar_position: 2
id: model-prep
title: 🧊 Model Prep
sidebar_label: 🧊 Model Prep
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Model Preparation

Goes over the export, optimization, and import of robot CAD

## Export from CAD

:::tip

If you have the ability to copy or edit the CAD model, it is highly recommended that you swap to simplified swerve modules as they are a HUGE performance drain. Make sure the robot is using simplified motors and electronics (as seen in [FRCDesignLib](https://www.frcdesign.org/resources/frcdesignlib/)).

:::

Export the assembly to GLTF, the normal naming scheme for models is `team name or number(year)` coarse is recommended if you intend to not do optimizations.

<img src="/img/lynk/onshapeexport.png" alt="Onshape Export" width="65%"/>
<p></p>
:::note
GLTF files keep the file structure as it is in CAD, meaning subassemblies are separated. Identical components also share their object data, meaning modifications are applied to all identical components
:::

## Import to Blender

:::tip
If you're new to Blender, you can find the official UI docs [here](https://docs.blender.org/manual/en/latest/interface/index.html).
:::

Now we will open the model in Blender by simply dragging it on to the Blender window. Remember to delete the light, camera, and cube that are in the scene by default. I also highly recommend checking Merge Vertices, Onshape tends to generate duplicate vertices and this is a free optimization.

:::info

This takes a while and will frequently report NOT RESPONDING, on windows, just give it time, unless the window closes itself something is happening. Robot models are big.

:::

<img src="/img/lynk/blenderimport.png" alt="Blender Import" width="50%"/>

:::note

If your computer consistently crashes or fails to import to Blender you can alternatively export to Collada, HOWEVER, it will be less performant and will make your editor experience worse and the end user experience worse.

<img src="/img/lynk/colladaexport.png" alt="Alternate OnShape Export" width="50%"/>

:::

<img src="/img/modeling/uprightbot.png" alt="Upright Robot" width="35%"/>

Often when importing from CAD, the robot will come in horizontally. To fix this:
1. Select all parts if they are not already (press a or click and drag over the viewport)
2. Ensure your pivot point is set to the 3d cursor
3. Rotate the robot 90 degrees in the X axis (This is most common)
4. Ensure the robot is touching the floor

If you are familiar with Blender, now is your chance to optimize the model. The more time you spend now, the more performance the end user will have.

<div>
<img src="/img/modeling/3dcursor.png" alt="Select 3d Cursor" width="35%"/>
<img src="/img/modeling/2910floor.png" alt="Check floor alignment" width="65%"/>
</div>

## Optimization

<Tabs>
  <TabItem value="simple" label="Basic">
    If you're looking to optimize quickly and move on to Unity:
    1.  Select all objects, press tab to enter edit mode (this may take a bit to load) 
    2. Type `M` and select `By Distance` in the menu that pops up to merge vertices
    3. Go into the modifiers tab (the blue wrench), and add the following modifiers:
        - Limited dissolve
        - Decimate
        - Weighted normal
    4. This will apply the modifiers to the active object (the bright orange one). You can copy it to everything else by pressing `Ctrl-L` and selecting `Copy Modifiers`

    :::note

    This will allow for a very minor decimation compared to doing it properly (final decimate of ~0.9)

    :::
  </TabItem>
  <TabItem value="advanced" label="Advanced">
    This is the overview of how high poly robots are optimized for the base game. The exact process is up to the user, as long as the end result is the same.

    1. Pick an assembly to start with
        - Double click to select all children
        - Press `/` to enter local view (this will simplify hiding/unhiding parts)
    
    <img src="/img/modeling/hierarchy.png" alt="Top Level Assembly" width="35%"/>
    
    This will leave only the chosen subassembly shown

    <img src="/img/modeling/stationary.png" alt="Drivetrain Subassembly" width="45%"/>

    2. Identify what to do with various parts
        - Simple components (shafts, obscured parts, etc.) can be remodeled with basic shapes
        - Any part completely hidden from view can be deleted
        - Complex parts can be decimated

    ### Importing Simplified Components

    1. Verify import method in the asset browser is set to append under `Import settings -> Import Method`
    2. Drag your asset into the 3d viewport (away from the robot to prevent snapping)
    3. Change import method to link
    4. Enter the material tab for the part and reassign each one to an existing material.

    ### Replacing Components with Imports

    1. Select the part you want to replace
    2. Move 3d cursor to the center of the part with `Shift+S -> Cursor to Selected`
    3. Import the simplified part as `Import Setting -> Input Method -> Link`
    4. After importing, either:
        - `Outliner -> Right click on object -> Library Override -> Make -> Selected`
        - `OR with component selected -> Space (Or chosen search menu hotkey) -> Type library override -> Select Object -> Library Override -> Make`
    5. Select simplified part and move to 3d cursor with `Shift-S -> Selection to Cursor`
    6. Finish aligning manually or snapping with `Ctrl-Shift-Tab -> Snap Target -> Face`. Snapping can be enabled by holding ctrl and moving a part

    ### Remodeling Simple Components

    This is done differently for each part, but the general process is
    1. Enter edit mode on the part with `tab`
    2. Remodel the part using primitive Blender shapes (like replacing a hex shaft with a cylinder set to 6 vertices)
        - Try to keep the vertex count of gears between 12 and 24 based on size (using cylinders)
    3. Ensure import method is "Link"
    4. Enter the materials panel for the part and assign a proper material

    ### Decimating Complex Parts
    
    This is the most common method, used for machined plates, large sprockets, gears with pocketing, etc.

    1. Select one of the parts you would like to decimate
    2. Add the following modifiers:
        - Decimate (default settings)
        - Weighted Normals (`Right click -> Shade Auto Smooth`)
        - Unpin Smooth by Angle modifier, reorder above Weighted Normals
        - Enable "Keep Sharp" on Weighted Normals
    
    <img src="/img/modeling/modifiers.png" alt="Modifiers" width="35%"/>

    3. Select all parts you wish to decimate, make sure the one with the modifiers is active (lighter orange)
    4. Copy modifiers with `Ctrl-L -> Copy modifiers`
    5. Enter edit mode with `tab`, then do `M -> By Distance` to merge vertices
    6. Exit edit mode
    7. Go one part at a time and reduce the Ratio parameter under Decimate until right before geometry starts to look noticeably worse
    8. Ensure import method is Link
    9. Enter materials panel and assign a proper material

  </TabItem>
</Tabs>  

## Export from Blender

Once you are happy click `File -> Export -> FBX`, then export to `[Team Number]([Year]).fbx`
You can now close Blender, and delete the `.gltf` file.

You can now open the MoSim project in Unity
