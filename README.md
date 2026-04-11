# LLMWiki-AIresearch

An LLM-maintained research wiki for computer science work, designed to be used with Obsidian and an agent that follows the repository schema in [AGENTS.md](./AGENTS.md).

This project is inspired by Andrej Karpathy's `llm-wiki` pattern and adapts that core idea to AI research workflows, topic-centered literature organization, and idea generation. The original design reference is:

- Andrej Karpathy, `llm-wiki`: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>

This repository is not an app. The repository itself is the artifact: raw source documents go into `raw/`, the LLM compiles them into markdown pages under `topics/`, `papers/`, `ideas/`, and `assets/`, and the wiki compounds over time.

## What This Is For

- Maintaining a persistent literature and idea system instead of doing one-off RAG over raw papers
- Organizing research by topic, not just by paper
- Generating and tracking hypotheses and proposals with provenance back to source papers
- Reusing mechanisms, experiment patterns, evaluation patterns, and failure modes across papers

## Repository Layout

```text
raw/                      Immutable inputs: papers, notes, images
topics/                   Topic map pages and survey subpages
papers/                   One structured page per paper
ideas/hypothesis/         Fast-capture idea cards
ideas/proposals/          Mature research proposals
assets/                   Reusable mechanisms, patterns, failure modes
reviews/                  Cross-paper syntheses and deep dives
logs/                     Detailed audit and lint records
index.md                  Content catalog
log.md                    Global timeline
research-map.md           High-level map of the research space
AGENTS.md                 Canonical workflow and schema rules
```

## Open In Obsidian

1. Open Obsidian.
2. Choose `Open folder as vault`.
3. Select this repository root.
4. Use graph view, backlinks, and frontmatter-aware plugins such as Dataview if you want richer navigation.

The wiki already uses `[[WikiLink]]` and structured YAML frontmatter, so Obsidian should work out of the box.

## Quick Start

1. Read [AGENTS.md](./AGENTS.md).
2. Read [research-map.md](./research-map.md) and [index.md](./index.md).
3. Drop a paper PDF or markdown export into `raw/papers/`.
4. Ask your LLM agent to `ingest` that paper using the pipeline in `AGENTS.md`.
5. Review the new or updated pages under `papers/`, `topics/`, `ideas/`, and `assets/`.

For a concrete walkthrough, see [docs/getting-started/first-ingest.md](./docs/getting-started/first-ingest.md).

## Recommended Workflow

- Use one source at a time at first.
- Keep `raw/` immutable.
- Let the agent update `index.md`, `log.md`, and bidirectional links during each ingest.
- Run periodic lint passes to catch stale claims, orphan pages, and upgradeable ideas.

## Obsidian CLI

This repository is structured so an Obsidian CLI layer can act as a semi-automatic executor:

- the LLM decides what to create or update
- the CLI writes files from templates, updates `index.md`, and routes log output

The naming rules, frontmatter schema, and Action -> Template map live in [AGENTS.md](./AGENTS.md).

## Attribution

This repository builds on the persistent wiki pattern described in Andrej Karpathy's `llm-wiki` gist, then specializes it for:

- AI research topic maps
- hypothesis / proposal tracking
- reusable experiment and evaluation patterns
- Obsidian-friendly LLM maintenance workflows

## License

This repository is released under the [MIT License](./LICENSE).

## Status

The repository is ready to use as an Obsidian vault and as a public template for an LLM-maintained research wiki.
