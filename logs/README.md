# Directory Purpose

This directory stores operation-process records and audit trails.

## What Goes Here

- Ingest audits, doctor audits, maintenance reports, dry-run reports, and link-repair details.

## What Does Not Go Here

- Research-content synthesis. Put that in `reviews/`.
- Topic maps. Put those in `topics/`.
- Paper analysis. Put that in `papers/`.

## Neighboring Directories To Check

- `log.md` for the global operation timeline.
- `reviews/` when the content is research judgment rather than an operation audit.

## Required Frontmatter

```yaml
type: log
title: "Log Title"
slug: YYYY-MM-DD-log-slug
tags: [log]
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<YYYY-MM-DD>-<slug>.md`, for example `2026-04-13-doctor-audit.md`.

## Good Example

`logs/2026-04-13-doctor-audit.md`

## Common Wrong Placement

Do not put reusable research synthesis here. Logs are for process and audit records.
