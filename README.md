# LLMWiki-AIresearch

An LLM-maintained research wiki for computer science work, designed to be used with Obsidian and an agent that follows the repository schema in [AGENTS.md](./AGENTS.md).

This project is inspired by Andrej Karpathy's `llm-wiki` pattern and adapts that core idea to AI research workflows, topic-centered literature organization, and idea generation. The original design reference is:

- Andrej Karpathy, `llm-wiki`: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>

This repository is not an app. The repository itself is the artifact: source materials go into `sources/`, the LLM compiles them into markdown pages under `topics/`, `papers/`, `ideas/`, and `assets/`, and the wiki compounds over time.

## What This Is For

- Maintaining a persistent literature and idea system instead of doing one-off RAG over source papers
- Organizing research by topic, not just by paper
- Generating and tracking hypotheses and proposals with provenance back to source papers
- Reusing mechanisms, experiment patterns, evaluation patterns, and failure modes across papers

## Repository Layout

```text
sources/                  Low-friction input folders; user drops materials here
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
3. Create a folder under `sources/` and drop your materials there.
4. Optionally add a short `notes.md` describing why the material matters, what you want from ingest, or what idea you are exploring.
5. Tell your LLM agent what new material you added and what you want to know, in natural language.
6. Let the agent route the request, ingest missing coverage, and update the wiki.
7. Review the new or updated pages under `papers/`, `topics/`, `ideas/`, `assets/`, and `reviews/`.

Natural-language requests are preferred over command-style prompts. Example: `我补充了 sources/hacrl，我想看看这个训练能不能和 PDA 结合在一起。`

For a concrete walkthrough, see [docs/getting-started/first-ingest.md](./docs/getting-started/first-ingest.md).

## Recommended Workflow

- Use one source folder at a time at first.
- Treat `sources/` as user-owned input space: drop in materials freely, let the LLM read from it, and avoid hand-maintaining structured output there.
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
