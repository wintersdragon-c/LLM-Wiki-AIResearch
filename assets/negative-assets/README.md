# Directory Purpose

This directory stores what-not-to-do lessons: design choices that look reasonable but are traps.

## What Goes Here

- Trap-like objectives, baselines, evaluation shortcuts, or system designs that should usually be avoided.
- Negative lessons extracted after critique.

## What Does Not Go Here

- Observed failure behavior without a reusable warning. Put that in `assets/failure-modes/`.
- A missing baseline complaint that is not reusable. Keep it in the paper critique.
- A mechanism that remains useful. Put that in `assets/mechanisms/`.

## Neighboring Directories To Check

- `assets/failure-modes/` when the key item is an observed failure.
- `papers/` for critique provenance.
- `ideas/` if the warning invalidates a hypothesis or proposal.

## Required Frontmatter

```yaml
type: negative-asset
title: "Negative Asset Title"
slug: negative-asset-slug
asset_kind: negative-asset
tags: [asset, negative-asset]
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<slug>.md`, for example `outcome-only-supervision.md`.

## Good Example

`assets/negative-assets/outcome-only-supervision-for-process-learning.md`

## Common Wrong Placement

Do not put every critique point here. Only extract critiques that become reusable warnings for future research design.
