---
title: Permissions reference
slug: admin-permissions
sidebar_position: 12
tags: [administration, permissions, roles, reference, security]
---

Every action in the web interface and the API is checked by the server against the signed-in user's **effective
permissions**: the union of the permissions of all their roles. This page lists every permission, which built-in role
has it and what it unlocks.

## Built-in roles and their permissions

Built-in roles cannot be changed. ✓ = the role has the permission, — = it does not.

| Permission | viewer | operator | qa | engineer | org_admin |
|---|:---:|:---:|:---:|:---:|:---:|
| `data.read` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `data.export` | — | ✓ | ✓ | ✓ | ✓ |
| `data.annotate` | — | ✓ | — | ✓ | ✓ |
| `alarm.ack` | — | ✓ | — | ✓ | ✓ |
| `device.manage` | — | — | — | ✓ | ✓ |
| `device.console` | — | — | — | ✓ | ✓ |
| `device.command` | — | ✓ | — | ✓ | ✓ |
| `fota.read` | — | ✓ | ✓ | ✓ | ✓ |
| `fota.manage` | — | — | — | ✓ | ✓ |
| `config.write` | — | — | — | ✓ | ✓ |
| `content.write` | — | — | — | ✓ | ✓ |
| `token.manage` | — | — | — | ✓ | ✓ |
| `user.admin` | — | — | — | — | ✓ |
| `audit.read` | — | — | ✓ | — | ✓ |
| `audit.review` | — | — | ✓ | — | — |
| `report.sign` | — | — | ✓ | — | — |
| `system.admin` | — | — | — | — | ✓ |
| `video.view` | — | ✓ | — | ✓ | ✓ |
| `video.playback` | — | — | — | ✓ | ✓ |
| `video.ptz` | — | ✓ | — | ✓ | ✓ |
| `video.manage` | — | — | — | ✓ | ✓ |
| `video.evidence` | — | — | — | ✓ | ✓ |
| `video.unmask` | — | — | — | — | ✓ |
| `video.audio` | — | — | — | — | ✓ |
| `display.control` | — | ✓ | — | ✓ | ✓ |
| `display.manage` | — | — | — | ✓ | ✓ |

Two flags of the built-in roles also matter:

- **org_admin** is the *administrator* role: the first administrator of an organization gets it, and the
  *Administrator* column of *System → Organizations* shows its holder.
- **qa** is the *quality* role.

!!! note "Operators do not play back recordings"
    The built-in `operator` watches live cameras (`video.view`) and turns PTZ cameras, but has no `video.playback`.
    Give a shift team that must review recordings an organization role with `video.playback`.

## What each permission unlocks

### Data and alarms

| Permission | Unlocks |
|---|---|
| `data.read` | reading assets, sites, datastreams, measurements, devices and their messages, attributes and command history, dashboards, process screens, flows, alarms, incidents, Home and Energy, the live update stream, `/metrics`, the audit chain check |
| `data.export` | exports of measurements, alarms and raw messages (CSV, JSON, PDF), period tables, report templates (read), stored reports and their PDF files |
| `data.annotate` | reserved for annotating measurements and gaps; no function of this version checks it |
| `alarm.ack` | acknowledging and suppressing alarms; creating and updating incidents |

### Devices

| Permission | Unlocks |
|---|---|
| `device.manage` | creating, editing, approving, rejecting and revoking devices; device tokens; datastream assignments of a device; shared attributes; provisioning profiles; MQTT accounts; the automatic device discovery inbox (adopt, ignore); renaming entities; scenes (create, change, delete) |
| `device.console` | opening, using and closing a remote console on a device |
| `device.command` | sending commands to devices and entities, writes through connectors, activating scenes; approving and rejecting commands and automation actions held by four eyes |
| `fota.read` | the Firmware pages: images and campaigns |
| `fota.manage` | uploading firmware, creating and cancelling campaigns |

### Configuration and content

| Permission | Unlocks |
|---|---|
| `config.write` | sites, assets, datastreams and alarm rules (create, change); connectors and Modbus register profiles; notification channels, escalation policies and the delivery log; automation rules; flow settings; reading the security policy |
| `content.write` | the *Settings* menu; dashboards (create, change, delete, share links); process screen objects and SVG import; flows (create, change, deploy, disable, restore, import, inject); report templates (change, preview, revert); branding and white label; languages and translations; apps (PWA); energy settings |
| `token.manage` | creating, listing and revoking your own API tokens |

### Users, audit and system

| Permission | Unlocks |
|---|---|
| `user.admin` | the *Users* menu: accounts, roles and permissions, account requests, camera access of users, resetting passwords and MFA, unlocking, signing a user out everywhere; changing the security policy; switching AI assistants on or off and disconnecting any user's assistant |
| `audit.read` | exporting the audit trail and the access log |
| `audit.review` | reserved for recording audit trail reviews; no function of this version checks it |
| `report.sign` | reserved for electronic signatures of reports; no function of this version checks it |
| `system.admin` | the *System* menu: organizations of the whole server, Server, Domain and TLS, WireGuard, backups, redundant database, updates, licence, front page; the organization export; saving the video settings |

!!! warning "system.admin is server-wide"
    *System → Organizations* lists and changes **every** organization on the server, and the System pages control the
    machine. The built-in `org_admin` role includes `system.admin`. On a server shared by several tenants, give the
    tenants' administrators an organization role without `system.admin` and keep the built-in `org_admin` for the
    people who run the server.

### Cameras and displays

| Permission | Unlocks |
|---|---|
| `video.view` | live view, video walls (watching), snapshots, the list of cameras and camera groups, marking a moment on a camera |
| `video.playback` | recordings, playback, export of clips, events of cameras, the list of evidence holds, the evidence verification key |
| `video.ptz` | turning, zooming and presets of PTZ cameras |
| `video.manage` | adding, changing and deleting cameras, discovery and ONVIF profiles, the relay secret of remote cameras, storage information, reading the video settings, creating and changing video walls |
| `video.evidence` | placing and releasing evidence holds, exporting signed evidence packages |
| `video.unmask` | seeing and exporting camera pictures without privacy masks |
| `video.audio` | hearing camera sound, live and recorded, and exporting it |
| `display.control` | the *Displays* menu: sending cameras, walls and commands to displays |
| `display.manage` | pairing, changing and revoking displays |

Camera groups narrow `video.*` further: a user limited to some camera groups sees only those cameras everywhere (live
view, recordings, walls, events, PTZ). See [Users and permissions](users-and-permissions.md).

!!! note "Sensitive permissions"
    `video.unmask` and `video.audio` belong only to `org_admin` by default, and `video.evidence` to `engineer` and
    `org_admin`. Sound is off on every camera until it is switched on for that camera. Every playback, export,
    unmasked view and listening session is written to the audit trail. See [Privacy](../video-nvr/privacy.md).

## Permissions that are always there

Some functions need only a signed-in user, with no permission:

- your own profile, password, two-factor setup, language and Web Push subscriptions (*My account*);
- the list of permissions and roles;
- your own AI assistant connections.

*Settings → Approvals* lists change requests to users with `config.write` or `device.manage`. Approving or rejecting
one needs the permission of the change it holds (see [Settings pages](settings-reference.md)), and its author can
never approve it.

## Organization roles

*Users → Roles and permissions → New role* creates a role of the organization from any permissions in the catalogue.
The name is 2–32 characters: a lowercase letter first, then `a-z`, `0-9` or `_`. An organization role can be changed
later (*Save permissions*, with a reason); built-in roles cannot.

## API tokens and AI assistants

- An **API token** carries the scopes chosen when it was created; each scope must be one of its owner's
  permissions. A request with a token is allowed only for permissions that are both in the token's scopes and in
  the owner's current permissions.
- An **AI assistant** acts with the permissions of the user who approved it, or only reads if it was approved for
  reading. See [AI assistants: security](../ai-assistants/security.md).
