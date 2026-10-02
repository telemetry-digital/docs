---
title: Displays (kiosks)
slug: displays
sidebar_position: 3
tags: [displays, kiosk, video-wall, production]
---

A display is a screen at a wall — a PC, a mini-PC or a smart TV with a browser — that shows dashboards, process
screens and video wall screens **in turn, without anyone signed in**. It shows only what it is given, changes nothing
and can be disconnected at any time.

## Pair a display

1. On the computer at the wall open `https://<server>/display` in Chrome or Edge. It shows a code such as
   `K7QM-4TZP`.
2. In the application open **Displays → Pair display**, enter the code, name the display (for example *Control
   room – left monitor*) and choose what it shows.
3. The display starts showing its content within a few seconds.

![The Pair display dialog with the code shown on the display, name, location, the items it shows in turn, enlarging cameras with an event, the language, the incident option and a reason](img/pair-display.webp)

The code is valid for 15 minutes and pairs once. Knowing the code is not enough to become the display: only the
browser that showed the code collects the display's credential. Pairing, changes, commands and disconnecting are
written to the audit trail.

## What a display shows

- **Items in turn**: dashboards, process screens and screens of video walls, each for 10–3600 seconds. A single item
  stays on.
- **Cameras with an event**: *cameras of its video walls* or *all cameras* — a camera reporting motion, tamper, line
  crossing and so on fills the display for the chosen time (up to four at once).
- **Language** of the display.
- **Incident when the display stops showing** (on by default).

A display reads only its own content and plays only the cameras on it, a camera an operator sent, or cameras with an
active event. It never plays sound. Its credential opens no other part of the system.

## Production screens

Any dashboard or process screen can be on a display — for example one screen per production line with the pieces
made in the shift (*Aggregated value*, sum), the plan fulfilment (*Progress bar* with the plan as maximum), pieces per
hour (*Bars over time*), machine states (*Status*, *State timeline*), speeds and temperatures, and the line's process
screen.

Give each display a **location** (hall, line): the list can be filtered by it, and a message or command can go to all
displays of a location at once.

![A production dashboard: the shift clock, pieces made on two lines in 8 hours, scrap, plan fulfilment bars, line states, pieces per hour as bars and a machine state timeline](img/production-lines.webp)

*A production screen for a display.*

## Operator commands

On **Displays** (permission `display.control`; operators have it) or from a camera's page (*show on display*):

| Command | Effect |
|---|---|
| Send camera | the camera fills the display for the chosen time (only cameras the operator may see) |
| Identify | the display shows its name in large letters for a few seconds |
| Back to content | closes cameras sent or enlarged |
| Reload | the display reloads its page |
| Message | *information* (band at the bottom), *warning* (band at the top) or *alarm* (the whole screen, flashing), for 1–60 minutes; to one display, the selected ones, all of a location, or all |

Pairing, changing and disconnecting displays needs `display.manage` (engineers and administrators).

![The Displays page: a paired display with what it shows and its state, the buttons Send camera, Identify, Back to content, Reload and Edit, the location filter, Message to displays and Pair display, and the steps for setting up a display computer](img/displays.webp)

## Setting up the computer

- Start the browser in kiosk mode when the computer starts, for example
  `chrome --kiosk --autoplay-policy=no-user-gesture-required https://<server>/display` (Windows: a shortcut in the
  Startup folder; Linux or Raspberry Pi: an autostart entry).
- The display stays paired over restarts and reconnects by itself after an outage (showing a red notice meanwhile).
- The page keeps the screen awake and hides the cursor; the first click switches to full screen.
- Each display decodes its own video: for 20 camera tiles on the sub-stream a mini-PC with Intel graphics is enough.
- Several monitors on one computer: pair one display per monitor, or use a video wall's *Open on all monitors* with a
  signed-in user.

!!! tip "Raspberry Pi kiosk"
    For a Raspberry Pi with a touch display, the public repository
    [kiosk_setup_raspberry](https://github.com/telemetry-digital/kiosk_setup_raspberry) sets up a fullscreen Chromium
    kiosk on Raspberry Pi OS Lite.
