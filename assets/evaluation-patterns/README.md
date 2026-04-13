# Directory Purpose

This directory stores reusable evaluation and measurement patterns.

## What Goes Here

- Benchmark strategies, metric definitions, judge protocols, evaluation harness patterns, and measurement designs.

## What Does Not Go Here

- Training mechanisms. Put those in `assets/mechanisms/`.
- Ablation or reproduction setups. Put those in `assets/experiment-patterns/`.
- Observed failure behavior. Put that in `assets/failure-modes/`.
- Trap-like design choices. Put those in `assets/negative-assets/`.

## Neighboring Directories To Check

- `papers/` for source provenance.
- `topics/` when evaluation defines a subfield's current dispute.
- `reviews/` for cross-paper evaluation comparisons.

## Required Frontmatter

```yaml
type: evaluation-pattern
title: "Evaluation Pattern Title"
slug: evaluation-pattern-slug
asset_kind: evaluation-pattern
tags: [asset, evaluation-pattern]
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<slug>.md`, for example `process-vs-outcome-eval.md`.

## Good Example

`assets/evaluation-patterns/agent-harness-task-success-eval.md`

## Common Wrong Placement

Do not store a paper's entire experiment section here. Extract only the reusable evaluation design.
