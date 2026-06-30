---
name: design-system
description: "Guided, section-by-section GDD authoring for a single game system. Gathers context from existing docs, walks through each required section collaboratively, cross-references dependencies, and writes incrementally to file."
argument-hint: "<system-name> [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion, TodoWrite
model: sonnet
---
---
名称：design-system
描述："为单个游戏系统进行引导式、逐段落的 GDD 编写。从现有文档收集上下文，协作走查每个必需段落，交叉引用依赖，并增量写入文件。"
argument-hint："<system-name> [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Task, AskUserQuestion, TodoWrite
model：sonnet
---

When this skill is invoked:
当此技能被调用时：

## 1. Parse Arguments & Validate
## 1. 解析参数并验证

Resolve the review mode (once, store for all gate spawns this run):
解析评审模式（仅一次，为本轮所有门禁启动存储）：
1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`
1. 若传入了 `--review [full|lean|solo]` → 使用该值
2. 否则读取 `production/review-mode.txt` → 使用该值
3. 否则 → 默认为 `lean`

See `.claude/docs/director-gates.md` for the full check pattern.
完整检查模式见 `.claude/docs/director-gates.md`。

A system name or retrofit path is **required**. If missing:
系统名或 retrofit 路径是**必需的**。若缺失：

1. Check if `design/gdd/systems-index.md` exists.
2. If it exists: read it, find the highest-priority system with status "Not Started" or equivalent, and use `AskUserQuestion`:
   - Prompt: "The next system in your design order is **[system-name]** ([priority] | [layer]). Start designing it?"
   - Options: `[A] Yes — design [system-name]` / `[B] Pick a different system` / `[C] Stop here`
   - If [A]: proceed with that system name. If [B]: ask which system to design (plain text). If [C]: exit.
3. If no systems index exists, fail with:
   > "Usage: `/design-system <system-name>` — e.g., `/design-system movement`
   > Or to fill gaps in an existing GDD: `/design-system retrofit design/gdd/[system-name].md`
   > No systems index found. Run `/map-systems` first to map your systems and get the design order."
1. 检查 `design/gdd/systems-index.md` 是否存在。
2. 若存在：读取它，找出状态为 "Not Started" 或等价且优先级最高的系统，使用
   `AskUserQuestion`：
   - 提示："The next system in your design order is **[system-name]** ([priority] | [layer]). Start designing it?"
   - 选项：`[A] Yes — design [system-name]` / `[B] Pick a different system` / `[C] Stop here`
   - 若 [A]：使用该系统名继续。若 [B]：询问要设计哪个系统（纯文本）。若 [C]：退出。
3. 若不存在 systems index，失败：
   > "Usage: `/design-system <system-name>` — e.g., `/design-system movement`
   > Or to fill gaps in an existing GDD: `/design-system retrofit design/gdd/[system-name].md`
   > No systems index found. Run `/map-systems` first to map your systems and get the design order."

**Detect retrofit mode:**
If the argument starts with `retrofit` or the argument is a file path to an
existing `.md` file in `design/gdd/`, enter **retrofit mode**:
**检测 retrofit 模式：**
若参数以 `retrofit` 开头或参数是 `design/gdd/` 中已存在的 `.md` 文件路径，
进入 **retrofit 模式**：

1. Read the existing GDD file.
2. Identify which of the 8 required sections are present (scan for section headings).
   Required sections: Overview, Player Fantasy, Detailed Design/Rules, Formulas,
   Edge Cases, Dependencies, Tuning Knobs, Acceptance Criteria.
3. Identify which sections contain only placeholder text (`[To be designed]` or
   equivalent — blank, a single line, or obviously incomplete).
4. Present to the user before doing anything:
   ```
   ## Retrofit: [System Name]
   File: design/gdd/[filename].md

   Sections already written (will not be touched):
   ✓ [section name]
   ✓ [section name]

   Missing or incomplete sections (will be authored):
   ✗ [section name] — missing
   ✗ [section name] — placeholder only
   ```
5. Ask: "Shall I fill the [N] missing sections? I will not modify any existing content."
6. If yes: proceed to **Phase 2 (Gather Context)** as normal, but in **Phase 3**
   skip creating the skeleton (file already exists) and in **Phase 4** skip
   sections that are already complete. Only run the section cycle for missing/
   incomplete sections.
7. **Never overwrite existing section content.** Use Edit tool to replace only
   `[To be designed]` placeholders or empty section bodies.
1. 读取现有 GDD 文件。
2. 识别 8 个必需段落中哪些已存在（扫描段落标题）。
   必需段落：Overview、Player Fantasy、Detailed Design/Rules、Formulas、
   Edge Cases、Dependencies、Tuning Knobs、Acceptance Criteria。
3. 识别哪些段落只含占位文本（`[To be designed]` 或等价物 —— 空白、单行或明显
   不完整）。
4. 在做任何事之前向用户展示：
   ```
   ## Retrofit: [System Name]
   File: design/gdd/[filename].md

   Sections already written (will not be touched):
   ✓ [section name]
   ✓ [section name]

   Missing or incomplete sections (will be authored):
   ✗ [section name] — missing
   ✗ [section name] — placeholder only
   ```
5. 询问："Shall I fill the [N] missing sections? I will not modify any existing content."
6. 若是：如常继续 **Phase 2 (Gather Context)**，但在 **Phase 3** 跳过创建
   骨架（文件已存在），在 **Phase 4** 跳过已完成段落。仅对缺失/不完整段落运行
   段落循环。
7. **绝不覆盖现有段落内容。** 使用 Edit 工具仅替换 `[To be designed]` 占位符
   或空段落正文。

If NOT in retrofit mode, normalize the system name to kebab-case for the
filename (e.g., "combat system" becomes `combat-system`).
若不在 retrofit 模式，将系统名规范化为 kebab-case 作为文件名（如 "combat
system" 变为 `combat-system`）。

---

## 2. Gather Context (Read Phase)
## 2. 收集上下文（读取阶段）

Read all relevant context **before** asking the user anything. This is the skill's
primary advantage over ad-hoc design — it arrives informed.
在向用户询问任何内容之前**先**读取所有相关上下文。这是本技能相对临时设计的
主要优势 —— 它带着信息到达。

### 2a: Required Reads
### 2a：必需读取

- **Game concept**: Read `design/gdd/game-concept.md` — fail if missing:
  > "No game concept found. Run `/brainstorm` first."
- **Systems index**: Read `design/gdd/systems-index.md` — fail if missing:
  > "No systems index found. Run `/map-systems` first to map your systems."
- **Target system**: Find the system in the index. If not listed, warn:
  > "[system-name] is not in the systems index. Would you like to add it, or
  > design it as an off-index system?"
- **Entity registry**: Read `design/registry/entities.yaml` if it exists.
  Extract all entries referenced by or relevant to this system (grep
  `referenced_by.*[system-name]` and `source.*[system-name]`). Hold these
  in context as **known facts** — values that other GDDs have already
  established and this GDD must not contradict.
- **Reflexion log**: Read `docs/consistency-failures.md` if it exists.
  Extract entries whose Domain matches this system's category. These are
  recurring conflict patterns — present them under "Past failure patterns"
  in the Phase 2d context summary so the user knows where mistakes have
  occurred before in this domain.
- **游戏概念**：读取 `design/gdd/game-concept.md` —— 若缺失则失败：
  > "No game concept found. Run `/brainstorm` first."
- **系统索引**：读取 `design/gdd/systems-index.md` —— 若缺失则失败：
  > "No systems index found. Run `/map-systems` first to map your systems."
- **目标系统**：在索引中查找该系统。若未列出，警告：
  > "[system-name] is not in the systems index. Would you like to add it, or
  > design it as an off-index system?"
- **实体注册表**：若存在则读取 `design/registry/entities.yaml`。
  提取所有引用或相关于本系统的条目（grep `referenced_by.*[system-name]` 与
  `source.*[system-name]`）。将这些作为**已知事实**保留在上下文中 —— 其他
  GDD 已确立的值，本 GDD 不得与之矛盾。
- **反思日志**：若存在则读取 `docs/consistency-failures.md`。
  提取 Domain 匹配本系统类别的条目。这些是反复出现的冲突模式 —— 在阶段 2d
  上下文摘要的 "Past failure patterns" 下展示，让用户知道此领域过去犯过哪些
  错误。

### 2b: Dependency Reads
### 2b：依赖读取

From the systems index, identify:
- **Upstream dependencies**: Systems this one depends on. Read their GDDs if they
  exist (these contain decisions this system must respect).
- **Downstream dependents**: Systems that depend on this one. Read their GDDs if
  they exist (these contain expectations this system must satisfy).
从系统索引中识别：
- **上游依赖**：本系统依赖的系统。若其 GDD 存在则读取（包含本系统必须尊重的
  决策）。
- **下游被依赖者**：依赖本系统的系统。若其 GDD 存在则读取（包含本系统必须
  满足的预期）。

For each dependency GDD that exists, extract and hold in context:
- Key interfaces (what data flows between the systems)
- Formulas that reference this system's outputs
- Edge cases that assume this system's behavior
- Tuning knobs that feed into this system
对于每个存在的依赖 GDD，提取并保留在上下文中：
- 关键接口（系统间流动的数据）
- 引用本系统输出的公式
- 假设本系统行为的边界情况
- 输入本系统的调优旋钮

### 2c: Optional Reads
### 2c：可选读取

- **Game pillars**: Read `design/gdd/game-pillars.md` if it exists
- **Existing GDD**: Read `design/gdd/[system-name].md` if it exists (resume, don't
  restart from scratch)
- **Related GDDs**: Glob `design/gdd/*.md` and read any that are thematically related
  (e.g., if designing a system that overlaps with another in scope, read the related GDD
  even if it's not a formal dependency)
- **游戏支柱**：若存在则读取 `design/gdd/game-pillars.md`
- **现有 GDD**：若存在则读取 `design/gdd/[system-name].md`（续作而非从头开始）
- **相关 GDD**：Glob `design/gdd/*.md` 并读取任何主题相关的（例如，若设计的系统
  在范围上与另一个系统重叠，即使非正式依赖也读取相关 GDD）

### 2d: Present Context Summary
### 2d：展示上下文摘要

Before starting design work, present a brief summary to the user:
在开始设计工作之前，向用户展示简要摘要：

> **Designing: [System Name]**
> - Priority: [from index] | Layer: [from index]
> - Depends on: [list, noting which have GDDs vs. undesigned]
> - Depended on by: [list, noting which have GDDs vs. undesigned]
> - Existing decisions to respect: [key constraints from dependency GDDs]
> - Pillar alignment: [which pillar(s) this system primarily serves]
> - **Known cross-system facts (from registry):**
>   - [entity_name]: [attribute]=[value], [attribute]=[value] (owned by [source GDD])
>   - [item_name]: [attribute]=[value], [attribute]=[value] (owned by [source GDD])
>   - [formula_name]: variables=[list], output=[min–max] (owned by [source GDD])
>   - [constant_name]: [value] [unit] (owned by [source GDD])
>   *(These values are locked — if this GDD needs different values, surface
>   the conflict before writing. Do not silently use different numbers.)*
>
> If no registry entries are relevant: omit the "Known cross-system facts" section.
> **Designing: [System Name]**
> - 优先级：[来自索引] | 层级：[来自索引]
> - 依赖：[列表，标注哪些已有 GDD vs 未设计]
> - 被依赖：[列表，标注哪些已有 GDD vs 未设计]
> - 需尊重的现有决策：[来自依赖 GDD 的关键约束]
> - 支柱对齐：[本系统主要服务哪些支柱]
> - **已知跨系统事实（来自注册表）：**
>   - [entity_name]：[attribute]=[value], [attribute]=[value]（归属 [source GDD]）
>   - [item_name]：[attribute]=[value], [attribute]=[value]（归属 [source GDD]）
>   - [formula_name]：variables=[list], output=[min–max]（归属 [source GDD]）
>   - [constant_name]：[value] [unit]（归属 [source GDD]）
>   *（这些值已锁定 —— 若本 GDD 需要不同值，在写入前暴露冲突。不要默默使用
>   不同数字。）*
>
> 若无相关注册表条目：省略 "Known cross-system facts" 段落。

If any upstream dependencies are undesigned, warn:
> "[dependency] doesn't have a GDD yet. We'll need to make assumptions about
> its interface. Consider designing it first, or we can define the expected
> contract and flag it as provisional."
若任何上游依赖未设计，警告：
> "[dependency] doesn't have a GDD yet. We'll need to make assumptions about
> its interface. Consider designing it first, or we can define the expected
> contract and flag it as provisional."

### 2e: Technical Feasibility Pre-Check
### 2e：技术可行性预检

Before asking the user to begin designing, load engine context and surface any
constraints or knowledge gaps that will shape the design.
在请用户开始设计之前，加载引擎上下文并暴露任何会塑造设计的约束或知识缺口。

**Step 1 — Determine the engine domain for this system:**
Map the system's category (from systems-index.md) to an engine domain:
**步骤 1 —— 确定本系统的引擎领域：**
将系统类别（来自 systems-index.md）映射到引擎领域：

| System Category | Engine Domain |
|----------------|--------------|
| Combat, physics, collision | Physics |
| Rendering, visual effects, shaders | Rendering |
| UI, HUD, menus | UI |
| Audio, sound, music | Audio |
| AI, pathfinding, behavior trees | Navigation / Scripting |
| Animation, IK, rigs | Animation |
| Networking, multiplayer, sync | Networking |
| Input, controls, keybinding | Input |
| Save/load, persistence, data | Core |
| Dialogue, quests, narrative | Scripting |
| 系统类别 | 引擎领域 |
|----------------|--------------|
| 战斗、物理、碰撞 | Physics |
| 渲染、视觉效果、着色器 | Rendering |
| UI、HUD、菜单 | UI |
| 音频、声音、音乐 | Audio |
| AI、寻路、行为树 | Navigation / Scripting |
| 动画、IK、骨骼绑定 | Animation |
| 网络、多人、同步 | Networking |
| 输入、控制、按键绑定 | Input |
| 存档/加载、持久化、数据 | Core |
| 对话、任务、叙事 | Scripting |

**Step 2 — Read engine context (if available):**
- Read `.claude/docs/technical-preferences.md` to identify the engine and version
- If engine is configured, read `docs/engine-reference/[engine]/VERSION.md`
- Read `docs/engine-reference/[engine]/modules/[domain].md` if it exists
- Read `docs/engine-reference/[engine]/breaking-changes.md` for domain-relevant entries
- Glob `docs/architecture/adr-*.md` and read any ADRs whose domain matches
  (check the Engine Compatibility table's "Domain" field)
**步骤 2 —— 读取引擎上下文（若可用）：**
- 读取 `.claude/docs/technical-preferences.md` 确定引擎与版本
- 若引擎已配置，读取 `docs/engine-reference/[engine]/VERSION.md`
- 若存在则读取 `docs/engine-reference/[engine]/modules/[domain].md`
- 读取 `docs/engine-reference/[engine]/breaking-changes.md` 中领域相关条目
- Glob `docs/architecture/adr-*.md` 并读取领域匹配的 ADR（检查 Engine
  Compatibility 表的 "Domain" 字段）

**Step 3 — Present the Feasibility Brief:**
**步骤 3 —— 展示可行性简报：**

If engine reference docs exist, present before starting design:
若引擎参考文档存在，在开始设计之前展示：

```
## Technical Feasibility Brief: [System Name]
Engine: [name + version]
Domain: [domain]

### Known Engine Capabilities (verified for [version])
- [capability relevant to this system]
- [capability 2]

### Engine Constraints That Will Shape This Design
- [constraint from engine-reference or existing ADR]

### Knowledge Gaps (verify before committing to these)
- [post-cutoff feature this design might rely on — mark HIGH/MEDIUM risk]

### Existing ADRs That Constrain This System
- ADR-XXXX: [decision summary] — means [implication for this GDD]
  (or "None yet")
```
```
## Technical Feasibility Brief: [System Name]
Engine: [name + version]
Domain: [domain]

### Known Engine Capabilities (verified for [version])
- [capability relevant to this system]
- [capability 2]

### Engine Constraints That Will Shape This Design
- [constraint from engine-reference or existing ADR]

### Knowledge Gaps (verify before committing to these)
- [post-cutoff feature this design might rely on — mark HIGH/MEDIUM risk]

### Existing ADRs That Constrain This System
- ADR-XXXX: [decision summary] — means [implication for this GDD]
  (or "None yet")
```

If no engine reference docs exist (engine not yet configured), show a short note:
> "No engine configured yet — skipping technical feasibility check. Run
> `/setup-engine` before moving to architecture if you haven't already."
若无引擎参考文档（引擎尚未配置），显示简短说明：
> "No engine configured yet — skipping technical feasibility check. Run
> `/setup-engine` before moving to architecture if you haven't already."

**Step 4 — Ask before proceeding:**
**步骤 4 —— 继续之前询问：**

Use `AskUserQuestion`:
- "Any constraints to add before we begin, or shall we proceed with these noted?"
  - Options: "Proceed with these noted", "Add a constraint first", "I need to check the engine docs — pause here"
使用 `AskUserQuestion`：
- "Any constraints to add before we begin, or shall we proceed with these noted?"
  - 选项："Proceed with these noted"、"Add a constraint first"、"I need to check the engine docs — pause here"

---

Use `AskUserQuestion`:
- "Ready to start designing [system-name]?"
  - Options: "Yes, let's go", "Show me more context first", "Design a dependency first"
使用 `AskUserQuestion`：
- "Ready to start designing [system-name]?"
  - 选项："Yes, let's go"、"Show me more context first"、"Design a dependency first"

---

## 3. Create File Skeleton
## 3. 创建文件骨架

Once the user confirms, **immediately** create the GDD file with empty section
headers. This ensures incremental writes have a target.
用户确认后，**立即**创建带空段落标题的 GDD 文件。这确保增量写入有目标。

Use the template structure from `.claude/docs/templates/game-design-document.md`:
使用 `.claude/docs/templates/game-design-document.md` 的模板结构：

```markdown
# [System Name]

> **Status**: In Design
> **Author**: [user + agents]
> **Last Updated**: [today's date]
> **Implements Pillar**: [from context]

## Overview

[To be designed]

## Player Fantasy

[To be designed]

## Detailed Design

### Core Rules

[To be designed]

### States and Transitions

[To be designed]

### Interactions with Other Systems

[To be designed]

## Formulas

[To be designed]

## Edge Cases

[To be designed]

## Dependencies

[To be designed]

## Tuning Knobs

[To be designed]

## Visual/Audio Requirements

[To be designed]

## UI Requirements

[To be designed]

## Acceptance Criteria

[To be designed]

## Open Questions

[To be designed]
```

Ask: "May I create the skeleton file at `design/gdd/[system-name].md`?"
询问："May I create the skeleton file at `design/gdd/[system-name].md`?"

If the user declines: Stop with the following message:
> "Verdict: **BLOCKED** — skeleton creation declined. The design session cannot proceed without the skeleton file, as all subsequent phases use it as the base. Re-run `/design-system [system]` when ready to create the file."
若用户拒绝：停止并显示：
> "Verdict: **BLOCKED** — skeleton creation declined. The design session cannot proceed without the skeleton file, as all subsequent phases use it as the base. Re-run `/design-system [system]` when ready to create the file."
Do not proceed to Section A.
不要进入段落 A。

After writing, update `production/session-state/active.md`:
- Use Glob to check if the file exists.
- If it **does not exist**: use the **Write** tool to create it. Never attempt Edit on a file that may not exist.
- If it **already exists**: use the **Edit** tool to update the relevant fields.
写入后，更新 `production/session-state/active.md`：
- 使用 Glob 检查文件是否存在。
- 若**不存在**：使用 **Write** 工具创建它。绝不尝试 Edit 可能不存在的文件。
- 若**已存在**：使用 **Edit** 工具更新相关字段。

File content:
- Task: Designing [system-name] GDD
- Current section: Starting (skeleton created)
- File: design/gdd/[system-name].md
文件内容：
- Task：Designing [system-name] GDD
- Current section：Starting (skeleton created)
- File：design/gdd/[system-name].md

---

## 4. Section-by-Section Design
## 4. 逐段落设计

Walk through each section in order. For **each section**, follow this cycle:
按顺序走查每个段落。对**每个段落**，遵循此循环：

### The Section Cycle
### 段落循环

```
Context  ->  Questions  ->  Options  ->  Decision  ->  Draft  ->  Approval  ->  Write
```

1. **Context**: State what this section needs to contain, and surface any relevant
   decisions from dependency GDDs that constrain it.
1. **Context**：陈述本段落需要包含什么，并暴露依赖 GDD 中约束它的相关决策。

2. **Questions**: Ask clarifying questions specific to this section. Use
   `AskUserQuestion` for constrained questions, conversational text for open-ended
   exploration.
2. **Questions**：针对本段落问澄清问题。约束性问题用 `AskUserQuestion`，
   开放性探索用对话文本。

3. **Options**: Where the section involves design choices (not just documentation),
   present 2-4 approaches with pros/cons. Explain reasoning in conversation text,
   then use `AskUserQuestion` to capture the decision.
3. **Options**：当段落涉及设计选择（不仅是文档）时，展示 2-4 个方案及优缺点。
   在对话文本中解释推理，然后用 `AskUserQuestion` 捕获决策。

4. **Decision**: User picks an approach or provides custom direction.
4. **Decision**：用户选择方案或提供自定义方向。

5. **Draft**: Write the section content in conversation text for review. Flag any
   provisional assumptions about undesigned dependencies.
5. **Draft**：在对话文本中写出段落内容供评审。标记任何对未设计依赖的临时假设。

6. **Approval**: Immediately after the draft — in the SAME response — use
   `AskUserQuestion`. **NEVER use plain text. NEVER skip this step.**
   - Prompt: "Approve the [Section Name] section?"
   - Options: `[A] Approve — write it to file` / `[B] Make changes — describe what to fix` / `[C] Start over`
6. **Approval**：草稿之后立即 —— 在同一回复中 —— 使用 `AskUserQuestion`。
   **绝不用纯文本。绝不跳过此步。**
   - 提示："Approve the [Section Name] section?"
   - 选项：`[A] Approve — write it to file` / `[B] Make changes — describe what to fix` / `[C] Start over`

   **The draft and the approval widget MUST appear together in one response.
   If the draft appears without the widget, the user is left at a blank prompt
   with no path forward — this is a protocol violation.**
   **草稿与批准组件必须出现在同一回复中。若草稿出现而无组件，用户将停留在
   空白提示前无路可走 —— 这是协议违规。**

7. **Write**: Use the Edit tool to replace the placeholder with the approved content.
   **CRITICAL**: Always include the section heading in the `old_string` to ensure
   uniqueness — never match `[To be designed]` alone, as multiple sections use the
   same placeholder and the Edit tool requires a unique match. Use this pattern:
   ```
   old_string: "## [Section Name]\n\n[To be designed]"
   new_string: "## [Section Name]\n\n[approved content]"
   ```
   Confirm the write.
7. **Write**：使用 Edit 工具将占位符替换为已批准内容。
   **关键**：始终在 `old_string` 中包含段落标题以确保唯一 —— 绝不只匹配
   `[To be designed]`，因为多个段落使用相同占位符且 Edit 工具要求唯一匹配。
   使用此模式：
   ```
   old_string: "## [Section Name]\n\n[To be designed]"
   new_string: "## [Section Name]\n\n[approved content]"
   ```
   确认写入。

8. **Registry conflict check** (Sections C and D only — Detailed Design and Formulas):
   After writing, scan the section content for entity names, item names, formula
   names, and numeric constants that appear in the registry. For each match:
   - Compare the value just written against the registry entry.
   - If they differ: **surface the conflict immediately** before starting the next
     section. Do not continue silently.
     > "Registry conflict: [name] is registered in [source GDD] as [registry_value].
     > This section just wrote [new_value]. Which is correct?"
   - If new (not in registry): flag it as a candidate for registry registration
     (will be handled in Phase 5).
8. **注册表冲突检查**（仅段落 C 和 D —— Detailed Design 与 Formulas）：
   写入后，扫描段落内容中的实体名、物品名、公式名与注册表中出现的数字常量。
   对每个匹配：
   - 将刚写入的值与注册表条目对比。
   - 若不同：在开始下一段落之前**立即暴露冲突**。不要默默继续。
     > "Registry conflict: [name] is registered in [source GDD] as [registry_value].
     > This section just wrote [new_value]. Which is correct?"
   - 若为新（不在注册表中）：标记为注册表注册候选（将在阶段 5 处理）。

After writing each section, update `production/session-state/active.md` with the
completed section name. Use Glob to check if the file exists — use Write to create
it if absent, Edit to update it if present.
写入每段落后，更新 `production/session-state/active.md` 的已完成段落名。
使用 Glob 检查文件是否存在 —— 不存在则 Write 创建，存在则 Edit 更新。

### Section-Specific Guidance
### 段落专属指导

Each section has unique design considerations and may benefit from specialist agents:
每个段落有独特设计考量，可能受益于专家智能体：

---

### Section A: Overview
### 段落 A：Overview

**Goal**: One paragraph a stranger could read and understand.
**目标**：陌生人能读懂的一段话。

**Derive recommended options before building the widget**: Read the system's category and layer from the systems index (already in context from Phase 2), then determine the recommended option for each tab:
- **Framing tab**: Foundation/Infrastructure layer → `[A]` recommended. Player-facing categories (Combat, UI, Dialogue, Character, Animation, Visual Effects, Audio) → `[C] Both` recommended.
- **ADR ref tab**: Glob `docs/architecture/adr-*.md` and grep for the system name in the GDD Requirements section of any ADR. If a matching ADR is found → `[A] Yes — cite the ADR` recommended. If none found → `[B] No` recommended.
- **Fantasy tab**: Foundation/Infrastructure layer → `[B] No` recommended. All other categories → `[A] Yes` recommended.
**在构建组件之前推导推荐选项**：从 systems index 读取系统类别与层级（已在阶段 2
上下文中），然后为每个 tab 决定推荐选项：
- **Framing tab**：Foundation/Infrastructure 层 → 推荐 `[A]`。面向玩家的类别
  （Combat、UI、Dialogue、Character、Animation、Visual Effects、Audio）→ 推荐
  `[C] Both`。
- **ADR ref tab**：Glob `docs/architecture/adr-*.md` 并在任何 ADR 的 GDD
  Requirements 段落中 grep 系统名。若找到匹配 ADR → 推荐 `[A] Yes — cite the ADR`。
  若未找到 → 推荐 `[B] No`。
- **Fantasy tab**：Foundation/Infrastructure 层 → 推荐 `[B] No`。其他所有类别 →
  推荐 `[A] Yes`。

Append `(Recommended)` to the appropriate option text in each tab.
在每个 tab 的对应选项文本后追加 `(Recommended)`。

**Framing questions (ask BEFORE drafting)**: Use `AskUserQuestion` with a multi-tab widget:
- Tab "Framing" — "How should the overview frame this system?" Options: `[A] As a data/infrastructure layer (technical framing)` / `[B] Through its player-facing effect (design framing)` / `[C] Both — describe the data layer and its player impact`
- Tab "ADR ref" — "Should the overview reference the existing ADR for this system?" Options: `[A] Yes — cite the ADR for implementation details` / `[B] No — keep the GDD at pure design level`
- Tab "Fantasy" — "Does this system have a player fantasy worth stating?" Options: `[A] Yes — players feel it directly` / `[B] No — pure infrastructure, players feel what it enables`
**框架问题（草稿之前询问）**：使用 `AskUserQuestion` 多 tab 组件：
- Tab "Framing" —— "How should the overview frame this system?" 选项：`[A] As a data/infrastructure layer (technical framing)` / `[B] Through its player-facing effect (design framing)` / `[C] Both — describe the data layer and its player impact`
- Tab "ADR ref" —— "Should the overview reference the existing ADR for this system?" 选项：`[A] Yes — cite the ADR for implementation details` / `[B] No — keep the GDD at pure design level`
- Tab "Fantasy" —— "Does this system have a player fantasy worth stating?" 选项：`[A] Yes — players feel it directly` / `[B] No — pure infrastructure, players feel what it enables`

Use the user's answers to shape the draft. Do NOT answer these questions yourself and auto-draft.
用用户答案塑造草稿。不要自己回答这些问题并自动草拟。

**Questions to ask**:
- What is this system in one sentence?
- How does a player interact with it? (active/passive/automatic)
- Why does this system exist — what would the game lose without it?
**要问的问题**：
- 用一句话描述这个系统是什么？
- 玩家如何与它交互？（主动/被动/自动）
- 此系统为何存在 —— 没有它游戏会失去什么？

**Cross-reference**: Check that the description aligns with how the systems index
describes it. Flag discrepancies.
**交叉引用**：检查描述是否与 systems index 中的描述对齐。标记不一致。

**Design vs. implementation boundary**: Overview questions must stay at the behavior
level — what the system *does*, not *how it is built*. If implementation questions
arise during the Overview (e.g., "Should this use an Autoload singleton or a signal
bus?"), note them as "→ becomes an ADR" and move on. Implementation patterns belong
in `/architecture-decision`, not the GDD. The GDD describes behavior; the ADR
describes the technical approach used to achieve it.
**设计与实现的边界**：Overview 问题必须停留在行为层面 —— 系统*做什么*，而非
*如何构建*。若 Overview 中出现实现问题（如 "Should this use an Autoload singleton
or a signal bus?"），将其标注为 "→ becomes an ADR" 并继续。实现模式属于
`/architecture-decision`，而非 GDD。GDD 描述行为；ADR 描述用于实现它的技术
方法。

---

### Section B: Player Fantasy
### 段落 B：Player Fantasy

**Goal**: The emotional target — what the player should *feel*.
**目标**：情感目标 —— 玩家应*感受*到什么。

**Derive recommended option before building the widget**: Read the system's category and layer from Phase 2 context:
- Player-facing categories (Combat, UI, Dialogue, Character, Animation, Audio, Level/World) → `[A] Direct` recommended
- Foundation/Infrastructure layer → `[B] Indirect` recommended
- Mixed categories (Camera/input, Economy, AI with visible player effects) → `[C] Both` recommended
**在构建组件之前推导推荐选项**：从阶段 2 上下文读取系统类别与层级：
- 面向玩家的类别（Combat、UI、Dialogue、Character、Animation、Audio、
  Level/World）→ 推荐 `[A] Direct`
- Foundation/Infrastructure 层 → 推荐 `[B] Indirect`
- 混合类别（Camera/input、Economy、有可见玩家效果的 AI）→ 推荐 `[C] Both`

Append `(Recommended)` to the appropriate option text.
在对应选项文本后追加 `(Recommended)`。

**Framing question (ask BEFORE drafting)**: Use `AskUserQuestion`:
- Prompt: "Is this system something the player engages with directly, or infrastructure they experience indirectly?"
- Options: `[A] Direct — player actively uses or feels this system` / `[B] Indirect — player experiences the effects, not the system` / `[C] Both — has a direct interaction layer and infrastructure beneath it`
**框架问题（草稿之前询问）**：使用 `AskUserQuestion`：
- 提示："Is this system something the player engages with directly, or infrastructure they experience indirectly?"
- 选项：`[A] Direct — player actively uses or feels this system` / `[B] Indirect — player experiences the effects, not the system` / `[C] Both — has a direct interaction layer and infrastructure beneath it`

Use the answer to frame the Player Fantasy section appropriately. Do NOT assume the answer.
用答案适当框架 Player Fantasy 段落。不要假设答案。

**Questions to ask**:
- What emotion or power fantasy does this serve?
- What reference games nail this feeling? What specifically creates it?
- Is this a "system you love engaging with" or "infrastructure you don't notice"?
**要问的问题**：
- 它服务于什么情感或力量幻想？
- 哪些参考游戏精准把握了这种感觉？什么具体地创造了它？
- 这是 "你喜爱与之互动的系统" 还是 "你不注意的基础设施"？

**Cross-reference**: Must align with the game pillars. If the system serves a pillar,
quote the relevant pillar text.
**交叉引用**：必须与游戏支柱对齐。若系统服务于支柱，引用相关支柱文本。

**Review mode check** (apply before spawning):
- `solo` → skip this agent spawn. Draft the section without the specialist. Add a note: "`creative-director` not consulted — Solo mode. Review manually before production."
- `lean` → skip unless this is a section with HIGH implementation risk (Sections D and H only). For other sections, draft without the agent.
- `full` → spawn as described below.
**评审模式检查**（启动之前应用）：
- `solo` → 跳过此智能体启动。无专家草拟段落。添加说明："`creative-director` not consulted — Solo mode. Review manually before production."
- `lean` → 除非是 HIGH 实现风险段落（仅段落 D 和 H），否则跳过。其他段落无
  智能体草拟。
- `full` → 如下所述启动。

**Agent delegation (MANDATORY)**: After the framing answer is given but before drafting,
spawn `creative-director` via Task:
- Provide: system name, framing answer (direct/indirect/both), game pillars, any reference games the user mentioned, the game concept summary
- Ask: "Shape the Player Fantasy for this system. What emotion or power fantasy should it serve? What player moment should we anchor to? What tone and language fits the game's established feeling? Be specific — give me 2-3 candidate framings."
- Collect the creative-director's framings and present them to the user alongside the draft.
**智能体委派（强制）**：在框架答案给出之后、草拟之前，通过 Task 启动
`creative-director`：
- 提供：系统名、框架答案（direct/indirect/both）、游戏支柱、用户提及的任何参考
  游戏、游戏概念摘要
- 询问："Shape the Player Fantasy for this system. What emotion or power fantasy should it serve? What player moment should we anchor to? What tone and language fits the game's established feeling? Be specific — give me 2-3 candidate framings."
- 收集 creative-director 的框架并连同草稿一起展示给用户。

**Do NOT draft Section B without first consulting `creative-director`.** The framing
answer tells us *what kind* of fantasy it is; the creative-director shapes *how it's
described* — tone, language, the specific player moment to anchor to.
**不要在咨询 `creative-director` 之前草拟段落 B。** 框架答案告诉我们这是
*什么类型*的幻想；creative-director 塑造*它如何被描述* —— 语调、语言、
要锚定的具体玩家时刻。

---

### Section C: Detailed Design (Core Rules, States, Interactions)
### 段落 C：Detailed Design（Core Rules、States、Interactions）

**Goal**: Unambiguous specification a programmer could implement without questions.
**目标**：程序员可无需提问即可实现的明确规范。

This is usually the largest section. Break it into sub-sections:
这通常是最大段落。拆为子段落：

1. **Core Rules**: The fundamental mechanics. Use numbered rules for sequential
   processes, bullets for properties.
2. **States and Transitions**: If the system has states, map every state and
   every valid transition. Use a table.
3. **Interactions with Other Systems**: For each dependency (upstream and downstream),
   specify what data flows in, what flows out, and who owns the interface.
1. **Core Rules**：基础机制。顺序流程用编号规则，属性用项目符号。
2. **States and Transitions**：若系统有状态，映射每个状态与每个有效转换。用表格。
3. **Interactions with Other Systems**：对每个依赖（上游与下游），指定流入什么
   数据、流出什么、谁拥有接口。

**Questions to ask**:
- Walk me through a typical use of this system, step by step
- What are the decision points the player faces?
- What can the player NOT do? (Constraints are as important as capabilities)
**要问的问题**：
- 逐步带我走一遍本系统的典型用法
- 玩家面对的决策点是什么？
- 玩家不能做什么？（约束与能力同样重要）

**Review mode check** (apply before spawning):
- `solo` → skip this agent spawn. Draft the section without the specialist. Add a note: "Specialist agents not consulted — Solo mode. Review manually before production."
- `lean` → skip unless this is a section with HIGH implementation risk (Sections D and H only). For other sections, draft without the agent.
- `full` → spawn as described below.
**评审模式检查**（启动之前应用）：
- `solo` → 跳过此智能体启动。无专家草拟段落。添加说明："Specialist agents not consulted — Solo mode. Review manually before production."
- `lean` → 除非是 HIGH 实现风险段落（仅段落 D 和 H），否则跳过。其他段落无
  智能体草拟。
- `full` → 如下所述启动。

**Agent delegation (MANDATORY)**: Before drafting Section C, spawn specialist agents via Task in parallel:
- Look up the system category in the routing table (Section 6 of this skill)
- Spawn the Primary Agent AND Supporting Agent(s) listed for this category
- Provide each agent: system name, game concept summary, pillar set, dependency GDD excerpts, the specific section being worked on
- Collect their findings before drafting
- Surface any disagreements between agents to the user via `AskUserQuestion`
- Draft only after receiving specialist input
**智能体委派（强制）**：草拟段落 C 之前，通过 Task 并行启动专家智能体：
- 在路由表（本技能段落 6）中查找系统类别
- 启动该类别列出的 Primary Agent 与 Supporting Agent(s)
- 为每个智能体提供：系统名、游戏概念摘要、支柱集、依赖 GDD 摘录、当前段落
- 草拟之前收集其发现
- 通过 `AskUserQuestion` 向用户暴露智能体之间的任何分歧
- 仅在收到专家输入后草拟

**Do NOT draft Section C without first consulting the appropriate specialists.** A `systems-designer` reviewing rules and mechanics will catch design gaps the main session cannot.
**不要在咨询合适专家之前草拟段落 C。** 审查规则与机制的 `systems-designer` 会
捕捉主会话无法捕捉的设计缺漏。

**Cross-reference**: For each interaction listed, verify it matches what the
dependency GDD specifies. If a dependency defines a value or formula and this
system expects something different, flag the conflict.
**交叉引用**：对列出的每个交互，验证它与依赖 GDD 指定的一致。若依赖定义了
值或公式而本系统期望不同，标记冲突。

---

### Section D: Formulas
### 段落 D：Formulas

**Goal**: Every mathematical formula, with variables defined, ranges specified,
and edge cases noted.
**目标**：每个数学公式，定义变量、指定范围、注明边界情况。

**Completion Steering — always begin each formula with this exact structure:**
**完成引导 —— 每个公式始终以此结构开始：**

```
The [formula_name] formula is defined as:

`[formula_name] = [expression]`

**Variables:**
| Variable | Symbol | Type | Range | Description |
|----------|--------|------|-------|-------------|
| [name] | [sym] | float/int | [min–max] | [what it represents] |

**Output Range:** [min] to [max] under normal play; [behaviour at extremes]
**Example:** [worked example with real numbers]
```

Do NOT write `[Formula TBD]` or describe a formula in prose without the variable
table. A formula without defined variables cannot be implemented without guesswork.
不要写 `[Formula TBD]` 或用散文描述公式而无变量表。无定义变量的公式无法不靠
猜测地实现。

**Questions to ask**:
- What are the core calculations this system performs?
- Should scaling be linear, logarithmic, or stepped?
- What should the output ranges be at early/mid/late game?
**要问的问题**：
- 本系统执行的核心计算是什么？
- 扩展应是线性、对数还是阶跃？
- 早/中/后期的输出范围应是什么？

**Review mode check** (apply before spawning):
- `solo` → skip this agent spawn. Draft the section without the specialist. Add a note: "`systems-designer` not consulted — Solo mode. Review manually before production."
- `lean` → skip unless this is a section with HIGH implementation risk (Sections D and H only). For other sections, draft without the agent.
- `full` → spawn as described below.
**评审模式检查**（启动之前应用）：
- `solo` → 跳过此智能体启动。无专家草拟段落。添加说明："`systems-designer` not consulted — Solo mode. Review manually before production."
- `lean` → 除非是 HIGH 实现风险段落（仅段落 D 和 H），否则跳过。其他段落无
  智能体草拟。
- `full` → 如下所述启动。

**Agent delegation (MANDATORY)**: Before proposing any formulas or balance values, spawn specialist agents via Task in parallel:
- **Always spawn `systems-designer`**: provide Core Rules from Section C, tuning goals from user, balance context from dependency GDDs. Ask them to propose formulas with variable tables and output ranges.
- **For economy/cost systems, also spawn `economy-designer`**: provide placement costs, upgrade cost intent, and progression goals. Ask them to validate cost curves and ratios.
- Present the specialists' proposals to the user for review via `AskUserQuestion`
- The user decides; the main session writes to file
- **Do NOT invent formula values or balance numbers without specialist input.** A user without balance design expertise cannot evaluate raw numbers — they need the specialists' reasoning.
**智能体委派（强制）**：在提出任何公式或平衡值之前，通过 Task 并行启动专家
智能体：
- **始终启动 `systems-designer`**：提供段落 C 的 Core Rules、用户的调优目标、
  依赖 GDD 的平衡上下文。请其提出带变量表与输出范围的公式。
- **对于经济/成本系统，也启动 `economy-designer`**：提供放置成本、升级成本
  意图、进度目标。请其验证成本曲线与比率。
- 通过 `AskUserQuestion` 向用户展示专家提议供评审
- 用户决策；主会话写入文件
- **不要在没有专家输入的情况下编造公式值或平衡数字。** 没有平衡设计专业知识的
  用户无法评估原始数字 —— 他们需要专家的推理。

**Cross-reference**: If a dependency GDD defines a formula whose output feeds into
this system, reference it explicitly. Don't reinvent — connect.
**交叉引用**：若依赖 GDD 定义的公式输出流入本系统，明确引用。不要重发明 ——
连接。

---

### Section E: Edge Cases
### 段落 E：Edge Cases

**Goal**: Explicitly handle unusual situations so they don't become bugs.
**目标**：显式处理异常情况，使其不变成 bug。

**Completion Steering — format each edge case as:**
- **If [condition]**: [exact outcome]. [rationale if non-obvious]
**完成引导 —— 每个边界情况格式化为：**
- **If [condition]**：[exact outcome]．[rationale if non-obvious]

Example (adapt terminology to the game's domain):
- **If [resource] reaches 0 while [protective condition] is active**: hold at minimum until condition ends, then apply consequence.
- **If two [triggers/events] fire simultaneously**: resolve in [defined priority order]; ties use [defined tiebreak rule].
示例（根据游戏领域调整术语）：
- **If [resource] reaches 0 while [protective condition] is active**：保持
  最小值直到条件结束，然后施加后果。
- **If two [triggers/events] fire simultaneously**：按 [defined priority order]
  解决；平局用 [defined tiebreak rule]。

Do NOT write vague entries like "handle appropriately" — each must name the exact
condition and the exact resolution. An edge case without a resolution is an open
design question, not a specification.
不要写 "handle appropriately" 等模糊条目 —— 每个必须命名确切条件与确切解决。
没有解决方案的边界情况是开放设计问题，而非规范。

**Questions to ask**:
- What happens at zero? At maximum? At out-of-range values?
- What happens when two rules apply at the same time?
- What happens if a player finds an unintended interaction? (Identify degenerate strategies)
**要问的问题**：
- 在零时发生什么？在最大时？在范围外值时？
- 两条规则同时适用时发生什么？
- 若玩家发现未预期交互会怎样？（识别退化策略）

**Review mode check** (apply before spawning):
- `solo` → skip this agent spawn. Draft the section without the specialist. Add a note: "`systems-designer` not consulted — Solo mode. Review manually before production."
- `lean` → skip unless this is a section with HIGH implementation risk (Sections D and H only). For other sections, draft without the agent.
- `full` → spawn as described below.
**评审模式检查**（启动之前应用）：
- `solo` → 跳过此智能体启动。无专家草拟段落。添加说明："`systems-designer` not consulted — Solo mode. Review manually before production."
- `lean` → 除非是 HIGH 实现风险段落（仅段落 D 和 H），否则跳过。其他段落无
  智能体草拟。
- `full` → 如下所述启动。

**Agent delegation (MANDATORY)**: Spawn `systems-designer` via Task before finalising edge cases. Provide: the completed Sections C and D, and ask them to identify edge cases from the formula and rule space that the main session may have missed. For narrative systems, also spawn `narrative-director`. Present their findings and ask the user which to include.
**智能体委派（强制）**：在最终确定边界情况之前通过 Task 启动
`systems-designer`。提供：完成的段落 C 和 D，并请其识别主会话可能遗漏的
公式与规则空间中的边界情况。对于叙事系统，也启动 `narrative-director`。
展示其发现并询问用户要包含哪些。

**Cross-reference**: Check edge cases against dependency GDDs. If a dependency
defines a floor, cap, or resolution rule that this system could violate, flag it.
**交叉引用**：对照依赖 GDD 检查边界情况。若依赖定义了本系统可能违反的下限、
上限或解决规则，标记它。

---

### Section F: Dependencies
### 段落 F：Dependencies

**Goal**: Map every system connection with direction and nature.
**目标**：映射每个系统连接的方向与性质。

This section is partially pre-filled from the context gathering phase. Present the
known dependencies from the systems index and ask:
- Are there dependencies I'm missing?
- For each dependency, what's the specific data interface?
- Which dependencies are hard (system cannot function without it) vs. soft
  (enhanced by it but works without it)?
此段落已从上下文收集阶段部分预填。展示来自 systems index 的已知依赖并询问：
- 是否有我遗漏的依赖？
- 对每个依赖，具体数据接口是什么？
- 哪些依赖是硬的（无它系统无法运行）vs 软的（受它增强但无它也可工作）？

**Cross-reference**: This section must be bidirectionally consistent. If this system
lists "depends on Combat", then the Combat GDD should list "depended on by [this
system]". Flag any one-directional dependencies for correction.
**交叉引用**：本段落必须双向一致。若本系统列出 "depends on Combat"，则
Combat GDD 应列出 "depended on by [this system]"。标记任何单向依赖以供修正。

---

### Section G: Tuning Knobs
### 段落 G：Tuning Knobs

**Goal**: Every designer-adjustable value, with safe ranges and extreme behaviors.
**目标**：每个设计师可调的值，含安全范围与极端行为。

**Questions to ask**:
- What values should designers be able to tweak without code changes?
- For each knob, what breaks if it's set too high? Too low?
- Which knobs interact with each other? (Changing A makes B irrelevant)
**要问的问题**：
- 设计师应能在不改代码的情况下调整哪些值？
- 对每个旋钮，设太高会破坏什么？太低？
- 哪些旋钮互相交互？（改变 A 使 B 无关）

**Agent delegation**: If formulas are complex, delegate to `systems-designer`
to derive tuning knobs from the formula variables.
**智能体委派**：若公式复杂，委派给 `systems-designer` 从公式变量推导调优旋钮。

**Cross-reference**: If a dependency GDD lists tuning knobs that affect this system,
reference them here. Don't create duplicate knobs — point to the source of truth.
**交叉引用**：若依赖 GDD 列出影响本系统的调优旋钮，在此引用。不要创建重复
旋钮 —— 指向真相源。

---

### Section H: Acceptance Criteria
### 段落 H：Acceptance Criteria

**Goal**: Testable conditions that prove the system works as designed.
**目标**：证明系统按设计工作的可测试条件。

**Completion Steering — format each criterion as Given-When-Then:**
- **GIVEN** [initial state], **WHEN** [action or trigger], **THEN** [measurable outcome]
**完成引导 —— 每个标准格式化为 Given-When-Then：**
- **GIVEN** [initial state], **WHEN** [action or trigger], **THEN** [measurable outcome]

Example (adapt terminology to the game's domain):
- **GIVEN** [initial state], **WHEN** [player action or system trigger], **THEN** [specific measurable outcome].
- **GIVEN** [a constraint is active], **WHEN** [player attempts an action], **THEN** [feedback shown and action result].
示例（根据游戏领域调整术语）：
- **GIVEN** [initial state], **WHEN** [player action or system trigger], **THEN** [specific measurable outcome]。
- **GIVEN** [a constraint is active], **WHEN** [player attempts an action], **THEN** [feedback shown and action result]。

Include at least: one criterion per core rule from Section C, and one per formula
from Section D. Do NOT write "the system works as designed" — every criterion must
be independently verifiable by a QA tester without reading the GDD.
至少包含：段落 C 中每条核心规则一个标准，段落 D 中每个公式一个标准。不要写
"the system works as designed" —— 每个标准必须可由 QA 测试者独立验证而无需读
GDD。

**Review mode check** (apply before spawning):
- `solo` → skip this agent spawn. Draft the section without the specialist. Add a note: "`qa-lead` not consulted — Solo mode. Review manually before production."
- `lean` → skip unless this is a section with HIGH implementation risk (Sections D and H only). For other sections, draft without the agent.
- `full` → spawn as described below.
**评审模式检查**（启动之前应用）：
- `solo` → 跳过此智能体启动。无专家草拟段落。添加说明："`qa-lead` not consulted — Solo mode. Review manually before production."
- `lean` → 除非是 HIGH 实现风险段落（仅段落 D 和 H），否则跳过。其他段落无
  智能体草拟。
- `full` → 如下所述启动。

**Agent delegation (MANDATORY)**: Spawn `qa-lead` via Task before finalising acceptance criteria. Provide: the completed GDD sections C, D, E, and ask them to validate that the criteria are independently testable and cover all core rules and formulas. Surface any gaps or untestable criteria to the user.
**智能体委派（强制）**：在最终确定验收标准之前通过 Task 启动 `qa-lead`。
提供：完成的段落 C、D、E，请其验证标准是否可独立测试且覆盖所有核心规则与
公式。向用户暴露任何缺漏或不可测试的标准。

**Questions to ask**:
- What's the minimum set of tests that prove this works?
- What performance budget does this system get? (frame time, memory)
- What would a QA tester check first?
**要问的问题**：
- 证明此可行的最小测试集是什么？
- 本系统获得什么性能预算？（帧时间、内存）
- QA 测试者会首先检查什么？

**Cross-reference**: Include criteria that verify cross-system interactions work,
not just this system in isolation.
**交叉引用**：包含验证跨系统交互的标准，不仅是本系统孤立工作。

---

### Optional Sections: Visual/Audio, UI Requirements, Open Questions
### 可选段落：Visual/Audio、UI Requirements、Open Questions

These sections are included in the template. Visual/Audio is **REQUIRED** for visual system categories — not optional. Determine the requirement level before asking:
这些段落已包含在模板中。Visual/Audio 对于视觉系统类别是**必需的** —— 非可选。
询问之前确定要求级别：

**Visual/Audio is REQUIRED (mandatory — do not offer to skip) for these system categories:**
- Combat, damage, health
- UI systems (HUD, menus)
- Animation, character movement
- Visual effects, particles, shaders
- Character systems
- Dialogue, quests, lore
- Level/world systems
**Visual/Audio 是必需的（强制 —— 不要提供跳过选项）针对以下系统类别：**
- 战斗、伤害、生命
- UI 系统（HUD、菜单）
- 动画、角色移动
- 视觉效果、粒子、着色器
- 角色系统
- 对话、任务、传说
- 关卡/世界系统

For required systems: **spawn `art-director` via Task** before drafting this section. Provide: system name, game concept, game pillars, art bible sections 1–4 if they exist. Ask them to specify: (1) VFX and visual feedback requirements for this system's events, (2) any animation or visual style constraints, (3) which art bible principles most directly apply to this system. Present their output; do NOT leave this section as `[To be designed]` for visual systems.
对于必需系统：在草拟本段落之前**通过 Task 启动 `art-director`**。提供：系统名、
游戏概念、游戏支柱、若存在则 art bible 段落 1-4。请其指定：(1) 本系统事件的
VFX 与视觉反馈需求，(2) 任何动画或视觉风格约束，(3) 哪些 art bible 原则最
直接适用于本系统。展示其输出；不要将此段落留为 `[To be designed]` 给视觉
系统。

For **all other system categories** (Foundation/Infrastructure, Economy, AI/pathfinding, Camera/input), offer the optional sections after the required sections:
对**所有其他系统类别**（Foundation/Infrastructure、Economy、AI/pathfinding、
Camera/input），在必需段落之后提供可选段落：

Use `AskUserQuestion`:
- "The 8 required sections are complete. Do you want to also define Visual/Audio
  requirements, UI requirements, or capture open questions?"
  - Options: "Yes, all three", "Just open questions", "Skip — I'll add these later"
使用 `AskUserQuestion`：
- "The 8 required sections are complete. Do you want to also define Visual/Audio
  requirements, UI requirements, or capture open questions?"
  - 选项："Yes, all three"、"Just open questions"、"Skip — I'll add these later"

For **Visual/Audio** (non-required systems): Coordinate with `art-director` and `audio-director` if detail is needed. Often a brief note suffices at the GDD stage.
对 **Visual/Audio**（非必需系统）：若需细节则与 `art-director` 和
`audio-director` 协调。GDD 阶段通常简短说明即可。

> **Asset Spec Flag**: After the Visual/Audio section is written with real content, output this notice:
> "📌 **Asset Spec** — Visual/Audio requirements are defined. After the art bible is approved, run `/asset-spec system:[system-name]` to produce per-asset visual descriptions, dimensions, and generation prompts from this section."
> **Asset Spec 标志**：在 Visual/Audio 段落写入真实内容后，输出此通知：
> "📌 **Asset Spec** — Visual/Audio requirements are defined. After the art bible is approved, run `/asset-spec system:[system-name]` to produce per-asset visual descriptions, dimensions, and generation prompts from this section."

For **UI Requirements**: Coordinate with `ux-designer` for complex UI systems.
After writing this section, check whether it contains real content (not just
`[To be designed]` or a note that this system has no UI). If it does have real
UI requirements, output this flag immediately:
对 **UI Requirements**：对复杂 UI 系统与 `ux-designer` 协调。写入此段落后，
检查它是否包含真实内容（不仅是 `[To be designed]` 或本系统无 UI 的说明）。
若确实有真实 UI 需求，立即输出此标志：

> **📌 UX Flag — [System Name]**: This system has UI requirements. In Phase 4
> (Pre-Production), run `/ux-design` to create a UX spec for each screen or
> HUD element this system contributes to **before** writing epics. Stories that
> reference UI should cite `design/ux/[screen].md`, not the GDD directly.
>
> Note this in the systems index for this system if you update it.
> **📌 UX 标志 —— [System Name]**：本系统有 UI 需求。在阶段 4
> （Pre-Production）中，运行 `/ux-design` 为本系统贡献的每个屏幕或
> HUD 元素创建 UX 规范，**在**编写史诗之前。引用 UI 的故事应引用
> `design/ux/[screen].md`，而非直接引用 GDD。
>
> 若更新 systems index，请为本系统注明此点。

For **Open Questions**: Capture anything that came up during design that wasn't
fully resolved. Each question should have an owner and target resolution date.
对 **Open Questions**：捕捉设计中出现但未完全解决的任何内容。每个问题应有
负责人和目标解决日期。

---

## 5. Post-Design Validation
## 5. 设计后验证

After all sections are written:
所有段落写入后：

### 5a: Self-Check
### 5a：自检

Read back the complete GDD from file (not from conversation memory — the file is
the source of truth). Verify:
- All 8 required sections have real content (not placeholders)
- Formulas reference defined variables
- Edge cases have resolutions
- Dependencies are listed with interfaces
- Acceptance criteria are testable
从文件读回完整 GDD（非对话记忆 —— 文件是真相源）。验证：
- 所有 8 个必需段落有真实内容（非占位符）
- 公式引用已定义变量
- 边界情况有解决
- 依赖列出含接口
- 验收标准可测试

### 5a-bis: Creative Director Pillar Review
### 5a-bis：创意总监支柱评审

**Review mode check** — apply before spawning CD-GDD-ALIGN:
- `solo` → skip. Note: "CD-GDD-ALIGN skipped — Solo mode." Proceed to Step 5b.
- `lean` → skip (not a PHASE-GATE). Note: "CD-GDD-ALIGN skipped — Lean mode." Proceed to Step 5b.
- `full` → spawn as normal.
**评审模式检查** —— 启动 CD-GDD-ALIGN 之前应用：
- `solo` → 跳过。注明："CD-GDD-ALIGN skipped — Solo mode." 进入步骤 5b。
- `lean` → 跳过（非 PHASE-GATE）。注明："CD-GDD-ALIGN skipped — Lean mode." 进入步骤 5b。
- `full` → 如常启动。

Before finalizing the GDD, spawn `creative-director` via Task using gate **CD-GDD-ALIGN** (`.claude/docs/director-gates.md`).
在最终确定 GDD 之前，通过 Task 使用门禁 **CD-GDD-ALIGN**
（`.claude/docs/director-gates.md`）启动 `creative-director`。

Pass: completed GDD file path, game pillars (from `design/gdd/game-concept.md` or `design/gdd/game-pillars.md`), MDA aesthetics target.
传递：完成的 GDD 文件路径、游戏支柱（来自 `design/gdd/game-concept.md` 或
`design/gdd/game-pillars.md`）、MDA 美学目标。

Handle verdict per the standard rules in `director-gates.md`. After resolution, record the verdict in the GDD Status header:
`> **Creative Director Review (CD-GDD-ALIGN)**: APPROVED [date] / CONCERNS (accepted) [date] / REVISED [date]`
按 `director-gates.md` 中的标准规则处理结论。解决后，在 GDD Status 头中记录
结论：
`> **Creative Director Review (CD-GDD-ALIGN)**: APPROVED [date] / CONCERNS (accepted) [date] / REVISED [date]`

---

### 5b: Update Entity Registry
### 5b：更新实体注册表

Scan the completed GDD for cross-system facts that should be registered:
- Named entities (enemies, NPCs, bosses) with stats or drops
- Named items with values, weights, or categories
- Named formulas with defined variables and output ranges
- Named constants referenced by value in more than one place
扫描完成的 GDD，找出应注册的跨系统事实：
- 带属性或掉落的命名实体（敌人、NPC、Boss）
- 带值、重量或类别的命名物品
- 带定义变量与输出范围的命名公式
- 在多处按值引用的命名常量

For each candidate, check if it already exists in `design/registry/entities.yaml`:
```
Grep pattern="  - name: [candidate_name]" path="design/registry/entities.yaml"
```
对每个候选，检查它是否已存在于 `design/registry/entities.yaml`：
```
Grep pattern="  - name: [candidate_name]" path="design/registry/entities.yaml"
```

Present a summary:
```
Registry candidates from this GDD:
  NEW (not yet registered):
    - [entity_name] [entity]: [attribute]=[value], [attribute]=[value]
    - [item_name] [item]: [attribute]=[value], [attribute]=[value]
    - [formula_name] [formula]: variables=[list], output=[min–max]
  ALREADY REGISTERED (referenced_by will be updated):
    - [constant_name] [constant]: value=[N] ← matches registry ✅
```
展示摘要：
```
Registry candidates from this GDD:
  NEW (not yet registered):
    - [entity_name] [entity]: [attribute]=[value], [attribute]=[value]
    - [item_name] [item]: [attribute]=[value], [attribute]=[value]
    - [formula_name] [formula]: variables=[list], output=[min–max]
  ALREADY REGISTERED (referenced_by will be updated):
    - [constant_name] [constant]: value=[N] ← matches registry ✅
```

Ask: "May I update `design/registry/entities.yaml` with these [N] new entries
and update `referenced_by` for the existing entries?"
询问："May I update `design/registry/entities.yaml` with these [N] new entries
and update `referenced_by` for the existing entries?"

If yes: append new entries and update `referenced_by` arrays. Never modify
existing `value` / attribute fields without surfacing it as a conflict first.
若是：追加新条目并更新 `referenced_by` 数组。未经先暴露为冲突，绝不修改
现有 `value` / 属性字段。

### 5c: Offer Design Review
### 5c：提供设计评审

Present a completion summary:
展示完成摘要：

> **GDD Complete: [System Name]**
> - Sections written: [list]
> - Provisional assumptions: [list any assumptions about undesigned dependencies]
> - Cross-system conflicts found: [list or "none"]
> **GDD Complete: [System Name]**
> - 已写段落：[list]
> - 临时假设：[列出对未设计依赖的任何假设]
> - 发现的跨系统冲突：[list or "none"]

> **To validate this GDD, open a fresh Claude Code session and run:**
> `/design-review design/gdd/[system-name].md`
>
> **Never run `/design-review` in the same session as `/design-system`.** The reviewing
> agent must be independent of the authoring context. Running it here would inherit
> the full design history, making independent critique impossible.
> **要验证此 GDD，打开一个新的 Claude Code 会话并运行：**
> `/design-review design/gdd/[system-name].md`
>
> **绝不在 `/design-system` 的同一会话中运行 `/design-review`。** 评审智能体
> 必须独立于编写上下文。在此运行会继承完整设计历史，使独立评审不可能。

**NEVER offer to run `/design-review` inline.** Always direct the user to a fresh window.
**绝不内联提供运行 `/design-review`。** 始终引导用户到新窗口。

### 5d: Update Systems Index
### 5d：更新系统索引

After the GDD is complete (and optionally reviewed):
GDD 完成后（可选已评审）：

- Read the systems index
- Update the target system's row:
  - If design-review was run and verdict is APPROVED: Status → "Approved"
  - If design-review was run and verdict is NEEDS REVISION: Status → "In Review"
  - If design-review was skipped: Status → "Designed" (pending review)
  - If the user chose "I'll review it myself first": Status → "Designed"
  - Design Doc: link to `design/gdd/[system-name].md`
- Update the Progress Tracker counts
- 读取系统索引
- 更新目标系统行：
  - 若 design-review 已运行且结论为 APPROVED：Status → "Approved"
  - 若 design-review 已运行且结论为 NEEDS REVISION：Status → "In Review"
  - 若 design-review 已跳过：Status → "Designed"（待评审）
  - 若用户选择 "I'll review it myself first"：Status → "Designed"
  - Design Doc：链接到 `design/gdd/[system-name].md`
- 更新 Progress Tracker 计数

Ask: "May I update the systems index at `design/gdd/systems-index.md`?"
询问："May I update the systems index at `design/gdd/systems-index.md`?"

### 5e: Update Session State
### 5e：更新会话状态

Update `production/session-state/active.md` with:
- Task: [system-name] GDD
- Status: Complete (or In Review if design-review was run)
- File: design/gdd/[system-name].md
- Sections: All 8 written
- Next: [suggest next system from design order]
更新 `production/session-state/active.md`：
- Task：[system-name] GDD
- Status：Complete（若 design-review 已运行则为 In Review）
- File：design/gdd/[system-name].md
- Sections：All 8 written
- Next：[建议设计顺序中的下一个系统]

### 5f: Suggest Next Steps
### 5f：建议下一步

Use `AskUserQuestion`:
- "What's next?"
  - Options:
    - "Run `/consistency-check` — verify this GDD's values don't conflict with existing GDDs (recommended before designing the next system)"
    - "Design next system ([next-in-order])" — if undesigned systems remain
    - "Fix review findings" — if design-review flagged issues
    - "Stop here for this session"
    - "Run `/gate-check`" — if enough MVP systems are designed
使用 `AskUserQuestion`：
- "What's next?"
  - 选项：
    - "Run `/consistency-check` — verify this GDD's values don't conflict with existing GDDs (recommended before designing the next system)"
    - "Design next system ([next-in-order])" —— 若仍有未设计系统
    - "Fix review findings" —— 若 design-review 标记了问题
    - "Stop here for this session"
    - "Run `/gate-check`" —— 若已设计足够多 MVP 系统

---

## 6. Specialist Agent Routing
## 6. 专家智能体路由

This skill delegates to specialist agents for domain expertise. The main session
orchestrates the overall flow; agents provide expert content.
本技能委派专家智能体提供领域专业。主会话编排整体流程；智能体提供专家内容。

| System Category | Primary Agent | Supporting Agent(s) |
|----------------|---------------|---------------------|
| **Foundation/Infrastructure** (event bus, save/load, scene mgmt, service locator) | `systems-designer` | `gameplay-programmer` (feasibility), `engine-programmer` (engine integration) |
| Combat, damage, health | `game-designer` | `systems-designer` (formulas), `ai-programmer` (enemy AI), `art-director` (hit feedback visual direction, VFX intent) |
| Economy, loot, crafting | `economy-designer` | `systems-designer` (curves), `game-designer` (loops) |
| Progression, XP, skills | `game-designer` | `systems-designer` (curves), `economy-designer` (sinks) |
| Dialogue, quests, lore | `game-designer` | `narrative-director` (story), `writer` (content), `art-director` (character visual profiles, cinematic tone) |
| UI systems (HUD, menus) | `game-designer` | `ux-designer` (flows), `ui-programmer` (feasibility), `art-director` (visual style direction), `technical-artist` (render/shader constraints) |
| Audio systems | `game-designer` | `audio-director` (direction), `sound-designer` (specs) |
| AI, pathfinding, behavior | `game-designer` | `ai-programmer` (implementation), `systems-designer` (scoring) |
| Level/world systems | `game-designer` | `level-designer` (spatial), `world-builder` (lore) |
| Camera, input, controls | `game-designer` | `ux-designer` (feel), `gameplay-programmer` (feasibility) |
| Animation, character movement | `game-designer` | `art-director` (animation style, pose language), `technical-artist` (rig/blend constraints), `gameplay-programmer` (feel) |
| Visual effects, particles, shaders | `game-designer` | `art-director` (VFX visual direction), `technical-artist` (performance budget, shader complexity), `systems-designer` (trigger/state integration) |
| Character systems (stats, archetypes) | `game-designer` | `art-director` (character visual archetype), `narrative-director` (character arc alignment), `systems-designer` (stat formulas) |
| 系统类别 | 主智能体 | 辅助智能体 |
|----------------|---------------|---------------------|
| **Foundation/Infrastructure**（事件总线、存档/加载、场景管理、服务定位器） | `systems-designer` | `gameplay-programmer`（可行性）、`engine-programmer`（引擎集成） |
| 战斗、伤害、生命 | `game-designer` | `systems-designer`（公式）、`ai-programmer`（敌人 AI）、`art-director`（受击反馈视觉方向、VFX 意图） |
| 经济、掉落、合成 | `economy-designer` | `systems-designer`（曲线）、`game-designer`（循环） |
| 进度、XP、技能 | `game-designer` | `systems-designer`（曲线）、`economy-designer`（消耗口） |
| 对话、任务、传说 | `game-designer` | `narrative-director`（故事）、`writer`（内容）、`art-director`（角色视觉档案、电影化基调） |
| UI 系统（HUD、菜单） | `game-designer` | `ux-designer`（流程）、`ui-programmer`（可行性）、`art-director`（视觉风格方向）、`technical-artist`（渲染/着色器约束） |
| 音频系统 | `game-designer` | `audio-director`（方向）、`sound-designer`（规格） |
| AI、寻路、行为 | `game-designer` | `ai-programmer`（实现）、`systems-designer`（评分） |
| 关卡/世界系统 | `game-designer` | `level-designer`（空间）、`world-builder`（传说） |
| 相机、输入、控制 | `game-designer` | `ux-designer`（手感）、`gameplay-programmer`（可行性） |
| 动画、角色移动 | `game-designer` | `art-director`（动画风格、姿势语言）、`technical-artist`（绑定/混合约束）、`gameplay-programmer`（手感） |
| 视觉效果、粒子、着色器 | `game-designer` | `art-director`（VFX 视觉方向）、`technical-artist`（性能预算、着色器复杂度）、`systems-designer`（触发/状态集成） |
| 角色系统（属性、原型） | `game-designer` | `art-director`（角色视觉原型）、`narrative-director`（角色弧线对齐）、`systems-designer`（属性公式） |

**When delegating via Task tool**:
- Provide: system name, game concept summary, dependency GDD excerpts, the specific
  section being worked on, and what question needs expert input
- The agent returns analysis/proposals to the main session
- The main session presents the agent's output to the user via `AskUserQuestion`
- The user decides; the main session writes to file
- Agents do NOT write to files directly — the main session owns all file writes
**通过 Task 工具委派时**：
- 提供：系统名、游戏概念摘要、依赖 GDD 摘录、当前段落、需要专家输入的问题
- 智能体返回分析/提议给主会话
- 主会话通过 `AskUserQuestion` 将智能体输出展示给用户
- 用户决策；主会话写入文件
- 智能体不直接写文件 —— 主会话拥有所有文件写入

---

## 7. Recovery & Resume
## 7. 恢复与续作

If the session is interrupted (compaction, crash, new session):
若会话被中断（压缩、崩溃、新会话）：

1. Read `production/session-state/active.md` — it records the current system and
   which sections are complete
2. Read `design/gdd/[system-name].md` — sections with real content are done;
   sections with `[To be designed]` still need work
3. Resume from the next incomplete section — no need to re-discuss completed ones
1. 读取 `production/session-state/active.md` —— 它记录当前系统与哪些段落已完成
2. 读取 `design/gdd/[system-name].md` —— 有真实内容的段落已完成；含
   `[To be designed]` 的段落仍需工作
3. 从下一个未完成段落续作 —— 无需重新讨论已完成段落

This is why incremental writing matters: every approved section survives any
disruption.
这就是增量写入重要的原因：每个已批准段落能在任何中断中存活。

---

## Collaborative Protocol
## 协作协议

This skill follows the collaborative design principle at every step:
本技能在每个步骤遵循协作设计原则：

1. **Question -> Options -> Decision -> Draft -> Approval** for every section
2. **AskUserQuestion** at every decision point (Explain -> Capture pattern):
   - Phase 2: "Ready to start, or need more context?"
   - Phase 3: "May I create the skeleton?"
   - Phase 4 (each section): Design questions, approach options, draft approval
   - Phase 5: "Run design review? Update systems index? What's next?"
3. **"May I write to [filepath]?"** before the skeleton and before each section write
4. **Incremental writing**: Each section is written to file immediately after approval
5. **Session state updates**: After every section write
6. **Cross-referencing**: Every section checks existing GDDs for conflicts
7. **Specialist routing**: Complex sections get expert agent input, presented to
   the user for decision — never written silently
1. **Question -> Options -> Decision -> Draft -> Approval** 用于每个段落
2. **AskUserQuestion** 在每个决策点（Explain -> Capture 模式）：
   - 阶段 2："Ready to start, or need more context?"
   - 阶段 3："May I create the skeleton?"
   - 阶段 4（每段落）：设计问题、方案选项、草稿批准
   - 阶段 5："Run design review? Update systems index? What's next?"
3. **"May I write to [filepath]?"** 在骨架之前与每段落写入之前
4. **增量写入**：每个段落批准后立即写入文件
5. **会话状态更新**：每次段落写入后
6. **交叉引用**：每个段落检查现有 GDD 是否冲突
7. **专家路由**：复杂段落获专家智能体输入，展示给用户决策 —— 绝不默默写入

**Never** auto-generate the full GDD and present it as a fait accompli.
**Never** write a section without user approval.
**Never** contradict an existing approved GDD without flagging the conflict.
**Always** show where decisions come from (dependency GDDs, pillars, user choices).
**绝不**自动生成完整 GDD 并作为既成事实展示。
**绝不**未经用户批准写入段落。
**绝不**未标记冲突而与已批准 GDD 矛盾。
**始终**展示决策来源（依赖 GDD、支柱、用户选择）。

## Context Window Awareness
## 上下文窗口感知

This is a long-running skill. After writing each section, check if the status line
shows context at or above 70%. If so, append this notice to the response:
这是长运行技能。写入每段落后，检查状态行是否显示上下文达到或超过 70%。若是，
向响应追加此通知：

> **Context is approaching the limit (≥70%).** Your progress is saved — all approved
> sections are written to `design/gdd/[system-name].md`. When you're ready to continue,
> open a fresh Claude Code session and run `/design-system [system-name]` — it will
> detect which sections are complete and resume from the next one.
> **上下文即将达到上限（≥70%）。** 您的进度已保存 —— 所有已批准段落已写入
> `design/gdd/[system-name].md`。准备好继续时，打开新的 Claude Code 会话并运行
> `/design-system [system-name]` —— 它会检测哪些段落已完成并从下一个续作。

---

## Recommended Next Steps
## 推荐下一步

- Run `/design-review design/gdd/[system-name].md` in a **fresh session** to validate the completed GDD independently
- Run `/consistency-check` to verify this GDD's values don't conflict with other GDDs
- Run `/map-systems next` to move to the next highest-priority undesigned system
- Run `/gate-check pre-production` when all MVP GDDs are authored and reviewed
- 在**新会话**中运行 `/design-review design/gdd/[system-name].md` 独立验证完成的 GDD
- 运行 `/consistency-check` 验证此 GDD 的值与其他 GDD 不冲突
- 运行 `/map-systems next` 移至下一个最高优先级的未设计系统
- 当所有 MVP GDD 已编写并评审后运行 `/gate-check pre-production`
