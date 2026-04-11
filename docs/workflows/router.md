# Intent Router

This document defines how the agent should route natural-language research requests into repository operations.

## Routing Principles

- Route by meaning, not by exact string match.
- Treat `sources/<slug>/notes.md`, when present, as an explicit intent layer.
- If a user mentions new `sources/...` material and also asks an analytic question, ingest first and continue without pausing for a separate confirmation step.
- Reuse existing structured coverage when it is already adequate.
- Ask a clarifying question only when the referenced path does not exist, the target is materially ambiguous, or repository state is insufficient to resolve the request.

## Coverage Check Before Ingest

Before ingesting a referenced source folder, inspect:

- `papers/` for an existing canonical paper page
- `topics/` for relevant topic coverage
- `ideas/` for existing linked hypotheses or proposals
- `assets/` for already-extracted mechanisms, experiment patterns, evaluation patterns, failure modes, or negative assets

If coverage is missing or stale, run the full ingest pipeline before continuing the requested analysis.

## Intent Families

### New Material + Understand

Typical user language:

- Chinese: “我补充了…帮我看看这篇做了什么”, “读一下这个材料”, “整理一下这个 source”
- English: “I added ... help me understand it”, “read this source”, “organize this material”

Default route:

`coverage check -> ingest if needed -> appreciate -> critique -> update`

### New Material + Combine

Typical user language:

- Chinese: “能不能和 PDA 结合”, “和另一篇拼一下”, “借鉴到 GRPO 里”, “结合现有 idea 看看”
- English: “can this combine with PDA”, “compose this with another paper”, “adapt this to GRPO”, “compare this against my current idea”

Default route:

`coverage check -> ingest missing or stale sources -> compose -> critique -> mutate -> update`

### New Material + Reproduce

Typical user language:

- Chinese: “复刻这个实验”, “按这个设计做最小复现”, “看看实验怎么搭”
- English: “replicate this experiment”, “build a minimal reproduction”, “extract the experiment design”

Default route:

`coverage check -> ingest if needed -> abstract experiment/evaluation patterns -> draft replication plan -> update if durable`

### New Material + Evaluate Existing Idea

Typical user language:

- Chinese: “这能不能支持我现在的 idea”, “对我现有 proposal 有什么影响”
- English: “does this support my current idea”, “how does this affect my proposal”

Default route:

`coverage check -> ingest if needed -> diff -> idea review -> update affected hypothesis or proposal`

### Existing Material Only

Typical user language:

- Chinese: “对比一下 HACRL 和 PDA”, “看看这个 topic 的现状”
- English: “compare HACRL and PDA”, “show me the state of this topic”

Default route:

`query current wiki state -> read relevant pages -> write back durable output when warranted`

## Source Folder Signals

When inspecting a source folder, use these signals in priority order:

1. `notes.md`
2. obvious primary artifacts such as `paper.pdf`, paper markdown, or source code roots
3. chat-message intent
4. current wiki coverage

`notes.md` may refine or override the chat-message signal when the note is more specific.

## Example

Input:

```text
我补充了 sources/hacrl，我想看看这个训练能不能和 PDA 结合在一起。
```

Route:

1. Read `sources/hacrl/` and `sources/hacrl/notes.md` if present.
2. Check for existing `hacrl` coverage in `papers/`, `topics/`, `ideas/`, and `assets/`.
3. Ingest `hacrl` if coverage is missing or stale.
4. Read PDA-related structured pages or source folders.
5. Run compose + critique + mutate centered on the combination question.
6. Update durable outputs in `papers/`, `ideas/`, `assets/`, `reviews/`, `index.md`, and `log.md` as warranted.
