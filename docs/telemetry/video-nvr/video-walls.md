---
title: Video walls, floor plans and widgets
slug: video-walls
sidebar_position: 5
tags: [video, video-wall, floor-plan, widgets]
---

## Video walls

A video wall is a saved layout of camera tiles over one or more monitors. *Cameras → Video walls* lists the walls
with a preview of each screen (click a preview to open that screen) and the number of cameras. Everyone with
`video.view` opens walls; creating, editing and deleting needs `video.manage`.

![The Video walls page listing two walls with a preview of their screens and tiles, the number of cameras and the buttons Open on all monitors, Edit and Delete](img/video-walls.webp)

### Wall fields

*Cameras → Video walls → New video wall*, or *Edit*:

| Field | Default | Limits | Meaning |
|---|:---:|:---:|---|
| Name | — | 1–80 characters | the wall's name |
| Screens | 1 | 1–8 | one per monitor; *Add screen*, *Remove screen* |
| Screen name | Monitor n | ≤ 40 characters | shown in the screen selector of the wall |
| Layout | 3 × 3 | see below | columns × rows of the screen |
| Stream | automatic | automatic, sub-stream, main stream | which stream the tiles play |
| Tiles | empty | one camera or — per tile | the camera of each tile, in reading order |
| Reason | — | ≤ 200 characters, required | written to the audit trail |

Layouts offered (columns × rows, tiles): 1 × 1 (1), 2 × 1 (2), 2 × 2 (4), 3 × 2 (6), 3 × 3 (9), 4 × 3 (12),
4 × 4 (16), 5 × 4 (20), 6 × 4 (24), 6 × 5 (30), 8 × 5 (40) and 8 × 6 (48).

**Fill with all cameras** puts all enabled cameras on the screens in order and leaves the remaining tiles empty.
Deleting a wall needs a reason. A user limited to camera groups can put only cameras of their groups on a wall.

### Which stream the tiles play

| Stream setting | Tiles play |
|---|---|
| automatic | the sub-stream when the screen has **more tiles than** *Video walls: sub-stream above* (default 4, 0–64), otherwise the main stream |
| sub-stream (smaller, saves bandwidth) | always the sub-stream |
| main stream (full quality) | always the main stream |

With the default 4, a 2 × 2 screen plays main streams and a 3 × 3 screen sub-streams. A camera without a sub-stream
plays its main stream. The threshold is set in [Video settings](settings.md).

### Open a wall

- **Open on all monitors** opens one window per screen. In Chrome and Edge the browser asks once for permission to
  place windows and puts each on its own monitor; the first click in each window switches it to full screen. In other
  browsers a message asks you to move each window to its monitor and press F11.
- A single screen opens at `/video/walls/<id>?screen=<n>`, where `n` counts from **0** for the first screen — for
  example one PC per monitor.

On the wall:

- a click (or double click) on a tile enlarges that camera to the whole monitor with the **main stream**; Esc or
  another click returns;
- tiles with an event going on are outlined with the event kinds, refreshed every 3 seconds;
- the toolbar — wall name, **screen selector**, **layout** (grid or one camera per row), **Full screen**, **Open on
  all monitors** (the other screens), **Close** — hides together with the pointer after 3 seconds without movement.

![A wall screen with 20 camera tiles in a 5 × 4 layout](img/wall-control-room.webp)

*One screen of a control-room wall: 5 × 4 cameras.*

![A wall screen with four camera tiles in a 2 × 2 layout](img/wall-gatehouse.webp)

*A small 2 × 2 wall for a gatehouse.*

Walls never play sound and always show privacy masks. Wall changes need a reason and are audited.

!!! tip "Walls that run day and night"
    For a wall without anyone signed in, pair the wall computer as a **display**: it shows wall screens and dashboards
    in turn, enlarges cameras with events and follows operators' commands. See [Displays](../dashboards/displays.md).

## Cameras on a floor plan

A floor plan or site map is a process screen with the plan as a background image and one pin per camera:

1. *Dashboards → New → Process picture*.
2. *Library → Import SVG* — for example a plan exported from a CAD drawing — and place it.
3. *Add widget → Process → Camera on a plan* for each camera.

| Setting | Default | Meaning |
|---|:---:|---|
| Title | — | optional title |
| Camera | — | the camera of the pin |
| Looks towards (degrees) | 90 | direction of the view cone, 0 = up, clockwise |
| Field of view (degrees) | 70 | width of the view cone, drawn between 10 and 180 |
| Length of the view cone | 80 % | 0–100 % |
| Show camera name | on | the name next to the pin |

![The process screen editor with the floor plan of a site, the symbol library on the left and the picture properties on the right](img/floor-plan-edit.webp)

The pin is **green** when the camera is online, **red** when offline, and **orange, pulsing** while an event is in
progress (the tooltip names the event kinds). A click opens the live picture in a window (privacy masks apply) with
a link to the camera page. Pins of cameras outside your camera groups are not drawn. On a display the window closes
by itself after a minute; public shared dashboards show the pins without state.

![A site floor plan with halls, a warehouse, offices and a gate, with camera pins and their view cones](img/floor-plan.webp)

*Cameras on a floor plan with their view cones; a click on a pin opens the live picture.*

## Camera widget

Any dashboard or process screen can show a live camera: *Add widget → Other → Camera* (default size 4 × 3).

| Setting | Default | Meaning |
|---|:---:|---|
| Title | — | optional title |
| Camera | — | the camera |
| Stream | sub | `sub` or `main`; use the sub-stream for small widgets |
| Show camera name | on | the name over the picture |
| Open the camera on click | on | a click opens the camera page |
| Show stream information | off | codec, size, fps and kbit/s over the picture |

The widget needs `video.view`. **Public shared dashboards never show cameras.** A user limited to camera groups can
put only cameras of their groups on a dashboard.
