---
name: team-ui
description: "Orchestrate the UI team through the full UX pipeline: from UX spec authoring through visual design, implementation, review, and polish. Integrates with /ux-design, /ux-review, and studio UX templates."
argument-hint: "[UI feature description] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---
---
名称：team-ui
描述："通过完整 UX 流水线编排 UI 团队：从 UX 规范编写到视觉设计、实施、评审和打磨。与 /ux-design、/ux-review 和工作室 UX 模板集成。"
argument-hint："[UI 功能描述] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model：sonnet
---

When this skill is invoked, orchestrate the UI team through a structured pipeline.
当此技能被调用时，通过结构化的流水线编排 UI 团队。

**Decision Points:** At each phase transition, use `AskUserQuestion` to present
the user with the subagent's proposals as selectable options. Write the agent's
full analysis in conversation, then capture the decision with concise labels.
The user must approve before moving to the next phase.
**决策点：** 在每个阶段转换时，使用 `AskUserQuestion` 将子智能体的提案作为可选项呈现给用户。先将智能体的完整分析写入对话，然后用简洁的标签捕获决策。用户必须批准后才能进入下一阶段。

## Phase 0: Resolve Review Mode
## 阶段 0：解析评审模式

1. If `--review [mode]` was passed as an argument, use that mode.
2. Else read `production/review-mode.txt` — use whatever is written there.
3. Else default to `lean`.
1. 如果参数中传入了 `--review [mode]`，则使用该模式。
2. 否则读取 `production/review-mode.txt` — 使用其中写入的值。
3. 否则默认使用 `lean`。

Modes:
模式：

- `full` — spawn all director and lead gates as described
- `lean` — skip director gates unless they are PHASE-GATE type (CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE)
- `solo` — skip all director gate spawning entirely; run the skill without any agent gates
- `full` — 按描述生成所有总监和主管门禁
- `lean` — 跳过总监门禁，除非是 PHASE-GATE 类型（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE）
- `solo` — 完全跳过所有总监门禁生成；在没有任何智能体门禁的情况下运行技能

Store the resolved mode for use in all subsequent phases.
将解析出的模式存储起来，供所有后续阶段使用。

**Director gate skip rule**: Before spawning creative-director, art-director, or any other Tier 1/2 director for review (outside of PHASE-GATE triggers), apply the resolved mode: skip if solo mode; skip if lean mode and this is not a PHASE-GATE.
**总监门禁跳过规则**：在生成 creative-director、art-director 或任何其他一级/二级总监进行评审时（非 PHASE-GATE 触发的情况），应用解析出的模式：如果是 solo 模式则跳过；如果是 lean 模式且这不是 PHASE-GATE，则跳过。

## Team Composition
## 团队构成

- **ux-designer** — User flows, wireframes, accessibility, input handling
- **ui-programmer** — UI framework, screens, widgets, data binding, implementation
- **art-director** — Visual style, layout polish, consistency with art bible
- **engine UI specialist** — Validates UI implementation patterns against engine-specific best practices (read from `.claude/docs/technical-preferences.md` Engine Specialists → UI Specialist)
- **accessibility-specialist** — Audits accessibility compliance at Phase 4
- **ux-designer** — 用户流程、线框图、无障碍、输入处理
- **ui-programmer** — UI 框架、屏幕、组件、数据绑定、实施
- **art-director** — 视觉风格、布局打磨、与美术圣经的一致性
- **engine UI specialist** — 根据引擎特定最佳实践验证 UI 实施模式（从 `.claude/docs/technical-preferences.md` Engine Specialists → UI Specialist 读取）
- **accessibility-specialist** — 在阶段 4 审计无障碍合规性

**Templates used by this pipeline:**
**此流水线使用的模板：**

- `ux-spec.md` — Standard screen/flow UX specification
- `hud-design.md` — HUD-specific UX specification
- `interaction-pattern-library.md` — Reusable interaction patterns
- `accessibility-requirements.md` — Committed accessibility tier and requirements
- `ux-spec.md` — 标准屏幕/流程 UX 规范
- `hud-design.md` — HUD 专用 UX 规范
- `interaction-pattern-library.md` — 可重用交互模式
- `accessibility-requirements.md` — 承诺的无障碍等级和需求

## How to Delegate
## 如何委派

Use the Task tool to spawn each team member as a subagent:
使用 Task 工具将每个团队成员作为子智能体生成：

- `subagent_type: ux-designer` — User flows, wireframes, accessibility, input handling
- `subagent_type: ui-programmer` — UI framework, screens, widgets, data binding
- `subagent_type: art-director` — Visual style, layout polish, art bible consistency
- `subagent_type: [UI engine specialist]` — Engine-specific UI pattern validation (e.g., unity-ui-specialist, ue-umg-specialist, godot-specialist)
- `subagent_type: accessibility-specialist` — Accessibility compliance audit
- `subagent_type: ux-designer` — 用户流程、线框图、无障碍、输入处理
- `subagent_type: ui-programmer` — UI 框架、屏幕、组件、数据绑定
- `subagent_type: art-director` — 视觉风格、布局打磨、美术圣经一致性
- `subagent_type: [UI 引擎专家]` — 引擎特定 UI 模式验证（例如，unity-ui-specialist、ue-umg-specialist、godot-specialist）
- `subagent_type: accessibility-specialist` — 无障碍合规审计

Always provide full context in each agent's prompt (feature requirements, existing UI patterns, platform targets). Launch independent agents in parallel where the pipeline allows it (e.g., Phase 4 review agents can run simultaneously).
始终在每个智能体的提示中提供完整上下文（功能需求、已有 UI 模式、平台目标）。在流水线允许的情况下并行启动独立的智能体（例如，阶段 4 的评审智能体可以同时运行）。

## Pipeline
## 流水线

### Phase 1a: Context Gathering
### 阶段 1a：上下文收集

Before designing anything, read and synthesize:
在设计任何内容之前，读取并综合：

- `design/gdd/game-concept.md` — platform targets and intended audience
- `design/player-journey.md` — player's state and context when they reach this screen
- All GDD UI Requirements sections relevant to this feature
- `design/ux/interaction-patterns.md` — existing patterns to reuse (not reinvent)
- `design/accessibility-requirements.md` — committed accessibility tier (e.g., Basic, Enhanced, Full)
- `design/gdd/game-concept.md` — 平台目标和目标受众
- `design/player-journey.md` — 玩家到达此屏幕时的状态和上下文
- 与此功能相关的所有 GDD UI Requirements 部分
- `design/ux/interaction-patterns.md` — 可重用的已有模式（而非重新发明）
- `design/accessibility-requirements.md` — 承诺的无障碍等级（例如，Basic、Enhanced、Full）

**If `design/ux/interaction-patterns.md` does not exist**, surface the gap immediately:
**如果 `design/ux/interaction-patterns.md` 不存在**，立即暴露缺口：

> "interaction-patterns.md does not exist — no existing patterns to reuse."
> "interaction-patterns.md 不存在 — 没有可重用的已有模式。"

Then use `AskUserQuestion` with options:
然后使用 `AskUserQuestion` 提供选项：

- (a) Run `/ux-design patterns` first to establish the pattern library, then continue
- (b) Proceed without the pattern library — ui-programmer will treat all patterns created as new and add each to a new `design/ux/interaction-patterns.md` at completion
- (a) 先运行 `/ux-design patterns` 建立模式库，然后继续
- (b) 在没有模式库的情况下继续 — ui-programmer 会将所有创建的模式视为新模式，并在完成时将每个添加到新的 `design/ux/interaction-patterns.md`

Do NOT invent or assume patterns from the feature name or GDD alone. If the user chooses (b), explicitly instruct ui-programmer in Phase 3 to treat all patterns as new and document them in `design/ux/interaction-patterns.md` when implementation is complete. Note the pattern library status (created / absent / updated) in the final summary report.
不要仅从功能名或 GDD 发明或假设模式。如果用户选择 (b)，在阶段 3 中明确指示 ui-programmer 将所有模式视为新模式，并在实施完成时将其记录在 `design/ux/interaction-patterns.md` 中。在最终摘要报告中注明模式库状态（已创建 / 不存在 / 已更新）。

Summarize the context in a brief for the ux-designer: what the player is doing, what they need, what constraints apply, and which existing patterns are relevant.
为 ux-designer 综合上下文简报：玩家在做什么、他们需要什么、适用什么约束以及哪些已有模式相关。

### Phase 1b: UX Spec Authoring
### 阶段 1b：UX 规范编写

Invoke `/ux-design [feature name]` skill OR delegate directly to ux-designer to produce `design/ux/[feature-name].md` following the `ux-spec.md` template.
调用 `/ux-design [功能名]` 技能或直接委派给 ux-designer，按照 `ux-spec.md` 模板生成 `design/ux/[feature-name].md`。

If designing the HUD, use the `hud-design.md` template instead of `ux-spec.md`.
如果设计 HUD，使用 `hud-design.md` 模板而非 `ux-spec.md`。

> **Notes on special cases:**
> - For HUD design specifically, invoke `/ux-design` with `argument: hud` (e.g., `/ux-design hud`).
> - For the interaction pattern library, run `/ux-design patterns` once at project start and update it whenever new patterns are introduced during later phases.
> **特殊情况注意：**
> - 对于 HUD 设计，使用 `argument: hud` 调用 `/ux-design`（例如，`/ux-design hud`）。
> - 对于交互模式库，在项目开始时运行一次 `/ux-design patterns`，并在后续阶段引入新模式时更新它。

Output: `design/ux/[feature-name].md` with all required spec sections filled.
输出：`design/ux/[feature-name].md`，填写所有必需的规范部分。

### Phase 1c: UX Review
### 阶段 1c：UX 评审

After the spec is complete, invoke `/ux-review design/ux/[feature-name].md`.
规范完成后，调用 `/ux-review design/ux/[feature-name].md`。

**Gate**: Do not proceed to Phase 2 until the verdict is APPROVED. If the verdict is NEEDS REVISION, the ux-designer must address the flagged issues and re-run the review. The user may explicitly accept a NEEDS REVISION risk and proceed, but this must be a conscious decision — present the specific concerns via `AskUserQuestion` before asking whether to proceed.
**门禁**：在裁决为 APPROVED 之前不要进入阶段 2。如果裁决为 NEEDS REVISION，ux-designer 必须解决标记的问题并重新运行评审。用户可以明确接受 NEEDS REVISION 风险并继续，但这必须是有意识的决策 — 在询问是否继续之前通过 `AskUserQuestion` 呈现具体顾虑。

### Phase 2: Visual Design
### 阶段 2：视觉设计

Delegate to **art-director**:
委派给 **art-director**：

- Review the full UX spec (flows, wireframes, interaction patterns, accessibility notes) — not just the wireframe images
- Apply visual treatment from the art bible: colors, typography, spacing, animation style
- Check that visual design preserves accessibility compliance: verify color contrast ratios, and confirm color is never the only indicator of state (shape, text, or icon must reinforce it)
- Specify all asset requirements needed from the art pipeline: icons at specified sizes, background textures, fonts, decorative elements — with precise dimensions and format requirements
- Ensure consistency with existing implemented UI screens
- Output: visual design spec with style notes and asset manifest
- 评审完整 UX 规范（流程、线框图、交互模式、无障碍备注）— 不仅是线框图图像
- 应用美术圣经的视觉处理：色彩、字体、间距、动画风格
- 检查视觉设计是否保持无障碍合规性：验证色彩对比度，并确认颜色永远不是状态的唯一指示器（形状、文本或图标必须强化它）
- 指定美术管线所需的所有资产需求：指定大小的图标、背景纹理、字体、装饰元素 — 带有精确的尺寸和格式要求
- 确保与已实施 UI 屏幕的一致性
- 输出：带风格注释和资产清单的视觉设计规范

### Phase 3: Implementation
### 阶段 3：实施

Before implementation begins, spawn the **engine UI specialist** (from `.claude/docs/technical-preferences.md` Engine Specialists → UI Specialist) to review the UX spec and visual design spec for engine-specific implementation guidance:
实施开始之前，生成 **engine UI specialist**（来自 `.claude/docs/technical-preferences.md` Engine Specialists → UI Specialist），以审查 UX 规范和视觉设计规范，获取引擎特定的实施指导：

- Which engine UI framework should be used for this screen? (e.g., UI Toolkit vs UGUI in Unity, Control nodes vs CanvasLayer in Godot, UMG vs CommonUI in Unreal)
- Any engine-specific gotchas for the proposed layout or interaction patterns?
- Recommended widget/node structure for the engine?
- Output: engine UI implementation notes to hand off to ui-programmer before they begin
- 此屏幕应使用哪个引擎 UI 框架？（例如，Unity 中的 UI Toolkit vs UGUI，Godot 中的 Control nodes vs CanvasLayer，Unreal 中的 UMG vs CommonUI）
- 提议的布局或交互模式是否有任何引擎特定的陷阱？
- 引擎的推荐组件/节点结构？
- 输出：移交给 ui-programmer 的引擎 UI 实施注释，在他们开始之前

If no engine is configured, skip this step.
如果未配置引擎，跳过此步骤。

Delegate to **ui-programmer**:
委派给 **ui-programmer**：

- Implement the UI following the UX spec and visual design spec
- **Use patterns from `design/ux/interaction-patterns.md`** — do not reinvent patterns that are already specified. If a pattern almost fits but needs modification, note the deviation and flag it for ux-designer review.
- **UI NEVER owns or modifies game state** — display only; emit events for all player actions
- All text through the localization system — no hardcoded player-facing strings
- Support both input methods (keyboard/mouse AND gamepad)
- Implement accessibility features per the committed tier in `design/accessibility-requirements.md`
- Wire up data binding to game state
- **If any new interaction pattern is created during implementation** (i.e., something not already in the pattern library), add it to `design/ux/interaction-patterns.md` before marking implementation complete
- Output: implemented UI feature
- 按照 UX 规范和视觉设计规范实施 UI
- **使用 `design/ux/interaction-patterns.md` 中的模式** — 不要重新发明已指定的模式。如果某个模式几乎合适但需要修改，记录偏差并标记给 ux-designer 评审。
- **UI 永远不拥有或修改游戏状态** — 仅显示；为所有玩家动作触发事件
- 所有文本通过本地化系统 — 无硬编码的面向玩家字符串
- 支持两种输入方法（键盘/鼠标和手柄）
- 按 `design/accessibility-requirements.md` 中承诺的等级实施无障碍功能
- 连接数据绑定到游戏状态
- **如果实施过程中创建了任何新的交互模式**（即，模式库中尚不存在的内容），在标记实施完成之前将其添加到 `design/ux/interaction-patterns.md`
- 输出：已实施的 UI 功能

### Phase 4: Review (parallel)
### 阶段 4：评审（并行）

Delegate in parallel:
并行委派：

- **ux-designer**: Verify implementation matches wireframes and interaction spec. Test keyboard-only and gamepad-only navigation. Check accessibility features function correctly.
- **art-director**: Verify visual consistency with art bible. Check at minimum and maximum supported resolutions.
- **accessibility-specialist**: Verify compliance against the committed accessibility tier documented in `design/accessibility-requirements.md`. Flag any violations as blockers.
- **ux-designer**：验证实施是否匹配线框图和交互规范。测试仅键盘和仅手柄导航。检查无障碍功能是否正常运行。
- **art-director**：验证与美术圣经的视觉一致性。在最小和最大支持的分辨率下检查。
- **accessibility-specialist**：验证是否符合 `design/accessibility-requirements.md` 中记录的承诺无障碍等级。将任何违规标记为阻塞问题。

All three review streams must report before proceeding to Phase 5.
所有三个评审流必须在进入阶段 5 之前报告。

### Phase 5: Polish
### 阶段 5：打磨

- Address all review feedback
- Verify animations are skippable and respect the player's motion reduction preferences
- Confirm UI sounds trigger through the audio event system (no direct audio calls)
- Test at all supported resolutions and aspect ratios
- **Verify `design/ux/interaction-patterns.md` is up to date** — if any new patterns were introduced during this feature's implementation, confirm they have been added to the library
- **Confirm all HUD elements respect the visual budget** defined in `design/ux/hud.md` (element count, screen region allocations, maximum opacity values)
- 处理所有评审反馈
- 验证动画可跳过并尊重玩家的减少动效偏好
- 确认 UI 声音通过音频事件系统触发（无直接音频调用）
- 在所有支持的分辨率和宽高比下测试
- **验证 `design/ux/interaction-patterns.md` 是最新的** — 如果在此功能实施期间引入了任何新模式，确认它们已添加到库中
- **确认所有 HUD 元素遵守 `design/ux/hud.md` 中定义的视觉预算**（元素计数、屏幕区域分配、最大不透明度值）

## Quick Reference — When to Use Which Skill
## 快速参考 — 何时使用哪个技能

- `/ux-design` — Author a new UX spec for a screen, flow, or HUD from scratch
- `/ux-review` — Validate a completed UX spec before implementation
- `/team-ui [feature]` — Full pipeline from concept through polish (calls `/ux-design` and `/ux-review` internally)
- `/quick-design` — Small UI changes that don't need a full new UX spec
- `/ux-design` — 从零开始为屏幕、流程或 HUD 编写新 UX 规范
- `/ux-review` — 在实施之前验证已完成的 UX 规范
- `/team-ui [功能]` — 从概念到打磨的完整流水线（内部调用 `/ux-design` 和 `/ux-review`）
- `/quick-design` — 不需要完整新 UX 规范的小型 UI 改动

## Error Recovery Protocol
## 错误恢复协议

If any spawned agent (via Task) returns BLOCKED, errors, or cannot complete:
如果任何生成的智能体（通过 Task）返回 BLOCKED、错误或无法完成：

1. **Surface immediately**: Report "[AgentName]: BLOCKED — [reason]" to the user before continuing to dependent phases
2. **Assess dependencies**: Check whether the blocked agent's output is required by subsequent phases. If yes, do not proceed past that dependency point without user input.
3. **Offer options** via AskUserQuestion with choices:
   - Skip this agent and note the gap in the final report
   - Retry with narrower scope
   - Stop here and resolve the blocker first
4. **Always produce a partial report** — output whatever was completed. Never discard work because one agent blocked.
1. **立即上报**：在继续进入依赖阶段之前，向用户报告"[AgentName]：BLOCKED — [原因]"
2. **评估依赖**：检查被阻塞智能体的输出是否为后续阶段所必需。如果是，则在用户输入之前不要越过该依赖点继续推进。
3. **通过 AskUserQuestion 提供选项**：
   - 跳过此智能体并在最终报告中标注缺口
   - 以更窄的范围重试
   - 在此停止并先解决阻塞问题
4. **始终产出部分报告** — 输出已完成的内容。不要因为一个智能体被阻塞就丢弃已完成的工作。

Common blockers:
常见阻塞问题：

- Input file missing (story not found, GDD absent) → redirect to the skill that creates it
- ADR status is Proposed → do not implement; run `/architecture-decision` first
- Scope too large → split into two stories via `/create-stories`
- Conflicting instructions between ADR and story → surface the conflict, do not guess
- 输入文件缺失（未找到故事、GDD 缺失）→ 重定向到创建该文件的技能
- ADR 状态为 Proposed → 不要实施；先运行 `/architecture-decision`
- 范围过大 → 通过 `/create-stories` 拆分为两个故事
- ADR 与故事之间的指令冲突 → 上报冲突，不要猜测

## File Write Protocol
## 文件写入协议

All file writes (UX specs, interaction pattern library updates, implementation files) are
delegated to sub-agents and sub-skills (`/ux-design`, `ui-programmer`). Each enforces the
"May I write to [path]?" protocol. This orchestrator does not write files directly.
所有文件写入（UX 规范、交互模式库更新、实施文件）都
委派给子智能体和子技能（`/ux-design`、`ui-programmer`）。每个都执行
"是否可以写入 [path]？"协议。此编排器不直接写入文件。

## Output
## 输出

A summary report covering: UX spec status, UX review verdict, visual design status, implementation status, accessibility compliance, input method support, interaction pattern library update status, and any outstanding issues.
一份摘要报告，涵盖：UX 规范状态、UX 评审裁决、视觉设计状态、实施状态、无障碍合规性、输入方法支持、交互模式库更新状态以及任何遗留问题。

Verdict: **COMPLETE** — UI feature delivered through full pipeline (UX spec → visual → implementation → review → polish).
Verdict: **BLOCKED** — pipeline halted; surface the blocker and its phase before stopping.
裁决：**COMPLETE** — UI 功能通过完整流水线交付（UX 规范 → 视觉 → 实施 → 评审 → 打磨）。
裁决：**BLOCKED** — 流水线暂停；在停止之前暴露阻塞问题及其阶段。

## Next Steps
## 后续步骤

- Run `/ux-review` on the final spec if not yet approved.
- Run `/code-review` on the UI implementation before closing stories.
- Run `/team-polish` if visual or audio polish pass is needed.
- 如果最终规范尚未批准，运行 `/ux-review`。
- 在关闭故事之前运行 `/code-review` 审查 UI 实施。
- 如果需要视觉或音频打磨，运行 `/team-polish`。
