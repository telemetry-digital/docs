---
title: Connecting an assistant
slug: ai-connecting
sidebar_position: 1
tags: [ai, mcp, oauth]
---

## The MCP address

```
https://<your server>/mcp
```

The address is shown under *Settings → AI assistants* and *My account → AI assistants*. Before connecting, make sure
an administrator has switched AI access on, and that `http.base_url` in `config.toml` holds the address users reach
the server at (for example `https://telemetry.example.com`) — the sign-in metadata is built from it.

## Sign in from the assistant (OAuth)

Most AI assistants (MCP clients) let you add a **custom connector** or **remote MCP server** by its address:

1. Add a custom connector and paste the MCP address.
2. The assistant opens the **sign-in page of your server**. Sign in as usual, with two-factor sign-in.
3. Choose **Everything my permissions allow** or **Reading only**, and press **Allow**.

That is all — the assistant now works as you. Signing in uses OAuth 2.1 with PKCE; clients register themselves, so
there is nothing to set up on the server for each client.

## With an API token instead

Clients that cannot sign in through the browser can use a personal API token: create one under *My account → API
tokens* and send it as `Authorization: Bearer <token>` to the MCP address.

## See and disconnect assistants

- Every user sees and disconnects their own assistants under *My account → AI assistants*.
- An administrator sees and disconnects all of the organization's assistants under *Settings → AI assistants*.

Disconnecting ends the approval and every token issued under it at once.

## For client developers

Streamable HTTP, one POST per JSON-RPC message, `application/json` answers. OAuth 2.1 is discovered from the 401
answer (RFC 9728 → RFC 8414), with dynamic client registration (RFC 7591) and revocation (RFC 7009). Supported
protocol versions: 2025-11-25, 2025-06-18, 2025-03-26 and 2024-11-05.
