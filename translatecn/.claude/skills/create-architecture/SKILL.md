---
name: create-architecture
description: "Guided, section-by-section authoring of the master architecture document for the game. Reads all GDDs, the systems index, existing ADRs, and the engine reference library to produce a complete architecture blueprint before any code is written. Engine-version-aware: flags knowledge gaps and validates decisions against the pinned engine version."
argument-hint: "[focus-area: full | layers | data-flow | api-boundaries | adr-audit] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, AskUserQuestion, Task
model: sonnet
agent: technical-director
---
---
名称：create-architecture
描述："分章节引导编写游戏的主架构文档。读取所有 GDD、系统索引、现有 ADR 和引擎参考库，在任何代码编写之前生成完整的架构蓝图。具备引擎版本感知：标记知识缺口并针对所固定的引擎版本验证决策。"
argument-hint："[focus-area: full | layers | data-flow | api-boundaries | adr-audit] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Bash, AskUserQuestion, Task
model：sonnet
agent：technical-director
---

# Create Architecture
# 创建架构

This skill produces `docs/architecture/architecture.md` — the master architecture
document that translates all approved GDDs into a concrete technical blueprint.
It sits between design and implementation, and must exist before sprint planning begins.
此技能生成 `docs/architecture/architecture.md` —— 主架构文档，将所有已批准的 GDD 转化为具体的技术蓝图。
它位于设计与实施之间，必须在冲刺规划开始前存在。

**Distinct from `/architecture-decision`**: ADRs record individual point decisions.
This skill creates the whole-system blueprint that gives ADRs their context.
**与 `/architecture-decision` 的区别**：ADR 记录单个点决策。
此技能创建为 ADR 提供上下文的整个系统蓝图。

Resolve the review mode (once, store for all gate spawns this run):
解析评审模式（解析一次，本次运行中所有门禁派生都使用该模式）：
1. If `--review [full|lean|solo]` was passed → use that
1. 如果传入了 `--review [full|lean|solo]` → 使用该值
2. Else read `production/review-mode.txt` → use that value
2. 否则读取 `production/review-mode.txt` → 使用该值
3. Else → default to `lean`
3. 否则 → 默认为 `lean`

See `.claude/docs/director-gates.md` for the full check pattern.
完整的检查模式请参见 `.claude/docs/director-gates.md`。

**Argument modes:**
**参数模式：**
- **No argument / `full`**: Full guided walkthrough — all sections, start to finish
- **无参数 / `full`**：完整引导演练 —— 所有章节，从头到尾
- **`layers`**: Focus on the system layer diagram only
- **`layers`**：仅关注系统层图
- **`data-flow`**: Focus on data flow between modules only
- **`data-flow`**：仅关注模块间的数据流
- **`api-boundaries`**: Focus on API boundary definitions only
- **`api-boundaries`**：仅关注 API 边界定义
- **`adr-audit`**: Audit existing ADRs for engine compatibility gaps only
- **`adr-audit`**：仅审计现有 ADR 的引擎兼容性缺口

---

## Phase 0: Load All Context
## 阶段 0：加载所有上下文

Before anything else, load the full project context in this order:
在做任何事之前，按以下顺序加载完整的项目上下文：

### 0a. Engine Context (Critical)
### 0a. 引擎上下文（关键）

Read the engine reference library completely:
完整读取引擎参考库：

1. `docs/engine-reference/[engine]/VERSION.md`
   → Extract: engine name, version, LLM cutoff, post-cutoff risk levels
1. `docs/engine-reference/[engine]/VERSION.md`
   → 提取：引擎名称、版本、LLM 截止、截止后风险级别
2. `docs/engine-reference/[engine]/breaking-changes.md`
   → Extract: all HIGH and MEDIUM risk changes
2. `docs/engine-reference/[engine]/breaking-changes.md`
   → 提取：所有 HIGH 和 MEDIUM 风险变更
3. `docs/engine-reference/[engine]/deprecated-apis.md`
   → Extract: APIs to avoid
3. `docs/engine-reference/[engine]/deprecated-apis.md`
   → 提取：应避免的 API
4. `docs/engine-reference/[engine]/current-best-practices.md`
   → Extract: post-cutoff best practices that differ from training data
4. `docs/engine-reference/[engine]/current-best-practices.md`
   → 提取：与训练数据不同的截止后最佳实践
5. All files in `docs/engine-reference/[engine]/modules/`
   → Extract: current API patterns per domain
5. `docs/engine-reference/[engine]/modules/` 中的所有文件
   → 提取：各领域的当前 API 模式

If no engine is configured, stop and prompt:
若未配置引擎，停止并提示：
> "No engine is configured. Run `/setup-engine` first. Architecture cannot be
> written without knowing which engine and version you are targeting."
> "未配置引擎。请先运行 `/setup-engine`。不知道目标引擎和版本就无法编写架构。"

### 0b. Design Context + Technical Requirements Extraction
### 0b. 设计上下文 + 技术需求提取

Read all approved design documents and extract technical requirements from each:
读取所有已批准的设计文档并从每个文档提取技术需求：

1. `design/gdd/game-concept.md` — game pillars, genre, core loop
1. `design/gdd/game-concept.md` —— 游戏支柱、类型、核心循环
2. `design/gdd/systems-index.md` — all systems, dependencies, priority tiers
2. `design/gdd/systems-index.md` —— 所有系统、依赖、优先级层
3. `.claude/docs/technical-preferences.md` — naming conventions, performance budgets,
   allowed libraries, forbidden patterns
3. `.claude/docs/technical-preferences.md` —— 命名约定、性能预算、允许的库、禁止模式
4. **Every GDD in `design/gdd/`** — for each, extract technical requirements:
4. **`design/gdd/` 中的每个 GDD** —— 为每个 GDD 提取技术需求：
   - Data structures implied by the game rules
   - 游戏规则暗示的数据结构
   - Performance constraints stated or implied
   - 明示或暗示的性能约束
   - Engine capabilities the system requires
   - 系统所需的引擎能力
   - Cross-system communication patterns (what talks to what, how)
   - 跨系统通信模式（什么与什么通信、如何通信）
   - State that must persist (save/load implications)
   - 必须持久化的状态（存档/读档影响）
   - Threading or timing requirements
   - 线程或时序要求

Build a **Technical Requirements Baseline** — a flat list of all extracted
requirements across all GDDs, numbered `TR-[gdd-slug]-[NNN]`. This is the
complete set of what the architecture must cover. Present it as:
构建**技术需求基线** —— 跨所有 GDD 提取的所有需求的扁平列表，编号为 `TR-[gdd-slug]-[NNN]`。这是架构必须涵盖的完整集合。呈现为：

```
## Technical Requirements Baseline
Extracted from [N] GDDs | [X] total requirements

| Req ID | GDD | System | Requirement | Domain |
|--------|-----|--------|-------------|--------|
| TR-combat-001 | combat.md | Combat | Hitbox detection per-frame | Physics |
| TR-combat-002 | combat.md | Combat | Combo state machine | Core |
| TR-inventory-001 | inventory.md | Inventory | Item persistence | Save/Load |
```
```
## 技术需求基线
从 [N] 个 GDD 提取 | 共 [X] 项需求

| 需求 ID | GDD | 系统 | 需求 | 领域 |
|--------|-----|--------|-------------|--------|
| TR-combat-001 | combat.md | Combat | 每帧命中盒检测 | Physics |
| TR-combat-002 | combat.md | Combat | 连招状态机 | Core |
| TR-inventory-001 | inventory.md | Inventory | 物品持久化 | Save/Load |
```

This baseline feeds into every subsequent phase. No GDD requirement should be
left without an architectural decision to support it by the end of this session.
此基线将馈入每个后续阶段。到本次会话结束时，任何 GDD 需求都不应没有支持它的架构决策。

### 0c. Existing Architecture Decisions
### 0c. 现有架构决策

Read all files in `docs/architecture/` to understand what has already been decided.
List any ADRs found and their domains.
读取 `docs/architecture/` 中的所有文件以了解已决策的内容。
列出找到的所有 ADR 及其领域。

### 0d. Generate Knowledge Gap Inventory
### 0d. 生成知识缺口清单

Before proceeding, display a structured summary:
继续之前，显示结构化摘要：

```
## Engine Knowledge Gap Inventory
Engine: [name + version]
LLM Training Covers: up to approximately [version]
Post-Cutoff Versions: [list]

### HIGH RISK Domains (must verify against engine reference before deciding)
- [Domain]: [Key changes]

### MEDIUM RISK Domains (verify key APIs)
- [Domain]: [Key changes]

### LOW RISK Domains (in training data, likely reliable)
- [Domain]: [no significant post-cutoff changes]

### Systems from GDD that touch HIGH/MEDIUM risk domains:
- [GDD system name] → [domain] → [risk level]
```
```
## 引擎知识缺口清单
引擎：[名称 + 版本]
LLM 训练覆盖：截至约 [版本]
截止后版本：[列表]

### HIGH 风险领域（决策前必须对照引擎参考验证）
- [领域]：[关键变更]

### MEDIUM 风险领域（验证关键 API）
- [领域]：[关键变更]

### LOW 风险领域（在训练数据中，可能可靠）
- [领域]：[无重大截止后变更]

### 触及 HIGH/MEDIUM 风险领域的 GDD 系统：
- [GDD 系统名] → [领域] → [风险级别]
```

Use `AskUserQuestion`:
使用 `AskUserQuestion`：
- Prompt: "One or more engine domains are HIGH RISK — the LLM's knowledge may be unreliable for these areas. Architectural recommendations in these domains should be cross-referenced with the engine docs before being acted on. How would you like to proceed?"
- 提示："一个或多个引擎领域为 HIGH 风险 —— LLM 在这些领域的知识可能不可靠。这些领域的架构建议在执行前应与引擎文档交叉引用。您希望如何进行？"
- Options:
- 选项：
  - `[A] Proceed — flag HIGH RISK domains throughout the output`
  - `[A] 继续 —— 在整个输出中标记 HIGH 风险领域`
  - `[B] Let me check the engine reference first — pause here`
  - `[B] 让我先查看引擎参考 —— 在此暂停`
  - `[C] Show me which domains are HIGH RISK and why`
  - `[C] 向我展示哪些领域是 HIGH 风险以及原因`

---

## Phase 1: System Layer Mapping
## 阶段 1：系统层映射

Map every system from `systems-index.md` into an architecture layer. The standard
game architecture layers are:
将 `systems-index.md` 中的每个系统映射到架构层。标准的游戏架构层为：

```
┌─────────────────────────────────────────────┐
│  PRESENTATION LAYER                         │  ← UI, HUD, menus, VFX, audio
├─────────────────────────────────────────────┤
│  FEATURE LAYER                              │  ← gameplay systems, AI, quests
├─────────────────────────────────────────────┤
│  CORE LAYER                                 │  ← physics, input, combat, movement
├─────────────────────────────────────────────┤
│  FOUNDATION LAYER                           │  ← engine integration, save/load,
│                                             │    scene management, event bus
├─────────────────────────────────────────────┤
│  PLATFORM LAYER                             │  ← OS, hardware, engine API surface
└─────────────────────────────────────────────┘
```
```
┌─────────────────────────────────────────────┐
│  表现层                          │  ← UI、HUD、菜单、VFX、音频
├─────────────────────────────────────────────┤
│  特性层                              │  ← 游戏玩法系统、AI、任务
├─────────────────────────────────────────────┤
│  核心层                                 │  ← 物理、输入、战斗、移动
├─────────────────────────────────────────────┤
│  基础层                           │  ← 引擎集成、存档/读档、
│                                             │    场景管理、事件总线
├─────────────────────────────────────────────┤
│  平台层                             │  ← 操作系统、硬件、引擎 API 表面
└─────────────────────────────────────────────┘
```

For each GDD system, ask:
对每个 GDD 系统，询问：
- Which layer does it belong to?
- 它属于哪一层？
- What are its module boundaries?
- 它的模块边界是什么？
- What does it own exclusively? (data, state, behaviour)
- 它独占什么？（数据、状态、行为）

Present the proposed layer assignment and ask for approval before proceeding to
the next section. Write the approved layer map immediately to the skeleton file.
呈现提议的层分配，并在进入下一章节前请求批准。将批准的层映射立即写入骨架文件。

**Engine awareness check**: For each system assigned to the Core and Foundation
layers, flag if it touches a HIGH or MEDIUM risk engine domain. Show the relevant
engine reference excerpt inline.
**引擎感知检查**：对于分配给核心层和基础层的每个系统，标记它是否触及 HIGH 或 MEDIUM 风险的引擎领域。内联显示相关引擎参考摘录。

---

## Phase 2: Module Ownership Map
## 阶段 2：模块所有权映射

For each module defined in Phase 1, define ownership:
对阶段 1 中定义的每个模块，定义所有权：

- **Owns**: what data and state this module is solely responsible for
- **拥有**：此模块单独负责的数据和状态
- **Exposes**: what other modules may read or call
- **暴露**：其他模块可读取或调用的内容
- **Consumes**: what it reads from other modules
- **消费**：它从其他模块读取的内容
- **Engine APIs used**: which specific engine classes/nodes/signals this module
  calls directly (with version and risk level noted)
- **使用的引擎 API**：此模块直接调用哪些具体的引擎类/节点/信号（注明版本和风险级别）

Format as a table per layer, then as an ASCII dependency diagram.
格式为每层一个表格，然后是 ASCII 依赖图。

**Engine awareness check**: For every engine API listed, verify against the
relevant module reference doc. If an API is post-cutoff, flag it:
**引擎感知检查**：对列出的每个引擎 API，对照相关模块参考文档进行验证。若 API 为截止后，标记它：

```
⚠️  [ClassName.method()] — Godot 4.6 (post-cutoff, HIGH risk)
    Verified against: docs/engine-reference/godot/modules/[domain].md
    Behaviour confirmed: [yes / NEEDS VERIFICATION]
```
```
⚠️  [ClassName.method()] — Godot 4.6（截止后，HIGH 风险）
    对照验证：docs/engine-reference/godot/modules/[domain].md
    行为已确认：[是 / 需要验证]
```

Get user approval on the ownership map before writing.
写入前获取用户对所有权映射的批准。

---

## Phase 3: Data Flow
## 阶段 3：数据流

Define how data moves between modules during key game scenarios. Cover at minimum:
定义数据在关键游戏场景中如何在模块间流动。至少涵盖：

1. **Frame update path**: Input → Core systems → State → Rendering
1. **帧更新路径**：输入 → 核心系统 → 状态 → 渲染
2. **Event/signal path**: How systems communicate without tight coupling
2. **事件/信号路径**：系统如何在不紧密耦合的情况下通信
3. **Save/load path**: What state is serialised, which module owns serialisation
3. **存档/读档路径**：什么状态被序列化，哪个模块拥有序列化
4. **Initialisation order**: Which modules must boot before others
4. **初始化顺序**：哪些模块必须在其他模块之前启动

Use ASCII sequence diagrams where helpful. For each data flow:
在有帮助处使用 ASCII 时序图。对每个数据流：
- Name the data being transferred
- 命名正在传输的数据
- Identify the producer and consumer
- 标识生产者和消费者
- State whether this is synchronous call, signal/event, or shared state
- 说明这是同步调用、信号/事件还是共享状态
- Flag any data flows that cross thread boundaries
- 标记任何跨线程边界的数据流

Get user approval per scenario before writing.
写入前获取每个场景的用户批准。

---

## Phase 4: API Boundaries
## 阶段 4：API 边界

Define the public contracts between modules. For each boundary:
定义模块间的公共契约。对每个边界：
- What is the interface a module exposes to the rest of the system?
- 模块向系统其余部分暴露的接口是什么？
- What are the entry points (functions/signals/properties)?
- 入口点是什么（函数/信号/属性）？
- What invariants must callers respect?
- 调用方必须遵守哪些不变量？
- What must the module guarantee to callers?
- 模块必须向调用方保证什么？

Write in pseudocode or the project's actual language (from technical preferences).
These become the contracts programmers implement against.
用伪代码或项目的实际语言编写（来自技术偏好）。
这些将成为程序员实施的契约。

**Engine awareness check**: If any interface uses engine-specific types (e.g.
`Node`, `Resource`, `Signal` in Godot), flag the version and verify the type
exists and has not changed signature in the target engine version.
**引擎感知检查**：若任何接口使用引擎特定类型（例如 Godot 中的 `Node`、`Resource`、`Signal`），标记版本并验证该类型存在且在目标引擎版本中未改变签名。

---

## Phase 5: ADR Audit + Traceability Check
## 阶段 5：ADR 审计 + 可追溯性检查

Review all existing ADRs from Phase 0c against both the architecture built in
Phases 1-4 AND the Technical Requirements Baseline from Phase 0b.
将阶段 0c 中的所有现有 ADR 与阶段 1-4 中构建的架构以及阶段 0b 的技术需求基线进行对比评审。

### ADR Quality Check
### ADR 质量检查

For each ADR:
对每个 ADR：
- [ ] Does it have an Engine Compatibility section?
- [ ] 它是否有引擎兼容性章节？
- [ ] Is the engine version recorded?
- [ ] 是否记录了引擎版本？
- [ ] Are post-cutoff APIs flagged?
- [ ] 是否标记了截止后 API？
- [ ] Does it have a "GDD Requirements Addressed" section?
- [ ] 它是否有 "GDD Requirements Addressed" 章节？
- [ ] Does it conflict with the layer/ownership decisions made in this session?
- [ ] 它是否与本次会话中做出的层/所有权决策冲突？
- [ ] Is it still valid for the pinned engine version?
- [ ] 它对所固定的引擎版本是否仍然有效？

| ADR | Engine Compat | Version | GDD Linkage | Conflicts | Valid |
|-----|--------------|---------|-------------|-----------|-------|
| ADR-0001: [title] | ✅/❌ | ✅/❌ | ✅/❌ | None/[conflict] | ✅/⚠️ |
| ADR | 引擎兼容 | 版本 | GDD 关联 | 冲突 | 有效 |

### Traceability Coverage Check
### 可追溯性覆盖检查

Map every requirement from the Technical Requirements Baseline to existing ADRs.
For each requirement, check if any ADR's "GDD Requirements Addressed" section
or decision text covers it:
将技术需求基线中的每个需求映射到现有 ADR。
对每个需求，检查是否有任何 ADR 的 "GDD Requirements Addressed" 章节或决策文本涵盖它：

| Req ID | Requirement | ADR Coverage | Status |
|--------|-------------|--------------|--------|
| TR-combat-001 | Hitbox detection per-frame | ADR-0003 | ✅ |
| TR-combat-002 | Combo state machine | — | ❌ GAP |
| 需求 ID | 需求 | ADR 覆盖 | 状态 |
| TR-combat-001 | 每帧命中盒检测 | ADR-0003 | ✅ |
| TR-combat-002 | 连招状态机 | — | ❌ 缺口 |

Count: X covered, Y gaps. For each gap, it becomes a **Required New ADR**.
计数：X 已覆盖，Y 个缺口。每个缺口成为**必需的新 ADR**。

### Required New ADRs
### 必需的新 ADR

List all decisions made during this architecture session (Phases 1-4) that do
not yet have a corresponding ADR, PLUS all uncovered Technical Requirements.
Group by layer — Foundation first:
列出本次架构会话（阶段 1-4）中做出但尚无对应 ADR 的所有决策，加上所有未覆盖的技术需求。
按层分组 —— 基础层优先：

**Foundation Layer (must create before any coding):**
**基础层（任何编码前必须创建）：**
- `/architecture-decision [title]` → covers: TR-[id], TR-[id]
- `/architecture-decision [title]` → 涵盖：TR-[id], TR-[id]

**Core Layer:**
**核心层：**
- `/architecture-decision [title]` → covers: TR-[id]
- `/architecture-decision [title]` → 涵盖：TR-[id]

---

## Phase 6: Missing ADR List
## 阶段 6：缺失 ADR 列表

Based on the full architecture, produce a complete list of ADRs that should exist
but don't yet. Group by priority:
基于完整架构，生成应存在但尚不存在的完整 ADR 列表。按优先级分组：

**Must have before coding starts (Foundation & Core decisions):**
**编码开始前必须有（基础和核心决策）：**
- [e.g. "Scene management and scene loading strategy"]
- [例如"场景管理和场景加载策略"]
- [e.g. "Event bus vs direct signal architecture"]
- [例如"事件总线 vs 直接信号架构"]

**Should have before the relevant system is built:**
**构建相关系统前应有：**
- [e.g. "Inventory serialisation format"]
- [例如"物品栏序列化格式"]

**Can defer to implementation:**
**可推迟到实施时：**
- [e.g. "Specific shader technique for water"]
- [例如"水的特定着色器技术"]

---

## Phase 7: Write the Master Architecture Document
## 阶段 7：编写主架构文档

Once all sections are approved, write the complete document to
`docs/architecture/architecture.md`.
所有章节批准后，将完整文档写入 `docs/architecture/architecture.md`。

Display a one-paragraph summary of what the document will contain (layers, modules, data flows, ADR gaps). Then use `AskUserQuestion`:
显示一段关于文档将包含什么（层、模块、数据流、ADR 缺口）的摘要。然后使用 `AskUserQuestion`：
- "All sections approved. May I write the master architecture document?"
- "所有章节已批准。我可以编写主架构文档吗？"
  - [A] Yes — write to `docs/architecture/architecture.md` now
  - [A] 是 —— 现在写入 `docs/architecture/architecture.md`
  - [B] Show me the full draft inline first, then ask again
  - [B] 先内联显示完整草稿，然后再询问
  - [C] Not yet — I have more changes to discuss
  - [C] 暂不 —— 我还有更多变更要讨论

The document structure:
文档结构：

```markdown
# [Game Name] — Master Architecture
```
```markdown
# [游戏名] —— 主架构
```

```markdown
## Document Status
- Version: [N]
- Last Updated: [date]
- Engine: [name + version]
- GDDs Covered: [list]
- ADRs Referenced: [list]
```
```markdown
## 文档状态
- 版本：[N]
- 最后更新：[日期]
- 引擎：[名称 + 版本]
- 覆盖的 GDD：[列表]
- 引用的 ADR：[列表]
```

```markdown
## Engine Knowledge Gap Summary
[Condensed from Phase 0d inventory — HIGH/MEDIUM risk domains and their implications]
```
```markdown
## 引擎知识缺口摘要
[从阶段 0d 清单精简 —— HIGH/MEDIUM 风险领域及其影响]
```

```markdown
## System Layer Map
[From Phase 1]
```
```markdown
## 系统层映射
[来自阶段 1]
```

```markdown
## Module Ownership
[From Phase 2]
```
```markdown
## 模块所有权
[来自阶段 2]
```

```markdown
## Data Flow
[From Phase 3]
```
```markdown
## 数据流
[来自阶段 3]
```

```markdown
## API Boundaries
[From Phase 4]
```
```markdown
## API 边界
[来自阶段 4]
```

```markdown
## ADR Audit
[From Phase 5]
```
```markdown
## ADR 审计
[来自阶段 5]
```

```markdown
## Required ADRs
[From Phase 6]
```
```markdown
## 必需的 ADR
[来自阶段 6]
```

```markdown
## Architecture Principles
[3-5 key principles that govern all technical decisions for this project,
derived from the game concept, GDDs, and technical preferences]
```
```markdown
## 架构原则
[3-5 项治理本项目所有技术决策的关键原则，
源自游戏概念、GDD 和技术偏好]
```

```markdown
## Open Questions
[Decisions deferred — must be resolved before the relevant layer is built]
```
```markdown
## 待解决问题
[推迟的决策 —— 必须在构建相关层之前解决]
```

---

## Phase 7b: Technical Director Sign-Off + Lead Programmer Feasibility Review
## 阶段 7b：技术总监签字 + 首席程序员可行性评审

After writing the master architecture document, perform an explicit sign-off before handoff.
编写主架构文档后，在交接前执行明确的签字。

**Step 1 — Technical Director self-review** (this skill runs as technical-director):
**步骤 1 —— 技术总监自评审**（此技能以 technical-director 运行）：

Apply gate **TD-ARCHITECTURE** (`.claude/docs/director-gates.md`) as a self-review. Check all four criteria from that gate definition against the completed document.
应用门禁 **TD-ARCHITECTURE**（`.claude/docs/director-gates.md`）作为自评审。对照该门禁定义中的所有四项标准检查已完成文档。

**Review mode check** — apply before spawning LP-FEASIBILITY:
**评审模式检查** —— 在派生 LP-FEASIBILITY 前应用：
- `solo` → skip. Note: "LP-FEASIBILITY skipped — Solo mode." Proceed to Phase 8 handoff.
- `solo` → 跳过。备注："LP-FEASIBILITY 已跳过 —— Solo 模式。" 进入阶段 8 交接。
- `lean` → skip (not a PHASE-GATE). Note: "LP-FEASIBILITY skipped — Lean mode." Proceed to Phase 8 handoff.
- `lean` → 跳过（非 PHASE-GATE）。备注："LP-FEASIBILITY 已跳过 —— Lean 模式。" 进入阶段 8 交接。
- `full` → spawn as normal.
- `full` → 正常派生。

**Step 2 — Spawn `lead-programmer` via Task using gate LP-FEASIBILITY (`.claude/docs/director-gates.md`):**
**步骤 2 —— 通过 Task 使用门禁 LP-FEASIBILITY（`.claude/docs/director-gates.md`）派生 `lead-programmer`：**

Pass: architecture document path, technical requirements baseline summary, ADR list.
传递：架构文档路径、技术需求基线摘要、ADR 列表。

**Step 3 — Present both assessments to the user:**
**步骤 3 —— 向用户呈现两项评估：**

Show the Technical Director assessment and Lead Programmer verdict side by side.
并排显示技术总监评估和首席程序员裁定。

Use `AskUserQuestion` — "Technical Director and Lead Programmer have reviewed the architecture. How would you like to proceed?"
使用 `AskUserQuestion` —— "技术总监和首席程序员已评审架构。您希望如何进行？"
Options: `Accept — proceed to handoff` / `Revise flagged items first` / `Discuss specific concerns`
选项：`接受 —— 进入交接` / `先修订标记项` / `讨论具体疑虑`

**Step 4 — Record sign-off in the architecture document:**
**步骤 4 —— 在架构文档中记录签字：**

Update the Document Status section:
更新文档状态章节：
```
- Technical Director Sign-Off: [date] — APPROVED / APPROVED WITH CONDITIONS
- Lead Programmer Feasibility: FEASIBLE / CONCERNS ACCEPTED / REVISED
```
```
- 技术总监签字：[日期] —— APPROVED / APPROVED WITH CONDITIONS
- 首席程序员可行性：FEASIBLE / CONCERNS ACCEPTED / REVISED
```

Show the proposed Document Status block inline, then use `AskUserQuestion`:
内联显示提议的文档状态块，然后使用 `AskUserQuestion`：
- "May I update the Document Status section with the sign-off results?"
- "我可以用签字结果更新文档状态章节吗？"
  - [A] Yes — apply to `docs/architecture/architecture.md`
  - [A] 是 —— 应用到 `docs/architecture/architecture.md`
  - [B] Not yet — I want to revisit the concerns first
  - [B] 暂不 —— 我想先重新审视疑虑

---

## Phase 8: Handoff
## 阶段 8：交接

**Step 1 — Update session state**: Write a summary to `production/session-state/active.md` covering: artifact written, TD/LP sign-off verdicts, any blockers, required ADRs remaining, and next step.
**步骤 1 —— 更新会话状态**：将摘要写入 `production/session-state/active.md`，涵盖：已写产物、TD/LP 签字裁定、任何阻碍、剩余必需 ADR 和下一步。

**Step 2 — Output the handoff** using exactly this template (no freeform prose, no rephrasing of section titles):
**步骤 2 —— 输出交接**，严格使用此模板（无自由格式散文，不重述章节标题）：

---

## Architecture Complete
## 架构完成

`docs/architecture/architecture.md` v1.0 — [TD verdict: APPROVED / APPROVED WITH CONCERNS / CONCERNS]. [One sentence on what the architecture covers.]
`docs/architecture/architecture.md` v1.0 —— [TD 裁定：APPROVED / APPROVED WITH CONCERNS / CONCERNS]。[一句话说明架构涵盖内容。]

---

## Run These ADRs Next
## 接下来运行这些 ADR

**1. `/architecture-decision "[Title]"` → ADR-[XXXX]**
[One sentence: what it defines and what it unblocks.]
[一句话：它定义什么以及解锁什么。]

**2. `/architecture-decision "[Title]"` → ADR-[XXXX]**
[One sentence.]
[一句话。]

**3. `/architecture-decision "[Title]"` → ADR-[XXXX]**
[One sentence.]
[一句话。]

List top 3 from Phase 6 in priority order. If fewer than 3 remain, list only what's outstanding.
按优先级顺序列出阶段 6 的前 3 项。若剩余少于 3 项，仅列出未完成项。

---

## Gate-Check Readiness
## 门禁检查就绪状态

> **Required before `/gate-check [stage]`:**
> - [ ] Accept ADRs: [list Proposed ADR IDs that must be Accepted]
> - [ ] Write ADRs: [list ADR IDs that must still be written]
> - [ ] Run `/test-setup` — scaffolds `tests/unit/`, `tests/integration/`, CI workflow, and an example test file
> - [ ] Run `/ux-design` — creates `design/ux/interaction-patterns.md` and `design/accessibility-requirements.md`
>
> Run `/gate-check [stage]` when all boxes are checked.
> **运行 `/gate-check [stage]` 前必需：**
> - [ ] Accept ADR：[必须 Accepted 的 Proposed ADR ID 列表]
> - [ ] 编写 ADR：[仍需编写的 ADR ID 列表]
> - [ ] 运行 `/test-setup` —— 脚手架 `tests/unit/`、`tests/integration/`、CI 工作流和一个示例测试文件
> - [ ] 运行 `/ux-design` —— 创建 `design/ux/interaction-patterns.md` 和 `design/accessibility-requirements.md`
>
> 当所有复选框勾选后运行 `/gate-check [stage]`。

If nothing is blocking, write instead:
若无任何阻碍，改为写入：
> No blockers — run `/gate-check [stage]` now.
> 无阻碍 —— 现在运行 `/gate-check [stage]`。

---

## Open Questions to Watch
## 待观察的开放问题

| ID | Summary | Priority | Resolution Path |
|----|---------|----------|-----------------|
| QQ-XX | [short description] | High / Medium / Low | [ADR or system that resolves it] |
| ID | 摘要 | 优先级 | 解决路径 |
| QQ-XX | [简短描述] | High / Medium / Low | [解决它的 ADR 或系统] |

Omit this section entirely if there are no open QQs.
若无开放 QQ，完全省略此章节。

---

(End of handoff. Do not add trailing commentary after the closing rule.)
（交接结束。不要在结束规则后添加尾部评论。）

---

## Collaborative Protocol
## 协作协议

This skill follows the collaborative design principle at every phase:
此技能在每个阶段都遵循协作设计原则：

1. **Load context silently** — do not narrate file reads
1. **静默加载上下文** —— 不要叙述文件读取
2. **Present findings** — show the knowledge gap inventory and layer proposals
2. **呈现发现** —— 显示知识缺口清单和层提议
3. **Ask before deciding** — present options for each architectural choice
3. **决策前询问** —— 为每个架构选择呈现选项
4. **Draft before approval** — show the content inline before asking to write it.
   Never ask approval for a section the user has not yet seen.
4. **批准前起草** —— 在请求写入前内联显示内容。
   绝不请求用户批准其尚未看到的章节。
5. **Use `AskUserQuestion` for write approvals** — plain text "May I?" is not
   sufficient. Use the structured tool with labeled options [A]/[B]/[C] (write now /
   show full draft first / not yet). For multi-file changesets, list every file
   and what changes, then ask once grouped — not separate plain-text asks per file.
5. **使用 `AskUserQuestion` 进行写入批准** —— 纯文本"我可以吗？"不
   足够。使用带标记选项 [A]/[B]/[C]（现在写入 / 先显示完整草稿 / 暂不）的结构化工具。对于多文件变更集，列出每个文件
   及其变更，然后分组询问一次 —— 而非每个文件单独纯文本询问。
6. **Incremental writing** — write each approved section immediately; do not
   accumulate everything and write at the end. This survives session crashes.
6. **增量写入** —— 立即写入每个已批准章节；不要
   积累所有内容到最后写入。这能在会话崩溃时保留。

Never make a binding architectural decision without user input. If the user is
unsure, present 2-4 options with pros/cons before asking them to decide.
未经用户输入，绝不做出有约束力的架构决策。若用户不确定，在请求决策前呈现 2-4 个带优缺点的选项。

---

## Recommended Next Steps
## 推荐的后续步骤

- Run `/architecture-decision [title]` for each required ADR listed in Phase 6 — Foundation layer ADRs first
- 为阶段 6 中列出的每个必需 ADR 运行 `/architecture-decision [title]` —— 基础层 ADR 优先
- Run `/architecture-review` — bootstraps the Requirements Traceability Matrix and TR registry from the ADRs just written. Required before the Pre-Production gate.
- 运行 `/architecture-review` —— 从刚编写的 ADR 引导需求可追溯性矩阵和 TR 注册表。在预制作门禁前必需。
- Run `/test-setup` to scaffold `tests/unit/`, `tests/integration/`, CI workflow, and an example test (required for gate-check)
- 运行 `/test-setup` 脚手架 `tests/unit/`、`tests/integration/`、CI 工作流和一个示例测试（门禁检查必需）
- Run `/ux-design` to initialize `design/ux/interaction-patterns.md` and `design/accessibility-requirements.md` (required for gate-check)
- 运行 `/ux-design` 初始化 `design/ux/interaction-patterns.md` 和 `design/accessibility-requirements.md`（门禁检查必需）
- Run `/create-control-manifest` once the required ADRs are written to produce the layer rules manifest
- 编写必需 ADR 后运行 `/create-control-manifest` 生成层规则清单
- Run `/gate-check pre-production` when all required ADRs, `/test-setup`, and `/ux-design` are complete
- 当所有必需 ADR、`/test-setup` 和 `/ux-design` 完成后运行 `/gate-check pre-production`
