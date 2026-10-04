---
title: Settings pages reference
slug: admin-settings-pages
sidebar_position: 8
tags: [administration, settings, branding, reports, notifications, reference]
---

The **Settings** menu holds the settings of your organization. It appears for users with `content.write`; some pages
need a further permission, named in each section. Every setting belongs to one organization only.

Every save on these pages asks for a **reason** (up to 200 characters). The reason is stored with the old and new
values in the [audit trail](audit.md); a save without a reason is refused.

| Page | Address | Needed to change |
|---|---|---|
| Branding and theme | `/settings/branding` | `content.write` |
| Report templates | `/settings/templates` | `content.write` |
| Stored reports | `/settings/reports` | `data.export` (read only) |
| Connectors | `/settings/connectors` | `config.write` |
| Languages | `/settings/languages` | `content.write` |
| Translations | `/settings/translations` | `content.write` |
| Apps | `/settings/apps` | `content.write` |
| VPN | `/vpn` | `vpn.manage` (shown to organization administrators without `system.admin`) |
| Organization export | `/settings/export` | `user.admin` |
| Notifications | `/settings/notifications` | `config.write` |
| Approvals | `/settings/approvals` | the permission of each change |
| Security policy | `/settings/policy` | `user.admin` (`config.write` to read) |
| AI assistants | `/settings/ai` | `user.admin` |

## Branding and theme

Logo, colours and font apply to every page and to PDF reports of the organization. Every user can still switch
between light and dark for themselves in the menu.

| Field | Default | Limits | Meaning |
|---|:---:|---|---|
| Custom logo | built-in logo | PNG, SVG, JPEG, WebP or GIF; at most 256 KiB; SVG without scripts | shown in the header and in reports |
| Remove the custom logo | off | — | go back to the built-in logo |
| Primary colour | `#e30613` | `#rrggbb` | buttons, links, highlights |
| Font family | system default | a plain font family name, up to 64 characters | the font of the interface |
| Default mode | Follow system | Follow system, Light, Dark | the theme a user sees until they choose one |
| Corner radius | Small | None, Small, Medium, Large | rounding of cards and buttons (0, 3, 6, 10 px) |
| Font size | Normal | Small, Normal, Large | base text size (14, 15, 17 px) |

The form previews colours, font, radius and size while you change them.

### Map tiles

Map widgets and device positions use raster XYZ tiles.

| Field | Default | Limits | Meaning |
|---|:---:|---|---|
| Tile URL template | empty | `http(s)` address with `{z}`, `{x}` and `{y}`, up to 512 characters | your tile server or a provider with a key |
| Attribution | empty | plain text, up to 300 characters | shown on the map |
| Max zoom | 19 | 1–22 | deepest zoom offered |
| Centre latitude | 48.7 | −90 to 90 | initial map centre |
| Centre longitude | 19.5 | −180 to 180 | initial map centre |
| Zoom | 7 | 1–22 | initial zoom |

With an empty tile URL the maps use a self-hosted map archive when `[map] pmtiles` is set in
[config.toml](config-reference.md) (the page then shows *Self-hosted map archive is active* with its name and zoom
range), otherwise the built-in OpenStreetMap tiles. OpenStreetMap allows light use only; for regular use enter your
own tile server.

### White label

The white label replaces the product name, footer and icon. Its settings can be prepared at any time, but *Use the
white label* can be switched on only with a valid licence (requires a licence). See
[White labeling and branding](white-labeling.md).

| Field | Limits |
|---|---|
| Use the white label | needs an installed, valid licence |
| Product name | required when the white label is used; up to 64 characters |
| App name (short) | up to 24 characters |
| Footer text | up to 120 characters |
| Introduction on the start page | up to 400 characters; empty = the standard text |
| Footer link 1–4 | label 1–40 characters; URL up to 300 characters: `http(s)://…`, `mailto:…` or a path |
| Show the version in the footer | on or off |
| Browser icon | PNG or SVG, at most 64 KiB, SVG without scripts |
| Remove the icon | on or off |

The page reloads after saving so that the new name and footer show at once.

## Report templates

PDF reports are rendered from **templates** written in Typst. Every organization can replace the built-in template
of a kind with its own, in any language.

| Control | Meaning |
|---|---|
| Kind | `observations`, `alarms`, `audit`, `generic`, plus any kind your organization has saved |
| Locale | language code of the template (default `en`, up to 8 characters) |
| Mode | **Blocks (designer)** or **Source (Typst)** |
| Start from built-in blocks | load the built-in blocks of this kind into the designer |
| Preview PDF | render the current template with sample data in a new tab |
| Save as new version | store a new version (a reason is required); the template is compiled first and refused if it does not compile |
| Revert to built-in | remove the organization's template for this kind and locale |
| Data contract | the sample `data.json` the template receives |
| layout.typ helpers | the shared helpers every template can use |

The state line shows *built-in (no organization override)* or *organization version N* with its checksum, and whether
the template is made of blocks or hand-written source. Every save is a new version; earlier versions stay in history.
A template source may be at most 512 KiB.

### Page settings (designer)

| Field | Default | Limits |
|---|:---:|---|
| Paper | A4 | A4, A3, A5, US Letter, US Legal |
| Landscape | off | — |
| Font | Libertinus Serif | up to 64 characters |
| Font size (pt) | 9.5 | 6–16 in steps of 0.5 |

### Blocks

Blocks render from top to bottom; move them with ↑ and ↓, remove them with *Remove*, add them with *Add block*.

| Block | Parameters (default) |
|---|---|
| Title | Title (`{{title}}`), Subtitle (`{{subtitle}}`), Show period (on), Show scope (on) |
| Summary facts | Only these keys (empty = all) |
| Chart | Caption (empty); only kinds that provide a chart |
| Text | Text (a sentence with `{{org}}` and the period), Style (normal, note, warning) |
| Data table | Only these columns (empty = all), Max rows (3000), Font size (8 pt) |
| Daily statistics | Value column (`value`), Time column (`measured_at`), Decimals (2) |
| Signatures | Roles (Prepared by, Reviewed by, Approved by), Date line (on) |
| Space | Height (8 pt) |
| Page break | — |
| End marker | — |

Texts may use tokens such as `{{title}}`, `{{org}}`, `{{period.from}}`, `{{period.to}}`, `{{scope.asset}}`,
`{{scope.datastream}}`, `{{scope.unit}}` and `{{summary.count}}`. A report holds at most 3000 rows.

!!! note "Typst must be installed"
    Without the Typst program on the server, previews and PDF reports answer *unavailable*. The installers download
    it; see `[report]` in the [configuration reference](config-reference.md).

## Stored reports

Every generated PDF is a **record**: the hash of its data, the hash of the file and the head of the audit chain at
the time it was generated. The page lists the latest 100 reports with *Generated*, *Kind*, *By*, *Template* (version
or built-in), *Size* and *File hash*. *Download* checks the file against its recorded hash before it is sent; a file
that does not match is refused and the failure is audited.

## Scheduled reports

Reports mailed on a schedule (`config.write`): see [Scheduled reports by e-mail](../object-counting/scheduled-reports.md).

| Field | Limits |
|---|---|
| Name | 1–80 characters |
| Content | object counting summary of an area; a period table widget of a dashboard; a report template (observations of a datastream, alarms) |
| Period | previous day, previous week (Monday to Sunday), previous month, in the organization's time zone |
| Schedule | a cron expression of five fields (minute, hour, day of month, month, day of week); presets daily 07:00, Monday 07:00, 1st of the month 07:00 |
| Recipients | up to 10 e-mail channels and 50 addresses; at least one |
| Attachments | PDF and/or Excel (CSV for templates); at least one |
| Enabled | a disabled report is not sent |

*Send now* sends the report for the period before now; *Delivery log* lists the last 100 runs with their state,
recipients, attachments (name, size, SHA-256) and error. Every change and every sending is audited.

## Connectors

Connectors bring data from systems that do not send it on their own: a ChirpStack network server (LoRaWAN), an OPC UA
server or a Modbus TCP device. The page lists the connectors with *Name*, *Kind*, *Messages*, *Last seen* and *State*,
and the versioned **register profiles** of Modbus connectors. Every field is described in
[Connectors](../devices/connectors.md).

## Languages

English is the base language and is always enabled. Tick the languages your users may choose; every user picks their
own language in the menu.

| Field | Meaning |
|---|---|
| Language check boxes | English, Slovenčina, Čeština, Українська, Deutsch, Polski, Magyar, Español, Français, Italiano, Română, Suomi, Svenska, Norsk, Dansk, Eesti, Nederlands, Български, Ελληνικά |
| Default language | must be one of the enabled languages |

Each language shows how many built-in translations it has; missing strings fall back to English until you add them
under *Translations*.

## Translations

Replace any text of the interface with your own wording, per language.

| Control | Meaning |
|---|---|
| Language | one of the enabled languages other than English |
| Show only untranslated | hide strings that have a built-in translation or your own |
| filter… | search in the English text, the built-in translation and your translation |
| Your translation | your text; leave it empty to remove your override |
| Reason (audit trail) | required to save |
| Save translations | saves only the strings you changed |

The counter shows the number of strings, how many are untranslated and how many your organization overrides. One save
takes up to 5000 strings; a key is at most 500 and a text at most 2000 characters.

## Apps

Installable apps for phones, tablets and computers, each with its own name, icon label, start page and menu. Every
field and option is described in [Apps for phones and tablets](pwa-apps.md).

| Field | Limits |
|---|---|
| Name | 1–64 characters, required |
| Name on the home screen | up to 24 characters (about 12 fit under an icon) |
| Address | `/app/<address>`: 1–40 lowercase letters, digits and dashes, starting with a letter or digit; unique |
| Start page | a page of this application; *Path of the start page* for a custom path (up to 300 characters, no other site) |
| Menu sections | at least one of Dashboards, Home, Energy, Object counting, Cameras, Displays, Incidents, Devices, Assets, Flows, Firmware, Users, Audit, Settings, System |
| Own colour | `#rrggbb`, or off for the organization's colour |
| Cameras in live view and on video walls | automatic, one camera per row, grid |

## AI assistants

AI assistants connect over MCP (Model Context Protocol) and act with the permissions of the user who approves them,
never more. Every change they make carries a reason and is audited with the note *via MCP*. Camera sound, unmasked
pictures and evidence need their own permissions, as in the web interface.

| Control | Default | Meaning |
|---|:---:|---|
| Allow AI assistants in this organization | off | off: no assistant can connect, and connected ones are refused at once; they continue when it is switched on again |
| Reason (audit trail) | — | required |

Only users with `user.admin` can change the switch. The page also shows:

- whether **changes through AI** are licensed on this server — reading is always possible, changes require a licence;
- the **MCP address** of this server and the header form `Authorization: Bearer c32_…` for assistants that take a
  personal API token;
- the number of API operations and MCP tools offered;
- **Connected assistants** of all users: assistant, user, access (*reading only* or *user permissions*), connected,
  last used, state, and *Disconnect* (asks for a reason).

See [Connecting AI assistants](../ai-assistants/connecting.md) and [AI assistants: security](../ai-assistants/security.md).

## Organization export

One ZIP file with the whole configuration of the organization and its records for a period. It needs `user.admin`
(an organization administrator's task) and is audited.

| Field | Default |
|---|:---:|
| From | 365 days ago |
| To | now; a chosen date counts to the end of that day |

The ZIP contains:

- configuration as JSON: organization, sites, assets, datastreams, devices, assignments, alarm rules, roles, users,
  user role history, report templates, stored reports, connectors, screens (dashboards), process screen objects,
  device commands;
- records of the period as CSV: measurements (up to 5 000 000 rows), gaps, alarms with their events, raw messages,
  audit trail, access log;
- a manifest with the SHA-256 of every file and the head of the audit chain.

## Notifications

Where alarms are delivered and how they escalate.

### Channels

| Kind | Fields |
|---|---|
| E-mail | Recipients: 1–50 e-mail addresses, one per line |
| Webhook (HTTP POST, HMAC) | URL `http(s)://host/path`; Secret: the body is signed with HMAC-SHA256 in the header `X-Ctrl32-Signature`; Headers |
| SMS gateway (HTTP) | URL with the placeholders `{to}` and `{text}`; Recipients: 1–50 phone numbers; Method GET or POST (default GET); POST body template; Headers |
| Voice call (HTTP provider) | as SMS gateway |
| Web Push (browser notifications) | Users: 1–200 user names, one per line, or `all` |

Every channel has a **Name** (up to 64 characters, unique) and a reason. Headers are `Name: value` lines, at most 20.
A stored secret is kept when the field is left blank. The kind cannot be changed after creation.

The channel list shows *Name*, *Kind*, *Target* and *State* (enabled, disabled, revoked) with **Test** (sends a test
notification), **Edit** and **Revoke** (asks for a reason). E-mail channels need `[smtp]` in
[config.toml](config-reference.md); Web Push reaches only users who enabled it under *My account → Notifications*.
Alarm rules without an escalation policy use the server's global e-mail recipients (`smtp.to`).

### Escalation policies

| Field | Limits |
|---|---|
| Name | up to 64 characters, unique |
| Steps | 1–10 lines `minutes | channel name`; minutes 0 to 10 080 (7 days) |

Step 0 is sent at once; later steps only while the alarm is still active and not acknowledged; clearing the alarm
sends a notice to the channels of step 0. Saving a policy creates a new version. A policy used by an active alarm rule
or by an automation rule (also a disabled one) cannot be revoked. Assign a policy to an alarm rule on the datastream (see
[Alarms, notifications and incidents](../automation/alarms.md)).

### Delivery log

The latest 50 deliveries: *Due*, *Event*, *Channel*, *Step*, *State* (scheduled, sent, failed) and *Detail*.

## Approvals

Changes held by the four-eyes policies wait here.

| Kind | Held when | Approved by a different user with |
|---|---|---|
| Alarm limits | *Four eyes for alarm limits* is on: new alarm rule versions and disabling a rule | `config.write` |
| Datastream assignments | *Four eyes for datastream assignments* is on: assigning or removing a datastream on a device | `device.manage` |
| Flows | *Four eyes for commands* is on: deploying a flow that sends commands | `content.write` and `device.command` |
| VPN access | *Four eyes for VPN access* is on: a time-limited access grant for a technician (VPN peer of the kind Technician) | `vpn.manage` or `system.admin` (for a peer of the whole server: `system.admin`) |

**Pending changes** shows *Change* (summary and reason), *Kind*, *Requested by*, *Requested* and *Expires*, with
**Approve** and **Reject** (each asks for a reason). The author of a request sees *your own request* instead of the
buttons. Approving replays the original request under the approver's name; both names go to the audit trail.
Requests expire after **7 days**. An approved VPN access starts at the approval and lasts the requested time (see
[Access grants](../vpn/organizations-and-approvals.md#access-grants)).

**History** shows each decision: approved, rejected, failed (approved, but the change itself failed) or expired.

Commands held by *Four eyes for commands* are approved on the device page (tab *Commands*), not here (see
[Commands and firmware](../devices/commands-and-firmware.md)).

## Security policy

| Option | Default | Effect |
|---|:---:|---|
| Four eyes for commands | off | every command (set point, switch, button, connector write, device command, scene, AI assistant, flow) waits until a different user with `device.command` approves it; deploying a flow that sends commands becomes a change request |
| Four eyes for alarm limits | off | new alarm rule versions and disabling a rule become change requests approved by a different user with `config.write` |
| Four eyes for datastream assignments | off | assigning or removing a datastream on a device becomes a change request approved by a different user with `device.manage` |
| Four eyes for VPN access | off | a technician's VPN access becomes active only after a different user with `vpn.manage` or `system.admin` approves a time-limited access grant here under *Approvals*; new technicians are added disabled, and enabling one or changing its expiry directly is refused; grants require a licence |
| Reason (audit trail) | — | required |

The other rules of the organization's policy have fixed values in this version and are not shown on the page:

| Rule | Value |
|---|:---:|
| Sign-out after inactivity | 30 min |
| Longest session | 8 h |
| Failed sign-ins before the account locks | 5 |
| Lock duration | 15 min |
| Shortest password | 12 characters |
| Character classes in a password | at least 3 of 4 |
| Roles that must use two-factor sign-in | server_admin, org_admin, qa, engineer |

In the internet profile every user must use two-factor sign-in.
