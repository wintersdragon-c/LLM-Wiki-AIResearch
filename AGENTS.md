# Repository Guidelines

## What This Repository Is

This repository is an LLM-maintained research wiki for computer science work. The repository is a persistent, compounding markdown knowledge base rather than an application codebase.

Read `LLM-wiki.md`, `docs/superpowers/specs/2026-04-10-research-wiki-design.md`, and `research-map.md` before making structural edits.

## Architecture

Three layers:

- `raw/` contains immutable source materials. Never edit files in `raw/`.
- Root-level wiki directories (`topics/`, `papers/`, `ideas/`, `assets/`, `reviews/`, `logs/`) contain LLM-maintained markdown pages.
- Schema files (`AGENTS.md`, `CLAUDE.md`) define workflows and conventions.

## Core Operations

### Ingest
Run the approved pipeline in order:
`Parse -> Appreciate -> Map -> Positioning -> Diff -> Abstract -> Critique -> Mutate -> Compose -> Update -> Log`

#### Parse
- Extract title, authors, venue, year, problem statement, proposed method, key results, and author-stated limitations.

#### Appreciate
- Answer: what is the core innovation, what is the actual advance over prior work, what is most worth learning from, which experiment is most elegant, and what transfers to other problems.

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
- After critique, extract `negative-assets` for designs that look reasonable but are traps.

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

#### Log
- Append a summary to `log.md`.
- Write detailed audit records to `logs/` when the session is substantial.

### Query
Read `research-map.md` and `index.md` first, then relevant topic, paper, idea, and asset pages. File valuable outputs back into the wiki.

### Lint
Check for contradictions, stale claims, orphan pages, missing cross-links, upgradeable hypotheses, stale `under-review` ideas, and `research-map.md` drift.

## Page Conventions

- Use `[[WikiLink]]` syntax for all internal references.
- Every page must have YAML frontmatter with `type`, `tags`, and `updated`.
- `hypothesis` pages also require `status`, `confidence`, `sources`, and `created`.
- `proposal` pages also require `status`, `sources`, `positioning`, `created`, and `updated`.
- `paper` pages should maintain `inspired_ideas` when relevant.

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

`## [YYYY-MM-DD] ingest | Title`

Put detailed audit trails in `logs/`.
