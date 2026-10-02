---
title: Privacy, sound and permissions
slug: video-privacy
sidebar_position: 8
tags: [video, privacy, gdpr, permissions, audit, sound]
---

Recordings of people are personal data. telemetry.digital gives you the tools to keep them under control — and keeps
the sensitive ones **off by default, behind their own permission, and audited**.

## Privacy masks

*Camera management → Edit → Privacy masks*: drag over the live picture to cover areas that must not be watched — a
neighbour's window, a cash desk, a public street.

The masks travel with every live and recorded stream, and **every player covers them**: the camera page, video walls,
camera widgets, floor plans, displays and phones. Snapshots are masked on the server.

The recording itself stays complete. Therefore:

- seeing a masked camera **unmasked** needs the permission `video.unmask` (administrators only) and is audited;
- MP4 export and evidence packages of a masked camera need `video.unmask`;
- to keep the areas out of the recording altogether, set the privacy masks **in the camera itself** (its web page
  or ONVIF) — then they are missing from the recording and from every export.

## Sound is off by default

Recording sound — conversations — is regulated more strictly than pictures. A camera's sound stays off until you
switch it on per camera (*Camera management → camera → Sound*). Do it only where it is lawful, and inform people with
a sign.

- Supported: **AAC**, **Opus** and **G.711** (TP-Link Tapo and most Hikvision and Dahua defaults). Other codecs show
  a warning with the advice to choose AAC or G.711.
- **Sound recording**: *record it with the picture*, or *live only* — heard live but never written to disk, so it is
  not in recordings, playback or exports.
- Hearing sound live or recorded, and exporting it, needs the permission `video.audio` (administrators). Each
  listening is audited.
- Walls, widgets and displays never play sound. At speeds other than 1× there is no sound.

## Permissions

| Permission | Allows | Built-in roles |
|---|---|---|
| `video.view` | live view, video walls | operator, engineer, org_admin |
| `video.playback` | recordings, playback, export | engineer, org_admin |
| `video.manage` | cameras, walls, storage, discovery | engineer, org_admin |
| `video.ptz` | PTZ control and presets | operator, engineer, org_admin |
| `video.evidence` | hold recordings as evidence, signed evidence packages | engineer, org_admin |
| `video.unmask` | see and export pictures without privacy masks | org_admin |
| `video.audio` | hear camera sound live and recorded, export it | org_admin |
| `display.control` | send cameras and commands to displays | operator, engineer, org_admin |
| `display.manage` | pair, change and disconnect displays | engineer, org_admin |

## Camera groups

A camera may belong to a **camera group** (*Camera management → Edit → Camera group*). A user limited to some groups
(*Users → user → Cameras*) sees only those cameras — live view, recordings, walls, events, PTZ, marks and floor plans,
also through the API and AI assistants. An operator limited to groups can send only those cameras to displays.

![The Add camera dialog with the field Camera group (who may see it; optional) below name and location](img/add-camera.webp)

## Audit: who watched what

- Every **playback** and every **export** is written to the audit trail with the user, the camera and the time range.
- Unmasked viewing, listening to sound, PTZ moves, marks and evidence actions are audited.
- Changes of cameras and walls need a **reason** and are audited.

!!! warning "You are the data controller"
    telemetry.digital does not decide for you what is lawful. Set the retention to what the purpose needs, mark the
    recorded area, mask what must not be watched, keep sound off unless it is lawful, and give `video.playback`,
    `video.unmask` and `video.audio` only to people who need them.
