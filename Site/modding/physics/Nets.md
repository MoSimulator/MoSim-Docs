---
sidebar_position: 9
id: nets
title: Nets
sidebar_label: Nets
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

How to create flexible nets for your robots

## Authoring

<Tabs>
  <TabItem value="simple" label="Simple Nets">
    Start by placing a `NetParent` object, then a child at 0 all, named `net`. Move the parent to be roughly at the center of where you want the net to be. Then, add 4 objects named `00`, `01`, `10`, `11` childed to `NetParent`.

    Finally, add a `Net Parent Follower` and `Net Solver` to the `net`. Then at the top of the script, assign the four objects we created to their respective corners.

    <div>
    <img src="/img/mason/nets/simple_net.png" alt="Simple net" width="40%"/>
    </div>
    <div>
    <img src="/img/mason/nets/simple_hierarchy.png" alt="Simple net hierarchy" width="50%"/>
    </div>

    Now you can move the four corner objects around to size your square net. If a corner moves with another subsystem, simply rechild it to be under that subsystem.

    :::note
    When tuning a simple net, the width and height do not affect the net that is created. To make a net feel "looser" you can decrease the anchor border fraction.
    :::
  </TabItem>
  <TabItem value="advanced" label="Advanced Nets">

    Start by placing a `NetParent` object, then a child at 0 all, named `net`. Move the parent to be roughly at the center of where you want the net to be.
    Add a `Net Parent Follower` and `Net Solver` to the `net`. Then click the three buttons on the Net Solver script and select `Generate Edge Anchors`. Then set the `Anchor Border Fraction` to 0.

    <img src="/img/mason/nets/generate_anchors.png" alt="Generate edge anchors" width="30%"/>

    This will make a new child of the net object named `NetEdgeAnchors`. Start by rechilding it to the `NetParent`.

    <div>
    <img src="/img/mason/nets/adv_hierarchy.png" alt="Advanced net hierarchy" width="50%"/>
    </div>

    Now when you click on the net, a number of green points will appear. Those are the "pin points" where the net is being held. Mousing over one will allow you to click it and select that node. Moving it will move that node's location.

    <div>
    <img src="/img/mason/nets/adv_net.png" alt="Advanced net" width="50%"/>
    </div>

    It is time-consuming and tedious, but this allows you to make advanced net shapes that are non-square, so long as they have continuous edges.

    :::note
    The number of edge nodes you have to work with is determined by the x and y resolution of the net. 8x8 is recommended for most, but up to 15x15 has been used for complex net shapes.
    :::
  </TabItem>
</Tabs>

### Unique Uses

Sometimes you need to do something a little beyond the above. You can use the nets in very interesting ways, such as replacements for flexible plates.

<img src="/img/mason/nets/flex_plate.png" alt="Flexible plate" width="50%"/>

When setting up an advanced net, instead of using the `Generate Edge Anchors` button you can manually assign them from the `Custom Anchors` dropdown.

<img src="/img/mason/nets/custom_anchors.png" alt="Custom anchors" width="50%"/>

These require you to manually assign which node that transform pins, which means you are able to pin only the ends.

<img src="/img/mason/nets/end_pins.png" alt="End pins" width="50%"/>

By making the net have very low stretch and shear compliance with moderate bend compliance, you can make a plate that bends similar to IRL. By adding rows of sphere colliders to points on the robot, you can also make the robot capable of interacting with and bending the net. Setting gravity to a positive value also allows you to make the net appear to be a plate when not loaded, without sagging significantly.

<img src="/img/mason/nets/sphere_rows.png" alt="Sphere collider rows" width="50%"/>

You can also give a net a custom visible pattern, such as square mesh, diamond mesh, straps, or a flexible plate with cutouts.

Your pattern comes from a PNG's alpha channel. Opaque pixels show the material, while transparent pixels leave visible holes.

1. In an image editor, start with a transparent canvas and draw the parts you want to see. For example, draw crossing lines for mesh, wider strips for webbing, or a filled panel with transparent holes for a perforated plate. Use opaque white for a pattern you want to tint in Unity, and export as a PNG that preserves transparency. Painting the holes black will make black areas instead of holes.
2. Import the PNG into Unity. In its texture import settings, use **Alpha Source: Input Texture Alpha**, enable **Alpha Is Transparency**, and click **Apply**.
3. Duplicate `Net2.mat`, rename the copy, and put your PNG in its **Base Map** slot. This keeps the example's **Surface Type: Transparent**, **Alpha Clipping** enabled, **Threshold: 0.5**, and **Render Face: Both**, so the net is visible from either side. Adjust the Base Map color to tint your pattern.
4. Assign the new material to **Element 0** in the **Mesh Renderer** on the `net` object with the Net Solver.
5. Adjust the material's **Tiling** and **Offset** to size and position the pattern. The solver maps UVs from 0 to 1 across the net, so tiling of 1 by 1 maps the whole image once across it. For repeating mesh, make a seamless tile, set the texture's **Wrap Mode** to **Repeat**, and increase tiling to fit more repeats. For a single panel with specific cutouts, keep tiling at 1 by 1 and draw the full layout. Match the artwork's proportions to the net to avoid stretched holes. The pattern follows the surface as the net bends.

:::note
This changes the net's appearance only. Transparent holes do not remove solver nodes, triangles, or collision interactions. Keep tuning resolution, anchors, and compliance for the movement you want; you do not need a solver node for every visible strand or hole.
:::

## Tuning

When tuning a net there are a handful of important values: `Net Height`, `Net Width`, `Sub Steps`, `Stretch Compliance`, `Shear Compliance`, `Bend Compliance`, `Damping`, `Collision Radius`, and `Collision Layers`.

- `Use Authored Rest Shape` is used to select between the size being determined by `Rest Length Scale` or `Height` and `Width`.
- `Net Height` the planar height of the net. For advanced nets this is **not** determined by the prefab preview.
- `Net Width` the planar width of the net. For advanced nets this is **not** determined by the prefab preview.
- `Rest Length Scale` allows you to add slack to a net. Increasing it increases the amount of slack present; decreasing does the opposite.
- `Sub Steps` the number of substeps used to calculate the new net positions. 2-3 is usually sufficient, though sometimes up to 5 is needed.
- `Stretch Compliance` increasing this allows the net to stretch node to node more when **not** flexing.
- `Shear Compliance` increasing this allows the net to stretch node to node more when flexing.
- `Bend Compliance` increasing this allows the net to more aggressively deform from its starting shape.
- `Damping` dampens the motions of the solver. 0.1-0.3 is usually correct; underdamping can make nets seem unstable and overdamping can cause phasing.
- `Collision Radius` the radius the primary colliders reach from node to node. Values of 0.035 to 0.05 are usually fine.
- `Collision Layers` the layers that the net is allowed to check collisions for. `GamePiece1` is usually all that is necessary, though sometimes you may want to add the `Robot` layer.
