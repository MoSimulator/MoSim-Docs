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

Export the assembly to GLTF, the normal naming scheme for models is `team name or number(year)`. For resolution, use Medium if you intend to optimize, and Coarse if you don't.

<img src="/img/lynk/onshapeexport.png" alt="Onshape Export" width="65%"/>
<p></p>
:::note
GLTF files keep the file structure as it is in CAD, meaning subassemblies are separated. Identical components also share their object data, meaning modifications are applied to all identical components
:::

## Import to Blender

:::tip
If you're new to Blender, you can find the official UI docs [here](https://docs.blender.org/manual/en/latest/interface/index.html).
:::

Now open the model in Blender by simply dragging it on to the Blender window. Remember to delete the light, camera, and cube that are in the scene by default. Be sure to check Merge Vertices, as Onshape tends to generate duplicate vertices and this is a free optimization.

:::info

This is quite a laggy process, so be patient. It will frequently claim it is not responding, but it will eventually finish.

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
    1.  Select a part with the mouse and then click A to select all. In the top left corner, click the object mode button and switch to Edit Mode (or press `tab`). This will lag significantly upon first open.
    2. In edit mode, click M and select Merge by Distance from the new menu. This is again, quite laggy the first time. Once finished, in the bottom left a `> Merge by Distance` will appear. Select it and change merge distance to 0.0001m, it will reperform the merge.

    <img src="/img/modeling/BlenderMergeByDistance.png" alt="Check floor alignment" width="60%"/>

    3. Staying in edit mode, click X, then select Limited Dissolve from the new menu. This is also quite laggy. Once finished, in the bottom left a `> Limited Dissolve` will appear. Select it and change `Max Angle` to 5. it will reperform the dissolve.

        <img src="/img/modeling/BlenderLimitedDissolve.png" alt="Check floor alignment" width="90%"/>

    4. Exit edit mode back to object mode. From the right side of the screen, select the Modifier tab (the wrench icon). While holding alt, click the add modifier button and then select `Generate -> Decimate` from the menu. Then Add `Normals -> Smooth By Angle`, and `Normals -> Weighted Normal`. While holding alt, enable `Keep Sharp` on the weighted normal.

    <img src="/img/modeling/BlenderDecimate.png" alt="Check floor alignment" width="50%"/>

    5. On the top right, click the third dropdown from the center `Viewport Overlays` and enable Statistics. Your goal is to get the bottom `Triangles` number below 1 million, but the lower the better.

    <img src="/img/modeling/BlenderViewportOverlays.png" alt="Check floor alignment" width="45%"/>

    6. In the Modifier tab, hold alt and select `Ratio`, lower it to about 0.5 as a starting point, then click enter (no longer holding alt). this will recompute the decimate modifier. Continue to lower `Ratio` until you get the Triangles number below 1 million. This may cause some parts to become significantly deformed. You can simply select them individually and increase the ratio until it looks good again. if parts are not visible or are unneccesary, they can be deleted.
  </TabItem>
  <TabItem value="advanced" label="Advanced">
    This is the overview of how high poly robots are optimized for the base game. The exact process is up to the user, as long as the end result is the same.

    1. Pick an assembly to start with
    - Double click to select all children
    - Press `/` to enter local view (this will simplify hiding/unhiding parts)
    
    <div>
    <img src="/img/modeling/hierarchy.png" alt="Top Level Assembly" width="40%"/>
    <img src="/img/modeling/stationary.png" alt="Drivetrain Subassembly" width="35%"/>
    </div>

    This will leave only the chosen subassembly shown

    2. Identify what to do with various parts to optimize them
        - Simple components (shafts, obscured parts, bolts, etc.) can be remodeled with basic shapes
        - Common components may already be remodeled in the [Asset Library](https://drive.google.com/drive/folders/1fEwXXF1eMvkxc63vULps3WeAayXQikqW?usp=sharing) and can be swapped in
        - Any part completely hidden from view can be deleted
        - Complex parts such as machined plates should use the basic optimization process, pushing the decimate ratio as low as possible for each individual part without sacrificing visual quality

    ### Importing Library Components

    1. Ensure the [Asset Library](https://drive.google.com/drive/folders/1fEwXXF1eMvkxc63vULps3WeAayXQikqW?usp=sharing) is setup correctly
    2. Verify import method in the asset browser is set to append under `Import settings -> Import Method`
    3. Drag your asset into the 3d viewport (away from the robot to prevent snapping)
    4. Change import method to link
    5. Enter the material tab for the part and reassign each one to an imported material.

    ### Replacing Components using the Asset Library

    1. Select the part you want to replace
    2. Move 3d cursor to the center of the part with `Shift+S -> Cursor to Selected`
    3. Import the simplified part from the library with `Import Setting -> Input Method -> Link`
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

    1. Follow steps 1-5 of the Basic optimization docs, however only apply it to the parts that need it instead of everything
    2. Go one part at a time and reduce the Ratio parameter under Decimate until right before geometry starts to look noticeably worse
    3. Ensure import method is Link
    4. Enter the materials panel and assign a proper material
  </TabItem>
</Tabs>  

## Export from Blender

Once you are happy click `File -> Export -> FBX`, then export to `[Team Number]([Year]).fbx`. You can now close Blender, and delete the `.gltf` file.

<img src="/img/modeling/ExportFBX.png" alt="Check floor alignment" width="85%"/>

You can now open the MoSim project in Unity
