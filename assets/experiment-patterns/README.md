# Directory Purpose

This directory stores reusable experimental design patterns.

## What Goes Here

- Ablation structures, control designs, reproduction setups, minimal experiments, and comparison patterns.

## What Does Not Go Here

- A metric or benchmark definition. Put that in `assets/evaluation-patterns/`.
- A method mechanism. Put that in `assets/mechanisms/`.
- A failure behavior. Put that in `assets/failure-modes/`.
- A design trap to avoid. Put that in `assets/negative-assets/`.

## Neighboring Directories To Check

- `papers/` for source provenance.
- `ideas/proposals/` if the experiment is attached to a specific proposal.
- `assets/evaluation-patterns/` if the main contribution is measurement.

## Required Frontmatter

```yaml
type: experiment-pattern
title: "Experiment Pattern Title"
slug: experiment-pattern-slug
asset_kind: experiment-pattern
tags: [asset, experiment-pattern]
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<slug>.md`, for example `step-level-reward-ablation.md`.

## Good Example

`assets/experiment-patterns/process-vs-outcome-reward-ablation.md`

## Common Wrong Placement

Do not put benchmark selection here unless the benchmark design is the reusable pattern. Put measurement strategy in `assets/evaluation-patterns/`.
