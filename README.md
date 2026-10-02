# docs_telemetry — product documentation for telemetry.digital

End-user documentation of **telemetry.digital** (the ctrl32 telemetry server): installation, devices, dashboards,
the Video NVR, energy, automation, AI assistants, administration and the licence.

Text only. No product code lives here. The repository is published at
[github.com/telemetry-digital/docs](https://github.com/telemetry-digital/docs); the pages are rendered on the web by
the Sourcebook documentation renderer, which pulls this repository from `main`.

## Layout

```
docs/telemetry/index.md            the root page (slug: telemetry)
docs/telemetry/<section>/index.md  one folder per section, the section's own page
docs/telemetry/<section>/<page>.md pages of the section (children of its index.md)
```

Rules every file follows:

- Only `*.md` files and their images under `docs/`, English only for now.
- Every file starts with YAML front matter: `title`, `slug` (short, kebab-case, unique in the whole repository),
  `sidebar_position` (integer, the order among siblings) and `tags` (a list). Optional: `draft`, `image`,
  `image_alt`.
- The title comes from the front matter: no `#` heading in the body; use `##` to `####` only.
- Python-Markdown: fenced code, tables, sane lists, attr_list. Nested lists are indented by 4 spaces.
- Admonitions in the MkDocs spelling — `!!! note "Title"` with a 4-space indented body. Types: note, tip, info,
  warning, danger, example, success.
- No raw HTML of any kind.
- Screenshots go next to the pages of a section, in `docs/telemetry/<section>/img/*.webp` (short kebab-case names),
  and are linked relatively with meaningful alt text (`![The live view with camera tiles](img/live-view.webp)`).
- Links between pages are relative `.md` paths, for example `[relay](../video-nvr/remote-sites.md)`.

## Adding a page

1. Create `docs/telemetry/<section>/<name>.md` (or a new folder with its own `index.md` for a new section).
2. Add the front matter with a new unique `slug` and a `sidebar_position` after the existing pages.
3. Link it from the section's `index.md`.
4. Check before committing: every relative `.md` link resolves, no `#` heading in the body, no raw HTML.

Facts come from the product's own documentation; do not describe features, prices or numbers that are not there.
