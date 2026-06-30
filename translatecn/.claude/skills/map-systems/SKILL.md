---
name: map-systems
description: "Decompose a game concept into individual systems, map dependencies, prioritize design order, and create the systems index."
argument-hint: "[next | system-name] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, TodoWrite, Task
model: sonnet
---
---
name: map-systems
description: "将游戏概念分解为单个系统、映射依赖关系、确定设计顺序优先级，并创建系统索引。"
argument-hint: "[next | system-name] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, TodoWrite, Task
model: sonnet
---

When this skill is invoked:
当调用此技能时：

## Parse Arguments
## 解析参数

Two modes:
两种模式：

- **No argument**: `/map-systems` — Run the full decomposition workflow (Phases 1-5)
  to create or update the systems index.
- **`next`**: `/map-systems next` — Pick the highest-priority undesigned system
  from the index and hand off to `/design-system` (Phase 6).
- **无参数**：`/map-systems` — 运行完整的分解工作流（阶段 1-5）
  以创建或更新系统索引。
- **`next`**：`/map-systems next` — 从索引中挑选最高优先级的未设计系统
  并移交给 `/design-system`（阶段 6）。

Also resolve the review mode (once, store for all gate spawns this run):
1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`
还要解析评审模式（一次，为本次运行的所有门禁生成存储）：
1. 如果传入了 `--review [full|lean|solo]` → 使用它
2. 否则读取 `production/review-mode.txt` → 使用该值
3. 否则 → 默认为 `lean`

See `.claude/docs/director-gates.md` for the full check pattern.
参见 `.claude/docs/director-gates.md` 获取完整检查模式。

---

## Phase 1: Read Concept (Required Context)
## 阶段 1：读取概念（必需上下文）

Read the game concept and any existing design work. This provides the raw material
for systems decomposition.
读取游戏概念和任何现有的设计工作。这提供了系统分解的
原始材料。

**Required:**
- Read `design/gdd/game-concept.md` — **fail with a clear message if missing**:
  > "No game concept found at `design/gdd/game-concept.md`. Run `/brainstorm` first
  > to create one, then come back to decompose it into systems."
**必需：**
- 读取 `design/gdd/game-concept.md` — **如果缺失则失败并给出明确消息**：
  > "No game concept found at `design/gdd/game-concept.md`. Run `/brainstorm` first
  > to create one, then come back to decompose it into systems."

**Optional (read if they exist):**
- Read `design/gdd/game-pillars.md` — pillars constrain priority and scope
- Read `design/gdd/systems-index.md` — if exists, **resume** from where it left off
  (update, don't recreate from scratch)
- Glob `design/gdd/*.md` — check which system GDDs already exist
**可选（如果存在则读取）：**
- 读取 `design/gdd/game-pillars.md` — 支柱约束优先级和范围
- 读取 `design/gdd/systems-index.md` — 如果存在，从中断处 **恢复**
  （更新，不要从头重建）
- Glob `design/gdd/*.md` — 检查哪些系统 GDD 已存在

**If the systems index already exists:**
- Read it and present current status to the user
- Use `AskUserQuestion` to ask:
  "The systems index already exists with [N] systems ([M] designed, [K] not started).
  What would you like to do?"
  - Options: "Update the index with new systems", "Design the next undesigned system",
    "Review and revise priorities"
**如果系统索引已存在：**
- 读取它并向用户呈现当前状态
- 使用 `AskUserQuestion` 询问：
  "The systems index already exists with [N] systems ([M] designed, [K] not started).
  What would you like to do?"
  - 选项："Update the index with new systems"、"Design the next undesigned system"、
    "Review and revise priorities"

---

## Phase 2: Systems Enumeration (Collaborative)
## 阶段 2：系统枚举（协作式）

Extract and identify all systems the game needs. This is the creative core of the
skill — it requires human judgment because concept docs rarely enumerate every
system explicitly.
提取并识别游戏需要的所有系统。这是技能的创意核心 —
它需要人工判断，因为概念文档很少明确枚举每个
系统。

### Step 2a: Extract Explicit Systems
### 步骤 2a：提取显式系统

Scan the game concept for directly mentioned systems and mechanics:
- Core Mechanics section (most explicit)
- Core Loop section (implies what systems drive each loop tier)
- Technical Considerations section (networking, procedural generation, etc.)
- MVP Definition section (required features = required systems)
扫描游戏概念查找直接提及的系统和机制：
- Core Mechanics 章节（最显式）
- Core Loop 章节（暗示哪些系统驱动每个循环层级）
- Technical Considerations 章节（网络、程序化生成等）
- MVP Definition 章节（必需功能 = 必需系统）

### Step 2b: Identify Implicit Systems
### 步骤 2b：识别隐式系统

For each explicit system, identify the **hidden systems** it implies. Games always
need more systems than the concept doc mentions. Use this inference pattern:
对每个显式系统，识别它暗示的 **隐藏系统**。游戏总是
需要比概念文档提及的更多系统。使用此推断模式：

- "Inventory" implies: item database, equipment slots, weight/capacity rules,
  inventory UI, item serialization for save/load
- "Combat" implies: damage calculation, health system, hit detection, status effects,
  enemy AI, combat UI (health bars, damage numbers), death/respawn
- "Open world" implies: streaming/chunking, LOD system, fast travel, map/minimap,
  point of interest tracking, world state persistence
- "Multiplayer" implies: networking layer, lobby/matchmaking, state synchronization,
  anti-cheat, network UI (ping, player list)
- "Crafting" implies: recipe database, ingredient gathering, crafting UI,
  success/failure mechanics, recipe discovery/learning
- "Dialogue" implies: dialogue tree system, dialogue UI, choice tracking, NPC
  state management, localization hooks
- "Progression" implies: XP system, level-up mechanics, skill tree, unlock
  tracking, progression UI, progression save data
- "Inventory" 暗示：物品数据库、装备槽位、重量/容量规则、
  背包 UI、用于存档/读档的物品序列化
- "Combat" 暗示：伤害计算、生命系统、命中检测、状态效果、
  敌人 AI、战斗 UI（生命条、伤害数字）、死亡/重生
- "Open world" 暗示：流式/分块、LOD 系统、快速旅行、地图/小地图、
  兴趣点跟踪、世界状态持久化
- "Multiplayer" 暗示：网络层、大厅/匹配、状态同步、
  反作弊、网络 UI（延迟、玩家列表）
- "Crafting" 暗示：配方数据库、材料收集、制作 UI、
  成功/失败机制、配方发现/学习
- "Dialogue" 暗示：对话树系统、对话 UI、选择跟踪、NPC
  状态管理、本地化钩子
- "Progression" 暗示：XP 系统、升级机制、技能树、解锁
  跟踪、进度 UI、进度存档数据

Explain in conversation text why each implicit system is needed (with examples).
在对话文本中解释为什么需要每个隐式系统（附示例）。

### Step 2c: User Review
### 步骤 2c：用户评审

Present the enumeration organized by category. For each system, show:
- Name
- Category
- Brief description (1 sentence)
- Whether it was explicit (from concept) or implicit (inferred)
按类别组织呈现枚举。对每个系统，显示：
- 名称
- 类别
- 简短描述（1 句话）
- 它是显式的（来自概念）还是隐式的（推断的）

Then use `AskUserQuestion` to capture feedback:
- "Are there systems missing from this list?"
- "Should any of these be combined or split?"
- "Are there systems listed that this game does NOT need?"
然后使用 `AskUserQuestion` 捕获反馈：
- "Are there systems missing from this list?"
- "Should any of these be combined or split?"
- "Are there systems listed that this game does NOT need?"

Iterate until the user approves the enumeration.
迭代直到用户批准枚举。

---

## Phase 3: Dependency Mapping (Collaborative)
## 阶段 3：依赖映射（协作式）

For each system, determine what it depends on. A system "depends on" another if
it cannot function without that other system existing first.
对每个系统，确定它依赖什么。一个系统 "depends on"（依赖于）另一个系统，
如果它不能在另一个系统先存在之前运行。

### Step 3a: Map Dependencies
### 步骤 3a：映射依赖

For each system, list its dependencies. Use these dependency heuristics:
- **Input/output dependencies**: System A produces data System B needs
- **Structural dependencies**: System A provides the framework System B plugs into
- **UI dependencies**: Every gameplay system has a corresponding UI system that
  depends on it (but UI is designed after the gameplay system)
对每个系统，列出其依赖。使用这些依赖启发式：
- **输入/输出依赖**：系统 A 产生系统 B 需要的数据
- **结构依赖**：系统 A 提供系统 B 插入的框架
- **UI 依赖**：每个游戏玩法系统都有对应的 UI 系统依赖于它
  （但 UI 在游戏玩法系统之后设计）

### Step 3b: Sort by Dependency Order
### 步骤 3b：按依赖顺序排序

Arrange systems into layers:
1. **Foundation**: Systems with zero dependencies (designed and built first)
2. **Core**: Systems depending only on Foundation systems
3. **Feature**: Systems depending on Core systems
4. **Presentation**: UI and feedback systems that wrap gameplay systems
5. **Polish**: Meta-systems, tutorials, analytics, accessibility
将系统排列为层：
1. **Foundation**：零依赖的系统（首先设计和构建）
2. **Core**：仅依赖于 Foundation 系统的系统
3. **Feature**：依赖于 Core 系统的系统
4. **Presentation**：包装游戏玩法系统的 UI 和反馈系统
5. **Polish**：元系统、教程、分析、可访问性

### Step 3c: Detect Circular Dependencies
### 步骤 3c：检测循环依赖

Check for cycles in the dependency graph. If found:
- Highlight them to the user
- Propose resolutions (interface abstraction, simultaneous design, breaking the
  cycle by defining a contract between the two systems)
检查依赖图中的循环。如果发现：
- 向用户突出显示它们
- 提议解决方案（接口抽象、同时设计、通过
  在两个系统之间定义契约来打破循环）

### Step 3d: Present to User
### 步骤 3d：向用户呈现

Show the dependency map as a layered list. Highlight:
- Any circular dependencies
- Any "bottleneck" systems (many others depend on them — these are high-risk)
- Any systems with no dependents (leaf nodes — lower risk, can be designed late)
以分层列表形式显示依赖映射。突出显示：
- 任何循环依赖
- 任何 "瓶颈" 系统（许多其他系统依赖它们 — 这些是高风险的）
- 任何没有依赖者的系统（叶节点 — 风险较低，可以晚设计）

Use `AskUserQuestion` to ask: "Does this dependency ordering look right? Any
dependencies I'm missing or that should be removed?"
使用 `AskUserQuestion` 询问："Does this dependency ordering look right? Any
dependencies I'm missing or that should be removed?"

**Review mode check** — apply before spawning TD-SYSTEM-BOUNDARY:
- `solo` → skip. Note: "TD-SYSTEM-BOUNDARY skipped — Solo mode." Proceed to priority assignment.
- `lean` → skip (not a PHASE-GATE). Note: "TD-SYSTEM-BOUNDARY skipped — Lean mode." Proceed to priority assignment.
- `full` → spawn as normal.
**评审模式检查** — 在生成 TD-SYSTEM-BOUNDARY 之前应用：
- `solo` → 跳过。注明："TD-SYSTEM-BOUNDARY skipped — Solo mode." 继续优先级分配。
- `lean` → 跳过（不是 PHASE-GATE）。注明："TD-SYSTEM-BOUNDARY skipped — Lean mode." 继续优先级分配。
- `full` → 正常生成。

**After dependency mapping is approved, spawn `technical-director` via Task using gate TD-SYSTEM-BOUNDARY (`.claude/docs/director-gates.md`) before proceeding to priority assignment.**
**在依赖映射批准之后，通过 Task 使用门禁 TD-SYSTEM-BOUNDARY（`.claude/docs/director-gates.md`）生成 `technical-director`，然后继续优先级分配。**

Pass: the dependency map summary, layer assignments, bottleneck systems list, any circular dependency resolutions.
传递：依赖映射摘要、层分配、瓶颈系统列表、任何循环依赖解决方案。

Present the assessment. If REJECT, revise the system boundaries with the user before moving to priority assignment. If CONCERNS, note them inline in the systems index and continue.
呈现评估。如果 REJECT，在与用户一起修订系统边界后再进行优先级分配。如果 CONCERNS，在系统索引中内联注明它们并继续。

---

## Phase 4: Priority Assignment (Collaborative)
## 阶段 4：优先级分配（协作式）

Assign each system to a priority tier based on what milestone it's needed for.
根据每个系统需要的里程碑将其分配到优先级层。

### Step 4a: Auto-Assign Based on Concept
### 步骤 4a：基于概念自动分配

Use these heuristics for initial assignment:
- **MVP**: Systems mentioned in the concept's "Required for MVP" section, plus their
  Foundation-layer dependencies
- **Vertical Slice**: Systems needed for a complete experience in one area
- **Alpha**: All remaining gameplay systems
- **Full Vision**: Polish, meta, and nice-to-have systems
使用这些启发式进行初始分配：
- **MVP**：概念中 "Required for MVP" 章节提及的系统，加上它们的
  Foundation 层依赖
- **Vertical Slice**：一个区域内完整体验所需的系统
- **Alpha**：所有剩余的游戏玩法系统
- **Full Vision**：打磨、元和锦上添花的系统

### Step 4b: User Review
### 步骤 4b：用户评审

Present the priority assignments in a table. For each tier, explain why systems
were placed there.
以表格形式呈现优先级分配。对每个层，解释为什么系统
被放在那里。

Use `AskUserQuestion` to ask: "Do these priority assignments match your vision?
Which systems should be higher or lower priority?"
使用 `AskUserQuestion` 询问："Do these priority assignments match your vision?
Which systems should be higher or lower priority?"

Explain reasoning in conversation: "I placed [system] in MVP because the core loop
requires it — without [system], the 30-second loop can't function."
在对话中解释推理："I placed [system] in MVP because the core loop
requires it — without [system], the 30-second loop can't function."

**"Why" column guidance**: When explaining why each system was placed in a priority tier, mix technical necessity with player-experience reasoning. Do not use purely technical justifications like "Combat needs damage math" — connect to player experience where relevant. Examples of good "Why" entries:
- "Required for the core loop — without it, placement decisions have no consequence (Pillar 2: Placement is the Puzzle)"
- "Ballista's punch-through identity is established here — this stat definition is what makes it feel different from Archer"
- "Foundation for all economy decisions — players must understand upgrade costs to make meaningful placement choices"
**"Why" 列指南**：在解释为什么每个系统被放在优先级层时，混合技术必要性和玩家体验推理。不要使用纯技术理由如 "Combat needs damage math" — 在相关地方连接到玩家体验。好的 "Why" 条目示例：
- "Required for the core loop — without it, placement decisions have no consequence (Pillar 2: Placement is the Puzzle)"
- "Ballista's punch-through identity is established here — this stat definition is what makes it feel different from Archer"
- "Foundation for all economy decisions — players must understand upgrade costs to make meaningful placement choices"

Pure technical necessity ("X depends on Y") is insufficient alone when the system directly shapes player experience.
当系统直接塑造玩家体验时，纯技术必要性（"X depends on Y"）单独是不够的。

**Review mode check** — apply before spawning PR-SCOPE:
- `solo` → skip. Note: "PR-SCOPE skipped — Solo mode." Proceed to writing the systems index.
- `lean` → skip (not a PHASE-GATE). Note: "PR-SCOPE skipped — Lean mode." Proceed to writing the systems index.
- `full` → spawn as normal.
**评审模式检查** — 在生成 PR-SCOPE 之前应用：
- `solo` → 跳过。注明："PR-SCOPE skipped — Solo mode." 继续写入系统索引。
- `lean` → 跳过（不是 PHASE-GATE）。注明："PR-SCOPE skipped — Lean mode." 继续写入系统索引。
- `full` → 正常生成。

**After priorities are approved, spawn `producer` via Task using gate PR-SCOPE (`.claude/docs/director-gates.md`) before writing the index.**
**在优先级批准之后，通过 Task 使用门禁 PR-SCOPE（`.claude/docs/director-gates.md`）生成 `producer`，然后写入索引。**

Pass: total system count per milestone tier, estimated implementation volume per tier (system count × average complexity), team size, stated project timeline.
传递：每个里程碑层的总系统计数、每层的估算实现量（系统计数 × 平均复杂度）、团队规模、声明的项目时间表。

Present the assessment. If UNREALISTIC, offer to revise priority tier assignments before writing the index. If CONCERNS, note them and continue.
呈现评估。如果 UNREALISTIC，在写入索引之前提议修订优先级层分配。如果 CONCERNS，注明它们并继续。

### Step 4c: Determine Design Order
### 步骤 4c：确定设计顺序

Combine dependency sort + priority tier to produce the final design order:
1. MVP Foundation systems first
2. MVP Core systems second
3. MVP Feature systems third
4. Vertical Slice Foundation/Core systems
5. ...and so on
结合依赖排序 + 优先级层以生成最终设计顺序：
1. MVP Foundation 系统优先
2. MVP Core 系统其次
3. MVP Feature 系统第三
4. Vertical Slice Foundation/Core 系统
5. ...以此类推

This is the order the team should write GDDs in.
这是团队编写 GDD 的顺序。

---

## Phase 5: Create Systems Index (Write)
## 阶段 5：创建系统索引（写入）

### Step 5a: Draft the Document
### 步骤 5a：起草文档

Using the template at `.claude/docs/templates/systems-index.md`, populate the
systems index with all data from Phases 2-4:
- Fill the enumeration table
- Fill the dependency map
- Fill the recommended design order
- Fill the high-risk systems
- Fill progress tracker (all systems "Not Started" initially, unless GDDs already exist)
使用 `.claude/docs/templates/systems-index.md` 中的模板，用
阶段 2-4 的所有数据填充系统索引：
- 填充枚举表
- 填充依赖映射
- 填充推荐设计顺序
- 填充高风险系统
- 填充进度跟踪器（所有系统初始为 "Not Started"，除非 GDD 已存在）

### Step 5b: Approval
### 步骤 5b：批准

Present a summary of the document:
- Total systems count by category
- MVP system count
- First 3 systems in the design order
- Any high-risk items
呈现文档摘要：
- 按类别的总系统计数
- MVP 系统计数
- 设计顺序中的前 3 个系统
- 任何高风险项

Ask: "May I write the systems index to `design/gdd/systems-index.md`?"
询问："May I write the systems index to `design/gdd/systems-index.md`?"

Wait for approval. Write the file only after "yes."
等待批准。仅在 "yes" 之后写入文件。

**Review mode check** — apply before spawning CD-SYSTEMS:
- `solo` → skip. Note: "CD-SYSTEMS skipped — Solo mode." Proceed to Phase 7 next steps.
- `lean` → skip (not a PHASE-GATE). Note: "CD-SYSTEMS skipped — Lean mode." Proceed to Phase 7 next steps.
- `full` → spawn as normal.
**评审模式检查** — 在生成 CD-SYSTEMS 之前应用：
- `solo` → 跳过。注明："CD-SYSTEMS skipped — Solo mode." 继续阶段 7 后续步骤。
- `lean` → 跳过（不是 PHASE-GATE）。注明："CD-SYSTEMS skipped — Lean mode." 继续阶段 7 后续步骤。
- `full` → 正常生成。

**After the systems index is written, spawn `creative-director` via Task using gate CD-SYSTEMS (`.claude/docs/director-gates.md`).**
**在系统索引写入之后，通过 Task 使用门禁 CD-SYSTEMS（`.claude/docs/director-gates.md`）生成 `creative-director`。**

Pass: systems index path, game pillars and core fantasy (from `design/gdd/game-concept.md`), MVP priority tier system list.
传递：系统索引路径、游戏支柱和核心幻想（来自 `design/gdd/game-concept.md`）、MVP 优先级层系统列表。

Present the assessment. If REJECT, revise the system set with the user before GDD authoring begins. If CONCERNS, record them in the systems index as a `> **Creative Director Note**` at the top of the relevant tier section.
呈现评估。如果 REJECT，在 GDD 编写开始之前与用户一起修订系统集。如果 CONCERNS，将它们记录在系统索引中相关层章节顶部的 `> **Creative Director Note**` 中。

### Step 5c: Update Session State
### 步骤 5c：更新会话状态

After writing, create `production/session-state/active.md` if it does not exist, then update it with:
- Task: Systems decomposition
- Status: Systems index created
- File: design/gdd/systems-index.md
- Next: Design individual system GDDs
写入后，如果 `production/session-state/active.md` 不存在则创建它，然后用以下内容更新它：
- Task: Systems decomposition
- Status: Systems index created
- File: design/gdd/systems-index.md
- Next: Design individual system GDDs

**Verdict: COMPLETE** — systems index written to `design/gdd/systems-index.md`.
If the user declined: **Verdict: BLOCKED** — user did not approve the write.
**Verdict: COMPLETE** — 系统索引已写入 `design/gdd/systems-index.md`。
如果用户拒绝：**Verdict: BLOCKED** — 用户未批准写入。

---

## Phase 6: Design Individual Systems (Handoff to /design-system)
## 阶段 6：设计单个系统（移交至 /design-system）

This phase is entered when:
- The user says "yes" to designing systems after creating the index
- The user invokes `/map-systems [system-name]`
- The user invokes `/map-systems next`
此阶段在以下情况进入：
- 用户在创建索引之后对设计系统说 "yes"
- 用户调用 `/map-systems [system-name]`
- 用户调用 `/map-systems next`

### Step 6a: Select the System
### 步骤 6a：选择系统

- If a system name was provided, find it in the systems index
- If `next` was used, pick the highest-priority undesigned system (by design order)
- If the user just finished the index, ask:
  "Would you like to start designing individual systems now? The first system in
  the design order is [name]. Or would you prefer to stop here and come back later?"
- 如果提供了系统名称，在系统索引中找到它
- 如果使用了 `next`，挑选最高优先级的未设计系统（按设计顺序）
- 如果用户刚刚完成索引，询问：
  "Would you like to start designing individual systems now? The first system in
  the design order is [name]. Or would you prefer to stop here and come back later?"

Use `AskUserQuestion` for: "Start designing [system-name] now, pick a different
system, or stop here?"
使用 `AskUserQuestion` 询问："Start designing [system-name] now, pick a different
system, or stop here?"

### Step 6b: Hand Off to /design-system
### 步骤 6b：移交至 /design-system

Once a system is selected, invoke the `/design-system [system-name]` skill.
一旦选择了系统，调用 `/design-system [system-name]` 技能。

The `/design-system` skill handles the full GDD authoring process:
- Gathers context from game concept, systems index, and dependency GDDs
- Creates a file skeleton immediately
- Walks through all 8 required sections one at a time (collaborative, incremental)
- Cross-references existing docs to prevent contradictions
- Routes to specialist agents for domain expertise
- Writes each section to file as soon as it's approved
- Runs `/design-review` when complete
- Updates the systems index
`/design-system` 技能处理完整的 GDD 编写流程：
- 从游戏概念、系统索引和依赖 GDD 收集上下文
- 立即创建文件骨架
- 一次一个地走完所有 8 个必需章节（协作式、增量）
- 交叉引用现有文档以防止矛盾
- 路由到专家智能体获取领域专业知识
- 每个章节一经批准就写入文件
- 完成时运行 `/design-review`
- 更新系统索引

**Do not duplicate the /design-system workflow here.** This skill owns the systems
*index*; `/design-system` owns individual system *GDDs*.
**不要在此重复 /design-system 工作流。** 此技能拥有系统
*索引*；`/design-system` 拥有单个系统 *GDD*。

### Step 6c: Loop or Stop
### 步骤 6c：循环或停止

After `/design-system` completes, use `AskUserQuestion`:
- "Continue to the next system ([next system name])?"
- "Pick a different system?"
- "Stop here for this session?"
`/design-system` 完成后，使用 `AskUserQuestion`：
- "Continue to the next system ([next system name])?"
- "Pick a different system?"
- "Stop here for this session?"

If continuing, return to Step 6a.
如果继续，返回步骤 6a。

---

## Phase 7: Suggest Next Steps
## 阶段 7：建议后续步骤

After the systems index is created (or after designing some systems), present next actions using `AskUserQuestion`:
在系统索引创建之后（或设计了一些系统之后），使用 `AskUserQuestion` 呈现后续行动：

- "Systems index is written. What would you like to do next?"
  - [A] Start designing GDDs — run `/design-system [first-system-in-order]`
  - [B] Run `/gate-check systems-design` — triggers the CD-SYSTEMS and TD-SYSTEM-BOUNDARY gates automatically for a formal director sign-off on the system set
  - [C] Stop here for this session
- "Systems index is written. What would you like to do next?"
  - [A] Start designing GDDs — run `/design-system [first-system-in-order]`
  - [B] Run `/gate-check systems-design` — triggers the CD-SYSTEMS and TD-SYSTEM-BOUNDARY gates automatically for a formal director sign-off on the system set
  - [C] Stop here for this session

**The gate-check option ([B]) is worth highlighting**: running `/gate-check systems-design` triggers both the CD-SYSTEMS and TD-SYSTEM-BOUNDARY gates, catching scope issues, missing systems, and boundary problems before they're locked in across many documents. It is optional but recommended for new projects.
**gate-check 选项（[B]）值得强调**：运行 `/gate-check systems-design` 触发 CD-SYSTEMS 和 TD-SYSTEM-BOUNDARY 两个门禁，在它们被锁定到许多文档之前捕获范围问题、缺失系统和边界问题。它是可选的，但对新项目推荐。

After any individual GDD is completed:
- "Run `/design-review design/gdd/[system].md` in a fresh session to validate quality"
- "Run `/gate-check systems-design` when all MVP GDDs are complete"
在任何单个 GDD 完成之后：
- "Run `/design-review design/gdd/[system].md` in a fresh session to validate quality"
- "Run `/gate-check systems-design` when all MVP GDDs are complete"

---

## Collaborative Protocol
## 协作协议

This skill follows the collaborative design principle at every phase:
此技能在每个阶段遵循协作设计原则：

1. **Question -> Options -> Decision -> Draft -> Approval** at every step
2. **AskUserQuestion** at every decision point (Explain -> Capture pattern):
   - Phase 2: "Missing systems? Combine or split?"
   - Phase 3: "Dependency ordering correct?"
   - Phase 4: "Priority assignments match your vision?"
   - Phase 5: "May I write the systems index?"
   - Phase 6: "Start designing, pick different, or stop?" then hand off to `/design-system`
3. **"May I write to [filepath]?"** before every file write
4. **Incremental writing**: Update the systems index after each system is designed
5. **Handoff**: Individual GDD authoring is owned by `/design-system`, which handles
   incremental section writing, cross-referencing, design review, and index updates
6. **Session state updates**: Write to `production/session-state/active.md` after
   each milestone (index created, system designed, priorities changed)
1. **Question -> Options -> Decision -> Draft -> Approval** 在每一步
2. **AskUserQuestion** 在每个决策点（Explain -> Capture 模式）：
   - 阶段 2："Missing systems? Combine or split?"
   - 阶段 3："Dependency ordering correct?"
   - 阶段 4："Priority assignments match your vision?"
   - 阶段 5："May I write the systems index?"
   - 阶段 6："Start designing, pick different, or stop?" 然后移交至 `/design-system`
3. **"May I write to [filepath]?"** 在每次文件写入之前
4. **增量写入**：在每个系统设计之后更新系统索引
5. **移交**：单个 GDD 编写由 `/design-system` 负责，它处理
   增量章节写入、交叉引用、设计评审和索引更新
6. **会话状态更新**：在每个
   里程碑之后写入 `production/session-state/active.md`（索引创建、系统设计、优先级更改）

**Never** auto-generate the full systems list and write it without review.
**Never** start designing a system without user confirmation.
**Always** show the enumeration, dependencies, and priorities for user validation.
**切勿**未经评审就自动生成完整系统列表并写入。
**切勿**在未经用户确认的情况下开始设计系统。
**始终**展示枚举、依赖和优先级以供用户验证。

## Context Window Awareness
## 上下文窗口感知

If context reaches or exceeds 70% at any point, append this notice:
如果上下文在任何点达到或超过 70%，追加此通知：

> **Context is approaching the limit (≥70%).** The systems index is saved to
> `design/gdd/systems-index.md`. Open a fresh Claude Code session to continue
> designing individual GDDs — run `/map-systems next` to pick up where you left off.
> **Context is approaching the limit (≥70%).** The systems index is saved to
> `design/gdd/systems-index.md`. Open a fresh Claude Code session to continue
> designing individual GDDs — run `/map-systems next` to pick up where you left off.

---

## Recommended Next Steps
## 推荐的后续步骤

- Run `/design-system [first-system-in-order]` to author the first GDD (use design order from the index)
- Run `/map-systems next` to always pick the highest-priority undesigned system automatically
- Run `/design-review design/gdd/[system].md` in a fresh session after each GDD is authored
- Run `/gate-check pre-production` when all MVP GDDs are authored and reviewed
- 运行 `/design-system [first-system-in-order]` 编写第一个 GDD（使用索引中的设计顺序）
- 运行 `/map-systems next` 始终自动挑选最高优先级的未设计系统
- 在每个 GDD 编写之后，在新的会话中运行 `/design-review design/gdd/[system].md`
- 当所有 MVP GDD 都已编写并评审之后，运行 `/gate-check pre-production`
