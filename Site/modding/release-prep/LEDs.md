---
sidebar_position: 2
id: leds
title: LEDs
sidebar_label: LEDs
---

LEDs communicate robot status through colors, textures, and animations. Use them to show the selected mode, intake activity, piece possession, or whether a mechanism is ready.

Reefscape uses texture patterns on existing LED meshes. Rebuilt provides generated strips with individual color control. Both approaches can be configured for your own robot.

## Reefscape LEDs

The Reefscape `LedStripController` works with `ReefscapeRobotBase` and `ReefscapeCommandRobotBase`. It selects a texture from the robot's current state and applies it to the LED meshes.

### Usage

Place the controller on the same GameObject as the robot base and robot game piece controller. It finds these components with `GetComponent`.

Create or identify the meshes that should display the LEDs, then configure the controller:

| Field | Usage |
| --- | --- |
| `leds` | Mesh GameObjects to display the pattern. Each needs a Renderer on that object. |
| `shaderGraphShader` | The LED pattern shader. |
| Texture fields | Patterns for the statuses the robot displays, such as Disabled, Intake, held pieces, modes, climbing, and auto-aligning. |

The shader must expose `_Texture2D`, `_X`, `_Y`, and `_intensity`. Use textures suited to the mesh's UV layout so the pattern follows the LED surface correctly.

At startup, the controller creates one material and assigns it to all listed Renderers. All meshes in that list share the selected pattern. Use separate materials and control logic if different groups need independent displays.

Configure the robot's Coral and Algae game piece nodes when using possession indicators. The built-in selection also reads the robot's mode, intake input, climbing state, and auto-align input.

### Custom Patterns

Choose a priority for overlapping conditions. For example, Disabled can take priority over climbing, which can take priority over piece possession and the ordinary mode display. Use an `if` / `else if` chain so one condition selects the display each update.

The following helper can be added inside the Reefscape controller to group its existing shader assignments. `LEDs` is the material created by that controller:

```cs
private void SetPattern(Texture pattern, float x, float y, float intensity = 20f)
{
    LEDs.SetTexture("_Texture2D", pattern);
    LEDs.SetFloat("_X", x);
    LEDs.SetFloat("_Y", y);
    LEDs.SetFloat("_intensity", intensity);
}
```

Then a status branch can select a texture and its shader settings together:

```cs
// Example inside the controller's status selection.
SetPattern(hasCoral, 1.5f, 0f);
```

The numbers are example settings for the shader. Tune them with your texture and mesh. When adding a new status, add its texture field, assign it in the Inspector, and include its condition at the intended priority. Update both legacy and command-base selection branches if both need that behavior.

## Rebuilt LEDs

Use `Games.Rebuilt.LEDs.LEDStripController` for a configurable set of generated strips. It builds the LED objects and exposes methods for coloring a whole display, one strip, or one LED. Supply your own status selection to connect it to the robot.

### Usage

1. Create a small LED mesh with a Renderer and save it as a prefab. Its material should support `_BaseColor` or `_Color`, plus `_EmissionColor` for emission.
2. Add the Rebuilt `LEDStripController` to the robot and assign that prefab to `ledPrefab`.
3. Add entries to `strips` and configure their layout.
4. Assign `emissionIntensity` for the desired brightness.

| Strip field | Usage |
| --- | --- |
| `stripName` | A label for the strip. |
| `ledCount` | Number of generated LEDs. Use a positive count. |
| `ledSpacing` | Distance between LEDs along local positive X. |
| `localPosition` | Strip position relative to the controller. |
| `localRotation` | Strip rotation in degrees. |
| `localScale` | Strip scale, affecting the LEDs and spacing. |
| `defaultColor` | Starting color when the strip is built. |

Spacing and position use local Unity units. Rotate the strip's container to run it along another direction. Use multiple entries for separate parts of the robot.

The controller builds strips during runtime `Awake()` and can generate an editor preview in a loaded scene or Prefab Mode. Change the layout fields and source prefab rather than editing generated children.

### Selecting a Status

The general strip controller does not choose status colors automatically. Add an updater to your robot or a separate component and pass in the conditions your robot already knows.

```cs
using UnityEngine;
using RebuiltLEDController = Games.Rebuilt.LEDs.LEDStripController;

// Members inside your robot or LED behavior component.
[SerializeField] private RebuiltLEDController statusLeds;

private void UpdateStatusLeds(bool disabled, bool intaking, bool ready)
{
    Color color;

    if (disabled)
        color = Color.cyan;
    else if (intaking)
        color = Mathf.FloorToInt(Time.time / 0.1f) % 2 == 0
            ? Color.yellow
            : new Color(1f, 0.25f, 0f);
    else if (ready)
        color = Color.green;
    else
        color = Color.white;

    statusLeds.SetAllStrips(color);
}
```

Call this method regularly after the strips have been built. Supply the disabled state, intake request, and readiness condition from your own robot. For example, readiness could combine joint arrival, flywheel speed, and aiming tolerance. Define what the light means through that condition.

The example gives Disabled the highest priority, then a flashing Intake display, then Ready, then the ordinary display. The colors and flash interval are example choices you can change.

### Individual Strips and Animations

The controller also exposes these writers. Indices start at zero:

```cs
statusLeds.SetAll(0, Color.green);          // Entire first strip.
statusLeds.SetLED(0, 0, Color.white);       // First LED of the first strip.
statusLeds.SetLED(0, 0, Color.white, 8f);   // With a custom emission intensity.
```

For a moving indicator, update each LED using time and its position within the strip:

```cs
private void ShowChase(Color color, float ledsPerSecond)
{
    for (int stripIndex = 0; stripIndex < statusLeds.strips.Count; stripIndex++)
    {
        var strip = statusLeds.strips[stripIndex];
        if (strip.ledCount <= 0) continue;

        int head = Mathf.FloorToInt(Time.time * Mathf.Max(0f, ledsPerSecond))
            % strip.ledCount;

        for (int led = 0; led < strip.ledCount; led++)
        {
            statusLeds.SetLED(stripIndex, led,
                led == head ? color : Color.black);
        }

        // Force the next solid-color write to repaint this strip.
        strip.lastColor = Color.clear;
    }
}
```

Choose either the solid/flash updater or the chase for the current status. Give one updater ownership of the display so separate functions do not overwrite each other's results.

`SetAll` caches the last whole-strip color. Individual `SetLED` calls do not update that cache, so invalidate it after a custom pixel pattern as shown when returning to solid colors. Treat `Color.clear` as the cache reset value in this example; use `Color.black` for an off LED.

### Scheduling

LEDs often need to change while the robot is disabled. Call your updater from an ordinary component `Update()`, or use a [Scheduled Robots](../api-and-scripting/ScheduledRobots.md) protected/frame callback. An enabled-only subsystem will stop updating the display while disabled.

Pass current status values on every update and keep the LED code focused on displaying them. The robot's mechanism and game piece logic should determine possession, readiness, and mode.
