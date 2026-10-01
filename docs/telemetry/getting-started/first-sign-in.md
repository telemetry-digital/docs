---
title: First sign-in
slug: first-sign-in
sidebar_position: 3
tags: [getting-started, sign-in, security]
---

The installer creates one administrator, `admin`, with a **one-time password**.

## Sign in

1. Open the server's address in a browser: `https://<your domain>` or, in the intranet, `http://<server>:8080`.
2. Find the one-time password:
    - Linux: `/etc/ctrl32-telemetry/admin-bootstrap.txt`
    - Windows: `C:\ProgramData\ctrl32-telemetry\admin-bootstrap.txt`
3. Sign in as `admin` with that password and choose your own password when asked.
4. Set up **two-factor sign-in** with an authenticator app (TOTP). Keep the recovery codes somewhere safe.
5. **Delete `admin-bootstrap.txt`** from the server.

!!! warning "Two-factor sign-in is required for administrators"
    Administrators, engineers and quality reviewers must use two-factor sign-in by default. On a server in the
    internet profile (with a public domain or a Cloudflare Tunnel) every user must.

## Look around

- The **menu** on the left (a drawer on phones) holds Dashboards, Home, Energy, Cameras, Displays, Flows, Devices,
  Incidents, Assets, Firmware, Users, Settings and System — you see only what your permissions allow.
- **My account** holds your password and two-factor settings, API tokens, connected AI assistants and the language.
  The interface is available in 19 languages.
- Before anyone signs in, the address `/` shows the sign-in page with a short overview of the system, or your own
  front page once you publish one (see [Front page](../administration/front-page.md)).

## Set up e-mail

Password resets, account requests and alarm notifications go out by e-mail. Fill in the `[smtp]` section of
`config.toml` and restart the service; until then e-mail notifications cannot be delivered.

Next: [Organization and users](organization-and-users.md).
