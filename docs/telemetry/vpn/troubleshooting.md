---
title: VPN security, upgrade and troubleshooting
slug: vpn-troubleshooting
sidebar_position: 5
tags: [vpn, security, audit, upgrade, troubleshooting]
---

## Security

### Who may do what

| Action | Needs |
|---|---|
| See the VPN page | `system.admin` (everything) or `vpn.manage` (the own organization) |
| Server settings, firewall rules, apply again, peers and rules of the whole server | `system.admin` and a reason |
| Edit, disable, enable and remove peers, access rules, access grants of the own organization | `vpn.manage` and a reason |
| Add a peer, rotate a key (a configuration with a private key is shown) | `system.admin` or `vpn.manage`, a reason, your password and — with two-factor sign-in — the authenticator code |
| Approve a technician's access under four eyes | a different user with `vpn.manage` or `system.admin`, and a reason |
| Connect this server to a hub (uplink), replace its configuration | `system.admin`, a reason, your password and — with two-factor sign-in — the authenticator code |
| Uplink rules, the uplink's transport, disconnecting | `system.admin` and a reason |

AI assistants are never offered adding a peer, rotating a key, granting access or connecting an uplink.

### What a peer cannot reach

| On the server | From the VPN |
|---|---|
| The database | dropped — also through Docker's internal network |
| The web interface | only with a rule |
| MQTT (plain inside the VPN, or with TLS) | only with a rule |
| SSH of the host | only with a rule |
| The camera relay port | only with a rule |
| The server agent | not on the network at all |
| Other peers and site networks | only with a rule |

### Secrets

- Peer private keys are created on the server, shown once and **never stored**.
- Preshared keys are kept only by the server agent (readable by root); the database holds public keys only.
- The server's private key never leaves the agent.
- An uplink configuration holds the private key of the customer server: shown once on the hub, kept only by the
  server agent of the customer server, deleted when it disconnects.

### Audit trail

Every change is written to the [audit trail](../administration/audit.md) with its reason:

| Entry | When |
|---|---|
| `server.wireguard` | the server settings were applied |
| `vpn.peer_add`, `vpn.peer_update`, `vpn.peer_rotate`, `vpn.peer_remove` | a peer was added, changed (also disabled or enabled), got a new key or was removed |
| `vpn.peer_expire` | a time-limited access ended (actor *system*), with the grant |
| `vpn.grant` | access was granted for a time window (who asked, who approved) |
| `change_request.create`, `change_request.approve`, `change_request.reject` | a technician's access was requested, approved or rejected under four eyes |
| `vpn.rule_add`, `vpn.rule_update`, `vpn.rule_remove` | an access rule was added, changed or removed |
| `vpn.apply` | *Apply again* |
| `vpn.import` | peers of an earlier version were imported (see below) |
| `vpn.uplink_connect`, `vpn.uplink_replace`, `vpn.uplink_transport`, `vpn.uplink_disconnect` | this server was connected to a hub, got a new uplink configuration, changed the transport or was disconnected (see [Uplink to a hub](uplink-hub.md)) |
| `vpn.uplink_rule_add`, `vpn.uplink_rule_update`, `vpn.uplink_rule_remove` | an uplink rule was added, changed or removed |

## Upgrade from the earlier WireGuard page

Versions before the VPN section had a simpler page, *System → WireGuard*, for devices only: a peer had a name, an
address and a key, and could reach every service of the server that listened on the VPN address. On the first start
after the upgrade:

- the existing peers are **imported once** as **device** peers of the whole server, with their addresses and keys —
  their configurations keep working;
- one access rule, **MQTT for devices** (TCP 1883 to this server), keeps the documented use working;
- everything else from the VPN is **denied** from then on;
- the import is written to the audit trail as `vpn.import`.

A peer that used the web interface or SSH through the tunnel needs a rule for it. Name, organization, expiry and
the other new fields can be set afterwards with **Edit**.

## Limits

| Item | Limit |
|---|---|
| Operating system | Linux with nftables (Debian, Ubuntu), or Windows with the VPN inside the server agent (see [Windows servers](setup.md#windows-servers)) |
| Access grants | 15 minutes to 30 days |
| Addresses | IPv4 only |
| Topology | hub and spoke; a hub needs a reachable UDP port (no mesh); a server without a public address joins a hub through an [uplink](uplink-hub.md) |
| Uplinks | one per server; technicians always go through the hub; the hub's other sites are not routed to a customer server |
| Peers | up to 5 free; with a licence up to 1 000 |
| Routed subnets | up to 16 per site, from /8 to /32 |
| Access rules | up to 500; up to 20 sources and 20 port entries per rule |
| Layer 2 | not carried: broadcasts such as Profinet discovery do not cross the VPN |

## Troubleshooting

| Symptom | Check |
|---|---|
| *Server agent not configured* or *not reachable* | the agent service: `systemctl status ctrl32-telemetry-agent` |
| *Configure and enable the VPN server first* | open *Server settings*, tick *Enabled*, apply |
| State *enabled but not running* | `journalctl -u ctrl32-telemetry-agent` and `systemctl status wg-quick@wg0`; a host without nftables support cannot start the interface |
| Firewall *not applied* | the error next to it; the previous rules stay loaded |
| A peer stays *offline*, *Last handshake: never* | the UDP port in the provider's firewall or the router's port forwarding, the endpoint in *Server settings*, the clock of the peer |
| The peer connects but reaches nothing | an access rule for exactly that source, destination and port; is the peer enabled and not expired? |
| A technician reaches the site router but not the PLC | the router routes replies back: is it the LAN's default gateway? Otherwise masquerade on the Raspberry Pi or add a route on the gateway ([Peers](peers.md#where-the-site-device-sits)) |
| *… overlaps …* when saving a site | the subnet is used by another site, the VPN range or the server's own network — sites need distinct LAN ranges |
| *requires a license* | sites, expiry dates, access grants and more than 5 peers require a licence; existing peers keep working |
| *four eyes for VPN access is on: request a time-limited grant* | under four eyes a technician cannot be enabled or get an expiry directly: use **Request access** and have a second person approve it |
| The technician still shows *awaiting approval* | the request waits under **Settings → Approvals**; it must be approved by someone other than the person who asked |
| *peer not found* or *destination not found* for a peer you know exists | it belongs to another organization or to the whole server: only a server administrator manages it |
| *an organization's rule may use its own peers …* or *… is not offered to organizations* | the limits of [Rules of an organization](organizations-and-approvals.md#rules-of-an-organization) |
| Windows: a service of the server is not reachable through the VPN | it must listen on `127.0.0.1` or all addresses; UDP services are not offered on Windows |
| Windows: a connector cannot read a PLC at a site | on Windows the server cannot open connections into sites; use a Linux server |
| The configuration was lost | **Rotate key** and configure the peer again |

On the server, these commands show the live state:

```bash
nft list table inet ctrl32_vpn            # the loaded access rules with their counters
wg show wg0                               # peers, handshakes and traffic
ip route show dev wg0                     # routes to the site networks
sysctl net.ipv4.conf.wg0.forwarding       # 1 while a rule needs forwarding
journalctl -u ctrl32-telemetry-agent      # what the agent applied, and errors
```

The server sends the stored state to the agent again every five minutes; **Apply again** does it at once.
