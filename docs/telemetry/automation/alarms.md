---
title: Alarms
slug: alarms
sidebar_position: 8
tags: [alarms, alarm-rules, limits, acknowledgement]
---

An **alarm rule** watches a datastream: it **raises** an alarm when a value crosses a limit and **clears** it when
the value is back in range. Who is told, and how, is decided by the rule's
[escalation policy](notifications.md). Every raise, escalation, notification, acknowledgement and clearing is
written to the append-only alarm log.

## Alarm rules

*Assets → Alarm rules* lists the current version of every alarm rule: asset, datastream, type, warning and action
limit, delay, hysteresis, version (hover for the reason) and since when it applies. Click a row to open the
datastream, where you can save a new version or disable the rule.

![Assets → Alarm rules: rules per asset and datastream with type, warning and action limits, delay, hysteresis, version and since when](img/alarm-rules.webp)

Reading rules needs `data.read`; **New rule**, new versions and disabling need `config.write`.

### New alarm rule

![The New alarm rule dialog with datastream, type, warning limit, action limit, delay, hysteresis, escalation policy and a reason](img/new-alarm-rule.webp)

| Field | Type | Default | Allowed | Notes |
|---|---|:---:|:---:|---|
| Datastream | datastream | — | a datastream of your organization | required |
| Type | choice | high | high, low, comm_loss, sensor_fault, low_battery, power_loss, door_open | see *Rule types* |
| Warning limit | number | — | any number | *high* and *low* need a warning or an action limit, or both |
| Action limit | number | — | any number | the stronger limit |
| Delay (s) | number | 0 | 0–604800 | stored with the rule; see the warning below |
| Hysteresis | number | 0 | 0 or more | how far the value must come back before the alarm clears |
| Escalation policy | choice | (none: global e-mail) | a policy of *Settings → Notifications* | who is told and when |
| Reason (audit trail) | text | — | up to 200 characters | required |

A rule is **versioned**: saving a rule of the same type on the same datastream closes the current version and
starts the next one (*A rule of the same type on the same datastream is superseded by the new version*). Old
versions stay in the database; alarms keep a link to the version that raised them.

In the datastream's detail, **New rule version** offers type, warning and action limit, delay, hysteresis and a
reason, and **Disable** closes the current version with a reason — the rule stops applying.

!!! warning "Keep the escalation policy"
    The *New rule version* form in the datastream's detail has no escalation-policy field: a version saved there has
    no policy and falls back to the global e-mail recipients. To keep or change the policy, save the new version with
    **New rule** on the *Alarm rules* page.

### Four eyes for alarm limits

With *Four eyes for alarm limits* on (*Settings → Security policy*), creating a rule version or disabling a rule is
not executed: it becomes a change request (*change request recorded — waiting for approval by a second person*).
Another user with `config.write` approves or rejects it under *Settings → Approvals*; the approval replays the
original request under the approver's name, and both names go to the audit trail. Requests expire after 7 days.

### Rule types

| Type | Raises when | Clears when |
|---|---|---|
| high | the value reaches the warning limit (≥) — severity *warning* — or the action limit (≥) — severity *action* | the value falls below the warning limit minus the hysteresis (the action limit when there is no warning limit) |
| low | the value reaches the warning limit (≤) or the action limit (≤) | the value rises above the warning limit plus the hysteresis (the action limit when there is no warning limit) |
| comm_loss, sensor_fault, low_battery, power_loss, door_open | — | — |

!!! warning "What is evaluated today"
    Only **high** and **low** rules raise alarms. The other types can be saved and are listed, but no alarm is raised
    from them yet. The **delay** is stored but not yet applied: a high or low rule raises the alarm on the first value
    that crosses the limit. For "above the limit for N minutes", use an [automation rule](automation-rules.md) with
    a hold time or a flow with a *True for* node. For missing data, use an automation rule with a *stale* condition.

How a high rule with warning 8, action 10 and hysteresis 0.5 behaves:

| Value | Result |
|---|---|
| 7.9 | nothing |
| 8.2 | alarm raised, severity *warning* |
| 10.1 | the same alarm rises to *action* (event *escalated*) |
| 8.4 | the alarm stays at *action* — severity never goes back down |
| 7.6 | still active: not yet below 8 − 0.5 |
| 7.4 | alarm cleared |

- Only numeric values are evaluated; a value without a number is skipped.
- One rule has at most one alarm that is not cleared. A new alarm is raised only after the previous one cleared.
- Rule changes take effect within 30 seconds.
- An alarm found in data that a device sends late from its offline buffer is flagged **retrospective**, so you know
  it was not seen live.

## Alarm states

| State | Meaning |
|---|---|
| active | raised and not yet acknowledged or cleared |
| acknowledged | someone confirmed they know about it; it stays until the value clears it |
| cleared | the value is back in range (or, for alarms of automation rules, the rule's condition ended) |

| Severity | Raised by |
|---|---|
| warning | alarm rules (warning limit) and automation rules |
| action | alarm rules (action limit) and automation rules |
| critical | automation rules |

### The alarm log

Every alarm has an append-only history:

| Event | Written when |
|---|---|
| raised | the alarm was raised, with its severity and value |
| retrospective | the value came from late data |
| escalated | the severity rose from warning to action |
| notified | a notification was sent (channel, kind and event) |
| delivery_failed | a notification could not be sent (with the error) |
| acknowledged | someone acknowledged it, with name and reason |
| cleared | the alarm cleared, with the value |

The *Alarm log PDF* (permission `data.export`) and the export `GET /api/v1/export/alarms` contain the alarms with
their history. The **Alarms** widget on a dashboard lists active alarms first — see
[Widgets](../dashboards/widgets.md).

## Acknowledging an alarm

Acknowledging confirms that a person knows about an active alarm. It:

- needs the permission `alarm.ack` and a **reason** of at least 3 characters,
- works only on an *active* alarm,
- stops the escalation: later steps of the escalation policy are cancelled,
- is written to the alarm log with your name and reason and to the audit trail as `alarm.acknowledge`.

In this version you acknowledge through the API: `POST /api/v1/alarms/{id}/ack` with `{"reason": "…"}`. The web
interface shows the state but has no acknowledge button.

There is no shelving or suppression of alarms in this version: to silence a rule, disable it (with a reason), and
save it again when the cause is fixed.

## Who is told

| The rule has | Raised alarm | Cleared alarm |
|---|---|---|
| an escalation policy | the policy's steps — see [Notifications and escalation](notifications.md) | a notice to the step-0 channels |
| no policy | one e-mail to the server's global recipients (configured by the server administrator) | nothing |

When the global e-mail is not configured, the alarm log records that the notification was skipped. A failed global
e-mail is recorded as *delivery_failed*; only failed steps of an escalation policy open an
[incident](incidents.md).

Flows can react to alarms too: the [Alarm trigger](flow-nodes-triggers.md) starts a message when an alarm is raised
or cleared.
