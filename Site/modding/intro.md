---
sidebar_position: 1
---

# MoSim Modding

Welcome to the **MoSim Robot Modding Documentation**.
This documentation exists to help the community understand, create, and share **custom robot mods** for MoSim.

:::note
MoSim’s modding support is intentionally scoped and evolving. These docs are designed to be a **living, community-driven resource**, where contributors can document discoveries, best practices, limitations, and examples as robot modding matures.
:::

## What Can You Mod?

:::info
At this time, **MoSim supports robot mods only**
:::

| Supported | Not Supported |
|-----------------|-----------------|
| Add custom robots to MoSim   | Custom fields or game pieces   |
| Configure drivetrains, mechanisms, and motion  | Game rules or scoring logic   |
| Define robot physical properties and behavior within supported systems   | Physics engines or global simulation behavior   |
| Recreate real-world FRC robots or design original concepts  | Audio, effects, or animations outside robot assets   |

:::warning
Implementation of unsupported features with intent to distribute is a violation of the MoSim Modding License. If something is not documented here, it should be assumed unsupported unless explicitly stated otherwise.
:::

## Community-Driven Documentation

This documentation is not exhaustive and is not expected to be.

Experience levels may vary. Some sections are beginner-friendly, while others assume familiarity with CAD, simulation concepts, or configuration files.

:::tip
If you learn something new while modding a robot, **you are encouraged to contribute it** so others don’t have to rediscover it.
:::
### How to Contribute

- Clone the public MoSim-Docs Repo
- If you wish to test your changes first, follow [these docs](https://docusaurus.io/docs)
- Edit the page(s) you wish to contribute to using distinct commits
  - Be thorough but maintain legibility
  - Include images and/or links to other documentation (within the site or unity/blender/etc)
- Create a single pull request

### Stability & Expectations

Robot modding support may change over time.
Features may be added, adjusted, or deprecated as MoSim evolves.

These docs aim to:

- Reflect current behavior as accurately as possible
- Clearly label limitations or experimental features
- Avoid speculation unless explicitly marked as future plans

## Getting Help

If something doesn’t work, create a post in `# modding-and-builder-help` on the [MoSim Discord](https://discord.gg/RW2QXKvJKp)

Clear reports help improve both MoSim and these docs.

## Disclaimer
- **MoSim robot modding is provided as-is.**
- **Not all real-world robot behaviors can be perfectly simulated, and realism may vary depending on configuration choices and performance constraints.**
- **AI contributions to these docs will not be accepted**