# Workflow Operationalization Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Turn the workflow operationalization spec into repository-owned, skill-shaped workflow documents, stronger directory resolvers, slimmer root instructions, and timeline-aware idea templates.

**Architecture:** Keep the implementation markdown-only. `AGENTS.md` becomes the thin root router, `docs/workflows/*.md` hold detailed executable workflows, directory `README.md` files become local resolvers, and idea templates adopt a current-judgment plus evidence-timeline structure. No CLI, database, vector index, MCP, Obsidian plugin, cron, or real `.agents/skills/` directory is added in this phase.

**Tech Stack:** Markdown, Obsidian WikiLinks, existing repository schema, `rg`, `wc`, `git`

---

### Task 1: Add Skill-Shaped Workflow Documents

**Files:**
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/ingest.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/query.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/maintain.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/doctor.md`

- [ ] **Step 1: Write `docs/workflows/ingest.md`**

Write this exact file:

```markdown
# Ingest Workflow

Use this workflow when a user references a `sources/<slug>/` folder or asks to organize new research material.

## Trigger Conditions

- The user says they added, supplemented, copied, or placed material under `sources/<slug>/`.
- The user asks to understand, critique, reproduce, combine, or extract ideas from a source folder.
- `docs/workflows/router.md` routes the request to ingest because structured coverage is missing or stale.

## Required Inputs

- A source folder path such as `sources/hacrl/`.
- Optional `sources/<slug>/notes.md` with the user's intent.
- Existing wiki state from `research-map.md`, `index.md`, and relevant directory README resolvers.

## Read First

1. `docs/workflows/router.md`
2. `research-map.md`
3. `index.md`
4. `sources/README.md`
5. The target `sources/<slug>/` folder and `sources/<slug>/notes.md` when present
6. The README resolver for every output directory before writing there

## Pipeline

Run these stages in order:

`Parse -> Appreciate -> Map -> Positioning -> Diff -> Abstract -> Critique -> Mutate -> Compose -> Update -> Log`

### Parse

- Extract title, authors, venue, year, problem statement, proposed method, key results, and author-stated limitations.
- If the source includes code, figures, screenshots, or a repo snapshot, record how those artifacts relate to the paper or research question.

### Appreciate

Answer explicitly:

1. What is the core innovation after removing marketing language?
2. What is the real advance over prior work?
3. Which single design is most worth learning from?
4. Which experiment is most elegant?
5. Which framing, task definition, or evaluation approach is most inspiring?
6. What is transferable to other problems?

### Map

- Read `research-map.md` before assigning topics.
- Identify relevant topic map pages and survey subpages.
- Create a topic survey subpage only when the topic has 5+ papers or a clear route divergence.

### Positioning

Classify the source as exactly one primary role:

- `opens-direction`
- `fills-gap`
- `overturns-assumption`
- `engineering-reinforcement`

Apply the matching update target:

- `opens-direction`: update `research-map.md` and possibly create a new topic page.
- `fills-gap`: update an existing topic page or known-gap section.
- `overturns-assumption`: trigger idea re-review and critique updates.
- `engineering-reinforcement`: update experiment-pattern and method-route pages.

### Diff

- Compare against current wiki pages.
- Record contradictions, reinforcements, newly tracked variables, and superseded claims.

### Abstract

Extract reusable assets when present:

- mechanisms
- experiment patterns
- evaluation patterns
- failure modes
- transferable objectives, inductive biases, or data tricks

### Critique

- Record assumption holes, experimental blind spots, missing baselines, overclaims, and reproducibility concerns.
- After critique, extract negative assets for designs that look reasonable but are traps.

### Mutate

- Generate internal variants by changing one meaningful design variable.
- Convert promising variants into `ideas/hypothesis/` cards.

### Compose

- Always run Compose.
- If the wiki has fewer than 10 paper pages, use weak-compose and mark new combination hypotheses with `confidence: low`.
- If the wiki has 10 or more paper pages, use strong-compose and generate normal cross-paper combinations.

### Update

- Create or update affected pages in `papers/`, `topics/`, `assets/`, `ideas/`, and `reviews/`.
- Maintain `papers/*.md` `inspired_ideas` and idea `source_papers` bidirectionally.
- Check whether any hypothesis should be promoted to proposal.
- Add or refresh every new or significantly changed page in `index.md`.

### Log

- Append to `log.md` using `## [YYYY-MM-DD] ingest | <title or summary>`.
- Write a detailed audit to `logs/<YYYY-MM-DD>-<slug>-ingest.md` when the session changes multiple pages or makes a durable research decision.

## Write Targets

- `papers/`: one structured page per ingested paper or paper-like source.
- `topics/`: topic maps and conditional survey subpages.
- `assets/`: reusable mechanisms, experiment patterns, evaluation patterns, failure modes, and negative assets.
- `ideas/hypothesis/`: fast-capture idea cards from Mutate or Compose.
- `ideas/proposals/`: promoted ideas with at least two independent sources and a minimal experiment.
- `reviews/`: durable synthesis across materials.
- `index.md`: catalog entries for new or significantly changed pages.
- `log.md` and `logs/`: operation summary and audit trail.

## Prohibited Actions

- Do not require the user to pre-fill metadata beyond optional `notes.md`.
- Do not create compiled wiki pages inside `sources/`.
- Do not create topic survey subpages before the 5+ papers or route-divergence trigger.
- Do not create real `.agents/skills/` files in Phase 1.

## Verification

- Run `rg -n "source_papers|inspired_ideas|paper_id|type:|title:|slug:" papers ideas assets topics reviews` after a substantial ingest.
- Run `rg -n "\\[\\[.*\\]\\]" index.md research-map.md topics papers ideas assets` to spot missing or malformed WikiLinks.
- Run the doctor workflow in `docs/workflows/doctor.md` when the ingest creates or changes multiple pages.
```

- [ ] **Step 2: Write `docs/workflows/query.md`**

Write this exact file:

```markdown
# Query Workflow

Use this workflow when the user asks a research question against the existing wiki.

## Trigger Conditions

- The user asks to compare papers, topics, mechanisms, assets, or ideas already represented in the wiki.
- The user asks for the state of a research direction.
- The user asks whether an existing idea is supported, contradicted, or worth upgrading.

If the request references new `sources/...` material, return to `docs/workflows/router.md` and run the coverage-check behavior before answering.

## Required Inputs

- The user's research question.
- `research-map.md`
- `index.md`
- Relevant topic, paper, idea, asset, review, and log pages.

## Read First

1. `docs/workflows/router.md`
2. `research-map.md`
3. `index.md`
4. Relevant directory README resolvers if the answer may write new durable pages

## Context Diversity Rule

Do not answer from the most similar paper alone. Check diverse page types:

- topic map or survey page
- paper pages
- idea hypothesis or proposal pages
- assets pages
- reviews or logs when they contain relevant synthesis or audit history

If one of these page classes has no relevant coverage, say that internally in the analysis and avoid inventing it.

## Execution

1. Identify the research question and requested output type.
2. Read `research-map.md` to place the question in the global research space.
3. Read `index.md` to find candidate pages.
4. Read the relevant topic map or survey page.
5. Read relevant paper pages.
6. Read relevant idea pages.
7. Read relevant asset pages.
8. Synthesize an answer that distinguishes known evidence, inference, and speculation.
9. Decide whether the output is durable.
10. If durable, update `reviews/`, `topics/`, or `ideas/` after reading the target directory README resolver.
11. If any page is created or significantly changed, update `index.md` and append to `log.md`.

## Durable Output Rules

- Use `reviews/` for cross-paper synthesis, comparison, or reusable research judgment.
- Update `topics/` when the answer changes the research map, known gaps, or route split.
- Update `ideas/hypothesis/` or `ideas/proposals/` when the answer changes an idea's current claim, feasibility, evidence timeline, or review status.
- Do not write a new page for a one-off answer that will not be reused.

## Verification

- If durable output was written, run `rg -n "<new-slug>|<changed-slug>" index.md log.md`.
- If an idea was changed, run `rg -n "source_papers|Evidence Timeline|under-review|confidence" ideas`.
- If the answer changed topic structure, run `rg -n "<topic-slug>|<subtopic-slug>" research-map.md topics`.
```

- [ ] **Step 3: Write `docs/workflows/maintain.md`**

Write this exact file:

```markdown
# Maintain Workflow

Use this workflow for periodic wiki consolidation, stale-claim review, and research-map hygiene.

## Trigger Conditions

- The user asks to maintain, clean, audit, consolidate, or lint the wiki.
- A substantial ingest changed multiple pages.
- A paper is positioned as `overturns-assumption`.
- The wiki has accumulated unreviewed source folders, stale hypotheses, or unclear topic drift.

## Required Inputs

- `research-map.md`
- `index.md`
- `log.md`
- `sources/`
- Relevant `topics/`, `papers/`, `ideas/`, `assets/`, and `reviews/` pages

## Read First

1. `docs/workflows/doctor.md`
2. `research-map.md`
3. `index.md`
4. Directory README resolvers for directories that may be modified

## Maintenance Cycle

### Source Sweep

- Scan `sources/` for source folders.
- For each source folder, check whether corresponding structured coverage exists in `papers/`, `topics/`, `ideas/`, or `assets/`.
- Report uncovered source folders as ingest candidates.
- Do not modify `sources/` during this step.

### Citation And Source Audit

- Check idea pages for `source_papers` entries that point to existing paper pages.
- Check paper pages for `inspired_ideas` entries that point to existing idea pages.
- Flag broken or one-way provenance links for repair.

### Idea Re-Review

- Review hypotheses and proposals marked `under-review`.
- Review hypotheses whose evidence timeline or source papers changed since the last maintenance pass.
- If a new paper overturns an assumption, mark affected ideas as `under-review` until resolved.

### Research-Map Drift Check

- Compare `research-map.md` with topic pages and recent paper pages.
- Flag missing topics, stale relationships, or route divergences.
- Create a topic survey subpage only when there are 5+ papers under the topic or a clear route divergence.

### Index Refresh

- Check that new or significantly changed pages appear in `index.md`.
- Refresh stale descriptions when page purpose changed.

## Write Targets

- `ideas/hypothesis/` and `ideas/proposals/` for review status and evidence timeline changes.
- `topics/` and `research-map.md` for topic drift.
- `index.md` for catalog refresh.
- `logs/` for detailed maintenance audit.
- `log.md` only when maintenance changes wiki content or creates a durable maintenance decision.

## Prohibited Actions

- Do not run this as an automatic nightly cron in Phase 1.
- Do not create database, vector, MCP, or plugin infrastructure.
- Do not migrate all old pages just to match a new template; migrate pages when touched.

## Verification

- Run the doctor workflow after maintenance changes.
- Run `rg -n "under-review|Evidence Timeline|source_papers|inspired_ideas" ideas papers`.
- Run `rg -n "research-map|topic|survey" logs log.md` after writing maintenance records.
```

- [ ] **Step 4: Write `docs/workflows/doctor.md`**

Write this exact file:

````markdown
# Doctor Workflow

Use this workflow to run deterministic wiki checks. This is a workflow specification, not an implemented CLI script in Phase 1.

## Trigger Conditions

- The user asks to lint, audit, doctor, validate, or check the wiki.
- A substantial ingest or maintenance pass changed multiple pages.
- Before publishing or pushing a release.

## Required Inputs

- Repository root
- `AGENTS.md`
- `index.md`
- `research-map.md`
- `sources/`, `topics/`, `papers/`, `ideas/`, `assets/`, `reviews/`, and `logs/`

## Read First

1. `AGENTS.md`
2. `docs/workflows/maintain.md`
3. Directory README resolvers for directories being checked

## Checks

### Frontmatter

- Every non-guide wiki page must include `type`, `title`, `slug`, `tags`, `created`, and `updated`.
- `AGENTS.md`, `CLAUDE.md`, `LLM-wiki.md`, `README.md`, `docs/`, and directory guide files are exempt.
- `papers/*.md` must include `paper_id`, `topic_refs`, and `inspired_ideas`.
- `ideas/hypothesis/*.md` must include `status`, `confidence`, `topic_refs`, and `source_papers`.
- `ideas/proposals/*.md` must include `status`, `confidence`, `positioning`, `topic_refs`, and `source_papers`.
- `assets/**/*.md` must include `asset_kind` and `source_papers`.

### Naming

- All wiki filenames must be kebab-case.
- Paper `paper_id` must match the file slug without `.md`.
- Hypothesis files must start with `h-`.
- Proposal files must start with `p-`.
- Log files in `logs/` must start with `YYYY-MM-DD-`.

### Bidirectional Links

- For each paper `inspired_ideas` entry, the target idea page must include the paper in `source_papers`.
- For each idea `source_papers` entry, the target paper page should include the idea in `inspired_ideas` when the idea was inspired by that paper.
- Broken WikiLinks should be reported with file path and link text.

### Index Coverage

- Every new or significantly changed non-guide wiki page should have an `index.md` entry.
- The entry format should remain `- [[path]] | type | description`.

### Content Artifacts

- Report LLM preambles such as `Of course`, `Certainly`, `Here is`, and `I've created` at the top of wiki pages.
- Report pages wrapped entirely in ```markdown or ```md code fences.
- Report placeholder dates such as `YYYY-MM-DD`, `XX-XX`, and `2026-XX-XX`.
- Report empty sections or placeholder section bodies such as `[No data yet]`.
- Report unclosed citation-like bracket text such as `[Source: ...`.

### Sources Coverage

- Scan `sources/` for source folders.
- Report source folders that have no apparent structured coverage in `papers/`, `topics/`, `ideas/`, or `assets/`.

## Severity Levels

- `error`: schema violations, broken required links, malformed frontmatter, or filename/slug mismatches.
- `warning`: missing index entries, stale source coverage, empty sections, placeholder dates, or one-way provenance links.
- `info`: optional cleanup, old untouched templates, or suggestions for future script automation.

## Fix Policy

- Auto-fix candidates: LLM preambles and wrapping markdown code fences.
- Manual-fix items: frontmatter, bidirectional links, placeholder dates, empty sections, missing index entries, source coverage, and broken WikiLinks.
- Dry-run output must describe changes without writing files.

## Reporting Format

Use this structure for detailed audit reports in `logs/<YYYY-MM-DD>-doctor-audit.md`:

```markdown
---
type: log
title: "Doctor Audit"
slug: YYYY-MM-DD-doctor-audit
tags: [doctor, lint, maintenance]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Doctor Audit

## Summary

- errors: 0
- warnings: 0
- info: 0
- auto_fix_candidates: 0

## Findings

| severity | rule | file | fixable | message | next action |
|----------|------|------|---------|---------|-------------|
```

Append to `log.md` only if the doctor run changes wiki content or produces a durable maintenance decision. Read-only checks should stay in `logs/` only.

## Verification

- Run `rg -n "error|warning|info|fixable|next action" logs` after writing a doctor audit.
- Run `rg -n "doctor \\|" log.md` only when the doctor run changed wiki content.
````

- [ ] **Step 5: Verify all workflow files contain required anchors**

Run:

```bash
rg -n "Parse -> Appreciate|weak-compose|strong-compose|Context Diversity Rule|Source Sweep|Reporting Format|Auto-fix candidates" /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows
```

Expected: matches appear in `ingest.md`, `query.md`, `maintain.md`, and `doctor.md`.

- [ ] **Step 6: Commit workflow documents**

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI add \
  /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/ingest.md \
  /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/query.md \
  /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/maintain.md \
  /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/doctor.md
git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: add research wiki workflow runbooks"
```

### Task 2: Slim `AGENTS.md` Into The Root Router

**Files:**
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md`

- [ ] **Step 1: Replace `AGENTS.md` with the slim root-router version**

Replace the entire file with this exact content:

```markdown
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
```

- [ ] **Step 2: Verify `AGENTS.md` is slim enough**

Run:

```bash
wc -l /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md
```

Expected: output shows `180` lines or fewer for `AGENTS.md`.

- [ ] **Step 3: Verify workflow pointers replaced inline procedure bodies**

Run the positive workflow pointer check:

```bash
rg -n "docs/workflows/ingest.md|docs/workflows/query.md|docs/workflows/maintain.md|docs/workflows/doctor.md" /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md
```

Expected: all four workflow paths are present.

Run the negative inline-procedure check:

```bash
if rg -n "Parse -> Appreciate|### Appreciate|### Compose" /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md; then exit 1; else exit 0; fi
```

Expected: command exits with status 0 because `Parse -> Appreciate`, `### Appreciate`, and `### Compose` do not appear in `AGENTS.md`.

- [ ] **Step 4: Commit the slim root router**

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI add /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md
git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: slim agents guide into workflow router"
```

### Task 3: Update Router To Delegate To Workflow Documents

**Files:**
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md`

- [ ] **Step 1: Replace `docs/workflows/router.md` with the delegated router**

Replace the entire file with this exact content:

````markdown
# Intent Router

This document routes natural-language research requests into repository workflows.

## Routing Principles

- Route by meaning, not by exact string match.
- Treat `sources/<slug>/notes.md`, when present, as an explicit intent layer.
- If a user mentions new `sources/...` material and also asks an analytic question, run the coverage check and ingest first when coverage is missing or stale.
- Reuse existing structured coverage when it is already adequate.
- Ask a clarifying question only when the referenced path does not exist, the target is materially ambiguous, or repository state is insufficient to resolve the request.
- After routing, read the selected workflow document before executing.

## Coverage Check Before Ingest

Before ingesting a referenced source folder, inspect:

- `papers/` for an existing canonical paper page
- `topics/` for relevant topic coverage
- `ideas/` for existing linked hypotheses or proposals
- `assets/` for already-extracted mechanisms, experiment patterns, evaluation patterns, failure modes, or negative assets

If coverage is missing or stale, run `docs/workflows/ingest.md` before continuing the requested analysis.

## Intent Families

### New Material + Understand

Typical user language:

- Chinese: “我补充了…帮我看看这篇做了什么”, “读一下这个材料”, “整理一下这个 source”
- English: “I added ... help me understand it”, “read this source”, “organize this material”

Default route:

`coverage check -> docs/workflows/ingest.md -> appreciate -> critique -> update`

### New Material + Combine

Typical user language:

- Chinese: “能不能和 PDA 结合”, “和另一篇拼一下”, “借鉴到 GRPO 里”, “结合现有 idea 看看”
- English: “can this combine with PDA”, “compose this with another paper”, “adapt this to GRPO”, “compare this against my current idea”

Default route:

`coverage check -> docs/workflows/ingest.md if missing or stale -> compose -> critique -> mutate -> update`

### New Material + Reproduce

Typical user language:

- Chinese: “复刻这个实验”, “按这个设计做最小复现”, “看看实验怎么搭”
- English: “replicate this experiment”, “build a minimal reproduction”, “extract the experiment design”

Default route:

`coverage check -> docs/workflows/ingest.md if needed -> abstract experiment/evaluation patterns -> draft replication plan -> update if durable`

### New Material + Evaluate Existing Idea

Typical user language:

- Chinese: “这能不能支持我现在的 idea”, “对我现有 proposal 有什么影响”
- English: “does this support my current idea”, “how does this affect my proposal”

Default route:

`coverage check -> docs/workflows/ingest.md if needed -> diff -> idea review -> update affected hypothesis or proposal`

### Existing Material Only

Typical user language:

- Chinese: “对比一下 HACRL 和 PDA”, “看看这个 topic 的现状”
- English: “compare HACRL and PDA”, “show me the state of this topic”

Default route:

`docs/workflows/query.md -> read relevant pages -> write back durable output when warranted`

### Maintenance Or Validation

Typical user language:

- Chinese: “检查一下 wiki”, “清理链接”, “重审 idea”, “跑一次维护”
- English: “doctor the wiki”, “lint the wiki”, “check links”, “review stale ideas”, “run maintenance”

Default route:

`docs/workflows/doctor.md for checks -> docs/workflows/maintain.md for content-changing consolidation`

## Source Folder Signals

When inspecting a source folder, use these signals in priority order:

1. `notes.md`
2. obvious primary artifacts such as `paper.pdf`, paper markdown, or source code roots
3. chat-message intent
4. current wiki coverage

`notes.md` may refine or override the chat-message signal when the note is more specific.

## Example

Input:

```text
我补充了 sources/hacrl，我想看看这个训练能不能和 PDA 结合在一起。
```

Route:

1. Read `sources/hacrl/` and `sources/hacrl/notes.md` if present.
2. Check for existing `hacrl` coverage in `papers/`, `topics/`, `ideas/`, and `assets/`.
3. Read `docs/workflows/ingest.md` and ingest `hacrl` if coverage is missing or stale.
4. Read PDA-related structured pages or source folders.
5. Run compose + critique + mutate centered on the combination question.
6. Update durable outputs in `papers/`, `ideas/`, `assets/`, `reviews/`, `index.md`, and `log.md` as warranted.
````

- [ ] **Step 2: Verify router delegation**

Run:

```bash
rg -n "docs/workflows/ingest.md|docs/workflows/query.md|docs/workflows/doctor.md|docs/workflows/maintain.md|Maintenance Or Validation" /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md
```

Expected: router references all four workflow documents and includes the maintenance route.

- [ ] **Step 3: Commit router delegation**

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI add /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md
git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: delegate router routes to workflow runbooks"
```

### Task 4: Strengthen Directory README Resolvers

**Files:**
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/sources/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/papers/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/topics/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/assets/mechanisms/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/assets/experiment-patterns/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/assets/evaluation-patterns/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/assets/failure-modes/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/assets/negative-assets/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/reviews/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/logs/README.md`

- [ ] **Step 1: Replace `sources/README.md`**

Write this exact file:

````markdown
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
````

- [ ] **Step 2: Replace `papers/README.md`**

Write this exact file:

````markdown
# Directory Purpose

This directory stores one structured markdown page per ingested paper or paper-like research source.

## What Goes Here

- Canonical paper summaries created by the ingest workflow.
- Structured analysis of a single paper's problem, method, experiments, critique, reusable assets, and inspired ideas.

## What Does Not Go Here

- Raw PDFs, code snapshots, screenshots, and unprocessed notes. Put those in `sources/`.
- Cross-paper synthesis. Put that in `reviews/`.
- Reusable mechanism or experiment pages. Put those in `assets/`.
- Research idea cards. Put those in `ideas/`.

## Neighboring Directories To Check

- `sources/` for the source bundle that produced the paper page.
- `topics/` for topic map and survey links.
- `assets/` for extracted reusable patterns.
- `ideas/` for inspired hypotheses and proposals.

## Required Frontmatter

```yaml
type: paper
title: "Paper Title"
slug: firstauthor-year-keyword
paper_id: firstauthor-year-keyword
tags: [paper]
topic_refs: []
inspired_ideas: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<firstauthor>-<year>-<keyword>.md`, and make `paper_id` match the file slug exactly.

## Good Example

`papers/wei-2022-chain-of-thought.md`

## Common Wrong Placement

Do not put a replication plan here if it compares multiple papers or proposes a new experiment. Put that durable synthesis in `reviews/` or `ideas/proposals/`.
````

- [ ] **Step 3: Replace `topics/README.md`**

Write this exact file:

````markdown
# Directory Purpose

This directory stores topic map pages and conditional survey subpages.

## What Goes Here

- `topics/<topic>.md` topic map pages that define a research direction, core sub-problems, current judgment, and known gaps.
- `topics/<topic>/<subtopic>.md` survey subpages when a topic has 5+ papers or clear route divergence.

## What Does Not Go Here

- Single-paper analysis. Put that in `papers/`.
- Cross-topic research synthesis. Put that in `reviews/`.
- Research idea cards. Put those in `ideas/`.
- Reusable mechanisms or failure modes. Put those in `assets/`.

## Neighboring Directories To Check

- `research-map.md` before creating or moving topic pages.
- `papers/` for source papers linked to the topic.
- `ideas/` for hypotheses and proposals linked to the topic.
- `reviews/` for broader synthesis that should not become a topic page.

## Required Frontmatter

```yaml
type: topic
title: "Topic Title"
slug: topic-slug
tags: [topic]
subtopics: []
papers: []
ideas: []
open_questions: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

For survey subpages, use `type: topic-survey`.

## Naming Pattern

- Topic map: `topics/llm-reasoning.md`
- Survey subpage: `topics/llm-reasoning/chain-of-thought.md`

## Good Example

Create `topics/agent-systems.md` immediately when agent systems become a major direction. Create `topics/agent-systems/tool-use.md` only after enough papers or route divergence justify a living survey.

## Common Wrong Placement

Do not create a survey subpage after reading one paper. Update the topic map page until the 5+ papers or route-divergence trigger is met.
````

- [ ] **Step 4: Replace `ideas/hypothesis/README.md`**

Write this exact file:

````markdown
# Directory Purpose

This directory stores fast-capture hypothesis cards produced during Mutate or Compose.

## What Goes Here

- One-question research hypothesis cards.
- Low- or medium-confidence ideas that are not yet supported by two independent sources.
- Ideas with a rough feasibility judgment but no complete proposal.

## What Does Not Go Here

- Mature ideas with at least two independent sources and a minimal experiment. Put those in `ideas/proposals/`.
- Paper summaries. Put those in `papers/`.
- Cross-paper reviews. Put those in `reviews/`.
- Reusable mechanisms detached from a specific idea. Put those in `assets/mechanisms/`.

## Neighboring Directories To Check

- `ideas/proposals/` when support and minimal experiment are strong enough.
- `papers/` for source provenance.
- `topics/` for research-space placement.

## Required Frontmatter

```yaml
type: hypothesis
title: "Hypothesis Title"
slug: h-slug
status: hypothesis
confidence: low
tags: [idea, hypothesis]
topic_refs: []
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `h-<slug>.md`, for example `h-process-reward-scaling.md`.

## Good Example

`ideas/hypothesis/h-lambdarank-style-grpo-preference-shaping.md`

## Common Wrong Placement

Do not keep an idea here after it has two independent sources and a concrete minimal experiment. Promote it to `ideas/proposals/`.
````

- [ ] **Step 5: Replace `ideas/proposals/README.md`**

Write this exact file:

````markdown
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
````

- [ ] **Step 6: Replace asset directory README files**

Write these exact files:

`assets/mechanisms/README.md`

````markdown
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
````

`assets/experiment-patterns/README.md`

````markdown
# Directory Purpose

This directory stores reusable experimental design patterns.

## What Goes Here

- Ablation structures, control designs, reproduction setups, minimal experiments, and comparison patterns.

## What Does Not Go Here

- A metric or benchmark definition. Put that in `assets/evaluation-patterns/`.
- A method mechanism. Put that in `assets/mechanisms/`.
- A failure behavior. Put that in `assets/failure-modes/`.
- A design trap to avoid. Put that in `assets/negative-assets/`.

## Neighboring Directories To Check

- `papers/` for source provenance.
- `ideas/proposals/` if the experiment is attached to a specific proposal.
- `assets/evaluation-patterns/` if the main contribution is measurement.

## Required Frontmatter

```yaml
type: experiment-pattern
title: "Experiment Pattern Title"
slug: experiment-pattern-slug
asset_kind: experiment-pattern
tags: [asset, experiment-pattern]
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<slug>.md`, for example `step-level-reward-ablation.md`.

## Good Example

`assets/experiment-patterns/process-vs-outcome-reward-ablation.md`

## Common Wrong Placement

Do not put benchmark selection here unless the benchmark design is the reusable pattern. Put measurement strategy in `assets/evaluation-patterns/`.
````

`assets/evaluation-patterns/README.md`

````markdown
# Directory Purpose

This directory stores reusable evaluation and measurement patterns.

## What Goes Here

- Benchmark strategies, metric definitions, judge protocols, evaluation harness patterns, and measurement designs.

## What Does Not Go Here

- Training mechanisms. Put those in `assets/mechanisms/`.
- Ablation or reproduction setups. Put those in `assets/experiment-patterns/`.
- Observed failure behavior. Put that in `assets/failure-modes/`.
- Trap-like design choices. Put those in `assets/negative-assets/`.

## Neighboring Directories To Check

- `papers/` for source provenance.
- `topics/` when evaluation defines a subfield's current dispute.
- `reviews/` for cross-paper evaluation comparisons.

## Required Frontmatter

```yaml
type: evaluation-pattern
title: "Evaluation Pattern Title"
slug: evaluation-pattern-slug
asset_kind: evaluation-pattern
tags: [asset, evaluation-pattern]
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<slug>.md`, for example `process-vs-outcome-eval.md`.

## Good Example

`assets/evaluation-patterns/agent-harness-task-success-eval.md`

## Common Wrong Placement

Do not store a paper's entire experiment section here. Extract only the reusable evaluation design.
````

`assets/failure-modes/README.md`

````markdown
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
````

`assets/negative-assets/README.md`

````markdown
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
````

- [ ] **Step 7: Replace `reviews/README.md` and `logs/README.md`**

Write these exact files:

`reviews/README.md`

````markdown
# Directory Purpose

This directory stores durable research-content syntheses.

## What Goes Here

- Cross-paper reviews, topic comparisons, replication plans, durable critique synthesis, and research memos.

## What Does Not Go Here

- Operation audit trails. Put those in `logs/`.
- Single-paper summaries. Put those in `papers/`.
- Topic map pages. Put those in `topics/`.
- Idea cards or proposals. Put those in `ideas/`.

## Neighboring Directories To Check

- `topics/` when the synthesis should become a living topic map or survey.
- `ideas/` when the synthesis is a concrete research proposal.
- `logs/` when the content is an operation trace.

## Required Frontmatter

```yaml
type: review
title: "Review Title"
slug: review-slug
tags: [review]
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<slug>.md`, for example `process-supervision-survey.md`.

## Good Example

`reviews/lambdarank-for-grpo-transfer-analysis.md`

## Common Wrong Placement

Do not store a doctor or ingest audit here. Put process records in `logs/`.
````

`logs/README.md`

````markdown
# Directory Purpose

This directory stores operation-process records and audit trails.

## What Goes Here

- Ingest audits, doctor audits, maintenance reports, dry-run reports, and link-repair details.

## What Does Not Go Here

- Research-content synthesis. Put that in `reviews/`.
- Topic maps. Put those in `topics/`.
- Paper analysis. Put that in `papers/`.

## Neighboring Directories To Check

- `log.md` for the global operation timeline.
- `reviews/` when the content is research judgment rather than an operation audit.

## Required Frontmatter

```yaml
type: log
title: "Log Title"
slug: YYYY-MM-DD-log-slug
tags: [log]
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

## Naming Pattern

Use `<YYYY-MM-DD>-<slug>.md`, for example `2026-04-13-doctor-audit.md`.

## Good Example

`logs/2026-04-13-doctor-audit.md`

## Common Wrong Placement

Do not put reusable research synthesis here. Logs are for process and audit records.
````

- [ ] **Step 8: Verify every resolver has required headings**

Run:

```bash
for f in \
  /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/papers/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/topics/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/mechanisms/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/experiment-patterns/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/evaluation-patterns/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/failure-modes/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/negative-assets/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/reviews/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/logs/README.md; do
  rg -n "## What Goes Here|## What Does Not Go Here|## Neighboring Directories To Check|## Required Frontmatter|## Naming Pattern|## Good Example|## Common Wrong Placement" "$f" >/dev/null || exit 1
done
```

Expected: command exits with status 0.

- [ ] **Step 9: Verify topic resolver includes survey trigger**

Run:

```bash
rg -n "5\\+ papers|route divergence|survey subpage" /Users/chendongyao/Desktop/LLM-WIKI/topics/README.md
```

Expected: all three concepts are present.

- [ ] **Step 10: Commit directory resolvers**

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI add \
  /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/papers/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/topics/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/mechanisms/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/experiment-patterns/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/evaluation-patterns/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/failure-modes/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/negative-assets/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/reviews/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/logs/README.md
git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: strengthen directory resolver guides"
```

### Task 5: Update Idea Templates For Evidence Timelines

**Files:**
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/_template.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/_template.md`

- [ ] **Step 1: Replace hypothesis template**

Write this exact file:

```markdown
---
type: hypothesis
title: "Hypothesis Title"
slug: h-slug
status: hypothesis
confidence: low
tags: [idea, hypothesis]
topic_refs: []
source_papers: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Hypothesis Title

## Current Claim

One-sentence research question or hypothesis.

## Source Mechanisms

<!-- Anchor to the closest stable section heading in the paper page.
     Paper pages use section headings: #appreciate, #abstract, #critique, #mutate, #compose.
     Example: [[papers/some-paper]]#appreciate
     Mechanism-level anchors do not exist in paper pages — link to the section, not a sub-item. -->
- [[papers/example-paper]]#appreciate

## Feasibility Judgment

Short note on why this may be worth testing and what evidence is still missing.

## Minimal Test

- Smallest experiment or analysis that would make the hypothesis more or less credible.
- What result would trigger proposal promotion?

---

## Evidence Timeline

- YYYY-MM-DD | Created from Mutate or Compose. Source: [[papers/example-paper]]
```

- [ ] **Step 2: Replace proposal template**

Write this exact file:

```markdown
---
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
---

# Proposal Title

## Current Proposal

One-paragraph current version of the research proposal.

## Motivation

Why this is worth doing now.

## Core Mechanism

What mechanism or design change the proposal relies on.

## Minimal Experiment

The smallest experiment that can test the core assumption.

## Risks And Assumption Holes

- Main assumption that could fail.
- Missing baseline or evaluation risk.
- Reproducibility concern.

## Positioning

How this fits the research space: `opens-direction`, `fills-gap`, `overturns-assumption`, or `engineering-reinforcement`.

## Relationship To Existing Ideas

- Related hypothesis or proposal links.
- Whether this supersedes, combines, or conflicts with another idea.

---

## Evidence Timeline

- YYYY-MM-DD | Promoted from hypothesis after at least two independent sources and a minimal experiment were identified.
```

- [ ] **Step 3: Verify timeline sections exist**

Run:

```bash
rg -n "Current Claim|Feasibility Judgment|Minimal Test|Evidence Timeline|Current Proposal|Risks And Assumption Holes|Relationship To Existing Ideas" /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/_template.md /Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/_template.md
```

Expected: all target sections are present.

- [ ] **Step 4: Commit idea templates**

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI add \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/_template.md \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/_template.md
git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: add evidence timelines to idea templates"
```

### Task 6: Final Verification

**Files:**
- Verify: `/Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md`
- Verify: `/Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/*.md`
- Verify: `/Users/chendongyao/Desktop/LLM-WIKI/*/README.md` and nested resolver README files
- Verify: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/**/_template.md`

- [ ] **Step 1: Verify workflow documents exist**

Run:

```bash
test -f /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/ingest.md
test -f /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/query.md
test -f /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/maintain.md
test -f /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/doctor.md
```

Expected: all commands exit with status 0.

- [ ] **Step 2: Verify Phase 1 exclusions were respected**

Run:

```bash
test ! -d /Users/chendongyao/Desktop/LLM-WIKI/.agents/skills
rg -n "Postgres|PGLite|vector search|MCP|Obsidian plugin|cron" /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md
```

Expected: `.agents/skills` does not exist. Any matches for excluded technologies appear only as exclusions or "not implemented" statements, not as implementation instructions.

- [ ] **Step 3: Verify `AGENTS.md` line count**

Run:

```bash
wc -l /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md
```

Expected: line count is `180` or fewer.

- [ ] **Step 4: Verify root routing coverage**

Run:

```bash
rg -n "Action To Workflow Map|docs/workflows/router.md|docs/workflows/ingest.md|docs/workflows/query.md|docs/workflows/maintain.md|docs/workflows/doctor.md" /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md
```

Expected: all workflow paths and the action map are present.

- [ ] **Step 5: Verify no unresolved placeholders were introduced outside templates**

Run:

```bash
rg -n "T[B]D|TO""DO|implement"" later|fill in"" details" /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows /Users/chendongyao/Desktop/LLM-WIKI/*/README.md /Users/chendongyao/Desktop/LLM-WIKI/assets/*/README.md /Users/chendongyao/Desktop/LLM-WIKI/ideas/*/README.md
```

Expected: no matches.

- [ ] **Step 6: Verify markdown whitespace**

Run:

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI diff --check
```

Expected: no whitespace errors.

- [ ] **Step 7: Review final changed files**

Run:

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI status --short
git -C /Users/chendongyao/Desktop/LLM-WIKI log --oneline -6
```

Expected: only intentional workflow operationalization changes remain unstaged or committed. Existing unrelated `CLAUDE.md` or `reference/` changes, if still present from earlier work, must not be included unless the user explicitly asks.

- [ ] **Step 8: Commit verification fixes only if needed**

If Step 1 through Step 7 required small corrections, commit only those corrections:

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI add \
  /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md \
  /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows \
  /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/papers/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/topics/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/mechanisms/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/experiment-patterns/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/evaluation-patterns/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/failure-modes/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/assets/negative-assets/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/reviews/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/logs/README.md \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/_template.md \
  /Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/_template.md
git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: verify workflow operationalization"
```

Expected: verification corrections are committed only if Step 1 through Step 7 revealed necessary changes.
