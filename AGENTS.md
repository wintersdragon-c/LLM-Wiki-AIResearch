# Repository Guidelines

## What This Repository Is

This repository is an LLM-maintained research wiki for computer science work. It is a persistent markdown knowledge base, not an application codebase.

Read `LLM-wiki.md` for the pointer to the original Karpathy gist, then read `research-map.md`, `index.md`, and the relevant workflow document before making structural wiki edits.

## Architecture

- `sources/` contains user-owned source folders. Read from it during ingest; do not treat it as compiled wiki output.
- Root-level wiki directories (`topics/`, `papers/`, `ideas/`, `assets/`, `reviews/`, `logs/`) contain LLM-maintained markdown pages.
- `docs/workflows/` contains repository-owned, skill-shaped workflows. These are the authoritative procedure documents for ingest, query, maintain, and doctor operations.
- `AGENTS.md` is the root router and hard-constraint document, not the place for full procedure bodies.

Root-level special files:

- `index.md` — content catalog; read before every Query.
- `log.md` — append-only global timeline; append after every content-changing operation.
- `research-map.md` — authoritative research space map; read before every Map stage.

## Required Read Order

- For natural-language routing: read `docs/workflows/router.md`.
- For source ingest: read `docs/workflows/ingest.md`.
- For research questions over existing wiki content: read `docs/workflows/query.md`.
- For consolidation and re-review: read `docs/workflows/maintain.md`.
- For validation and lint-style checks: read `docs/workflows/doctor.md`.
- Before creating or moving pages: read the target directory `README.md` resolver.

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

The `paper_id` field in paper frontmatter must match the file slug exactly.

## Source Intake

Use `sources/<slug>/` as the intake unit.

- A source folder may contain any mix of files: `paper.pdf`, `notes.md`, `repo/`, `figures/`, copied markdown, screenshots, or supporting artifacts.
- `notes.md` is recommended but optional. Treat it as the user's intent layer.
- Never require the user to pre-fill multiple metadata files before ingest.
- Do not create compiled wiki pages inside `sources/`.

## Intent Routing

Interpret user requests by research intent, not only by explicit workflow verbs.

- If the user mentions `sources/...` and asks an analytic question, check existing coverage in `papers/`, `topics/`, `ideas/`, and `assets/`.
- If coverage is missing or stale, run full ingest without pausing for a separate confirmation step, then continue into the requested analysis.
- Read `notes.md` inside a referenced source folder when present.
- Use semantic routing across Chinese and English requests.
- Reuse existing structured pages when coverage is already sufficient.
- Ask a clarifying question only when the referenced path does not exist, multiple targets conflict materially, or the analysis target is too ambiguous to resolve from repository context.
- Use `docs/workflows/router.md` as the concrete route map.

## Page Conventions

- Use `[[WikiLink]]` syntax for internal references.
- Every non-guide wiki page must have YAML frontmatter with at minimum: `type`, `title`, `slug`, `tags`, `created`, `updated`.
- Schema files (`AGENTS.md`, `CLAUDE.md`), reference documents (`LLM-wiki.md`), `docs/` planning artifacts, and directory guide files (`README.md`) are exempt from the universal schema.
- `hypothesis` pages also require `status`, `confidence`, `topic_refs`, and `source_papers`.
- `proposal` pages also require `status`, `confidence`, `positioning`, `topic_refs`, and `source_papers`.
- `paper` pages also require `paper_id`, `topic_refs`, and `inspired_ideas`.
- `asset` pages also require `asset_kind` and `source_papers`.

## Idea Lifecycle

- Create hypothesis cards during `Mutate` or `Compose`.
- Promote a hypothesis to proposal only when at least two independent sources support the core assumption and a minimal experiment can be sketched.
- If a paper is positioned as `overturns-assumption`, mark affected hypotheses or proposals as `under-review` until resolved.
- For evolving idea pages, rewrite current-judgment sections and append evidence below `## Evidence Timeline`.

## Bidirectional Tracking

- Each relevant `papers/*.md` page maintains `inspired_ideas:`.
- Each idea page maintains `source_papers:`.
- If finer provenance matters, record section-level evidence in the body under `## Source Mechanisms` or `## Evidence Timeline`.
- Keep both directions consistent during every Update.

## Topic Pages

- Default topic pages live at `topics/<topic-name>.md`.
- Topic map pages define the direction, core sub-problems, current judgment, and known gaps.
- Create `topics/<topic-name>/<subtopic>.md` only when a topic accumulates 5+ papers or clear route divergence appears.

## Logging

Append operation summaries to `log.md` using:

`## [YYYY-MM-DD] <operation> | <title or summary>`

Where `<operation>` is one of: `ingest`, `query`, `lint`, `bootstrap`, `dry-run`.

Write detailed audit records to `logs/`. Read-only intermediate checks should stay in `logs/` only and should not create a `log.md` entry unless they change wiki content or produce a durable maintenance decision.

## Action To Workflow Map

| Action | Workflow |
|--------|----------|
| natural-language route | `docs/workflows/router.md` |
| source ingest | `docs/workflows/ingest.md` |
| research query | `docs/workflows/query.md` |
| consolidation / re-review | `docs/workflows/maintain.md` |
| validation / lint / doctor | `docs/workflows/doctor.md` |
