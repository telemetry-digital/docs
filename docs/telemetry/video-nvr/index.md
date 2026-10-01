---
title: Video NVR
slug: video-nvr
sidebar_position: 2
tags: [video, cameras, nvr]
---

**A complete network video recorder on your own server** — for a warehouse, a yard, a shop or a whole site, with the
recordings staying with you. No cloud service and nothing to install in the browser. More cameras require a
[licence](../licence/index.md).

The server receives each camera's stream, stores it and forwards it to browsers **as it is**: it never decodes or
re-encodes video. That is why one server records many cameras on little processor time, and why the picture you
play back is exactly the camera's own.

## What you get

- **Cameras**: RTSP and ONVIF IP cameras (H.264, H.265, MJPEG), discovery in the local network, presets for common
  makes including TP-Link Tapo; tested with 128 cameras on one server.
- **Recording**: continuous or on events (with pre- and post-recording), retention per camera and a disk limit.
- **Events**: motion, tamper, line crossing, intrusion and digital inputs from the camera over ONVIF, or motion
  detected on the server for cameras without it.
- **Viewing**: live view and timeline playback in the browser, speeds up to 16×, digital zoom, MP4 export, PTZ with
  presets, sound (AAC, Opus, G.711).
- **Video walls and displays**: walls over several monitors, kiosk displays paired with a code, a camera enlarged
  automatically on an event, cameras on a floor plan.
- **Phone app**: an installable camera app (PWA) for Android and iPhone that shows only the cameras.
- **Privacy and evidence**: privacy masks, access per camera group, an audit of who watched what, recordings locked
  as evidence and exported as a signed package (SHA-256, Ed25519).
- **Remote sites**: an encrypted relay (Linux, Raspberry Pi, Windows) sends the video of cameras at another site —
  no open ports and no VPN there.

## Pages in this section

1. [Adding cameras](cameras.md) — stream addresses, camera makes, TP-Link Tapo step by step, discovery.
2. [Recording and storage](recording.md) — continuous and event recording, retention, disks, sizing.
3. [Events and motion](events-and-motion.md) — ONVIF events, motion detection on the server, marks, PTZ, flows.
4. [Live view and playback](watching.md) — the player, timeline, timeshift, export, stream information.
5. [Video walls, floor plans and widgets](video-walls.md) — walls over several monitors, cameras on a plan.
6. [Remote sites (relay)](remote-sites.md) — cameras behind NAT at another site, with PTZ, events and sound.
7. [Phone app](phone-app.md) — install a camera-only app on phones and tablets.
8. [Privacy, sound and permissions](privacy.md) — masks, sound off by default, camera groups, audit.
9. [Evidence](evidence.md) — hold recordings, signed evidence packages and how to verify them.
10. [Video settings](settings.md) — server-wide settings, monitoring incidents, network notes.

## Quick start

1. *Cameras → Camera management → Add camera*. Choose the **camera make**, type the IP address, user name and
   password — the stream addresses are filled in. *Test connection*.
2. Set **Recording** to continuous (or on events) and **Keep recordings (days)**.
3. Open *Cameras → Live view* — every camera as a tile. Click one for the player with the timeline.
4. For phones: *Settings → Apps → New app → Cameras*, then install it on the phone.

!!! warning "Recording people is regulated"
    Recordings of people are personal data. You are the data controller: set the retention to what the purpose
    needs, mark the recorded area with signs, cover what must not be watched with privacy masks, and give access only
    to people who need it. Sound is **off by default** because recording conversations is regulated more strictly
    than pictures — switch it on only where it is lawful. See [Privacy, sound and permissions](privacy.md).
