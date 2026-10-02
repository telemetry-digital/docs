---
title: Notifications and escalation
slug: notifications
sidebar_position: 9
tags: [alarms, notifications, escalation, web-push, webhooks]
---

**Notification channels** say *how* people are told — e-mail, SMS, a voice call, a webhook, Web Push. **Escalation
policies** say *who is told when*: a list of steps in minutes after the alarm. Both live under
*Settings → Notifications* together with the **delivery log**. Every change needs `config.write` and a reason and is
audited.

![Settings → Notifications: channels with kind, target and state; an escalation policy with steps 0 min → e-mail and 15 min → webhook; and the delivery log with the state of every delivery](img/notifications.webp)

Channels are used by escalation policies, by [automation rules](automation-rules.md) (*Notify a channel*) and by
[flows](flow-nodes-actions.md) (*Notify*).

## Channels

**New channel** opens the dialog. The fields depend on the kind.

![The New channel dialog with name, kind E-mail, recipients one per line and a reason](img/new-channel.webp)

| Field | Kinds | Allowed | Notes |
|---|---|---|---|
| Name | all | 1–64 characters, unique in the organization | required |
| Kind | all | E-mail, Webhook (HTTP POST, HMAC), SMS gateway (HTTP), Voice call (HTTP provider), Web Push (browser notifications) | cannot be changed later |
| Recipients — one per line | E-mail, SMS, Voice | 1–50; e-mail addresses must contain `@`; phone numbers for SMS and voice | required |
| URL | Webhook, SMS, Voice | `http://` or `https://`; SMS and voice put `{to}` and `{text}` in the URL or body | required |
| Secret | Webhook | up to 256 characters | signs every call; blank when editing keeps the stored secret |
| Method | SMS, Voice | GET or POST | GET when empty |
| POST body template | SMS, Voice | for example `{"to":"{to}","text":"{text}"}` | used with POST |
| Headers — one per line `Name: value` | Webhook, SMS, Voice | at most 20 | for example an `Authorization` header of the provider |
| Users — one per line, or all | Web Push | 1–200 user names, or `all` for every user of the organization | required |
| Reason (audit trail) | all | up to 200 characters | required |

Secrets are never shown again: the list shows `••••••` in their place.

### How each kind delivers

| Kind | Delivery | Counts as failed when |
|---|---|---|
| E-mail | one e-mail per recipient through the server's SMTP settings | SMTP is not configured on the server, or any recipient fails |
| Webhook | `POST` of a JSON message to the URL | the call fails or the answer is not 2xx |
| SMS gateway | one HTTP call per recipient; `{to}` and `{text}` are URL-encoded in the URL and JSON-escaped in the POST body; the text is the subject followed by the text | any call fails or answers other than 2xx |
| Voice call | as SMS, to a voice provider | as SMS |
| Web Push | a browser notification to every registered browser of the listed users | no browser of those users is registered |

HTTP calls have a timeout of 10 seconds; for a failed call the first 200 characters of the answer are kept as the
error. Deliveries are not retried.

### The webhook message

A webhook receives a JSON object:

| Member | Content |
|---|---|
| `event` | `raised`, `escalated`, `cleared`, `test`, `rule` or `flow` |
| `subject`, `text` | the readable message |
| `alarm` | for alarms: identifier, rule, datastream, key, severity, value, measurement time, retrospective |
| `rule` | for automation rules and flows: the rule's identifier, name, version, transition and inputs, or the flow's name, version, node, topic and value |
| `sent_at` | the time of sending |

| Header | Content |
|---|---|
| `X-Ctrl32-Event` | the event |
| `X-Ctrl32-Channel` | the channel's name |
| `X-Ctrl32-Signature` | `sha256=` and the hex HMAC-SHA256 of the body with the channel's secret (only with a secret) |

Check the signature on the receiving side to be sure a call came from your server.

### Test, edit, revoke

- **Test** sends a test message on the channel and records it in the delivery log and the audit trail; an error is
  shown at once.
- **Edit** changes the name and settings (not the kind).
- **Revoke** asks for a reason and ends the channel for good. Pending deliveries on it are cancelled
  (*channel disabled or removed*); flows that use it get *channel not found or disabled* and cannot be deployed
  until you choose another channel.

A channel can also be disabled without revoking it through the API (`PUT /api/v1/notification-channels/{id}` with
`enabled: false`); the list shows it as *disabled*.

## Escalation policies

**New escalation policy**:

| Field | Allowed | Notes |
|---|---|---|
| Name | 1–64 characters, unique | required |
| Steps — one per line | 1–10 steps; 0 to 10 080 minutes (7 days) | minutes, a vertical bar, the channel's name — see the example below |
| Reason (audit trail) | up to 200 characters | required |

```
0 | shift e-mail
15 | on-call SMS
60 | maintenance webhook
```

The channel name must match an existing channel exactly. Steps are sorted by time. Assign the policy to an alarm rule
(*Escalation policy* in the rule) or to the *Raise an alarm* action of an automation rule.

### What happens when an alarm is raised

1. All steps are scheduled at once, each with its due time.
2. Steps with **0 minutes** are sent immediately — subject *[ctrl32] warning alarm: key = value*.
3. Later steps are sent when due — subject *[ctrl32] still active — …* — but **only while the alarm is still active
   and unacknowledged**. When it was acknowledged or cleared, the step is cancelled. Due steps are checked every 5
   seconds.
4. When the alarm **clears**, the remaining steps are cancelled (*alarm cleared*) and the step-0 channels get a
   *cleared* notice.

The steps are taken from the policy when the alarm is raised; editing the policy affects later alarms.

!!! note "A failed step opens an incident"
    When a step's channel fails and nothing of that step was delivered, an incident *Alarm not delivered* opens —
    severity *critical* for critical alarms, otherwise *major* — so that a missed alarm is never silent. One incident
    per alarm and step. See [Incidents](incidents.md).

### Edit and revoke

**Edit** saves the policy as a new version (*Save as new version*). **Revoke** asks for a reason; a policy that is
used by an enabled current alarm rule cannot be revoked (*policy is used by N active alarm rule(s)*).

## The delivery log

The bottom of the page lists the last 50 deliveries (the API returns up to 500):

| Column | Content |
|---|---|
| Due | when the delivery was or is due |
| Event | `raised`, `escalated`, `cleared`, `test`, `rule` or `flow` |
| Channel | the channel's name and kind |
| Step | the escalation step (0 for the first) |
| State | *scheduled*, *sent*, *failed* or *cancelled* |
| Detail | the error, or the subject |

## Web Push in your browser

Each user switches Web Push on for their own browser under *My account → Notifications*: **Enable in this browser**,
**Send a test notification**, **Disable in this browser**, and a list of registered browsers with the last
notification and their state (*active*, *delivery failed*, *removed*). Web Push needs HTTPS and a browser that
supports it; on phones install the app from the browser menu first.

![My account → Notifications with the button Enable in this browser and the list of registered browsers](img/web-push.webp)

A Web Push channel reaches only users who enabled it in at least one browser. Administrators (users with
`alarm.ack` or `system.admin`) also get Web Push for incidents the system opens.
