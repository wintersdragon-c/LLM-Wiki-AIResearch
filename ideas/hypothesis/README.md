# Directory Purpose

This directory stores fast-capture hypothesis cards produced during Mutate or Compose.

## What Goes Here

- One-question research hypothesis cards.
- Low- or medium-confidence ideas that are not yet supported by two independent sources.
- Ideas with a rough feasibility judgment but no complete proposal.

## What Does Not Go Here

- Mature ideas with at least two independent sources and a minimal experiment. Put those in `ideas/proposals/`.
- Paper summaries. Put those in `papers/`.
- Cross-paper reviews. Put those in `reviews/`.
- Reusable mechanisms detached from a specific idea. Put those in `assets/mechanisms/`.

## Neighboring Directories To Check

- `ideas/proposals/` when support and minimal experiment are strong enough.
- `papers/` for source provenance.
- `topics/` for research-space placement.

## Required Frontmatter

```yaml
type: hypothesis
title: "Hypothesis Title"
slug: h-slug
status: hypothesis
confidence: low
tags: [idea, hypothesis]
topic_refs: []
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `h-<slug>.md`, for example `h-process-reward-scaling.md`.

## Good Example

`ideas/hypothesis/h-lambdarank-style-grpo-preference-shaping.md`

## Common Wrong Placement

Do not keep an idea here after it has two independent sources and a concrete minimal experiment. Promote it to `ideas/proposals/`.
