---
sidebar_position: 2
id: complexcollider
title: Guess Complex Collider
sidebar_label: Guess Complex Collider
---

When working, you may notice that the per triangle collider script really struggles to generate simple and usable colliders for pocketed plates such as the one below.

<img src="/img/mason/tools/pocketed_plate.png" alt="Pocketed plate" width="50%"/>

This is where the complex collider guess comes in. When you select it, it will prompt you for a collider count.

<img src="/img/mason/tools/complex_collider_prompt.png" alt="Collider count prompt" width="50%"/>

To use it, make a guess as to the minimum needed to follow the shape and select `Create 4 Colliders`. Generally, if per triangle fails to make it, this will work, or at least get you a usable starting place.

:::note
There is no way to know the correct collider count; you will simply gain a feel for the correct number as you work.
:::

<img src="/img/mason/tools/complex_collider_result.png" alt="Complex collider results" width="50%"/>

In this instance, 6 and 8 were the values I tried. Both of them had issues with the gap for fuel to move through, so they needed cleanup, but they were broadly usable as starting places, unlike what per triangle was creating.
