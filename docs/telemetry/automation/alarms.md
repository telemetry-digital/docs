---
title: Alarms, notifications and incidents
slug: alarms
sidebar_position: 2
tags: [alarms, notifications, incidents]
---

## Alarm rules

An alarm rule watches a datastream — for example a limit — and **raises** an alarm
when it is met and **clears** it when the value returns to normal. Operators **acknowledge** alarms (permission
`alarm.ack`). Every raise, escalation, acknowledgement and clearing is recorded.

Alarms found in data replayed later from a device's offline buffer are flagged as **retrospective**, so you know they
were not seen live.

Alarm rules are listed under *Assets → Alarm rules* with their type (high, low, loss of data), the warning and action
limits, a delay, a hysteresis and the version. A new version of a rule of the same type on the same datastream
replaces the old one.

![Assets → Alarm rules: rules per asset and datastream with type, warning and action limits, delay, hysteresis, version and since when](img/alarm-rules.webp)

![The New alarm rule dialog with datastream, type, warning limit, action limit, delay, hysteresis, escalation policy and a reason](img/new-alarm-rule.webp)

## Escalation and notification channels

An **escalation policy** decides who is told, how, and what happens when nobody reacts. Notification channels:

- **E-mail** (configure `[smtp]` in `config.toml`)
- **Webhooks**, signed with HMAC so the receiver can check they came from your server
- **SMS** and **voice** through HTTP gateways
- **Web Push** to browsers and installed apps

Every delivery is recorded, so you can show who was notified and when. An alarm whose notification could not be
delivered opens an incident — a failed notification is never silent.

Channels, escalation policies and the delivery log are under *Settings → Notifications*. An escalation policy is a
list of steps in minutes: step 0 at once, later steps only while the alarm is still active and unacknowledged.

![Settings → Notifications: channels with kind, target and state; an escalation policy with steps 0 min → e-mail and 15 min → webhook; and the delivery log with the state of every delivery](img/notifications.webp)

![The New channel dialog with name, kind E-mail, recipients one per line and a reason](img/new-channel.webp)

Each user switches Web Push on for their own browser under *My account → Notifications*.

![My account → Notifications with the button Enable in this browser and the list of registered browsers](img/web-push.webp)

## Automation rules

Simple automation rules — **condition → action** with an expression language, including conditions held for a while
and "no data for N seconds" — notify, call a webhook, send a command,
raise an alarm or store a derived value. For anything larger use [flows](flows.md); a rule converts into a flow.

They are under *Assets → Automation*, with a run log of what each rule did.

![Assets → Automation: rules with their state, condition, actions and last run, and the run log below](img/automation-rules.webp)

![The New automation rule dialog: template, name, description, inputs, condition with hold time and cooldown, actions on enter, actions on exit and a reason](img/new-automation-rule.webp)

## Incidents

The **Incidents** page is the log of what went wrong: impact, cause and **corrective and preventive action (CAPA)**.

An incident goes from *open* to *investigating*, *resolved* and *closed*. Resolving needs the cause; closing needs the
cause and the CAPA. Everything is kept as history and audited.

![The Incidents page: incidents with the time opened, kind, severity, status, title and organization](img/incidents.webp)

*The incident log.*

![An incident opened: kind, severity, status, reason, impact, cause, CAPA, a note, a reason for the change, the buttons Save fields and note, Start investigating, Mark resolved and Close, and the history](img/incident-detail.webp)

Incidents open **automatically** for:

- alarms whose notifications were not delivered;
- a broken audit chain;
- cameras that stop recording, displays that stop showing and a nearly full video disk (see
  [Video settings](../video-nvr/settings.md));
- MQTT bridges and accounts disconnected too long;
- flows, through their *Incident* node.

Administrators are notified of system incidents by e-mail and Web Push. You can also open an incident by hand.

![The New incident dialog with title, severity, impact, cause, CAPA and a reason](img/new-incident.webp)
