---
sidebar_position: 3
id: tutorialcards
title: Tutorial Cards
sidebar_label: Tutorial Cards
---

Each robot should have tutorial cards that explain special functions of that robot. Start by coming up with a list of about 3 things about your robot - they can be a special control scheme, mechanism, a spot the robot is more accurate from, a cool thing it can do, or anything else.

## Taking Pictures

The easiest way to take pictures is by taking screenshots in the Unity editor's scene view. Before doing so, we recommend switching to high fidelity graphics (doing so in the Steam or GitHub version of the game should also update it for your editor), and disabling gizmos, which are the visualizations Unity provides in the scene view, as well as axis gridlines. To toggle gizmos on and off, press the globe icon on the right side of the scene view toolbar, and to toggle the gridlines, press the waffle shape with the letter Y on it.

<img src="/img/tutorial-cards/disable_gizmos.jpg" alt="Disable gizmos" width="50%"/>

After importing your images by dragging them into the project view, select them and set the Texture Type to `Sprite (2D and UI)` and the Sprite Mode to `Single`, then click Apply.

<img src="/img/tutorial-cards/image_settings.jpg" alt="Image import settings" width="50%"/>

## Tutorial Card Prefab

Make a copy of the tutorial card prefab from another robot and open it. Then, select the `Image` object from the hierarchy, and set its Source Image field to the image you imported earlier. You can do this either by dragging in your image from the project view, or by clicking the circle with a dot inside of it next to the field and searching for your image by name.

Then, select the `Text` object from the hierarchy and write your tutorial card text in the Text Input box. Move the text and scale the image until you're happy with how it looks, and move on to the next one.

<img src="/img/tutorial-cards/card_prefab.jpg" alt="Tutorial card prefab" width="50%"/>

## Assigning Tutorial Cards

In your robot's metadata scriptable object (`[game] Robot Metadata SO`), there's an array called Tutorial Cards. Drag each of your tutorial card prefabs into this array, in the order you want them to show up in game from left to right.

<img src="/img/tutorial-cards/robot_metadata_so.jpg" alt="Robot metadata scriptable object" width="50%"/>
