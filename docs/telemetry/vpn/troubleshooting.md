---
title: VPN security, upgrade and troubleshooting
slug: vpn-troubleshooting
sidebar_position: 4
tags: [vpn, security, audit, upgrade, troubleshooting]
---

## Security

### Who may do what

| Action | Needs |
|---|---|
| See the VPN page | `system.admin` |
| Server settings, edit, disable, enable and remove peers, access rules, apply again | `system.admin` and a reason |
| Add a peer, rotate a key (a configuration with a private key is shown) | `system.admin`, a reason, your password and — with two-factor sign-in — the authenticator code |

Organizations are an attribute of a peer and a rule source; managing the VPN stays with server administrators. AI
assistants are never offered adding a peer or rotating a key.

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

### Audit trail

Every change is written to the [audit trail](../administration/audit.md) with its reason:

| Entry | When |
|---|---|
| `server.wireguard` | the server settings were applied |
| `vpn.peer_add`, `vpn.peer_update`, `vpn.peer_rotate`, `vpn.peer_remove` | a peer was added, changed (also disabled or enabled), got a new key or was removed |
| `vpn.peer_expire` | a time-limited access ended (actor *system*) |
| `vpn.rule_add`, `vpn.rule_update`, `vpn.rule_remove` | an access rule was added, changed or removed |
| `vpn.apply` | *Apply again* |
| `vpn.import` | peers of an earlier version were imported (see below) |

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
| Operating system | Linux with nftables (Debian, Ubuntu); Windows in a later version |
| Addresses | IPv4 only |
| Topology | hub and spoke; the server needs a reachable UDP port (no NAT traversal, no mesh) |
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
| *requires a license* | sites, expiry dates and more than 5 peers require a licence; existing peers keep working |
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
