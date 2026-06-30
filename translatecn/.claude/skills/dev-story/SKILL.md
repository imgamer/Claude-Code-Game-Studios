---
name: dev-story
description: "Read a story file and implement it. Loads the full context (story, GDD requirement, ADR guidelines, control manifest), routes to the right programmer agent for the system and engine, implements the code and test, and confirms each acceptance criterion. The core implementation skill — run after /story-readiness, before /code-review and /story-done."
argument-hint: "[story-path]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, Task, AskUserQuestion
model: sonnet
---
---
名称：dev-story
描述："读取故事文件并实现它。加载完整上下文（故事、GDD 需求、ADR 指南、控制清单），路由到适合系统与引擎的程序员智能体，实现代码与测试，并确认每个验收标准。核心实现技能 —— 在 /story-readiness 之后、/code-review 与 /story-done 之前运行。"
argument-hint："[story-path]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Bash, Task, AskUserQuestion
model：sonnet
---

# Dev Story
# Dev Story

This skill bridges planning and code. It reads a story file in full, assembles
all the context a programmer needs, routes to the correct specialist agent, and
drives implementation to completion — including writing the test.
本技能连接规划与代码。它完整读取故事文件，组装程序员所需的所有上下文，路由到
正确的专家智能体，并驱动实现直至完成 —— 包括编写测试。

**The loop for every story:**
**每个故事的循环：**
```
/qa-plan sprint           ← define test requirements before sprint begins
/story-readiness [path]   ← validate before starting
/dev-story [path]         ← implement it  (this skill)
/code-review [files]      ← review it
/story-done [path]        ← verify and close it
```
```
/qa-plan sprint           ← define test requirements before sprint begins
/story-readiness [path]   ← validate before starting
/dev-story [path]         ← implement it  (this skill)
/code-review [files]      ← review it
/story-done [path]        ← verify and close it
```

**After all sprint stories are done:** run `/team-qa sprint` to execute the full QA cycle and get a sign-off verdict before advancing the project stage.
**所有冲刺故事完成后：** 运行 `/team-qa sprint` 执行完整 QA 周期并在推进项目阶段
之前获取签收结论。

**Output:** Source code + test file in the project's `src/` and `tests/` directories.
**产出：** 项目 `src/` 与 `tests/` 目录中的源代码 + 测试文件。

---

## Phase 1: Find the Story
## 阶段 1：查找故事

**If a path is provided**: read that file directly.
**若提供路径**：直接读取该文件。

**If no argument**: check `production/session-state/active.md` for the active
story. If found, confirm: "Continuing work on [story title] — is that correct?"
If not found, ask: "Which story are we implementing?" Glob
`production/epics/**/*.md` and list stories with Status: Ready.
**若无参数**：检查 `production/session-state/active.md` 中的活动故事。若找到，
确认："Continuing work on [story title] — is that correct?"
若未找到，询问："Which story are we implementing?" Glob
`production/epics/**/*.md` 并列出 Status: Ready 的故事。

---

## Phase 2: Load Full Context
## 阶段 2：加载完整上下文

**Before loading any context, verify required files exist.** Extract the ADR path from the story's `ADR Governing Implementation` field, then check:
**加载任何上下文之前，验证必需文件存在。** 从故事的 `ADR Governing Implementation`
字段提取 ADR 路径，然后检查：

| File | Path | If missing |
|------|------|------------|
| TR registry | `docs/architecture/tr-registry.yaml` | **STOP** — "TR registry not found at `docs/architecture/tr-registry.yaml`. Run `/architecture-review` to bootstrap the registry from your GDDs and ADRs." |
| Governing ADR | path from story's ADR field | **STOP** — "ADR file [path] not found. Run `/architecture-decision` to create it, or correct the filename in the story's ADR field." |
| Control manifest | `docs/architecture/control-manifest.md` | **WARN and continue** — "Control manifest not found — layer rules cannot be checked. Run `/create-control-manifest`." |
| 文件 | 路径 | 若缺失 |
|------|------|------------|
| TR 注册表 | `docs/architecture/tr-registry.yaml` | **停止** —— "TR registry not found at `docs/architecture/tr-registry.yaml`. Run `/architecture-review` to bootstrap the registry from your GDDs and ADRs." |
| 治理 ADR | 故事 ADR 字段中的路径 | **停止** —— "ADR file [path] not found. Run `/architecture-decision` to create it, or correct the filename in the story's ADR field." |
| 控制清单 | `docs/architecture/control-manifest.md` | **警告并继续** —— "Control manifest not found — layer rules cannot be checked. Run `/create-control-manifest`." |

If the TR registry or governing ADR is missing, set the story status to **BLOCKED** in the session state and do not spawn any programmer agent.
若 TR 注册表或治理 ADR 缺失，在会话状态中将故事状态设为 **BLOCKED** 且不启动
任何程序员智能体。

Read all of the following simultaneously — these are independent reads. Do not start implementation until all context is loaded:
同时读取以下所有内容 —— 这些是独立读取。所有上下文加载完成之前不要开始实现：

### The story file
### 故事文件

Extract and hold:
- **Story title, ID, layer, type** (Logic / Integration / Visual/Feel / UI / Config/Data)
- **TR-ID** — the GDD requirement identifier
- **Governing ADR** reference
- **Manifest Version** embedded in story header
- **Acceptance Criteria** — every checkbox item, verbatim
- **Implementation Notes** — the ADR guidance section in the story
- **Out of Scope** boundaries
- **Test Evidence** — the required test file path
- **Dependencies** — what must be DONE before this story
提取并保留：
- **故事标题、ID、层级、类型**（Logic / Integration / Visual/Feel / UI /
  Config/Data）
- **TR-ID** —— GDD 需求标识符
- **治理 ADR** 引用
- **嵌入故事头的 Manifest Version**
- **Acceptance Criteria** —— 每个复选框项，逐字
- **Implementation Notes** —— 故事中的 ADR 指南段落
- **Out of Scope** 边界
- **Test Evidence** —— 所需测试文件路径
- **Dependencies** —— 此故事之前必须完成什么

### The TR registry
### TR 注册表

Read `docs/architecture/tr-registry.yaml`. Look up the story's TR-ID.
Read the current `requirement` text — this is the source of truth for what the
GDD requires now. Do not rely on any inline text in the story file (may be stale).
读取 `docs/architecture/tr-registry.yaml`。查找故事的 TR-ID。
读取当前 `requirement` 文本 —— 这是 GDD 现在所要求的真相源。不要依赖故事文件
中的任何内联文本（可能过期）。

### The governing ADR
### 治理 ADR

Read `docs/architecture/[adr-file].md`. Extract:
- The full Decision section
- The Implementation Guidelines section (this is what the programmer follows)
- The Engine Compatibility section (post-cutoff APIs, known risks)
- The ADR Dependencies section
读取 `docs/architecture/[adr-file].md`。提取：
- 完整 Decision 段落
- Implementation Guidelines 段落（程序员遵循的内容）
- Engine Compatibility 段落（截止后 API、已知风险）
- ADR Dependencies 段落

### The control manifest
### 控制清单

Read `docs/architecture/control-manifest.md`. Extract the rules for this story's layer:
- Required patterns
- Forbidden patterns
- Performance guardrails
读取 `docs/architecture/control-manifest.md`。提取此故事层级的规则：
- 必需模式
- 禁止模式
- 性能护栏

Check: does the story's embedded Manifest Version match the current manifest header date?
If they differ, use `AskUserQuestion` before proceeding:
- Prompt: "Story was written against manifest v[story-date]. Current manifest is v[current-date]. New rules may apply. How do you want to proceed?"
- Options:
  - `[A] Update story manifest version and implement with current rules (Recommended)`
  - `[B] Implement with old rules — I accept the risk of non-compliance`
  - `[C] Stop here — I want to review the manifest diff first`
检查：故事嵌入的 Manifest Version 是否与当前清单头日期匹配？
若不同，继续之前使用 `AskUserQuestion`：
- 提示："Story was written against manifest v[story-date]. Current manifest is v[current-date]. New rules may apply. How do you want to proceed?"
- 选项：
  - `[A] Update story manifest version and implement with current rules (Recommended)`
  - `[B] Implement with old rules — I accept the risk of non-compliance`
  - `[C] Stop here — I want to review the manifest diff first`

If [A]: edit the story file's `Manifest Version:` field to the current manifest date before spawning the programmer. Then read the manifest carefully for new rules.
If [B]: edit the story file's `Manifest Version:` field to the current manifest date AND add a `Manifest-Note: Proceeded with old manifest rules on [date] — non-compliance risk accepted.` line to the story header. Read the manifest for new rules anyway. Note the decision in the Phase 6 summary under "Deviations". `/story-done` will include the Manifest-Note in its deviations section without re-checking staleness.
If [C]: stop. Do not spawn any agent. Let the user review and re-run `/dev-story`.
若 [A]：在启动程序员之前将故事文件的 `Manifest Version:` 字段编辑为当前清单
日期。然后仔细读取清单寻找新规则。
若 [B]：将故事文件的 `Manifest Version:` 字段编辑为当前清单日期，并向故事头
添加 `Manifest-Note: Proceeded with old manifest rules on [date] — non-compliance risk accepted.` 行。无论如何读取清单寻找新规则。在阶段 6 摘要的 "Deviations"
下记录此决策。`/story-done` 会在其 deviations 段落包含 Manifest-Note 而不
重新检查过期。
若 [C]：停止。不启动任何智能体。让用户评审并重新运行 `/dev-story`。

### Dependency validation
### 依赖验证

After extracting the **Dependencies** list from the story file, validate each:
从故事文件提取 **Dependencies** 列表后，验证每个：

1. Glob `production/epics/**/*.md` to find each dependency story file.
2. Read its `Status:` field.
3. If any dependency has Status other than `Complete` or `Done`:
   - Use `AskUserQuestion`:
     - Prompt: "Story '[current story]' depends on '[dependency title]' which is currently [status], not Complete. How do you want to proceed?"
     - Options:
       - `[A] Proceed anyway — I accept the dependency risk`
       - `[B] Stop — I'll complete the dependency first`
       - `[C] The dependency is done but status wasn't updated — mark it Complete and continue`
   - If [B]: set story status to **BLOCKED** in session state and stop. Do not spawn any programmer agent.
   - If [C]: ask "May I update [dependency path] Status to Complete?" before continuing.
   - If [A]: note in Phase 6 summary under "Deviations": "Implemented with incomplete dependency: [dependency title] — [status]."
1. Glob `production/epics/**/*.md` 查找每个依赖故事文件。
2. 读取其 `Status:` 字段。
3. 若任何依赖 Status 不是 `Complete` 或 `Done`：
   - 使用 `AskUserQuestion`：
     - 提示："Story '[current story]' depends on '[dependency title]' which is currently [status], not Complete. How do you want to proceed?"
     - 选项：
       - `[A] Proceed anyway — I accept the dependency risk`
       - `[B] Stop — I'll complete the dependency first`
       - `[C] The dependency is done but status wasn't updated — mark it Complete and continue`
   - 若 [B]：在会话状态中设故事状态为 **BLOCKED** 并停止。不启动任何程序员
     智能体。
   - 若 [C]：继续之前询问 "May I update [dependency path] Status to Complete?"。
   - 若 [A]：在阶段 6 摘要的 "Deviations" 下注明："Implemented with incomplete dependency: [dependency title] — [status]."

If a dependency file cannot be found: warn "Dependency story not found: [path]. Verify the path or create the story file."
若依赖文件找不到：警告 "Dependency story not found: [path]. Verify the path or create the story file."

---

### Engine reference
### 引擎参考

Read `.claude/docs/technical-preferences.md`:
- `Engine:` value — determines which programmer agents to use
- Naming conventions (class names, file names, signal/event names)
- Performance budgets (frame budget, memory ceiling)
- Forbidden patterns
读取 `.claude/docs/technical-preferences.md`：
- `Engine:` 值 —— 决定使用哪些程序员智能体
- 命名约定（类名、文件名、信号/事件名）
- 性能预算（帧预算、内存上限）
- 禁止模式

### Mark Story In Progress
### 标记故事进行中

Silently update two things before spawning any agent:
启动任何智能体之前静默更新两件事：

1. **`production/sprint-status.yaml`** (if it exists): find the entry matching this story's file path and set `status: in_progress`. Update the top-level `updated` field to today's date. If the file does not exist, skip silently.
1. **`production/sprint-status.yaml`**（若存在）：找到匹配此故事文件路径的条目
   并设 `status: in_progress`。更新顶层 `updated` 字段为今日日期。若文件不存在，
   静默跳过。

2. **The story file itself**: edit the `Last Updated:` field in the story header to today's date (format: `YYYY-MM-DD`). If the field does not exist in the story header, add it after the `Status:` line. This enables sprint-status staleness detection for this story.
2. **故事文件本身**：编辑故事头的 `Last Updated:` 字段为今日日期（格式：
   `YYYY-MM-DD`）。若故事头中无此字段，在 `Status:` 行后添加。这为此故事启用
   sprint-status 过期检测。

---

## Phase 3: Route to the Right Programmer
## 阶段 3：路由到正确的程序员

Based on the story's **Layer**, **Type**, and **system name**, determine which
specialist to spawn via Task.
基于故事的 **Layer**、**Type** 与 **system name**，决定通过 Task 启动哪个
专家。

**Config/Data stories — skip agent spawning entirely:**
If the story's Type is `Config/Data`, no programmer agent or engine specialist is needed. Jump directly to Phase 4 (Config/Data note). The implementation is a data file edit — no routing table evaluation, no engine specialist.
**Config/Data 故事 —— 完全跳过智能体启动：**
若故事 Type 为 `Config/Data`，不需要程序员智能体或引擎专家。直接跳到阶段 4
（Config/Data 注）。实现是数据文件编辑 —— 无路由表评估，无引擎专家。

### Primary agent routing table
### 主智能体路由表

| Story context | Primary agent |
|---|---|
| Foundation layer — any type | `engine-programmer` |
| Any layer — Type: UI | `ui-programmer` |
| Any layer — Type: Visual/Feel | `gameplay-programmer` (implements) |
| Core or Feature — gameplay mechanics | `gameplay-programmer` |
| Core or Feature — AI behaviour, pathfinding | `ai-programmer` |
| Core or Feature — networking, replication | `network-programmer` |
| Config/Data — no code | No agent needed (see Phase 4 Config note) |
| 故事上下文 | 主智能体 |
|---|---|
| Foundation 层 —— 任何类型 | `engine-programmer` |
| 任何层 —— Type: UI | `ui-programmer` |
| 任何层 —— Type: Visual/Feel | `gameplay-programmer`（实现） |
| Core 或 Feature —— 玩法机制 | `gameplay-programmer` |
| Core 或 Feature —— AI 行为、寻路 | `ai-programmer` |
| Core 或 Feature —— 网络、复制 | `network-programmer` |
| Config/Data —— 无代码 | 无需智能体（见阶段 4 Config 注） |

### Engine specialist — always spawn as secondary for code stories
### 引擎专家 —— 对代码故事始终作为次要启动

Read the `Engine Specialists` section of `.claude/docs/technical-preferences.md`
to get the configured primary specialist. Spawn them alongside the primary agent
when the story involves engine-specific APIs, patterns, or the ADR has HIGH
engine risk.
读取 `.claude/docs/technical-preferences.md` 的 `Engine Specialists` 段落获取
配置的主专家。当故事涉及引擎特定 API、模式或 ADR 有 HIGH 引擎风险时，与主
智能体一起启动。

| Engine | Specialist agents available |
|--------|----------------------------|
| Godot 4 | `godot-specialist`, `godot-gdscript-specialist`, `godot-shader-specialist` |
| Unity | `unity-specialist`, `unity-ui-specialist`, `unity-shader-specialist` |
| Unreal Engine | `unreal-specialist`, `ue-gas-specialist`, `ue-blueprint-specialist`, `ue-umg-specialist`, `ue-replication-specialist` |
| 引擎 | 可用专家智能体 |
|--------|----------------------------|
| Godot 4 | `godot-specialist`, `godot-gdscript-specialist`, `godot-shader-specialist` |
| Unity | `unity-specialist`, `unity-ui-specialist`, `unity-shader-specialist` |
| Unreal Engine | `unreal-specialist`, `ue-gas-specialist`, `ue-blueprint-specialist`, `ue-umg-specialist`, `ue-replication-specialist` |

**When engine risk is HIGH** (from the ADR or VERSION.md): always spawn the engine
specialist, even for non-engine-facing stories. High risk means the ADR records
assumptions about post-cutoff engine APIs that need expert verification.
**当引擎风险为 HIGH**（来自 ADR 或 VERSION.md）：即使对非引擎面向的故事也始终
启动引擎专家。HIGH 风险意味着 ADR 记录了对截止后引擎 API 的假设，需要专家
验证。

---

## Phase 4: Implement
## 阶段 4：实现

Spawn the chosen programmer agent(s) via Task with the full context package:
通过 Task 启动所选程序员智能体并附完整上下文包：

Brief the agent with file paths and targeted reading instructions — do not serialize document content into the Task prompt. The agent reads what it needs directly:
用文件路径与定向读取指令简报智能体 —— 不要将文档内容序列化到 Task 提示。
智能体直接读取所需：

1. **Story file**: `[story-path]` — read in full
2. **GDD requirement**: look up TR-ID `[TR-XXX-NNN]` in `docs/architecture/tr-registry.yaml` — use the `requirement` field as source of truth
3. **ADR**: `docs/architecture/[adr-file].md` — read the **Decision** and **Implementation Guidelines** sections only
4. **Control manifest**: `docs/architecture/control-manifest.md` — read rules for the **[layer]** layer only
5. **Engine preferences**: `.claude/docs/technical-preferences.md` — read naming conventions and performance budgets
6. **Test file path**: `[path from story's Test Evidence section]` — this file must be created as part of implementation
7. **Test requirement** (Logic and Integration stories only): The test file MUST be created at `[path from the story's Test Evidence section]`. Write the test alongside the implementation — do not defer it. The story cannot be closed via `/story-done` without this file present. Each acceptance criterion must have at least one test function covering it. Test file naming: `[system]_[feature]_test.[ext]`. Function naming: `test_[scenario]_[expected_outcome]`. No random seeds, no time-dependent assertions, no external I/O.
8. **Explicit instruction**: implement this story following the ADR guidelines, respect the manifest rules, stay within the story's Out of Scope boundaries. Write clean, doc-commented public APIs.
1. **故事文件**：`[story-path]` —— 完整读取
2. **GDD 需求**：在 `docs/architecture/tr-registry.yaml` 中查找 TR-ID
   `[TR-XXX-NNN]` —— 用 `requirement` 字段作为真相源
3. **ADR**：`docs/architecture/[adr-file].md` —— 仅读 **Decision** 与
   **Implementation Guidelines** 段落
4. **控制清单**：`docs/architecture/control-manifest.md` —— 仅读 **[layer]**
   层规则
5. **引擎偏好**：`.claude/docs/technical-preferences.md` —— 读命名约定与性能
   预算
6. **测试文件路径**：`[来自故事 Test Evidence 段落的路径]` —— 此文件必须作为
   实现一部分创建
7. **测试要求**（仅 Logic 与 Integration 故事）：测试文件必须创建在
   `[来自故事 Test Evidence 段落的路径]`。与实现一起写测试 —— 不要推迟。没有
   此文件故事无法通过 `/story-done` 关闭。每个验收标准必须至少有一个测试函数
   覆盖。测试文件命名：`[system]_[feature]_test.[ext]`。函数命名：
   `test_[scenario]_[expected_outcome]`。无随机种子、无时间依赖断言、无外部 I/O。
8. **显式指令**：遵循 ADR 指南实现此故事，尊重清单规则，停留在故事 Out of
   Scope 边界内。写干净、带文档注释的公共 API。

The agent should:
- Create or modify files in `src/` following the ADR guidelines
- Respect all Required and Forbidden patterns from the control manifest
- Stay within the story's Out of Scope boundaries (do not touch unrelated files)
- Write clean, doc-commented public APIs
智能体应：
- 按 ADR 指南在 `src/` 创建或修改文件
- 尊重控制清单的所有 Required 与 Forbidden 模式
- 停留在故事 Out of Scope 边界内（不要触碰无关文件）
- 写干净、带文档注释的公共 API

### Config/Data stories (no agent needed)
### Config/Data 故事（无需智能体）

For Type: Config/Data stories, no programmer agent is required. The implementation
is editing a data file. Read the story's acceptance criteria and make the specified
changes to the data file directly. Note which values were changed and what they
changed from/to.
对 Type: Config/Data 故事，不需要程序员智能体。实现是编辑数据文件。读取故事
验收标准并直接对数据文件做指定更改。注明哪些值改了、从什么改到什么。

### Visual/Feel stories
### Visual/Feel 故事

Spawn `gameplay-programmer` to implement the code/animation calls. Note that
Visual/Feel acceptance criteria cannot be auto-verified — the "does it feel right?"
check happens in `/story-done` via manual confirmation.
启动 `gameplay-programmer` 实现代码/动画调用。注意 Visual/Feel 验收标准无法
自动验证 —— "does it feel right?" 检查在 `/story-done` 中通过手动确认进行。

---

## Phase 5: Test Evidence Requirements
## 阶段 5：测试证据要求

The test requirement was included in the Phase 4 programmer agent brief (item 7). This phase summarizes what evidence each story type requires — used when collecting the Phase 6 summary.
测试要求已包含在阶段 4 程序员智能体简报（条目 7）。此阶段总结每种故事类型
要求什么证据 —— 用于收集阶段 6 摘要时。

| Story Type | Required Evidence | Notes |
|---|---|---|
| **Logic** | Automated unit test at path from story's Test Evidence section | BLOCKING — included in Phase 4 agent brief |
| **Integration** | Integration test OR documented playtest record | BLOCKING — included in Phase 4 agent brief |
| **Visual/Feel** | Evidence doc at `production/qa/evidence/[slug]-evidence.md` | ADVISORY — note in Phase 6 summary |
| **UI** | Manual walkthrough doc or interaction test | ADVISORY — note in Phase 6 summary |
| **Config/Data** | None — smoke check serves as evidence | N/A |
| 故事类型 | 所需证据 | 注 |
|---|---|---|
| **Logic** | 故事 Test Evidence 段落路径中的自动化单元测试 | BLOCKING —— 包含在阶段 4 智能体简报 |
| **Integration** | 集成测试或文档化试玩记录 | BLOCKING —— 包含在阶段 4 智能体简报 |
| **Visual/Feel** | `production/qa/evidence/[slug]-evidence.md` 中的证据文档 | ADVISORY —— 在阶段 6 摘要注明 |
| **UI** | 手动 walkthrough 文档或交互测试 | ADVISORY —— 在阶段 6 摘要注明 |
| **Config/Data** | 无 —— smoke 检查作为证据 | N/A |

For Visual/Feel and UI stories, include in the Phase 6 summary: "Manual evidence required at `production/qa/evidence/[slug]-evidence.md` before this story can be fully closed."
对 Visual/Feel 与 UI 故事，在阶段 6 摘要中包含："Manual evidence required at `production/qa/evidence/[slug]-evidence.md` before this story can be fully closed."

---

## Phase 6: Collect and Summarise
## 阶段 6：收集并总结

After the programmer agent(s) complete, collect:
程序员智能体完成后，收集：

- Files created or modified (with paths)
- Test file created (path and number of test functions written)
- Any deviations from the story's Out of Scope boundary (flag these)
- Any questions or blockers the agent surfaced
- Any engine-specific risks the specialist flagged
- 创建或修改的文件（含路径）
- 创建的测试文件（路径与所写测试函数数）
- 偏离故事 Out of Scope 边界的任何内容（标记这些）
- 智能体提出的任何问题或阻塞
- 专家标记的任何引擎特定风险

Present a concise implementation summary:
展示简洁的实现摘要：

```
## Implementation Complete: [Story Title]

**Files changed**:
- `src/[path]` — created / modified ([brief description])
- `tests/[path]` — test file ([N] test functions)

**Acceptance criteria covered**:
- [x] [criterion] — implemented in [file:function]
- [x] [criterion] — covered by test [test_name]
- [ ] [criterion] — DEFERRED: requires playtest (Visual/Feel)

**Deviations from scope**: [None] or [list files touched outside story boundary]
**Engine risks flagged**: [None] or [specialist finding]
**Blockers**: [None] or [describe]

**Before running `/story-done`:** run your test suite locally and confirm the tests you wrote pass. `/story-done` will re-run them automatically, but a failing test discovered there means returning to implementation context.

Ready for: `/code-review [file1] [file2]` then `/story-done [story-path]`
```
```
## Implementation Complete: [Story Title]

**Files changed**:
- `src/[path]` — created / modified ([brief description])
- `tests/[path]` — test file ([N] test functions)

**Acceptance criteria covered**:
- [x] [criterion] — implemented in [file:function]
- [x] [criterion] — covered by test [test_name]
- [ ] [criterion] — DEFERRED: requires playtest (Visual/Feel)

**Deviations from scope**: [None] or [list files touched outside story boundary]
**Engine risks flagged**: [None] or [specialist finding]
**Blockers**: [None] or [describe]

**Before running `/story-done`:** run your test suite locally and confirm the tests you wrote pass. `/story-done` will re-run them automatically, but a failing test discovered there means returning to implementation context.

Ready for: `/code-review [file1] [file2]` then `/story-done [story-path]`
```

---

## Phase 7: Update Session State
## 阶段 7：更新会话状态

Silently append to `production/session-state/active.md`:
静默追加到 `production/session-state/active.md`：

```
## Session Extract — /dev-story [date]
- Story: [story-path] — [story title]
- Files changed: [comma-separated list]
- Test written: [path, or "None — Visual/Feel/Config story"]
- Blockers: [None, or description]
- Next: /code-review [files] then /story-done [story-path]
```
```
## Session Extract — /dev-story [date]
- Story: [story-path] — [story title]
- Files changed: [comma-separated list]
- Test written: [path, or "None — Visual/Feel/Config story"]
- Blockers: [None, or description]
- Next: /code-review [files] then /story-done [story-path]
```

Create `active.md` if it does not exist. Confirm: "Session state updated."
若 `active.md` 不存在则创建。确认："Session state updated."

---

## Error Recovery Protocol
## 错误恢复协议

If any spawned agent (via Task) returns BLOCKED, errors, or cannot complete:
若任何被启动智能体（通过 Task）返回 BLOCKED、错误或无法完成：

1. **Surface immediately**: Report "[AgentName]: BLOCKED — [reason]" to the user before continuing to dependent phases
2. **Assess dependencies**: Check whether the blocked agent's output is required by subsequent phases. If yes, do not proceed past that dependency point without user input.
3. **Offer options** via AskUserQuestion with choices:
   - Skip this agent and note the gap in the final report
   - Retry with narrower scope
   - Stop here and resolve the blocker first
4. **Always produce a partial report** — output whatever was completed. Never discard work because one agent blocked.
1. **立即暴露**：在继续到依赖阶段之前向用户报告 "[AgentName]: BLOCKED — [reason]"
2. **评估依赖**：检查被阻塞智能体的输出是否后续阶段所需。若是，无用户输入不
   越过该依赖点。
3. **通过 AskUserQuestion 提供选项**：
   - 跳过此智能体并在最终报告中注明缺漏
   - 以更窄范围重试
   - 在此停止并先解决阻塞
4. **始终产出部分报告** —— 输出已完成的内容。绝不因一个智能体阻塞而丢弃工作。

Common blockers:
- Input file missing (story not found, GDD absent) → redirect to the skill that creates it
- ADR status is Proposed → do not implement; run `/architecture-decision` first
- Scope too large → split into two stories via `/create-stories`
- Conflicting instructions between ADR and story → surface the conflict, do not guess
- Manifest version mismatch → show diff to user, ask whether to proceed with old rules or update story first
常见阻塞：
- 输入文件缺失（找不到故事、GDD 缺失）→ 重定向到创建它的技能
- ADR 状态为 Proposed → 不实现；先运行 `/architecture-decision`
- 范围过大 → 通过 `/create-stories` 拆为两个故事
- ADR 与故事指令冲突 → 暴露冲突，不要猜测
- 清单版本不匹配 → 向用户展示 diff，询问用旧规则继续还是先更新故事

## Collaborative Protocol
## 协作协议

- **File writes are delegated** — all source code, test files, and evidence docs are written by sub-agents spawned via Task. Each sub-agent enforces the "May I write to [path]?" protocol individually. This orchestrator does not write files directly.
- **Load before implementing** — do not start coding until all context is loaded
  (story, TR-ID, ADR, manifest, engine prefs). Incomplete context produces code
  that drifts from design.
- **The ADR is the law** — implementation must follow the ADR's Implementation
  Guidelines. If the guidelines conflict with what seems "better," flag it in the
  summary rather than silently deviating.
- **Stay in scope** — the Out of Scope section is a contract. If implementing
  the story requires touching an out-of-scope file, stop and surface it:
  "Implementing [criterion] requires modifying [file], which is out of scope.
  Shall I proceed or create a separate story?"
- **Test is not optional for Logic/Integration** — do not mark implementation
  complete without the test file existing
- **Visual/Feel criteria are deferred, not skipped** — mark them as DEFERRED
  in the summary; they will be manually verified in `/story-done`
- **Ask before large structural decisions** — if the story requires an
  architectural pattern not covered by the ADR, surface it before implementing:
  "The ADR doesn't specify how to handle [case]. My plan is [X]. Proceed?"
- **文件写入被委派** —— 所有源代码、测试文件与证据文档由通过 Task 启动的子
  智能体写入。每个子智能体单独执行 "May I write to [path]?" 协议。此编排器
  不直接写文件。
- **实现之前加载** —— 所有上下文加载完成之前不开始编码（故事、TR-ID、ADR、
  清单、引擎偏好）。不完整上下文会产生偏离设计的代码。
- **ADR 是法律** —— 实现必须遵循 ADR 的 Implementation Guidelines。若指南
  与看似 "更好" 的方案冲突，在摘要中标记而非默默偏离。
- **保持在范围内** —— Out of Scope 段落是契约。若实现故事需要触碰范围外文件，
  停下并暴露："Implementing [criterion] requires modifying [file], which is out
  of scope. Shall I proceed or create a separate story?"
- **测试对 Logic/Integration 不可选** —— 测试文件不存在则不标记实现完成
- **Visual/Feel 标准是推迟而非跳过** —— 在摘要中标记为 DEFERRED；它们将在
  `/story-done` 中手动验证
- **大型结构决策之前询问** —— 若故事需要 ADR 未覆盖的架构模式，实现之前暴露：
  "The ADR doesn't specify how to handle [case]. My plan is [X]. Proceed?"

---

## Recommended Next Steps
## 推荐下一步

- Run `/code-review [file1] [file2]` to review the implementation before closing the story
- Run `/story-done [story-path]` to verify acceptance criteria and mark the story complete
- After all sprint stories are done: run `/team-qa sprint` for the full QA cycle before advancing the project stage
- 运行 `/code-review [file1] [file2]` 在关闭故事之前评审实现
- 运行 `/story-done [story-path]` 验证验收标准并标记故事完成
- 所有冲刺故事完成后：在推进项目阶段之前运行 `/team-qa sprint` 做完整 QA 周期
