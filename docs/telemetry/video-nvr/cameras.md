---
title: Adding cameras
slug: video-cameras
sidebar_position: 1
tags: [video, cameras, onvif, rtsp, tapo]
---

Cameras are added under *Cameras → Camera management → Add camera* and changed with *Edit* in the same list. You
need the permission `video.manage`. Any IP camera that sends **RTSP** (H.264, H.265 or MJPEG) or **HTTP MJPEG**
works; ONVIF adds events, PTZ and automatic filling of the stream addresses.

![The Add camera dialog with the Connection field, which chooses whether the server connects to the camera or the camera sends its video to the server](img/add-camera.webp)

## The fastest way: camera make

**Camera make** and **Camera IP address** fill in the main stream, sub-stream, ONVIF and snapshot addresses (and the
name, when it is empty). They are helpers of the form and are not stored.

| Make | Main stream | Sub-stream | ONVIF | Snapshot |
|---|---|---|---|:---:|
| TP-Link Tapo | `rtsp://IP:554/stream1` | `rtsp://IP:554/stream2` | `http://IP:2020/onvif/device_service` | — |
| Hikvision (HiLook) | `rtsp://IP:554/Streaming/Channels/101` | `…/Channels/102` | `http://IP/onvif/device_service` | `http://IP/ISAPI/Streaming/channels/101/picture` |
| Dahua (Imou, Amcrest) | `rtsp://IP:554/cam/realmonitor?channel=1&subtype=0` | `…&subtype=1` | `http://IP/onvif/device_service` | `http://IP/cgi-bin/snapshot.cgi` |
| Reolink | `rtsp://IP:554/h264Preview_01_main` | `rtsp://IP:554/h264Preview_01_sub` | `http://IP:8000/onvif/device_service` | — |
| Axis | `rtsp://IP/axis-media/media.amp` | `…/media.amp?resolution=640x360` | `http://IP/onvif/device_service` | `http://IP/axis-cgi/jpg/image.cgi` |
| Uniview | `rtsp://IP:554/media/video1` | `rtsp://IP:554/media/video2` | `http://IP/onvif/device_service` | — |
| EZVIZ | `rtsp://IP:554/h264/ch1/main/av_stream` | `rtsp://IP:554/h264/ch1/sub/av_stream` | — | — |

For some makes the dialog shows a tip:

- **Hikvision** — enable ONVIF in the camera (*Network → Advanced → Integration protocol*) with an ONVIF user; for
  sound choose AAC or G.711 in *Video/Audio*.
- **Reolink** — enable RTSP and ONVIF in the camera (*Network → Advanced → Server settings*); 4K models send H.265 on
  the main stream, tiles use the H.264 sub-stream.
- **EZVIZ** — user name `admin`, password = the verification code on the camera label; enable RTSP in the EZVIZ app
  (LAN live view).
- **TP-Link Tapo** — see [TP-Link Tapo step by step](#tp-link-tapo-step-by-step).

## Every field of a camera

| Field | Default | Limits | Meaning |
|---|:---:|:---:|---|
| Name | — | 1–80 characters | shown on tiles, walls, the camera page and in the audit trail |
| Location | empty | ≤ 120 characters | free text shown next to the name, for example "Gate, north side" |
| Camera group | empty | ≤ 60 characters | who may see the camera; see [Camera groups](privacy.md#camera-groups) |
| Connection | server connects | — | *The server connects to the camera* (pull) or *The camera sends its video to the server* (push, through a [relay](remote-sites.md)) |
| Main stream URL | — | required for pull | `rtsp://`, `rtsps://`, `http://` or `https://`; used for recording, the full view and sound |
| Sub-stream URL | empty | optional | the camera's smaller stream for tiles and walls |
| User name | empty | — | the camera account; may also be pasted into the URL |
| Password | empty | — | stored encrypted; the field shows *unchanged* or *none* when editing |
| Transport | automatic | auto, TCP, UDP | how RTP comes from the camera; automatic tries UDP and falls back to TCP |
| ONVIF address | empty | `http(s)://…`, no credentials | events, PTZ and *Fill in from ONVIF* |
| Recording | continuous | continuous, on events, off | see [Recording and storage](recording.md) |
| Record before the event (s) | 10 | 0–60 | pre-roll, only for *on events* |
| Record after the event (s) | 20 | 1–600 | post-roll, only for *on events* |
| Events that start recording | all except *other* | 7 kinds | motion, tampering, line crossing, intrusion, input, marks, other |
| Keep recordings (days) | 7 | 1–3650 | the camera's retention |
| Order | 0 | whole number | cameras are listed by order, then by name |
| Sound from the camera | off | — | see [Sound](privacy.md#sound-is-off-by-default) |
| Sound recording | record it | — | *record it with the picture* or *live only — not recorded* |
| Motion detection on the server | off | — | for cameras without ONVIF events; see [Events and motion](events-and-motion.md#motion-detection-on-the-server) |
| Sensitivity | 50 | 1–100 | of the motion detection on the server |
| Snapshot address | empty | `http(s)://…`, ≤ 500 characters, no credentials | one JPEG picture of an H.264/H.265 camera, for motion detection |
| Privacy masks | none | ≤ 16 areas | drawn over the picture after the camera is saved; see [Privacy masks](privacy.md#privacy-masks) |
| Motion zones | none (whole picture) | ≤ 16 areas | where motion counts, drawn after the camera is saved |
| Enabled | on | — | a disabled camera is neither connected nor recorded and is missing from the live view |
| Reason | — | ≤ 200 characters, required | written to the audit trail with the old and new values |

**Credentials in the URL.** When you paste `rtsp://admin:secret@192.168.1.64/…`, the server takes the user name and
password out of the address and stores them separately (unless you also typed them into their own fields). The
password is encrypted with the server's secret key and is never shown again, never sent to the browser and never
written to the audit trail — the audit records only whether a password is set.

**Stream addresses are visible only to managers.** People without `video.manage` never receive the stream, ONVIF or
snapshot addresses, the user name or the transport — they reveal your network.

**Cameras behind a relay** keep no stream addresses on the server: the relay at the site knows them. For such a
camera the dialog hides the stream fields, the transport, *Test connection*, *Find cameras* and the snapshot address;
the user name and password remain the camera's ONVIF account for control through the relay.

!!! note "PTZ profile"
    The ONVIF media profile used for PTZ is chosen automatically: the first profile that has PTZ. A different profile
    token (at most 64 characters) can be set only through the API (`ptz_profile`); the form has no field for it.

## Test connection

*Test connection* connects once to the main stream with the user name, password and transport of the form (the
stored password when you leave the field empty) and shows the codec and picture size, for example
`✓ H264 1920×1080`, or the error. It gives up after 8 seconds. Nothing is saved.

## Find cameras in the network

**Find cameras in the network** sends an ONVIF WS-Discovery probe (multicast) and lists the cameras that answer
within the discovery wait time (default 3 seconds, see [Video settings](settings.md)), with name, address and
hardware. Clicking a found camera fills in its ONVIF address and name and runs *Fill in from ONVIF*.

Cameras in another subnet do not answer multicast; the API (`POST /api/v1/video/discover`) also probes up to 16 IP
addresses you list. The probe uses UDP port 3702.

## Fill in from ONVIF

**Fill in from ONVIF** reads the camera's media profiles with the user name and password of the form. The profile
with the largest picture becomes the main stream, the one with the smallest the sub-stream. The answer lists every
profile (name, codec, size) and whether the camera offers **events** and **PTZ**. A wrong password answers *The
camera refused the user name or password*. It gives up after 15 seconds.

For a camera behind a relay, save the camera first: the server reaches it only through its connected relay, and only
the answer is shown (the relay reads the streams itself).

## Camera management list

| Column | Shows |
|---|---|
| Name | the camera name |
| Location | the location and the camera group as a badge |
| Stream | the main stream address and *+ sub*, or *remote, sends via relay* (with *receiving is off* when the server's relay port is not configured) |
| State | the status dot and state with codec, size, frames per second and bit rate of the main stream and the sub-stream, or the last error |
| Recording | *continuous* or *on events* with the retention days, or *off*; badges for ONVIF events, relay control, sound and motion detection |
| Stored | the size of the camera's recordings |

The list refreshes every 5 seconds. Below it, **Storage** shows the space recordings use, the limit and the
percentage. On a server without a licence a badge above the list shows **Cameras: N of 4 without a licence** — the
enabled cameras of all organizations; at 4 it adds that more cameras need a licence (see
[Cameras without a licence](index.md#cameras-without-a-licence)).

Badges in the *Recording* column:

- **ONVIF** — the event connection; **⚠** when it is neither *connected* nor *connecting* (the tooltip has the error).
- **relay control not connected** — a relayed camera with an ONVIF address whose relay does not carry camera control.
- **🔊** — sound is on; **live** when it is not recorded; **⚠** when the camera sends a codec other than AAC, Opus or
  G.711.
- **motion** — motion detection on the server; **⚠** when it is not watching (the tooltip says what is missing).

### Camera states

| State | Meaning |
|---|---|
| online | the stream arrives |
| connecting | the server is connecting or reconnecting |
| offline | the camera does not answer; the error is shown below the state |
| on demand | the camera does not record and nobody watches it, so it is not connected |
| disabled | the camera is switched off |

A camera that does not record connects only while someone watches it and stays connected for the *Keep an unwatched
camera connected* time (default 30 s), so switching back to it is instant. A recording camera and a camera behind a
relay are always connected. A lost connection is retried with a back-off from 1 to 30 seconds.

### Row actions

- **Open** — the camera page.
- **Edit** — the camera dialog, with the picture editor for privacy masks and motion zones.
- **New secret** (cameras behind a relay) — issues a new relay secret; the relay with the old one is disconnected at
  once. Needs a reason. See [Remote sites](remote-sites.md).
- **Delete** — needs a reason. The camera disappears, but **its recordings stay until its retention ends**.

## TP-Link Tapo step by step

Tapo C100, C110, C200, C210, C220, C225, C310, C320WS, C500, C510W, C520WS, TC60, TC70 and similar:

1. In the **Tapo app**: camera → Settings → Advanced settings → **Camera account** — create a user name and password.
   This is the account for RTSP and ONVIF, not your Tapo app account. On newer firmware also turn on
   **Third-Party Compatibility** (Tapo app → Me → Tapo Lab); until then the camera answers ping but keeps ports 554
   and 2020 closed.
2. Give the camera a **fixed IP address** in your router.
3. *Add camera → Camera make: TP-Link Tapo*, the IP address and the camera account. The addresses filled in:
    - main stream `rtsp://IP:554/stream1`
    - sub-stream `rtsp://IP:554/stream2`
    - ONVIF `http://IP:2020/onvif/device_service`
4. **Motion events** come over ONVIF — turn on motion detection in the Tapo app. Pan/tilt models (C200, C210, C500,
   C510W, C520WS) turn and use presets with PTZ. The sound is G.711; switch *Sound* on only where it is lawful.

!!! warning "Tapo limits"
    - Battery cameras and doorbells (C400, C420, C425, D230) have no RTSP and cannot be added.
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
| Sub (tiles, walls) | **H.264**, 640 × 360, 10–15 fps, 256–512 kbit/s | the browser decodes every tile; H.264 plays everywhere, small pictures keep a PC with many tiles light |

"Smart" codecs (H.265+, H.264+, WiseStream) with key frames every 10 seconds or more work, but make recording files
and seeking coarser. Turn them off if exact seeking matters more than space.

!!! tip "Keep cameras in their own network"
    The server connects to the camera addresses a manager enters. Keep the cameras in a separate network reachable
    only from the server.
