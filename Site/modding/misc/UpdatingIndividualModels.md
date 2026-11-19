---
sidebar_position: 6
id: modelupdate
title: Updating Partial Models
sidebar_label: Updating Partial Models
---

# Updating Partial Models

If a team decides to provide an updated model for a part of a robot while you are actively developing, you do _not_ need to update the entire model. Start by exporting only the part of the robot they have updated.

Perform the standard optimization process on the updated model.

<img src="/img/mason/updatedModel.png" alt="Updated Model" width="60%"/>

After optimizing the updated model, import it to unity, then delete the old model and place the new model in its place. Paint it either manually or by selecting only it and then using the autopaint tool. Then, seperate parts that need separated and run auto organize to finish it off.

<img src="/img/mason/PostSwap.png" alt="Post-Swap" width="70%"/>
