# First Ingest Walkthrough

This walkthrough shows the smallest useful end-to-end cycle for adding the first real paper to the wiki.

## Goal

Take one paper from `raw/papers/` and compile it into:

- one `papers/*.md` page
- updates to one or more `topics/*.md` pages
- zero or more `ideas/hypothesis/*.md` pages
- zero or more `assets/*.md` pages
- refreshed `index.md`
- one new `log.md` entry

## Before You Start

1. Put a single paper into `raw/papers/`.
2. Make sure its filename is stable and readable, for example:

```text
raw/papers/wei-2022-chain-of-thought.pdf
```

3. Read:
   - [AGENTS.md](../../AGENTS.md)
   - [research-map.md](../../research-map.md)
   - [index.md](../../index.md)

## Tell The Agent What To Do

Use a prompt like:

```text
Ingest the paper at raw/papers/wei-2022-chain-of-thought.pdf using the pipeline in AGENTS.md. Update topics, ideas, assets, index.md, and log.md as needed. Keep raw/ immutable.
```

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
5. No file under `raw/` was modified.

## Good First Questions To Ask After Ingest

- What did this paper genuinely do well?
- Which mechanism here is transferable to another topic?
- Does this paper weaken or strengthen any existing hypothesis?
- Should any new hypothesis card be created?
- Which experiment pattern or failure mode is worth extracting?

## After The First Paper

Once one paper goes through cleanly, repeat with a second paper in the same topic. The first real value appears when the wiki starts linking papers through shared topics, assets, and ideas rather than storing them as isolated notes.
