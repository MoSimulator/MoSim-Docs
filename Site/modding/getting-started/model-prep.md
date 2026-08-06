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

Now we will open the model in blender by simply dragging it on to the blender window. Remember to delete the light, camera, and cube that are in the scene by default. I also highly recommend checking Merge Vertices, Onshape tends to generate duplicate vertices and this is a free optimization.

:::info

This takes a while and will frequently report NOT RESPONDING, on windows, just give it time, unless the window closes itself something is happening. Robot models are big.

:::

<img src="/img/lynk/blenderimport.png" alt="Blender Import" width="50%"/>

:::note

If your computer consistently crashes or fails to import to blender you can alternatively export to Collada, HOWEVER, it will be less performant and will make your editor experience worse and the end user experience worse.

<img src="/img/lynk/colladaexport.png" alt="Alternate OnShape Export" width="50%"/>

:::

<img src="/img/modeling/uprightbot.png" alt="Upright Robot" width="35%"/>

Often when importing from CAD, the robot will come in horizontally. To fix this:
1. Select all parts if they are not already (press a or click and drag over the viewport)
2. Ensure your pivot point is set to the 3d cursor
3. Rotate the robot 90 degrees in the X axis (This is most common)
4. Ensure the robot is touching the floor

If you are familiar with blender now is your chance to optimize the model. The more time you spend now, the more performance the end user will have.

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
    
  </TabItem>
</Tabs>  

## Exporting

Once you are happy click `File -> Export -> FBX`, then export to `[Team Number]([Year]).fbx`
You can now close Blender, and delete the `.gltf` file.

You can now open the MoSim project in Unity
