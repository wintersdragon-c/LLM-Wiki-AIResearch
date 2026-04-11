# Research Wiki Design Spec

**Domain:** LLM / Agent Systems / Evaluation Harness  
**Pattern basis:** LLM-wiki.md  
**Date:** 2026-04-10

---

## 1. Purpose

Build a persistent, compounding knowledge base for computer science research. The system serves two equal goals: accumulating a reusable library of research techniques (mechanisms, experiment patterns, evaluation patterns), and generating and tracking original research ideas with full provenance back to source papers.

This is not a reading log. It is a compiled artifact that gets richer with every paper ingested and every question asked.

---

## 2. Three-Layer Architecture

Directly inherits from LLM-wiki.md:

- **`sources/`** — User-owned source folders. The user drops materials here with minimal structure; the LLM reads from them and compiles the wiki elsewhere.
  - `sources/<slug>/paper.pdf` — PDF or markdown of a source paper
  - `sources/<slug>/notes.md` — Optional short intent note from the user
  - `sources/<slug>/repo/`, `figures/`, `data/`, or arbitrary files — Supporting material when useful

- **Wiki layer** — LLM-owned markdown files. The persistent compiled artifact.

- **`AGENTS.md`** — Schema document. Defines wiki structure, conventions, and workflows. Co-evolved with the user over time.

---

## 3. Directory Structure

```
sources/
  <source-slug>/
    notes.md
    paper.pdf
    repo/
    figures/
    ...

topics/                        # Research space navigation
  <topic-name>.md              # Topic map pages
  <topic-name>/                # Topic survey subpages (conditional)
    <subtopic>.md

papers/                        # One page per ingested paper
ideas/
  hypothesis/                  # Fast-capture idea cards
  proposals/                   # Mature research proposals
assets/
  mechanisms/                  # Reusable innovation mechanisms
  experiment-patterns/         # Reusable experiment designs
  evaluation-patterns/         # Reusable evaluation approaches
  failure-modes/               # Known failure modes
  negative-assets/             # Designs that look reasonable but should be avoided
reviews/                       # Research-content-oriented analyses: cross-paper syntheses, topic deep-dives
logs/                          # Operation-process-oriented records: session audit trails, stage logs, lint reports

index.md                       # Content catalog: all pages with one-line summaries
log.md                         # Global timeline: ingest / query / lint summaries
research-map.md                # Authoritative high-level map of topics and their relationships
AGENTS.md                      # Schema
```

---

## 4. Special Files

### `index.md`
Content-oriented catalog. Every wiki page listed with a one-line summary, organized by category. LLM reads this first when answering queries. Updated on every ingest.

### `log.md`
Global append-only timeline. One entry per operation. Format:
```
## [YYYY-MM-DD] ingest | Paper Title
## [YYYY-MM-DD] query | Question summary
## [YYYY-MM-DD] lint | Scope of pass
```
Greppable: `grep "^## \[" log.md | tail -10` gives last 10 operations.

### `logs/`
Detailed records that don't belong in the global timeline: per-session notes, stage-level audit trails, multi-paper review sessions. When a lint pass produces a long report, it goes in `logs/`, with only a summary line in `log.md`.

### `research-map.md` (root, authoritative)
High-level research space map. Single physical file at the root — not duplicated inside `topics/`. All pipeline stages that reference the research map read this file. Answers:
- What are the current main Topics?
- What are the relationships between them (upstream/downstream, overlapping, bridging)?
- Which are foundational, which are applied, which are bridging layers?

This is the first file LLM reads in the Map stage. It must NOT become a link dump — it should contain judgment and structure, not just a list of topics. Kept short and opinionated.

---

## 5. Compilation Pipeline

Each source-folder ingestion runs through these 11 stages in order:

### Stage 1: Parse
Read the source folder, identify the primary paper or artifact set, and extract title, authors, venue, year, problem statement, proposed method, key results, and author-stated limitations. If `notes.md` exists, treat it as guidance rather than ground truth.

### Stage 2: Appreciate
Extract what the paper genuinely does well — independent of the authors' own framing:
- What is the core innovation (not the marketing language)?
- What is the real advance over prior work?
- Which single design is most worth learning from?
- Which experiment is most elegant?
- Which framing, task definition, or evaluation approach is most inspiring?
- What is transferable to other problems?

### Stage 3: Map
Map the paper to existing Topics and research sub-problems in `topics/`. Read `research-map.md` first. Identify which Topic pages need updating.

### Stage 4: Positioning
Classify the paper's role in the research space. This determines where compilation effort is concentrated:

| Role | Primary update target |
|------|----------------------|
| Opens new direction | `research-map.md`, new Topic page |
| Fills known gap | Existing Topic page, specific gap section |
| Overturns assumption | Trigger idea decay review, update Critique sections |
| Engineering reinforcement | `assets/experiment-patterns/`, method route pages |

Do not treat engineering papers as paradigm shifts.

### Stage 5: Diff
Diff against existing wiki based on the paper's Positioning (not a uniform diff). Look for: contradictions with existing claims, claims that strengthen existing judgments, new variables not yet tracked, superseded claims.

### Stage 6: Abstract
Extract reusable assets into `assets/`. Five categories written immediately:

1. **Mechanisms** (`assets/mechanisms/`) — Innovation mechanisms that could be applied elsewhere
2. **Experiment patterns** (`assets/experiment-patterns/`) — Reusable experiment designs
3. **Evaluation patterns** (`assets/evaluation-patterns/`) — Reusable evaluation approaches
4. **Failure modes** (`assets/failure-modes/`) — Known failure modes with conditions
5. **Transferable inductive biases / objectives / data tricks** — Filed under mechanisms or experiment-patterns as appropriate

**Negative assets** (`assets/negative-assets/`) are written after Stage 7 (Critique), not here. They require Critique output to identify. See Stage 7.

### Stage 7: Critique
Systematic problem-finding: assumption holes, experimental blind spots, missing baselines, scope overclaims, reproducibility concerns.

After Critique completes, extract **negative assets**: designs from this paper that look reasonable but are traps — write each to `assets/negative-assets/`. These are a direct output of Critique and cannot be produced before it.

### Stage 8: Mutate
Without reference to other papers: generate internal variants and superior alternatives. "What if we changed variable X?" "What if we relaxed assumption Y?" Each promising variant becomes a hypothesis card in `ideas/hypothesis/`.

### Stage 9: Compose
Always runs, but operates in two modes based on asset library depth:

- **Weak-compose mode** (fewer than 10 papers): Generate candidate combinations from available assets. Output hypothesis cards tagged `confidence: low` to signal they rest on a thin evidence base.
- **Strong-compose mode** (10+ papers): Full cross-paper combination with richer asset pool. Output hypothesis cards at normal confidence.

The 10-paper threshold is a mode switch, not a gate. Early-stage wikis still produce Compose output; the confidence tag signals how much weight to place on it.

### Stage 10: Update
Write all changes to wiki pages. Maintain bidirectional links (see Section 7). Check for hypothesis cards that should be upgraded to proposals (see Section 6).

### Stage 11: Log
Append summary entry to `log.md`. Write detailed notes to `logs/` if the session produced significant audit material.

---

## 6. Idea Lifecycle

### Hypothesis Card (`ideas/hypothesis/`)
Fast-capture. Created automatically during Mutate or Compose stages.

Required frontmatter:
```yaml
---
type: hypothesis
status: hypothesis
confidence: low | medium | high  # low = weak-compose or thin evidence; high = multiple strong independent sources
sources:
  - "[[papers/paper-A]]#mechanism-reward-decomposition"
created: YYYY-MM-DD
---
```
Body: one-sentence question + rough feasibility note. No more than half a page.

`confidence` is only used on hypothesis cards, not proposals. A proposal has already passed the two-source upgrade threshold, so confidence is implicit in its existence.

### Upgrade Trigger
During each Update stage, LLM checks existing hypothesis cards. Upgrade to proposal if:
- At least two independent sources support the core assumption, AND
- A minimal experiment design can be sketched

### Research Proposal (`ideas/proposals/`)
Required frontmatter:
```yaml
---
type: proposal
status: proposal
sources:
  - "[[papers/paper-A]]#mechanism-X"
  - "[[papers/paper-B]]#experiment-pattern-Y"
positioning: fills-gap | opens-direction | overturns-assumption
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```
Body sections: Motivation → Core mechanism → Risk / assumption holes → Minimal experiment → Positioning in research space → Related ideas.

### Decay and Re-review
When Positioning classifies a new paper as "overturns assumption," LLM identifies all hypothesis cards and proposals whose source assumptions may be invalidated and flags them for re-review. Flagged ideas get `status: under-review` until explicitly resolved.

---

## 7. Bidirectional Tracking

Every paper page tracks what it inspired:
```yaml
# papers/paper-A.md
inspired_ideas:
  - "[[ideas/hypothesis/h-sparse-reward]]"
  - "[[ideas/proposals/p-tool-curriculum]]"
```

Every idea page tracks its sources with anchor precision:
```yaml
# ideas/hypothesis/h-sparse-reward.md
sources:
  - "[[papers/paper-A]]#mechanism-reward-decomposition"
  - "[[papers/paper-B]]#experiment-pattern-curriculum"
```

LLM maintains both directions during Update stage. Obsidian backlinks panel renders the reverse direction automatically.

---

## 8. Topic Pages

### Map Page (default, all topics)
```yaml
---
type: topic
subtopics: []
open_questions: []
papers: []
ideas: []
updated: YYYY-MM-DD
---
```
Body: one-sentence definition → core sub-problems (linked) → current judgment (main controversy) → known gaps.

### Survey Subpage (conditional)
Triggered when a topic accumulates 5+ papers OR shows clear route divergence. Lives at `topics/<topic-name>/<subtopic>.md`.

Body: route evolution → representative works → current strongest hypothesis → known counterexamples → open problems. This is a living survey — updated on every relevant Diff.

### `research-map.md`
Opinionated, short. Structure:
- Foundational layer: [topics that others depend on]
- Applied layer: [topics that build on foundational]
- Bridging layer: [topics that connect foundational to applied]
- Current frontier: [where the action is right now]
- Relationship notes: explicit statements about how topics interact

Updated when a paper Opens a New Direction or when the LLM judges the map has drifted from reality.

---

## 9. Frontmatter Conventions

All wiki pages include at minimum:
```yaml
---
type: paper | topic | topic-survey | hypothesis | proposal | mechanism | experiment-pattern | evaluation-pattern | failure-mode | negative-asset | index | log | research-map | review | checklist | lint-report
tags: []
updated: YYYY-MM-DD
---
```

This enables Dataview queries for status boards, topic views, and idea tracking.

Additional page-type-specific fields:

- `hypothesis` pages also include `status`, `sources`, `created`, and `confidence: low | medium | high`
- `proposal` pages also include `status`, `sources`, `positioning`, `created`, and `updated`
- `paper` pages also include `inspired_ideas` when relevant so bidirectional tracking stays explicit

---

## 10. Query Operation

When answering a research question:
1. Read `research-map.md` and `index.md` to identify relevant pages
2. Read relevant Topic, paper, and asset pages
3. Synthesize answer with citations using `[[WikiLink]]` format
4. If the answer is a valuable analysis (comparison, gap map, mechanism synthesis): file it as a new page in the appropriate directory and add to `index.md`
5. Append to `log.md`

---

## 11. Lint Operation

Periodic health check. Look for:
- Contradictions between pages
- Stale claims superseded by newer papers
- Orphan pages (no inbound WikiLinks)
- Important concepts mentioned but lacking their own page
- Missing cross-references
- Hypothesis cards that qualify for upgrade but haven't been promoted
- Ideas under-review that have been sitting unresolved
- `research-map.md` drift from actual topic structure

Write detailed findings to `logs/lint-YYYY-MM-DD.md`, summary to `log.md`.
