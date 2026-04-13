# Directory Purpose

This directory stores durable research-content syntheses.

## What Goes Here

- Cross-paper reviews, topic comparisons, replication plans, durable critique synthesis, and research memos.

## What Does Not Go Here

- Operation audit trails. Put those in `logs/`.
- Single-paper summaries. Put those in `papers/`.
- Topic map pages. Put those in `topics/`.
- Idea cards or proposals. Put those in `ideas/`.

## Neighboring Directories To Check

- `topics/` when the synthesis should become a living topic map or survey.
- `ideas/` when the synthesis is a concrete research proposal.
- `logs/` when the content is an operation trace.

## Required Frontmatter

```yaml
type: review
title: "Review Title"
slug: review-slug
tags: [review]
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<slug>.md`, for example `process-supervision-survey.md`.

## Good Example

`reviews/lambdarank-for-grpo-transfer-analysis.md`

## Common Wrong Placement

Do not store a doctor or ingest audit here. Put process records in `logs/`.
