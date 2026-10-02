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

| Permission | viewer | operator | qa | engineer | org_admin | server_admin |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| `data.read` | ✓ | ✓ | ✓ | ✓ | ✓ | — |
| `data.export` | — | ✓ | ✓ | ✓ | ✓ | — |
| `data.annotate` | — | ✓ | — | ✓ | ✓ | — |
| `alarm.ack` | — | ✓ | — | ✓ | ✓ | — |
| `device.manage` | — | — | — | ✓ | ✓ | — |
| `device.console` | — | — | — | ✓ | ✓ | — |
| `device.command` | — | ✓ | — | ✓ | ✓ | — |
| `fota.read` | — | ✓ | ✓ | ✓ | ✓ | — |
| `fota.manage` | — | — | — | ✓ | ✓ | — |
| `config.write` | — | — | — | ✓ | ✓ | — |
| `content.write` | — | — | — | ✓ | ✓ | — |
| `token.manage` | — | — | — | ✓ | ✓ | — |
| `user.admin` | — | — | — | — | ✓ | — |
| `audit.read` | — | — | ✓ | — | ✓ | — |
| `audit.review` | — | — | ✓ | — | — | — |
| `report.sign` | — | — | ✓ | — | — | — |
| `system.admin` | — | — | — | — | — | ✓ |
| `video.view` | — | ✓ | — | ✓ | ✓ | — |
| `video.playback` | — | — | — | ✓ | ✓ | — |
| `video.ptz` | — | ✓ | — | ✓ | ✓ | — |
| `video.manage` | — | — | — | ✓ | ✓ | — |
| `video.evidence` | — | — | — | ✓ | ✓ | — |
| `video.unmask` | — | — | — | — | ✓ | — |
| `video.audio` | — | — | — | — | ✓ | — |
| `display.control` | — | ✓ | — | ✓ | ✓ | — |
| `display.manage` | — | — | — | ✓ | ✓ | — |
| `vpn.manage` | — | — | — | — | ✓ | — |

Two flags of the built-in roles also matter:

- **org_admin** is the *administrator* role of an organization: the first administrator of an organization gets it,
  and the *Administrator* column of *System → Organizations* shows its holder. It administers the organization only —
  it does not include `system.admin`.
- **server_admin** administers the whole server: it holds `system.admin` and nothing else, and is usually given in
  addition to `org_admin`. Only a server administrator grants or removes it.
- **qa** is the *quality* role. It is the only built-in role that records reviews of the audit trail
  (`audit.review`) and signs reports (`report.sign`): administrators do not review or sign their own work. Give these
  permissions to other people through an organization role if your procedures need it.

!!! note "Operators do not play back recordings"
    The built-in `operator` watches live cameras (`video.view`) and turns PTZ cameras, but has no `video.playback`.
    Give a shift team that must review recordings an organization role with `video.playback`.

## What each permission unlocks

### Data and alarms

| Permission | Unlocks |
|---|---|
| `data.read` | reading assets, sites, datastreams, measurements, devices and their messages, the *Telemetry* tab of a device, attributes and command history, annotations, dashboards, process screens, flows, alarms, incidents, Home and Energy, the live update stream, `/metrics`, the audit chain check |
| `data.export` | exports of measurements, alarms and raw messages (CSV, JSON, PDF), the CSV and Excel export of one key on the *Telemetry* tab, period tables, report templates (read), stored reports, their PDF files and their signatures |
| `data.annotate` | adding annotations to measurements (one value or a period) and retracting them; see [The device page](../devices/device-page.md#annotations) |
| `alarm.ack` | acknowledging and suppressing alarms; creating and updating incidents |

### Devices

| Permission | Unlocks |
|---|---|
| `device.manage` | creating, editing, approving, rejecting and revoking devices; device tokens; datastream assignments of a device, including *Create datastream and assign* for a key without a datastream on the *Telemetry* tab; shared attributes; provisioning profiles; MQTT accounts; the automatic device discovery inbox (adopt, ignore); renaming entities; scenes (create, change, delete) |
| `device.console` | opening, using and closing a remote console on a device |
| `device.command` | sending commands to devices and entities, writes through connectors, activating scenes; approving and rejecting commands and automation actions held by four eyes |
| `fota.read` | the Firmware pages: images and campaigns |
| `fota.manage` | uploading firmware, creating and cancelling campaigns |

### Configuration and content

| Permission | Unlocks |
|---|---|
| `config.write` | sites, assets, datastreams (including their physical range) and alarm rules (create, change); connectors and Modbus register profiles; notification channels, escalation policies and the delivery log; automation rules; flow settings; reading the security policy |
| `content.write` | the *Settings* menu; dashboards (create, change, delete, share links); process screen objects and SVG import; flows (create, change, deploy, disable, restore, import, inject); report templates (change, preview, revert); branding and white label; languages and translations; apps (PWA); energy settings |
| `token.manage` | creating, listing and revoking your own API tokens |

### Users, audit and system

| Permission | Unlocks |
|---|---|
| `user.admin` | the *Users* menu: accounts, roles and permissions, account requests, camera access of users, resetting passwords and MFA, unlocking, signing a user out everywhere; changing the security policy; switching AI assistants on or off and disconnecting any user's assistant; the organization export |
| `audit.read` | *Audit → Audit trail* and *Reviews*: reading the audit trail and its reviews; exporting the audit trail and the access log |
| `audit.review` | recording a signed review of the audit trail — a period or one entry (*Audit → Audit trail*); see [Audit trail](audit.md#reviews) |
| `report.sign` | signing stored PDF reports electronically (*Audit → Report signatures*); see [PDF reports and exports](../energy/pdf-and-exports.md#electronic-signatures) |
| `system.admin` | server administration: the *System* menu — organizations of the whole server, Server, Domain and TLS, VPN of the whole server, backups, redundant database, updates, licence, front page; saving the video settings; granting and removing `server_admin` and changing the accounts of server administrators |

!!! warning "system.admin is server-wide"
    *System → Organizations* lists and changes **every** organization on the server, and the System pages control the
    machine. Only the built-in role `server_admin` holds `system.admin`. After the upgrade to 0.65 every user who had
    `org_admin` also holds `server_admin`, so nobody lost access — review who holds it under *Users → Roles and
    permissions* and remove it where it is not needed, especially on a server shared by several tenants.

### VPN

| Permission | Unlocks |
|---|---|
| `vpn.manage` | *Settings → VPN*: the peers, access rules and access grants of the own organization; approving technicians' VPN access held by four eyes. The VPN server settings and the peers and rules of the whole server need `system.admin`. See [VPN for organizations](../vpn/organizations-and-approvals.md) |

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

*Settings → Approvals* lists change requests to users with `config.write`, `device.manage`, `vpn.manage` or `system.admin`. Approving or rejecting
one needs the permission of the change it holds (see [Settings pages](settings-reference.md)), and its author can
never approve it.

## Organization roles

*Users → Roles and permissions → New role* creates a role of the organization from any permissions in the catalogue.
The name is 2–32 characters: a lowercase letter first, then `a-z`, `0-9` or `_`. An organization role can be changed
later (*Save permissions*, with a reason); built-in roles cannot. A role with `system.admin` can be created or changed
only by a server administrator.

## API tokens and AI assistants

- An **API token** carries the scopes chosen when it was created; each scope must be one of its owner's
  permissions. A request with a token is allowed only for permissions that are both in the token's scopes and in
  the owner's current permissions.
- An **AI assistant** acts with the permissions of the user who approved it, or only reads if it was approved for
  reading. See [AI assistants: security](../ai-assistants/security.md).
- **Electronic signatures and audit reviews** are given only by a signed-in person in the browser, who enters the
  password again. An API token or an AI assistant cannot sign a report or record a review, even with `report.sign` or
  `audit.review` in its scopes.
