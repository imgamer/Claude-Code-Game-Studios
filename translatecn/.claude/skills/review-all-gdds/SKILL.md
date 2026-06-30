---
name: review-all-gdds
description: "Holistic cross-GDD consistency and game design review. Reads all system GDDs simultaneously and checks for contradictions between them, stale references, ownership conflicts, formula incompatibilities, and game design theory violations (dominant strategies, economic imbalance, cognitive overload, pillar drift). Run after all MVP GDDs are written, before architecture begins."
argument-hint: "[focus: full | consistency | design-theory | since-last-review]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, AskUserQuestion, Task
model: opus
---
---
名称：review-all-gdds
描述："跨 GDD 整体一致性与游戏设计评审。同时读取所有系统 GDD，检查它们之间的矛盾、过期引用、归属冲突、公式不兼容，以及游戏设计理论违规（主导策略、经济失衡、认知过载、支柱漂移）。在所有 MVP GDD 编写完成后、架构开始之前运行。"
argument-hint："[focus: full | consistency | design-theory | since-last-review]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Bash, AskUserQuestion, Task
model：opus
---

# Review All GDDs
# 审查所有 GDD

This skill reads every system GDD simultaneously and performs two complementary
reviews that cannot be done per-GDD in isolation:
本技能同时读取每个系统 GDD，并执行两项无法在单个 GDD 中独立完成的互补
评审：

1. **Cross-GDD Consistency** — contradictions, stale references, and ownership
   conflicts between documents
1. **跨 GDD 一致性** —— 文档之间的矛盾、过期引用和归属冲突
2. **Game Design Holism** — issues that only emerge when you see all systems
   together: dominant strategies, broken economies, cognitive overload, pillar
   drift, competing progression loops
2. **游戏设计整体性** —— 只有当所有系统放在一起看时才会暴露的问题：主导策略、
   失衡经济、认知过载、支柱漂移、互相竞争的进度循环

**This is distinct from `/design-review`**, which reviews one GDD for internal
completeness. This skill reviews the *relationships* between all GDDs.
**这与 `/design-review` 不同**，后者审查单个 GDD 的内部完整性。本技能审查
所有 GDD 之间的*关系*。

**When to run:**
- After all MVP-tier GDDs are individually approved
- After any GDD is significantly revised mid-production
- Before `/create-architecture` begins (architecture built on inconsistent GDDs
  inherits those inconsistencies)
**何时运行：**
- 在所有 MVP 层级 GDD 单独获批之后
- 在制作中期任何 GDD 经过重大修订之后
- 在 `/create-architecture` 开始之前（基于不一致 GDD 构建的架构会继承这些不一致）

**Argument modes:**
**参数模式：**

**Focus:** `$ARGUMENTS[0]` (blank = `full`)
**焦点：** `$ARGUMENTS[0]`（留空 = `full`）

- **No argument / `full`**: Both consistency and design theory passes
- **`consistency`**: Cross-GDD consistency checks only (faster)
- **`design-theory`**: Game design holism checks only
- **`since-last-review`**: Only GDDs modified since the last review report (git-based)
- **无参数 / `full`**：同时进行一致性与设计理论两轮检查
- **`consistency`**：仅进行跨 GDD 一致性检查（更快）
- **`design-theory`**：仅进行游戏设计整体性检查
- **`since-last-review`**：仅检查自上次评审报告以来被修改过的 GDD（基于 git）

---

## Phase 1: Load Everything
## 阶段 1：加载一切

### Phase 1a — L0: Summary Scan (fast, low tokens)
### 阶段 1a —— L0：摘要扫描（快速、低 token）

Before reading any full document, use Grep to extract `## Summary` sections
from all GDD files:
在读取任何完整文档之前，使用 Grep 从所有 GDD 文件中提取 `## Summary` 段落：

```
Grep pattern="## Summary" glob="design/gdd/*.md" output_mode="content" -A 5
```

Display a manifest to the user:
向用户展示清单：
```
Found [N] GDDs. Summaries:
  • combat.md — [summary text]
  • inventory.md — [summary text]
  ...
```

For `since-last-review` mode: run `git log --name-only` to identify GDDs
modified since the last review report file was written. Show the user which
GDDs are in scope based on summaries before doing any full reads. Only
proceed to L1 for those GDDs plus any GDDs listed in their "Key deps".
对于 `since-last-review` 模式：运行 `git log --name-only` 以识别自上次评审报告
文件写入以来被修改过的 GDD。在进行任何完整读取之前，基于摘要向用户展示哪些
GDD 在范围内。仅对这些 GDD 以及它们 "Key deps" 中列出的任何 GDD 继续执行 L1。

### Phase 1b — Registry Pre-Load (fast baseline)
### 阶段 1b —— 注册表预加载（快速基线）

Before full-reading any GDD, check for the entity registry:
在完整读取任何 GDD 之前，检查实体注册表：

```
Read path="design/registry/entities.yaml"
```

If the registry exists and has entries, use it as a **pre-built conflict
baseline**: known entities, items, formulas, and constants with their
authoritative values and source GDDs. In Phase 2, grep GDDs for registered
names first — this is faster than reading all GDDs in full before knowing
what to look for.
如果注册表存在且有条目，将其用作**预构建的冲突基线**：已知实体、物品、
公式及其权威值与源 GDD。在阶段 2 中，先在 GDD 中 grep 已注册的名称 ——
这比在不知道要查找什么之前完整读取所有 GDD 更快。

If the registry is empty or absent: proceed without it. Note in the report:
"Entity registry is empty — consistency checks rely on full GDD reads only.
Run `/consistency-check` after this review to populate the registry."
如果注册表为空或不存在：在没有它的情况下继续。在报告中注明：
"实体注册表为空 —— 一致性检查仅依赖完整 GDD 读取。在本评审之后运行
`/consistency-check` 来填充注册表。"

### Phase 1c — L1/L2: Full Document Load
### 阶段 1c —— L1/L2：完整文档加载

Full-read the in-scope documents:
完整读取范围内的文档：

1. `design/gdd/game-concept.md` — game vision, core loop, MVP definition
2. `design/gdd/game-pillars.md` if it exists — design pillars and anti-pillars
3. `design/gdd/systems-index.md` — authoritative system list, layers, dependencies, status
4. **Every in-scope system GDD in `design/gdd/`** — read completely (skip
   game-concept.md and systems-index.md — those are read above)
1. `design/gdd/game-concept.md` —— 游戏愿景、核心循环、MVP 定义
2. `design/gdd/game-pillars.md`（如存在）—— 设计支柱与反支柱
3. `design/gdd/systems-index.md` —— 权威系统列表、层级、依赖、状态
4. **`design/gdd/` 中每一个范围内的系统 GDD** —— 完整读取（跳过
   game-concept.md 和 systems-index.md —— 这些已在上面读取）

Report: "Loaded [N] system GDDs covering [M] systems. Pillars: [list]. Anti-pillars: [list]."
报告："已加载 [N] 个系统 GDD，覆盖 [M] 个系统。支柱：[列表]。反支柱：[列表]。"

If fewer than 2 system GDDs exist, stop:
如果系统 GDD 数量少于 2 个，则停止：
> "Cross-GDD review requires at least 2 system GDDs. Write more GDDs first,
> then re-run `/review-all-gdds`."
> "跨 GDD 评审需要至少 2 个系统 GDD。请先编写更多 GDD，
> 然后重新运行 `/review-all-gdds`。"

---

### Parallel Execution
### 并行执行

Phase 2 (Consistency) and Phase 3 (Design Theory) are independent — they read
the same GDD inputs but produce separate reports. Spawn both as parallel Task
agents simultaneously rather than waiting for Phase 2 to complete before
starting Phase 3. Collect both results before writing the combined report.
阶段 2（一致性）与阶段 3（设计理论）相互独立 —— 它们读取相同的 GDD 输入，
但产出各自的报告。同时将两者作为并行 Task 智能体启动，而不是等待阶段 2 完成
后再启动阶段 3。在编写合并报告之前收集两份结果。

**When spawning parallel Task agents for Phase 2 and Phase 3, always pass:**
- The complete list of GDD file paths loaded in Phase 1 (explicit paths, not just counts)
- The full TR registry contents if loaded in Phase 1b (paste the registry text, not just a file path)
- The specific checklist items assigned to that agent's phase (Phase 2 gets 2a–2f; Phase 3 gets 3a–3g)
- The engine name and version from `.claude/docs/technical-preferences.md` and `docs/engine-reference/[engine]/VERSION.md`
**在为阶段 2 和阶段 3 启动并行 Task 智能体时，始终传递：**
- 阶段 1 中加载的 GDD 文件路径完整列表（显式路径，不只是数量）
- 若在阶段 1b 中加载了 TR 注册表，则传递其完整内容（粘贴注册表文本，而不仅是文件路径）
- 分配给该智能体阶段的特定检查清单条目（阶段 2 获得 2a–2f；阶段 3 获得 3a–3g）
- 来自 `.claude/docs/technical-preferences.md` 和 `docs/engine-reference/[engine]/VERSION.md` 的引擎名称与版本

Do not rely on the subagent to re-read these files — it has its own context window and cannot access Phase 1 results unless they are explicitly passed in the Task prompt.
不要依赖子智能体重新读取这些文件 —— 它有独立的上下文窗口，除非在 Task 提示中显式传递，否则无法访问阶段 1 的结果。

---

## Phase 2: Cross-GDD Consistency
## 阶段 2：跨 GDD 一致性

Work through every pair and group of GDDs to find contradictions and gaps.
遍历每一对和每一组 GDD，以查找矛盾和缺漏。

### 2a: Dependency Bidirectionality
### 2a：依赖双向性

For every GDD's Dependencies section, check that every listed dependency is
reciprocal:
对于每个 GDD 的 Dependencies 段落，检查所列每个依赖是否互为对应：
- If GDD-A lists "depends on GDD-B", check that GDD-B lists GDD-A as a dependent
- If GDD-A lists "depended on by GDD-C", check that GDD-C lists GDD-A as a dependency
- Flag any one-directional dependency as a consistency issue
- 若 GDD-A 列出 "depends on GDD-B"，检查 GDD-B 是否将 GDD-A 列为被依赖者
- 若 GDD-A 列出 "depended on by GDD-C"，检查 GDD-C 是否将 GDD-A 列为依赖项
- 将任何单向依赖标记为一致性问题

```
⚠️  Dependency Asymmetry
[system-a].md lists: Depends On → [system-b].md
[system-b].md does NOT list [system-a].md as a dependent
→ One of these documents has a stale dependency section
```

### 2b: Rule Contradictions
### 2b：规则矛盾

For each game rule, mechanic, or constraint defined in any GDD, check whether
any other GDD defines a contradicting rule for the same situation:
对于任何 GDD 中定义的每条游戏规则、机制或约束，检查是否有其他 GDD 为相同
情形定义了相互矛盾的规则：

Categories to scan:
要扫描的类别：
- **Floor/ceiling rules**: Does any GDD define a minimum value for an output? Does any other say a different system can bypass that floor? These contradict.
- **Resource ownership**: If two GDDs both define how a shared resource accumulates or depletes, do they agree?
- **State transitions**: If GDD-A describes what happens when a character dies,
  does GDD-B's description of the same event agree?
- **Timing**: If GDD-A says "X happens on the same frame", does GDD-B assume
  it happens asynchronously?
- **Stacking rules**: If GDD-A says status effects stack, does GDD-B assume
  they don't?
- **下限/上限规则**：是否有 GDD 为某个输出定义了最小值？是否有其他 GDD 表示不同系统可以绕过该下限？这两者相互矛盾。
- **资源归属**：如果两个 GDD 都定义了某个共享资源如何累积或消耗，它们是否一致？
- **状态转换**：如果 GDD-A 描述角色死亡时发生什么，GDD-B 对同一事件的描述是否一致？
- **时序**：如果 GDD-A 表示 "X 在同一帧内发生"，GDD-B 是否假设它异步发生？
- **叠加规则**：如果 GDD-A 表示状态效果可叠加，GDD-B 是否假设它们不能叠加？

```
🔴 Rule Contradiction
[system-a].md: "Minimum [output] after reduction is [floor_value]"
[system-b].md: "[mechanic] bypasses [system-a]'s rules and can reduce [output] to 0"
→ These rules directly contradict. Which GDD is authoritative?
```

### 2c: Stale References
### 2c：过期引用

For every cross-document reference (GDD-A mentions a mechanic, value, or
system name from GDD-B), verify the referenced element still exists in GDD-B
with the same name and behaviour:
对于每个跨文档引用（GDD-A 提及 GDD-B 中的机制、数值或系统名称），验证被引用
元素在 GDD-B 中是否仍以相同名称和行为存在：

- If GDD-A says "combo multiplier from the combat system feeds into score", check
  that the combat GDD actually defines a combo multiplier that outputs to score
- If GDD-A references "the progression curve defined in [system].md", check that
  [system].md actually has that curve, not a different progression model
- If GDD-A was written before GDD-B and assumed a mechanic that GDD-B later
  designed differently, flag GDD-A as containing a stale reference
- 若 GDD-A 表示 "combat 系统的连击倍数计入 score"，检查 combat GDD 是否确实定义了输出到 score 的连击倍数
- 若 GDD-A 引用 "[system].md 中定义的进度曲线"，检查 [system].md 是否确实包含该曲线，而非不同的进度模型
- 若 GDD-A 在 GDD-B 之前编写，并假设了一种 GDD-B 后来以不同方式设计的机制，将 GDD-A 标记为包含过期引用

```
⚠️  Stale Reference
inventory.md (written first): "Item weight uses the encumbrance formula
  from movement.md"
movement.md (written later): Defines no encumbrance formula — uses a flat
  carry limit instead
→ inventory.md references a formula that doesn't exist
```

### 2d: Data and Tuning Knob Ownership Conflicts
### 2d：数据与调优旋钮归属冲突

Two GDDs should not both claim to own the same data or tuning knob. Scan all
Tuning Knobs sections across all GDDs and flag duplicates:
两个 GDD 不应都声称拥有同一数据或调优旋钮。扫描所有 GDD 的 Tuning Knobs 段落
并标记重复项：

```
⚠️  Ownership Conflict
[system-a].md Tuning Knobs: "[multiplier_name] — controls [output] scaling"
[system-b].md Tuning Knobs: "[multiplier_name] — scales [output] with [factor]"
→ Two GDDs define multipliers on the same output. Which owns the final value?
  This will produce either a double-application bug or a design conflict.
```

### 2e: Formula Compatibility
### 2e：公式兼容性

For GDDs whose formulas are connected (output of one feeds input of another),
check that the output range of the upstream formula is within the expected
input range of the downstream formula:
对于公式相连的 GDD（一个公式的输出作为另一个的输入），检查上游公式的输出
范围是否落在下游公式的预期输入范围内：

- If [system-a].md outputs values between [min]–[max], and [system-b].md is
  designed to receive values between [min2]–[max2], is the mismatch intentional?
- If an economy GDD expects resource acquisition in range X, and the
  progression GDD generates it at range Y, the economy will be trivial or
  inaccessible — is that intended?
- 若 [system-a].md 输出值在 [min]–[max] 之间，而 [system-b].md 设计为接收 [min2]–[max2] 之间的值，这种不匹配是有意的吗？
- 若经济 GDD 期望资源获取在范围 X，而进度 GDD 在范围 Y 生成资源，经济将变得过于简单或无法触及 —— 这是有意的吗？

Flag incompatibilities as CONCERNS (design judgment needed, not necessarily wrong):
将不兼容项标记为 CONCERNS（需要设计判断，不一定错误）：

```
⚠️  Formula Range Mismatch
[system-a].md: Max [output] = [value_a] (at max [condition])
[system-b].md: Base [input] = [value_b], max [input] = [value_c]
→ Late-[stage] [scenario] can resolve in a single [event].
  Is this intentional? If not, either [system-a]'s ceiling or [system-b]'s ceiling needs adjustment.
```

### 2f: Acceptance Criteria Cross-Check
### 2f：验收标准交叉检查

Scan Acceptance Criteria sections across all GDDs for contradictions:
扫描所有 GDD 的 Acceptance Criteria 段落，查找矛盾：

- GDD-A criteria: "Player cannot die from a single hit"
- GDD-B criteria: "Boss attack deals 150% of player max health"
These acceptance criteria cannot both pass simultaneously.
- GDD-A 标准："玩家不能被一击致死"
- GDD-B 标准："Boss 攻击造成玩家最大生命值 150% 的伤害"
这两个验收标准无法同时通过。

---

## Phase 3: Game Design Holism
## 阶段 3：游戏设计整体性

Review all GDDs together through the lens of game design theory and player
psychology. These are issues that individual GDD reviews cannot catch because
they require seeing all systems at once.
透过游戏设计理论和玩家心理学的视角将所有 GDD 一起评审。这些是单个 GDD 评审
无法捕捉到的问题，因为它们需要同时查看所有系统。

### 3a: Progression Loop Competition
### 3a：进度循环竞争

A game should have one dominant progression loop that players feel is "the
point" of the game, with supporting loops that feed into it. When multiple
systems compete equally as the primary progression driver, players don't know
what the game is about.
一款游戏应有一个主导进度循环，让玩家觉得这是游戏的 "核心"，并有支撑性循环
为之输送内容。当多个系统作为主要进度驱动同等竞争时，玩家会不知道游戏是关于
什么的。

Scan all GDDs for systems that:
扫描所有 GDD 中存在以下情况的系统：
- Award the player's primary resource (XP, levels, prestige, unlocks)
- Define themselves as the "core" or "main" loop
- Have comparable depth and time investment to other systems doing the same
- 给予玩家主要资源（经验值、等级、声望、解锁）
- 自我定义为 "核心" 或 "主要" 循环
- 与其他做同样事情的系统具有可比的深度和时间投入

```
⚠️  Competing Progression Loops
combat.md: Awards XP, unlocks abilities, is described as "the core loop"
crafting.md: Awards XP, unlocks recipes, is described as "the primary activity"
exploration.md: Awards XP, unlocks map areas, described as "the main driver"
→ Three systems all claim to be the primary progression loop and all award
  the same primary currency. Players will optimise one and ignore the others.
  Consider: one primary loop with the others as support systems.
```

### 3b: Player Attention Budget
### 3b：玩家注意力预算

Count how many systems require active player attention simultaneously during
a typical session. Each actively-managed system costs attention:
统计在一次典型会话中有多少系统需要玩家同时主动关注。每个主动管理的系统都
消耗注意力：

- Active = player must make decisions about this system regularly during play
- Passive = system runs automatically, player sees results but doesn't manage it
- 主动 = 玩家在游玩过程中必须定期就该系统做决策
- 被动 = 系统自动运行，玩家看到结果但不去管理它

More than 3-4 simultaneously active systems creates cognitive overload for most
players. Present the count and flag if it exceeds 4 concurrent active systems:
超过 3-4 个同时主动的系统会给大多数玩家带来认知过载。展示数量，并在并发主动
系统超过 4 个时标记：

```
⚠️  Cognitive Load Risk
Simultaneously active systems during [core loop moment]:
  1. [system-a].md — [decision type] (active)
  2. [system-b].md — [resource management] (active)
  3. [system-c].md — [tracking] (active)
  4. [system-d].md — [item/action use] (active)
  5. [system-e].md — [cooldown/timer management] (active)
  6. [system-f].md — [coordination decisions] (active)
  6 simultaneously active systems during the core loop.
  Research suggests 3-4 is the comfortable limit for most players.
  Consider: which of these can be made passive or simplified?
```

### 3c: Dominant Strategy Detection
### 3c：主导策略检测

A dominant strategy makes other strategies irrelevant — players discover it,
use it exclusively, and find the rest of the game boring. Look for:
主导策略会使其他策略变得无关紧要 —— 玩家发现它、独占使用它，并觉得游戏其余
部分无聊。寻找：

- **Resource monopolies**: One strategy generates a resource significantly
  faster than all others
- **Risk-free power**: A strategy that is both high-reward and low-risk
  (if high-risk strategies exist, they need proportionally higher reward)
- **No trade-offs**: An option that is superior in all dimensions to all others
- **Obvious optimal path**: If any progression choice is "clearly correct",
  the others aren't real choices
- **资源垄断**：某种策略生成资源的速度显著快于其他所有策略
- **无风险强度**：一种既高回报又低风险的策略（若存在高风险策略，则需要相应更高的回报）
- **无取舍**：在所有维度上都优于所有其他选项的选项
- **明显最优路径**：如果某个进度选择 "明显正确"，其他选择就不是真正的选择

```
⚠️  Potential Dominant Strategy
combat.md: Ranged attacks deal 80% of melee damage with no risk
combat.md: Melee attacks deal 100% damage but require close range
→ Unless melee has a significant compensating advantage (AOE, stagger,
  resource regeneration), ranged is dominant — higher safety, only 20% less
  damage. Consider what melee offers that ranged cannot.
```

### 3d: Economic Loop Analysis
### 3d：经济循环分析

Identify all resources across all GDDs (gold, XP, crafting materials, stamina,
health, mana, etc.). For each resource, map its **sources** (how players gain
it) and **sinks** (how players spend it).
识别所有 GDD 中的所有资源（金币、经验值、合成材料、耐力、生命、法力等）。
对于每种资源，绘制其**来源**（玩家如何获取它）和**消耗口**（玩家如何花费它）。

Flag dangerous economic conditions:
标记危险的经济状况：

| Condition | Sign | Risk |
|-----------|------|------|
| **Infinite source, no sink** | Resource accumulates indefinitely | Late game becomes trivially easy |
| **Sink, no source** | Resource drains to zero | System becomes unavailable |
| **Source >> Sink** | Surplus accumulates | Resource becomes meaningless |
| **Sink >> Source** | Constant scarcity | Frustration and gatekeeping |
| **Positive feedback loop** | More resource → easier to earn more | Runaway leader, snowball |
| **No catch-up** | Falling behind accelerates deficit | Unrecoverable states |
| 状况 | 征兆 | 风险 |
|-----------|------|------|
| **无限来源，无消耗口** | 资源无限累积 | 后期变得过于简单 |
| **有消耗口，无来源** | 资源消耗至零 | 系统变得不可用 |
| **来源 >> 消耗口** | 剩余累积 | 资源变得毫无意义 |
| **消耗口 >> 来源** | 持续稀缺 | 挫败感与门槛限制 |
| **正反馈循环** | 资源越多 → 赚取越多越容易 | 一骑绝尘、雪球效应 |
| **无追赶机制** | 落后加速差距 | 不可恢复的状态 |

```
🔴 Economic Imbalance: Unbounded Positive Feedback
gold economy:
  Sources: monster drops (scales with player power), merchant selling (unlimited)
  Sinks: equipment purchase (one-time), ability upgrades (finite count)
→ After equipment and abilities are purchased, gold has no sink.
  Infinite surplus. Gold becomes meaningless mid-game.
  Add ongoing gold sinks (upkeep, consumables, cosmetics, gambling).
```

### 3e: Difficulty Curve Consistency
### 3e：难度曲线一致性

When multiple systems scale with player progression, they must scale in
compatible directions and at compatible rates. Mismatched scaling curves
create unintended difficulty spikes or trivialisations.
当多个系统随玩家进度扩展时，它们必须以兼容的方向和兼容的速率扩展。不匹配的
扩展曲线会制造意外的难度尖峰或过度简单化。

For each system that scales over time, extract:
对于每个随时间扩展的系统，提取：
- What scales (enemy health, player damage, resource cost, area size)
- How it scales (linear, exponential, stepped)
- When it scales (level, time, area)
- 什么在扩展（敌人生命、玩家伤害、资源成本、区域大小）
- 如何扩展（线性、指数、阶跃）
- 何时扩展（等级、时间、区域）

Compare all scaling curves. Flag mismatches:
比较所有扩展曲线。标记不匹配项：

```
⚠️  Difficulty Curve Mismatch
combat.md: Enemy health scales exponentially with area (×2 per area)
progression.md: Player damage scales linearly with level (+10% per level)
→ By area 5, enemies have 32× base health; player deals ~1.5× base damage.
  The gap widens indefinitely. Late areas will become inaccessibly difficult
  unless the curves are reconciled.
```

### 3f: Pillar Alignment
### 3f：支柱对齐

Every system should clearly serve at least one design pillar. A system that
serves no pillar is "scope creep by design" — it's in the game but not in
service of what the game is trying to be.
每个系统都应清晰地服务于至少一个设计支柱。不服务于任何支柱的系统是 "设计
层面的范围蔓延" —— 它存在于游戏中，但不为游戏想要成为的样子服务。

For each GDD system, check its Player Fantasy section against the design pillars.
Flag any system whose stated fantasy doesn't map to any pillar:
对于每个 GDD 系统，将其 Player Fantasy 段落与设计支柱对照。标记任何声明的
幻想不映射到任何支柱的系统：

```
⚠️  Pillar Drift
fishing-system.md: Player Fantasy — "peaceful, meditative activity"
Pillars: "Brutal Combat", "Tense Survival", "Emergent Stories"
→ The fishing system serves none of the three pillars. Either add a pillar
  that covers it, redesign it to serve an existing pillar, or cut it.
```

Also check anti-pillars — flag any system that does what an anti-pillar
explicitly says the game will NOT do:
同时检查反支柱 —— 标记任何做了反支柱明确表示游戏不会做的系统：

```
🔴 Anti-Pillar Violation
Anti-Pillar: "We will NOT have linear story progression — player defines their path"
main-quest.md: Defines a 12-chapter linear story with mandatory sequence
→ This system directly violates the defined anti-pillar.
```

### 3g: Player Fantasy Coherence
### 3g：玩家幻想一致性

The player fantasies across all systems should be compatible — they should
reinforce a consistent identity for what the player IS in this game. Conflicting
player fantasies create identity confusion.
所有系统中的玩家幻想应相互兼容 —— 它们应强化一致的 "玩家在这款游戏中是谁"
的身份认知。相互冲突的玩家幻想会造成身份困惑。

```
⚠️  Player Fantasy Conflict
combat.md: "You are a ruthless, precise warrior — every kill is earned"
dialogue.md: "You are a charismatic diplomat — violence is always avoidable"
exploration.md: "You are a reckless adventurer — diving in without a plan"
→ Three systems present incompatible identities. Players will feel the game
  doesn't know what it wants them to be. Consider: do these fantasies serve
  the same core identity from different angles, or do they genuinely conflict?
```

---

## Phase 4: Cross-System Scenario Walkthrough
## 阶段 4：跨系统场景走查

Walk through the game from the player's perspective to find problems that only
appear at the interaction boundary between multiple systems — things static
analysis of individual GDDs cannot surface.
从玩家视角走查游戏，找出仅在多个系统交互边界才会出现的问题 —— 这些是个别
GDD 的静态分析无法暴露的。

### 4a: Identify Key Multi-System Moments
### 4a：识别关键多系统时刻

Scan all GDDs and identify the 3–5 most important player-facing moments where
multiple systems activate simultaneously. Look specifically for:
扫描所有 GDD，识别 3-5 个最重要的、面向玩家且多系统同时触发的时刻。专门
寻找：

- **Combat + Economy overlap**: killing enemies that drop resources, spending
  resources during combat, death/respawn interacting with economy state
- **Progression + Difficulty overlap**: level-up triggering mid-fight, ability
  unlocks changing combat viability, difficulty scaling at progression milestones
- **Narrative + Gameplay overlap**: dialogue choices locking/unlocking mechanics,
  story beats interrupting resource loops, quest completion triggering system
  state changes
- **3+ system chains**: any player action that triggers System A, which feeds
  into System B, which triggers System C (these are highest-risk interaction paths)
- **战斗 + 经济重叠**：击杀掉落资源的敌人、在战斗中花费资源、死亡/重生与经济状态交互
- **进度 + 难度重叠**：战斗中途升级、能力解锁改变战斗可行性、难度在进度里程碑处扩展
- **叙事 + 玩法重叠**：对话选项锁定/解锁机制、剧情节点打断资源循环、任务完成触发系统状态变更
- **3+ 系统链**：任何触发系统 A → 输入系统 B → 触发系统 C 的玩家行为（这些是最高风险的交互路径）

List each identified scenario with a one-line description before proceeding.
在继续之前，为每个识别出的场景列出一句描述。

### 4b: Walk Through Each Scenario
### 4b：走查每个场景

For each scenario, step through the sequence explicitly:
对于每个场景，明确地逐步走查：

1. **Trigger** — what player action or game event starts this?
2. **Activation order** — which systems activate, in what sequence?
3. **Data flow** — what does each system output, and is that output a valid
   input for the next system in the chain?
4. **Player experience** — what does the player see, hear, or feel at each step?
5. **Failure modes** — are there any of the following?
   - **Race conditions**: two systems trying to modify the same state simultaneously
   - **Feedback loops**: System A amplifies System B which re-amplifies System A
     with no cap or dampener
   - **Broken state transitions**: a system assumes a state that a previous
     system may have changed (e.g., "player is alive" assumption after a combat
     step that could have caused death)
   - **Contradictory messaging**: player receives conflicting feedback from two
     systems reacting to the same event (e.g., "success" sound + "failure" UI)
   - **Compounding difficulty spikes**: two systems both scaling up at the same
     progression point, multiplying the intended difficulty increase
   - **Reward conflicts**: two systems both reacting to the same trigger with
     rewards that together exceed the intended value (double-dipping)
   - **Undefined behavior**: the GDDs don't specify what happens in this combined
     state (neither system's rules cover it)
1. **触发** —— 什么玩家行为或游戏事件启动了它？
2. **激活顺序** —— 哪些系统被激活，按什么顺序？
3. **数据流** —— 每个系统输出什么？该输出是否是链中下一个系统的有效输入？
4. **玩家体验** —— 玩家在每个步骤看到、听到或感觉到什么？
5. **失败模式** —— 是否存在以下情况？
   - **竞争条件**：两个系统试图同时修改相同状态
   - **反馈循环**：系统 A 放大系统 B，系统 B 又放大系统 A，且无上限或阻尼
   - **状态转换破损**：某系统假设了一个可能已被前一个系统改变的状态（例如，"玩家存活" 的假设在一个可能致死的战斗步骤之后）
   - **信息矛盾**：玩家从两个对同一事件做出反应的系统收到相互冲突的反馈（例如，"成功" 音效 + "失败" UI）
   - **复合难度尖峰**：两个系统在同一个进度点同时扩展，使预期难度增加成倍放大
   - **奖励冲突**：两个系统都对同一触发做出反应，奖励合计超过预期价值（双重获利）
   - **未定义行为**：GDD 未指定在这种组合状态下会发生什么（两个系统的规则都没有覆盖它）

```
Example walkthrough:
Scenario: Player kills elite enemy at level-up threshold during active quest

Trigger: Player lands killing blow on elite enemy
→ combat.md: awards kill XP (100 pts)
→ progression.md: XP total crosses level threshold → triggers level-up
  Output: new level, stat increases, ability unlock popup
→ quest.md: kill-count criterion met → triggers quest completion event
  Output: quest reward XP (500 pts), completion fanfare
→ progression.md (again): quest XP added → triggers SECOND level-up in same frame
  ⚠️  Data flow issue: quest.md awards XP without checking if a level-up
  is already in progress. progression.md has no guard against concurrent
  level-up events. Undefined behavior: does the player level up once or twice?
  Does the ability popup fire twice? Does the second level use the updated or
  pre-update stat baseline?
```

### 4c: Flag Scenario Issues
### 4c：标记场景问题

For each problem found during the walkthrough, categorize severity:
对于走查中发现的每个问题，按严重程度分类：

- **BLOCKER**: undefined behavior, broken state transition, or contradictory
  player messaging — the experience is broken or incoherent in this scenario
- **WARNING**: compounding spikes, feedback loops without caps, reward conflicts —
  the experience works but produces unintended outcomes
- **INFO**: minor ordering ambiguity or messaging overlap — worth noting but
  unlikely to cause player-visible problems
- **BLOCKER**：未定义行为、状态转换破损或玩家信息矛盾 —— 在此场景中体验是破损或不连贯的
- **WARNING**：复合尖峰、无上限反馈循环、奖励冲突 —— 体验能用但产生意外结果
- **INFO**：轻微的顺序歧义或信息重叠 —— 值得记录但不太可能造成玩家可见的问题

Add all findings to the output report under **"Cross-System Scenario Issues"**.
Each finding must cite: the scenario name, the specific systems involved, the
step where the issue occurs, and the nature of the failure mode.
将所有发现添加到输出报告的 **"Cross-System Scenario Issues"** 下。每条发现必须
引用：场景名称、涉及的具体系统、问题发生的步骤以及失败模式的性质。

---

## Phase 5: Output the Review Report
## 阶段 5：输出评审报告

```
## Cross-GDD Review Report
Date: [date]
GDDs Reviewed: [N]
Systems Covered: [list]

---

### Consistency Issues

#### Blocking (must resolve before architecture begins)
🔴 [Issue title]
[What GDDs are involved, what the contradiction is, what needs to change]

#### Warnings (should resolve, but won't block)
⚠️  [Issue title]
[What GDDs are involved, what the concern is]

---

### Game Design Issues

#### Blocking
🔴 [Issue title]
[What the problem is, which GDDs are involved, design recommendation]

#### Warnings
⚠️  [Issue title]
[What the concern is, which GDDs are affected, recommendation]

---

### Cross-System Scenario Issues

Scenarios walked: [N]
[List scenario names]

#### Blockers
🔴 [Scenario name] — [Systems involved]
[Step where failure occurs, nature of the failure mode, what must be resolved]

#### Warnings
⚠️  [Scenario name] — [Systems involved]
[What the unintended outcome is, recommendation]

#### Info
ℹ️  [Scenario name] — [Systems involved]
[Minor ordering ambiguity or note]

---

### GDDs Flagged for Revision

| GDD | Reason | Type | Priority |
|-----|--------|------|----------|
| [system-a].md | Rule contradiction with [system-b].md | Consistency | Blocking |
| [system-c].md | Stale reference to nonexistent mechanic | Consistency | Blocking |
| [system-d].md | No pillar alignment | Design Theory | Warning |

---

### Verdict: [PASS / CONCERNS / FAIL]

PASS: No blocking issues. Warnings present but don't prevent architecture.
CONCERNS: Warnings present that should be resolved but are not blocking.
FAIL: One or more blocking issues must be resolved before architecture begins.

### If FAIL — required actions before re-running:
[Specific list of what must change in which GDD]
```

---

## Phase 6: Write Report and Flag GDDs
## 阶段 6：编写报告并标记 GDD

Use `AskUserQuestion` for write permission:
- Prompt: "May I write this review to `design/gdd/gdd-cross-review-[date].md`?"
- Options: `[A] Yes — write the report` / `[B] No — skip`
使用 `AskUserQuestion` 请求写入许可：
- 提示："May I write this review to `design/gdd/gdd-cross-review-[date].md`?"
- 选项：`[A] Yes — write the report` / `[B] No — skip`

If any GDDs are flagged for revision, use a second `AskUserQuestion`:
- Prompt: "Should I update the systems index to mark these GDDs as needing revision? ([list of flagged GDDs])"
- Options: `[A] Yes — update systems index` / `[B] No — leave as-is`
- If yes: update each flagged GDD's Status field in systems-index.md to "Needs Revision".
  (Do NOT append parentheticals to the status value — other skills match "Needs Revision"
  as an exact string and parentheticals break that match.)
若有任何 GDD 被标记需修订，使用第二个 `AskUserQuestion`：
- 提示："Should I update the systems index to mark these GDDs as needing revision? ([list of flagged GDDs])"
- 选项：`[A] Yes — update systems index` / `[B] No — leave as-is`
- 若是：将 systems-index.md 中每个被标记 GDD 的 Status 字段更新为 "Needs Revision"。
  （不要在状态值后追加括注 —— 其他技能会按精确字符串匹配 "Needs Revision"，
  括注会破坏该匹配。）

### Session State Update
### 会话状态更新

After writing the report (and updating systems index if approved), silently
append to `production/session-state/active.md`:
在写入报告后（若被批准则同时更新 systems index），静默追加到
`production/session-state/active.md`：

    ## Session Extract — /review-all-gdds [date]
    - Verdict: [PASS / CONCERNS / FAIL]
    - GDDs reviewed: [N]
    - Flagged for revision: [comma-separated list, or "None"]
    - Blocking issues: [N — brief one-line descriptions, or "None"]
    - Recommended next: [the Phase 7 handoff action, condensed to one line]
    - Report: design/gdd/gdd-cross-review-[date].md   ← only if user approved the write
    - Report: (not written — user declined at [date])  ← only if user declined the write

Use the appropriate line based on the user's response to the write-permission widget in Phase 6.
根据用户在阶段 6 对写入许可组件的响应，使用对应行。

If `active.md` does not exist, create it with this block as the initial content.
Confirm in conversation: "Session state updated."
若 `active.md` 不存在，以此块内容作为初始内容创建它。在对话中确认：
"Session state updated."

---

## Phase 7: Handoff
## 阶段 7：交接

After all file writes are complete, use `AskUserQuestion` for a closing widget.
所有文件写入完成后，使用 `AskUserQuestion` 弹出结束组件。

Before building options, check project state:
- Are there any Warning-level items that are simple edits (flagged with "30-second edit", "brief addition", or similar)? → offer inline quick-fix option
- Are any GDDs in the "Flagged for Revision" table? → offer /design-review option for each
- Read systems-index.md for the next system with Status: Not Started → offer /design-system option
- Is the verdict PASS or CONCERNS? → offer /gate-check or /create-architecture
构建选项之前，检查项目状态：
- 是否存在 Warning 级别的简单修改项（标有 "30-second edit"、"brief addition" 或类似）？→ 提供内联快速修复选项
- "Flagged for Revision" 表格中是否有 GDD？→ 为每个提供 /design-review 选项
- 读取 systems-index.md 查找下一个 Status: Not Started 的系统 → 提供 /design-system 选项
- 评审结果是否为 PASS 或 CONCERNS？→ 提供 /gate-check 或 /create-architecture

Build the option list dynamically — only include options that apply:
动态构建选项列表 —— 仅包含适用的选项：

**Option pool:**
- `[_] Apply quick fix: [W-XX description] in [gdd-name].md — [effort estimate]` (one option per simple-edit warning; only for Warning-level, not Blocking)
- `[_] Run /design-review [flagged-gdd-path] — address flagged warnings` (one per flagged GDD, if any)
- `[_] Run /design-system [next-system] — next in design order` (always include, name the actual system)
- `[_] Run /create-architecture — begin architecture (verdict is PASS/CONCERNS)` (include if verdict is not FAIL)
- `[_] Run /gate-check — validate Systems Design phase gate` (include if verdict is PASS)
- `[_] Stop here`
**选项池：**
- `[_] Apply quick fix: [W-XX description] in [gdd-name].md — [effort estimate]`（每个简单修改警告一个选项；仅用于 Warning 级别，不用于 Blocking）
- `[_] Run /design-review [flagged-gdd-path] — address flagged warnings`（每个被标记 GDD 一个，若有）
- `[_] Run /design-system [next-system] — next in design order`（始终包含，并指明实际系统名）
- `[_] Run /create-architecture — begin architecture (verdict is PASS/CONCERNS)`（若评审结果非 FAIL 则包含）
- `[_] Run /gate-check — validate Systems Design phase gate`（若评审结果为 PASS 则包含）
- `[_] Stop here`

Assign letters A, B, C… only to included options. Mark the most pipeline-advancing option as `(recommended)`.
仅向所包含的选项分配字母 A、B、C…。将最推进流水线的选项标记为 `(recommended)`。

Never end the skill with plain text. Always close with this widget.
永远不要以纯文本结束技能。始终以本组件收尾。

---

## Error Recovery Protocol
## 错误恢复协议

If any spawned agent returns BLOCKED, errors, or fails to complete:
若任何被启动的智能体返回 BLOCKED、错误或未能完成：

1. **Surface immediately**: Report "[AgentName]: BLOCKED — [reason]" before continuing
2. **Assess dependencies**: If the blocked agent's output is required by a later phase, do not proceed past that phase without user input
3. **Offer options** via AskUserQuestion with three choices:
   - Skip this agent and note the gap in the final report
   - Retry with narrower scope (fewer GDDs, single-system focus)
   - Stop here and resolve the blocker first
4. **Always produce a partial report** — output whatever was completed so work is not lost
1. **立即暴露**：在继续之前报告 "[AgentName]: BLOCKED — [reason]"
2. **评估依赖**：若被阻塞智能体的输出是后续阶段所必需的，则在无用户输入情况下不要越过该阶段
3. **通过 AskUserQuestion 提供选项**，三个选择：
   - 跳过该智能体并在最终报告中注明缺漏
   - 以更窄范围重试（更少 GDD、单系统聚焦）
   - 在此停止，先解决阻塞问题
4. **始终产出部分报告** —— 输出已完成的内容，确保工作不丢失

---

## Collaborative Protocol
## 协作协议

1. **Read silently** — load all GDDs before presenting anything
2. **Show everything** — present the full consistency and design theory analysis
   before asking for any action
3. **Distinguish blocking from advisory** — not every issue needs to block
   architecture; be clear about which do
4. **Don't make design decisions** — flag contradictions and options, but never
   unilaterally decide which GDD is "right"
5. **Ask before writing** — confirm before writing the report or updating the
   systems index
6. **Be specific** — every issue must cite the exact GDD, section, and text
   involved; no vague warnings
1. **静默读取** —— 在展示任何内容之前加载所有 GDD
2. **展示一切** —— 在要求任何行动之前，展示完整的一致性与设计理论分析
3. **区分阻塞性与建议性** —— 不是每个问题都需要阻塞架构；明确哪些是阻塞性的
4. **不做设计决策** —— 标记矛盾与选项，但永远不单方面决定哪个 GDD "正确"
5. **写入前询问** —— 在写入报告或更新 systems index 之前进行确认
6. **具体明确** —— 每个问题都必须引用精确的 GDD、段落和涉及的文本；不要模糊警告
