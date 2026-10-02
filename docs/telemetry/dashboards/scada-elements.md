---
title: Drawing elements, dynamics and faceplates
slug: scada-elements
sidebar_position: 9
tags: [scada, hmi, symbols, dynamics, faceplates, commands, reference]
---

The drawing of a process picture consists of **elements**: shapes, lines, pipes, texts, industrial symbols and
imported graphics. Each element can be bound to a datastream (its **tag**), change its look with **dynamics** and
react to a click with **click actions**. This page is the reference for all of them; the editor itself is described
in [Process picture editor](scada.md).

## Element types

| Type | Drawn with | Main properties |
|---|---|---|
| Rectangle | Rectangle tool | fill, line, corner radius, level fill |
| Ellipse | Ellipse tool | fill, line |
| Line | Line tool | two points, line colour and width, flow |
| Polyline | Polyline tool | 2–200 points, line colour and width, flow |
| Polygon | Polygon tool | 3–200 points, fill, line, flow |
| Pipe | Pipe tool | 2–200 points, medium colour, outline, diameter, 3D highlight, flow |
| Text | Text tool | text with placeholders, font, alignment, background, border |
| Symbol | symbol library | one of 68 industrial symbols, state colours, level, rotation, label and value |
| Image | organization library | an imported SVG graphic (static) |

## Element properties

Select one element to see all its properties; with several selected, the Appearance settings apply to all of them.

### Object

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Name (tooltip, faceplate) | text | — | at most 64 characters; shown in the tooltip, the faceplate and the Layers panel |
| Locked | checkbox | off | cannot be selected on the canvas or moved |
| Hidden in operation | checkbox | off | not drawn in view mode; pale in the editor |

### Geometry

| Setting | Type | Allowed values |
|---|---|---|
| X, Y | number | the top-left corner in pixels; up to twice the larger canvas side in any direction |
| W, H | number | width and height in pixels, at least 0 |
| Rotation ° | number | −360 to 360, around the centre (not for lines) |

Coordinates are stored with two decimals.

### Symbol, graphic and text

| Setting | For | Allowed values |
|---|---|---|
| Symbol | symbol | any symbol of the library; the size stays |
| Label (instruments, buttons) | symbol | at most 500 characters; the tag letters of instrument bubbles, the caption of a push button, the letters of an analyzer |
| Library graphic | image | an imported graphic of the organization library |
| Text | text | at most 500 characters, several lines; placeholders below |

Text placeholders:

| Placeholder | Replaced by |
|---|---|
| `{value}` | the value of the bound tag, formatted with the element's decimals; `—` without a value |
| `{unit}` | the element's unit, or the datastream's unit |
| `{name}` | the element's name |
| `{time}` | the time of the reading |

A text element with an empty text shows `{value}`.

### Appearance

| Setting | For | Default | Allowed values |
|---|---|---|---|
| Fill | rectangle, ellipse, polygon, symbol | the tool's style; empty = none (symbols `#cbd5e1`) | hex colour or `none` |
| Medium colour | pipe | `#94a3b8` | hex colour |
| Background | text | none | hex colour or `none` |
| Line / Outline | all | the tool's style; empty = none for shapes and texts, `#334155` for lines and symbols, a darker medium colour for pipes | hex colour or `none` |
| Line width / Diameter px | all | 1 (lines 2, pipes 12, symbols 1.5) | 0–100 |
| Opacity | all | 1 | 0–1 |
| Line style | all except pipe and symbol | solid | solid, dashed, dotted, dash-dot |
| Corner radius | rectangle, text | 0 | 0–1000 |
| Liquid / level colour | symbol, rectangle | `#3b82f6` | hex colour |
| Text colour | text | theme | hex colour |
| Font size | text | 14 | 4–400 |
| Align | text | left | left, centre, right |
| Vertical | text | middle | middle, top, bottom |
| Bold, Italic | text | off | — |
| Metallic shading | symbol, rectangle, ellipse | on for new symbols, otherwise off | — |
| 3D highlight | pipe | on | — |
| Shadow | all | off | — |

Hex colours may have 3, 4, 6 or 8 digits. An empty colour field means the default.

### Binding

| Setting | Allowed values |
|---|---|
| Tag (datastream) | a datastream of the organization; a filter field narrows the list |
| Unit (empty = datastream) | at most 16 characters |
| Decimals | 0–6; default 1 |
| Device for operation | the device that receives commands from this object |
| Command key (register / datastream key) | at most 64 characters: letters, digits and `_ . : / -` |

The tag drives the value of symbols and texts and is the default tag of every dynamic. The tooltip of a bound element
shows its name (or *asset / key*) and the value with unit. A true or false value is shown as ON or OFF. A reading with
a quality other than *ok* gets ` ?` after the value and its quality in the tooltip.

## Symbol library

The library has 68 symbols in eight categories. Search filters by name. A symbol's body takes the fill colour, the
state colour or a colour dynamic; behaviours driven by dynamics are listed in the last column.

| Category | Symbols | Driven by dynamics |
|---|---|---|
| Vessels | vertical tank, horizontal tank, spherical tank, silo, hopper, open basin, reactor with agitator, column, stack / chimney | Level fills all except the stack; Rotate when turns the reactor's agitator |
| Pumps and drives | centrifugal pump, positive displacement pump, vacuum pump, motor, fan, blower, compressor, agitator, belt conveyor, screw conveyor, static mixer | Rotate when turns the fan, blower and agitator and moves the belt of the conveyor |
| Valves | gate, ball, butterfly, control, solenoid, three-way, check, safety and hand valve, damper | State colours the body; the damper blade follows Level (1 = open) or the state *open*, *running* (open) and *in transit* (half) |
| Heat transfer | heat exchanger, shell and tube exchanger, plate exchanger, electric heater, air cooler, boiler, cooling tower, furnace | State *running* turns the heater's element red and lights the flame of the boiler and furnace; Level fills the boiler; Rotate when turns the air cooler's fan |
| Process equipment | filter, cyclone, separator, flow meter, orifice plate, weighing scale, spray nozzle, rotary dryer | Level fills the cyclone and separator; Rotate when turns the dryer |
| Instruments | instrument (field, panel, DCS, PLC), dial gauge, indicator lamp, push button, selector switch, bar graph, analyzer, alarm horn | bubbles show the label and the value, red in *fault*, amber in *warning*; Level moves the dial needle and fills the bar graph; the selector turns right in *running*, *open* or *manual*; the horn sounds waves in *fault*, *warning* or *running* |
| Electrical | circuit breaker, transformer, generator, battery, solar panel, wind turbine, load / consumer | the breaker closes in *closed* or *running*; Level fills the battery (red below 20 %, amber below 40 %); Rotate when turns the turbine |
| Other | flow arrow, pipe junction, flange, building | — |

Animations stop when the operating system asks for reduced motion; blinking then slows down.

## State colours

The **State** dynamic maps a value to a state; the picture setting *State colours* decides the colour.

| State | Classic (WinCC-like) | ISA-101 high performance |
|---|:---:|:---:|
| running | `#22c55e` | `#475569` |
| stopped | `#9ca3af` | `#e2e8f0` |
| fault | `#ef4444` | `#dc2626` |
| manual | `#3b82f6` | `#60a5fa` |
| open | `#22c55e` | `#475569` |
| closed | `#9ca3af` | `#e2e8f0` |
| in transit | `#facc15` | `#94a3b8` |
| off | `#d1d5db` | `#f1f5f9` |
| warning | `#f59e0b` | `#f59e0b` |

The state colour fills symbols and shapes, the background of texts and the medium of pipes that have no *Medium
colour* set (the pipe tool sets one). A *Fill colour* dynamic wins over the state colour.

## Dynamics

**Dynamics** change an element with the values of datastreams. An element has up to **8** dynamics; each uses the
element's tag, or another tag chosen in the dynamic. **+ Add dynamic** starts with a sensible default: *Flow when
> 0* for pipes, *Text* for texts, *State* (1 = running, 0 = stopped) for symbols, *Fill colour* (≥ 80 → red) for the
rest.

| Dynamic | Kind | Settings | Effect |
|---|---|---|---|
| Fill colour | mapping | rows: operator, value → colour | fills the element (pipes: the medium; texts: the background) |
| Line colour | mapping | rows: operator, value → colour | the line or outline |
| Text colour | mapping | rows: operator, value → colour | the colour of a text |
| State | mapping | rows: operator, value → state | one of the nine states; colour from *State colours*, plus symbol behaviours |
| Text | mapping | rows: operator, value → text (at most 64 characters) | replaces `{value}`; without a matching row the formatted value |
| Visible when | condition | operator, value | the element is drawn only while the condition holds (always drawn in the editor) |
| Blink when | condition | operator, value, frame colour | blinks while the condition holds; with a frame colour a blinking frame is drawn around the element instead |
| Flow when | condition | operator, value, speed 0.05–20 | moving dashes along lines, polylines, polygons and pipes |
| Rotate when | condition | operator, value, speed 0.05–20 | turns the moving parts of symbols; other elements turn as a whole |
| Level (fill 0–1) | linear | value from, value to → result from, result to | the fill height of vessels, rectangles and level symbols |
| Opacity (0–1) | linear | value from, value to → result from, result to | the opacity |
| Angle (°) | linear | value from, value to → result from, result to | added to the rotation |
| Move X (px), Move Y (px) | linear | value from, value to → result from, result to | moves the element |

- **Operators**: `>`, `>=`, `<`, `<=`, `==`, `!=`.
- **Mappings** have up to 16 rows, checked from the top; the first matching row wins. *+ row* adds one.
- **Linear** dynamics map the value range to the result range and keep the result inside it; *value from* and *value
  to* must differ. Defaults: Level 0–100 → 0–1, Opacity 0–1 → 0.2–1, Angle 0–100 → 0–90, Move 0–100 → 0–100.
- **Speed** 1 means one animation cycle per second; 2 is twice as fast.
- **Without a value** a dynamic does nothing: no state is set, nothing is hidden, nothing blinks, and texts show `—`.
  No object pretends to know a state it does not know.

## Click actions

In view mode a click on an element runs its click action. An element has up to **4** actions, but a click runs **only
the first one**. Without an action, a click on a symbol or text with a tag opens its faceplate; other elements
without an action do nothing.

| Action | Settings | Effect |
|---|---|---|
| Open faceplate | — | the faceplate of the element's tag, with operation when a device and a command key are bound |
| Open trend | — | the faceplate without the operation part |
| Go to screen | Screen: another dashboard or picture of the organization | opens it (not on public links) |
| Send command | Key, Value, Method (default `write`), Ask for confirmation (default on) | opens a command dialog |

The **Send command** action needs the *Device for operation* of the Binding section. Its value is `true`, `false`, a
number, or a text of at most 64 characters; the method is a lower-case name of at most 32 characters.

## Faceplates

A faceplate is a window with the details of one tag:

- the element's name and *asset / key* of the datastream;
- the latest value with unit, a quality badge and the time (*no value yet* without a reading);
- a trend with the range buttons **1h**, **6h** (default), **24h** and **7d**;
- the **Operation** part when the element has a device and a command key and the action is not *Open trend*:
  **Start / On** sends `true`, **Stop / Off** sends `false`, a number field with **Set** sends the number.

Without a bound tag the faceplate shows *No datastream is bound to this object*. A click outside the window or ✕ closes
it.

## Writing values

Commands from faceplates and from *Send command* actions are written to the bound device as the command method
(`write` for faceplates) with the arguments `key` and `value`:

| Step | Faceplate | Send command action |
|---|---|---|
| Permission | `device.command`; without it *no permission to send commands* | the same |
| Reason | required in the reason field (at most 200 characters) | required in the dialog (at most 200 characters) |
| Confirmation | always: the browser asks *key → value?* | when *Ask for confirmation* is on |
| Public link | *read-only*, no operation | *read-only* |

The result is shown as a badge: *acked*, *pending* (waiting for approval by another user under the four-eyes policy)
or the command's status. Every command is written to the audit trail. See
[Commands](../devices/commands-and-firmware.md).

!!! danger "A screen is not an interlock"
    Operator commands from a process picture go through the server, which is not in the control loop. Interlocks,
    emergency stops and other safety functions belong in the PLC and in hard wiring.
