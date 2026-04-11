# Intent Router Design Spec

**Domain:** LLM-maintained AI research wiki  
**Repository:** `/Users/chendongyao/Desktop/LLM-WIKI`  
**Date:** 2026-04-11

---

## 1. Purpose

Reduce user friction at the interaction layer. The user should describe newly added materials and research intent in natural language. The LLM should infer the correct operation sequence instead of requiring explicit commands such as `ingest`, `query`, or `compose`.

Example target interaction:

```text
我补充了 sources/HACRL，我想看看这个训练能不能和 PDA 结合在一起。
```

The system should route this into: detect new source folder -> run full ingest on the missing structured material -> analyze against PDA-related material -> write back useful outputs.

---

## 2. Design Goals

- Users speak in research language, not workflow language.
- New `sources/` material triggers default full ingest without asking for confirmation.
- Routing is semantic, not exact-string matching.
- Existing structured pages should be reused when sufficient; avoid redundant ingest.
- The router must stay lightweight and legible. It is not a classifier service.

---

## 3. Artifacts

Two files own the behavior:

- `AGENTS.md`
  - Holds high-level routing rules and default execution policy.
- `docs/workflows/router.md`
  - Holds concrete semantic routing patterns, trigger families, and action sequences.

`AGENTS.md` should define principle-level behavior. `docs/workflows/router.md` should define operational examples and the default route for each intent family.

---

## 4. Core Routing Rule

When a user message mentions one or more `sources/...` folders and also expresses an analytic goal, the LLM should:

1. Identify the referenced source folders.
2. Determine whether each folder already has adequate structured coverage in `papers/`, `topics/`, `ideas/`, and `assets/`.
3. If coverage is missing or stale, run full ingest without pausing for a separate confirmation step.
4. Continue into the requested analysis without pausing for a separate ingest confirmation step.
5. Write back durable outputs when the result is wiki-worthy.

Default policy: **ingest first, then analyze**.

---

## 5. Semantic Intent Families

The router must classify by meaning, not literal wording. It should use folder mentions, verbs, and surrounding research intent together.

Intent families:

- `new-material + understand`
  - Examples: “我补充了…帮我看看这篇做了什么”, “读一下这个材料”
  - Route: `ingest -> summarize/appreciate/critique -> update`

- `new-material + combine`
  - Examples: “能不能和 PDA 结合”, “和另一篇拼一下”, “借鉴到 GRPO 里”
  - Route: `ingest missing sources -> compose -> critique -> mutate -> update`

- `new-material + reproduce`
  - Examples: “复刻这个实验”, “按这个设计做一个最小复现”
  - Route: `ingest -> abstract experiment/eval patterns -> replication plan -> update if durable`

- `new-material + evaluate idea`
  - Examples: “这能不能支持我现在的 idea”, “对我现有 proposal 有什么影响”
  - Route: `ingest -> diff -> idea review -> proposal/hypothesis update`

- `existing-material only`
  - No new source folder mentioned
  - Route from current wiki state without forced ingest

---

## 6. Matching Heuristics

The router should consider:

- explicit `sources/<slug>` mentions
- file-path mentions that imply new source material
- `notes.md` in the source folder, when present, as an explicit intent layer that may override or refine the chat-message signal
- verbs such as “补充了”, “加了”, “放进去了”, “新加入”
- research-intent verbs such as “结合”, “对比”, “借鉴”, “复刻”, “迁移”, “支持”, “质疑”
- existing wiki coverage

The router must not require exact phrase equality. It should infer intent from nearby semantics and repository state.

`docs/workflows/router.md` should include both Chinese and English trigger expressions, because the surrounding user language may vary across sessions.

If the message is ambiguous but still contains a clear new source folder, default to ingest-first.

Only ask a clarifying question when:

- the referenced path does not exist
- multiple possible source folders conflict materially
- the requested comparison target is too ambiguous to resolve from repository context

---

## 7. Output Policy

After routing and execution, the LLM should decide what is durable enough to write back:

- always: `papers/` page and `index.md` / `log.md` updates for genuinely new source ingest
- sometimes: topic updates, assets, hypothesis cards, proposal promotions
- when the request is synthesis-heavy: a `reviews/` page if the answer is broader than a single paper page or idea card

Ephemeral chat-only analysis is allowed only when the result has no durable research value.

---

## 8. Initial Implementation Scope

This design does not introduce a separate executable tool or classifier.

Phase 1 changes:

- add a new `Intent Routing` section to `AGENTS.md`
- add `docs/workflows/router.md`
- update onboarding docs so users can describe materials naturally instead of issuing command-style prompts

Phase 1 explicitly does not include:

- automatic filesystem watchers
- a standalone CLI router
- embedding-based intent classification

---

## 9. Example Route

Input:

```text
我补充了 sources/HACRL，我想看看这个训练能不能和 PDA 结合在一起。
```

Expected route:

1. Read `sources/HACRL/`
2. Check whether HACRL already has structured wiki coverage
3. If not, run full ingest
4. Read PDA-related source material or existing structured pages
5. Run compose + critique + mutate centered on the combination question
6. Update `papers/`, `ideas/`, `assets/`, `reviews/`, `index.md`, and `log.md` as warranted

---

## 10. Risks

- Over-eager ingest may do more work than the user expected.
- Weak semantic routing may misclassify a question as combination vs. critique.
- Repeated ingest attempts may create duplicate work if coverage checks are too shallow.

Mitigation:

- make “coverage check before ingest” a hard rule
- keep the router rules explicit and inspectable in markdown
- prefer deterministic folder/path detection plus semantic intent, not semantics alone
