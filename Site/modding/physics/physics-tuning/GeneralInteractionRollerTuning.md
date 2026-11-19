---
sidebar_position: 4
id: irollertuning
title: Interaction Roller Tuning
sidebar_label: Interaction Roller Tuning
---

| Field | Type | Range | Default | Units | What it does | Tuning notes |
|---|---|---|---|---|---|---|
| `radius` | float | — | 4.0 | inches | Wheel/roller radius; capsule radius, or in belt mode the reach either side of the belt surface. | Match CAD or larger. Also sets tip speed (`rpm × radius`), so it drives grip authority as much as `rpm` does. |
| `length` | float | — | 2.0 | inches | Span along local X — capsule height, or belt width. | Match CAD or greater. |
| `useBeltMode` | bool | — | false | — | Swaps the capsule for a box: contact normal becomes the roller's local up, and depth is measured along it instead of radially. | On for belts/conveyors, off for wheels. Changes gizmo colour (yellow vs cyan). |
| `beltLength` | float | — | 10.0 | inches | Travel span along local Z. Belt mode only. | Ignored when `useBeltMode` is off. |
| `maxSquish` | float | — | 0.5 | inches | Depth the game piece is allowed to compress into before hitting the solid inner core collider. | Set **higher** than CAD compliance — the piece is a solid body here. Core radius is `radius - maxSquish`, floored at 0.001 m. |
| `coreFriction` | float | 0–1 | 1 | — | Static/dynamic friction of the inner core's physic material (Maximum combine). | Only matters once a piece has squished all the way through; the motored channel does the real work. |
| `rpm` | float | — | 800 | rpm | Commanded free speed. Clamped every FixedUpdate to the selected motor's max. | can be set at runtime, or non destructive mutated, review scripting for details|
| `compressionStiffness` | float | — | 500 | N/m | Spring constant of the normal (squish) force. | With `frictionCoupledToNormal` on this becomes a **grip** parameter too — raise it when enabling that. 0 means zero grip on the coupled path. |
| `compressionDamping` | float | — | 50 | N·s/m | Damps the normal spring, applied only while the contact is closing. | Raise to stop pieces bouncing off the surface; can sometimes improve shot arc consistency. |
| `scrubForces` | float | 0–100 | 0 | — | Unmotored lateral channel that cancels sideways velocity across the roller axis. | A stabiliser against horizontal variation. |
| `motorSlipResponse` | float | 0.01–1 | 1 | fraction | Slip, as a fraction of surface speed, at which the motor is at full stall torque. | Lower = more aggressive speed matching. 1 is full-range ramp — leave it unless tuning a specific roller. Low values can cause distance spread. |
| `frictionCoupledToNormal` | bool | — | false | — | Bounds tangential impulse by `frictionCoefficient × normal force` instead of motor torque alone. | Makes grip follow the squish: fades to zero at release instead of cutting off at full strength, can significantly reduce exit spread. |
| `frictionCoefficient` | float | 0.1–20 | 3 | μ | Coulomb μ for the cap above. | Unused unless `frictionCoupledToNormal` is on. Affect is minimal to negligable |
| `oppositeReaction` | bool | — | true | — | Applies the equal-and-opposite impulse to the parent robot rigidbody. | Off decouples the roller from robot dynamics — useful for hood or high load systems that cant handle the back force, physically wrong otherwise. |
| `ignoreLayers` | LayerMask | — | none | — | Layers excluded from the overlap query (`_layerMask = ~ignoreLayers`). | Exclude the robot's own geometry and anything the roller should pass through. |
| `motor` | enum | — | — | — | Motor model: FullSize (7 N·m / 6000 rpm), Medium (4.5 / 5500), Small (1.5 / 11000), Tiny (0.1 / 15000), Shooter (7.5 / 11000), Unstable (25 / ∞). | Sets both the torque ceiling and the `rpm` clamp. Unstable is uncapped — for prototyping, not for a tuned mechanism. If you find it more stable, you have another issue go fix that instead. |
| `atRangeTollerance` | float | 0–0.2 | 0.02 | — | Serialized under "other stuff". | Used by groups and flywheel scripts |
