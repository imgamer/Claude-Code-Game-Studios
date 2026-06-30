---
name: team-release
description: "Orchestrate the release team: coordinates release-manager, qa-lead, devops-engineer, and producer to execute a release from candidate to deployment."
argument-hint: "[version number or 'next'] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---
---
name: team-release
description: "编排发布团队：协调 release-manager、qa-lead、devops-engineer 和 producer，执行从候选到部署的发布。"
argument-hint: "[version number or 'next'] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---
**Argument check:** If no version number is provided:
1. Read `production/session-state/active.md` and the most recent file in `production/milestones/` (if they exist) to infer the target version.
2. If a version is found: report "No version argument provided — inferred [version] from milestone data. Proceeding." Then confirm with `AskUserQuestion`: "Releasing [version]. Is this correct?"
3. If no version is discoverable: use `AskUserQuestion` to ask "What version number should be released? (e.g., v1.0.0)" and wait for user input before proceeding. Do NOT default to a hardcoded version string.
**参数检查：** 如果未提供版本号：
1. 读取 `production/session-state/active.md` 和 `production/milestones/` 中最近的文件（如果存在）以推断目标版本。
2. 如果找到版本：报告 "No version argument provided — inferred [version] from milestone data. Proceeding." 然后通过 `AskUserQuestion` 确认："Releasing [version]. Is this correct?"
3. 如果无法发现版本：使用 `AskUserQuestion` 询问 "What version number should be released? (e.g., v1.0.0)" 并在继续之前等待用户输入。切勿默认为硬编码的版本字符串。

When this skill is invoked, orchestrate the release team through a structured pipeline.
当此技能被调用时，通过结构化的管线编排发布团队。

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
- **release-manager** — Release branch, versioning, changelog, deployment
- **qa-lead** — Test sign-off, regression suite, release quality gate
- **devops-engineer** — Build pipeline, artifacts, deployment automation
- **security-engineer** — Pre-release security audit (invoke if game has online/multiplayer features or player data)
- **analytics-engineer** — Verify telemetry events fire correctly and dashboards are live
- **community-manager** — Patch notes, launch announcement, player-facing messaging
- **producer** — Go/no-go decision, stakeholder communication, scheduling
- **release-manager** — 发布分支、版本控制、变更日志、部署
- **qa-lead** — 测试签署、回归套件、发布质量门禁
- **devops-engineer** — 构建管线、工件、部署自动化
- **security-engineer** — 发布前安全审计（如果游戏有在线/多人功能或玩家数据则调用）
- **analytics-engineer** — 验证遥测事件正确触发且仪表板已上线
- **community-manager** — 补丁说明、发布公告、面向玩家的消息
- **producer** — 上线/不上线决策、利益相关者沟通、调度

## How to Delegate
## 如何委派

Use the Task tool to spawn each team member as a subagent:
- `subagent_type: release-manager` — Release branch, versioning, changelog, deployment
- `subagent_type: qa-lead` — Test sign-off, regression suite, release quality gate
- `subagent_type: devops-engineer` — Build pipeline, artifacts, deployment automation
- `subagent_type: security-engineer` — Security audit for online/multiplayer/data features
- `subagent_type: analytics-engineer` — Telemetry event verification and dashboard readiness
- `subagent_type: community-manager` — Patch notes and launch communication
- `subagent_type: producer` — Go/no-go decision, stakeholder communication
- `subagent_type: network-programmer` — Netcode stability sign-off (invoke if game has multiplayer)
使用 Task 工具将每个团队成员派生为子智能体：
- `subagent_type: release-manager` — 发布分支、版本控制、变更日志、部署
- `subagent_type: qa-lead` — 测试签署、回归套件、发布质量门禁
- `subagent_type: devops-engineer` — 构建管线、工件、部署自动化
- `subagent_type: security-engineer` — 在线/多人/数据功能的安全审计
- `subagent_type: analytics-engineer` — 遥测事件验证和仪表板就绪度
- `subagent_type: community-manager` — 补丁说明和发布沟通
- `subagent_type: producer` — 上线/不上线决策、利益相关者沟通
- `subagent_type: network-programmer` — 网络代码稳定性签署（如果游戏有多人模式则调用）

Always provide full context in each agent's prompt (version number, milestone status, known issues). Launch independent agents in parallel where the pipeline allows it (e.g., Phase 3 agents can run simultaneously).
始终在每个智能体的提示中提供完整上下文（版本号、里程碑状态、已知问题）。在管线允许的情况下并行启动独立的智能体（例如，阶段 3 的智能体可以同时运行）。

## Pipeline
## 管线

### Phase 1: Release Planning
### 阶段 1：发布规划
Delegate to **producer**:
- Confirm all milestone acceptance criteria are met
- Identify any scope items deferred from this release
- Set the target release date and communicate to team
- Output: release authorization with scope confirmation
委派给 **producer**：
- 确认所有里程碑验收标准均已满足
- 识别从此发布中推迟的任何范围项
- 设定目标发布日期并与团队沟通
- 输出：带范围确认的发布授权

### Phase 2: Release Candidate
### 阶段 2：发布候选
Delegate to **release-manager**:
- Cut release branch from the agreed commit
- Bump version numbers in all relevant files
- Generate the release checklist using `/release-checklist`
- Freeze the branch — no feature changes, bug fixes only
- Output: release branch name and checklist
委派给 **release-manager**：
- 从商定的提交切出发布分支
- 在所有相关文件中提升版本号
- 使用 `/release-checklist` 生成发布检查清单
- 冻结分支 — 无功能变更，仅修复缺陷
- 输出：发布分支名称和检查清单

### Phase 3: Quality Gate (parallel)
### 阶段 3：质量门禁（并行）
Delegate in parallel:
- **qa-lead**: Execute full regression test suite. Test all critical paths. Verify no S1/S2 bugs. Sign off on quality.
- **devops-engineer**: Build release artifacts for all target platforms. Verify builds are clean and reproducible. Run automated tests in CI.
- **security-engineer** *(if game has online features, multiplayer, or player data)*: Conduct pre-release security audit. Review authentication, anti-cheat, data privacy compliance. Sign off on security posture.
- **network-programmer** *(if game has multiplayer)*: Sign off on netcode stability. Verify lag compensation, reconnect handling, and bandwidth usage under load.
并行委派：
- **qa-lead**：执行完整回归测试套件。测试所有关键路径。验证无 S1/S2 缺陷。签署质量。
- **devops-engineer**：为所有目标平台构建发布工件。验证构建干净且可重现。在 CI 中运行自动化测试。
- **security-engineer** *（如果游戏有在线功能、多人模式或玩家数据）*：进行发布前安全审计。审查认证、反作弊、数据隐私合规。签署安全态势。
- **network-programmer** *（如果游戏有多人模式）*：签署网络代码稳定性。验证延迟补偿、重连处理和负载下的带宽使用。

### Phase 4: Localization, Performance, and Analytics
### 阶段 4：本地化、性能和分析
Delegate (can run in parallel with Phase 3 if resources available):
- Verify all strings are translated (delegate to **localization-lead** if available)
- Run performance benchmarks against targets (delegate to **performance-analyst** if available)
- **analytics-engineer**: Verify all telemetry events fire correctly on release build. Confirm dashboards are receiving data. Check that critical funnels (onboarding, progression, monetization if applicable) are instrumented.
- Output: localization, performance, and analytics sign-off
委派（如果资源允许可与阶段 3 并行运行）：
- 验证所有字符串已翻译（如果可用，委派给 **localization-lead**）
- 针对目标运行性能基准（如果可用，委派给 **performance-analyst**）
- **analytics-engineer**：验证所有遥测事件在发布构建上正确触发。确认仪表板正在接收数据。检查关键漏斗（新手引导、进度、变现（如适用））是否已埋点。
- 输出：本地化、性能和分析签署

### Phase 5: Go/No-Go
### 阶段 5：上线/不上线
Delegate to **producer**:
- Collect sign-off from: qa-lead, release-manager, devops-engineer, security-engineer (if spawned in Phase 3), network-programmer (if spawned in Phase 3), and technical-director
- Evaluate any open issues — are they blocking or can they ship?
- Make the go/no-go call
- Output: release decision with rationale
委派给 **producer**：
- 收集以下方面的签署：qa-lead、release-manager、devops-engineer、security-engineer（如果在阶段 3 派生）、network-programmer（如果在阶段 3 派生）和 technical-director
- 评估任何待决问题 — 它们是否阻塞或可以发布？
- 做出上线/不上线决策
- 输出：带理由的发布决策

**If producer declares NO-GO:**
- Surface the decision immediately: "PRODUCER: NO-GO — [rationale, e.g., S1 bug found in Phase 3]."
- Use `AskUserQuestion` with options:
  - Fix the blocker and re-run the affected phase
  - Defer the release to a later date
  - Override NO-GO with documented rationale (user must provide written justification)
- **Skip Phase 6 entirely** — do not tag, deploy to staging, deploy to production, or spawn community-manager.
- Produce a partial report summarizing Phases 1–5 and what was skipped (Phase 6) and why.
- Verdict: **BLOCKED** — release not deployed.
**如果 producer 宣布 NO-GO：**
- 立即呈现决策："PRODUCER: NO-GO — [rationale, e.g., S1 bug found in Phase 3]."
- 使用 `AskUserQuestion` 提供选项：
  - 修复阻塞项并重新运行受影响的阶段
  - 将发布推迟到更晚的日期
  - 附带记录的理由覆盖 NO-GO（用户必须提供书面理由）
- **完全跳过阶段 6** — 不要打标签、部署到预发布、部署到生产或派生 community-manager。
- 生成部分报告总结阶段 1-5 以及跳过的内容（阶段 6）和原因。
- 结论：**已阻塞** — 发布未部署。

After the user selects "Override NO-GO with documented rationale":
- Ask (plain text, not widget): "Please describe the justification for overriding the NO-GO verdict. This will be embedded in the release record."
- Wait for the user's written justification.
- Embed the justification text in the partial approval record before Phase 6: append a "⚠️ Override Justification: [user's text]" field.
- Only then proceed to Phase 6.
在用户选择 "Override NO-GO with documented rationale" 之后：
- 询问（纯文本，非小部件）："Please describe the justification for overriding the NO-GO verdict. This will be embedded in the release record."
- 等待用户的书面理由。
- 在阶段 6 之前将理由文本嵌入部分批准记录中：追加 "⚠️ Override Justification: [user's text]" 字段。
- 仅然后继续至阶段 6。

### Phase 6: Deployment (if GO)
### 阶段 6：部署（如果 GO）
Delegate to **release-manager** + **devops-engineer**:
- Tag the release in version control
- Generate changelog using `/changelog`
- Deploy to staging for final smoke test
- Deploy to production
- Human team action: Monitor dashboards and error rates for 48 hours post-release. Schedule a follow-up retrospective using `/retrospective` at the 48-hour mark.
委派给 **release-manager** + **devops-engineer**：
- 在版本控制中标记发布
- 使用 `/changelog` 生成变更日志
- 部署到预发布以进行最终冒烟测试
- 部署到生产
- 人工团队行动：发布后监控仪表板和错误率 48 小时。在 48 小时标记处使用 `/retrospective` 安排后续回顾。

Delegate to **community-manager** (in parallel with deployment):
- Finalize patch notes using `/patch-notes [version]`
- Prepare launch announcement (store page updates, social media, community post)
- Draft known issues post if any S3+ issues shipped
- Output: all player-facing release communication, ready to publish on deploy confirmation
委派给 **community-manager**（与部署并行）：
- 使用 `/patch-notes [version]` 最终确定补丁说明
- 准备发布公告（商店页面更新、社交媒体、社区帖子）
- 如果发布了任何 S3+ 问题，起草已知问题帖子
- 输出：所有面向玩家的发布沟通，准备在部署确认时发布

### Phase 7: Post-Release
### 阶段 7：发布后
- **release-manager**: Generate release report (what shipped, what was deferred, metrics)
- **producer**: Update milestone tracking, communicate to stakeholders
- **qa-lead**: Monitor incoming bug reports for regressions
- **community-manager**: Publish all player-facing communication, monitor community sentiment
- **analytics-engineer**: Confirm live dashboards are healthy; alert if any critical events are missing
- Schedule post-release retrospective if issues occurred
- **release-manager**：生成发布报告（发布了什么、推迟了什么、指标）
- **producer**：更新里程碑跟踪，与利益相关者沟通
- **qa-lead**：监控传入的缺陷报告以发现回归
- **community-manager**：发布所有面向玩家的沟通，监控社区情绪
- **analytics-engineer**：确认实时仪表板健康；如果缺少任何关键事件则警报
- 如果发生问题，安排发布后回顾

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

All file writes (release checklists, changelogs, patch notes, deployment scripts) are
delegated to sub-agents and sub-skills. Each enforces the "May I write to [path]?"
protocol. This orchestrator does not write files directly.
所有文件写入（发布检查清单、变更日志、补丁说明、部署脚本）都
委派给子智能体和子技能。每个都强制执行 "May I write to [path]?"
协议。此编排器不直接写入文件。

## Output
## 输出

A summary report covering: release version, scope, quality gate results, go/no-go decision, deployment status, and monitoring plan.
一份摘要报告，涵盖：发布版本、范围、质量门禁结果、上线/不上线决策、部署状态和监控计划。

Verdict: **COMPLETE** — release executed and deployed.
Verdict: **BLOCKED** — release halted; go/no-go was NO or a hard blocker is unresolved.
结论：**已完成** — 发布已执行并部署。
结论：**已阻塞** — 发布暂停；上线/不上线为否或有未解决的硬阻塞项。

## Next Steps
## 后续步骤

- Monitor post-release dashboards for 48 hours.
- Run `/retrospective` if significant issues occurred during the release.
- Update `production/stage.txt` to `Live` after successful deployment.
- 发布后监控仪表板 48 小时。
- 如果发布期间发生重大问题，运行 `/retrospective`。
- 成功部署后将 `production/stage.txt` 更新为 `Live`。
