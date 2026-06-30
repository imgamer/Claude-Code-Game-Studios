# Director Gates — Shared Review Pattern
# 总监门禁 — 共享评审模式

This document defines the standard gate prompts for all director and lead reviews
across every workflow stage. Skills reference gate IDs from this document instead
of embedding full prompts inline — eliminating drift when prompts need updating.
本文档定义了所有总监和主管评审在每个工作流阶段的标准门禁提示词。技能通过本文档引用门禁 ID，而非内嵌完整提示词 — 当提示词需要更新时消除漂移。

**Scope**: All 7 production stages (Concept → Release), all 3 Tier 1 directors,
all key Tier 2 leads. Any skill, team orchestrator, or workflow may invoke these gates.
**范围**：所有 7 个生产阶段（概念 → 发布）、所有 3 位层级 1 总监、所有关键层级 2 主管。任何技能、团队编排器或工作流均可调用这些门禁。

---

## How to Use This Document
## 如何使用本文档

In any skill, replace an inline director prompt with a reference:
在任何技能中，将内嵌的总监提示词替换为引用：

```
Spawn `creative-director` via Task using gate **CD-PILLARS** from
`.claude/docs/director-gates.md`.
```

Pass the context listed under that gate's **Context to pass** field, then handle
the verdict using the **Verdict handling** rules below.
传递该门禁 **Context to pass** 字段下列出的上下文，然后使用下方的 **Verdict handling** 规则处理裁决结果。

---

## Review Modes
## 评审模式

Review intensity controls whether director gates run. It can be set globally
(persists across sessions) or overridden per skill run.
评审强度控制总监门禁是否运行。可全局设置（跨会话持久）或按技能运行覆盖。

**Global config**: `production/review-mode.txt` — one word: `full`, `lean`, or `solo`.
Set once during `/start`. Edit the file directly to change it at any time.
**全局配置**：`production/review-mode.txt` — 单词：`full`、`lean` 或 `solo`。
在 `/start` 时设置一次。随时可直接编辑文件更改。

**Per-run override**: any gate-using skill accepts `--review [full|lean|solo]` as an
argument. This overrides the global config for that run only.
**按运行覆盖**：任何使用门禁的技能接受 `--review [full|lean|solo]` 作为参数。仅覆盖该次运行的全局配置。

Examples:
示例：
```
/brainstorm space horror           → uses global mode
/brainstorm space horror --review full   → forces full mode this run
/architecture-decision --review solo     → skips all gates this run
```

| Mode | What runs | Best for |
| 模式 | 运行内容 | 适用场景 |
|------|-----------|----------|
| `full` | All gates active — every workflow step reviewed | Teams, learning users, or when you want thorough director feedback at every step |
| `full` | 所有门禁激活 — 每个工作流步骤都评审 | 团队、学习中的用户，或希望每步都获得详尽总监反馈时 |
| `lean` | PHASE-GATEs only (`/gate-check`) — per-skill gates skipped | **Default** — solo devs and small teams; directors review at milestones only |
| `lean` | 仅 PHASE-GATE（`/gate-check`）— 跳过各技能内门禁 | **默认** — 独立开发者和小团队；总监仅在里程碑时评审 |
| `solo` | No director gates anywhere | Game jams, prototypes, maximum speed |
| `solo` | 任何地方都不运行总监门禁 | 游戏开发挑战赛、原型、最大速度 |

**Check pattern — apply before every gate spawn:**
**检查模式 — 在每次生成门禁前应用：**

```
Before spawning gate [GATE-ID]:
1. If skill was called with --review [mode], use that
2. Else read production/review-mode.txt
3. Else default to lean

Apply the resolved mode:
- solo → skip all gates. Note: "[GATE-ID] skipped — Solo mode"
- lean → skip unless this is a PHASE-GATE (CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE)
         Note: "[GATE-ID] skipped — Lean mode"
- full → spawn as normal
```

---

## Invocation Pattern (copy into any skill)
## 调用模式（复制到任何技能中）

**MANDATORY: Resolve review mode before every gate spawn.** Never spawn a gate without checking. The resolved mode is determined once per skill run:
**强制：在每次生成门禁前解析评审模式。** 绝不在未检查的情况下生成门禁。模式在每个技能运行中解析一次：
1. If skill was called with `--review [mode]`, use that
1. 如果技能以 `--review [mode]` 调用，则使用该模式
2. Else read `production/review-mode.txt`
2. 否则读取 `production/review-mode.txt`
3. Else default to `lean`
3. 否则默认为 `lean`

Apply the resolved mode:
应用解析后的模式：
- `solo` → **skip all gates**. Note in output: `[GATE-ID] skipped — Solo mode`
- `solo` → **跳过所有门禁**。在输出中注明：`[GATE-ID] skipped — Solo mode`
- `lean` → **skip unless this is a PHASE-GATE** (CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE). Note: `[GATE-ID] skipped — Lean mode`
- `lean` → **除非是 PHASE-GATE 否则跳过**（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE）。注明：`[GATE-ID] skipped — Lean mode`
- `full` → spawn as normal
- `full` → 正常生成

```
# Apply mode check, then:
Spawn `[agent-name]` via Task:
- Gate: [GATE-ID] (see .claude/docs/director-gates.md)
- Context: [fields listed under that gate]
- Await the verdict before proceeding.
```

For parallel spawning (multiple directors at the same gate point):
对于并行生成（同一门禁点有多个总监）：

```
# Apply mode check for each gate first, then spawn all that survive:
Spawn all [N] agents simultaneously via Task — issue all Task calls before
waiting for any result. Collect all verdicts before proceeding.
```

---

## Standard Verdict Format
## 标准裁决格式

All gates return one of three verdicts. Skills must handle all three:
所有门禁返回三种裁决之一。技能必须处理全部三种情况：

| Verdict | Meaning | Default action |
| 裁决 | 含义 | 默认动作 |
|---------|---------|----------------|
| **APPROVE / READY** | No issues. Proceed. | Continue the workflow |
| **APPROVE / READY** | 无问题。继续。 | 继续工作流 |
| **CONCERNS [list]** | Issues present but not blocking. | Surface to user via `AskUserQuestion` — options: `Revise flagged items` / `Accept and proceed` / `Discuss further` |
| **CONCERNS [list]** | 存在问题但不阻塞。 | 通过 `AskUserQuestion` 呈现给用户 — 选项：`Revise flagged items` / `Accept and proceed` / `Discuss further` |
| **REJECT / NOT READY [blockers]** | Blocking issues. Do not proceed. | Surface blockers to user. Do not write files or advance stage until resolved. |
| **REJECT / NOT READY [blockers]** | 阻塞性问题。不得继续。 | 将阻塞问题呈现给用户。在解决前不得写入文件或推进阶段。 |

**Escalation rule**: When multiple directors are spawned in parallel, apply the
strictest verdict — one NOT READY overrides all READY verdicts.
**升级规则**：当多个总监并行生成时，应用最严格的裁决 — 一个 NOT READY 覆盖所有 READY 裁决。

---

## Recording Gate Outcomes
## 记录门禁结果

After a gate resolves, record the verdict in the relevant document's status header:
门禁解决后，在相关文档的状态标题中记录裁决：

```markdown
> **[Director] Review ([GATE-ID])**: APPROVED [date] / CONCERNS (accepted) [date] / REVISED [date]
```

For phase gates, record in `docs/architecture/architecture.md` or
`production/session-state/active.md` as appropriate.
对于阶段门禁，视情况记录在 `docs/architecture/architecture.md` 或
`production/session-state/active.md` 中。

---

## Tier 1 — Creative Director Gates
## 层级 1 — 创意总监门禁

Agent: `creative-director` | Model tier: Opus | Domain: Vision, pillars, player experience
智能体：`creative-director` | 模型层级：Opus | 领域：愿景、支柱、玩家体验

---

### CD-PILLARS — Pillar Stress Test
### CD-PILLARS — 支柱压力测试

**Trigger**: After game pillars and anti-pillars are defined (brainstorm Phase 4,
or any time pillars are revised)
**触发**：在游戏支柱和反支柱定义后（头脑风暴阶段 4，或任何修订支柱时）

**Context to pass**:
**传递的上下文**：
- Full pillar set with names, definitions, and design tests
- 完整支柱集，包含名称、定义和设计测试
- Anti-pillars list
- 反支柱列表
- Core fantasy statement
- 核心幻想陈述
- Unique hook ("Like X, AND ALSO Y")
- 独特卖点（"Like X, AND ALSO Y"）

**Prompt**:
**提示词**：
> "Review these game pillars. Are they falsifiable — could a real design decision
> actually fail this pillar? Do they create meaningful tension with each other? Do
> they differentiate this game from its closest comparables? Would they help resolve
> a design disagreement in practice, or are they too vague to be useful? Return
> specific feedback for each pillar and an overall verdict: APPROVE (strong), CONCERNS
> [list] (needs sharpening), or REJECT (weak — pillars do not carry weight)."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### CD-GDD-ALIGN — GDD Pillar Alignment Check
### CD-GDD-ALIGN — GDD 支柱对齐检查

**Trigger**: After a system GDD is authored (design-system, quick-design, or any
workflow that produces a GDD)
**触发**：在系统 GDD 编写后（design-system、quick-design 或任何产出 GDD 的工作流）

**Context to pass**:
**传递的上下文**：
- GDD file path
- GDD 文件路径
- Game pillars (from `design/gdd/game-concept.md` or `design/gdd/game-pillars.md`)
- 游戏支柱（来自 `design/gdd/game-concept.md` 或 `design/gdd/game-pillars.md`）
- MDA aesthetics target for this game
- 本游戏的 MDA 美学目标
- System's stated Player Fantasy section
- 系统声明的玩家幻想章节

**Prompt**:
**提示词**：
> "Review this system GDD for pillar alignment. Does every section serve the stated
> pillars? Are there mechanics or rules that contradict or weaken a pillar? Does
> the Player Fantasy section match the game's core fantasy? Return APPROVE, CONCERNS
> [specific sections with issues], or REJECT [pillar violations that must be
> redesigned before this system is implementable]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### CD-SYSTEMS — Systems Decomposition Vision Check
### CD-SYSTEMS — 系统分解愿景检查

**Trigger**: After the systems index is written by `/map-systems` — validates the
complete system set before GDD authoring begins
**触发**：在 `/map-systems` 写入系统索引后 — 在 GDD 编写开始前验证完整系统集

**Context to pass**:
**传递的上下文**：
- Systems index path (`design/gdd/systems-index.md`)
- 系统索引路径（`design/gdd/systems-index.md`）
- Game pillars and core fantasy (from `design/gdd/game-concept.md`)
- 游戏支柱和核心幻想（来自 `design/gdd/game-concept.md`）
- Priority tier assignments (MVP / Vertical Slice / Alpha / Full Vision)
- 优先级层级分配（MVP / 垂直切片 / Alpha / 完整愿景）
- Any high-risk or bottleneck systems identified in the dependency map
- 依赖图中识别的任何高风险或瓶颈系统

**Prompt**:
**提示词**：
> "Review this systems decomposition against the game's design pillars. Does the
> full set of MVP-tier systems collectively deliver the core fantasy? Are there
> systems whose mechanics don't serve any stated pillar — indicating they may be
> scope creep? Are there pillar-critical player experiences that have no system
> assigned to deliver them? Are any systems missing that the core loop requires?
> Return APPROVE (systems serve the vision), CONCERNS [specific gaps or
> misalignments with their pillar implications], or REJECT [fundamental gaps —
> the decomposition misses critical design intent and must be revised before GDD
> authoring begins]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### CD-NARRATIVE — Narrative Consistency Check
### CD-NARRATIVE — 叙事一致性检查

**Trigger**: After narrative GDDs, lore documents, dialogue specs, or world-building
documents are authored (team-narrative, design-system for story systems, writer
deliverables)
**触发**：在叙事 GDD、设定文档、对话规格或世界观构建文档编写后（team-narrative、用于故事系统的 design-system、writer 交付物）

**Context to pass**:
**传递的上下文**：
- Document file path(s)
- 文档文件路径
- Game pillars
- 游戏支柱
- Narrative direction brief or tone guide (if exists at `design/narrative/`)
- 叙事方向简报或基调指南（如存在于 `design/narrative/`）
- Any existing lore that the new document references
- 新文档引用的任何已有设定

**Prompt**:
**提示词**：
> "Review this narrative content for consistency with the game's pillars and
> established world rules. Does the tone match the game's established voice? Are
> there contradictions with existing lore or world-building? Does the content serve
> the player experience pillar? Return APPROVE, CONCERNS [specific inconsistencies],
> or REJECT [contradictions that break world coherence]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### CD-PLAYTEST — Player Experience Validation
### CD-PLAYTEST — 玩家体验验证

**Trigger**: After playtest reports are generated (`/playtest-report`), or after
any session that produces player feedback
**触发**：在试玩报告生成后（`/playtest-report`），或在任何产生玩家反馈的会话后

**Context to pass**:
**传递的上下文**：
- Playtest report file path
- 试玩报告文件路径
- Game pillars and core fantasy statement
- 游戏支柱和核心幻想陈述
- The specific hypothesis being tested
- 正在测试的具体假设

**Prompt**:
**提示词**：
> "Review this playtest report against the game's design pillars and core fantasy.
> Is the player experience matching the intended fantasy? Are there systematic issues
> that represent pillar drift — mechanics that feel fine in isolation but undermine
> the intended experience? Return APPROVE (core fantasy is landing), CONCERNS [gaps
> between intended and actual experience], or REJECT [core fantasy is not present —
> redesign needed before further playtesting]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### CD-PHASE-GATE — Creative Readiness at Phase Transition
### CD-PHASE-GATE — 阶段过渡时的创意就绪度

**Trigger**: Always at `/gate-check` — spawn in parallel with TD-PHASE-GATE and PR-PHASE-GATE
**触发**：总是在 `/gate-check` 时 — 与 TD-PHASE-GATE 和 PR-PHASE-GATE 并行生成

**Context to pass**:
**传递的上下文**：
- Target phase name
- 目标阶段名称
- List of all artifacts present (file paths)
- 所有存在产物的列表（文件路径）
- Game pillars and core fantasy
- 游戏支柱和核心幻想

**Prompt**:
**提示词**：
> "Review the current project state for [target phase] gate readiness from a
> creative direction perspective. Are the game pillars faithfully represented in
> all design artifacts? Does the current state preserve the core fantasy? Are there
> any design decisions across GDDs or architecture that compromise the intended
> player experience? Return READY, CONCERNS [list], or NOT READY [blockers]."

**Verdicts**: READY / CONCERNS / NOT READY
**裁决**：READY / CONCERNS / NOT READY

---

## Tier 1 — Technical Director Gates
## 层级 1 — 技术总监门禁

Agent: `technical-director` | Model tier: Opus | Domain: Architecture, engine risk, performance
智能体：`technical-director` | 模型层级：Opus | 领域：架构、引擎风险、性能

---

### TD-SYSTEM-BOUNDARY — System Boundary Architecture Review
### TD-SYSTEM-BOUNDARY — 系统边界架构评审

**Trigger**: After `/map-systems` Phase 3 dependency mapping is agreed but before
GDD authoring begins — validates that the system structure is architecturally
sound before teams invest in writing GDDs against it
**触发**：在 `/map-systems` 阶段 3 依赖映射达成一致后、GDD 编写开始前 — 验证系统结构在架构上健全，确保团队在基于其编写 GDD 之前的投入有效

**Context to pass**:
**传递的上下文**：
- Systems index path (or the dependency map summary if index not yet written)
- 系统索引路径（若索引尚未编写则用依赖图摘要）
- Layer assignments (Foundation / Core / Feature / Presentation / Polish)
- 层级分配（Foundation / Core / Feature / Presentation / Polish）
- The full dependency graph (what each system depends on)
- 完整依赖图（每个系统依赖什么）
- Any bottleneck systems flagged (many dependents)
- 标记的任何瓶颈系统（许多依赖者）
- Any circular dependencies found and their proposed resolutions
- 发现的任何循环依赖及其建议解决方案

**Prompt**:
**提示词**：
> "Review this systems decomposition from an architectural perspective before GDD
> authoring begins. Are the system boundaries clean — does each system own a
> distinct concern with minimal overlap? Are there God Object risks (systems doing
> too much)? Does the dependency ordering create implementation-sequencing problems?
> Are there implicit shared-state problems in the proposed boundaries that will
> cause tight coupling when implemented? Are any Foundation-layer systems actually
> dependent on Feature-layer systems (inverted dependency)? Return APPROVE
> (boundaries are architecturally sound — proceed to GDD authoring), CONCERNS
> [specific boundary issues to address in the GDDs themselves], or REJECT
> [fundamental boundary problems — the system structure will cause architectural
> issues and must be restructured before any GDD is written]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### TD-FEASIBILITY — Technical Feasibility Assessment
### TD-FEASIBILITY — 技术可行性评估

**Trigger**: After biggest technical risks are identified during scope/feasibility
(brainstorm Phase 6, quick-design, or any early-stage concept with technical unknowns)
**触发**：在范围/可行性阶段识别出最大技术风险后（头脑风暴阶段 6、quick-design 或任何带技术未知数的早期概念）

**Context to pass**:
**传递的上下文**：
- Concept's core loop description
- 概念的核心循环描述
- Platform target
- 目标平台
- Engine choice (or "undecided")
- 引擎选择（或 "undecided"）
- List of identified technical risks
- 已识别的技术风险列表

**Prompt**:
**提示词**：
> "Review these technical risks for a [genre] game targeting [platform] using
> [engine or 'undecided engine']. Flag any HIGH risk items that could invalidate
> the concept as described, any risks that are engine-specific and should influence
> the engine choice, and any risks that are commonly underestimated by solo
> developers. Return VIABLE (risks are manageable), CONCERNS [list with mitigation
> suggestions], or HIGH RISK [blockers that require concept or scope revision]."

**Verdicts**: VIABLE / CONCERNS / HIGH RISK
**裁决**：VIABLE / CONCERNS / HIGH RISK

---

### TD-ARCHITECTURE — Architecture Sign-Off
### TD-ARCHITECTURE — 架构签字确认

**Trigger**: After the master architecture document is drafted (`/create-architecture`
Phase 7), and after any major architecture revision
**触发**：在主架构文档起草后（`/create-architecture` 阶段 7），以及任何重大架构修订后

**Context to pass**:
**传递的上下文**：
- Architecture document path (`docs/architecture/architecture.md`)
- 架构文档路径（`docs/architecture/architecture.md`）
- Technical requirements baseline (TR-IDs and count)
- 技术需求基线（TR-ID 和数量）
- ADR list with statuses
- ADR 列表及状态
- Engine knowledge gap inventory
- 引擎知识缺口清单

**Prompt**:
**提示词**：
> "Review this master architecture document for technical soundness. Check: (1) Is
> every technical requirement from the baseline covered by an architectural decision?
> (2) Are all HIGH risk engine domains explicitly addressed or flagged as open
> questions? (3) Are the API boundaries clean, minimal, and implementable? (4) Are
> Foundation layer ADR gaps resolved before implementation begins? Return APPROVE,
> CONCERNS [list], or REJECT [blockers that must be resolved before coding starts]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### TD-ADR — Architecture Decision Review
### TD-ADR — 架构决策评审

**Trigger**: After an individual ADR is authored (`/architecture-decision`), before
it is marked Accepted
**触发**：在单个 ADR 编写后（`/architecture-decision`）、标记为 Accepted 之前

**Context to pass**:
**传递的上下文**：
- ADR file path
- ADR 文件路径
- Engine version and knowledge gap risk level for the domain
- 引擎版本及该领域的知识缺口风险等级
- Related ADRs (if any)
- 相关 ADR（如有）

**Prompt**:
**提示词**：
> "Review this Architecture Decision Record. Does it have a clear problem statement
> and rationale? Are the rejected alternatives genuinely considered? Does the
> Consequences section acknowledge the trade-offs honestly? Is the engine version
> stamped? Are post-cutoff API risks flagged? Does it link to the GDD requirements
> it covers? Return APPROVE, CONCERNS [specific gaps], or REJECT [the decision is
> underspecified or makes unsound technical assumptions]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### TD-ENGINE-RISK — Engine Version Risk Review
### TD-ENGINE-RISK — 引擎版本风险评审

**Trigger**: When making architecture decisions that touch post-cutoff engine APIs,
or before finalizing any engine-specific implementation approach
**触发**：当做出涉及训练截止后引擎 API 的架构决策时，或在敲定任何引擎特定实现方案之前

**Context to pass**:
**传递的上下文**：
- The specific API or feature being used
- 使用的具体 API 或功能
- Engine version and LLM knowledge cutoff (from `docs/engine-reference/[engine]/VERSION.md`)
- 引擎版本和 LLM 知识截止日期（来自 `docs/engine-reference/[engine]/VERSION.md`）
- Relevant excerpt from breaking-changes or deprecated-apis docs
- 来自 breaking-changes 或 deprecated-apis 文档的相关摘录

**Prompt**:
**提示词**：
> "Review this engine API usage against the version reference. Is this API present
> in [engine version]? Has its signature, behaviour, or namespace changed since the
> LLM knowledge cutoff? Are there known deprecations or post-cutoff alternatives?
> Return APPROVE (safe to use as described), CONCERNS [verify before implementing],
> or REJECT [API has changed — provide corrected approach]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### TD-PHASE-GATE — Technical Readiness at Phase Transition
### TD-PHASE-GATE — 阶段过渡时的技术就绪度

**Trigger**: Always at `/gate-check` — spawn in parallel with CD-PHASE-GATE and PR-PHASE-GATE
**触发**：总是在 `/gate-check` 时 — 与 CD-PHASE-GATE 和 PR-PHASE-GATE 并行生成

**Context to pass**:
**传递的上下文**：
- Target phase name
- 目标阶段名称
- Architecture document path (if exists)
- 架构文档路径（如存在）
- Engine reference path
- 引擎参考路径
- ADR list
- ADR 列表

**Prompt**:
**提示词**：
> "Review the current project state for [target phase] gate readiness from a
> technical direction perspective. Is the architecture sound for this phase? Are
> all high-risk engine domains addressed? Are performance budgets realistic and
> documented? Are Foundation-layer decisions complete enough to begin implementation?
> Return READY, CONCERNS [list], or NOT READY [blockers]."

**Verdicts**: READY / CONCERNS / NOT READY
**裁决**：READY / CONCERNS / NOT READY

---

## Tier 1 — Producer Gates
## 层级 1 — 制作人门禁

Agent: `producer` | Model tier: Opus | Domain: Scope, timeline, dependencies, production risk
智能体：`producer` | 模型层级：Opus | 领域：范围、时间线、依赖、生产风险

---

### PR-SCOPE — Scope and Timeline Validation
### PR-SCOPE — 范围与时间线验证

**Trigger**: After scope tiers are defined (brainstorm Phase 6, quick-design, or
any workflow that produces an MVP definition and timeline estimate)
**触发**：在范围层级定义后（头脑风暴阶段 6、quick-design 或任何产出 MVP 定义和时间线估算的工作流）

**Context to pass**:
**传递的上下文**：
- Full vision scope description
- 完整愿景范围描述
- MVP definition
- MVP 定义
- Timeline estimate
- 时间线估算
- Team size (solo / small team / etc.)
- 团队规模（独立 / 小团队 / 等）
- Scope tiers (what ships if time runs out)
- 范围层级（时间不够时发布什么）

**Prompt**:
**提示词**：
> "Review this scope estimate. Is the MVP achievable in the stated timeline for
> the stated team size? Are the scope tiers correctly ordered by risk — does each
> tier deliver a shippable product if work stops there? What is the most likely
> cut point under time pressure, and is it a graceful fallback or a broken product?
> Return REALISTIC (scope matches capacity), OPTIMISTIC [specific adjustments
> recommended], or UNREALISTIC [blockers — timeline or MVP must be revised]."

**Verdicts**: REALISTIC / OPTIMISTIC / UNREALISTIC
**裁决**：REALISTIC / OPTIMISTIC / UNREALISTIC

---

### PR-SPRINT — Sprint Feasibility Review
### PR-SPRINT — 冲刺可行性评审

**Trigger**: Before finalising a sprint plan (`/sprint-plan`), and after any
mid-sprint scope change
**触发**：在敲定冲刺计划前（`/sprint-plan`），以及任何冲刺中途范围变更后

**Context to pass**:
**传递的上下文**：
- Proposed sprint story list (titles, estimates, dependencies)
- 拟议冲刺 story 列表（标题、估算、依赖）
- Team capacity (hours available)
- 团队容量（可用小时数）
- Current sprint backlog debt (if any)
- 当前冲刺积压债务（如有）
- Milestone constraints
- 里程碑约束

**Prompt**:
**提示词**：
> "Review this sprint plan for feasibility. Is the story load realistic for the
> available capacity? Are stories correctly ordered by dependency? Are there hidden
> dependencies between stories that could block the sprint mid-way? Are any stories
> underestimated given their technical complexity? Return REALISTIC (plan is
> achievable), CONCERNS [specific risks], or UNREALISTIC [sprint must be
> descoped — identify which stories to defer]."

**Verdicts**: REALISTIC / CONCERNS / UNREALISTIC
**裁决**：REALISTIC / CONCERNS / UNREALISTIC

---

### PR-MILESTONE — Milestone Risk Assessment
### PR-MILESTONE — 里程碑风险评估

**Trigger**: At milestone review (`/milestone-review`), at mid-sprint retrospectives,
or when a scope change is proposed that affects the milestone
**触发**：在里程碑评审时（`/milestone-review`）、冲刺中期回顾时，或当提出影响里程碑的范围变更时

**Context to pass**:
**传递的上下文**：
- Milestone definition and target date
- 里程碑定义和目标日期
- Current completion percentage
- 当前进度百分比
- Blocked stories count
- 阻塞 story 数量
- Sprint velocity data (if available)
- 冲刺速度数据（如可用）

**Prompt**:
**提示词**：
> "Review this milestone status. Based on current velocity and blocked story count,
> will this milestone hit its target date? What are the top 3 production risks
> between now and the milestone? Are there scope items that should be cut to protect
> the milestone date vs. items that are non-negotiable? Return ON TRACK, AT RISK
> [specific mitigations], or OFF TRACK [date must slip or scope must cut — provide
> both options]."

**Verdicts**: ON TRACK / AT RISK / OFF TRACK
**裁决**：ON TRACK / AT RISK / OFF TRACK

---

### PR-EPIC — Epic Structure Feasibility Review
### PR-EPIC — Epic 结构可行性评审

**Trigger**: After epics are defined by `/create-epics`, before stories are
broken out — validates the epic structure is producible before `/create-stories`
is invoked
**触发**：在 `/create-stories` 调用前，epic 由 `/create-epics` 定义后、story 拆分前 — 验证 epic 结构可生产

**Context to pass**:
**传递的上下文**：
- Epic definition file paths (all epics just created)
- Epic 定义文件路径（所有刚创建的 epic）
- Epic index path (`production/epics/index.md`)
- Epic 索引路径（`production/epics/index.md`）
- Milestone timeline and target dates
- 里程碑时间线和目标日期
- Team capacity (solo / small team / size)
- 团队容量（独立 / 小团队 / 规模）
- Layer being epiced (Foundation / Core / Feature / etc.)
- 正在做 epic 的层级（Foundation / Core / Feature / 等）

**Prompt**:
**提示词**：
> "Review this epic structure for production feasibility before story breakdown
> begins. Are the epic boundaries scoped appropriately — could each epic realistically
> complete before a milestone deadline? Are epics correctly ordered by system
> dependency — does any epic require another epic's output before it can start?
> Are any epics underscoped (too small, should merge) or overscoped (too large,
> should split into 2-3 focused epics)? Are the Foundation-layer epics scoped to
> allow Core-layer epics to begin at the start of the next sprint after Foundation
> completes? Return REALISTIC (epic structure is producible), CONCERNS [specific
> structural adjustments before stories are written], or UNREALISTIC [epics must
> be split, merged, or reordered — story breakdown cannot begin until resolved]."

**Verdicts**: REALISTIC / CONCERNS / UNREALISTIC
**裁决**：REALISTIC / CONCERNS / UNREALISTIC

---

### PR-PHASE-GATE — Production Readiness at Phase Transition
### PR-PHASE-GATE — 阶段过渡时的生产就绪度

**Trigger**: Always at `/gate-check` — spawn in parallel with CD-PHASE-GATE and TD-PHASE-GATE
**触发**：总是在 `/gate-check` 时 — 与 CD-PHASE-GATE 和 TD-PHASE-GATE 并行生成

**Context to pass**:
**传递的上下文**：
- Target phase name
- 目标阶段名称
- Sprint and milestone artifacts present
- 存在的冲刺和里程碑产物
- Team size and capacity
- 团队规模和容量
- Current blocked story count
- 当前阻塞 story 数量

**Prompt**:
**提示词**：
> "Review the current project state for [target phase] gate readiness from a
> production perspective. Is the scope realistic for the stated timeline and team
> size? Are dependencies properly ordered so the team can actually execute in
> sequence? Are there milestone or sprint risks that could derail the phase within
> the first two sprints? Return READY, CONCERNS [list], or NOT READY [blockers]."

**Verdicts**: READY / CONCERNS / NOT READY
**裁决**：READY / CONCERNS / NOT READY

---

## Tier 1 — Art Director Gates
## 层级 1 — 美术总监门禁

Agent: `art-director` | Model tier: Sonnet | Domain: Visual identity, art bible, visual production readiness
智能体：`art-director` | 模型层级：Sonnet | 领域：视觉身份、美术圣经、视觉生产就绪度

---

### AD-CONCEPT-VISUAL — Visual Identity Anchor
### AD-CONCEPT-VISUAL — 视觉身份锚点

**Trigger**: After game pillars are locked (brainstorm Phase 4), in parallel with CD-PILLARS
**触发**：在游戏支柱锁定后（头脑风暴阶段 4），与 CD-PILLARS 并行

**Context to pass**:
**传递的上下文**：
- Game concept (elevator pitch, core fantasy, unique hook)
- 游戏概念（电梯演讲、核心幻想、独特卖点）
- Full pillar set with names, definitions, and design tests
- 完整支柱集，含名称、定义和设计测试
- Target platform (if known)
- 目标平台（如已知）
- Any reference games or visual touchstones mentioned by the user
- 用户提到的任何参考游戏或视觉基准

**Prompt**:
**提示词**：
> "Based on these game pillars and core concept, propose 2-3 distinct visual identity
> directions. For each direction provide: (1) a one-line visual rule that could guide
> all visual decisions (e.g., 'everything must move', 'beauty is in the decay'), (2)
> mood and atmosphere targets, (3) shape language (sharp/rounded/organic/geometric
> emphasis), (4) color philosophy (palette direction, what colors mean in this world).
> Be specific — avoid generic descriptions. One direction should directly serve the
> primary design pillar. Name each direction. Recommend which best serves the stated
> pillars and explain why."

**Verdicts**: CONCEPTS (multiple valid options — user selects) / STRONG (one direction clearly dominant) / CONCERNS (pillars don't provide enough direction to differentiate visual identity yet)
**裁决**：CONCEPTS（多个有效选项 — 用户选择） / STRONG（一个方向明显占主导） / CONCERNS（支柱未提供足够方向以区分视觉身份）

---

### AD-ART-BIBLE — Art Bible Sign-Off
### AD-ART-BIBLE — 美术圣经签字确认

**Trigger**: After the art bible is drafted (`/art-bible`), before asset production begins
**触发**：在美术圣经起草后（`/art-bible`）、资源生产开始前

**Context to pass**:
**传递的上下文**：
- Art bible path (`design/art/art-bible.md`)
- 美术圣经路径（`design/art/art-bible.md`）
- Game pillars and core fantasy
- 游戏支柱和核心幻想
- Platform and performance constraints (from `.claude/docs/technical-preferences.md` if configured)
- 平台和性能约束（如已配置，来自 `.claude/docs/technical-preferences.md`）
- Visual identity anchor chosen during brainstorm (from `design/gdd/game-concept.md`)
- 头脑风暴期间选择的视觉身份锚点（来自 `design/gdd/game-concept.md`）

**Prompt**:
**提示词**：
> "Review this art bible for completeness and internal consistency. Does the color
> system match the mood targets? Does the shape language follow from the visual
> identity statement? Are the asset standards achievable within the platform
> constraints? Does the character design direction give artists enough to work from
> without over-specifying? Are there contradictions between sections? Would an
> outsourcing team be able to produce assets from this document without additional
> briefing? Return APPROVE (art bible is production-ready), CONCERNS [specific
> sections needing clarification], or REJECT [fundamental inconsistencies that must
> be resolved before asset production begins]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### AD-PHASE-GATE — Visual Readiness at Phase Transition
### AD-PHASE-GATE — 阶段过渡时的视觉就绪度

**Trigger**: Always at `/gate-check` — spawn in parallel with CD-PHASE-GATE, TD-PHASE-GATE, and PR-PHASE-GATE
**触发**：总是在 `/gate-check` 时 — 与 CD-PHASE-GATE、TD-PHASE-GATE 和 PR-PHASE-GATE 并行生成

**Context to pass**:
**传递的上下文**：
- Target phase name
- 目标阶段名称
- List of all art/visual artifacts present (file paths)
- 所有美术/视觉产物列表（文件路径）
- Visual identity anchor from `design/gdd/game-concept.md` (if present)
- 来自 `design/gdd/game-concept.md` 的视觉身份锚点（如存在）
- Art bible path if it exists (`design/art/art-bible.md`)
- 美术圣经路径（如存在，`design/art/art-bible.md`）

**Prompt**:
**提示词**：
> "Review the current project state for [target phase] gate readiness from a visual
> direction perspective. Is the visual identity established and documented at the
> level this phase requires? Are the right visual artifacts in place? Would visual
> teams be able to begin their work without visual direction gaps that cause costly
> rework later? Are there visual decisions that are being deferred past their latest
> responsible moment? Return READY, CONCERNS [specific visual direction gaps that
> could cause production rework], or NOT READY [visual blockers that must exist
> before this phase can succeed — specify what artifact is missing and why it
> matters at this stage]."

**Verdicts**: READY / CONCERNS / NOT READY
**裁决**：READY / CONCERNS / NOT READY

---

## Tier 2 — Lead Gates
## 层级 2 — 主管门禁

These gates are invoked by orchestration skills and senior skills when a domain
specialist's feasibility sign-off is needed. Tier 2 leads use Sonnet (default).
这些门禁由编排技能和高级技能在需要领域专家可行性签字时调用。层级 2 主管使用 Sonnet（默认）。

---

### LP-FEASIBILITY — Lead Programmer Implementation Feasibility
### LP-FEASIBILITY — 主程序员实现可行性

**Trigger**: After the master architecture document is written (`/create-architecture`
Phase 7b), or when a new architectural pattern is proposed
**触发**：在主架构文档写入后（`/create-architecture` 阶段 7b），或提出新架构模式时

**Context to pass**:
**传递的上下文**：
- Architecture document path
- 架构文档路径
- Technical requirements baseline summary
- 技术需求基线摘要
- ADR list with statuses
- ADR 列表及状态

**Prompt**:
**提示词**：
> "Review this architecture for implementation feasibility. Flag: (a) any decisions
> that would be difficult or impossible to implement with the stated engine and
> language, (b) any missing interface definitions that programmers would need to
> invent themselves, (c) any patterns that create avoidable technical debt or
> that contradict standard [engine] idioms. Return FEASIBLE, CONCERNS [list], or
> INFEASIBLE [blockers that make this architecture unimplementable as written]."

**Verdicts**: FEASIBLE / CONCERNS / INFEASIBLE
**裁决**：FEASIBLE / CONCERNS / INFEASIBLE

---

### LP-CODE-REVIEW — Lead Programmer Code Review
### LP-CODE-REVIEW — 主程序员代码评审

**Trigger**: After a dev story is implemented (`/dev-story`, `/story-done`), or
as part of `/code-review`
**触发**：在 dev story 实现后（`/dev-story`、`/story-done`），或作为 `/code-review` 的一部分

**Context to pass**:
**传递的上下文**：
- Implementation file paths
- 实现文件路径
- Story file path (for acceptance criteria)
- story 文件路径（用于验收标准）
- Relevant GDD section
- 相关 GDD 章节
- ADR that governs this system
- 治理此系统的 ADR

**Prompt**:
**提示词**：
> "Review this implementation against the story acceptance criteria and governing
> ADR. Does the code match the architecture boundary definitions? Are there
> violations of the coding standards or forbidden patterns? Is the public API
> testable and documented? Are there any correctness issues against the GDD rules?
> Return APPROVE, CONCERNS [specific issues], or REJECT [must be revised before merge]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### QL-STORY-READY — QA Lead Story Readiness Check
### QL-STORY-READY — QA 主管 story 就绪度检查

**Trigger**: Before a story is accepted into a sprint — invoked by `/create-stories`,
`/story-readiness`, and `/sprint-plan` during story selection
**触发**：在 story 被纳入冲刺前 — 由 `/create-stories`、
`/story-readiness` 和 `/sprint-plan` 在 story 选择时调用

**Context to pass**:
**传递的上下文**：
- Story file path
- story 文件路径
- Story type (Logic / Integration / Visual/Feel / UI / Config/Data)
- story 类型（Logic / Integration / Visual/Feel / UI / Config/Data）
- Acceptance criteria list (verbatim from the story)
- 验收标准列表（原样来自 story）
- The GDD requirement (TR-ID and text) the story covers
- story 覆盖的 GDD 需求（TR-ID 和文本）

**Prompt**:
**提示词**：
> "Review this story's acceptance criteria for testability before it enters the
> sprint. Are all criteria specific enough that a developer would know unambiguously
> when they are done? For Logic-type stories: can every criterion be verified with
> an automated test? For Integration stories: is each criterion observable in a
> controlled test environment? Flag criteria that are too vague to implement
> against, and flag criteria that require a full game build to test (mark these
> DEFERRED, not BLOCKED). Return ADEQUATE (criteria are implementable as written),
> GAPS [specific criteria needing refinement], or INADEQUATE [criteria are too
> vague — story must be revised before sprint inclusion]."

**Verdicts**: ADEQUATE / GAPS / INADEQUATE
**裁决**：ADEQUATE / GAPS / INADEQUATE

---

### QL-TEST-COVERAGE — QA Lead Test Coverage Review
### QL-TEST-COVERAGE — QA 主管测试覆盖率评审

**Trigger**: After implementation stories are complete, before marking an epic
done, or at `/gate-check` Production → Polish
**触发**：在实现 story 完成后、标记 epic 完成前，或在 `/gate-check` Production → Polish 时

**Context to pass**:
**传递的上下文**：
- List of implemented stories with story types (Logic / Integration / Visual / UI / Config)
- 已实现 story 列表及 story 类型（Logic / Integration / Visual / UI / Config）
- Test file paths in `tests/`
- `tests/` 中的测试文件路径
- GDD acceptance criteria for the system
- 系统的 GDD 验收标准

**Prompt**:
**提示词**：
> "Review the test coverage for these implementation stories. Are all Logic stories
> covered by passing unit tests? Are Integration stories covered by integration
> tests or documented playtests? Are the GDD acceptance criteria each mapped to at
> least one test? Are there untested edge cases from the GDD Edge Cases section?
> Return ADEQUATE (coverage meets standards), GAPS [specific missing tests], or
> INADEQUATE [critical logic is untested — do not advance]."

**Verdicts**: ADEQUATE / GAPS / INADEQUATE
**裁决**：ADEQUATE / GAPS / INADEQUATE

---

### ND-CONSISTENCY — Narrative Director Consistency Check
### ND-CONSISTENCY — 叙事总监一致性检查

**Trigger**: After writer deliverables (dialogue, lore, item descriptions) are
authored, or when a design decision has narrative implications
**触发**：在 writer 交付物（对话、设定、物品描述）编写后，或当设计决策有叙事含义时

**Context to pass**:
**传递的上下文**：
- Document or content file path(s)
- 文档或内容文件路径
- Narrative bible or tone guide path (if exists)
- 叙事圣经或基调指南路径（如存在）
- Relevant world-building rules
- 相关世界观规则
- Character or faction profiles affected
- 受影响的角色或阵营档案

**Prompt**:
**提示词**：
> "Review this narrative content for internal consistency and adherence to
> established world rules. Are character voices consistent with their established
> profiles? Does the lore contradict any established facts? Is the tone consistent
> with the game's narrative direction? Return APPROVE, CONCERNS [specific
> inconsistencies to fix], or REJECT [contradictions that break the narrative
> foundation]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

### AD-VISUAL — Art Director Visual Consistency Review
### AD-VISUAL — 美术总监视觉一致性评审

**Trigger**: After art direction decisions are made, when new asset types are
introduced, or when a tech art decision affects visual style
**触发**：在做出美术方向决策后、引入新资源类型时，或当技术美术决策影响视觉风格时

**Context to pass**:
**传递的上下文**：
- Art bible path (if exists at `design/art/art-bible.md`)
- 美术圣经路径（如存在于 `design/art/art-bible.md`）
- The specific asset type, style decision, or visual direction being reviewed
- 正在评审的具体资源类型、风格决策或视觉方向
- Reference images or style descriptions
- 参考图像或风格描述
- Platform and performance constraints
- 平台和性能约束

**Prompt**:
**提示词**：
> "Review this visual direction decision for consistency with the established art
> style and production constraints. Does it match the art bible? Is it achievable
> within the platform's performance budget? Are there asset pipeline implications
> that create technical risk? Return APPROVE, CONCERNS [specific adjustments], or
> REJECT [style violation or production risk that must be resolved first]."

**Verdicts**: APPROVE / CONCERNS / REJECT
**裁决**：APPROVE / CONCERNS / REJECT

---

## Parallel Gate Protocol
## 并行门禁协议

When a workflow requires multiple directors at the same checkpoint (most common
at `/gate-check`), spawn all agents simultaneously:
当工作流在同一检查点需要多个总监时（最常见于
`/gate-check`），同时生成所有智能体：

```
Spawn in parallel (issue all Task calls before waiting for any result):
1. creative-director  → gate CD-PHASE-GATE
2. technical-director → gate TD-PHASE-GATE
3. producer           → gate PR-PHASE-GATE
4. art-director       → gate AD-PHASE-GATE

Collect all four verdicts, then apply escalation rules:
- Any NOT READY / REJECT → overall verdict minimum FAIL
- Any CONCERNS → overall verdict minimum CONCERNS
- All READY / APPROVE → eligible for PASS (still subject to artifact checks)
```

---

## Adding New Gates
## 添加新门禁

When a new gate is needed for a new skill or workflow:
当新技能或工作流需要新门禁时：

1. Assign a gate ID: `[DIRECTOR-PREFIX]-[DESCRIPTIVE-SLUG]`
1. 分配门禁 ID：`[DIRECTOR-PREFIX]-[DESCRIPTIVE-SLUG]`
   - Prefixes: `CD-` `TD-` `PR-` `LP-` `QL-` `ND-` `AD-`
   - 前缀：`CD-` `TD-` `PR-` `LP-` `QL-` `ND-` `AD-`
   - Add new prefixes for new agents: `audio-director` → `AU-`, `ux-designer` → `UX-`
   - 为新智能体添加新前缀：`audio-director` → `AU-`、`ux-designer` → `UX-`
2. Add the gate under the appropriate director section with all five fields:
   Trigger, Context to pass, Prompt, Verdicts, and any special handling notes
2. 在相应总监章节下添加门禁，包含全部五个字段：
   Trigger、Context to pass、Prompt、Verdicts 以及任何特殊处理说明
3. Reference it in skills by ID only — never copy the prompt text into the skill
3. 在技能中仅通过 ID 引用 — 绝不将提示词文本复制到技能中

---

## Gate Coverage by Stage
## 按阶段的门禁覆盖

| Stage | Required Gates | Optional Gates |
| 阶段 | 必需门禁 | 可选门禁 |
|-------|---------------|----------------|
| **Concept** | CD-PILLARS, AD-CONCEPT-VISUAL | TD-FEASIBILITY, PR-SCOPE |
| **概念** | CD-PILLARS, AD-CONCEPT-VISUAL | TD-FEASIBILITY, PR-SCOPE |
| **Systems Design** | TD-SYSTEM-BOUNDARY, CD-SYSTEMS, PR-SCOPE, CD-GDD-ALIGN (per GDD) | ND-CONSISTENCY, AD-VISUAL |
| **系统设计** | TD-SYSTEM-BOUNDARY, CD-SYSTEMS, PR-SCOPE, CD-GDD-ALIGN（每个 GDD） | ND-CONSISTENCY, AD-VISUAL |
| **Technical Setup** | TD-ARCHITECTURE, TD-ADR (per ADR), LP-FEASIBILITY, AD-ART-BIBLE | TD-ENGINE-RISK |
| **技术搭建** | TD-ARCHITECTURE, TD-ADR（每个 ADR）, LP-FEASIBILITY, AD-ART-BIBLE | TD-ENGINE-RISK |
| **Pre-Production** | PR-EPIC, QL-STORY-READY (per story), PR-SPRINT, all four PHASE-GATEs (via gate-check) | CD-PLAYTEST |
| **前期制作** | PR-EPIC, QL-STORY-READY（每个 story）, PR-SPRINT, 全部四个 PHASE-GATE（通过 gate-check） | CD-PLAYTEST |
| **Production** | LP-CODE-REVIEW (per story), QL-STORY-READY, PR-SPRINT (per sprint), QL-TEST-COVERAGE (per sprint close-out) | PR-MILESTONE, AD-VISUAL |
| **制作** | LP-CODE-REVIEW（每个 story）, QL-STORY-READY, PR-SPRINT（每个冲刺）, QL-TEST-COVERAGE（每次冲刺收尾） | PR-MILESTONE, AD-VISUAL |
| **Polish** | QL-TEST-COVERAGE, CD-PLAYTEST, PR-MILESTONE | AD-VISUAL |
| **打磨** | QL-TEST-COVERAGE, CD-PLAYTEST, PR-MILESTONE | AD-VISUAL |
| **Release** | All four PHASE-GATEs (via gate-check) | QL-TEST-COVERAGE |
| **发布** | 全部四个 PHASE-GATE（通过 gate-check） | QL-TEST-COVERAGE |
