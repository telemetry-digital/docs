---
title: Events and motion
slug: video-events
sidebar_position: 3
tags: [video, events, motion, onvif, ptz]
---

Events tell you **when something happened** on a camera. They outline the camera's tile in the live view and on
walls, appear as marks on the timeline, start recording on events, enlarge the camera on displays and can start
flows.

## Events from the camera (ONVIF)

Enter the camera's ONVIF address (`http://camera/onvif/device_service`) with the same user name and password as the
stream. The server subscribes to the camera's events and stores, with their start and end:

- motion
- tamper
- line crossing
- intrusion
- digital inputs

The server never decodes video, so event detection costs nothing per camera — **the camera's own analytics does the
work**. Set its sensitivity and zones in the camera itself. Motion that starts again within 5 seconds joins the same
event (adjustable). Events of a disconnected camera, a changed camera and a restarted server are closed properly.

## Motion detection on the server

Cheap cameras, MJPEG cameras and older models without ONVIF events get motion events from the server:
*Camera management → camera → Motion detection on the server*. The server looks at JPEG pictures only:

- a camera with an **MJPEG stream** — its frames, at most 2.5 a second (the sub-stream is tried first);
- an **H.264/H.265 camera** — its **snapshot address** (one JPEG picture), polled once a second with the camera's
  user name and password. Examples: Hikvision `http://camera/ISAPI/Streaming/channels/101/picture`, Dahua
  `http://camera/cgi-bin/snapshot.cgi`, Axis `http://camera/axis-cgi/jpg/image.cgi`. Never put the password into
  the address.

Set the **sensitivity** (1–100) and draw **motion zones** (green rectangles over the picture) so that only the gate
counts, not the road or the trees behind it. An object that stops — a parked car — becomes background; a change of
almost the whole picture (lights, infrared switching, exposure) resets the background instead of raising an event.

These events have the kind `motion` and the source `server` and behave like the camera's own. The camera list shows
the state: *watching (MJPEG)*, *watching (snapshots)*, or what is missing.

!!! tip "Prefer the camera's own detection"
    Where a camera offers ONVIF motion events, use them. Server detection is the fallback and costs a little
    processor time per camera (little with a small MJPEG sub-stream; 320 × 180 is enough).

## Marks

The **flag button** on the camera page (or a flow) marks the camera with a label, for example "Delivery arrived". On
a camera recording on events, a mark also records the chosen number of seconds. Marks are audited.

## PTZ

Cameras with pan, tilt and zoom are controlled from the player: hold an arrow or +/− to move, release to stop; use
the presets stored in the camera. The speed (slow, normal, fast) and whether the controls are shown are remembered in
your browser (on phones the controls are hidden until you press the PTZ button).

PTZ needs the permission `video.ptz`. Moves are audited once per 5 minutes per user and camera; preset changes
always.

## Events in flows

Flows have the trigger **Camera event** (camera, kind, start or end) and the action **Mark on camera**. Example: a
door contact opens → mark "Door opened" and record 30 seconds. See [Flows](../automation/flows.md).

## Floor plans and displays

On a floor plan the camera's pin pulses orange while an event is in progress. A display can enlarge a camera with an
event to the whole screen. See [Video walls, floor plans and widgets](video-walls.md) and
[Displays](../dashboards/displays.md).
