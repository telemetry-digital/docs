---
title: Video settings
slug: video-settings
sidebar_position: 10
tags: [video, settings, incidents]
---

**Cameras → Settings** holds the server-wide video settings. Anyone with `video.manage` sees them; a system
administrator changes them. Every change is audited with the old and new values and a **reason**. The server checks
every value against its bounds; **Default values** fills in the defaults.

| Setting | Default | Bounds | Meaning |
|---|:---:|:---:|---|
| Disk space for recordings | 50 GB (from `config.toml`) | 1 GB – 1 PB | the oldest recordings of all cameras are deleted beyond it |
| Length of one recorded file | 60 s | 10–600 s | shorter loses less after a power cut, makes more files |
| Join events within | 5 s | 0–120 s | flapping motion becomes one event (0 = never join) |
| Find cameras: wait for answers | 3 s | 1–15 s | how long discovery waits |
| Audit PTZ moves once per | 5 min | 0–60 min | per user and camera; 0 = every move; presets always |
| Keep an unwatched camera connected | 30 s | 0–600 s | cameras that do not record reconnect instantly within it |
| Timeshift end | 20 s | 5–300 s | a following playback ends after this long without new video |
| Video walls: sub-stream above | 4 tiles | 0–64 | for wall screens set to *automatic* |
| Relay blocking | 10 attempts / 10 min → 15 min | 3–100 / 1–1440 min / 1–10080 min | failed relay attempts from one address |
| Camera offline or not recording | 5 min | 0–1440 min | incident for a recording camera that is offline or writes nothing (0 = none) |
| Display not showing | 5 min | 0–1440 min | incident for a paired display that stopped reporting |
| Free space of the video disk | 5 % | 0–50 % | incident when the disk of the recordings is nearly full |

## Monitoring incidents

A camera that stops recording, a display that stops showing and a video disk that fills up open an **incident** in the
incident log. Administrators are notified by e-mail and Web Push, and the incident resolves itself when the problem
ends. See [Alarms, notifications and incidents](../automation/alarms.md).

## Per camera, per wall, per viewer

- **Per camera** (*Camera management → Edit*): addresses, transport, ONVIF, recording mode, pre- and post-roll, the
  event kinds that record, retention, sound, privacy masks, motion detection, camera group, order.
- **Per video wall screen**: layout, cameras and stream (automatic, sub-stream or main stream).
- **Per viewer** (remembered in the browser): tile size, playback speed, PTZ speed.

## Network notes

- Keep cameras in a separate network reachable only from the server.
- Browsers receive video over a WebSocket at the same address as the web interface; a reverse proxy must pass
  WebSocket upgrades.
- For many cameras over UDP on Linux, raise `net.core.rmem_max` or use transport TCP (see
  [Recording and storage](recording.md)).
