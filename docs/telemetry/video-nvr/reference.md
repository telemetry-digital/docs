---
title: Video reference
slug: video-reference
sidebar_position: 11
tags: [video, reference, limits, api]
---

All limits, defaults and permissions of the video recorder on one page. The explanations are on the pages linked in
each section.

## Camera limits and defaults

| Item | Default | Limits |
|---|:---:|:---:|
| Enabled cameras of the server | — | up to 4 without a licence |
| Name | — | 1–80 characters |
| Location | empty | ≤ 120 characters |
| Camera group | empty | ≤ 60 characters |
| Stream address schemes | — | `rtsp`, `rtsps`, `http`, `https` |
| Transport | auto | auto, tcp, udp |
| Recording | continuous | continuous, events, off |
| Pre-roll | 10 s | 0–60 s |
| Post-roll | 20 s | 1–600 s |
| Event kinds that record | 6 of 7 | motion, tamper, line, intrusion, input, mark, other |
| Retention | 7 days | 1–3650 days |
| PTZ profile token | automatic | ≤ 64 characters (API only) |
| Motion detection | off | — |
| Motion sensitivity | 50 | 1–100 |
| Snapshot address | empty | ≤ 500 characters |
| Privacy masks | none | ≤ 16, 3–16 points each |
| Motion zones | whole picture | ≤ 16, 3–16 points each |
| Sound | off | — |
| Sound recording | recorded | recorded, live only |
| Enabled | on | — |

See [Adding cameras](cameras.md).

## Viewing and export limits

| Item | Limit |
|---|---|
| Playback speed (camera page) | 0.5×, 1×, 2×, 4×, 8×, 16× (the server accepts 0.25–16) |
| Digital zoom | 1× to 8× |
| MP4 export | at most 1 hour per file |
| Evidence package | at most 1 hour; reason ≤ 500 characters; case number ≤ 80 characters |
| Evidence hold | title 1–120 characters; case number ≤ 80; up to 31 days; ending at most 1 day ahead |
| Timeline query | at most 32 days per request |
| Events listed | 2000 per request; 300 on the camera page; 1000 in an evidence package |
| Mark | label 1–200 characters; record 0–3600 s (60 s from the camera page) |
| Send to a display | 120 s from the camera page |
| Test connection | gives up after 8 s |
| Fill in from ONVIF | gives up after 15 s |
| PTZ request | gives up after 8 s; speeds −1 to 1 |
| Discovery | wait 1–15 s (default 3); up to 16 extra IP addresses through the API |

See [Live view and playback](watching.md) and [Evidence](evidence.md).

## Video wall limits

| Item | Limit |
|---|---|
| Name | 1–80 characters |
| Screens | 1–8 |
| Screen name | ≤ 40 characters |
| Columns and rows | 1–8 each; the editor offers 12 layouts from 1 × 1 to 8 × 6 |
| Stream per screen | automatic, sub-stream, main stream |

See [Video walls](video-walls.md).

## Relay limits

| Item | Limit |
|---|---|
| Secret | 192 bits, 32 characters; `relay.toml` needs at least 16 |
| Blocking | 10 failures in 10 minutes → 15 minutes (adjustable) |
| Camera control poll | up to 25 s; not connected after 35 s without a poll |
| Camera control body | 1 MB each way; 32 waiting requests per camera |
| Reconnect back-off | 1 s doubling to 30 s |

See [Remote sites](remote-sites.md).

## Permissions

| Permission | operator | engineer | org_admin |
|---|:---:|:---:|:---:|
| `video.view` | yes | yes | yes |
| `video.playback` | no | yes | yes |
| `video.manage` | no | yes | yes |
| `video.ptz` | yes | yes | yes |
| `video.evidence` | no | yes | yes |
| `video.unmask` | no | no | yes |
| `video.audio` | no | no | yes |
| `display.control` | yes | yes | yes |
| `display.manage` | no | yes | yes |

See [Privacy, sound and permissions](privacy.md) for what each one allows and the audit entries.

## API endpoints

Everything in the interface is available through the API under `/api/v1/video/`, with the same permissions, camera
groups and audit:

| Method and path | Permission |
|---|:---:|
| `GET /video/cameras`, `GET /video/cameras/{id}` | `video.view` |
| `POST /video/cameras`, `PUT /video/cameras/{id}`, `DELETE /video/cameras/{id}?reason=` | `video.manage` |
| `POST /video/cameras/test`, `POST /video/discover`, `POST /video/onvif/profiles` | `video.manage` |
| `POST /video/cameras/{id}/push-secret` | `video.manage` |
| `GET /video/cameras/{id}/live?quality=sub or main` (WebSocket) | `video.view` |
| `GET /video/cameras/{id}/snapshot` | `video.view` |
| `GET /video/cameras/{id}/recordings`, `/playback`, `/export`, `/events` | `video.playback` |
| `POST /video/cameras/{id}/events` (mark) | `video.view` |
| `GET /video/cameras/{id}/ptz/presets`, `POST /video/cameras/{id}/ptz` | `video.ptz` |
| `GET /video/events` | `video.playback` |
| `GET /video/storage` | `video.manage` |
| `GET /video/walls`, `GET /video/walls/{id}` | `video.view` |
| `POST /video/walls`, `PUT /video/walls/{id}`, `DELETE /video/walls/{id}?reason=` | `video.manage` |
| `GET /video/holds`, `GET /video/evidence/key` | `video.playback` |
| `POST /video/holds`, `DELETE /video/holds/{id}?reason=`, `GET /video/cameras/{id}/evidence` | `video.evidence` |
| `GET /video/camera-groups` | `video.view` |
| `GET /video/settings` | `video.manage` |
| `PUT /video/settings` | `system.admin` |

Paths are relative to `/api/v1`. Times are RFC 3339.

## Command line

| Command | What it does |
|---|---|
| `ctrl32-telemetry camera-relay -config relay.toml` | runs the camera relay |
| `ctrl32-telemetry camera-relay -config relay.toml -check` | tests the relay configuration and exits |
| `ctrl32-telemetry service install-relay -config relay.toml` | installs the relay as a service (also `uninstall-relay`, `start-relay`, `stop-relay`, `restart-relay`) |
| `ctrl32-telemetry verify-evidence [-key base64] package.zip` | verifies an evidence package |
