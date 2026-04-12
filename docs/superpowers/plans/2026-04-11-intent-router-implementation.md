# Intent Router Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a semantic intent-routing layer so users can describe new source materials and research goals naturally while the agent infers ingest-first execution and follow-up analysis.

**Architecture:** Keep the implementation markdown-only. Put principle-level routing behavior in `AGENTS.md`, put inspectable semantic route families in `docs/workflows/router.md`, and update onboarding docs so examples use natural-language requests instead of command-style prompts. No CLI, watcher, or classifier is added in this phase.

**Tech Stack:** Markdown, existing repository schema, Obsidian WikiLinks, `rg`, `sed`, `git`

---

### Task 1: Add The Router Workflow Document

**Files:**
- Create: `/Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md`

- [ ] **Step 1: Create the workflows directory**

Run: `mkdir -p /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows`
Expected: `/Users/chendongyao/Desktop/LLM-WIKI/docs/workflows` exists.

- [ ] **Step 2: Write `docs/workflows/router.md`**

Write this exact file:

```markdown
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
```
```

- [ ] **Step 3: Verify the router document exists and contains the required route families**

Run: `rg -n "New Material \\+ Understand|New Material \\+ Combine|New Material \\+ Reproduce|New Material \\+ Evaluate Existing Idea|Existing Material Only|notes\\.md may refine or override" /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md`
Expected: all five intent families and the `notes.md` precedence rule are present.

- [ ] **Step 4: Commit the router document**

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI add /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md
git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: add semantic intent router workflow"
```

### Task 2: Add High-Level Intent Routing Rules To `AGENTS.md`

**Files:**
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md`

- [ ] **Step 1: Insert an `Intent Routing` section after `Source Intake`**

Insert this exact section below the current `### Source Intake` block:

```markdown
### Intent Routing
Interpret user requests by research intent, not only by explicit workflow verbs.

- If a user mentions one or more `sources/...` folders and also asks an analytic question, first check whether those sources already have adequate structured coverage in `papers/`, `topics/`, `ideas/`, and `assets/`.
- If coverage is missing or stale, run full ingest without pausing for a separate confirmation step, then continue into the requested analysis.
- Read `notes.md` inside a referenced source folder when present. Treat it as an explicit intent layer that may refine or override the chat-message signal.
- Use semantic routing rather than exact-string matching. Route from path mentions, surrounding verbs, and current repository state together.
- Support both Chinese and English trigger language when mapping requests to routes.
- Reuse existing structured pages when coverage is already sufficient; avoid redundant ingest.
- Ask a clarifying question only when the referenced path does not exist, multiple candidate targets conflict materially, or the requested analysis target is too ambiguous to resolve from repository context.
- Use `docs/workflows/router.md` as the concrete route map for intent families and default action sequences.
```

- [ ] **Step 2: Add a router reference to `Query`**

Replace the current `### Query` block:

```markdown
### Query
Read `research-map.md` and `index.md` first, then relevant topic, paper, idea, and asset pages. File valuable outputs back into the wiki.
```

With this exact text:

```markdown
### Query
Route natural-language research requests using `docs/workflows/router.md`.
Read `research-map.md` and `index.md` first, then relevant topic, paper, idea, and asset pages. If the request references new `sources/...` material, perform the coverage check and ingest-first behavior from `Intent Routing` before answering. File valuable outputs back into the wiki.
```

- [ ] **Step 3: Add the router doc to `Operational Helpers`**

Append this line to the existing `## Operational Helpers` section:

```markdown
- `docs/workflows/router.md` is the concrete semantic route map for natural-language user requests.
```

- [ ] **Step 4: Verify the new routing rules are wired into `AGENTS.md`**

Run: `rg -n "### Intent Routing|notes\\.md inside a referenced source folder|Support both Chinese and English trigger language|docs/workflows/router\\.md is the concrete semantic route map|If the request references new `sources/...` material" /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md`
Expected: the new `Intent Routing` section, multilingual rule, query handoff, and helper reference all appear.

- [ ] **Step 5: Commit the `AGENTS.md` routing update**

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI add /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md
git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: add intent routing rules to agents guide"
```

### Task 3: Update Onboarding To Use Natural-Language Requests

**Files:**
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/README.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/docs/getting-started/first-ingest.md`
- Modify: `/Users/chendongyao/Desktop/LLM-WIKI/sources/README.md`

- [ ] **Step 1: Update `README.md` quick-start language**

Replace the current Quick Start steps 3-6:

```markdown
3. Create a folder under `sources/` and drop your materials there.
4. Optionally add a short `notes.md` describing why the material matters or what you want from the ingest.
5. Ask your LLM agent to `ingest` that source folder using the pipeline in `AGENTS.md`.
6. Review the new or updated pages under `papers/`, `topics/`, `ideas/`, and `assets/`.
```

With this exact text:

```markdown
3. Create a folder under `sources/` and drop your materials there.
4. Optionally add a short `notes.md` describing why the material matters, what you want from ingest, or what idea you are exploring.
5. Tell your LLM agent what new material you added and what you want to know, in natural language.
6. Let the agent route the request, ingest missing coverage, and update the wiki.
7. Review the new or updated pages under `papers/`, `topics/`, `ideas/`, `assets/`, and `reviews/`.
```

Add this paragraph directly below the Quick Start list:

```markdown
Natural-language requests are preferred over command-style prompts. Example: `我补充了 sources/hacrl，我想看看这个训练能不能和 PDA 结合在一起。`
```

- [ ] **Step 2: Replace the command-style prompt in `docs/getting-started/first-ingest.md`**

Replace the entire `## Tell The Agent What To Do` section:

```markdown
## Tell The Agent What To Do

Use a prompt like:

```text
Ingest the source folder at sources/wei-2022-chain-of-thought/ using the pipeline in AGENTS.md. Read notes.md if present. Update topics, ideas, assets, index.md, and log.md as needed. Do not write compiled wiki output into sources/.
```
```

With this exact text:

```markdown
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
```

- [ ] **Step 3: Add a natural-language example to `sources/README.md`**

Append this section to the end of `sources/README.md`:

```markdown
## How To Ask The Agent

You do not need to issue a command-style ingest prompt.

Good natural-language requests:

- `我补充了 sources/hacrl，先帮我整理这篇论文最值得学的地方。`
- `我补充了 sources/hacrl，我想看看这个训练能不能和 PDA 结合在一起。`
- `I added sources/hacpo. Can you see whether its reward design is reusable for my current agent idea?`

The agent should inspect the referenced source folder, read `notes.md` if present, ingest missing coverage, and then continue into the research question.
```

- [ ] **Step 4: Verify all onboarding docs now prefer natural-language requests**

Run: `rg -n "natural-language requests are preferred|Tell your LLM agent what new material you added|Use a natural-language request like|You do not need to issue a command-style ingest prompt|sources/hacrl" /Users/chendongyao/Desktop/LLM-WIKI/README.md /Users/chendongyao/Desktop/LLM-WIKI/docs/getting-started/first-ingest.md /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md`
Expected: the new natural-language guidance appears in all three files.

- [ ] **Step 5: Commit the onboarding updates**

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI add /Users/chendongyao/Desktop/LLM-WIKI/README.md /Users/chendongyao/Desktop/LLM-WIKI/docs/getting-started/first-ingest.md /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md
git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: switch onboarding to natural-language routing"
```

### Task 4: Verify Cross-Document Consistency

**Files:**
- Verify: `/Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md`
- Verify: `/Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md`
- Verify: `/Users/chendongyao/Desktop/LLM-WIKI/README.md`
- Verify: `/Users/chendongyao/Desktop/LLM-WIKI/docs/getting-started/first-ingest.md`
- Verify: `/Users/chendongyao/Desktop/LLM-WIKI/sources/README.md`

- [ ] **Step 1: Verify the repository has the new router file and no dead reference**

Run: `test -f /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md && rg -n "docs/workflows/router\\.md" /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md /Users/chendongyao/Desktop/LLM-WIKI/docs/superpowers/specs/2026-04-11-intent-router-design.md`
Expected: the file exists and both `AGENTS.md` and the spec reference it.

- [ ] **Step 2: Verify multilingual semantic-routing coverage**

Run: `rg -n "Chinese and English|我补充了|I added|结合|combine|复刻|replicate|支持|support" /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md`
Expected: both Chinese and English route examples appear across the router and onboarding docs.

- [ ] **Step 3: Verify no onboarding doc still prefers command-style ingest as the primary workflow**

Run: `rg -n "Ask your LLM agent to .ingest.|Ingest the source folder at" /Users/chendongyao/Desktop/LLM-WIKI/README.md /Users/chendongyao/Desktop/LLM-WIKI/docs/getting-started/first-ingest.md /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md || true`
Expected: no matches for the old command-style-first wording remain. (Note: the phrase "command-style ingest prompt" intentionally appears in sources/README.md as a negation and is not a false positive — exclude it from this check.)

- [ ] **Step 4: Run a markdown integrity check on the touched files**

Run: `git -C /Users/chendongyao/Desktop/LLM-WIKI diff --check -- /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md /Users/chendongyao/Desktop/LLM-WIKI/README.md /Users/chendongyao/Desktop/LLM-WIKI/docs/getting-started/first-ingest.md /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md`
Expected: no whitespace or patch-format errors.

- [ ] **Step 5: Commit only if verification required fixes**

```bash
git -C /Users/chendongyao/Desktop/LLM-WIKI diff --quiet -- /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md /Users/chendongyao/Desktop/LLM-WIKI/README.md /Users/chendongyao/Desktop/LLM-WIKI/docs/getting-started/first-ingest.md /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md || (
  git -C /Users/chendongyao/Desktop/LLM-WIKI add /Users/chendongyao/Desktop/LLM-WIKI/AGENTS.md /Users/chendongyao/Desktop/LLM-WIKI/docs/workflows/router.md /Users/chendongyao/Desktop/LLM-WIKI/README.md /Users/chendongyao/Desktop/LLM-WIKI/docs/getting-started/first-ingest.md /Users/chendongyao/Desktop/LLM-WIKI/sources/README.md &&
  git -C /Users/chendongyao/Desktop/LLM-WIKI commit -m "docs: wire semantic intent routing across repository guides"
)
```

---

## Coverage Check

- The plan adds the new `docs/workflows/router.md` artifact required by the spec.
- The plan adds principle-level routing rules to `AGENTS.md`.
- The plan updates onboarding so users describe materials and research goals naturally instead of issuing explicit workflow commands.
- The plan includes verification for `notes.md` routing behavior, multilingual trigger coverage, and cross-document consistency.
- The plan stays within Phase 1 scope: markdown-only documentation and schema updates, with no CLI or watcher work.
