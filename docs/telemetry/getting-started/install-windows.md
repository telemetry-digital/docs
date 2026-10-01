---
title: Install on Windows
slug: install-windows
sidebar_position: 2
tags: [installation, windows]
---

The Windows installer sets up the server on Windows 10 or later and on Windows Server. It brings its own PostgreSQL 18
and Typst (for PDF reports) and installs everything as Windows services.

## Install

Open **PowerShell as administrator** and run:

```powershell
Set-ExecutionPolicy -Scope Process Bypass -Force
irm https://telemetry.digital/install.ps1 -OutFile install.ps1; .\install.ps1
```

This installs an **intranet** server: web on port 8080, MQTT on port 1883, with the firewall ports opened.

## Common variants

Your organization's name and the ports:

```powershell
.\install.ps1 -OrgName "Acme Ltd" -PgPort 5432 -HttpPort 8080
```

Internet profile with your own certificate (on Windows the server terminates HTTPS itself, there is no reverse
proxy):

```powershell
.\install.ps1 -Domain telemetry.example.com -TlsCert C:\certs\fullchain.pem -TlsKey C:\certs\privkey.pem
```

Reachable from anywhere through a Cloudflare Tunnel — no open ports, no certificate, no public address (see
[Cloudflare Tunnel](cloudflare-tunnel.md)):

```powershell
.\install.ps1 -Domain cameras.example.com -CloudflareToken <token>
```

## What the installer does

1. Creates `C:\Program Files\ctrl32-telemetry` (program, Typst, PostgreSQL) and `C:\ProgramData\ctrl32-telemetry`
   (configuration, data, logs, backups); the data directory is limited to SYSTEM and Administrators.
2. Installs PostgreSQL 18 as the service `ctrl32-telemetry-postgres` (or uses an existing server given with
   `-PgHost`) and creates the database with random passwords.
3. Writes `config.toml` (secrets are kept when you run it again).
4. Registers and starts the Windows service `ctrl32-telemetry` and opens the firewall ports.
5. Creates the first administrator; the one-time password is saved in `admin-bootstrap.txt` in
   `C:\ProgramData\ctrl32-telemetry`.

## Other switches

| Switch | Meaning |
|---|---|
| `-DryRun` | print the plan only, change nothing |
| `-NoStart` | install but do not start |
| `-Uninstall` | remove the services and firewall rules; the data stays |
| `-Uninstall -Purge` | also delete the data |

The installer is idempotent: run it again to repair or update an installation.

Next: [First sign-in](first-sign-in.md).
