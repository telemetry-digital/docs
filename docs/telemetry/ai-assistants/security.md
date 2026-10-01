---
title: What an assistant can do, and security
slug: ai-security
sidebar_position: 2
tags: [ai, mcp, security, permissions]
---

## What an assistant can do

Every operation of the REST API that a user performs is offered to assistants as tools — dashboards, devices,
alarms, flows, cameras, reports, users, smart home, period tables and more. Tools come in pairs per area:

- `<area>_read` for reading — marked read-only, so a client may run it without asking;
- `<area>_change` for everything else — marked as changing, so a client asks you first.

Answers come back as JSON; camera snapshots as images; CSV and text as text. Large binary files — PDF reports, MP4
exports, backups — are not passed to the assistant: it gets their size and the address to download them.

## Not offered, and why

| Operations | Reason |
|---|---|
| Sign-in, sign-out, password recovery, own password, two-factor enrolment | done by the user |
| API tokens, AI access settings and approvals | an assistant does not create lasting credentials or manage AI access |
| Endpoints of devices, connectors, relays, displays and replicas | authenticated by those machines, not by a user |
| Live video and recordings as streams, live events | streams; snapshots, recording lists and MP4 exports are offered |

## Security

- **Permissions**: the assistant acts as you. What it may do is limited by your permissions and camera groups.
  *Reading only* refuses every change. Camera sound, unmasked pictures and evidence need their own permissions
  (`video.audio`, `video.unmask`, `video.evidence`), exactly as in the web interface.
- **Audit**: changes are audited as always, with the actor "user (AI assistant *client* via MCP)". Approving,
  disconnecting and switching AI access are audited too.
- **Reasons**: every change an assistant makes carries a reason, like any change.
- **Tokens**: access tokens are valid for one hour and **only at `/mcp`** — the REST API refuses them. Refresh tokens
  rotate on every use and end after 90 days without use. All tokens are stored only as hashes.
- **Licence**: checked on every call — installing or removing the licence takes effect at once.
- **Rate limit**: 300 calls a minute per user.

!!! warning "Read what you allow"
    An assistant can do whatever your permissions allow — including sending commands to devices. Approve
    *Everything my permissions allow* only for assistants you trust, prefer *Reading only* otherwise, and review the
    audit trail. With the four-eyes policy, commands from an assistant still wait for a second person.
