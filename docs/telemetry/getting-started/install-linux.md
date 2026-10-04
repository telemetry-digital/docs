---
title: Install on Linux
slug: install-linux
sidebar_position: 1
tags: [installation, linux]
---

One command installs the server on Debian, Ubuntu or Raspberry Pi OS (64-bit). PostgreSQL 18 and the Caddy web server
run in Docker by default; the server itself runs natively as a systemd service.

## With your domain and automatic HTTPS

Point a DNS record of your domain at the server first, then run:

```bash
curl -fsSL https://portal.telemetry.digital/install.sh | sudo bash -s -- --domain telemetry.example.com
```

With `--domain` the installer runs Caddy for automatic HTTPS certificates, uses the same certificate for MQTT over
TLS on port 8883, and switches the server to the **internet** profile (TLS everywhere, two-factor sign-in for
everyone).

## Without a domain (intranet)

```bash
curl -fsSL https://portal.telemetry.digital/install.sh | sudo bash
```

The server runs in the **intranet** profile and serves the web interface on port 8080. Use this only inside your own
network or VPN. The installer binds MQTT to the machine itself (`127.0.0.1:1883`); to accept devices from your
network, change the `[mqtt]` section of `config.toml` (or give the server a domain and use MQTT over TLS on 8883).

## What the installer does

1. Creates a system user and the directories.
2. Downloads the single program.
3. Starts PostgreSQL 18.
4. Writes `/etc/ctrl32-telemetry/config.toml` with random passwords (kept when you run it again).
5. Prepares the database, installs and starts the systemd service.
6. With `--domain`: runs Caddy for HTTPS.
7. Creates the first administrator and saves the one-time password in `/etc/ctrl32-telemetry/admin-bootstrap.txt`.

The installer is **idempotent**: running it again repairs or updates an installation and keeps your configuration
and passwords.

When the installer finishes, the server's address shows the sign-in page:

![The sign-in page with user name and password, the links Forgot password and Request an account, and a short overview of the system](img/sign-in.webp)

## Options

| Option | Meaning |
|---|---|
| `--domain NAME` | public host name: profile internet, Caddy with HTTPS, MQTT over TLS on 8883 |
| `--cloudflare-token T` | reach the server through a Cloudflare Tunnel instead of Caddy (needs `--domain`, the tunnel's host name); see [Cloudflare Tunnel](cloudflare-tunnel.md) |
| `--demo` | add a demo organization with sample data and print a device token |
| `--db docker` or `--db apt` | how PostgreSQL is installed (default: Docker if available, otherwise packages) |
| `--db-port N` | host port of the Docker PostgreSQL (default 5432, or 5433 when 5432 is taken) |
| `--no-start` | install everything but do not start the service |
| `--analytics` | also install the detector for [counting people and vehicles with cameras](../object-counting/cameras.md#install-the-detector): ffmpeg (LGPL build), ONNX Runtime and the YOLOX models, downloaded from their official releases and checked by SHA-256; off by default |
| `--ffmpeg apt` | with `--analytics`: use the distribution's ffmpeg package (a GPL build) instead of the LGPL build |

Example with demo data:

```bash
curl -fsSL https://portal.telemetry.digital/install.sh | sudo bash -s -- --domain telemetry.example.com --demo
```

## Add object counting later

On a server that is already installed, `--analytics` adds only the detector for object counting on cameras and
restarts the service; the configuration, data and passwords stay:

```bash
curl -fsSL https://portal.telemetry.digital/install.sh | sudo bash -s -- --analytics
```

It ends with the self-check of the detector (ffmpeg, ONNX Runtime and the models with their versions) and tells you
how to switch counting on for a camera. See [Install the detector](../object-counting/cameras.md#install-the-detector).

## Installation without the Internet

On a server without Internet access, use the **offline package** of the release,
`ctrl32-telemetry-<version>-offline.zip`, from the download page (on a machine with access):

1. Copy the package to the server and unpack the program for its processor, for example
   `unzip ctrl32-telemetry-0.76.0-offline.zip ctrl32-telemetry-linux-amd64 SHA256SUMS install.sh`.
2. Check it: `sha256sum -c --ignore-missing SHA256SUMS`. A machine that already runs ctrl32 telemetry can check the
   signature of the whole package as well: `ctrl32-telemetry update verify --file ctrl32-telemetry-0.76.0-offline.zip`.
3. Install PostgreSQL 18 from your local package source, then run
   `sudo bash install.sh --binary ./ctrl32-telemetry-linux-amd64 --db apt`.

Later updates come from the same kind of package — uploaded in *System → Updates* or with
`ctrl32-telemetry update --file …` — or from a mirror in your network. The detector of object counting takes its parts
from the package too. See [Updates without the Internet](../administration/server.md#updates-without-the-internet).

## Configuration

All settings are in one file, `/etc/ctrl32-telemetry/config.toml`. Every key can also be set with an environment
variable `CTRL32_<SECTION>_<KEY>`. After changing the file, restart the service (or use the Server page, see
[Server, backups and updates](../administration/server.md)).

Next: [First sign-in](first-sign-in.md).
