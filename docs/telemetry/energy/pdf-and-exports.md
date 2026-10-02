---
title: PDF reports and exports
slug: pdf-and-exports
sidebar_position: 1
tags: [reports, pdf, export, csv]
---

telemetry.digital produces **PDF reports** from templates you design yourself, and exports data to Excel and CSV.

## PDF reports

Report templates are designed from **blocks**: title, summary, chart, text, table, daily table, signatures, spacer
and page break, on A4, A3, A5, US Letter or US Legal paper, portrait or landscape. Templates are content like
dashboards — versioned, with a reason for every change.

Under *Settings → Report templates* a template is put together from blocks in the designer, or edited as Typst
source; *Preview PDF* shows the result before it is saved as a new version.

![Settings → Report templates: kind and locale, the Blocks (designer) and Source (Typst) modes, paper, font and size, the blocks Title, Summary facts, Data table and End marker, and the buttons Preview PDF, Save as new version and Revert to built-in](img/report-templates.webp)

Generated reports are stored under **Settings → Stored reports** together with their hashes, so a stored report can
be shown to be unchanged. Reports can be **electronically signed** by people with the signing permission (the
built-in *qa* role has it).

![Settings → Stored reports: generated PDF reports with time, kind, author, template, size, file hash and a download link](img/stored-reports.webp)

With [white labeling](../administration/white-labeling.md) the footer of PDF reports names your product.

## Exports

- **Period tables** export to Excel (XLSX), CSV and PDF — see [Period tables](../dashboards/period-tables.md).
- The **audit trail** and the **access log** export to CSV (see [Audit trail](../administration/audit.md)).
- Video clips export to MP4 and to signed evidence packages (see [Evidence](../video-nvr/evidence.md)).

Exporting data needs the permission `data.export`, and every export is written to the audit trail.

*Settings → Organization export* (permission `system.admin`) downloads one ZIP with the whole configuration as
JSON, all records of a chosen period as CSV and a manifest with the SHA-256 of every file and the head of the audit
chain.

![Settings → Organization export with the dates From and To and the button Download export](img/organization-export.webp)

## Through the API

Everything the user interface does is available in the REST API at `/api/v1`, described in OpenAPI at `/api/docs` on
your server — reports and exports included. API tokens are created under *My account → API tokens*.

![The API reference page: the list of operation groups on the left, the overview of authentication, permissions and conventions, and the operations with their method and path](img/api-reference.webp)
