---
name: milestone-review
description: "Generates a comprehensive milestone progress review including feature completeness, quality metrics, risk assessment, and go/no-go recommendation. Use at milestone checkpoints or when evaluating readiness for a milestone deadline."
argument-hint: "[milestone-name|current] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
model: sonnet
---
---
名称：milestone-review
描述："生成全面的里程碑进度评审，包括功能完整性、质量指标、风险评估以及通过/不通过建议。用于里程碑检查点或评估里程碑截止日期就绪状态时使用。"
argument-hint："[milestone-name|current] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Task, AskUserQuestion
model：sonnet
---

## Phase 0: Parse Arguments
## 阶段 0：解析参数

Extract the milestone name (`current` or a specific name) and resolve the review mode (once, store for all gate spawns this run):
提取里程碑名称（`current` 或具体名称），并解析评审模式（解析一次，本次运行中所有门禁派生都使用该模式）：
1. If `--review [full|lean|solo]` was passed → use that
1. 如果传入了 `--review [full|lean|solo]` → 使用该值
2. Else read `production/review-mode.txt` → use that value
2. 否则读取 `production/review-mode.txt` → 使用该值
3. Else → default to `lean`
3. 否则 → 默认为 `lean`

See `.claude/docs/director-gates.md` for the full check pattern.
完整的检查模式请参见 `.claude/docs/director-gates.md`。

---

## Phase 1: Load Milestone Data
## 阶段 1：加载里程碑数据

Read the milestone definition from `production/milestones/`. If the argument is `current`, use the most recently modified milestone file.
从 `production/milestones/` 读取里程碑定义。如果参数为 `current`，则使用最近修改的里程碑文件。

Read all sprint reports for sprints within this milestone from `production/sprints/`.
从 `production/sprints/` 读取该里程碑内所有冲刺的冲刺报告。

---

## Phase 2: Scan Codebase Health
## 阶段 2：扫描代码库健康状态

- Scan for `TODO`, `FIXME`, `HACK` markers that indicate incomplete work
- 扫描指示未完成工作的 `TODO`、`FIXME`、`HACK` 标记
- Check the risk register at `production/risk-register/`
- 检查 `production/risk-register/` 中的风险登记册

---

## Phase 3: Generate the Milestone Review
## 阶段 3：生成里程碑评审

```markdown
# Milestone Review: [Milestone Name]
```
```markdown
# 里程碑评审：[里程碑名称]
```

```markdown
## Overview
- **Target Date**: [Date]
- **Current Date**: [Today]
- **Days Remaining**: [N]
- **Sprints Completed**: [X/Y]
```
```markdown
## 概览
- **目标日期**：[日期]
- **当前日期**：[今日]
- **剩余天数**：[N]
- **已完成冲刺数**：[X/Y]
```

```markdown
## Feature Completeness
```
```markdown
## 功能完整性
```

```markdown
### Fully Complete
| Feature | Acceptance Criteria | Test Status |
|---------|-------------------|-------------|
```
```markdown
### 完全完成
| 功能 | 验收标准 | 测试状态 |
|---------|-------------------|-------------|
```

```markdown
### Partially Complete
| Feature | % Done | Remaining Work | Risk to Milestone |
|---------|--------|---------------|------------------|
```
```markdown
### 部分完成
| 功能 | 完成百分比 | 剩余工作 | 对里程碑的风险 |
|---------|--------|---------------|------------------|
```

```markdown
### Not Started
| Feature | Priority | Can Cut? | Impact of Cutting |
|---------|----------|----------|------------------|
```
```markdown
### 未开始
| 功能 | 优先级 | 可否裁剪？ | 裁剪的影响 |
|---------|----------|----------|------------------|
```

```markdown
## Quality Metrics
- **Open S1 Bugs**: [N] -- [List]
- **Open S2 Bugs**: [N]
- **Open S3 Bugs**: [N]
- **Test Coverage**: [X%]
- **Performance**: [Within budget? Details]
```
```markdown
## 质量指标
- **未解决的 S1 Bug**：[N] —— [列表]
- **未解决的 S2 Bug**：[N]
- **未解决的 S3 Bug**：[N]
- **测试覆盖率**：[X%]
- **性能**：[是否在预算内？详细信息]
```

```markdown
## Code Health
- **TODO count**: [N across codebase]
- **FIXME count**: [N]
- **HACK count**: [N]
- **Technical debt items**: [List critical ones]
```
```markdown
## 代码健康
- **TODO 计数**：[整个代码库共 N 处]
- **FIXME 计数**：[N]
- **HACK 计数**：[N]
- **技术债务条目**：[列出关键项]
```

```markdown
## Risk Assessment
| Risk | Status | Impact if Realized | Mitigation Status |
|------|--------|-------------------|------------------|
```
```markdown
## 风险评估
| 风险 | 状态 | 若发生的影响 | 缓解状态 |
|------|--------|-------------------|------------------|
```

```markdown
## Velocity Analysis
- **Planned vs Completed** (across all sprints): [X/Y tasks = Z%]
- **Trend**: [Improving / Stable / Declining]
- **Adjusted estimate for remaining work**: [Days needed at current velocity]
```
```markdown
## 速率分析
- **计划与完成对比**（所有冲刺合计）：[X/Y 任务 = Z%]
- **趋势**：[上升 / 稳定 / 下降]
- **剩余工作的调整估算**：[按当前速率所需天数]
```

```markdown
## Scope Recommendations
### Protect (Must ship with milestone)
- [Feature and why]
```
```markdown
## 范围建议
### 保护（必须随里程碑发布）
- [功能及原因]
```

```markdown
### At Risk (May need to cut or simplify)
- [Feature and risk]
```
```markdown
### 风险（可能需要裁剪或简化）
- [功能及风险]
```

```markdown
### Cut Candidates (Can defer without compromising milestone)
- [Feature and impact of cutting]
```
```markdown
### 裁剪候选（可以推迟而不影响里程碑）
- [功能及裁剪的影响]
```

```markdown
## Go/No-Go Assessment
```
```markdown
## 通过/不通过评估
```

```markdown
**Recommendation**: [GO / CONDITIONAL GO / NO-GO]
```
```markdown
**建议**：[通过 / 有条件通过 / 不通过]
```

```markdown
**Conditions** (if conditional):
- [Condition 1 that must be met]
- [Condition 2 that must be met]
```
```markdown
**条件**（若有条件）：
- [必须满足的条件 1]
- [必须满足的条件 2]
```

```markdown
**Rationale**: [Explanation of the recommendation]
```
```markdown
**理由**：[建议的解释]
```

```markdown
## Action Items
| # | Action | Owner | Deadline |
|---|--------|-------|----------|
```
```markdown
## 行动项
| # | 行动 | 负责人 | 截止日期 |
|---|--------|-------|----------|
```

---

## Phase 3b: Producer Risk Assessment
## 阶段 3b：制作人风险评估

**Review mode check** — apply before spawning PR-MILESTONE:
**评审模式检查** —— 在派生 PR-MILESTONE 前应用：
- `solo` → skip. Note: "PR-MILESTONE skipped — Solo mode." Present the Go/No-Go section without a producer verdict.
- `solo` → 跳过。备注："PR-MILESTONE 已跳过 —— Solo 模式。" 呈现通过/不通过部分但不给出制作人裁定。
- `lean` → skip (not a PHASE-GATE). Note: "PR-MILESTONE skipped — Lean mode." Present the Go/No-Go section without a producer verdict.
- `lean` → 跳过（非 PHASE-GATE）。备注："PR-MILESTONE 已跳过 —— Lean 模式。" 呈现通过/不通过部分但不给出制作人裁定。
- `full` → spawn as normal.
- `full` → 正常派生。

Before generating the Go/No-Go recommendation, spawn `producer` via Task using gate **PR-MILESTONE** (`.claude/docs/director-gates.md`).
在生成通过/不通过建议前，通过 Task 使用门禁 **PR-MILESTONE**（`.claude/docs/director-gates.md`）派生 `producer`。

Pass: milestone name and target date, current completion percentage, blocked story count, velocity data from sprint reports (if available), list of cut candidates.
传递：里程碑名称和目标日期、当前完成百分比、受阻故事数、冲刺报告中的速率数据（如有）、裁剪候选列表。

Present the producer's assessment inline within the Go/No-Go section. The producer's verdict (ON TRACK / AT RISK / OFF TRACK) informs the overall recommendation.
在通过/不通过部分内联呈现制作人评估。制作人的裁定（在轨 / 有风险 / 偏离）将影响整体建议。

If OFF TRACK, use `AskUserQuestion` before generating the recommendation:
若偏离，在生成建议前使用 `AskUserQuestion`：
- Prompt: "Producer verdict: OFF TRACK. The milestone is in jeopardy. This review will recommend NO-GO. How do you want to proceed?"
- 提示："制作人裁定：偏离。里程碑面临风险。本次评审将建议不通过。您希望如何进行？"
- Options:
- 选项：
  - `[A] Accept NO-GO — generate the full review with that recommendation`
  - `[A] 接受不通过 —— 以该建议生成完整评审`
  - `[B] Override to CONDITIONAL GO — I'll document the accepted risks myself`
  - `[B] 覆盖为有条件通过 —— 我将自行记录所接受的风险`
  - `[C] Stop — I want to address blockers before generating the review`
  - `[C] 停止 —— 我想在生成评审前先解决阻碍`

If AT RISK, use `AskUserQuestion`:
若有风险，使用 `AskUserQuestion`：
- Prompt: "Producer verdict: AT RISK. Milestone may slip. How should the Go/No-Go section be framed?"
- 提示："制作人裁定：有风险。里程碑可能延期。通过/不通过部分应如何表述？"
- Options:
- 选项：
  - `[A] CONDITIONAL GO — include producer's conditions in the review`
  - `[A] 有条件通过 —— 在评审中包含制作人的条件`
  - `[B] NO-GO — conditions cannot be met in time`
  - `[B] 不通过 —— 条件无法按时满足`
  - `[C] GO — I accept the risk and want to proceed`
  - `[C] 通过 —— 我接受风险并希望继续`

Do not issue a GO against an OFF TRACK verdict unless the user explicitly selects [B] above.
除非用户明确选择上述 [B]，否则不得针对偏离裁定作出通过建议。

---

## Phase 4: Save Review
## 阶段 4：保存评审

Present the review to the user.
向用户呈现评审。

Ask: "May I write this to `production/milestones/[milestone-name]-review.md`?"
询问："我可以将此写入 `production/milestones/[milestone-name]-review.md` 吗？"

If yes, write the file, creating the directory if needed. Verdict: **COMPLETE** — milestone review saved.
若是，写入文件，必要时创建目录。裁定：**COMPLETE** —— 里程碑评审已保存。

If no, stop here. Verdict: **BLOCKED** — user declined write.
若否，在此停止。裁定：**BLOCKED** —— 用户拒绝写入。

---

## Phase 5: Next Steps
## 阶段 5：后续步骤

- Run `/gate-check` for a formal phase gate verdict if this milestone marks a development phase boundary.
- 如果该里程碑标志着开发阶段边界，运行 `/gate-check` 获取正式阶段门禁裁定。
- Run `/sprint-plan` to adjust the next sprint based on the scope recommendations above.
- 运行 `/sprint-plan`，根据上述范围建议调整下一个冲刺。
