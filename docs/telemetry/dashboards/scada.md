---
title: Process screens (SCADA)
slug: scada-screens
sidebar_position: 2
tags: [scada, hmi, process-pictures, editor]
---

A process screen shows the plant as an engineer drew it: vessels with their current level, pumps and motors coloured
by state, valves open or closed, pipes with moving flow while the medium moves, and instrument bubbles (TT-101,
PT-102) with live values and units. When something is abnormal the object changes colour and blinks until the value
returns to normal.

## Create a screen

**Dashboards → New → Process picture**. Start empty, from the example *Process picture — mixing plant (demo)*, or
choose *Process picture with widgets* to place dashboard widgets over the drawing.

## The editor

The editor works like a desktop SCADA editor:

- **Library** of industrial symbols in categories — vessels, pumps and drives, valves, heat transfer, process
  equipment, instruments, electrical. Drag a symbol onto the canvas.
- **Pipes** drawn as polylines with corners (click the points, finish with a double click or Enter); lines,
  rectangles, ellipses, polygons and texts.
- Select, multi-select with a rubber band, move, resize, rotate, snap to a grid, align and distribute, bring to front
  and send to back, copy, paste and duplicate, lock objects, nudge with the arrow keys, zoom and pan, undo and redo.
- Every save is a new version with a reason.

## Bring it to life

Bind objects to datastreams and add **dynamics**:

- **Level** — a tank filled to the value (for example 0–100 %).
- **State colours** — for example 1 = running, 0 = stopped, 2 = fault; running pumps turn their rotating parts.
- **Flow** — moving marks along a pipe while a condition holds (for example flow > 0.5).
- **Blink** — framed in the alarm colour and blinking while a condition holds.
- **Texts** with placeholders such as `{value} {unit}`. A datastream without a value shows "—"; no object pretends
  to have a state it does not know.

## Faceplates and commands

In view mode a click on an object (a pump, a valve) opens its **faceplate** with a trend and, for users with the
permission, commands such as *Start* — each with a reason, acknowledged by the device and written to the audit trail.
Users without the permission see no controls.

## Your own graphics

**Library → Import SVG** brings in your own drawings — logos, special machines, a floor plan from CAD. The server
sanitises the file: scripts, event handlers and foreign references are removed, and the import tells you what was
removed. Imported objects are static images.

## Sharing

A process screen can be shared through a public read-only link like a dashboard: values visible, no commands, no
navigation, no cameras.

!!! danger "A screen is not an interlock"
    Operator commands from a process screen go through the server, which is not in the control loop. Interlocks,
    emergency stops and other safety functions belong in the PLC and in hard wiring.
