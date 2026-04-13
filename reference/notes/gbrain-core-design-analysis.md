---
type: review
title: "GBrain Core Design Analysis"
slug: gbrain-core-design-analysis
tags: [gbrain, knowledge-base, agent-system, research-wiki, architecture]
created: 2026-04-12
updated: 2026-04-12
---

# GBrain Core Design Analysis

## Executive Takeaway

GBrain 的价值不在于“把 markdown 接到一个向量库”，而在于把个人/组织知识库拆成三个可协作层：人和 LLM 都能读写的 markdown brain、可确定执行的 CLI/DB/index 层、以及把判断过程固化下来的 skillpack。它的核心设计是：让 LLM 做判断、综合、改写、归档，让代码做同步、检索、索引、健康检查、权限和一致性校验。

对 LLM-WIKI 来说，最值得偷的是操作纪律和架构边界，而不是直接复制 people/companies/deals 这些业务 schema。我们的研究 wiki 当前仍应保持轻量 markdown-first，但可以吸收 GBrain 的 resolver、compiled truth + timeline、contract-first operations、doctor/lint、hybrid search、skillpack/runbook 化等设计，逐步把“研究搭子”从一次性对话变成会自我维护的系统。

## What GBrain Is

GBrain 是一个面向 LLM agent 的长期知识系统。它不是单纯的笔记模板，也不是普通 RAG 系统。它把知识维护拆成三个角色：

- 用户负责提供信号、纠正方向、提出问题。
- Agent 负责读写 markdown、维护交叉引用、综合当前判断、记录时间线。
- CLI/DB 负责确定性的读写、检索、同步、版本、健康检查和权限边界。

GBrain 的默认领域是个人/组织 intelligence：people、companies、deals、meetings、projects、ideas、concepts、writing 等。它的原始 schema 不适合直接搬到计算机科研 wiki，但它处理“知识会持续更新、证据会互相矛盾、agent 必须知道写到哪里”的方式非常适合借鉴。

## Core Thesis

GBrain 的核心 thesis 可以压缩成四句话：

1. 知识库失败不是因为用户不会写笔记，而是因为维护成本落在人身上。
2. LLM agent 擅长不知疲倦地读、改、链接、重写、归档，因此知识维护应该 agent-owned。
3. 判断性工作留给 skill 和 LLM，确定性工作下沉到 CLI、SQL、索引和 health checks。
4. 每次信号进入系统，都应该触发 read-before-respond、write-after-learn、sync-after-write 的闭环。

这与 Karpathy 的 llm-wiki 思想一致：LLM 维护一个可读、可积累、可压缩的 wiki。但 GBrain 更强调 operationalization：不仅是“我丢资料进去，LLM 帮我整理”，而是“任何触及知识实体的事件都自动让 brain 变聪明”。

## Architecture Layers

### Layer 1: Markdown Brain

Markdown 是人类和 LLM 的共享界面。GBrain 强调 MECE directories：每类知识有唯一 primary home，每个目录有 README resolver，根目录有 RESOLVER.md 作为总决策树。这个设计的关键不是目录好看，而是避免同一个事实被 agent 写到多个地方，最后产生 split-brain。

对 LLM-WIKI 的启发是：我们已经有 `sources/`、`topics/`、`papers/`、`ideas/`、`assets/`、`reviews/`、`logs/`，但仍可以补强“每个目录的 resolver”。现在 `AGENTS.md` 给了全局规则，`docs/workflows/router.md` 给了意图路由，但目录级“什么放这里、什么不放这里、和邻近目录怎么区分”还不够强。

### Layer 2: Deterministic Storage And Retrieval

GBrain 的 DB schema 包含 `pages`、`content_chunks`、`links`、`tags`、`raw_data`、`timeline_entries`、`page_versions`、`ingest_log`、`files`、`access_tokens`、`mcp_request_log`。这说明它不是只把文件扔给向量检索，而是显式建模：

- 当前页内容：`compiled_truth`
- 证据时间线：`timeline`
- 分块和 embedding：`content_chunks`
- 图关系：`links`
- 原始证据：`raw_data`
- 操作轨迹：`ingest_log`
- 版本快照：`page_versions`
- 二进制附件：`files`

对我们来说，现阶段不需要立刻引入 Postgres/PGLite，否则会过早增加系统负担。但这个模型可以作为未来 CLI 层的目标蓝图：先用 frontmatter、WikiLink、`index.md`、`log.md` 实现弱版本，等真实 ingest 增多后再考虑把检索和一致性校验下沉到数据库。

### Layer 3: Skills And Recipes

GBrain 的 `skills/ingest`、`skills/query`、`skills/maintain` 体现了“thin harness, fat skills”。Skill 文件不是普通文档，而是可复用的操作过程：什么时候读什么、怎么判断、怎么写回、怎么验证。

LLM-WIKI 目前把大量规则放在 `AGENTS.md` 中，这适合早期启动，但长期会变成上下文负担。更好的演进方向是：`AGENTS.md` 保持高层不变量，具体操作拆成技能式文档，例如：

- `docs/workflows/ingest.md`：11 步 Parse -> Log 的执行细节。
- `docs/workflows/query.md`：自然语言研究请求如何检索、综合、写回。
- `docs/workflows/maintain.md`：lint、重审、过期 hypothesis、链接修复。
- `docs/workflows/router.md`：保留为入口路由和触发条件。

## Operational Loop

GBrain 的 brain-agent loop 是：

`signal -> detect -> read brain first -> respond with context -> write updates -> sync -> next signal`

关键不在“entity detection”本身，而在两个不变量：

- 回答前先读 brain，否则就是用过期上下文回答。
- 学到新信息后立刻写回，否则未来查询会继续丢失这次信号。

映射到 LLM-WIKI，可以变成研究版循环：

`new source or question -> route intent -> check existing coverage -> ingest if missing/stale -> answer/research -> update papers/topics/assets/ideas -> log`

这正好对应我们已经加入的 intent routing：用户只需要说“我补充了 `sources/hacrl`，想看看能不能和 PDA 结合”，系统不应该要求用户手动复制长 prompt，而应该自动做 coverage check，必要时先 ingest，再进入组合分析。

## Compiled Truth + Timeline

GBrain 的页面结构分为两层：

- 上半部分 compiled truth：当前最有用的综合判断，会被重写。
- 下半部分 timeline：证据轨迹，append-only，不改旧记录。

这比普通日志型笔记强，因为查询时优先读“已经压缩好的当前判断”，而不是每次从几十条历史记录重新推理。

对 LLM-WIKI 的直接翻译：

- `papers/*.md`：上半部分是当前对论文的结构化判断；下半部分可放 ingest/review timeline 或 source notes。
- `ideas/hypothesis/*.md`：上半部分是当前可做问题、假设漏洞、最小实验；下半部分是来源论文和每次重审记录。
- `topics/*.md`：上半部分是当前研究空间判断；下半部分是路线变化、争议变迁、重要更新。
- `assets/*.md`：上半部分是可复用模式；下半部分是出现过该模式的论文证据。

注意：这不等于每个页面都必须机械加 timeline。更合理的是先在 hypothesis/proposal/topic survey 这类会持续演化的页面上采用。对一次性 paper note，可以先保留现有结构，等页面被多次更新再引入 timeline。

## Search And Context Retrieval

GBrain 的检索实现有几个很实用的设计：

- Keyword search 永远可用，不依赖 API key。
- Vector search 是增强项，失败时降级为 keyword-only。
- Query expansion 只对 3 个词以上的问题触发，最多保留 3 个查询，避免膨胀。
- Hybrid search 用 Reciprocal Rank Fusion 合并 keyword 和 vector 结果，避免不同打分尺度不可比。
- Dedup 不是简单去重，而是四层控制：按 page/source、按文本相似度、按 page type 分布、按每页 chunk 上限。

这对 LLM-WIKI 后续 CLI 层很有启发：研究问题检索很容易被同一篇综述、同一类 paper、同一种 topic 淹没。未来如果实现 `wiki query`，应该不仅返回 top-k，还要控制来源多样性，例如每个 paper 最多 2 段、每个 page type 不超过 60%、topic/paper/idea/asset 都有机会出现。

当前阶段可以不做向量库，但可以先把这个原则写进 query workflow：检索上下文时不要只读最相关的 paper，还要至少检查 topic、idea、asset 三类页面，避免“局部最相关”压制“系统性有用”。

## CLI / Operation Layer

GBrain 的 `operations.ts` 是很重要的工程设计：它把每个操作的 name、description、params、handler、mutating、cliHints 放在一个 contract-first 层。这样 CLI、MCP、tools-json 可以共享同一套操作定义，而不是各自维护一套行为。

`BrainEngine` 是另一个值得注意的边界：上层 operation 不直接依赖 Postgres/PGLite 细节，而是依赖 `getPage`、`putPage`、`searchKeyword`、`searchVector`、`addLink`、`getBacklinks`、`addTimelineEntry`、`getHealth` 这类能力接口。这个抽象让“agent 操作语义”和“底层存储实现”分离。对 LLM-WIKI 来说，未来即使先用纯文件实现，也可以提前按类似能力接口思考，而不是把脚本写死在某个目录扫描方式上。

这对我们未来的 Obsidian/CLI 层很关键。LLM-WIKI 不应该一上来写很多零散脚本，例如 `new-paper.sh`、`fix-index.py`、`query-topic.ts` 各自定义参数。更好的方式是先定义统一 operation contract：

- `source_folder_init`
- `ingest_source`
- `new_paper`
- `new_hypothesis`
- `promote_proposal`
- `update_index`
- `lint_wiki`
- `query_research_map`
- `doctor`

每个 operation 声明是否 mutating、需要哪些字段、dry-run 如何表现、会写哪些目录。这样后续接 CLI、MCP、Obsidian 插件都不会分叉。

## Sync, Import, And Health Discipline

GBrain 的 import/sync/doctor 体现出一个成熟知识系统需要的工程纪律：

- sync 基于 git commit 做增量导入，避免每次全量扫。
- import 支持 checkpoint/resume、worker、no-embed、ingest log。
- doctor 检查连接、pgvector、RLS、schema version、embedding 配置。
- mutating operations 支持 dry-run。

LLM-WIKI 当前最需要的不是数据库版 sync，而是轻量 doctor/lint：

- 检查所有非豁免 wiki 页是否有 `type/title/slug/created/updated`。
- 检查 paper `paper_id` 是否等于文件 slug。
- 检查 idea 的 `source_papers` 和 paper 的 `inspired_ideas` 是否双向一致。
- 检查 `index.md` 是否覆盖新页面。
- 检查 `research-map.md` 是否包含主要 topic。
- 检查 `sources/` 中是否有未 ingest 的 source folder。

这类 deterministic 检查应该尽量用脚本做，不要每次让 LLM 手动读全库。

## Design Patterns To Steal

### 1. Resolver As First-Class Context

GBrain 的 resolver 是“agent 写文件前必须读的决策树”。LLM-WIKI 已经有 intent router，但可以进一步加目录级 resolver，尤其是 `assets/` 下几个容易混淆的目录：

- mechanism vs experiment-pattern
- evaluation-pattern vs failure-mode
- failure-mode vs negative-asset
- hypothesis vs proposal
- review vs topic survey

这会减少 agent 把“一个实验设计”误写成 mechanism，或把“论文局限”误写成 negative-asset。

### 2. Compiled Current Claim + Evidence Trail

研究 wiki 最容易腐烂的是 hypothesis：它创建后只会堆来源，不会被重写。GBrain 的 compiled truth + timeline 可以改造它：hypothesis 顶部永远是当前可做问题和可行性判断，底部记录每次来源支持/反驳/修正。这样后续看到的不是“idea 历史垃圾堆”，而是最新判断。

### 3. Thin Harness, Fat Workflows

不要让 CLI 负责“判断这篇论文的核心贡献是什么”。CLI 只负责创建页面、检查 schema、更新索引、查找 backlinks、跑 lint。判断过程留给 workflow/skill 文档和 LLM。这与我们当前“半自动研究搭子”目标一致。

### 4. Query Diversity Controls

GBrain 的 dedup/type diversity 对研究检索很重要。未来 query 结果不应只按相似度排序，而要强制混合：

- topic map：提供研究空间位置。
- paper pages：提供原始论文判断。
- idea pages：提供已有可做问题。
- assets：提供可迁移机制/实验设计/失败模式。

这比单纯 top-k 更适合“产出 research ideas”。

### 5. Dry-Run As Real Operation Type

GBrain 把 dry-run 当成正式执行路径，而不是口头承诺。LLM-WIKI 已经把 dry-run 写进 `log.md` 类型，这个方向是对的。后续 CLI 应继续保证 dry-run 能显示将创建/修改哪些页面，但不写盘。

### 6. Health Checks Before Ambition

GBrain 的 doctor 说明长期系统必须能自检。对我们来说，在做向量库、Obsidian 插件或 MCP 前，应先做 markdown 层 doctor。一个不能验证 schema/link/index 的 wiki，不应该急着引入更复杂索引。

## What Not To Copy Directly

### Do Not Copy The Business Schema

GBrain 的 people/companies/deals/meetings schema 是为 operational intelligence 设计的。LLM-WIKI 的核心对象是 research problem、paper、mechanism、experiment pattern、evaluation pattern、failure mode、negative asset、hypothesis、proposal。直接复制会污染研究 wiki。

### Do Not Start With Postgres

GBrain 的 DB 层很强，但当前 LLM-WIKI 的关键风险不是查询性能，而是 ingest 质量、idea 生命周期、目录边界、低摩擦输入。如果现在引入 Postgres/PGLite，很可能让用户把精力花在 schema migration 和索引调试上，而不是第一批真实论文的高质量整理。

### Do Not Over-Automate Every Message

GBrain 的“every signal fires enrichment”适合 email/meeting/social 这类高频 operational flow。研究 wiki 不一定需要每条对话都自动生成页面。更合适的边界是：当用户明确提到 `sources/...`、论文、repo、实验想法、topic 组合时，才触发 ingest/query/update。

### Do Not Let AGENTS.md Become A 20,000-Line Brain

GBrain 的 ethos 文档明确批判超长 agent config。LLM-WIKI 的 `AGENTS.md` 已经包含大量规则，后续应逐渐把细节迁移到 workflow docs，只在 `AGENTS.md` 保留硬约束和路由入口。

## Translation To LLM-WIKI

短期建议：

1. 给核心目录补 README resolver，尤其是 `papers/`、`ideas/`、`assets/`、`reviews/`、`sources/`。
2. 把 `AGENTS.md` 的细节拆一部分到 `docs/workflows/ingest.md`、`query.md`、`maintain.md`，`AGENTS.md` 只保留入口规则和必须读哪些文件。
3. 给 hypothesis/proposal 引入“当前判断 + evidence timeline”结构。
4. 写一个轻量 doctor/lint 脚本，先验证 frontmatter、slug、index、bidirectional links。
5. 更新 query workflow，要求上下文检索覆盖 topic/paper/idea/asset 多类型，而不是只读 paper。

中期建议：

1. 设计 contract-first operations 文档，先不写完整 CLI，也先定义 operation schema。
2. 让 `docs/workflows/router.md` 成为 resolver 入口，再由目录 README 决定文件落点。
3. 如果真实论文超过 30-50 篇，再考虑本地索引：keyword-first，vector optional，query expansion optional。
4. 用 git commit 或 file mtime 做增量 sync，而不是每次全量扫描。

长期建议：

1. 发展 Obsidian + CLI 双界面：Obsidian 负责可视化和手动浏览，CLI 负责批量更新和验证。
2. 将 idea/proposal 的 evidence trail 结构化，支持“哪些 idea 被新论文支持/反驳/需要重审”。
3. 对 research-map 引入 drift detector：新增 topic、路线分歧、proposal 升级时自动提示地图是否需要更新。

## Supplementary Findings

以下六个机制在初版分析中未覆盖，均来自源码和 skill 文件的二次审核。

### Lint 规则集（`src/commands/lint.ts`）

GBrain 的 `gbrain lint` 是零 LLM 调用的确定性检查器，支持 `--fix` 和 `--dry-run`。它检测的规则集对 LLM-WIKI 的 doctor 脚本设计直接可用：

| 规则 | 描述 | 可自动修复 |
|------|------|-----------|
| `llm-preamble` | 检测 "Of course! Here is..." 等 LLM 开场白污染 | 是 |
| `code-fence-wrap` | 检测页面被 ` ```markdown ``` ` 包裹（LLM artifact） | 是 |
| `placeholder-date` | 检测未填写的 `YYYY-MM-DD`、`XX-XX` 占位日期 | 否 |
| `missing-title/type/created` | 检测 frontmatter 缺少必填字段 | 否 |
| `broken-citation` | 检测未闭合的 `[Source: ...]` 括号 | 否 |
| `empty-section` | 检测空 section 或 `[No data yet]` 占位内容 | 否 |

可自动修复的两类（LLM preamble、code fence wrap）是 LLM 写页面时最常见的污染，GBrain 用正则批量清除。对 LLM-WIKI 来说，`llm-preamble` 和 `placeholder-date` 是最值得先实现的两条规则。

**对 LLM-WIKI 的翻译：** doctor 脚本的第一版应包含这六条规则的 markdown 静态检查，不需要 LLM，不需要数据库。可以先写成 `docs/workflows/doctor.md` 的 agent 执行规范，再逐步脚本化。

### Backlink 修复策略（`src/commands/backlinks.ts`）

GBrain 的 `gbrain check-backlinks` 实现了确定性的双向链接修复：

1. 扫描所有页面，用正则提取 `[Name](../people/slug.md)` 格式的 entity 引用
2. 检查目标页面是否已包含源页面文件名（即是否有反向链接）
3. `fix` 模式：在目标页面的 `## Timeline` section 追加条目 `- **YYYY-MM-DD** | Referenced in [title](path)`
4. 按目标页面分组批量写入，避免多次读写同一文件
5. 完整支持 `--dry-run`

关键设计：反向链接追加到 Timeline，而不是正文。这保持了 compiled truth 的干净，同时让图关系可追溯。

**对 LLM-WIKI 的翻译：** `papers.inspired_ideas` ↔ `ideas.source_papers` 的双向一致性检查可以用同样的模式实现：扫描 paper 页面的 `inspired_ideas` 列表，检查对应 idea 页面的 `source_papers` 是否包含该 paper，反之亦然。这是 doctor 脚本里最值得优先实现的一条规则。

### Nightly Dream Cycle（`docs/guides/operational-disciplines.md`）

GBrain 的五条 operational disciplines 中，第五条"nightly dream cycle"是最重要的，也是文档初版完全未提的：

```
# 每晚 02:00 自动运行
5a: entity sweep
    扫描所有页面，找未链接的 entity mention
    → 自动补 add_link

5b: citation audit
    找没有 [Source: ...] 的事实
    → 标记待修复，不自动改

5c: memory consolidation
    对 7 天以上未更新的页面，从 timeline 重新综合 compiled truth
    → 重写顶部 compiled truth，保留 timeline 不动

5d: sync
    gbrain sync
```

这个"大脑在夜间自我整理"的模式体现了一个重要原则：**知识系统的腐烂是渐进的，必须有定期的主动整理机制，而不是等到用户发现问题再修**。

**对 LLM-WIKI 的翻译：** 研究 wiki 的对应版本：

- entity sweep → 扫描 paper 页面里提到的 idea/asset，检查是否有对应的 WikiLink
- citation audit → 检查 hypothesis/proposal 的 `source_papers` 是否都有对应的 paper 页面
- memory consolidation → 对超过 30 天未更新的 hypothesis，检查是否有新论文支持/反驳，提示重审
- 不需要每晚自动运行，可以作为 lint 操作的一部分，由用户手动触发

### 三个 Chunker 实现（`src/core/chunkers/`）

GBrain 包含三个 chunker 实现，当前核心 import 路径（`src/core/import-file.ts`）默认直接使用 recursive。semantic 和 LLM-guided 是可选的更高质量 chunker，各自在失败时独立 fallback 到 recursive，而不是形成自动降级链。

**recursive（基础，无需 API）**
按词数递归分块，有 overlap，始终可用。当前 import 默认路径。

**semantic（需要 embedding API，可选）**
1. 把文本切成句子
2. 对每个句子做 embedding
3. 计算相邻句子的余弦相似度
4. 用 Savitzky-Golay 一阶导数检测局部最小点，再按低相似度阈值过滤，确定 topic boundary
5. 按 boundary 分组，过大的组递归用 recursive 再切
6. 任何步骤失败 → fallback 到 recursive

**LLM-guided（需要 LLM API，最准确，可选）**
1. 先用 recursive 切成 128-word 候选块
2. 滑动窗口（5 个候选块）
3. 问 Claude Haiku："第一个 topic shift 在哪里？"只回答数字或 NONE
4. 最多 3 次 retry，失败 → fallback 到 recursive
5. 按 split point 合并候选块

这三个实现的核心价值不是 chunking 算法本身，而是**各自独立的 fallback 设计**：每个高质量 chunker 都在失败时安全降级，系统不会因为 API 不可用而崩溃。

**对 LLM-WIKI 的翻译：** 当前阶段不需要实现 chunking，但这个独立 fallback 模式可以用在未来的 query 检索层：先尝试 vector search，失败降级到 keyword search，始终可用。

### Report Audit Trail（`src/commands/report.ts`）

GBrain 的 `gbrain report` 是一个零 LLM 调用的结构化报告保存命令：

```bash
gbrain report --type enrichment-sweep --title "Enrichment Sweep" --content "..."
echo "report body" | gbrain report --type meeting-sync --title "Meeting Sync"
```

输出到 `brain/reports/{type}/{YYYY-MM-DD-HHMM}.md`，frontmatter 包含 `report_type`、`date`、`time`。

设计意图：每次 enrichment sweep、maintenance run、backlink check、lint 后，agent 保存一份结构化报告。这不是 `log.md` 的替代，而是操作过程的详细 audit trail，和 `log.md` 的全局时间线互补。

**对 LLM-WIKI 的翻译：** 这正好对应我们已有的 `logs/` 目录设计。区别在于 GBrain 用 CLI 命令保存报告（确定性，零 LLM），而 LLM-WIKI 目前让 agent 手动写 `logs/` 文件。后续 doctor 脚本运行后，应该输出结构化报告到 `logs/`，而不是依赖 agent 手写。

### Publish 的内容剥离策略（`src/commands/publish.ts`）

GBrain 的 `makeShareable()` 函数定义了"什么是私有的、不应该对外分享的"，这个边界划分本身就是一个设计决策：

剥除的内容：
- YAML frontmatter（内部 schema，不对外）
- 所有 `[Source: ...]` 引用（内部溯源，不对外）
- brain 内部交叉链接（保留显示文本，去掉路径）
- `## Timeline` section（append-only 的证据轨迹，不对外）
- "See also" 内部链接行

保留的内容：
- compiled truth 正文
- 所有 section 标题和内容（Timeline 除外）

可选 AES-256-GCM 加密（client-side，无需服务器），`--password` 自动生成或手动指定。

**对 LLM-WIKI 的翻译：** 如果未来需要分享 review 或 proposal，这个剥离策略可以直接复用：frontmatter 和 evidence timeline 是内部的，compiled judgment 是可以对外的。这也反过来说明为什么 compiled truth 和 timeline 要分开写——分开写才能分开发布。

## Concrete Follow-Up Ideas

### Idea 1: Directory Resolver Pass

为每个核心目录写 `README.md`，格式固定为：

- What goes here
- What does not go here
- Neighboring directories to check
- Required frontmatter
- Example good page
- Example wrong placement

这个改动小，但能显著提高 agent 自动写页的稳定性。

### Idea 2: Hypothesis Evidence Timeline

修改 hypothesis/proposal 模板，加入：

- `## Current Claim`
- `## Feasibility Judgment`
- `## Minimal Experiment`
- `---`
- `## Evidence Timeline`

每次 ingest 后，如果新论文支持/反驳该 idea，就只追加 timeline，同时重写顶部 current claim。

### Idea 3: Research Wiki Doctor

实现 `scripts/doctor-wiki` 或 `docs/workflows/doctor.md` 的第一版，先做 markdown 静态检查。不要一开始做 embeddings。目标是让 agent 在每次大 ingest 后可以运行 deterministic validation。

### Idea 4: Query Diversity Rule

在 `docs/workflows/router.md` 或未来 `query.md` 中加入硬规则：综合研究问题时，至少检查 `research-map.md`、`index.md`、一个相关 topic、相关 paper、相关 idea、相关 asset。若某类不存在，要在回答中说明“该类暂无覆盖”，而不是静默忽略。

### Idea 5: Operation Contract Sketch

新增 `docs/architecture/operations.md`，把未来 CLI 操作先定义为 contract：参数、是否写盘、读哪些文件、写哪些文件、dry-run 输出。这样后续写 CLI 时不会变成临时脚本集合。

## Source Files Read

- `reference/raw/gbrain-master/README.md`
- `reference/raw/gbrain-master/package.json`
- `reference/raw/gbrain-master/docs/GBRAIN_RECOMMENDED_SCHEMA.md`
- `reference/raw/gbrain-master/docs/GBRAIN_SKILLPACK.md`
- `reference/raw/gbrain-master/docs/guides/repo-architecture.md`
- `reference/raw/gbrain-master/docs/guides/brain-agent-loop.md`
- `reference/raw/gbrain-master/docs/guides/brain-first-lookup.md`
- `reference/raw/gbrain-master/docs/guides/compiled-truth.md`
- `reference/raw/gbrain-master/docs/guides/enrichment-pipeline.md`
- `reference/raw/gbrain-master/docs/guides/operational-disciplines.md`
- `reference/raw/gbrain-master/docs/guides/skill-development.md`
- `reference/raw/gbrain-master/docs/guides/sub-agent-routing.md`
- `reference/raw/gbrain-master/docs/ethos/THIN_HARNESS_FAT_SKILLS.md`
- `reference/raw/gbrain-master/docs/ethos/MARKDOWN_SKILLS_AS_RECIPES.md`
- `reference/raw/gbrain-master/skills/_brain-filing-rules.md`
- `reference/raw/gbrain-master/skills/ingest/SKILL.md`
- `reference/raw/gbrain-master/skills/query/SKILL.md`
- `reference/raw/gbrain-master/skills/maintain/SKILL.md`
- `reference/raw/gbrain-master/skills/enrich/SKILL.md`
- `reference/raw/gbrain-master/skills/briefing/SKILL.md`
- `reference/raw/gbrain-master/src/cli.ts`
- `reference/raw/gbrain-master/src/core/operations.ts`
- `reference/raw/gbrain-master/src/core/engine.ts`
- `reference/raw/gbrain-master/src/schema.sql`
- `reference/raw/gbrain-master/src/commands/sync.ts`
- `reference/raw/gbrain-master/src/commands/import.ts`
- `reference/raw/gbrain-master/src/commands/doctor.ts`
- `reference/raw/gbrain-master/src/commands/lint.ts`
- `reference/raw/gbrain-master/src/commands/backlinks.ts`
- `reference/raw/gbrain-master/src/commands/publish.ts`
- `reference/raw/gbrain-master/src/commands/report.ts`
- `reference/raw/gbrain-master/src/core/chunkers/recursive.ts`
- `reference/raw/gbrain-master/src/core/chunkers/semantic.ts`
- `reference/raw/gbrain-master/src/core/chunkers/llm.ts`
- `reference/raw/gbrain-master/src/core/search/hybrid.ts`
- `reference/raw/gbrain-master/src/core/search/keyword.ts`
- `reference/raw/gbrain-master/src/core/search/vector.ts`
- `reference/raw/gbrain-master/src/core/search/dedup.ts`
- `reference/raw/gbrain-master/src/core/search/expansion.ts`
