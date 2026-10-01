---
title: Getting started
slug: getting-started
sidebar_position: 1
tags: [installation, getting-started]
---

telemetry.digital is one program plus a PostgreSQL database. The installers put both on your machine, start them as
services and create the first administrator. Count on a few minutes for the whole thing.

## What you need

- **Linux**: Debian, Ubuntu or Raspberry Pi OS (64-bit) with systemd, and root access.
- **Windows**: Windows 10 or later, or Windows Server, and an elevated PowerShell.
- For access from the internet either a **domain name** pointing at the server, or a **Cloudflare Tunnel** when the
  machine has no public address (no open ports needed).

## Steps

1. Install: [on Linux](install-linux.md) or [on Windows](install-windows.md).
2. [Sign in for the first time](first-sign-in.md) with the one-time password the installer saved, and set up
   two-factor sign-in.
3. [Set up your organization and users](organization-and-users.md).
4. If the server sits at a site without a public address, [connect it through a Cloudflare Tunnel](cloudflare-tunnel.md).

Then continue with what you came for: [cameras](../video-nvr/index.md), [devices](../devices/index.md) or
[dashboards](../dashboards/index.md).

!!! tip "Intranet or internet"
    Without a domain the server runs in the **intranet** profile: plain HTTP (port 8080) and plain MQTT (port 1883)
    are allowed, TLS is optional. With a public host name it runs in the **internet** profile: TLS everywhere and
    two-factor sign-in for every user.
