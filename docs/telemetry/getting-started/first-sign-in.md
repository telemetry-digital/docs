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

![The sign-in page with user name and password, the links Forgot password and Request an account, and a short overview of the system](img/sign-in.webp)

*The sign-in page, with a short overview of the system next to the form.*

The password and two-factor sign-in are under *My account → Password and MFA*: *Set up authenticator* starts the
registration of the authenticator app.

![The Password and MFA page: a form to change the password and the Two-factor authentication box with the button Set up authenticator](img/password-and-mfa.webp)

!!! warning "Two-factor sign-in is required for administrators"
    Administrators, engineers and quality reviewers must use two-factor sign-in by default. On a server in the
    internet profile (with a public domain or a Cloudflare Tunnel) every user must.

## Look around

- The **menu** on the left (a drawer on phones) holds Dashboards, Home, Energy, Cameras, Displays, Flows, Devices,
  Incidents, Assets, Firmware, Users, Audit, Settings and System — you see only what your permissions allow.
- **My account** holds your password and two-factor settings, API tokens, connected AI assistants and the language.
  The interface is available in 19 languages.
- Before anyone signs in, the address `/` shows the sign-in page with a short overview of the system, or your own
  front page once you publish one (see [Front page](../administration/front-page.md)).

*Collapse menu* at the bottom of the menu shrinks it to a bar of icons:

![The application with the menu collapsed to a narrow bar of icons next to a production dashboard](img/menu-collapsed.webp)

On a phone the menu opens as a drawer from the button at the top left:

![The menu opened as a drawer on a phone, with Dashboard, Home, Energy, Cameras with Live view, Video walls and Camera management, Displays, Flows, Devices, Incidents and Assets](img/phone-menu.webp)

## Set up e-mail

Password resets, account requests and alarm notifications go out by e-mail. Fill in the `[smtp]` section of
`config.toml` and restart the service; until then e-mail notifications cannot be delivered.

*Forgot password?* on the sign-in page sends a reset link that is valid for one hour — when the account has an
e-mail address and the server can send mail.

![The Forgot password page: a field for the user name or e-mail and the button Send reset link](img/forgot-password.webp)

Next: [Organization and users](organization-and-users.md).
