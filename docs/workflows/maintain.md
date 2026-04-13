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
