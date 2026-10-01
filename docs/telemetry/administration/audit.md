---
title: Audit trail
slug: audit-trail
sidebar_position: 2
tags: [administration, audit, records]
---

telemetry.digital keeps records you can prove.

## What is recorded

- **Every change** — of devices, datastreams, rules, flows, dashboards, cameras, users, settings — with who made it,
  when, the old and new values where it matters, and the **reason**. A change without a reason is refused.
- **Sensitive reads** — every video playback and export, unmasked viewing, listening to sound, data exports.
- **Commands** to devices, approvals and rejections; actions of flows with the flow, its version and node as origin;
  actions of AI assistants marked "via MCP".
- **Sign-ins** in the access log.

## Why you can trust it

- Measurements, raw messages, the audit trail and other records are **append-only**: the application's database role
  cannot change or delete them.
- The audit trail is **hash-chained**: each entry includes the hash of the previous one, so a changed or removed entry
  breaks the chain.
- `ctrl32-telemetry audit verify` checks the chain on the server. A broken chain is also detected when the server
  starts, and opens an incident.
- Objects such as assets, datastreams, screens, rules and profiles are **versioned**: older versions stay readable.

## Reading and exporting

Reading the audit trail needs `audit.read` (administrators and quality reviewers). The audit trail and the access log
export to CSV. Quality reviewers with `audit.review` record their reviews of the audit trail.

## Deleting data

Data is not deleted by editing it. Removing a site, a device, a dashboard or reports is a separate, audited purge
operation done by an administrator; recordings of cameras follow their retention.

!!! note "Not a validated system by itself"
    These features support work under rules such as GxP, but telemetry.digital is not a validated system by itself.
    Validating your use of it stays with you.
