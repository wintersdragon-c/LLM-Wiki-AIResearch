# Repository Guidelines

## What This Repository Is

This repository is an LLM-maintained research wiki for computer science work. The repository is a persistent, compounding markdown knowledge base rather than an application codebase.

Read `LLM-wiki.md`, `docs/superpowers/specs/2026-04-10-research-wiki-design.md`, and `research-map.md` before making structural edits.

## Architecture

Three layers:

- `raw/` contains immutable source materials. Never edit files in `raw/`.
- Root-level wiki directories (`topics/`, `papers/`, `ideas/`, `assets/`, `reviews/`, `logs/`) contain LLM-maintained markdown pages.
- Schema files (`AGENTS.md`, `CLAUDE.md`) define workflows and conventions.

Root-level special files:
- `index.md` — content catalog; read before every Query
- `log.md` — append-only global timeline; append after every operation
- `research-map.md` — authoritative research space map; read before every Map stage

## File Naming

All file names must be kebab-case slugs. No spaces, no uppercase, no underscores.

| Directory | Pattern | Example |
|-----------|---------|---------|
| `topics/` | `<slug>.md` | `llm-reasoning.md` |
| `topics/<slug>/` | `<subtopic-slug>.md` | `chain-of-thought.md` |
| `papers/` | `<firstauthor>-<year>-<keyword>.md` | `wei-2022-chain-of-thought.md` |
| `ideas/hypothesis/` | `h-<slug>.md` | `h-process-reward-scaling.md` |
| `ideas/proposals/` | `p-<slug>.md` | `p-process-reward-scaling.md` |
| `assets/mechanisms/` | `<slug>.md` | `chain-of-thought-prompting.md` |
| `assets/experiment-patterns/` | `<slug>.md` | `step-level-reward-ablation.md` |
| `assets/evaluation-patterns/` | `<slug>.md` | `process-vs-outcome-eval.md` |
| `assets/failure-modes/` | `<slug>.md` | `reward-hacking-on-format.md` |
| `assets/negative-assets/` | `<slug>.md` | `outcome-only-supervision.md` |
| `reviews/` | `<slug>.md` | `process-supervision-survey.md` |
| `logs/` | `<YYYY-MM-DD>-<slug>.md` | `2026-04-11-ingest-audit.md` |

The `paper_id` field in paper frontmatter must match the file slug exactly (without `.md`).

## Core Operations

### Ingest
Run the approved pipeline in order:
`Parse -> Appreciate -> Map -> Positioning -> Diff -> Abstract -> Critique -> Mutate -> Compose -> Update -> Log`

#### Parse
- Extract title, authors, venue, year, problem statement, proposed method, key results, and author-stated limitations.

#### Appreciate
Answer each question explicitly:
1. What is the core innovation? (Strip the marketing language — what actually changed?)
2. What is the real advance over prior work?
3. Which single design is most worth learning from?
4. Which experiment is most elegant?
5. Which framing, task definition, or evaluation approach is most inspiring?
6. What is transferable to other problems?

#### Map
- Read `research-map.md` first.
- Identify which topic map pages and survey subpages this paper belongs to.

#### Positioning
- Classify the paper as exactly one primary role:
  - `opens-direction`
  - `fills-gap`
  - `overturns-assumption`
  - `engineering-reinforcement`
- Apply the matching update target:
  - `opens-direction` updates `research-map.md` and may create a new topic page
  - `fills-gap` updates an existing topic page or known-gap section
  - `overturns-assumption` triggers idea re-review and critique updates
  - `engineering-reinforcement` updates experiment-pattern and method-route pages

#### Diff
- Record contradictions, reinforcements, newly tracked variables, and superseded claims relative to the current wiki.

#### Abstract
- Immediately extract these reusable assets when present:
  - mechanisms
  - experiment patterns
  - evaluation patterns
  - failure modes
  - transferable objectives, biases, and data tricks

#### Critique
- Record assumption holes, experimental blind spots, missing baselines, overclaims, and reproducibility concerns.
- After critique, extract `negative-assets` for designs that look reasonable but are traps — write each to `assets/negative-assets/`.

#### Mutate
- Generate internal variants without relying on other papers.
- Convert promising variants into `ideas/hypothesis/` cards.

#### Compose
- Always run.
- In weak-compose mode (`<10` papers), create low-confidence combinations and mark hypothesis cards with `confidence: low`.
- In strong-compose mode (`>=10` papers), generate full cross-paper combinations with normal confidence.

#### Update
- Update all affected pages.
- Maintain bidirectional links between `papers/` and `ideas/`.
- Check whether any hypothesis should be promoted to proposal.
- Add or refresh entries in `index.md` for every new or significantly changed page.

#### Log
- Append a summary to `log.md`.
- Write detailed audit records to `logs/` when the session is substantial.

### Query
Read `research-map.md` and `index.md` first, then relevant topic, paper, idea, and asset pages. File valuable outputs back into the wiki.

### Lint
Check for contradictions, stale claims, orphan pages, missing cross-links, upgradeable hypotheses, stale `under-review` ideas, and `research-map.md` drift.

## Page Conventions

- Use `[[WikiLink]]` syntax for all internal references.
- Every page must have YAML frontmatter with at minimum: `type`, `title`, `slug`, `tags`, `created`, `updated`.
- `hypothesis` pages also require `status`, `confidence`, `topic_refs`, `source_papers`.
- `proposal` pages also require `status`, `confidence`, `positioning`, `topic_refs`, `source_papers`.
- `paper` pages also require `paper_id`, `topic_refs`, `inspired_ideas`.
- `asset` pages also require `asset_kind`, `source_papers`.

## Frontmatter Schema

### Universal (all pages)

```yaml
type: <page-type>
title: "Human-readable title"
slug: <file-slug-without-extension>
tags: [<tag>, ...]
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

### paper

```yaml
paper_id: <firstauthor>-<year>-<keyword>   # must match file slug
topic_refs: ["[[topics/slug]]", ...]
inspired_ideas: []
```

### hypothesis

```yaml
status: hypothesis                          # or under-review
confidence: low                             # low | medium | high
topic_refs: ["[[topics/slug]]", ...]
source_papers: ["[[papers/slug]]", ...]
```

### proposal

```yaml
status: proposal                            # or under-review
confidence: medium                          # low | medium | high
positioning: fills-gap                      # opens-direction | fills-gap | overturns-assumption | engineering-reinforcement
topic_refs: ["[[topics/slug]]", ...]
source_papers: ["[[papers/slug]]", ...]
```

### topic / topic-survey

```yaml
subtopics: ["[[topics/slug/subtopic]]", ...]
papers: []
ideas: []
open_questions: []
```

### asset (mechanism, experiment-pattern, evaluation-pattern, failure-mode, negative-asset)

```yaml
asset_kind: mechanism                       # matches type value
source_papers: ["[[papers/slug]]", ...]
```

## Idea Lifecycle

### Hypothesis Cards
- Created automatically during `Mutate` or `Compose`.
- Must include a one-sentence question, source links, a feasibility note, and `confidence`.

### Proposal Promotion
- Promote a hypothesis to `ideas/proposals/` only when:
  - at least two independent sources support the core assumption
  - a minimal experiment can be sketched

### Decay And Re-Review
- If a paper is positioned as `overturns-assumption`, mark affected hypotheses or proposals as `under-review` until resolved.

## Bidirectional Tracking

- Each relevant `papers/*.md` page maintains `inspired_ideas:`.
- Each idea page maintains `sources:` with anchor-level provenance when possible.
- Keep both directions consistent during every `Update`.

## Topic Pages

### Topic Map Pages
- Default topic pages live at `topics/<topic-name>.md`.
- Required structure: definition, core sub-problems, current judgment, known gaps.

### Survey Subpages
- Create `topics/<topic-name>/<subtopic>.md` only when a topic accumulates 5+ papers or route divergence appears.
- Required structure: route evolution, representative works, strongest hypothesis, counterexamples, open problems.

## Directory Guide

- `topics/`: topic map pages and survey subpages
- `papers/`: one structured page per paper
- `ideas/hypothesis/`: fast-capture idea cards
- `ideas/proposals/`: mature ideas with minimal experiment plans
- `assets/`: reusable mechanisms, experiment patterns, evaluation patterns, failure modes, and negative assets
- `reviews/`: research-content syntheses
- `logs/`: operation-process records

## Logging

Append operation summaries to `log.md` using:

`## [YYYY-MM-DD] <operation> | <title or summary>`

Where `<operation>` is one of: `ingest`, `query`, `lint`, `bootstrap`, `dry-run`.

Put detailed audit trails in `logs/`.

### Log Routing

Write to `log.md` (global timeline) for:
- Every `ingest`
- Every `query` that writes new pages back to the wiki
- Every `lint`
- Every `proposal` promotion

Write to `logs/` only (no `log.md` entry) for:
- Intermediate checks and temporary audits
- Batch link-repair details
- Any operation that does not change wiki content

`dry-run` operations must use the `dry-run` operation type in `log.md` to distinguish test runs from real ingests:
```
## [YYYY-MM-DD] dry-run | <title>
```

## Action → Template Map

| Action | Template | Required frontmatter fields |
|--------|----------|-----------------------------|
| `new-paper` | `papers/_template.md` | `paper_id`, `title`, `slug`, `topic_refs` |
| `new-hypothesis` | `ideas/hypothesis/_template.md` | `title`, `slug`, `status`, `confidence`, `topic_refs`, `source_papers` |
| `promote-proposal` | `ideas/proposals/_template.md` | `title`, `slug`, `status`, `confidence`, `positioning`, `topic_refs`, `source_papers` |
| `new-mechanism` | `assets/mechanisms/_template.md` | `title`, `slug`, `asset_kind`, `source_papers` |
| `new-experiment-pattern` | `assets/experiment-patterns/_template.md` | `title`, `slug`, `asset_kind`, `source_papers` |
| `new-evaluation-pattern` | `assets/evaluation-patterns/_template.md` | `title`, `slug`, `asset_kind`, `source_papers` |
| `new-failure-mode` | `assets/failure-modes/_template.md` | `title`, `slug`, `asset_kind`, `source_papers` |
| `new-negative-asset` | `assets/negative-assets/_template.md` | `title`, `slug`, `asset_kind`, `source_papers` |
| `new-lint-report` | `logs/lint-template.md` | — |

## Operational Helpers

- `reviews/ingest-checklist.md` is the canonical ingest checklist.
- `logs/lint-template.md` is the starting point for lint reports.
