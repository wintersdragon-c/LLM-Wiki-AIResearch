# Workflow Operationalization Design

## Purpose

LLM-WIKI has enough schema to ingest and organize research material, but the operational rules are still concentrated in `AGENTS.md`. As the wiki grows, that creates two risks:

1. `AGENTS.md` becomes a long context dump instead of a stable router.
2. New LLM sessions know the global rules but do not have strong local filing rules for each directory.

This design applies the strongest transferable GBrain patterns without copying its database layer: thin root instructions, skill-shaped workflow documents, directory-level resolvers, and a deterministic doctor workflow.

## Design Thesis

Use skill architecture conceptually now, but do not bind the repository to one agent's local skill system yet.

- `AGENTS.md` remains the root router and hard-constraint document.
- `docs/workflows/*.md` become repository-owned, skill-shaped workflows that any LLM agent can read.
- Directory `README.md` files become local resolvers with explicit placement rules.
- Optional real Codex/Claude skills are deferred to Phase 2 after the workflows stabilize through real ingests.

This keeps the project portable for GitHub and Obsidian users while preserving the stronger operational discipline of "fat skills, thin harness."

## Scope

Phase 1 includes:

- Slim `AGENTS.md` by replacing detailed procedure bodies with pointers to workflow documents while preserving hard rules.
- Add workflow documents:
  - `docs/workflows/ingest.md`
  - `docs/workflows/query.md`
  - `docs/workflows/maintain.md`
  - `docs/workflows/doctor.md`
- Keep `docs/workflows/router.md` as the semantic route map and update it to delegate into the new workflow documents.
- Strengthen directory README resolvers for:
  - `sources/`
  - `papers/`
  - `topics/`
  - `ideas/hypothesis/`
  - `ideas/proposals/`
  - `assets/mechanisms/`
  - `assets/experiment-patterns/`
  - `assets/evaluation-patterns/`
  - `assets/failure-modes/`
  - `assets/negative-assets/`
  - `reviews/`
  - `logs/`
- Update hypothesis and proposal templates to support a current-judgment plus evidence-timeline pattern.
- Add a doctor workflow that specifies deterministic checks, but do not implement a full CLI script in Phase 1.

Phase 1 excludes:

- Postgres, PGLite, vector search, MCP, or Obsidian plugin implementation.
- Local real skill installation under `~/.codex/skills` or agent-specific skill registries.
- Automatic nightly jobs or cron.
- A full CLI harness.

## AGENTS.md Role

`AGENTS.md` should be reduced to:

- Repository purpose and architecture.
- File naming rules and universal schema requirements.
- Source intake and intent routing invariants.
- Required read order:
  - `research-map.md` before Map.
  - `index.md` before Query.
  - `docs/workflows/router.md` for natural-language routing.
  - The matching `docs/workflows/*.md` document before executing a workflow.
  - The target directory `README.md` before creating or moving pages.
- Links to workflow documents instead of embedding every detailed step.
- Logging and bidirectional-link invariants that must never be skipped.

It should not contain the full 11-step ingest instructions inline after the new `ingest.md` exists. It should point to them.

## Workflow Documents

Each workflow document should be written like a repository-owned skill:

- Trigger conditions.
- Required inputs.
- Files to read first.
- Step-by-step execution.
- Write targets.
- Verification checks.
- Prohibited actions.
- Logging requirements.

### `docs/workflows/ingest.md`

Purpose: Execute full research material ingest from `sources/<slug>/`.

Must include:

- Parse -> Appreciate -> Map -> Positioning -> Diff -> Abstract -> Critique -> Mutate -> Compose -> Update -> Log.
- Notes.md as explicit user intent layer.
- Output targets for `papers/`, `topics/`, `assets/`, `ideas/`, `reviews/`, `index.md`, and `log.md`.
- Weak-compose vs strong-compose rule.
- Idea promotion and decay checks.
- Requirement to read relevant directory README resolvers before creating files.

### `docs/workflows/query.md`

Purpose: Answer research questions against the existing wiki and write back durable outputs when useful.

Must include:

- Read `research-map.md` and `index.md` first.
- Use diverse context, not only the most similar paper:
  - topic map or survey
  - paper pages
  - idea pages
  - assets
  - reviews or logs when relevant
- If the query references new `sources/...` material, return to router coverage-check behavior and ingest first if needed.
- Save durable synthesis to `reviews/` or update topic/idea pages only when the output is reusable.

### `docs/workflows/maintain.md`

Purpose: Periodic wiki consolidation and re-review.

Must include:

- Research equivalent of GBrain's dream cycle:
  - source sweep
  - citation/source audit
  - idea re-review
  - research-map drift check
  - index refresh
- Manual trigger by user or agent, not automatic nightly cron in Phase 1.
- How to mark stale hypotheses or proposals as `under-review`.

### `docs/workflows/doctor.md`

Purpose: Specify deterministic checks that can later become a script.

Must include:

- Frontmatter required fields.
- Slug and filename rules.
- `paper_id` equals paper slug.
- `source_papers` and `inspired_ideas` bidirectional consistency.
- `index.md` coverage.
- Empty/stub section detection.
- Placeholder date detection.
- LLM preamble and markdown code-fence wrapper detection.
- Broken WikiLink or citation-like bracket detection where feasible.
- `sources/` folders with no structured coverage.
- Dry-run/fix separation: only trivial artifacts such as LLM preambles and wrapping code fences are auto-fix candidates.

## Directory Resolver Format

Each target directory README should use this structure:

```markdown
# Directory Purpose

## What Goes Here

## What Does Not Go Here

## Neighboring Directories To Check

## Required Frontmatter

## Naming Pattern

## Good Example

## Common Wrong Placement
```

The goal is not long documentation. The goal is strong filing disambiguation. For example:

- `failure-modes/` stores observed ways methods fail.
- `negative-assets/` stores design choices that look attractive but should usually be avoided.
- `experiment-patterns/` stores reusable experimental designs.
- `evaluation-patterns/` stores reusable measurement or benchmark strategies.
- `reviews/` stores synthesized analysis across materials, not operation logs.
- `logs/` stores audit trails and process records, not research arguments.

## Hypothesis And Proposal Timeline Pattern

Adopt a lightweight version of GBrain's compiled truth + timeline for evolving idea pages.

Hypothesis pages should include:

- `## Current Claim`
- `## Source Mechanisms`
- `## Feasibility Judgment`
- `## Minimal Test`
- `---`
- `## Evidence Timeline`

Proposal pages should include:

- `## Current Proposal`
- `## Motivation`
- `## Core Mechanism`
- `## Minimal Experiment`
- `## Risks And Assumption Holes`
- `## Positioning`
- `## Relationship To Existing Ideas`
- `---`
- `## Evidence Timeline`

The rule is:

- Rewrite the current-judgment sections when evidence changes.
- Append evidence/re-review entries below `## Evidence Timeline`.
- Do not append stale claims to the top as historical clutter.

This pattern should be introduced in templates first. Existing pages can migrate only when touched.

## Optional Real Skills: Phase 2

After several real ingests validate the workflow docs, add optional agent-specific skills. These should not duplicate the workflows. They should be thin entrypoints that point back into the repository:

- `.agents/skills/llm-wiki-ingest/SKILL.md`
- `.agents/skills/llm-wiki-query/SKILL.md`
- `.agents/skills/llm-wiki-maintain/SKILL.md`
- `.agents/skills/llm-wiki-doctor/SKILL.md`

Each real skill should contain:

- A short trigger description.
- A command to read the matching `docs/workflows/*.md`.
- A reminder to read the target directory README before writing files.

This preserves portability: the repository works without installed skills, but agents that support skills get stronger automatic routing.

## Success Criteria

The operationalization pass is successful when:

- A new LLM session can ingest a source by reading `AGENTS.md`, `router.md`, `ingest.md`, and target directory README files without returning to the original long spec.
- `AGENTS.md` is shorter and points to workflow documents for procedure details.
- Each key directory README can answer "does this page belong here or somewhere else?"
- Hypothesis and proposal templates support current-judgment plus evidence timeline.
- Doctor checks are specified clearly enough to become a deterministic script later.
- No database, vector index, MCP, or real skill installation is required for Phase 1.

## Risks

- Over-splitting documentation can make the system harder to navigate. Mitigation: `AGENTS.md` remains the root router and `router.md` maps user intent to workflow documents.
- Directory README files can become repetitive. Mitigation: keep each resolver short and focused on disambiguation.
- Doctor workflow may be mistaken for implemented tooling. Mitigation: explicitly label it as a workflow/specification until a script exists.
- Real skills may become agent-specific too early. Mitigation: defer them to Phase 2 and keep repository workflow docs authoritative.
