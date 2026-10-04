---
title: My account
slug: admin-my-account
sidebar_position: 10
tags: [administration, account, password, mfa, api-tokens, notifications]
---

**My account** is at the bottom of the menu of every signed-in user, next to *Sign out* (on a phone at the end of the
menu); a click opens its pages underneath. It needs no permission, except *API tokens*, which needs
`token.manage`.

| Page | Address |
|---|---|
| Profile | `/account` |
| Password and MFA | `/account/security` |
| API tokens | `/account/tokens` |
| Browser notifications | `/account/push` |
| AI assistants | `/account/ai` |

In an [app](pwa-apps.md) that is locked to its sections, *My account* keeps only *Password and MFA* and *Sign out*.

## Profile

Your user name and full name, your **roles** and your **effective permissions** — the union of the permissions of
all your roles. Only an administrator with `user.admin` changes your roles, name or e-mail (see
[Users and permissions](users-and-permissions.md)).

Your interface **language** is chosen in the menu; you can pick only languages your organization has enabled.

## Password and MFA

### Change password

| Field | Rule |
|---|---|
| Current password | required |
| New password | at least 12 characters and at least three of: lowercase, uppercase, digits, other characters; must differ from the current one |

After the change your other sessions are signed out.

If you forget your password, use *Forgot password* on the sign-in page: the server e-mails a link that is valid for
one hour (the server needs e-mail configured).

### Two-factor authentication

The state is *enabled*, *required by policy, not set up* or *not set up*.

1. **Set up authenticator** shows a QR code and, under *Secret for manual entry*, the secret. Scan it with an
   authenticator app (time-based codes, 6 digits, 30 seconds).
2. Type the 6-digit code from the app and press **Confirm**.

Two-factor sign-in is **not active until the code is confirmed**. After confirmation the page shows **8 recovery
codes** of the form `abcd-efgh`, once. Store them safely: each one replaces an authenticator code once.

If you lose the authenticator, an administrator resets it (*Users → user → Reset MFA*); you then set it up again.

Two-factor sign-in is required for the roles `server_admin`, `org_admin`, `qa` and `engineer`, and for everyone on a server in the
internet profile.

## API tokens

Tokens let scripts and integrations call the API as you, with a subset of your permissions. They are sent as
`Authorization: Bearer c32_…`.

| Field | Default | Rule |
|---|:---:|---|
| Name | — | required, up to 64 characters |
| Scopes | none | any of your own permissions; a scope you do not have is refused |
| Expires in | 1 year | 30 days, 90 days, 1 year or never |

The token is shown **once**, right after *Create token*; copy it then. The list shows *Name*, *Scopes*, *Created*,
*Last used* and *Expires*, with **Revoke**.

A token never has more than you: a request is allowed only for permissions that are both in its scopes and in your
current permissions, so removing a role from you also narrows your tokens.

## Browser notifications

Alarm and rule notifications can reach your browser as system notifications, even when the app is closed. Which
users are notified is chosen by an administrator with a Web Push channel (*Settings → Notification channels*).

| Control | Meaning |
|---|---|
| Enable in this browser | asks the browser for permission and registers it |
| Disable in this browser | removes this browser |
| Send a test notification | sends a test to every registered browser of yours |
| Remove | removes one registered browser |

The state line says *enabled in this browser*, *not enabled in this browser*, *blocked in the browser settings* or
*not supported in this browser (or not HTTPS)*. The list shows each browser with *Registered*, *Last notification*
and *State* (active, delivery failed, removed). On phones, install the app from the browser menu first.

## AI assistants

AI assistants connect over MCP and act with your permissions. Every change they make carries a reason and is written
to the audit trail with the note *via MCP*.

- The state line says whether AI access is on in your organization (an administrator switches it under
  *Settings → AI assistants*; it is off by default).
- When it is on, the page shows the **MCP address** to add as a custom connector in your assistant, and the header
  form for assistants that take a personal API token.
- When the server has no licence for changes through AI assistants, the page says that assistants can only read.
- The list shows your assistants with *Access* (*reading only* or *my permissions*), *Connected*, *Last used* and
  *State*, and **Disconnect** (asks for a reason).

See [Connecting AI assistants](../ai-assistants/connecting.md).
