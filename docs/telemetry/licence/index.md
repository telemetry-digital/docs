---
title: Licence
slug: licence
sidebar_position: 10
tags: [licence]
---

telemetry.digital runs without a licence. More cameras, [white labeling](../administration/white-labeling.md),
[changes through AI assistants](../ai-assistants/index.md) and, in the [VPN](../vpn/index.md), more than 5 peers, sites
and time-limited technician access require one.

## Activate a licence

1. Check that `http.base_url` in `config.toml` holds the name the server is reached at — the licence is issued for
   that host name.
2. **System → License → Activate with the order number**: enter the order number and a reason, then *Activate*.
   The page shows the server name the licence will be issued for.

    ![The activation form on the License page: order number, reason, the server name the licence is issued for, and the Activate button](img/activate.webp)

3. A server without internet access gets a licence key instead: **System → License → Install a licence key
   (offline)**, paste the key, give a reason, *Install*.

    ![The section Install a license key (offline) with the field for the licence key, which is verified offline](img/offline-key.webp)

The License page shows the licensee and the validity; the activation is written to the audit trail.
