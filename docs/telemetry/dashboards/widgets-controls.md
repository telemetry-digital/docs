---
title: Control widgets
slug: dashboard-widgets-controls
sidebar_position: 6
tags: [dashboards, widgets, commands, setpoints, reference]
---

Control widgets **send commands** to a device or to a writable register or node of a connector (Modbus, OPC UA):
a setpoint, on and off, a pulse. Every command needs the permission `device.command` and a reason, is acknowledged by
the device and written to the audit trail. See [Commands](../devices/commands-and-firmware.md).

## How a control widget sends a command

1. Bind the **device** on the Data tab (the first selected device is used) and set the **Key** — the datastream key or
   the register the value is written to. Optionally bind a **datastream** that shows the current value or state.
2. The viewer chooses the value, writes a **reason** into the field of the widget (required, at most 200 characters)
   and presses *Send* (or the switch button).
3. With *Ask for confirmation* on, the browser asks *Title → value?* before sending.
4. The command goes to the device as the method (default `write`) with the arguments `key` and `value`. The widget
   shows its status, which follows the device's answer live:

| Status | Meaning |
|---|---|
| sending… | the request is on its way |
| queued, sent | waiting for the device's acknowledgement |
| pending | the four-eyes policy is on: waiting for approval by another user |
| acked | the device confirmed it; a read-back value is shown when the device returns one |
| failed, expired, rejected | not executed, with the error when there is one |

The reason field is cleared after a successful send.

What the widget shows instead of the controls:

| Situation | Text |
|---|---|
| no device bound | *select a device* |
| no key set | *set the key in the widget properties* |
| the viewer lacks `device.command` | *no permission to send commands* |
| public link | *read-only* |

Knob, slider and round switch stay visible but disabled in these situations; the others show only the current value.

!!! danger "A widget is not an interlock"
    Commands from dashboards go through the server, which is not in the control loop. Interlocks, emergency stops and
    limits that protect people or machines belong in the PLC or the device itself — the device must also check every
    value it receives.

## Setpoint

Sends a number.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Setpoint | — |
| Key (datastream key / register) | text | — | required for sending |
| Command method | text | write | — |
| Minimum | number | — | any |
| Maximum | number | — | any |
| Step | number | 0.1 | any |
| Decimals | number | 2 | for the current value |
| Unit | text | — | shown next to the field |
| Ask for confirmation | checkbox | on | — |

- With a bound datastream the widget shows *current value unit · time* and fills the field with the current value.
- Minimum, maximum and step set the range and the arrows of the number field. A value typed outside the range is not
  refused by the widget.
- Text that is not a number is refused with *enter a number*.

## Switch

Sends `true` or `false`.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Switch | — |
| Key (datastream key / register) | text | — | required for sending |
| Command method | text | write | — |
| Label for on | text | On | — |
| Label for off | text | Off | — |
| Ask for confirmation | checkbox | on | — |

With a bound datastream a badge shows the current state — on when the value is 1 or true — and the time of the
reading. The two buttons send `true` and `false`.

## Button

Sends a fixed value, for example a reset pulse. It binds only a device.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Button | — |
| Key (datastream key / register) | text | — | required for sending |
| Command method | text | write | — |
| Value to send | text | 1 | `true` and `false` are sent as true and false, a number as a number, anything else as text |
| Button label | text | Send | — |
| Ask for confirmation | checkbox | on | — |

## Knob

A rotary knob over 270 degrees that sends a number.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Knob | — |
| Key (datastream key / register) | text | — | required for sending |
| Command method | text | write | — |
| Minimum | number | 0 | any |
| Maximum | number | 100 | any |
| Step | number | 1 | above 0; otherwise 1 |
| Unit (empty = datastream) | text | — | shown next to the value; when empty no unit is shown |
| Ask for confirmation | checkbox | on | — |

- Drag the knob, or turn the mouse wheel over it by one step. The value always stays between the minimum and the
  maximum and on a multiple of the step from the minimum.
- The knob starts at the current value of the bound datastream, or at the minimum. *current …* shows the current
  value; the number of decimals follows the step.
- **Send** sends the chosen value.

## Slider

A slider that sends a number.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Slider | — |
| Key (datastream key / register) | text | — | required for sending |
| Command method | text | write | — |
| Minimum | number | 0 | any |
| Maximum | number | 100 | any |
| Step | number | 1 | above 0; otherwise 1 |
| Unit (empty = datastream) | text | — | shown next to the value; when empty no unit is shown |
| Ask for confirmation | checkbox | on | — |

The slider shows the minimum, the maximum and the chosen value, and under it the current value and its time. It
starts at the current value or the minimum; values snap to the step.

## Round switch

A round power button that toggles a state.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Round switch | — |
| Key (datastream key / register) | text | — | required for sending |
| Command method | text | write | — |
| On label | text | ON | — |
| Off label | text | OFF | — |
| Ask for confirmation | checkbox | on | — |

The button is lit when the bound datastream is 1 or true. A press sends the opposite state: `false` when it is on,
`true` when it is off or has no value.

## Faceplate from a widget

With *On click = faceplate* (Actions tab), a click outside the controls opens the faceplate of the first bound
datastream; for a control widget with a device and a key it also offers *Start / On*, *Stop / Off* and *Set* with a
value — always with the method `write`, a reason and a confirmation. See
[faceplates](scada-elements.md).
