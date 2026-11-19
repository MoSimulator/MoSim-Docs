---
sidebar_position: 7
id: mecanum
title: Mecanum Rollers
sidebar_label: Mecanum Rollers
---

# Mecanum Rollers

<img src="/img/mason/mecanum/Starting.png" alt="Starting" width="60%"/>

For mecanum you have two main options. Which one you go with depends on your load form.

For intakes where you are primarily moving up with gentle coersion, simply angle the interaction roller to 45 degrees or less relative to the mecanum wheel.

For situations like the one above, where you are primarily sliding sideways, you can stack a belt interaction roller inside the main interaction roller. This will apply a sideways force to the object and create the net force behaviour of a mecanum wheel. Arrows on the top and bottom tell you the active faces as well as the direction of force they apply.

<img src="/img/mason/mecanum/ArrowFace.png" alt="Arrow Face" width="60%"/>

Finish by adding them to the subsystems Roller Group, and applying a reasonable down multiplier. (0.5 is a good starting point)

<img src="/img/mason/mecanum/Finishing.png" alt="Finishing" width="60%"/>
