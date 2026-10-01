---
title: Adding cameras
slug: video-cameras
sidebar_position: 1
tags: [video, cameras, onvif, rtsp, tapo]
---

Cameras are added under *Cameras → Camera management → Add camera*. Any IP camera that sends **RTSP** (H.264, H.265
or MJPEG) or **HTTP MJPEG** works; ONVIF adds events, PTZ and automatic filling of the addresses.

## The fastest way: camera make

*Add camera → Camera make* fills in the stream, ONVIF and snapshot addresses from the IP address for:

- TP-Link Tapo
- Hikvision (HiLook)
- Dahua (Imou, Amcrest)
- Reolink
- Axis
- Uniview
- EZVIZ

Type the IP address, the camera's user name and password, and press *Test connection* — it connects once and shows
the codec and picture size, or the error.

## Find cameras in the network

Press **Find cameras in the network**: the server sends an ONVIF WS-Discovery probe (multicast, and
unicast to IP addresses you list) and shows the cameras that answer. For a found camera, **Fill in from ONVIF** reads
its profiles and fills the main and sub-stream addresses.

## The fields

| Field | What to enter |
|---|---|
| Main stream URL | `rtsp://…` (H.264, H.265 or MJPEG) or `http://…` (MJPEG). Used for recording and the full view. |
| Sub-stream URL | the camera's smaller stream. Tiles and walls use it; without it they play the main stream, which costs bandwidth and browser power with many tiles. |
| User name, password | may also be pasted in the URL (`rtsp://admin:pw@…`); they are stored separately, the password encrypted, and are never shown or written to the audit trail. |
| Transport | automatic (UDP, falling back to TCP), or force TCP (through firewalls and NAT) or UDP. |
| ONVIF address | for example `http://camera/onvif/device_service`, same user name and password as the stream. |
| Recording | continuous, on events, or off; **Keep recordings (days)**. See [Recording and storage](recording.md). |
| Camera group | limits who sees the camera. See [Privacy, sound and permissions](privacy.md). |

Typical stream addresses when you enter them by hand:

| Make | Main stream | Sub-stream |
|---|---|---|
| Hikvision | `rtsp://IP:554/Streaming/Channels/101` | `…/102` |
| Dahua | `rtsp://IP:554/cam/realmonitor?channel=1&subtype=0` | `subtype=1` |
| Axis | `rtsp://IP/axis-media/media.amp` | |
| Many ONVIF cameras | `rtsp://IP:554/stream1` | |

A camera that does not record connects only while someone watches it (and a short while after), so an unwatched
camera costs nothing.

Every change of a camera needs a reason and is written to the audit trail.

## TP-Link Tapo step by step

Tapo C100, C110, C200, C210, C220, C225, C310, C320WS, C500, C510W, C520WS, TC60, TC70 and similar:

1. In the **Tapo app**: camera → Settings → Advanced settings → **Camera account** — create a user name and password.
   This is the account for RTSP and ONVIF, not your Tapo app account. On newer firmware also turn on
   **Third-Party Compatibility** (Tapo app → Me → Tapo Lab); until then the camera answers ping but keeps ports 554
   and 2020 closed. Check from a PC in the same network that TCP 554 and 2020 are open.
2. Give the camera a **fixed IP address** in your router.
3. *Add camera → Camera make: TP-Link Tapo*, the IP address and the camera account. The addresses filled in:
    - main stream `rtsp://IP:554/stream1` (1080p, 2K or 4K)
    - sub-stream `rtsp://IP:554/stream2` (640 × 360)
    - ONVIF `http://IP:2020/onvif/device_service`
4. **Motion events** come over ONVIF — turn on motion detection in the Tapo app. Pan/tilt models (C200, C210, C500,
   C510W, C520WS) turn and use presets with PTZ. The sound is G.711; switch *Sound* on only where it is lawful.

!!! warning "Tapo limits"
    - Battery cameras and doorbells (C400, C420, C425, D230, D235) have no RTSP/ONVIF and cannot be added.
    - Tapo cameras serve few RTSP connections at a time: do not also watch them from another recorder.
    - Tapo has no HTTP snapshot address, so motion detection on the server is not available for it; use the
      camera's own motion events.

A Tapo camera at another site works through the relay, with PTZ, presets, motion events and sound — see
[Remote sites](remote-sites.md).

## Set the camera for quality at low load

The server records exactly what the camera sends, so quality and size are set **in the camera**:

| Stream | Recommended | Why |
|---|---|---|
| Main (recording, full view) | H.265 (or H.264), full resolution, 15–20 fps, VBR 2–4 Mbit/s, **key frame every 1–2 s** | H.265 needs about half the space of H.264; short key-frame intervals make files start and seeking land exactly |
| Sub (tiles, walls) | **H.264**, 640 × 360, 10–15 fps, 256–512 kbit/s | the browser decodes every tile; H.264 plays everywhere, small pictures keep a PC with 40 tiles light |

"Smart" codecs (H.265+, H.264+, WiseStream) with key frames every 10 seconds or more work, but make recording files
and seeking coarser. Turn them off if exact seeking matters more than space.

!!! tip "Keep cameras in their own network"
    The server connects to the camera addresses an administrator enters. Keep the cameras in a separate network
    reachable only from the server.
