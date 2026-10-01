---
title: Live view and playback
slug: video-watching
sidebar_position: 4
tags: [video, playback, export, live-view]
---

Live and recorded video play in the browser without plug-ins.

## Live view

*Cameras → Live view* shows all cameras you may see as tiles (sub-stream). A tile with an active event is outlined.
Click a tile for the camera page. The *Stream information* switch shows codec, picture size, frames per second and
kbit/s on each tile.

## The camera page

One player for live and recorded video, with a control bar:

- previous recording, 60 s and 10 s back and forward, pause and play, **LIVE**;
- speed 0.5× to 16×;
- digital zoom: buttons, mouse wheel or pinch at the pointer, drag to pan, double click for the whole picture;
- save picture, full screen;
- PTZ for cameras that have it (see [Events and motion](events-and-motion.md)).

Below the player: **the day's timeline** and **the detail of one hour**. Click to play from a time.

Keyboard: space pause, ← → 10 s, Shift + ← → 1 minute, Home previous recording, + − 0 zoom, L live, F full screen,
I stream information.

## Timeshift

Pausing the live picture and pressing play continues from the paused moment out of the recording and can catch up to
the present. LIVE returns to the live picture. Playback never stops recording.

## Export a clip

Drag in the hour detail to select up to **one hour** and download it as **MP4**. Exports are ordinary MP4 files for
VLC or any player. Every playback and every export is written to the audit trail with the camera and the time range.

- Exporting needs the permission `video.playback`.
- Sound is in the export only for people with `video.audio`.
- A camera with privacy masks can be exported only by people with `video.unmask` — see [Privacy](privacy.md).
- For the police, an insurer or a court, use a signed [evidence package](evidence.md) instead.

## Stream information

What actually arrives from the camera (or its relay): codec, picture size, frames per second and kbit/s, for the main
and the sub-stream, updated every 3 seconds — below the player, or as an overlay with the **ⓘ** button.

## Browsers

- H.264 plays in every browser; **H.265** only where the operating system supports it (Safari, Edge or Chrome with
  hardware decoding). For tiles use an H.264 sub-stream.
- MJPEG plays everywhere.
- On iPhone live video needs iOS 17.1 or later.

If a reverse proxy sits in front of the server, it must pass WebSocket upgrades (Caddy, which the Linux installer
sets up, does by default).
