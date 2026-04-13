# Query Workflow

Use this workflow when the user asks a research question against the existing wiki.

## Trigger Conditions

- The user asks to compare papers, topics, mechanisms, assets, or ideas already represented in the wiki.
- The user asks for the state of a research direction.
- The user asks whether an existing idea is supported, contradicted, or worth upgrading.

If the request references new `sources/...` material, return to `docs/workflows/router.md` and run the coverage-check behavior before answering.

## Required Inputs

- The user's research question.
- `research-map.md`
- `index.md`
- Relevant topic, paper, idea, asset, review, and log pages.

## Read First

1. `docs/workflows/router.md`
2. `research-map.md`
3. `index.md`
4. Relevant directory README resolvers if the answer may write new durable pages

## Context Diversity Rule

Do not answer from the most similar paper alone. Check diverse page types:

- topic map or survey page
- paper pages
- idea hypothesis or proposal pages
- assets pages
- reviews or logs when they contain relevant synthesis or audit history

If one of these page classes has no relevant coverage, say that internally in the analysis and avoid inventing it.

## Execution

1. Identify the research question and requested output type.
2. Read `research-map.md` to place the question in the global research space.
3. Read `index.md` to find candidate pages.
4. Read the relevant topic map or survey page.
5. Read relevant paper pages.
6. Read relevant idea pages.
7. Read relevant asset pages.
8. Synthesize an answer that distinguishes known evidence, inference, and speculation.
9. Decide whether the output is durable.
10. If durable, update `reviews/`, `topics/`, or `ideas/` after reading the target directory README resolver.
11. If any page is created or significantly changed, update `index.md` and append to `log.md`.

## Durable Output Rules

- Use `reviews/` for cross-paper synthesis, comparison, or reusable research judgment.
- Update `topics/` when the answer changes the research map, known gaps, or route split.
- Update `ideas/hypothesis/` or `ideas/proposals/` when the answer changes an idea's current claim, feasibility, evidence timeline, or review status.
- Do not write a new page for a one-off answer that will not be reused.

## Verification

- If durable output was written, run `rg -n "<new-slug>|<changed-slug>" index.md log.md`.
- If an idea was changed, run `rg -n "source_papers|Evidence Timeline|under-review|confidence" ideas`.
- If the answer changed topic structure, run `rg -n "<topic-slug>|<subtopic-slug>" research-map.md topics`.
