# Research Wiki Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Turn the approved research-wiki spec into a working Obsidian-friendly repository skeleton with aligned schema files, seed pages, page templates, and a verified first-pass ingest workflow.

**Architecture:** Keep the repository as a markdown-first system. Implement the three-layer structure from the spec: low-friction `sources/`, compiled wiki pages at the repo root, and schema/control files (`AGENTS.md`, `CLAUDE.md`, spec, plan). Use Obsidian-native primitives only: directories, `[[WikiLink]]`, YAML frontmatter, and grep-friendly log formats.

**Tech Stack:** Markdown, YAML frontmatter, Obsidian WikiLinks/backlinks, zsh shell utilities (`mkdir`, `test`, `find`, `rg`, `sed`)

---

### Task 1: Align Schema Files With The Spec

**Files:**
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/CLAUDE.md`
- Reference: `/Users/chendongyao/Desktop/LLM-WIKI/docs/superpowers/specs/2026-04-10-research-wiki-design.md`

- [ ] **Step 1: Verify this directory is tracked by Git**

Run: `git -C /Users/chendongyao/Desktop/LLM-WIKI rev-parse --is-inside-work-tree`
Expected: prints `true` if Git is already initialized; otherwise fails with `fatal: not a git repository`.

- [ ] **Step 2: Initialize Git here if the previous command failed**

Run: `git -C /Users/chendongyao/Desktop/LLM-WIKI init`
Expected: `.git/` is created so later commit checkpoints are real.

- [ ] **Step 3: Write the new `AGENTS.md` from the approved spec**

```markdown
# Repository Guidelines

## What This Repository Is

This repository is an LLM-maintained research wiki for computer science work. The repository is a persistent, compounding markdown knowledge base rather than an application codebase.

Read `LLM-wiki.md`, `docs/superpowers/specs/2026-04-10-research-wiki-design.md`, and `research-map.md` before making structural edits.

## Architecture

Three layers:

- `sources/` contains user-owned source folders. Read from it during ingest; do not treat it as compiled wiki output.
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
```

- [ ] **Step 4: Verify the current `CLAUDE.md` still points to the obsolete `wiki/` layout**

Run: `rg -n "wiki/|wiki/index.md|wiki/log.md|wiki/sources|wiki/entities|wiki/concepts" /Users/chendongyao/Desktop/LLM-WIKI/CLAUDE.md`
Expected: matches are found, confirming the file still describes the legacy layout and needs replacement.

- [ ] **Step 5: Replace the obsolete `CLAUDE.md` sections with the new root-level structure**

```markdown
# CLAUDE.md

This file provides guidance to Claude Code when working in this repository.

## What this repository is

This repository is an LLM-maintained research wiki. There is no build system or test runner; the repository itself is the artifact.

Read `LLM-wiki.md`, `docs/superpowers/specs/2026-04-10-research-wiki-design.md`, `AGENTS.md`, and `research-map.md` before major edits.

## Architecture

Three layers:

- `sources/` — user-owned source folders for ingest input
- root wiki directories — maintained markdown knowledge pages
- schema files — `AGENTS.md` and `CLAUDE.md`

Key files:

- `index.md` — content catalog
- `log.md` — append-only global timeline
- `research-map.md` — authoritative high-level research map

## Operations

- Ingest follows the 11-stage pipeline from the design spec.
- Query starts from `research-map.md` and `index.md`.
- Lint checks contradictions, stale claims, orphan pages, missing links, upgradeable ideas, and map drift.

## Conventions

- Use `[[WikiLink]]` for internal links.
- Treat `sources/` as intake space, not compiled wiki output.
- Maintain YAML frontmatter on every wiki page.
- Keep logs grep-friendly with `## [YYYY-MM-DD] ...` headings.
```

- [ ] **Step 6: Confirm both schema files point at the same repository model**

Run: `rg -n "sources/|research-map.md|ideas/hypothesis|ideas/proposals|assets/negative-assets" /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md /Users/chendongyao/Desktop/LLM-WIKI/CLAUDE.md`
Expected: both files contain the new root-level structure and no `wiki/` references remain.

- [ ] **Step 7: Commit the schema alignment**

```bash
git add /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md /Users/chendongyao/Desktop/LLM-WIKI/CLAUDE.md
git commit -m "docs: align repository schema with research wiki spec"
```

### Task 2: Scaffold The Repository Structure And Seed Global Navigation Files

**Files:**
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/sources/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/sources/_template/notes.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/topics/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/papers/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/mechanisms/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/experiment-patterns/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/evaluation-patterns/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/failure-modes/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/negative-assets/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/reviews/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/logs/README.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/index.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/log.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/research-map.md`

- [ ] **Step 1: Create the directory skeleton**

Run:

```bash
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/sources/_template
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/topics
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/papers
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/assets/mechanisms
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/assets/experiment-patterns
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/assets/evaluation-patterns
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/assets/failure-modes
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/assets/negative-assets
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/reviews
mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/logs
```

Expected: all spec directories exist.

- [ ] **Step 2: Seed `README.md` files so empty directories stay visible and self-describing**

```markdown
---
type: index
tags: [directory-guide]
updated: 2026-04-10
---

# Directory Purpose

This directory stores repository artifacts for one specific wiki layer.
Create one markdown file per artifact and keep internal links in `[[WikiLink]]` format.
```

Use this exact second sentence per directory:

- `topics/`: `This directory stores topic map pages and conditional survey subpages.`
- `papers/`: `This directory stores one structured markdown page per ingested paper.`
- `ideas/hypothesis/`: `This directory stores fast-capture hypothesis cards produced during Mutate or Compose.`
- `ideas/proposals/`: `This directory stores mature research proposals promoted from supported hypotheses.`
- `assets/mechanisms/`: `This directory stores reusable innovation mechanisms.`
- `assets/experiment-patterns/`: `This directory stores reusable experiment designs.`
- `assets/evaluation-patterns/`: `This directory stores reusable evaluation approaches.`
- `assets/failure-modes/`: `This directory stores known failure modes and the conditions that trigger them.`
- `assets/negative-assets/`: `This directory stores design choices that look reasonable but should usually be avoided.`
- `reviews/`: `This directory stores research-content-oriented syntheses and deep dives.`
- `logs/`: `This directory stores operation-process-oriented audit trails, lint outputs, and session records.`

- [ ] **Step 3: Write the root `index.md`**

```markdown
---
type: index
tags: [catalog]
updated: 2026-04-10
---

# Index

## Global Files
- [[research-map]] - High-level research map across all active topics.
- [[log]] - Append-only global timeline of ingest, query, and lint operations.

## Topics
- Add topic pages here as they are created.

## Papers
- Add structured paper pages here as they are ingested.

## Ideas
### Hypothesis
- Add hypothesis cards here as they are created.

### Proposals
- Add mature proposals here as they are promoted.

## Assets
### Mechanisms
- Add reusable mechanisms here.

### Experiment Patterns
- Add reusable experiment patterns here.

### Evaluation Patterns
- Add reusable evaluation patterns here.

### Failure Modes
- Add failure mode pages here.

### Negative Assets
- Add trap-pattern pages here.

## Reviews
- Add research syntheses and deep dives here.
```

- [ ] **Step 4: Write the root `log.md` and `research-map.md`**

```markdown
---
type: log
tags: [timeline]
updated: 2026-04-10
---

# Log

## [2026-04-10] bootstrap | Initialized research wiki structure
- Created the repository skeleton from the approved design spec.
```

```markdown
---
type: research-map
tags: [research-map]
updated: 2026-04-10
---

# Research Map

## Foundational Layer
- To be populated as core topics are established.

## Applied Layer
- To be populated as project-facing topics emerge.

## Bridging Layer
- To be populated with topics that connect methods to applications.

## Current Frontier
- To be updated as the wiki accumulates papers and ideas.

## Relationship Notes
- Add explicit statements about topic dependencies and overlap here.
```

- [ ] **Step 5: Verify the scaffold is complete**

Run: `find /Users/chendongyao/Desktop/LLM-WIKI -maxdepth 3 -type d | sort`
Expected: all directories from the spec are present.

Run: `test -f /Users/chendongyao/Desktop/LLM-WIKI/index.md && test -f /Users/chendongyao/Desktop/LLM-WIKI/log.md && test -f /Users/chendongyao/Desktop/LLM-WIKI/research-map.md && echo OK`
Expected: `OK`

- [ ] **Step 6: Commit the scaffold**

```bash
git add /Users/chendongyao/Desktop/LLM-WIKI/sources /Users/chendongyao/Desktop/LLM-WIKI/topics /Users/chendongyao/Desktop/LLM-WIKI/papers /Users/chendongyao/Desktop/LLM-WIKI/ideas /Users/chendongyao/Desktop/LLM-WIKI/assets /Users/chendongyao/Desktop/LLM-WIKI/reviews /Users/chendongyao/Desktop/LLM-WIKI/logs /Users/chendongyao/Desktop/LLM-WIKI/index.md /Users/chendongyao/Desktop/LLM-WIKI/log.md /Users/chendongyao/Desktop/LLM-WIKI/research-map.md
git commit -m "docs: scaffold research wiki structure"
```

### Task 3: Create Page Templates For Core Artifact Types

**Files:**
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/topics/_template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/topics/_survey-template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/papers/_template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/_template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/proposals/_template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/mechanisms/_template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/experiment-patterns/_template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/evaluation-patterns/_template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/failure-modes/_template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/assets/negative-assets/_template.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/reviews/_template.md`

- [ ] **Step 1: Create the topic map template**

```markdown
---
type: topic
tags: [topic]
subtopics: []
open_questions: []
papers: []
ideas: []
updated: YYYY-MM-DD
---

# Topic Name

## Definition

One-sentence definition of the research problem.

## Core Sub-Problems
- [[subtopic-a]]
- [[subtopic-b]]

## Current Judgment

State the main controversy or current best understanding.

## Known Gaps

- Gap 1
- Gap 2
```

- [ ] **Step 2: Create the topic survey template**

```markdown
---
type: topic-survey
tags: [topic, survey]
updated: YYYY-MM-DD
---

# Survey Title

## Route Evolution

Summarize how the main approaches in this subtopic have evolved.

## Representative Works

- [[papers/example-paper]]

## Current Strongest Hypothesis

State the current best-supported view in this subtopic.

## Known Counterexamples

- Counterexample 1

## Open Problems

- Open problem 1
- Open problem 2
```

- [ ] **Step 3: Create the paper and hypothesis templates**

```markdown
---
type: paper
tags: [paper]
updated: YYYY-MM-DD
inspired_ideas: []
---

# Paper Title

## Parse
- Problem:
- Method:
- Results:
- Limitations:

## Appreciate
- Core innovation:
- Most transferable design:
- Best experiment:

## Map
- Topics:

## Positioning
- Role:

## Diff
- Contradictions:
- Reinforcements:

## Abstract
- Mechanisms:
- Experiment patterns:
- Evaluation patterns:
- Failure modes:
- Transferable tricks:

## Critique
- Assumption holes:
- Experimental blind spots:

## Mutate
- Variant 1:

## Compose
- Candidate combinations:
```

```markdown
---
type: hypothesis
status: hypothesis
confidence: low
tags: [idea, hypothesis]
sources: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Hypothesis Title

## Question

One-sentence research question or hypothesis.

## Source Mechanisms

- [[papers/example-paper]]#mechanism-example

## Feasibility

Short note on why this may be worth testing.

## Next Check

- What evidence is missing?
- What would trigger proposal promotion?
```

- [ ] **Step 4: Create the proposal and asset templates**

```markdown
---
type: proposal
status: proposal
tags: [idea, proposal]
sources: []
positioning: fills-gap
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Proposal Title

## Motivation

## Core Mechanism

## Risk / Assumption Holes

## Minimal Experiment

## Positioning In Research Space

## Related Ideas
```

```markdown
---
type: mechanism
tags: [asset, mechanism]
updated: YYYY-MM-DD
source_papers: []
---

# Mechanism Name

## Description

## Why It Matters

## Transfer Targets

## Source Papers
```

```markdown
---
type: experiment-pattern
tags: [asset, experiment-pattern]
updated: YYYY-MM-DD
source_papers: []
---

# Experiment Pattern Name

## Setup

## Variables

## Controls And Baselines

## Success Signal

## Source Papers
```

```markdown
---
type: evaluation-pattern
tags: [asset, evaluation-pattern]
updated: YYYY-MM-DD
source_papers: []
---

# Evaluation Pattern Name

## Target Behavior

## Metric Or Rubric

## Failure Cases It Exposes

## Recommended Use

## Source Papers
```

```markdown
---
type: failure-mode
tags: [asset, failure-mode]
updated: YYYY-MM-DD
source_papers: []
---

# Failure Mode Name

## Symptom

## Trigger Conditions

## Why It Happens

## Detection

## Source Papers
```

```markdown
---
type: negative-asset
tags: [asset, negative-asset]
updated: YYYY-MM-DD
source_papers: []
---

# Negative Asset Name

## Trap

## Why It Looks Reasonable

## Why It Fails

## Safer Alternative

## Source Papers
```

- [ ] **Step 5: Create the review template**

```markdown
---
type: review
tags: [review]
updated: YYYY-MM-DD
topics: []
papers: []
ideas: []
---

# Review Title

## Question

## Compared Sources

## Synthesis

## Implications For Current Ideas

## Follow-Up Actions
```

- [ ] **Step 6: Verify every core directory has a `_template.md`**

Run: `find /Users/chendongyao/Desktop/LLM-WIKI -name '_template.md' -o -name '_survey-template.md' | sort`
Expected: templates appear under `topics/`, `papers/`, `ideas/`, `assets/`, and `reviews/`.

- [ ] **Step 7: Commit the templates**

```bash
git add /Users/chendongyao/Desktop/LLM-WIKI/topics /Users/chendongyao/Desktop/LLM-WIKI/papers /Users/chendongyao/Desktop/LLM-WIKI/ideas /Users/chendongyao/Desktop/LLM-WIKI/assets /Users/chendongyao/Desktop/LLM-WIKI/reviews
git commit -m "docs: add research wiki page templates"
```

### Task 4: Seed The First Research Topics And Map Relationships

**Files:**
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/topics/llm-reasoning.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/topics/agent-systems.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/topics/evaluation-harness.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/topics/llm-reasoning/chain-of-thought.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/research-map.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/index.md`

- [ ] **Step 1: Create three topic map pages that match the intended research scope**

```markdown
---
type: topic
tags: [topic, llm-reasoning]
subtopics: ["[[topics/llm-reasoning/chain-of-thought]]"]
open_questions:
  - "When does explicit reasoning scaffolding help versus hurt?"
papers: []
ideas: []
updated: 2026-04-10
---

# LLM Reasoning

## Definition

Research on how language models plan, decompose, and externalize intermediate reasoning during problem solving.

## Core Sub-Problems
- [[topics/llm-reasoning/chain-of-thought]]

## Current Judgment

Reasoning quality depends on both training objective and evaluation design; prompting alone is not a sufficient account.

## Known Gaps

- Clean separation between reasoning quality and answer-format imitation
- Strong evaluation protocols for process quality
```

```markdown
---
type: topic
tags: [topic, agent-systems]
subtopics: []
open_questions:
  - "How should agents trade off planning depth, tool use, and execution reliability?"
papers: []
ideas: []
updated: 2026-04-10
---

# Agent Systems

## Definition

Research on LLM-based systems that plan, use tools, maintain state, and execute multi-step tasks.

## Core Sub-Problems
- Tool use and tool selection
- Planning and replanning
- Memory and state tracking
- Recovery from execution failure

## Current Judgment

Agent quality depends on the interaction between reasoning policy, tool interfaces, and evaluation setup rather than on prompting alone.

## Known Gaps

- Robust longitudinal evaluation
- Clear failure taxonomies for tool and planning loops
```

```markdown
---
type: topic
tags: [topic, evaluation-harness]
subtopics: []
open_questions:
  - "What evaluation harness best separates true capability from prompt or environment leakage?"
papers: []
ideas: []
updated: 2026-04-10
---

# Evaluation Harness

## Definition

Research on benchmark design, protocol construction, instrumentation, and analysis for evaluating LLM and agent behavior.

## Core Sub-Problems
- Benchmark construction
- Protocol fidelity
- Instrumentation and trace collection
- Metric reliability

## Current Judgment

Weak evaluation harnesses can dominate apparent model or agent gains, so measurement design is part of the research contribution.

## Known Gaps

- Process-faithful agent evaluation
- Better distinction between environment exploitation and genuine capability
```

- [ ] **Step 2: Create one survey subpage to validate the double-layer topic model**

```markdown
---
type: topic-survey
tags: [topic, survey, chain-of-thought]
updated: 2026-04-10
---

# Chain of Thought

## Route Evolution

Track prompting-only approaches, supervised process traces, and reinforcement-style reasoning training here.

## Representative Works

- Add papers after ingest.

## Current Strongest Hypothesis

Structured intermediate reasoning helps when it changes search or credit assignment, not just output format.

## Known Counterexamples

- Add failures and negative assets after ingest.

## Open Problems

- Process-faithful evaluation
- Transfer from reasoning traces to policy improvement
```

- [ ] **Step 3: Update `research-map.md` and `index.md` so navigation works immediately**

```markdown
## Foundational Layer
- [[topics/llm-reasoning]] - Core reasoning and problem-solving behavior in LLMs.

## Applied Layer
- [[topics/agent-systems]] - Tool-using and multi-step LLM systems built on reasoning policies.

## Bridging Layer
- [[topics/evaluation-harness]] - Evaluation infrastructure that connects reasoning and agents to credible measurement.

## Relationship Notes
- [[topics/llm-reasoning]] provides capability primitives that many [[topics/agent-systems]] systems depend on.
- [[topics/evaluation-harness]] measures both reasoning behavior and agent execution quality.
- Improvements in [[topics/agent-systems]] are difficult to trust without stronger [[topics/evaluation-harness]] design.
```

```markdown
## Topics
- [[topics/llm-reasoning]] - Research on reasoning behavior, process supervision, and evaluation.
- [[topics/agent-systems]] - Research on LLM agents, tool use, planning, and execution loops.
- [[topics/evaluation-harness]] - Research on benchmark and protocol design for credible evaluation.
```

- [ ] **Step 4: Verify the links resolve structurally**

Run: `rg -n "\\[\\[topics/llm-reasoning|\\[\\[topics/agent-systems|\\[\\[topics/evaluation-harness|\\[\\[topics/llm-reasoning/chain-of-thought" /Users/chendongyao/Desktop/LLM-WIKI`
Expected: matches in `research-map.md`, `index.md`, and the topic page frontmatter/body for all three seed topics.

- [ ] **Step 5: Commit the seed topic**

```bash
git add /Users/chendongyao/Desktop/LLM-WIKI/topics /Users/chendongyao/Desktop/LLM-WIKI/research-map.md /Users/chendongyao/Desktop/LLM-WIKI/index.md
git commit -m "docs: seed core research topics and map entries"
```

### Task 5: Document The Operational Workflow And Health Checks

**Files:**
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/reviews/ingest-checklist.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/logs/lint-template.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md`

- [ ] **Step 1: Add an ingest checklist page for repeatable execution**

```markdown
---
type: checklist
tags: [workflow, ingest]
updated: 2026-04-10
---

# Ingest Checklist

1. Parse the source and create or update the paper page.
2. Appreciate what the paper genuinely does well.
3. Map it against `research-map.md` and topic pages.
4. Position it in the research space.
5. Diff against current claims and open questions.
6. Extract reusable assets.
7. Critique assumptions and experiments.
8. Mutate the design into internal variants.
9. Compose cross-paper ideas and mark confidence.
10. Update all affected pages and links.
11. Log the session in `log.md` and `logs/` when needed.
```

- [ ] **Step 2: Add a lint template matching the spec**

```markdown
---
type: lint-report
tags: [lint]
updated: YYYY-MM-DD
---

# Lint Report

## Contradictions

## Superseded Claims

## Orphan Pages

## Missing Concept Pages

## Missing Cross-References

## Upgradeable Hypotheses

## Under-Review Ideas Still Unresolved

## Research Map Drift
```

- [ ] **Step 3: Link the workflow helpers from `AGENTS.md`**

```markdown
## Operational Helpers

- `reviews/ingest-checklist.md` is the canonical ingest checklist.
- `logs/lint-template.md` is the starting point for lint reports.
```

- [ ] **Step 4: Verify the helper files and references**

Run: `test -f /Users/chendongyao/Desktop/LLM-WIKI/reviews/ingest-checklist.md && test -f /Users/chendongyao/Desktop/LLM-WIKI/logs/lint-template.md && rg -n "ingest-checklist|lint-template" /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md`
Expected: both files exist and `AGENTS.md` references them.

- [ ] **Step 5: Commit the workflow docs**

```bash
git add /Users/chendongyao/Desktop/LLM-WIKI/reviews/ingest-checklist.md /Users/chendongyao/Desktop/LLM-WIKI/logs/lint-template.md /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md
git commit -m "docs: add ingest and lint workflow helpers"
```

### Task 6: Run A Dry-Run Verification Pass

**Files:**
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/papers/example-paper.md`
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/h-example-idea.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/index.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/log.md`

- [ ] **Step 1: Create one synthetic paper page and one linked hypothesis card**

```markdown
---
type: paper
tags: [paper, example]
updated: 2026-04-10
inspired_ideas:
  - "[[ideas/hypothesis/h-example-idea]]"
---

# Example Paper

## Parse
- Problem: Example placeholder for workflow validation.
- Method: Minimal structured content for dry-run validation.
- Results: Not applicable.
- Limitations: Synthetic page.
```

```markdown
---
type: hypothesis
status: hypothesis
confidence: low
tags: [idea, hypothesis, example]
sources:
  - "[[papers/example-paper]]#parse"
created: 2026-04-10
updated: 2026-04-10
---

# Example Idea

## Question

Can this repository maintain paper-to-idea provenance and Obsidian backlinks correctly?
```

- [ ] **Step 2: Add the synthetic pages to `index.md` and `log.md`**

```markdown
## Papers
- [[papers/example-paper]] - Synthetic page used to validate wiki structure and links.
```

```markdown
## Ideas
### Hypothesis
- [[ideas/hypothesis/h-example-idea]] - Synthetic hypothesis linked to the example paper.
```

```markdown
## [2026-04-10] dry-run | Verified synthetic paper-to-idea link path
- Added `example-paper` and `h-example-idea` to validate link and frontmatter conventions.
```

- [ ] **Step 3: Run structural verification commands**

Run: `rg -n "^type: |^status: |^confidence: |^sources: |^inspired_ideas:" /Users/chendongyao/Desktop/LLM-WIKI/papers/example-paper.md /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/h-example-idea.md`
Expected: all required frontmatter fields are present.

Run: `rg -n "\\[\\[papers/example-paper\\]\\]|\\[\\[ideas/hypothesis/h-example-idea\\]\\]" /Users/chendongyao/Desktop/LLM-WIKI`
Expected: bidirectional references are present in the paper, idea, and index.

- [ ] **Step 4: Remove the synthetic pages by default**

Run:

```bash
rm /Users/chendongyao/Desktop/LLM-WIKI/papers/example-paper.md
rm /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis/h-example-idea.md
```

Expected: the dry-run fixture is removed unless the user explicitly asks to keep it as a permanent smoke-test artifact.

- [ ] **Step 5: Commit the verification pass**

```bash
git add /Users/chendongyao/Desktop/LLM-WIKI/papers /Users/chendongyao/Desktop/LLM-WIKI/ideas/hypothesis /Users/chendongyao/Desktop/LLM-WIKI/index.md /Users/chendongyao/Desktop/LLM-WIKI/log.md
git commit -m "docs: verify research wiki workflow with dry-run fixture"
```

---

## Coverage Check

- Task 1 covers schema alignment and prevents the old `wiki/` model in `CLAUDE.md` from conflicting with the new spec.
- Task 2 covers the full directory structure plus `index.md`, `log.md`, and `research-map.md`.
- Task 3 covers core page templates for topics, papers, ideas, and assets.
- Task 4 covers the double-layer Topic model and initial navigation across reasoning, agents, and evaluation.
- Task 5 covers repeatable ingest and lint operations.
- Task 6 covers dry-run verification of frontmatter, links, and logging.
