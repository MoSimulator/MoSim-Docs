---
sidebar_position: 5
id: climbscorer
title: Reefscape Climb Scorer
sidebar_label: Reefscape Climb Scorer
---

The climb scorer script is required to be able to score climb points in Reefscape.

<img src="/img/mason/apis/climb_scorer.png" alt="Climb Scorer component" width="50%"/>

Add the script to the root of your robot prefab.

`Cage Scoring Trigger` is a box set to `Is Trigger` that should make up the region that the cage should occupy when climbing.

<img src="/img/mason/apis/cage_trigger.png" alt="Cage scoring trigger" width="50%"/>

`Auto Climb Triggers` are also `Is Trigger` boxes that can be used as the detection mechanism for auto climbing.

These can be accessed via:

```cs
[SerializeField] private ClimbScorer climbScorer;

bool ClimbCheck()
{
  return climbScorer.AutoClimbTriggered();
}
```
