# Directory Purpose

This directory stores one structured markdown page per ingested paper or paper-like research source.

## What Goes Here

- Canonical paper summaries created by the ingest workflow.
- Structured analysis of a single paper's problem, method, experiments, critique, reusable assets, and inspired ideas.

## What Does Not Go Here

- Raw PDFs, code snapshots, screenshots, and unprocessed notes. Put those in `sources/`.
- Cross-paper synthesis. Put that in `reviews/`.
- Reusable mechanism or experiment pages. Put those in `assets/`.
- Research idea cards. Put those in `ideas/`.

## Neighboring Directories To Check

- `sources/` for the source bundle that produced the paper page.
- `topics/` for topic map and survey links.
- `assets/` for extracted reusable patterns.
- `ideas/` for inspired hypotheses and proposals.

## Required Frontmatter

```yaml
type: paper
title: "Paper Title"
slug: firstauthor-year-keyword
paper_id: firstauthor-year-keyword
tags: [paper]
topic_refs: []
inspired_ideas: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<firstauthor>-<year>-<keyword>.md`, and make `paper_id` match the file slug exactly.

## Good Example

`papers/wei-2022-chain-of-thought.md`

## Common Wrong Placement

Do not put a replication plan here if it compares multiple papers or proposes a new experiment. Put that durable synthesis in `reviews/` or `ideas/proposals/`.
