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
- Report pages wrapped entirely in ` ```markdown ` or ` ```md ` code fences.
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
- Run `rg -n "doctor \|" log.md` only when the doctor run changed wiki content.
