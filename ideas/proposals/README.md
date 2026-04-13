# Directory Purpose

This directory stores mature research proposals upgraded from hypothesis cards.

## What Goes Here

- Ideas supported by at least two independent sources.
- Ideas with a minimal experiment design.
- Proposals with explicit risks, assumptions, positioning, and relationship to existing ideas.

## What Does Not Go Here

- Single-source idea sketches. Put those in `ideas/hypothesis/`.
- Paper notes. Put those in `papers/`.
- Experiment patterns that are reusable outside the proposal. Put those in `assets/experiment-patterns/`.
- Broad literature synthesis. Put that in `reviews/`.

## Neighboring Directories To Check

- `ideas/hypothesis/` for the originating hypothesis.
- `papers/` for supporting sources.
- `topics/` for research-space positioning.
- `reviews/` when the proposal requires a broader synthesis page.

## Required Frontmatter

```yaml
type: proposal
title: "Proposal Title"
slug: p-slug
status: proposal
confidence: medium
positioning: fills-gap
tags: [idea, proposal]
topic_refs: []
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `p-<slug>.md`, for example `p-process-reward-scaling.md`.

## Good Example

`ideas/proposals/p-lambdarank-style-grpo-preference-shaping.md`

## Common Wrong Placement

Do not create a proposal only because an idea sounds promising. Keep it as a hypothesis until it has two independent sources and a minimal experiment.
