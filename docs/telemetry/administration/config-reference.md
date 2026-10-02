---
title: Configuration file reference (config.toml)
slug: admin-config-toml
sidebar_position: 11
tags: [administration, configuration, config-toml, reference]
---

The server reads one configuration file, `config.toml`. This page lists every key it understands, with its type,
built-in default and meaning.

## Where the file is and how it is read

| Platform | Default path |
|---|---|
| Linux | `/etc/ctrl32-telemetry/config.toml` |
| Windows | `%ProgramData%\ctrl32-telemetry\config.toml` (usually `C:\ProgramData\ctrl32-telemetry\config.toml`) |

Every command accepts `-config <path>` to read another file.

The server builds its configuration in three steps:

1. the built-in defaults listed below;
2. the file, if it exists (a missing file is not an error, an unreadable one is);
3. the environment variables listed under *Environment variables* below.

Then it checks the result. If a check fails, the server does not start and prints the reason.

The installers write the file for you, including the secret keys. The Server page shows the values in use (read-only)
under *System → Server*. Some pages of *System* change keys for you: see *Keys changed by the System
pages* below.

!!! warning "The file holds secrets"
    `config.toml` contains the database passwords and the keys that protect password hashes, authenticator seeds and
    connector credentials. Keep it readable only by the service account, and keep a copy with your backups (a backup
    made on the *Backups* page already includes it).

Durations are written as strings such as `"30s"`, `"1m"` or `"15m"`.

## profile

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `profile` | string | `"intranet"` | `"intranet"` or `"internet"`; any other value stops the server |

The **internet** profile enforces secure defaults:

- HTTP needs `http.tls_cert` and `http.tls_key`, **or** `http.listen` bound to a loopback address (`127.…`,
  `localhost:…`, `[::1]:…`) behind a reverse proxy that terminates TLS;
- a plain MQTT listener (`mqtt.listen`) is allowed only on a loopback address — use `mqtt.tls_listen`;
- `mqtt.tls_listen` needs a certificate (`mqtt.tls_cert`/`tls_key` or `http.tls_cert`/`tls_key`);
- `video.push_listen` needs TLS;
- two-factor sign-in is required for **every** user.

The **intranet** profile allows plain HTTP and MQTT and requires two-factor sign-in only for the roles named by the
security policy (see [Users and permissions](users-and-permissions.md)).

## [http]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `listen` | string | `":8080"` | address and port of the web server and API |
| `tls_cert` | path | — | PEM certificate chain; empty = plain HTTP |
| `tls_key` | path | — | PEM private key of `tls_cert` |
| `base_url` | URL | `"http://localhost:8080"` | the public address of the server, used in links, e-mails, firmware download URLs, the MCP address and the licence host name |
| `public_read` | bool | `false` | demonstration only: allows read requests (`GET`) that need only `data.read` without signing in |

!!! danger "public_read"
    With `public_read = true` anyone who can reach the server reads measurements, devices and alarms without an
    account. The Server page marks it as *enabled (demo)*. Never enable it on a production server.

## [mqtt]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `listen` | string | `":1883"` | plain MQTT over TCP; empty = off; in the internet profile only on a loopback address |
| `tls_listen` | string | — | MQTT over TLS, for example `":8883"`; empty = off |
| `ws_listen` | string | `":1884"` | MQTT over WebSocket, used by the web interface |
| `vpn_listen` | string | — | an extra plain listener inside the WireGuard network, for example `"10.66.0.1:1883"`; binding is retried until the VPN interface is up |
| `tls_cert` | path | — | separate certificate for MQTT; empty = `http.tls_cert` |
| `tls_key` | path | — | key of `mqtt.tls_cert`; empty = `http.tls_key` |

## [db]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `url` | URL | a local development database | the application's connection (role `ctrl32_app`); required |
| `admin_url` | URL | same as `url` | the schema owner's connection, used by `migrate`, backups, replicas and `admin purge` |
| `app_password` | string | — | when set, `migrate` sets this password on the `ctrl32_app` role |

The application role can only read and append records; deleting records needs `admin_url` (see
[Audit trail](audit.md)).

## [smtp]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `host` | string | — | SMTP server; empty = no e-mail (password resets, e-mail channels and incident e-mails do not work) |
| `port` | int | `587` | SMTP port |
| `user` | string | — | SMTP user name |
| `password` | string | — | SMTP password |
| `from` | string | — | sender address |
| `to` | list of strings | — | the server's global alarm recipients: used by alarm rules that have no escalation policy |

## [log]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `level` | string | `"info"` | `debug`, `info`, `warn` or `error` |
| `file` | path | — | write the JSON log to this file as well; empty = standard output only; the server does not rotate it |
| `syslog` | URL | — | `udp://host:514` or `tcp://host:514`: forward the JSON log as RFC 5424 syslog; empty = off |

## [gap]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `check_interval` | duration | `"1m"` | how often missing data (gaps) are looked for; zero or negative falls back to 1 minute |

## [time]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `ntp_server` | string | `"pool.ntp.org"` | `host` or `host:port` of an NTP server to check the clock against; `""` = no check |
| `check_interval` | duration | `"15m"` | how often the clock is checked (also at start) |
| `max_offset` | duration | `"1s"` | a larger difference is a warning on the Server page, in `/healthz` and `/metrics` |

On an intranet without internet access, point `ntp_server` at your own time server.

## [security]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `pepper` | string | — | secret mixed into every password hash; required, at least 16 characters |
| `secret_key` | string | — | key for encrypting authenticator seeds, connector credentials and other stored secrets; required, at least 16 characters |
| `contact` | string | — | `mailto:` or `https:` address published in `/.well-known/security.txt` |

The installers generate both secrets. The server does not start without them.

!!! danger "Never change the secrets of a running installation"
    Changing `pepper` makes every stored password unusable. Changing `secret_key` makes the stored authenticator
    seeds and encrypted settings unreadable. Restore them from the backup instead.

## [report]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `typst_path` | path | — | the Typst program that renders PDF reports; empty = `typst` on the `PATH` or next to the server program |
| `storage_dir` | path | Linux `/var/lib/ctrl32-telemetry/reports`, Windows `%ProgramData%\ctrl32-telemetry\reports` | where generated PDF reports are kept; they are records checked against their hash on every download |

Without Typst the server runs, but PDF reports and PDF exports answer *unavailable*.

## [fota]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `storage_dir` | path | `firmware` next to the reports directory | uploaded firmware images |
| `max_size_mb` | int | `16` | largest firmware image that can be uploaded, in MB; zero or less falls back to 16 |

## [video]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `storage_dir` | path | `video` next to the reports directory | camera recordings |
| `max_disk_gb` | int | `50` | default disk space for recordings; the oldest recordings are deleted beyond it (*Cameras → Settings* can change the value in use) |
| `push_listen` | string | — | port where camera relays at remote sites deliver video, for example `":8322"`; empty = off |
| `push_tls_cert` | path | `http.tls_cert` | certificate for `push_listen`; the internet profile requires TLS |
| `push_tls_key` | path | `http.tls_key` | key of `push_tls_cert` |
| `push_host` | string | host of `http.base_url` | host name the relays connect to |

See [Recording and storage](../video-nvr/recording.md) and [Remote sites](../video-nvr/remote-sites.md).

## [license]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `activation_url` | URL | `"https://lic.ctrl32.com/telemetry/activate"` | the licence service used by *Activate with the order number*; must be `https://` (`http://` is accepted only for `127.0.0.1` or `localhost`) |
| `buy_url` | URL | `"https://ctrl32.com/pricing/"` | where the *Buy* links point; must be an `http(s)` address |

## [agent]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `socket` | path | — | socket of the server agent; empty = no agent, the System pages show read-only information |
| `pg_dump` | path | the PostgreSQL bundled next to the program | Windows: path to `pg_dump.exe` for backups |
| `pg_container` | string | `"ctrl32-telemetry-postgres"` | Linux: the Docker container of PostgreSQL used for backups |
| `update_url` | URL | `"https://telemetry.digital/dl"` | where the agent downloads updates and their `SHA256SUMS` |
| `vpn_simulate` | path | — | development and tests only: the agent renders the VPN configuration and firewall rules into this directory and applies nothing to the host; every other agent operation is refused |

When the agent itself starts and `socket` is empty, it listens on `/run/ctrl32-telemetry/agent.sock` (Linux) or
`%ProgramData%\ctrl32-telemetry\agent.sock` (Windows). The installers write the socket into the file so that the web
service finds the agent.

## [map]

| Key | Type | Default | Meaning |
|---|:---:|:---:|---|
| `pmtiles` | path | — | a raster PMTiles v3 archive (PNG, JPEG or WebP tiles) served by this server at `/tiles/{z}/{x}/{y}.png`; the file must exist or the server does not start |
| `attribution` | string | the archive's own attribution | text shown on the maps |

The archive is used whenever an organization has no tile address of its own under *Settings → Branding and theme*, so
maps work without the internet.

## Environment variables

Only these variables override the file. Other keys can be set only in the file.

| Variable | Key |
|---|---|
| `CTRL32_PROFILE` | `profile` |
| `CTRL32_HTTP_LISTEN` | `http.listen` |
| `CTRL32_HTTP_TLS_CERT` | `http.tls_cert` |
| `CTRL32_HTTP_TLS_KEY` | `http.tls_key` |
| `CTRL32_HTTP_BASE_URL` | `http.base_url` |
| `CTRL32_HTTP_PUBLIC_READ` | `http.public_read` (`true` or `false`) |
| `CTRL32_MQTT_LISTEN` | `mqtt.listen` |
| `CTRL32_MQTT_TLS_LISTEN` | `mqtt.tls_listen` |
| `CTRL32_MQTT_WS_LISTEN` | `mqtt.ws_listen` |
| `CTRL32_DB_URL` | `db.url` |
| `CTRL32_DB_ADMIN_URL` | `db.admin_url` |
| `CTRL32_DB_APP_PASSWORD` | `db.app_password` |
| `CTRL32_SMTP_HOST` | `smtp.host` |
| `CTRL32_SMTP_PORT` | `smtp.port` |
| `CTRL32_SMTP_USER` | `smtp.user` |
| `CTRL32_SMTP_PASSWORD` | `smtp.password` |
| `CTRL32_SMTP_FROM` | `smtp.from` |
| `CTRL32_SMTP_TO` | `smtp.to` (comma separated) |
| `CTRL32_LOG_LEVEL` | `log.level` |
| `CTRL32_LOG_FILE` | `log.file` |
| `CTRL32_SECURITY_PEPPER` | `security.pepper` |
| `CTRL32_SECURITY_SECRET_KEY` | `security.secret_key` |
| `CTRL32_REPORT_TYPST_PATH` | `report.typst_path` |
| `CTRL32_REPORT_STORAGE_DIR` | `report.storage_dir` |
| `CTRL32_LICENSE_ACTIVATION_URL` | `license.activation_url` |

## Keys changed by the System pages

The server agent edits the file for you in two places; when a value changes, the service restarts by itself to apply it.

| Page | Key | Value written |
|---|---|---|
| Domain and TLS → Apply | `http.base_url` | `https://<domain>` |
| VPN → Server settings → Apply | `mqtt.vpn_listen` | `<server VPN address>:1883` when enabled, empty when disabled |

## Example

```toml
profile = "internet"

[http]
listen = "127.0.0.1:8080"
base_url = "https://telemetry.example.com"

[mqtt]
listen = "127.0.0.1:1883"
tls_listen = ":8883"
ws_listen = ":1884"

[db]
url = "postgres://ctrl32_app:…@localhost:5433/ctrl32?sslmode=disable"
admin_url = "postgres://ctrl32:…@localhost:5433/ctrl32?sslmode=disable"

[smtp]
host = "smtp.example.com"
port = 587
from = "telemetry@example.com"

[security]
pepper = "…generated…"
secret_key = "…generated…"
contact = "mailto:security@example.com"

[video]
max_disk_gb = 500
```
