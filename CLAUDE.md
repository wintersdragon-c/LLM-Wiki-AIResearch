# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This repository is an LLM-maintained research wiki. There is no build system or test runner; the repository itself is the artifact.

Read `AGENTS.md` for the full canonical schema and workflow rules. Read `LLM-wiki.md` for the original Karpathy gist reference, then read `docs/superpowers/specs/2026-04-10-research-wiki-design.md` and `research-map.md` before structural edits.

## Architecture

Three layers:

- `sources/` — user-owned source folders for ingest input; never write compiled wiki output here
- root wiki directories (`topics/`, `papers/`, `ideas/`, `assets/`, `reviews/`, `logs/`) — LLM-maintained markdown pages
- schema files — `AGENTS.md` and `CLAUDE.md`

Key files (read before every operation):

- `index.md` — content catalog; read before every Query
- `log.md` — append-only global timeline; append after every ingest, query-that-writes, lint, and proposal promotion
- `research-map.md` — authoritative research space map; read before every Map stage and structural edit

## Intent Routing

Route user requests by research intent, not by exact command verbs. Use `docs/workflows/router.md` as the concrete route map.

- If a user mentions `sources/...` material and asks an analytic question, run a **coverage check** first (inspect `papers/`, `topics/`, `ideas/`, `assets/` for existing structured coverage).
- If coverage is missing or stale, run full ingest without pausing for confirmation, then continue into the requested analysis.
- Read `sources/<slug>/notes.md` when present — treat it as an explicit intent layer that may override the chat-message signal.
- Ask a clarifying question only when the referenced path does not exist, targets conflict materially, or the request is too ambiguous to resolve from repository state.

## Ingest Pipeline

Run all 11 stages in order: `Parse → Appreciate → Map → Positioning → Diff → Abstract → Critique → Mutate → Compose → Update → Log`

- **Parse**: title, authors, venue, year, problem, method, results, limitations.
- **Appreciate**: core innovation, real advance over prior work, most transferable design.
- **Map**: read `research-map.md`; identify which topic pages this paper belongs to.
- **Positioning**: classify as `opens-direction`, `fills-gap`, `overturns-assumption`, or `engineering-reinforcement`; apply the matching update target.
- **Diff**: record contradictions, reinforcements, newly tracked variables, superseded claims.
- **Abstract**: extract mechanisms, experiment patterns, evaluation patterns, failure modes, negative assets.
- **Critique**: assumption holes, missing baselines, overclaims, reproducibility concerns; extract negative assets.
- **Mutate**: generate internal design variants; convert promising ones to `ideas/hypothesis/` cards.
- **Compose**: weak-compose (`<10` papers) marks cards `confidence: low`; strong-compose (`>=10`) uses normal confidence.
- **Update**: update all affected pages, maintain bidirectional links, refresh `index.md`.
- **Log**: append to `log.md`; write detailed audit to `logs/` for substantial sessions.

## File Naming

All names are kebab-case slugs. No spaces, uppercase, or underscores.

| Directory | Pattern | Example |
|-----------|---------|---------|
| `topics/` | `<slug>.md` | `llm-reasoning.md` |
| `topics/<slug>/` | `<subtopic-slug>.md` | `chain-of-thought.md` |
| `papers/` | `<firstauthor>-<year>-<keyword>.md` | `wei-2022-chain-of-thought.md` |
| `ideas/hypothesis/` | `h-<slug>.md` | `h-process-reward-scaling.md` |
| `ideas/proposals/` | `p-<slug>.md` | `p-process-reward-scaling.md` |
| `assets/` subdirs | `<slug>.md` | `chain-of-thought-prompting.md` |
| `logs/` | `<YYYY-MM-DD>-<slug>.md` | `2026-04-11-ingest-audit.md` |

The `paper_id` frontmatter field must match the file slug exactly (without `.md`).

## Frontmatter Schema

Every non-guide wiki page requires at minimum:

```yaml
type: <page-type>
title: "Human-readable title"
slug: <file-slug-without-extension>
tags: [<tag>, ...]
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

Additional required fields by type:

- **paper**: `paper_id`, `topic_refs`, `inspired_ideas`
- **hypothesis**: `status` (`hypothesis`|`under-review`), `confidence` (`low`|`medium`|`high`), `topic_refs`, `source_papers`
- **proposal**: `status`, `confidence`, `positioning`, `topic_refs`, `source_papers`
- **topic / topic-survey**: `subtopics`, `papers`, `ideas`, `open_questions`
- **asset** (mechanism, experiment-pattern, evaluation-pattern, failure-mode, negative-asset): `asset_kind`, `source_papers`

Schema files, `docs/` planning artifacts, and `README.md` files are exempt.

## Logging Rules

Append to `log.md` (global timeline) for: every ingest, every query that writes new pages, every lint, every proposal promotion.

Write to `logs/` only (no `log.md` entry) for: intermediate checks, batch link-repair details, operations that do not change wiki content.

`dry-run` operations use operation type `dry-run` in `log.md`:
```
## [YYYY-MM-DD] dry-run | <title>
```

Log format: `## [YYYY-MM-DD] <operation> | <title or summary>`

## Conventions

- Use `[[WikiLink]]` for all internal references.
- Promote a hypothesis to proposal only when at least two independent sources support the core assumption and a minimal experiment can be sketched.
- If a paper is positioned as `overturns-assumption`, mark affected hypotheses/proposals as `under-review`.
- Create survey subpages (`topics/<slug>/<subtopic>.md`) only when a topic accumulates 5+ papers or route divergence appears.
- Use `reviews/ingest-checklist.md` as the canonical ingest checklist and `logs/lint-template.md` as the starting point for lint reports.
