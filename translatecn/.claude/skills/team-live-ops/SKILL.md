---
name: team-live-ops
description: "Orchestrate the live-ops team for post-launch content planning: coordinates live-ops-designer, economy-designer, analytics-engineer, community-manager, writer, and narrative-director to design and plan a season, event, or live content update."
argument-hint: "[season name or event description] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---
---
name: team-live-ops
description: "为上线后内容规划编排 live-ops 团队：协调 live-ops-designer、economy-designer、analytics-engineer、community-manager、writer 和 narrative-director，设计和规划一个赛季、活动或实时内容更新。"
argument-hint: "[season name or event description] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---
**Argument check:** If no season name or event description is provided, output:
> "Usage: `/team-live-ops [season name or event description]` — Provide the name or description of the season or live event to plan."
Then stop immediately without spawning any subagents or reading any files.
**参数检查：** 如果未提供赛季名称或活动描述，输出：
> "用法：`/team-live-ops [season name or event description]` — 提供要规划的赛季或实时活动的名称或描述。"
然后立即停止，不派生任何子智能体或读取任何文件。

When this skill is invoked with a valid argument, orchestrate the live-ops team through a structured planning pipeline.
当此技能带有效参数被调用时，通过结构化的规划管线编排 live-ops 团队。

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
- **live-ops-designer** — Season structure, event cadence, retention mechanics, battle pass
- **economy-designer** — Live economy balance, store rotation, currency pricing, pity timers
- **analytics-engineer** — Success metrics, A/B test design, event tracking, dashboard specs
- **community-manager** — Player-facing announcements, event descriptions, seasonal messaging
- **narrative-director** — Seasonal narrative theme, story arc, world event framing
- **writer** — Event descriptions, reward item names, seasonal flavor text, announcement copy
- **live-ops-designer** — 赛季结构、活动节奏、留存机制、战斗通行证
- **economy-designer** — 实时经济平衡、商店轮换、货币定价、保底机制
- **analytics-engineer** — 成功指标、A/B 测试设计、事件跟踪、仪表板规范
- **community-manager** — 面向玩家的公告、活动描述、赛季消息
- **narrative-director** — 赛季叙事主题、故事弧线、世界事件框架
- **writer** — 活动描述、奖励物品名称、赛季风味文本、公告文案

## How to Delegate
## 如何委派

Use the Task tool to spawn each team member as a subagent:
- `subagent_type: live-ops-designer` — Season/event structure and retention mechanics
- `subagent_type: economy-designer` — Live economy balance and reward pricing
- `subagent_type: analytics-engineer` — Success metrics, A/B tests, event instrumentation
- `subagent_type: community-manager` — Player-facing communication and messaging
- `subagent_type: narrative-director` — Seasonal theme and narrative framing
- `subagent_type: writer` — All player-facing text: event descriptions, item names, copy
使用 Task 工具将每个团队成员派生为子智能体：
- `subagent_type: live-ops-designer` — 赛季/活动结构和留存机制
- `subagent_type: economy-designer` — 实时经济平衡和奖励定价
- `subagent_type: analytics-engineer` — 成功指标、A/B 测试、事件埋点
- `subagent_type: community-manager` — 面向玩家的沟通和消息
- `subagent_type: narrative-director` — 赛季主题和叙事框架
- `subagent_type: writer` — 所有面向玩家的文本：活动描述、物品名称、文案

Always provide full context in each agent's prompt (game concept path, existing season docs, ethics policy path, current economy state). Launch independent agents in parallel where the pipeline allows it (Phases 3 and 4 can run simultaneously).
始终在每个智能体的提示中提供完整上下文（游戏概念路径、现有赛季文档、伦理策略路径、当前经济状态）。在管线允许的情况下并行启动独立的智能体（阶段 3 和 4 可以同时运行）。

## Pipeline
## 管线

### Phase 1: Season/Event Scoping
### 阶段 1：赛季/活动范围界定
Delegate to **live-ops-designer**:
- Define the season or event: type (seasonal, limited-time event, challenge), duration, theme direction
- Outline the content list: what's new (modes, items, challenges, story beats)
- Define the retention hook: what brings players back daily/weekly during this season
- Identify resource budget: how much new content needs to be created vs. reused
- Output: season brief with scope, content list, and retention mechanic overview
委派给 **live-ops-designer**：
- 定义赛季或活动：类型（赛季性、限时活动、挑战赛）、时长、主题方向
- 概述内容列表：新增内容（模式、物品、挑战、故事节点）
- 定义留存钩子：在此赛季期间什么让玩家每日/每周回归
- 识别资源预算：需要创建多少新内容 vs 复用内容
- 输出：带范围、内容列表和留存机制概述的赛季简报

### Phase 2: Narrative Theme
### 阶段 2：叙事主题
Delegate to **narrative-director**:
- Read the season brief from Phase 1
- Design the seasonal narrative theme: how does this event connect to the game world?
- Define the central story hook players will discover during the event
- Identify which existing lore threads this season can advance
- Output: narrative framing document (theme, story hook, lore connections)
委派给 **narrative-director**：
- 读取阶段 1 的赛季简报
- 设计赛季叙事主题：此活动如何与游戏世界连接？
- 定义玩家将在活动期间发现的中心故事钩子
- 识别此赛季可以推进的现有设定线索
- 输出：叙事框架文档（主题、故事钩子、设定连接）

### Phase 3: Economy Design (parallel with Phase 2 if theme is clear)
### 阶段 3：经济设计（如果主题明确，与阶段 2 并行）
Delegate to **economy-designer**:
- Read the season brief and existing economy rules from `design/live-ops/economy-rules.md`
- Design the reward track: free tier progression, premium tier value proposition
- Plan the in-season economy: seasonal currency, store rotation, pricing
- Define pity timer mechanics and bad-luck protection for any random elements
- Verify no pay-to-win items in premium track
- Output: economy design doc with reward tables, pricing, and currency flow
委派给 **economy-designer**：
- 读取赛季简报和 `design/live-ops/economy-rules.md` 中的现有经济规则
- 设计奖励轨道：免费层进度、付费层价值主张
- 规划赛季内经济：赛季货币、商店轮换、定价
- 为任何随机元素定义保底机制和防霉运保护
- 验证付费层中没有付费取胜物品
- 输出：带奖励表、定价和货币流向的经济设计文档

### Phase 4: Analytics and Success Metrics (parallel with Phase 3)
### 阶段 4：分析和成功指标（与阶段 3 并行）
Delegate to **analytics-engineer**:
- Read the season brief
- Define success metrics: participation rate target, retention lift target, battle pass completion rate
- Design any A/B tests to run during the season (e.g., different reward cadences)
- Specify new telemetry events needed for this season's content
- Output: analytics plan with success criteria and instrumentation requirements
委派给 **analytics-engineer**：
- 读取赛季简报
- 定义成功指标：参与率目标、留存提升目标、战斗通行证完成率
- 设计在赛季期间运行的任何 A/B 测试（例如，不同的奖励节奏）
- 指定此赛季内容所需的新遥测事件
- 输出：带成功标准和埋点要求的分析计划

### Phase 5: Content Writing (parallel)
### 阶段 5：内容撰写（并行）
Delegate in parallel:
- **narrative-director** (if needed): Write any in-game narrative text (cutscene scripts, NPC dialogue, world event descriptions) for the season
- **writer**: Write all player-facing text — event names, reward item descriptions, challenge objective text, seasonal flavor text
- Both should read the narrative framing doc from Phase 2
并行委派：
- **narrative-director**（如需要）：为赛季撰写任何游戏内叙事文本（过场动画脚本、NPC 对话、世界事件描述）
- **writer**：撰写所有面向玩家的文本 — 活动名称、奖励物品描述、挑战目标文本、赛季风味文本
- 两者都应阅读阶段 2 的叙事框架文档

### Phase 6: Player Communication Plan
### 阶段 6：玩家沟通计划
Delegate to **community-manager**:
- Read the season brief, economy design, and narrative framing
- Draft the season launch announcement (tone, key highlights, platform-specific versions)
- Plan the communication cadence: pre-launch teaser, launch day post, mid-season reminder, final week FOMO push
- Draft known-issues section placeholder for day-1 patch notes
- Output: communication calendar with draft copy for each touchpoint
委派给 **community-manager**：
- 读取赛季简报、经济设计和叙事框架
- 起草赛季发布公告（基调、关键亮点、特定平台版本）
- 规划沟通节奏：发布前预热、发布当日推送、赛季中期提醒、最后一周错失恐惧推送
- 为第一天补丁说明起草已知问题章节占位符
- 输出：带每个触点草稿文案的沟通日历

### Phase 7: Review and Sign-off
### 阶段 7：审查和签署
Collect outputs from all phases and present a consolidated season plan:
- Season brief (Phase 1)
- Narrative framing (Phase 2)
- Economy design and reward tables (Phase 3)
- Analytics plan and success metrics (Phase 4)
- Written content inventory (Phase 5)
- Communication calendar (Phase 6)
收集所有阶段的输出并呈现统一的赛季计划：
- 赛季简报（阶段 1）
- 叙事框架（阶段 2）
- 经济设计和奖励表（阶段 3）
- 分析计划和成功指标（阶段 4）
- 已撰写内容清单（阶段 5）
- 沟通日历（阶段 6）

Present a summary to the user with:
- **Content scope**: what is being created
- **Economy health check**: does the reward track feel fair and non-predatory?
- **Analytics readiness**: are success criteria defined and instrumented?
- **Ethics review**: check the Phase 3 economy design against `design/live-ops/ethics-policy.md`
  - If the file does not exist: flag "ETHICS REVIEW SKIPPED: `design/live-ops/ethics-policy.md` not found. Economy design was not reviewed against an ethics policy. Recommend creating one before production begins." Include this flag in the season design output document. Add to next steps: create `design/live-ops/ethics-policy.md`.
  - If the file exists and a violation is found: flag "ETHICS FLAG: [element] in Phase 3 economy design violates [policy rule]. Approval is blocked until this is resolved." Do NOT issue a COMPLETE verdict or write output documents. Use `AskUserQuestion` with options: revise economy design / override with documented rationale / cancel. If user chooses to revise: re-spawn economy-designer to produce a corrected design, then return to Phase 7 review. If user selects Cancel: end with Verdict: BLOCKED — "Live ops design cancelled due to unresolved ethics violation. Resolve the flagged issues and re-run /team-live-ops."
- **Open questions**: decisions still needed before production begins
向用户呈现摘要，包括：
- **内容范围**：正在创建什么
- **经济健康检查**：奖励轨道感觉公平且非掠夺性吗？
- **分析就绪度**：成功标准是否已定义并埋点？
- **伦理审查**：根据 `design/live-ops/ethics-policy.md` 检查阶段 3 经济设计
  - 如果文件不存在：标记 "ETHICS REVIEW SKIPPED: `design/live-ops/ethics-policy.md` not found. Economy design was not reviewed against an ethics policy. Recommend creating one before production begins." 在赛季设计输出文档中包含此标记。添加到后续步骤：创建 `design/live-ops/ethics-policy.md`。
  - 如果文件存在且发现违规：标记 "ETHICS FLAG: [element] in Phase 3 economy design violates [policy rule]. Approval is blocked until this is resolved." 不要发布 COMPLETE 结论或写入输出文档。使用 `AskUserQuestion` 提供选项：修订经济设计 / 附带记录的理由覆盖 / 取消。如果用户选择修订：重新派生 economy-designer 生成修正后的设计，然后返回阶段 7 审查。如果用户选择取消：以结论结束：BLOCKED — "Live ops design cancelled due to unresolved ethics violation. Resolve the flagged issues and re-run /team-live-ops."
- **待决问题**：生产开始前仍需做出的决策

Ask the user to approve the season plan before delegating to production teams. Issue the COMPLETE verdict only after the user approves and no unresolved ethics violations remain. If an ethics violation is unresolved, end with Verdict: **BLOCKED**.
在委派给生产团队之前要求用户批准赛季计划。仅在用户批准且没有未解决的伦理违规后发布 COMPLETE 结论。如果伦理违规未解决，以结论结束：**已阻塞**。

## Output Documents
## 输出文档

All documents save to `design/live-ops/`:
- `seasons/S[N]_[name].md` — Season design document (from Phase 1-3)
- `seasons/S[N]_[name]_analytics.md` — Analytics plan (from Phase 4)
- `seasons/S[N]_[name]_comms.md` — Communication calendar (from Phase 6)
所有文档保存到 `design/live-ops/`：
- `seasons/S[N]_[name].md` — 赛季设计文档（来自阶段 1-3）
- `seasons/S[N]_[name]_analytics.md` — 分析计划（来自阶段 4）
- `seasons/S[N]_[name]_comms.md` — 沟通日历（来自阶段 6）

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

If a BLOCKED state is unresolvable, end with Verdict: **BLOCKED** instead of COMPLETE.
如果 BLOCKED 状态无法解决，以结论结束：**已阻塞** 而非 已完成。

## File Write Protocol
## 文件写入协议

All file writes (season design docs, analytics plans, communication calendars) are
delegated to sub-agents spawned via Task. Each sub-agent enforces the
"May I write to [path]?" protocol. This orchestrator does not write files directly.
所有文件写入（赛季设计文档、分析计划、沟通日历）都
委派给通过 Task 派生的子智能体。每个子智能体强制执行
"May I write to [path]?" 协议。此编排器不直接写入文件。

## Output
## 输出

A summary covering: season theme and scope, economy design highlights, success metrics, content list, communication plan, and any open decisions needing user input before production.
一份摘要，涵盖：赛季主题和范围、经济设计亮点、成功指标、内容列表、沟通计划以及生产前需要用户输入的任何待决决策。

Verdict: **COMPLETE** — season plan produced and handed off for production.
结论：**已完成** — 赛季计划已生成并移交生产。

## Next Steps
## 后续步骤

- Run `/design-review` on the season design document for consistency validation.
- Run `/sprint-plan` to schedule content creation work for the season.
- Run `/team-release` when the season content is ready to deploy.
- 对赛季设计文档运行 `/design-review` 进行一致性验证。
- 运行 `/sprint-plan` 安排赛季的内容创建工作。
- 当赛季内容准备部署时运行 `/team-release`。
