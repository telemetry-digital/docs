---
title: Home page and scenes
slug: home-scenes
sidebar_position: 3
tags: [smart-home, scenes, automation]
---

Devices found by [automatic device discovery](../devices/automatic-discovery.md) are controlled from the **Home**
page, grouped by rooms.

## The Home page

Cards show values with units and a 24-hour trend, switches, brightness, colour and colour temperature of lights,
blinds with open, stop, close and position, locks, thermostats with target temperature and mode, numbers, selects,
texts and buttons. Changes appear live. A card's chart button opens the history (24 hours, 7 or 30 days).

![The Home page: scene buttons, room filters, and cards for a door, a light with switch, brightness and colour temperature, a plug, humidity and temperature with a trend](img/home.webp)

*The Home page with two scenes and the devices grouped by rooms.*

Every control action is a **device command**: the permission `device.command`, the four-eyes policy when the
organization requires it, the audit trail with a reason (for example *Home: turn on Hall light*), and a status —
*sent* until the device reports the requested state, then *acknowledged*; *failed* when it does not within 15
seconds.

## Scenes

A scene sets several devices at once — *Evening*: hall light at 30 % warm white, plug on, boiler off.

1. On **Home**, press **+ Scene**.
2. Set the devices the way you want, select them and save. The scene remembers their states — on or off,
   brightness, colour, position, target temperature and mode, value.
3. The scene buttons above the rooms activate it with one click; *⋯* renames it, captures the states again or
   deletes it.

Activating a scene sends one ordinary command per device, with your permission, four eyes where required, the reason
*Scene: Evening* in the audit trail and the device's confirmation.

## Smart-home flows

[Flows](flows.md) have nodes for discovered devices:

- **Entity** trigger — a state change, optionally only from or to given states.
- **Entity action** — turn on or off, toggle, open, close, stop, position, lock, press, set a value, temperature,
  mode, fan speed.
- **Command result** trigger — a command ended as acknowledged, failed, expired or rejected.
- **Scene** — activates a scene.
- **MQTT in** and **MQTT out** — any message on a topic of an MQTT account or bridge, for devices without discovery.

Example: *door opened → hall light on at 60 %* is an Entity trigger (to `on`) wired to an Entity action (`turn_on`,
value `60`).
