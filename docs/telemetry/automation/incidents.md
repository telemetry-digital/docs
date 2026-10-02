---
title: Incidents
slug: incidents
sidebar_position: 10
tags: [incidents, capa, alarms, audit]
---

The **Incidents** page is the log of what went wrong operationally: an alarm nobody received, a broken audit chain,
a camera that stopped recording, a disconnected bridge, or anything you record by hand. Each incident carries its
**impact**, the **cause** found and the **corrective and preventive action (CAPA)**, and it cannot be closed without
them. Everything is kept as history and audited.

![The Incidents page: incidents with the time opened, kind, severity, status, title and organization](img/incidents.webp)

*The incident log.*

## Permissions

| To | Permission |
|---|---|
| See the list and open an incident's detail | `data.read` |
| Open a new incident, change fields, add notes, change the status | `alarm.ack` (or `system.admin`) |

Without `alarm.ack` the detail opens read-only. Users of an organization see that organization's incidents; system
administrators see every organization and the incidents of the server itself (for example a broken audit chain or a
database replica), with the organization in the last column.

## The list

| Column | Content |
|---|---|
| Opened at | when the incident was opened |
| Kind | where it came from — see *Kinds* |
| Severity | minor, major or critical |
| Status | open, investigating, resolved or closed |
| Title | what happened |
| Organization | the organization (empty for incidents of the server) |

The filter above the list shows **Open** (open and investigating, the default), **All**, **Resolved** or **Closed**.
Open incidents come first, newest first; the list shows up to 200.

## Kinds

| Kind | Opened | Severity |
|---|---|---|
| Alarm not delivered | automatically, when every channel of an escalation step failed — one per alarm and step | critical for critical alarms, otherwise major |
| Audit chain broken | automatically, when the audit trail's hash chain does not verify | critical |
| Database replica | automatically, for a replica that is disconnected, lagging, lost or failed | major; critical when lost or failed |
| MQTT connection | automatically, for an MQTT bridge or account disconnected longer than its limit | major |
| Camera, Display, Video storage | automatically, for a camera that stops recording, a display that stops showing, a nearly full video disk — see [Video settings](../video-nvr/settings.md) | set by the source |
| Flow | by a flow's [Incident](flow-nodes-actions.md) node | chosen in the node |
| Manual | by a person with **New incident** | chosen in the dialog |

Automatic incidents are opened **once**: while an incident for the same problem is open or being investigated, the
same problem does not open another one. Some resolve themselves when the problem goes away — a replica or an MQTT
connection that recovers sets the status *resolved* with the cause *The connection was re-established.* or
*The replica recovered by itself*, unless someone already wrote a cause.

When the system opens an incident, administrators are told: an e-mail to the server's global recipients and Web Push
to users with `alarm.ack` or `system.admin` (of the incident's organization, or of every organization for server
incidents). The history records whom it reached. Manual incidents do not notify.

## New incident

**New incident** (`alarm.ack`, as a member of an organization):

![The New incident dialog with title, severity, impact, cause, CAPA and a reason](img/new-incident.webp)

| Field | Default | Allowed | Notes |
|---|:---:|:---:|---|
| Title | — | 1–200 characters | required |
| Severity | major | minor, major, critical | how serious the problem is |
| Impact | — | up to 4 000 characters | what the problem affected |
| Cause (if known) | — | up to 4 000 characters | needed later to resolve |
| CAPA (if known) | — | up to 4 000 characters | needed later to close |
| Reason (audit trail) | — | up to 200 characters | required; stored with the incident |

The incident opens with the status *open* and the detail opens at once.

## The incident detail

Click a row to open it.

![An incident opened: kind, severity, status, reason, impact, cause, CAPA, a note, a reason for the change, the buttons Save fields and note, Start investigating, Mark resolved and Close, and the history](img/incident-detail.webp)

- The head shows kind, severity, status, who opened it and when, the organization and, when closed, who closed it.
- The **detail** block shows what the source recorded — for an undelivered alarm the alarm, step, channel and error;
  for a flow the flow, version, node, topic, value and trigger.
- **Impact**, **Cause** and **CAPA** (each up to 4 000 characters) and a **Note** (up to 1 000 characters) that is
  added to the history.
- **Reason for the change (audit trail)** — required for every change.
- **Save fields and note** saves the text fields and the note without changing the status.
- The status buttons — *Start investigating*, *Mark resolved*, *Close*, *Back to open* — save the fields and change
  the status in one step.
- **History**: every opening, field change, status change, note and notification, with time and person.

## Status workflow

| From | Allowed next status |
|---|---|
| open | investigating, resolved, closed |
| investigating | open, resolved, closed |
| resolved | investigating, closed |
| closed | investigating |

| Status | Requires |
|---|---|
| resolved | a cause |
| closed | a cause and a CAPA |

Resolving stamps the resolution time; closing stamps the closing time and who closed it (and the resolution time if
it was not resolved before). Going back to *open* or *investigating* clears these times, so a reopened incident is
worked on again. A save that changes nothing and has no note is refused (*nothing to change*).

## Audit trail

| Entry | When |
|---|---|
| `incident.open` | an incident was opened — by a person or by the system |
| `incident.update` | fields, notes or the status changed, with the values before and after and your reason |

Attachments and approvals of incidents are not part of this version: record evidence in the fields and notes, and
reference documents by their names or numbers.
