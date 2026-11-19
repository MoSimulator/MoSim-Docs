---
sidebar_position: 1
id: swm
title: Shoot While Moving (SWM)
sidebar_label: SWM
---

Shoot while moving (SWM) is often used for turret-based robots. In Unity we utilize a system where we offset our reference transform instead of the target we are shooting at. This allows us to continue using the standard API for identifying targets. The core of SWM is based on the concept of `Relativity`, which for brevity we will not explain here.

## Implementations

The simplest is direct velocity offset, though we do not recommend it, as Unity's velocity readings are particularly bad and you will have accuracy issues. Our recommended minimum is a basic filter, which is provided inside the `RebuiltTurretBase` class.

```cs
[Header("SWM")] 
[SerializeField] protected Transform swmRef;
[SerializeField] protected SOTMSettings swmSettings;
private Vector3 _smoothedVelocity;

protected virtual void HandleSwm()
{
  Vector3 robotVelocity = rb.velocity;
  _smoothedVelocity = Vector3.Lerp(_smoothedVelocity, robotVelocity, swmSettings.velocityDampeningValue);
  swmRef.position = swmRef.parent.position + (_smoothedVelocity * swmSettings.timeOfFlight);
}
```

The other option, for robots that need abnormally high SWM accuracy, is this feed-forward-based filtering system.

```cs
[Header("SWM")]
[SerializeField] protected Transform swmRef;
[SerializeField] protected SOTMSettings swmSettings;

[Header("SWM Filtering")]
[SerializeField] private bool enableTranslationFeedForward = true;
[SerializeField, Min(0f)] private float swmGroundCorrectionSmoothingTime = 0.1f;

[Header("SWM Debug")]
[SerializeField] private Vector3 debugSwmPredictedVelocity;
[SerializeField] private Vector3 debugSwmGroundCorrection;
[SerializeField] private Vector3 debugSwmEstimatedVelocity;

private Vector3 filteredSwmGroundCorrection;
private Vector3 filteredSwmVelocity;
private Vector3 previousSwmPrediction;
private bool previousSwmFeedForwardEnabled;
private float previousSwmSampleTime;
private bool hasPreviousSwmPrediction;

protected virtual void HandleSwm()
{
    Vector3 measuredVelocity = rb.GetPointVelocity(swmRef.parent.position);

    Vector3 prediction = enableTranslationFeedForward && DriveController != null
        ? DriveController.GetTargetWorldVelocity()
        : Vector3.zero;
    prediction.y = 0f;

    bool consecutiveSample = hasPreviousSwmPrediction &&
                             previousSwmFeedForwardEnabled == enableTranslationFeedForward &&
                             Time.fixedTime - previousSwmSampleTime <= 1.5f * Time.fixedDeltaTime;

    Vector3 correction = measuredVelocity -
        (consecutiveSample ? previousSwmPrediction : prediction);

    if (!consecutiveSample)
    {
        filteredSwmGroundCorrection = correction;
    }
    else
    {
        float response = swmGroundCorrectionSmoothingTime > 0f
            ? 1f - Mathf.Exp(-Time.fixedDeltaTime / swmGroundCorrectionSmoothingTime)
            : 1f;

        filteredSwmGroundCorrection = Vector3.Lerp(
            filteredSwmGroundCorrection, correction, response);
    }

    filteredSwmVelocity = prediction + filteredSwmGroundCorrection;

    swmRef.position = swmRef.parent.position
        + (filteredSwmVelocity * swmSettings.timeOfFlight);

    previousSwmPrediction = prediction;
    previousSwmFeedForwardEnabled = enableTranslationFeedForward;
    previousSwmSampleTime = Time.fixedTime;
    hasPreviousSwmPrediction = true;

    debugSwmPredictedVelocity = prediction;
    debugSwmGroundCorrection = filteredSwmGroundCorrection;
    debugSwmEstimatedVelocity = filteredSwmVelocity;
}
```

## Usage

Make sure the SWM ref has an offset parent such that it's at 0,0,0 locally, and that the parent is over wherever the shooting is occurring from. Now all you need to do is assign the SWM ref transform as the reference transform on all of your interpolations and turrets.
