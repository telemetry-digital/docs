---
title: Administration
slug: administration
sidebar_position: 8
tags: [administration]
---

What an administrator of a telemetry.digital server takes care of.

## Pages in this section

1. [Users and permissions](users-and-permissions.md) — accounts, roles, camera access, two-factor sign-in, sign-in
   rules, four eyes.
2. [Audit trail](audit.md) — the hash-chained record of every change, with its reason.
3. [White labeling and branding](white-labeling.md) — your logo and colours; your product name and footer (requires
   a licence).
4. [Server, backups and updates](server.md) — the System pages at a glance, backups, updates, a second database
   server.
5. [Apps for phones and tablets (PWA)](pwa-apps.md) — installable apps with their own start page and menu.
6. [Front page](front-page.md) — the public page visitors see before signing in.
7. [Remote management of PCs](remote-management.md) — remote desktop and terminal of the PCs at customers' sites.

## Reference

- [Settings pages](settings-reference.md) — every field of Branding and theme, Report templates, Stored reports,
  Connectors, Languages, Translations, Apps, AI assistants, Organization export, Notifications, Approvals and Security
  policy.
- [System pages](system-reference.md) — every field of Organizations, Server, Domain and TLS, WireGuard, Backups,
  Redundant database, Updates and License, and the command-line administration.
- [My account](my-account.md) — profile, password and two-factor sign-in, API tokens, browser notifications, AI
  assistants.
- [Configuration file](config-reference.md) — every key of `config.toml` with its type, default and meaning, and the
  environment variables.
- [Permissions](permissions-reference.md) — every permission, which built-in role has it and what it unlocks.

## Who sees which menu

| Menu | Needed permission |
|---|---|
| Users | `user.admin` |
| Settings | `content.write` |
| System | `system.admin` |
| My account | none |

## The rule behind all of it

Every configuration change asks for a **reason**, and the reason is stored with the change in the audit trail.

![A dialog asking for the reason of a change before it is saved](img/change-reason.webp)

*Every save asks for a reason for the audit trail.*

Sensitive features — camera sound, unmasked video, evidence, AI access — are off by default, need their own
permission and are audited.
