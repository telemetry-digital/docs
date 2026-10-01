---
title: Front page
slug: front-page
sidebar_position: 6
tags: [administration, landing-page, branding]
---

The page visitors see before signing in is edited graphically: *System → Front page* (permission `system.admin`).

Until you publish a page of your own, visitors go straight to the **sign-in page**, which shows a short overview of
the system next to the form.

## Editing

- **Add a block**: hero, features, text, image, text + image, numbers, call to action, contact, divider.
- **Blocks**: drag to reorder, move up and down, duplicate, delete.
- **Preview**: click a block to select it, click a text to type directly in the page. Computer, tablet and phone
  widths show how the page adapts.
- **Properties**: texts, images (logo, uploaded images), alignment, columns, spacing, colours with an optional
  gradient, icons, numbers, buttons with links, contact details. Without a selected block: the browser title, the
  description for search engines, background, text and accent colours, width.
- Text: a blank line starts a paragraph, lines starting with "- " form a list, `**bold**` is bold.
- Undo and redo (Ctrl+Z, Ctrl+Shift+Z); a warning before leaving with unsaved changes.

## Versions and publishing

*Save draft* stores a new version (with a reason) without changing what visitors see. *Publish* stores and publishes
at once. *Versions* lists every saved version: continue from one, publish an older one, or show the built-in page
again.

## Safety

The page is public, so it is **data, not HTML**:

- only known blocks and fields, plain text with length limits, and links to this server, `https://`, `mailto:` or
  `tel:`;
- every text is shown as text — nothing typed into the page can run as code;
- images are the server's own (PNG, JPEG, WebP, and SVG with scripts removed), up to 2 MiB;
- the page cannot remove the sign-in button, so no edit can lock users out;
- every save, publication and image upload is audited.
