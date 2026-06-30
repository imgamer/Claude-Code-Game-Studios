---
name: team-combat
description: "Orchestrate the combat team: coordinates game-designer, gameplay-programmer, ai-programmer, technical-artist, sound-designer, and qa-tester to design, implement, and validate a combat feature end-to-end."
argument-hint: "[combat feature description] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---
---
名称：team-combat
描述："编排战斗团队：协调 game-designer、gameplay-programmer、ai-programmer、technical-artist、sound-designer 和 qa-tester，端到端地设计、实施和验证战斗功能。"
argument-hint："[combat feature description] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model：sonnet
---

**Argument check:** If no combat feature description is provided, output:
**参数检查：** 若未提供战斗功能描述，输出：
> "Usage: `/team-combat [combat feature description]` — Provide a description of the combat feature to design and implement (e.g., `melee parry system`, `ranged weapon spread`)."
> "用法：`/team-combat [combat feature description]` —— 提供要设计和实施的战斗功能描述（例如 `melee parry system`、`ranged weapon spread`）。"

Then stop immediately without spawning any subagents or reading any files.
然后立即停止，不派生任何子智能体或读取任何文件。

When this skill is invoked with a valid argument, orchestrate the combat team through a structured pipeline.
当此技能以有效参数被调用时，通过结构化流水线编排战斗团队。

**Decision Points:** At each phase transition, use `AskUserQuestion` to present
the user with the subagent's proposals as selectable options. Write the agent's
full analysis in conversation, then capture the decision with concise labels.
The user must approve before moving to the next phase.
**决策点：** 在每个阶段转换时，使用 `AskUserQuestion` 向用户呈现子智能体的提议作为可选选项。在对话中写入智能体的完整分析，然后用简明标签捕获决策。用户必须在进入下一阶段前批准。

## Phase 0: Resolve Review Mode
## 阶段 0：解析评审模式

1. If `--review [mode]` was passed as an argument, use that mode.
1. 若作为参数传入了 `--review [mode]`，使用该模式。
2. Else read `production/review-mode.txt` — use whatever is written there.
2. 否则读取 `production/review-mode.txt` —— 使用其中所写的内容。
3. Else default to `lean`.
3. 否则默认为 `lean`。

Modes:
模式：
- `full` — spawn all director and lead gates as described
- `full` —— 如描述派生所有总监和主管门禁
- `lean` — skip director gates unless they are PHASE-GATE type (CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE)
- `lean` —— 跳过总监门禁，除非它们是 PHASE-GATE 类型（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE）
- `solo` — skip all director gate spawning entirely; run the skill without any agent gates
- `solo` —— 完全跳过所有总监门禁派生；在无任何智能体门禁下运行技能

Store the resolved mode for use in all subsequent phases.
存储解析后的模式以供所有后续阶段使用。

## Team Composition
## 团队组成
- **game-designer** — Design the mechanic, define formulas and edge cases
- **game-designer** —— 设计机制，定义公式和边界情况
- **gameplay-programmer** — Implement the core gameplay code
- **gameplay-programmer** —— 实施核心游戏玩法代码
- **ai-programmer** — Implement NPC/enemy AI behavior for the feature
- **ai-programmer** —— 为该功能实施 NPC/敌人 AI 行为
- **technical-artist** — Create VFX, shader effects, and visual feedback
- **technical-artist** —— 创建 VFX、着色器效果和视觉反馈
- **sound-designer** — Define audio events, impact sounds, and ambient combat audio
- **sound-designer** —— 定义音频事件、撞击音效和环境战斗音频
- **engine specialist** (primary) — Validate architecture and implementation patterns are idiomatic for the engine (read from `.claude/docs/technical-preferences.md` Engine Specialists section)
- **engine specialist**（主要）—— 验证架构和实施模式对引擎是惯用的（从 `.claude/docs/technical-preferences.md` 的 Engine Specialists 章节读取）
- **qa-tester** — Write test cases and validate the implementation
- **qa-tester** —— 编写测试用例并验证实施

## How to Delegate
## 如何委派

Use the Task tool to spawn each team member as a subagent:
使用 Task 工具将每个团队成员派生为子智能体：
- `subagent_type: game-designer` — Design the mechanic, define formulas and edge cases
- `subagent_type: game-designer` —— 设计机制，定义公式和边界情况
- `subagent_type: gameplay-programmer` — Implement the core gameplay code
- `subagent_type: gameplay-programmer` —— 实施核心游戏玩法代码
- `subagent_type: ai-programmer` — Implement NPC/enemy AI behavior
- `subagent_type: ai-programmer` —— 实施 NPC/敌人 AI 行为
- `subagent_type: technical-artist` — Create VFX, shader effects, visual feedback
- `subagent_type: technical-artist` —— 创建 VFX、着色器效果、视觉反馈
- `subagent_type: sound-designer` — Define audio events, impact sounds, ambient audio
- `subagent_type: sound-designer` —— 定义音频事件、撞击音效、环境音频
- `subagent_type: [primary engine specialist]` — Engine idiom validation for architecture and implementation
- `subagent_type: [primary engine specialist]` —— 针对架构和实施的引擎惯用法验证
- `subagent_type: qa-tester` — Write test cases and validate implementation
- `subagent_type: qa-tester` —— 编写测试用例并验证实施

Always provide full context in each agent's prompt (design doc path, relevant code files, constraints). Launch independent agents in parallel where the pipeline allows it (e.g., Phase 3 agents can run simultaneously).
始终在每个智能体的提示中提供完整上下文（设计文档路径、相关代码文件、约束）。在流水线允许的地方并行启动独立智能体（例如，阶段 3 的智能体可同时运行）。

## Pipeline
## 流水线

### Phase 1: Design
### 阶段 1：设计
Delegate to **game-designer**:
委派给 **game-designer**：
- Create or update the design document in `design/gdd/` covering: mechanic overview, player fantasy, detailed rules, formulas with variable definitions, edge cases, dependencies, tuning knobs with safe ranges, and acceptance criteria
- 在 `design/gdd/` 中创建或更新设计文档，涵盖：机制概述、玩家幻想、详细规则、带变量定义的公式、边界情况、依赖、带安全范围的调参旋钮和验收标准
- Output: completed design document
- 输出：完成的设计文档

### Phase 2: Architecture
### 阶段 2：架构
Delegate to **gameplay-programmer** (with **ai-programmer** if AI is involved):
委派给 **gameplay-programmer**（若涉及 AI，则连同 **ai-programmer**）：
- Review the design document
- 评审设计文档
- Design the code architecture: class structure, interfaces, data flow
- 设计代码架构：类结构、接口、数据流
- Identify integration points with existing systems
- 标识与现有系统的集成点
- Output: architecture sketch with file list and interface definitions
- 输出：带文件列表和接口定义的架构草图

Then spawn the **primary engine specialist** to validate the proposed architecture:
然后派生 **主要引擎专家** 验证提议的架构：
- Is the class/node/component structure idiomatic for the pinned engine? (e.g., Godot node hierarchy, Unity MonoBehaviour vs DOTS, Unreal Actor/Component design)
- 类/节点/组件结构对所固定的引擎是否惯用？（例如 Godot 节点层次结构、Unity MonoBehaviour vs DOTS、Unreal Actor/Component 设计）
- Are there engine-native systems that should be used instead of custom implementations?
- 是否有应使用而非自定义实施的引擎原生系统？
- Any proposed APIs that are deprecated or changed in the pinned engine version?
- 提议的 API 中是否有在所固定引擎版本中已弃用或变更的？
- Output: engine architecture notes — incorporate into the architecture before Phase 3 begins
- 输出：引擎架构笔记 —— 在阶段 3 开始前纳入架构

Use `AskUserQuestion`:
使用 `AskUserQuestion`：
- Prompt: "Architecture sketch complete. Approve to proceed with parallel implementation."
- 提示："架构草图完成。批准进入并行实施。"
- Options:
- 选项：
  - `[A] Proceed — spawn implementation agents (gameplay-programmer, ai-programmer, technical-artist, sound-designer)`
  - `[A] 继续 —— 派生实施智能体（gameplay-programmer、ai-programmer、technical-artist、sound-designer）`
  - `[B] Revise the architecture first — I'll describe what needs to change`
  - `[B] 先修订架构 —— 我来描述需要变更的内容`
  - `[C] Stop here — I'll continue later`
  - `[C] 在此停止 —— 我稍后继续`

Only spawn implementation agents if user selects [A].
仅当用户选择 [A] 时才派生实施智能体。

### Phase 3: Implementation (parallel where possible)
### 阶段 3：实施（可能时并行）
Delegate in parallel:
并行委派：
- **gameplay-programmer**: Implement core combat mechanic code
- **gameplay-programmer**：实施核心战斗机制代码
- **ai-programmer**: Implement AI behaviors (if the feature involves NPC reactions)
- **ai-programmer**：实施 AI 行为（若该功能涉及 NPC 反应）
- **technical-artist**: Create VFX and shader effects
- **technical-artist**：创建 VFX 和着色器效果
- **sound-designer**: Define audio event list and mixing notes
- **sound-designer**：定义音频事件列表和混音笔记

### Phase 4: Integration
### 阶段 4：集成
- Wire together gameplay code, AI, VFX, and audio
- 将游戏玩法代码、AI、VFX 和音频连接在一起
- Ensure all tuning knobs are exposed and data-driven
- 确保所有调参旋钮已暴露且数据驱动
- Verify the feature works with existing combat systems
- 验证该功能与现有战斗系统协同工作

### Phase 5: Validation
### 阶段 5：验证
Delegate to **qa-tester**:
委派给 **qa-tester**：
- Write test cases from the acceptance criteria
- 根据验收标准编写测试用例
- Test all edge cases documented in the design
- 测试设计中记录的所有边界情况
- Verify performance impact is within budget
- 验证性能影响在预算内
- File bug reports for any issues found
- 为发现的任何问题提交 bug 报告

### Phase 6: Sign-off
### 阶段 6：签字
- Collect results from all team members
- 收集所有团队成员的结果
- Report feature status: COMPLETE / NEEDS WORK / BLOCKED
- 报告功能状态：COMPLETE / NEEDS WORK / BLOCKED
- List any outstanding issues and their assigned owners
- 列出任何未决问题及其指定负责人

## Error Recovery Protocol
## 错误恢复协议

If any spawned agent (via Task) returns BLOCKED, errors, or cannot complete:
若任何派生的智能体（通过 Task）返回 BLOCKED、错误或无法完成：

1. **Surface immediately**: Report "[AgentName]: BLOCKED — [reason]" to the user before continuing to dependent phases
1. **立即暴露**：在继续依赖阶段前向用户报告 "[AgentName]：BLOCKED —— [原因]"
2. **Assess dependencies**: Check whether the blocked agent's output is required by subsequent phases. If yes, do not proceed past that dependency point without user input.
2. **评估依赖**：检查受阻智能体的输出是否被后续阶段所需。若是，未经用户输入不要越过该依赖点继续。
3. **Offer options** via AskUserQuestion with choices:
3. **通过 AskUserQuestion 提供选项**：
   - Skip this agent and note the gap in the final report
   - 跳过此智能体并在最终报告中注明缺口
   - Retry with narrower scope
   - 以更窄范围重试
   - Stop here and resolve the blocker first
   - 在此停止并先解决阻碍
4. **Always produce a partial report** — output whatever was completed. Never discard work because one agent blocked.
4. **始终产生部分报告** —— 输出已完成的内容。绝不因一个智能体受阻而丢弃工作。

Common blockers:
常见阻碍：
- Input file missing (story not found, GDD absent) → redirect to the skill that creates it
- 输入文件缺失（未找到故事、GDD 缺失）→ 重定向到创建它的技能
- ADR status is Proposed → do not implement; run `/architecture-decision` first
- ADR 状态为 Proposed → 不要实施；先运行 `/architecture-decision`
- Scope too large → split into two stories via `/create-stories`
- 范围太大 → 通过 `/create-stories` 拆分为两个故事
- Conflicting instructions between ADR and story → surface the conflict, do not guess
- ADR 和故事之间的指令冲突 → 暴露冲突，不要猜测

## File Write Protocol
## 文件写入协议

All file writes (design documents, implementation files, test cases) are
delegated to sub-agents spawned via Task. Each sub-agent enforces the
"May I write to [path]?" protocol. This orchestrator does not write files directly.
所有文件写入（设计文档、实施文件、测试用例）均委派给通过 Task 派生的子智能体。每个子智能体执行"我可以写入 [path] 吗？"协议。此编排器不直接写入文件。

## Output
## 输出

A summary report covering: design completion status, implementation status per team member, test results, and any open issues.
一份摘要报告，涵盖：设计完成状态、各团队成员的实施状态、测试结果和任何未决问题。

Verdict: **COMPLETE** — combat feature designed, implemented, and validated.
裁定：**COMPLETE** —— 战斗功能已设计、实施和验证。
Verdict: **BLOCKED** — one or more phases could not complete; partial report produced with unresolved items listed.
裁定：**BLOCKED** —— 一个或多个阶段无法完成；产生部分报告并列出未决项。

## Next Steps
## 后续步骤

- Run `/code-review` on the implemented combat code before closing stories.
- 在关闭故事前对实施的战斗代码运行 `/code-review`。
- Run `/balance-check` to validate combat formulas and tuning values.
- 运行 `/balance-check` 验证战斗公式和调参值。
- Run `/team-polish` if VFX, audio, or performance polish is needed.
- 若需要 VFX、音频或性能打磨，运行 `/team-polish`。
