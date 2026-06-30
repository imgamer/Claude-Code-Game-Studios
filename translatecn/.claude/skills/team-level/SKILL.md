---
name: team-level
description: "Orchestrate level design team: level-designer + narrative-director + world-builder + art-director + systems-designer + qa-tester for complete area/level creation."
argument-hint: "[level name or area to design] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---
---
名称：team-level
描述："编排关卡设计团队：level-designer + narrative-director + world-builder + art-director + systems-designer + qa-tester，完成区域/关卡创建。"
argument-hint："[level name or area to design] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model：sonnet
---

When this skill is invoked:
当此技能被调用时：

**Decision Points:** At each step transition, use `AskUserQuestion` to present
the user with the subagent's proposals as selectable options. Write the agent's
full analysis in conversation, then capture the decision with concise labels.
The user must approve before moving to the next step.
**决策点：** 在每个步骤转换时，使用 `AskUserQuestion` 向用户呈现子智能体的提议作为可选选项。在对话中写入智能体的完整分析，然后用简明标签捕获决策。用户必须在进入下一步前批准。

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

1. **Read the argument** for the target level or area (e.g., `tutorial`,
   `forest dungeon`, `hub town`, `final boss arena`).
1. **读取参数**以获取目标关卡或区域（例如 `tutorial`、`forest dungeon`、`hub town`、`final boss arena`）。

2. **Gather context**:
2. **收集上下文**：
   - Read the game concept at `design/gdd/game-concept.md`
   - 读取 `design/gdd/game-concept.md` 中的游戏概念
   - Read game pillars at `design/gdd/game-pillars.md`
   - 读取 `design/gdd/game-pillars.md` 中的游戏支柱
   - Read existing level docs in `design/levels/`
   - 读取 `design/levels/` 中的现有关卡文档
   - Read relevant narrative docs in `design/narrative/`
   - 读取 `design/narrative/` 中的相关叙事文档
   - Read world-building docs for the area's region/faction
   - 读取该区域所属地区/阵营的世界观文档

## How to Delegate
## 如何委派

Use the Task tool to spawn each team member as a subagent:
使用 Task 工具将每个团队成员派生为子智能体：
- `subagent_type: narrative-director` — Narrative purpose, characters, emotional arc
- `subagent_type: narrative-director` —— 叙事目的、角色、情感弧线
- `subagent_type: world-builder` — Lore context, environmental storytelling, world rules
- `subagent_type: world-builder` —— 传说上下文、环境叙事、世界规则
- `subagent_type: level-designer` — Spatial layout, pacing, encounters, navigation
- `subagent_type: level-designer` —— 空间布局、节奏、遭遇、导航
- `subagent_type: systems-designer` — Enemy compositions, loot tables, difficulty balance
- `subagent_type: systems-designer` —— 敌人组成、战利品表、难度平衡
- `subagent_type: art-director` — Visual theme, color palette, lighting, asset requirements
- `subagent_type: art-director` —— 视觉主题、色彩调色板、光照、资产需求
- `subagent_type: accessibility-specialist` — Navigation clarity, colorblind safety, cognitive load
- `subagent_type: accessibility-specialist` —— 导航清晰度、色盲安全、认知负荷
- `subagent_type: qa-tester` — Test cases, boundary testing, playtest checklist
- `subagent_type: qa-tester` —— 测试用例、边界测试、试玩清单

Always provide full context in each agent's prompt (game concept, pillars, existing level docs, narrative docs).
始终在每个智能体的提示中提供完整上下文（游戏概念、支柱、现有关卡文档、叙事文档）。

3. **Orchestrate the level design team** in sequence:
3. **按顺序编排关卡设计团队**：

### Step 1: Narrative + Visual Direction (narrative-director + world-builder + art-director, parallel)
### 步骤 1：叙事 + 视觉方向（narrative-director + world-builder + art-director，并行）

Spawn all three agents simultaneously — issue all three Task calls before waiting for any result.
同时派生所有三个智能体 —— 在等待任何结果前发出所有三个 Task 调用。

Spawn the `narrative-director` agent to:
派生 `narrative-director` 智能体以：
- Define the narrative purpose of this area (what story beats happen here?)
- 定义此区域的叙事目的（此处发生哪些故事节拍？）
- Identify key characters, dialogue triggers, and lore elements
- 标识关键角色、对话触发器和传说元素
- Specify emotional arc (how should the player feel entering, during, leaving?)
- 指定情感弧线（玩家进入时、进行中、离开时应感受如何？）

Spawn the `world-builder` agent to:
派生 `world-builder` 智能体以：
- Provide lore context for the area (history, faction presence, ecology)
- 为该区域提供传说上下文（历史、阵营存在、生态）
- Define environmental storytelling opportunities
- 定义环境叙事机会
- Specify any world rules that affect gameplay in this area
- 指定影响此区域游戏玩法的任何世界规则

Spawn the `art-director` agent to:
派生 `art-director` 智能体以：
- Establish visual theme targets for this area — these are INPUTS to layout, not outputs of it
- 确立此区域的视觉主题目标 —— 这些是布局的输入，而非其输出
- Define the color temperature and lighting mood for this area (how does it differ from adjacent areas?)
- 定义此区域的色温和光照氛围（它与相邻区域有何不同？）
- Specify shape language direction (angular fortress? organic cave? decayed grandeur?)
- 指定形状语言方向（棱角分明的堡垒？有机洞穴？衰败的宏伟？）
- Name the primary visual landmarks that will orient the player
- 命名将引导玩家方向的主要视觉地标
- Read `design/art/art-bible.md` if it exists — anchor all direction in the established art bible
- 若存在，读取 `design/art/art-bible.md` —— 将所有方向锚定在已确立的美术圣经中

**The art-director's visual targets from Step 1 must be passed to the level-designer in Step 2** as explicit constraints. Layout decisions happen within the visual direction, not before it.
**步骤 1 中 art-director 的视觉目标必须作为显式约束传递给步骤 2 中的 level-designer**。布局决策在视觉方向之内发生，而非之前。

**Gate**: Use `AskUserQuestion` to present all three Step 1 outputs (narrative brief, lore foundation, visual direction targets) and confirm before proceeding to Step 2.
**门禁**：使用 `AskUserQuestion` 呈现所有三个步骤 1 输出（叙事简报、传说基础、视觉方向目标），并在进入步骤 2 前确认。

### Step 2: Layout and Encounter Design (level-designer)
### 步骤 2：布局和遭遇设计（level-designer）
Spawn the `level-designer` agent with the full Step 1 output as context:
派生 `level-designer` 智能体，以完整步骤 1 输出作为上下文：
- Narrative brief (from narrative-director)
- 叙事简报（来自 narrative-director）
- Lore foundation (from world-builder)
- 传说基础（来自 world-builder）
- **Visual direction targets (from art-director)** — layout must work within these targets, not contradict them
- **视觉方向目标（来自 art-director）** —— 布局必须在这些目标之内运作，不得与之矛盾

The level-designer should:
level-designer 应该：
- Design the spatial layout (critical path, optional paths, secrets) — ensuring primary routes align with the visual landmark targets from Step 1
- 设计空间布局（关键路径、可选路径、秘密）—— 确保主路线与步骤 1 的视觉地标目标对齐
- Define pacing curve (tension peaks, rest areas, exploration zones) — coordinated with the emotional arc from narrative-director
- 定义节奏曲线（紧张峰值、休息区、探索区）—— 与 narrative-director 的情感弧线协调
- Place encounters with difficulty progression
- 放置带难度递进的遭遇
- Design environmental puzzles or navigation challenges
- 设计环境谜题或导航挑战
- Define points of interest and landmarks for wayfinding — these must match the visual landmarks the art-director specified
- 定义兴趣点和导航地标 —— 这些必须与 art-director 指定的视觉地标匹配
- Specify entry/exit points and connections to adjacent areas
- 指定入口/出口点和与相邻区域的连接

**Adjacent area dependency check**: After the layout is produced, check `design/levels/` for each adjacent area referenced by the level-designer. If any referenced area's `.md` file does not exist, surface the gap:
**相邻区域依赖检查**：布局生成后，检查 `design/levels/` 中 level-designer 引用的每个相邻区域。若任何被引用区域的 `.md` 文件不存在，暴露缺口：
> "Level references [area-name] as an adjacent area but `design/levels/[area-name].md` does not exist."
> "关卡引用 [area-name] 作为相邻区域，但 `design/levels/[area-name].md` 不存在。"

Use `AskUserQuestion` with options:
使用 `AskUserQuestion` 提供选项：
- (a) Proceed with a placeholder reference — mark the connection as UNRESOLVED in the level doc and list it in the open cross-level dependencies section of the summary report
- (a) 用占位符引用继续 —— 在关卡文档中将连接标记为 UNRESOLVED 并在摘要报告的开放跨关卡依赖章节中列出
- (b) Pause and run `/team-level [area-name]` first to establish that area
- (b) 暂停并先运行 `/team-level [area-name]` 建立该区域

Do NOT invent content for the missing adjacent area.
不要为缺失的相邻区域编造内容。

**Gate**: Use `AskUserQuestion` to present Step 2 layout (including any unresolved adjacent area dependencies) and confirm before proceeding to Step 3.
**门禁**：使用 `AskUserQuestion` 呈现步骤 2 布局（包括任何未解决的相邻区域依赖），并在进入步骤 3 前确认。

### Step 3: Systems Integration (systems-designer)
### 步骤 3：系统集成（systems-designer）
Spawn the `systems-designer` agent to:
派生 `systems-designer` 智能体以：
- Specify enemy compositions and encounter formulas
- 指定敌人组成和遭遇公式
- Define loot tables and reward placement
- 定义战利品表和奖励放置
- Balance difficulty relative to expected player level/gear
- 相对于预期玩家等级/装备平衡难度
- Design any area-specific mechanics or environmental hazards
- 设计任何区域特定机制或环境危险
- Specify resource distribution (health pickups, save points, shops)
- 指定资源分布（生命拾取、存档点、商店）

**Gate**: Use `AskUserQuestion` to present Step 3 outputs and confirm before proceeding to Step 4.
**门禁**：使用 `AskUserQuestion` 呈现步骤 3 输出，并在进入步骤 4 前确认。

### Step 4: Production Concepts + Accessibility (art-director + accessibility-specialist, parallel)
### 步骤 4：制作概念 + 无障碍（art-director + accessibility-specialist，并行）

**Note**: The art-director's directional pass (visual theme, color targets, mood) happened in Step 1. This pass is location-specific production concepts — given the finalized layout, what does each specific space look like?
**注意**：art-director 的方向性通过（视觉主题、色彩目标、氛围）发生在步骤 1。此通过是位置特定的制作概念 —— 给定最终布局，每个具体空间是什么样的？

Spawn the `art-director` agent with the finalized layout from Step 2:
派生 `art-director` 智能体，传递步骤 2 的最终布局：
- Produce location-specific concept specs for key spaces (entrance, key encounter zones, landmarks, exits)
- 为关键空间（入口、关键遭遇区、地标、出口）生成位置特定的概念规范
- Specify which art assets are unique to this area vs. shared from the global pool
- 指定哪些美术资产是此区域独有的 vs 从全局池共享的
- Define sight-line and lighting setups per key space (these are now layout-informed, not directional)
- 为每个关键空间定义视线和光照设置（这些现在是布局知情而非方向性的）
- Specify VFX needs that are specific to this area's layout (weather volumes, particles, atmospheric effects)
- 指定特定于此区域布局的 VFX 需求（天气体积、粒子、大气效果）
- Flag any locations where the layout creates visual direction conflicts with the Step 1 targets — surface these as production risks
- 标记布局与步骤 1 目标产生视觉方向冲突的任何位置 —— 将其作为制作风险暴露

Spawn the `accessibility-specialist` agent in parallel to:
并行派生 `accessibility-specialist` 智能体以：
- Review the level layout for navigation clarity (can players orient themselves without relying on color alone?)
- 评审关卡布局的导航清晰度（玩家能否在不依赖颜色的情况下定位自己？）
- Check that critical path signposting uses shape/icon/sound cues in addition to color
- 检查关键路径指引除颜色外还使用形状/图标/声音提示
- Review any puzzle mechanics for cognitive load — flag anything that requires holding more than 3 simultaneous states
- 评审任何谜题机制的认知负荷 —— 标记需要同时保持超过 3 个状态的任何内容
- Check that key gameplay areas have sufficient contrast for colorblind players
- 检查关键游戏玩法区域对色盲玩家是否有足够对比度
- Output: accessibility concerns list with severity (BLOCKING / RECOMMENDED / NICE TO HAVE)
- 输出：带严重程度（BLOCKING / RECOMMENDED / NICE TO HAVE）的无障碍疑虑列表

Wait for both agents to return before proceeding.
等待两个智能体返回后再继续。

**Gate**: Use `AskUserQuestion` to present both Step 4 results. If the accessibility-specialist returned any BLOCKING concerns, highlight them prominently and offer:
**门禁**：使用 `AskUserQuestion` 呈现两个步骤 4 结果。若 accessibility-specialist 返回任何 BLOCKING 疑虑，醒目突出它们并提供：
- (a) Return to level-designer and art-director to redesign the flagged elements before Step 5
- (a) 返回 level-designer 和 art-director 在步骤 5 前重新设计标记的元素
- (b) Document as a known accessibility gap and proceed to Step 5 with the concern explicitly logged in the final report
- (b) 记录为已知无障碍缺口并在最终报告中明确记录疑虑后进入步骤 5

Do NOT proceed to Step 5 without the user acknowledging any BLOCKING accessibility concerns.
未经用户确认任何 BLOCKING 无障碍疑虑，不要进入步骤 5。

### Step 5: QA Planning (qa-tester)
### 步骤 5：QA 规划（qa-tester）
Spawn the `qa-tester` agent to:
派生 `qa-tester` 智能体以：
- Write test cases for the critical path
- 为关键路径编写测试用例
- Identify boundary and edge cases (sequence breaks, softlocks)
- 标识边界和边缘情况（顺序破坏、软锁）
- Create a playtest checklist for the area
- 为该区域创建试玩清单
- Define acceptance criteria for level completion
- 定义关卡完成的验收标准

4. **Compile the level design document** combining all team outputs into the
   level design template format.
4. **编译关卡设计文档**，将所有团队输出合并到关卡设计模板格式中。

After all subagent outputs are collected, spawn `level-designer` via Task to compile and write the final document:
收集所有子智能体输出后，通过 Task 派生 `level-designer` 编译并写入最终文档：
- Pass: all subagent outputs (verbatim), the level brief, game pillars, relevant GDD sections
- 传递：所有子智能体输出（逐字）、关卡简报、游戏支柱、相关 GDD 章节
- Ask level-designer to: compile into the level design document format, then request user approval before writing ("May I write the compiled level design to design/levels/[level-name].md?")
- 要求 level-designer：编译成关卡设计文档格式，然后在写入前请求用户批准（"我可以将编译后的关卡设计写入 design/levels/[level-name].md 吗？"）
- The orchestrator does NOT call Write directly for the final document.
- 编排器不直接为最终文档调用 Write。

5. **Save to** `design/levels/[level-name].md` (handled by the level-designer subagent after user approval — see above).
5. **保存到** `design/levels/[level-name].md`（由 level-designer 子智能体在用户批准后处理 —— 见上文）。

6. **Output a summary** with: area overview, encounter count, estimated asset
   list, narrative beats, any cross-team dependencies or open questions, open
   cross-level dependencies (adjacent areas referenced but not yet designed, each
   marked UNRESOLVED), and accessibility concerns with their resolution status.
6. **输出摘要**，包含：区域概览、遭遇数、估计资产列表、叙事节拍、任何跨团队依赖或开放问题、开放跨关卡依赖（被引用但尚未设计的相邻区域，每个标记为 UNRESOLVED）以及无障碍疑虑及其解决状态。

## File Write Protocol
## 文件写入协议

All file writes (level design docs, narrative docs, test checklists) are delegated
to sub-agents spawned via Task. Each sub-agent enforces the "May I write to [path]?"
protocol. This orchestrator does not write files directly.
所有文件写入（关卡设计文档、叙事文档、测试清单）均委派给通过 Task 派生的子智能体。每个子智能体执行"我可以写入 [path] 吗？"协议。此编排器不直接写入文件。

Verdict: **COMPLETE** — level design document produced and all team outputs compiled.
裁定：**COMPLETE** —— 关卡设计文档已生成并编译所有团队输出。
Verdict: **BLOCKED** — one or more agents blocked; partial report produced with unresolved items listed.
裁定：**BLOCKED** —— 一个或多个智能体受阻；产生部分报告并列出未决项。

## Next Steps
## 后续步骤

- Run `/design-review design/levels/[level-name].md` to validate the completed level design doc.
- 运行 `/design-review design/levels/[level-name].md` 验证已完成的关卡设计文档。
- Run `/dev-story` to implement level content once the design is approved.
- 设计批准后运行 `/dev-story` 实施关卡内容。
- Run `/qa-plan` to generate a QA test plan for this level.
- 运行 `/qa-plan` 为此关卡生成 QA 测试计划。

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
