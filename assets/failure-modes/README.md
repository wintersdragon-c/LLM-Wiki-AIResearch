# Directory Purpose

This directory stores observed ways methods, agents, training runs, or evaluations fail.

## What Goes Here

- Reward hacking behaviors, brittleness, benchmark overfitting, tool-use breakdowns, reproducibility failures, and other observed failure modes.

## What Does Not Go Here

- Design choices that look tempting but should be avoided before they fail. Put those in `assets/negative-assets/`.
- Evaluation metrics. Put those in `assets/evaluation-patterns/`.
- Mechanisms. Put those in `assets/mechanisms/`.

## Neighboring Directories To Check

- `assets/negative-assets/` when the item is a trap-like design choice rather than an observed failure.
- `papers/` for source provenance.
- `ideas/` if the failure affects an existing hypothesis or proposal.

## Required Frontmatter

```yaml
type: failure-mode
title: "Failure Mode Title"
slug: failure-mode-slug
asset_kind: failure-mode
tags: [asset, failure-mode]
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<slug>.md`, for example `reward-hacking-on-format.md`.

## Good Example

`assets/failure-modes/reward-hacking-on-response-format.md`

## Common Wrong Placement

Do not put a speculative design warning here unless the paper observed the failure. Use `assets/negative-assets/` for what-not-to-do lessons.
