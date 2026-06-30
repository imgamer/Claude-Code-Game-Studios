---
name: team-narrative
description: "Orchestrate the narrative team: coordinates narrative-director, writer, world-builder, and level-designer to create cohesive story content, world lore, and narrative-driven level design."
argument-hint: "[narrative content description] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion, TodoWrite
model: sonnet
---
---
name: team-narrative
description: "编排叙事团队：协调 narrative-director、writer、world-builder 和 level-designer，创造连贯的故事内容、世界观和叙事驱动的关卡设计。"
argument-hint: "[narrative content description] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion, TodoWrite
model: sonnet
---
If no argument is provided, output usage guidance and exit without spawning any agents:
> Usage: `/team-narrative [narrative content description]` — describe the story content, scene, or narrative area to work on (e.g., `boss encounter cutscene`, `faction intro dialogue`, `tutorial narrative`). Do not use `AskUserQuestion` here; output the guidance directly.
如果未提供参数，输出使用指南并退出，不派生任何智能体：
> 用法：`/team-narrative [narrative content description]` — 描述要处理的 story 内容、场景或叙事区域（例如 `boss encounter cutscene`、`faction intro dialogue`、`tutorial narrative`）。此处不要使用 `AskUserQuestion`；直接输出指南。

When this skill is invoked with an argument, orchestrate the narrative team through a structured pipeline.
当此技能带参数被调用时，通过结构化的管线编排叙事团队。

**Decision Points:** At each phase transition, use `AskUserQuestion` to present
the user with the subagent's proposals as selectable options. Write the agent's
full analysis in conversation, then capture the decision with concise labels.
The user must approve before moving to the next phase.
**决策点：** 在每次阶段转换时，使用 `AskUserQuestion` 向用户
呈现子智能体的建议作为可选选项。在对话中写入智能体的
完整分析，然后用简洁的标签捕获决策。
用户必须批准才能进入下一阶段。

## Phase 0: Resolve Review Mode
## 阶段 0：解析审查模式

1. If `--review [mode]` was passed as an argument, use that mode.
2. Else read `production/review-mode.txt` — use whatever is written there.
3. Else default to `lean`.
1. 如果作为参数传入了 `--review [mode]`，使用该模式。
2. 否则读取 `production/review-mode.txt` — 使用其中的内容。
3. 否则默认为 `lean`。

Modes:
- `full` — spawn all director and lead gates as described
- `lean` — skip director gates unless they are PHASE-GATE type (CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE)
- `solo` — skip all director gate spawning entirely; run the skill without any agent gates
模式：
- `full` — 按描述派生所有主管和负责人门禁
- `lean` — 跳过主管门禁，除非它们是 PHASE-GATE 类型（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE）
- `solo` — 完全跳过所有主管门禁派生；在没有任何智能体门禁的情况下运行此技能

Store the resolved mode for use in all subsequent phases.
存储解析后的模式以用于所有后续阶段。

## Team Composition
## 团队组成
- **narrative-director** — Story arcs, character design, dialogue strategy, narrative vision
- **writer** — Dialogue writing, lore entries, item descriptions, in-game text
- **world-builder** — World rules, faction design, history, geography, environmental storytelling
- **art-director** — Character visual design, environmental visual storytelling, cutscene/cinematic tone
- **level-designer** — Level layouts that serve the narrative, pacing, environmental storytelling beats
- **localization-lead** — Localization readiness — flags non-localizable strings, cultural assumptions, and i18n gaps
- **narrative-director** — 故事弧线、角色设计、对话策略、叙事愿景
- **writer** — 对话撰写、设定条目、物品描述、游戏内文本
- **world-builder** — 世界规则、阵营设计、历史、地理、环境叙事
- **art-director** — 角色视觉设计、环境视觉叙事、过场动画/电影感基调
- **level-designer** — 服务于叙事的关卡布局、节奏、环境叙事节点
- **localization-lead** — 本地化就绪度 — 标记不可本地化的字符串、文化假设和 i18n 缺口

## How to Delegate
## 如何委派

Use the Task tool to spawn each team member as a subagent:
- `subagent_type: narrative-director` — Story arcs, character design, narrative vision
- `subagent_type: writer` — Dialogue writing, lore entries, in-game text
- `subagent_type: world-builder` — World rules, faction design, history, geography
- `subagent_type: art-director` — Character visual profiles, environmental visual storytelling, cinematic tone
- `subagent_type: level-designer` — Level layouts that serve the narrative, pacing
- `subagent_type: localization-lead` — Localization readiness — flags non-localizable strings, cultural assumptions, and i18n gaps
使用 Task 工具将每个团队成员派生为子智能体：
- `subagent_type: narrative-director` — 故事弧线、角色设计、叙事愿景
- `subagent_type: writer` — 对话撰写、设定条目、游戏内文本
- `subagent_type: world-builder` — 世界规则、阵营设计、历史、地理
- `subagent_type: art-director` — 角色视觉档案、环境视觉叙事、电影感基调
- `subagent_type: level-designer` — 服务于叙事的关卡布局、节奏
- `subagent_type: localization-lead` — 本地化就绪度 — 标记不可本地化的字符串、文化假设和 i18n 缺口

Always provide full context in each agent's prompt (narrative brief, lore dependencies, character profiles). Launch independent agents in parallel where the pipeline allows it (e.g., Phase 2 agents can run simultaneously).
始终在每个智能体的提示中提供完整上下文（叙事简报、设定依赖、角色档案）。在管线允许的情况下并行启动独立的智能体（例如，阶段 2 的智能体可以同时运行）。

## Pipeline
## 管线

### Phase 1: Narrative Direction
### 阶段 1：叙事方向
Delegate to **narrative-director**:
- Define the narrative purpose of this content: what story beat does it serve?
- Identify characters involved, their motivations, and how this fits the overall arc
- Set the emotional tone and pacing targets
- Specify any lore dependencies or new lore this introduces
- Output: narrative brief with story requirements
委派给 **narrative-director**：
- 定义此内容的叙事目的：它服务于哪个故事节点？
- 识别涉及的角色、他们的动机，以及这如何契合整体弧线
- 设定情感基调和节奏目标
- 指定任何设定依赖或新引入的设定
- 输出：带故事需求的叙事简报

### Phase 2: World Foundation (parallel)
### 阶段 2：世界基础（并行）
Delegate in parallel — issue all three Task calls simultaneously before waiting for any result:
- **world-builder**: Create or update lore entries for factions, locations, and history relevant to this content. Cross-reference against existing lore for contradictions. Set canon level for new entries.
- **writer**: Draft character dialogue using voice profiles. Ensure all lines are under 120 characters, use named placeholders for variables, and are localization-ready.
- **art-director**: Define character visual design direction for key characters appearing in this content (silhouette, visual archetype, distinguishing features). Specify environmental visual storytelling elements for each key space (prop composition, lighting notes, spatial arrangement). Define tone palette and cinematic direction for any cutscenes or scripted sequences.
并行委派 — 在等待任何结果之前同时发出所有三个 Task 调用：
- **world-builder**：创建或更新与此内容相关的阵营、地点和历史的设定条目。与现有设定交叉检查以发现矛盾。为新条目设定正史级别。
- **writer**：使用语音档案起草角色对话。确保所有行不超过 120 个字符，对变量使用命名占位符，并具备本地化就绪性。
- **art-director**：为在此内容中出现的关键角色定义角色视觉设计方向（剪影、视觉原型、辨识特征）。为每个关键空间指定环境视觉叙事元素（道具构图、灯光说明、空间布局）。为任何过场动画或脚本化序列定义基调色板和电影感方向。

### Phase 3: Level Narrative Integration
### 阶段 3：关卡叙事整合
Delegate to **level-designer**:
- Review the narrative brief and lore foundation
- Design environmental storytelling elements in the level
- Place narrative triggers, dialogue zones, and discovery points
- Ensure pacing serves both gameplay and story
委派给 **level-designer**：
- 审查叙事简报和设定基础
- 在关卡中设计环境叙事元素
- 放置叙事触发器、对话区域和发现点
- 确保节奏同时服务于玩法和故事

### Phase 4: Review and Consistency
### 阶段 4：审查和一致性
Delegate to **narrative-director**:
- Review all dialogue against character voice profiles
- Verify lore consistency across new and existing entries
- Confirm narrative pacing aligns with level design
- Check that all mysteries have documented "true answers"
委派给 **narrative-director**：
- 对照角色语音档案审查所有对话
- 验证新条目和现有条目之间的设定一致性
- 确认叙事节奏与关卡设计一致
- 检查所有谜团都有记录的 "true answers"

### Phase 5: Polish (parallel)
### 阶段 5：打磨（并行）
Delegate in parallel:
- **writer**: Final self-review — verify no line exceeds dialogue box constraints, all text uses string keys (not raw strings), placeholder variable names are consistent
- **localization-lead**: Validate i18n compliance — check string key naming conventions, flag any strings with hardcoded formatting that won't survive translation, verify character limit headroom for languages that expand (German/Finnish typically +30%), confirm no cultural assumptions in text that would need locale-specific variants
- **world-builder**: Finalize canon levels for all new lore entries
并行委派：
- **writer**：最终自审 — 验证没有行超过对话框约束，所有文本使用字符串键（而非原始字符串），占位符变量名一致
- **localization-lead**：验证 i18n 合规性 — 检查字符串键命名约定，标记任何带有硬编码格式无法在翻译中存活的字符串，验证扩展语言的字符限制余量（德语/芬兰语通常 +30%），确认文本中没有需要特定区域变体的文化假设
- **world-builder**：为所有新设定条目最终确定正史级别

## Error Recovery Protocol
## 错误恢复协议

If any spawned agent (via Task) returns BLOCKED, errors, or cannot complete:
如果任何派生的智能体（通过 Task）返回 BLOCKED、错误或无法完成：

1. **Surface immediately**: Report "[AgentName]: BLOCKED — [reason]" to the user before continuing to dependent phases
2. **Assess dependencies**: Check whether the blocked agent's output is required by subsequent phases. If yes, do not proceed past that dependency point without user input.
3. **Offer options** via AskUserQuestion with choices:
   - Skip this agent and note the gap in the final report
   - Retry with narrower scope
   - Stop here and resolve the blocker first
4. **Always produce a partial report** — output whatever was completed. Never discard work because one agent blocked.
1. **立即呈现**：在继续依赖阶段之前向用户报告 "[AgentName]: BLOCKED — [reason]"
2. **评估依赖**：检查后续阶段是否需要被阻塞智能体的输出。如果需要，在没有用户输入的情况下不要超过该依赖点继续。
3. **通过 AskUserQuestion 提供选项**，供选择：
   - 跳过此智能体并在最终报告中记录缺口
   - 以更窄的范围重试
   - 在此停止并先解决阻塞问题
4. **始终生成部分报告** — 输出已完成的任何内容。绝不因为一个智能体被阻塞而丢弃工作。

Common blockers:
- Input file missing (story not found, GDD absent) → redirect to the skill that creates it
- ADR status is Proposed → do not implement; run `/architecture-decision` first
- Scope too large → split into two stories via `/create-stories`
- Conflicting instructions between ADR and story → surface the conflict, do not guess
常见阻塞：
- 输入文件缺失（未找到故事、GDD 缺失）→ 重定向到创建它的技能
- ADR 状态为 Proposed → 不要实现；先运行 `/architecture-decision`
- 范围太大 → 通过 `/create-stories` 拆分为两个故事
- ADR 和故事之间的指令冲突 → 呈现冲突，不要猜测

## File Write Protocol
## 文件写入协议

All file writes (narrative docs, dialogue files, lore entries) are delegated to
sub-agents spawned via Task. Each sub-agent enforces the "May I write to [path]?"
protocol. This orchestrator does not write files directly.
所有文件写入（叙事文档、对话文件、设定条目）都委派给
通过 Task 派生的子智能体。每个子智能体强制执行 "May I write to [path]?"
协议。此编排器不直接写入文件。

## Output
## 输出

A summary report covering: narrative brief status, lore entries created/updated, dialogue lines written, level narrative integration points, consistency review results, and any unresolved contradictions.
一份摘要报告，涵盖：叙事简报状态、创建/更新的设定条目、已写的对话行、关卡叙事整合点、一致性审查结果以及任何未解决的矛盾。

Verdict: **COMPLETE** — narrative content delivered.
结论：**已完成** — 叙事内容已交付。

If the pipeline stops because a dependency is unresolved (e.g., lore contradiction or missing prerequisite not resolved by the user):
如果管线因依赖未解决而停止（例如，设定矛盾或用户未解决的缺失先决条件）：

Verdict: **BLOCKED** — [reason]
结论：**已阻塞** — [原因]

## Next Steps
## 后续步骤

- Run `/design-review` on the narrative documents for consistency validation.
- Run `/localize extract` to extract new strings for translation after dialogue is finalized.
- Run `/dev-story` to implement dialogue triggers and narrative events in-engine.
- 对叙事文档运行 `/design-review` 进行一致性验证。
- 在对话最终确定后运行 `/localize extract` 提取新字符串以供翻译。
- 运行 `/dev-story` 在引擎中实现对话触发器和叙事事件。
