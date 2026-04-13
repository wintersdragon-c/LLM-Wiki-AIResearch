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
- Run `rg -n "\[\[.*\]\]" index.md research-map.md topics papers ideas assets` to spot missing or malformed WikiLinks.
- Run the doctor workflow in `docs/workflows/doctor.md` when the ingest creates or changes multiple pages.
