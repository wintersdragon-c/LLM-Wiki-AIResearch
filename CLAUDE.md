# CLAUDE.md

This file provides guidance to Claude Code when working in this repository.

## What this repository is

This repository is an LLM-maintained research wiki. There is no build system or test runner; the repository itself is the artifact.

Read `AGENTS.md` before any ingest, query, or lint operation. Read `LLM-wiki.md` for the pointer to the original Karpathy gist, then read `docs/superpowers/specs/2026-04-10-research-wiki-design.md` and `research-map.md` before structural edits.

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
