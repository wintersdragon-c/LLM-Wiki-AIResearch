# Directory Purpose

This directory stores reusable mechanisms extracted from papers or source materials.

## What Goes Here

- A method mechanism, training objective, architectural trick, reward design, or algorithmic move that can transfer across projects.

## What Does Not Go Here

- A full experimental setup. Put that in `assets/experiment-patterns/`.
- A benchmark or measurement strategy. Put that in `assets/evaluation-patterns/`.
- A failure behavior. Put that in `assets/failure-modes/`.
- A trap-like design choice to avoid. Put that in `assets/negative-assets/`.

## Neighboring Directories To Check

- `papers/` for source provenance.
- `ideas/` if the mechanism directly inspires a hypothesis or proposal.
- `assets/experiment-patterns/` if the main value is how the paper tested the mechanism.

## Required Frontmatter

```yaml
type: mechanism
title: "Mechanism Title"
slug: mechanism-slug
asset_kind: mechanism
tags: [asset, mechanism]
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<slug>.md`, for example `chain-of-thought-prompting.md`.

## Good Example

`assets/mechanisms/lambdarank-pairwise-ranking-loss.md`

## Common Wrong Placement

Do not put an ablation matrix here. Put the reusable test design in `assets/experiment-patterns/`.
