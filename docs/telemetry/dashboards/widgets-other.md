---
title: Other widgets and process symbols
slug: dashboard-widgets-other
sidebar_position: 7
tags: [dashboards, widgets, map, camera, process-symbols, reference]
---

This page describes the map, texts, clock, image, QR code, navigation card and camera widgets, and the process-symbol
widgets (tank, pump, valve, pipe, instrument, camera on a plan). The tables list the settings on the **Data** tab; the
shared Appearance, Series and Actions settings are described in the [widget catalogue](widgets.md). None of these
widgets sends commands or offers an export.

## Map

Devices with a position on a map, coloured by their connection state.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Map | — |
| Devices (empty = all) | devices | all | up to 24 devices |
| Initial zoom when empty | number | 7 | the zoom level when no device has a position |

- The map tiles, the maximum zoom, the attribution and the initial centre come from *Settings → Branding and theme*.
  Without a tile address the widget explains where to set it.
- A device appears when it has a position — fixed on the device or reported by it. Revoked devices are not shown.
- Marker colours: green online, amber seen within the last hour, red offline, grey never seen.
- A click on a marker shows the name, the state and the last contact, whether the position is fixed or reported (with
  the time), and a link *open device*.
- The map zooms to show all markers (at most zoom 15). The markers update when a device connects or disconnects.

## Text

A note or a heading; one paragraph per line.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Text | — |
| Text | multi-line text | — | at most 4000 characters; plain text |
| Style | list | normal | normal, note (grey box), warning (amber box), heading (each line a heading) |

Empty lines are skipped. An empty text shows *empty*.

## Markdown card

Formatted text with live values of the bound datastreams.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Markdown card | — |
| Text (Markdown subset) | multi-line text | — | at most 4000 characters |
| Decimals | number | 2 | for the values |

| You write | You get |
|---|---|
| `# Heading`, `## Heading`, `### Heading` | headings of three levels |
| `**bold**`, `*italic*`, `` `code` `` | bold, italic, code |
| `- item` or `* item` | a bulleted list |
| `[Line 2](screen:line-2)` | a link to the dashboard or picture with the slug `line-2` |
| `[Manual](https://example.com/manual)` | a link that opens in a new tab (only `https://` addresses) |
| `{value:1}` | the latest value of the first bound datastream with its unit; `{value:2}` the second, and so on |

A placeholder without a value shows `—`. Any other markup is shown as text. On a public link the links to screens do
nothing.

## Clock

The current time and date, optionally in another time zone.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Clock | — |
| Format | list | 24h | 24h, 12h |
| Show date | checkbox | on | the weekday, day, month and year |
| Time zone (IANA, empty = browser) | text | — | for example `Europe/Bratislava` or `America/New_York` |

The time has seconds and follows the language of the user. With a time zone its name is shown under the date; a
name the browser does not know falls back to the browser's time.

## Image

A graphic from your organization's library — for example a logo or a drawing imported in the
[process picture editor](scada.md).

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Image | — |
| Library graphic (slug) | text | — | the slug of an imported SVG graphic |
| Fit | list | contain | contain (the whole graphic), cover (fills the widget, may crop) |

Without a graphic the widget shows *choose a graphic from the library*. A public link serves only the graphics the
shared screen uses.

## QR code

A QR code of a text or an address — for example the public link of this dashboard on a screen in a reception.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | QR code | — |
| Text or address (empty = this page) | text | — | any text |

The text is encoded as UTF-8 with error correction level M and printed under the code. Empty means the address of the
page the widget is on.

## Navigation card

A card that opens another dashboard or process picture.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Navigation card | — |
| Icon | icon | none | an icon of the built-in catalogue |
| Screen (slug) | text | — | the slug of the target screen |
| Description | text | — | a second line under the title |

On a public link the card does not navigate.

## Entity selector

A drop-down or a searchable list of the entities of an alias; the chosen entity becomes the current entity of the
dashboard and the widgets bound to a *Current entity* alias switch to it. Described in
[Entity aliases, key filters and drill-down](aliases-filters-states.md#entity-selector).

## Camera

The live picture of a camera; a click opens the camera page with its recordings.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Camera | — |
| Camera | camera | — | the cameras you may see |
| Stream | list | sub | sub (sub-stream, smaller), main (full quality) |
| Show camera name | checkbox | on | — |
| Open the camera on click | checkbox | on | — |
| Show stream information (codec, size, fps, kbit/s) | checkbox | off | — |

- Viewers need `video.view`; without it the widget shows *No permission to watch cameras*.
- A dot next to the name shows whether the stream is online.
- Saving a dashboard with a camera outside your camera groups is refused.
- **Public links never show cameras.** A paired [display](displays.md) plays the cameras of its dashboards.
- The picture keeps playing when the dashboard refreshes. More in
  [Video walls, floor plans and widgets](../video-nvr/video-walls.md).

## Tank

A vessel filled to the level of the first bound datastream. Process symbols follow ISA-101: the process is grey and
colour appears only for abnormal states.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Tank | the label above the symbol; empty = the datastream key |
| Level at empty | number | 0 | any |
| Level at full | number | 100 | any |
| Decimals | number | 2 | any |
| Warning limit (high) | number | — | any |
| Action limit (high) | number | — | any |
| Warning limit (low) | number | — | any |
| Action limit (low) | number | — | any |
| Show as a framed card | checkbox | off | — |

The liquid is grey; in the warning zone amber, in the action zone red. The value and unit are written under the
vessel. A long title shrinks to fit. **Without data**: an empty vessel and `—`.

## Pump / motor

The running state of the first bound datastream.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Pump / motor | — |
| Value meaning running | number | 1 | any |
| Value meaning fault (optional) | number | — | any |
| Label when running | text | RUNNING | — |
| Label when stopped | text | STOPPED | — |
| Show as a framed card | checkbox | off | — |

Filled dark when running, outlined when stopped, red with *FAULT* when the value equals the fault value. The default
labels follow the user's language. **Without data**: outlined and `—`.

## Valve

The open or closed state of the first bound datastream.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Valve | — |
| Value meaning open | number | 1 | any |
| Label when open | text | OPEN | — |
| Label when closed | text | CLOSED | — |
| Value meaning fault (optional) | number | — | any |
| Orientation | list | horizontal | horizontal, vertical |
| Show as a framed card | checkbox | off | — |

Filled when open, outlined when closed, red with *FAULT* on the fault value. **Without data**: outlined and `—`.

## Pipe

A straight pipe segment; with a bound datastream it shows moving flow while the value is above a threshold.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Orientation | list | horizontal | horizontal, vertical |
| Thickness (% of the widget) | number | 40 | 5–100 |
| Flow when value is above | number | 0 | any |

Without a datastream, or with a value at or below the threshold, the pipe is static. For pipes with corners use the
pipe element of a [process picture](scada-elements.md).

## Instrument

An ISA-style instrument bubble with a tag and the latest value of the first bound datastream.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Tag | text | — | for example `TT-101`; empty = the datastream key |
| Decimals | number | 2 | any |
| Warning limit (high) | number | — | any |
| Action limit (high) | number | — | any |
| Warning limit (low) | number | — | any |
| Action limit (low) | number | — | any |
| Show as a framed card | checkbox | off | — |

In a zone the circle gets a thicker amber or red outline and the value takes its colour. **Without data**: `—`.

## Camera on a plan

A camera pin on a floor plan or a site map, with its view cone, its state and its events; a click shows the live
picture. Put the plan underneath as an imported graphic.

| Setting | Type | Default | Allowed values |
|---|---|:---:|---|
| Title | text | Camera on a plan | the label under the pin; empty = the camera name |
| Camera | camera | — | the cameras you may see |
| Looks towards (degrees, 0 = up, clockwise) | number | 90 | any; taken modulo 360 |
| Field of view (degrees) | number | 70 | 10–180 |
| Length of the view cone (0–100 %) | number | 80 | 0–100; 0 hides the cone |
| Show camera name | checkbox | on | — |

| State | Look |
|---|---|
| online | dark pin with a green dot, grey cone |
| offline | grey pin with a red outline and a red dot |
| event in progress | amber pin and a pulsing amber cone; the tooltip names the events |

- The state is refreshed at most every 10 seconds per camera.
- A click opens the live picture (main stream) in a window with *Open camera*; Esc, ✕ or a click outside closes it.
  On a display the window closes by itself after one minute.
- A camera outside your camera groups is not drawn.
- On a public link the pin is drawn without state and cannot be clicked.
