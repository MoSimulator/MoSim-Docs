---
sidebar_position: 3
id: turretfeedforward
title: Turret Feed Forward
sidebar_label: Turret Feed Forward
---

When working with turrets that have SWM, you may notice that they cannot keep up when rotating. This is an issue with it being a purely reactive system.

The solution is to pass in a feed forward.

## Solving the Issue

`turret.Align()` takes an override input `steerValue`. You can simply pass `RotateAction.ReadValue<float>()` into it, or you can pass in the following function:

```cs
[Header("Turret Feed Forward")]
[SerializeField, Min(0f)] private float turretSteerFeedForwardConstant = 6f;
[SerializeField, Range(0f, 0.8f)] private float groundYawCorrectionGain = 0.8f;
        
private float GetGroundYawCorrectedSteerInput(float steerInput)
{
  float predictedFeedForward = steerInput * turretSteerFeedForwardConstant;
  float correctedSteerInput = steerInput;
  debugGroundYawCorrection = 0f;
  debugGroundYawRate = 0f;

  if (robotRigidbody != null && turretSteerFeedForwardConstant > Mathf.Epsilon)
  { 
    debugGroundYawRate = -robotRigidbody.angularVelocity.y;
    
    if (hasPreviousGroundYawSample)
    {
      debugGroundYawCorrection = Mathf.Clamp(groundYawCorrectionGain, 0f, 0.8f) *
                                               (debugGroundYawRate - previousPredictedTurretFeedForward);
      correctedSteerInput += debugGroundYawCorrection / turretSteerFeedForwardConstant;
    }
    
    hasPreviousGroundYawSample = true;
  }
  else
  {
     hasPreviousGroundYawSample = false;
  }

  previousPredictedTurretFeedForward = predictedFeedForward;
  return correctedSteerInput;
}
```
