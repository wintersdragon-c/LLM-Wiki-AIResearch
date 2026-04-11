# First Ingest Walkthrough

This walkthrough shows the smallest useful end-to-end cycle for adding the first real paper to the wiki.

## Goal

Take one source folder from `sources/` and compile it into:

- one `papers/*.md` page
- updates to one or more `topics/*.md` pages
- zero or more `ideas/hypothesis/*.md` pages
- zero or more `assets/*.md` pages
- refreshed `index.md`
- one new `log.md` entry

## Before You Start

1. Create one source folder under `sources/`.
2. Put the paper and any supporting material there. Add `notes.md` if you want to guide the ingest.
3. Keep the folder name stable and readable, for example:

```text
sources/wei-2022-chain-of-thought/
  notes.md
  wei-2022-chain-of-thought.pdf
```

4. Read:
   - [AGENTS.md](../../AGENTS.md)
   - [research-map.md](../../research-map.md)
   - [index.md](../../index.md)

## Tell The Agent What To Do

Use a natural-language request like:

```text
我补充了 sources/wei-2022-chain-of-thought，我想先看看这篇论文到底做对了什么，以及它对现有 reasoning topic 有什么影响。
```

The agent should infer that this means:

- inspect the referenced source folder
- read `notes.md` if present
- run ingest first when structured coverage is missing or stale
- continue into the requested analysis
- write durable results back to the wiki

## Expected Output Shape

At minimum, the ingest should produce:

1. A structured paper page in `papers/`, for example:

```text
papers/wei-2022-chain-of-thought.md
```

2. Topic updates, for example:

```text
topics/llm-reasoning.md
topics/llm-reasoning/chain-of-thought.md
```

3. Optional idea pages if the paper triggers `Mutate` or `Compose`:

```text
ideas/hypothesis/h-process-reward-scaling.md
```

4. Optional reusable assets:

```text
assets/mechanisms/chain-of-thought-prompting.md
assets/evaluation-patterns/process-vs-outcome-eval.md
```

5. An `index.md` update that inserts the new pages in pipe-delimited format.

6. A new `log.md` entry:

```md
## [YYYY-MM-DD] ingest | <paper title>
```

## What To Review After Ingest

Check these five things:

1. The paper page has the required frontmatter and all pipeline sections.
2. `topic_refs`, `source_papers`, and `inspired_ideas` are internally consistent.
3. `index.md` contains the new or updated pages in the right section.
4. `log.md` records the ingest once, with a useful title.
5. No compiled wiki page was written into `sources/`; only the canonical wiki directories were updated.

## Good First Questions To Ask After Ingest

- What did this paper genuinely do well?
- Which mechanism here is transferable to another topic?
- Does this paper weaken or strengthen any existing hypothesis?
- Should any new hypothesis card be created?
- Which experiment pattern or failure mode is worth extracting?

## After The First Paper

Once one source folder goes through cleanly, repeat with a second source in the same topic. The first real value appears when the wiki starts linking papers through shared topics, assets, and ideas rather than storing them as isolated notes.
