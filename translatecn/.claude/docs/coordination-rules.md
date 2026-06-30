# Agent Coordination Rules
# 智能体协调规则

1. **Vertical Delegation**: Leadership agents delegate to department leads, who
   delegate to specialists. Never skip a tier for complex decisions.
1. **垂直委派**：领导智能体委派给部门主管，主管再委派给专家。复杂决策绝不跨级。
2. **Horizontal Consultation**: Agents at the same tier may consult each other
   but must not make binding decisions outside their domain.
2. **横向协商**：同层级的智能体可以相互协商，但不得在其领域之外做出约束性决策。
3. **Conflict Resolution**: When two agents disagree, escalate to the shared
   parent. If no shared parent, escalate to `creative-director` for design
   conflicts or `technical-director` for technical conflicts.
3. **冲突解决**：当两个智能体意见不合时，升级至共同上级。如无共同上级，设计冲突升级至 `creative-director`，技术冲突升级至 `technical-director`。
4. **Change Propagation**: When a design change affects multiple domains, the
   `producer` agent coordinates the propagation.
4. **变更传播**：当设计变更影响多个领域时，由 `producer` 智能体协调传播。
5. **No Unilateral Cross-Domain Changes**: An agent must never modify files
   outside its designated directories without explicit delegation.
5. **禁止单方面跨领域变更**：智能体绝不应在未获得明确委派的情况下修改其指定目录之外的文件。

## Model Tier Assignment
## 模型层级分配

Skills and agents are assigned to model tiers based on task complexity:
技能和智能体根据任务复杂度分配到不同模型层级：

| Tier | Model | When to use |
| 层级 | 模型 | 使用时机 |
|------|-------|-------------|
| **Haiku** | `claude-haiku-4-5-20251001` | Read-only status checks, formatting, simple lookups — no creative judgment needed |
| **Haiku** | `claude-haiku-4-5-20251001` | 只读状态检查、格式化、简单查询 — 无需创造性判断 |
| **Sonnet** | `claude-sonnet-4-6` | Implementation, design authoring, analysis of individual systems — default for most work |
| **Sonnet** | `claude-sonnet-4-6` | 实现、设计编写、单系统分析 — 大部分工作的默认选择 |
| **Opus** | `claude-opus-4-6` | Multi-document synthesis, high-stakes phase gate verdicts, cross-system holistic review |
| **Opus** | `claude-opus-4-6` | 多文档综合、高风险阶段门禁裁决、跨系统整体评审 |

Skills with `model: haiku`: `/help`, `/sprint-status`, `/story-readiness`, `/scope-check`,
`/project-stage-detect`, `/changelog`, `/patch-notes`, `/onboard`
`model: haiku` 的技能：`/help`、`/sprint-status`、`/story-readiness`、`/scope-check`、
`/project-stage-detect`、`/changelog`、`/patch-notes`、`/onboard`

Skills with `model: opus`: `/review-all-gdds`, `/architecture-review`, `/gate-check`
`model: opus` 的技能：`/review-all-gdds`、`/architecture-review`、`/gate-check`

All other skills default to Sonnet. When creating new skills, assign Haiku if the
skill only reads and formats; assign Opus if it must synthesize 5+ documents with
high-stakes output; otherwise leave unset (Sonnet).
所有其他技能默认使用 Sonnet。创建新技能时，如果该技能仅读取并格式化数据则分配 Haiku；如果必须综合 5 个以上文档并产出高风险结果则分配 Opus；否则不设置（使用 Sonnet）。

## Subagents vs Agent Teams
## 子智能体 vs 智能体团队

This project uses two distinct multi-agent patterns:
本项目使用两种不同的多智能体模式：

### Subagents (current, always active)
### 子智能体（当前模式，始终激活）
Spawned via `Task` within a single Claude Code session. Used by all `team-*` skills
and orchestration skills. Subagents share the session's permission context, run
sequentially or in parallel within the session, and return results to the parent.
通过单个 Claude Code 会话中的 `Task` 生成。被所有 `team-*` 技能和编排技能使用。子智能体共享会话的权限上下文，在会话内顺序或并行运行，并将结果返回给父级。

**When to spawn in parallel**: If two subagents' inputs are independent (neither
needs the other's output to begin), spawn both Task calls simultaneously rather
than waiting. Example: `/review-all-gdds` Phase 1 (consistency) and Phase 2
(design theory) are independent — spawn both at the same time.
**何时并行生成**：如果两个子智能体的输入相互独立（都不需要另一个的输出才能开始），则同时生成两个 Task 调用，而非等待。例如：`/review-all-gdds` 阶段 1（一致性）和阶段 2（设计理论）相互独立 — 同时生成两者。

### Agent Teams (experimental — opt-in)
### 智能体团队（实验性 — 选择启用）
Multiple independent Claude Code *sessions* running simultaneously, coordinated
via a shared task list. Each session has its own context window and token budget.
Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` environment variable.
多个独立的 Claude Code *会话* 同时运行，通过共享任务列表协调。每个会话有自己的上下文窗口和 token 预算。需要 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 环境变量。

**Use agent teams when**:
**何时使用智能体团队**：
- Work spans multiple subsystems that will not touch the same files
- 工作跨越多个不会触碰相同文件的子系统
- Each workstream would take >30 minutes and benefits from true parallelism
- 每个工作流耗时 >30 分钟，且能从真正的并行中受益
- A senior agent (technical-director, producer) needs to coordinate 3+ specialist
  sessions working on different epics simultaneously
- 高级智能体（technical-director、producer）需要协调 3 个以上同时处理不同 epic 的专家会话

**Do not use agent teams when**:
**何时不应使用智能体团队**：
- One session's output is required as input for another (use sequential subagents)
- 一个会话的输出是另一个会话的输入（使用顺序子智能体）
- The task fits in a single session's context (use subagents instead)
- 任务可容纳在单个会话的上下文中（改用子智能体）
- Cost is a concern — each team member burns tokens independently
- 成本是关注点 — 每个团队成员独立消耗 token

**Current status**: Opt-in via `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. Document first usage here when adopted.
**当前状态**：通过 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 选择启用。首次采用时在此记录。

## Parallel Task Protocol
## 并行任务协议

When an orchestration skill spawns multiple independent agents:
当编排技能生成多个独立智能体时：

1. Issue all independent Task calls before waiting for any result
1. 在等待任何结果之前，发出所有独立的 Task 调用
2. Collect all results before proceeding to dependent phases
2. 在进入依赖阶段之前收集所有结果
3. If any agent is BLOCKED, surface it immediately — do not silently skip
3. 如果任何智能体被 BLOCKED，立即暴露 — 不要静默跳过
4. Always produce a partial report if some agents complete and others block
4. 如果部分智能体完成而其他被阻塞，始终产出部分报告
