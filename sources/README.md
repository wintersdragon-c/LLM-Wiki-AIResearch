# Directory Purpose

Use `sources/` as the low-friction intake layer for user-owned source folders.

## What Goes Here

- One folder per source bundle, paper family, or research material bundle.
- Raw PDFs, markdown exports, screenshots, figures, copied notes, code snapshots, datasets, and repo snapshots.
- Optional `notes.md` describing why the material matters and what the user wants from ingest.

## What Does Not Go Here

- Structured paper pages. Put those in `papers/`.
- Topic maps or surveys. Put those in `topics/`.
- Hypothesis or proposal pages. Put those in `ideas/`.
- Reusable extracted mechanisms or experiment patterns. Put those in `assets/`.

## Neighboring Directories To Check

- `papers/` after a source is ingested into a canonical paper page.
- `assets/` after reusable mechanisms or patterns are extracted.
- `ideas/` after Mutate or Compose creates research ideas.
- `reviews/` when a durable synthesis is created from multiple materials.

## Required Frontmatter

No frontmatter is required inside `sources/`. Source folders are user-owned intake material.

## Naming Pattern

Use kebab-case folder names: `sources/hacrl/`, `sources/lambdarank-grpo/`.

## Good Example

```text
sources/hacrl/
  notes.md
  paper.pdf
  repo/
  figures/
```

## Common Wrong Placement

Do not write `sources/hacrl/paper-summary.md` as a compiled wiki page. Create the compiled page under `papers/` after ingest.
