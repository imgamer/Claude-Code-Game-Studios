---
name: sprint-plan
description: "Generates a new sprint plan or updates an existing one based on the current milestone, completed work, and available capacity. Pulls context from production documents and design backlogs."
argument-hint: "[new|update|status] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
context: |
  !ls production/sprints/ 2>/dev/null
---
---
name: sprint-plan
description: "基于当前里程碑、已完成工作和可用容量生成新冲刺计划或更新现有计划。从生产文档和设计积压中拉取上下文。"
argument-hint: "[new|update|status] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
context: |
  !ls production/sprints/ 2>/dev/null
---

## Phase 0: Parse Arguments

Extract the mode argument (`new`, `update`, or `status`) and resolve the review mode (once, store for all gate spawns this run):
1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`

See `.claude/docs/director-gates.md` for the full check pattern.

**Review mode check** (before gates run):
- Read `production/review-mode.txt` if it exists. Use that mode.
- If the file doesn't exist and this is a `new` sprint: use `AskUserQuestion`:
  - Prompt: "No review mode is set. Which review depth would you like for this sprint?"
  - Options:
    - `[A] full — spawn all director and lead gates`
    - `[B] lean — skip non-phase-gate director reviews (recommended for most sprints)`
    - `[C] solo — skip all gate spawning`
  - After selection: write `production/review-mode.txt` with the chosen mode. Say: "Review mode set to [mode] and saved to production/review-mode.txt."
- If the file doesn't exist and this is NOT a `new` sprint (e.g., updating an existing sprint): default to `lean` silently.

## 第 0 阶段：解析参数

提取模式参数（`new`、`update` 或 `status`）并解析评审模式（仅一次，保存供本次运行所有门禁派生使用）：
1. 如传入 `--review [full|lean|solo]` → 使用该值
2. 否则读取 `production/review-mode.txt` → 使用该值
3. 否则 → 默认为 `lean`

完整检查模式见 `.claude/docs/director-gates.md`。

**评审模式检查**（门禁运行前）：
- 如 `production/review-mode.txt` 存在则读取它。使用该模式。
- 如文件不存在且这是 `new` 冲刺：使用 `AskUserQuestion`：
  - 提示："未设置评审模式。此冲刺你想要多大评审深度？"
  - 选项：
    - `[A] full —— 派生所有总监和负责人门禁`
    - `[B] lean —— 跳过非阶段门禁的总监评审（对大多数冲刺推荐）`
    - `[C] solo —— 跳过所有门禁派生`
  - 选择后：将所选模式写入 `production/review-mode.txt`。说："评审模式设置为 [mode] 并保存到 production/review-mode.txt."
- 如文件不存在且这不是 `new` 冲刺（例如更新现有冲刺）：静默默认为 `lean`。

---

## Phase 1: Gather Context

1. **Read the current milestone** from `production/milestones/`.

2. **Read the previous sprint** (if any) from `production/sprints/` to
   understand velocity and carryover.

3. **Scan design documents** in `design/gdd/` for features tagged as ready
   for implementation.

4. **Check the risk register** at `production/risk-register/`.

## 第 1 阶段：收集上下文

1. **从 `production/milestones/` 读取当前里程碑**。

2. **从 `production/sprints/` 读取上一个冲刺**（如有）以
   了解速度和结转。

3. **扫描 `design/gdd/` 中的设计文档**，查找标记为可实现的
   功能。

4. **检查 `production/risk-register/` 处的风险登记表**。

---

## Phase 2: Generate Output

For `new`:

**Generate a sprint plan** following this format and present it to the user. Do NOT ask to write yet — the producer feasibility gate (Phase 4) runs first and may require revisions before the file is written.

```markdown
# Sprint [N] — [Start Date] to [End Date]

## Sprint Goal
[One sentence describing what this sprint achieves toward the milestone]

## Capacity
- Total days: [X]
- Buffer (20%): [Y days reserved for unplanned work]
- Available: [Z days]

## Tasks

### Must Have (Critical Path)
| ID | Task | Agent/Owner | Est. Days | Dependencies | Acceptance Criteria |
|----|------|-------------|-----------|-------------|-------------------|

### Should Have
| ID | Task | Agent/Owner | Est. Days | Dependencies | Acceptance Criteria |
|----|------|-------------|-----------|-------------|-------------------|

### Nice to Have
| ID | Task | Agent/Owner | Est. Days | Dependencies | Acceptance Criteria |
|----|------|-------------|-----------|-------------|-------------------|

## Carryover from Previous Sprint
| Task | Reason | New Estimate |
|------|--------|-------------|

## Risks
| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|

## Dependencies on External Factors
- [List any external dependencies]

## Definition of Done for this Sprint
- [ ] All Must Have tasks completed
- [ ] All tasks pass acceptance criteria
- [ ] QA plan exists (`production/qa/qa-plan-sprint-[N].md`)
- [ ] All Logic/Integration stories have passing unit/integration tests
- [ ] Smoke check passed (`/smoke-check sprint`)
- [ ] QA sign-off report: APPROVED or APPROVED WITH CONDITIONS (`/team-qa sprint`)
- [ ] No S1 or S2 bugs in delivered features
- [ ] Design documents updated for any deviations
- [ ] Code reviewed and merged
```

For `update`:

**Update an existing sprint plan**:

1. Read the most recent sprint plan from `production/sprints/`.
2. Present the current story list with their current statuses from `production/sprint-status.yaml`.
3. Ask the user what to change: stories to add, remove, reprioritize, or re-estimate. Use `AskUserQuestion` to gather changes.
4. Apply the changes and re-present the full revised plan for review.
5. Re-run the producer feasibility gate (Phase 4) on the revised plan.
6. Write the updated markdown plan and yaml together (same approval as `new` mode).

Note: `update` mode does not reset story statuses. Stories already marked `in-progress` or `done` keep their status. Only `backlog` and `ready-for-dev` stories can be removed or reprioritized freely.

For `status`:

**Generate a status report**:

```markdown
# Sprint [N] Status -- [Date]

## Progress: [X/Y tasks complete] ([Z%])

### Completed
| Task | Completed By | Notes |
|------|-------------|-------|

### In Progress
| Task | Owner | % Done | Blockers |
|------|-------|--------|----------|

### Not Started
| Task | Owner | At Risk? | Notes |
|------|-------|----------|-------|

### Blocked
| Task | Blocker | Owner of Blocker | ETA |
|------|---------|-----------------|-----|

## Burndown Assessment
[On track / Behind / Ahead]
[If behind: What is being cut or deferred]

## Emerging Risks
- [Any new risks identified this sprint]
```

## 第 2 阶段：生成输出

对于 `new`：

**按以下格式生成冲刺计划**并呈现给用户。不要先请求写入 —— 制作人可行性门禁（第 4 阶段）先运行，可能在写入文件前需要修订。

```markdown
# Sprint [N] — [Start Date] to [End Date]

## Sprint Goal
[One sentence describing what this sprint achieves toward the milestone]

## Capacity
- Total days: [X]
- Buffer (20%): [Y days reserved for unplanned work]
- Available: [Z days]

## Tasks

### Must Have (Critical Path)
| ID | Task | Agent/Owner | Est. Days | Dependencies | Acceptance Criteria |
|----|------|-------------|-----------|-------------|-------------------|

### Should Have
| ID | Task | Agent/Owner | Est. Days | Dependencies | Acceptance Criteria |
|----|------|-------------|-----------|-------------|-------------------|

### Nice to Have
| ID | Task | Agent/Owner | Est. Days | Dependencies | Acceptance Criteria |
|----|------|-------------|-----------|-------------|-------------------|

## Carryover from Previous Sprint
| Task | Reason | New Estimate |
|------|--------|-------------|

## Risks
| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|

## Dependencies on External Factors
- [List any external dependencies]

## Definition of Done for this Sprint
- [ ] All Must Have tasks completed
- [ ] All tasks pass acceptance criteria
- [ ] QA plan exists (`production/qa/qa-plan-sprint-[N].md`)
- [ ] All Logic/Integration stories have passing unit/integration tests
- [ ] Smoke check passed (`/smoke-check sprint`)
- [ ] QA sign-off report: APPROVED or APPROVED WITH CONDITIONS (`/team-qa sprint`)
- [ ] No S1 or S2 bugs in delivered features
- [ ] Design documents updated for any deviations
- [ ] Code reviewed and merged
```

对于 `update`：

**更新现有冲刺计划**：

1. 从 `production/sprints/` 读取最近的冲刺计划。
2. 呈现当前故事列表及其来自 `production/sprint-status.yaml` 的当前状态。
3. 询问用户要变更什么：添加、移除、重排优先级或重新估算的故事。使用 `AskUserQuestion` 收集变更。
4. 应用变更并重新呈现完整修订后的计划供评审。
5. 在修订后的计划上重新运行制作人可行性门禁（第 4 阶段）。
6. 一起写入更新后的 markdown 计划和 yaml（与 `new` 模式相同的批准）。

注意：`update` 模式不重置故事状态。已标记为 `in-progress` 或 `done` 的故事保持其状态。只有 `backlog` 和 `ready-for-dev` 故事可以自由移除或重排优先级。

对于 `status`：

**生成状态报告**：

```markdown
# Sprint [N] Status -- [Date]

## Progress: [X/Y tasks complete] ([Z%])

### Completed
| Task | Completed By | Notes |
|------|-------------|-------|

### In Progress
| Task | Owner | % Done | Blockers |
|------|-------|--------|----------|

### Not Started
| Task | Owner | At Risk? | Notes |
|------|-------|----------|-------|

### Blocked
| Task | Blocker | Owner of Blocker | ETA |
|------|---------|-----------------|-----|

## Burndown Assessment
[On track / Behind / Ahead]
[If behind: What is being cut or deferred]

## Emerging Risks
- [Any new risks identified this sprint]
```

---

## Phase 3: Prepare Sprint Status File

After generating a new sprint plan, also prepare the `production/sprint-status.yaml` content.
This is the machine-readable source of truth for story status — read by
`/sprint-status`, `/story-done`, and `/help` without markdown parsing.

**Do not write the yaml yet** — hold it in context. The producer feasibility gate (Phase 4) may revise the story list. Both files will be written together after Phase 4 in a single write approval.

Format:

```yaml
# Auto-generated by /sprint-plan. Updated by /story-done and /dev-story.
# DO NOT edit manually — use /story-done to update story status.
#
# Status value mapping (yaml ↔ story file Status field):
#   backlog        ↔  Not Started
#   ready-for-dev  ↔  Ready
#   in-progress    ↔  In Progress
#   review         ↔  In Review
#   done           ↔  Complete
#   blocked        ↔  Blocked

sprint: [N]
goal: "[sprint goal]"
start: "[YYYY-MM-DD]"
end: "[YYYY-MM-DD]"
generated: "[YYYY-MM-DD]"
updated: "[YYYY-MM-DD]"

stories:
  - id: "[epic-story, e.g. 1-1]"
    name: "[story name]"
    file: "[production/stories/path.md]"
    priority: must-have        # must-have | should-have | nice-to-have
    status: ready-for-dev      # backlog | ready-for-dev | in-progress | review | done | blocked
    owner: ""
    estimate_days: 0
    blocker: ""
    completed: ""
```

Initialize each story from the sprint plan's task tables:
- Must Have tasks → `priority: must-have`, `status: ready-for-dev`
- Should Have tasks → `priority: should-have`, `status: backlog`
- Nice to Have tasks → `priority: nice-to-have`, `status: backlog`

For `update`: read the existing `sprint-status.yaml`, carry over statuses for
stories that haven't changed, add new stories, remove dropped ones.

## 第 3 阶段：准备冲刺状态文件

生成新冲刺计划后，还要准备 `production/sprint-status.yaml` 内容。
这是故事状态的机器可读真实数据源 —— 由
`/sprint-status`、`/story-done` 和 `/help` 读取，无需 markdown 解析。

**暂不写入 yaml** —— 在上下文中保存。制作人可行性门禁（第 4 阶段）可能修订故事列表。两个文件将在第 4 阶段后通过单次写入批准一起写入。

格式：

```yaml
# Auto-generated by /sprint-plan. Updated by /story-done and /dev-story.
# DO NOT edit manually — use /story-done to update story status.
#
# Status value mapping (yaml ↔ story file Status field):
#   backlog        ↔  Not Started
#   ready-for-dev  ↔  Ready
#   in-progress    ↔  In Progress
#   review         ↔  In Review
#   done           ↔  Complete
#   blocked        ↔  Blocked

sprint: [N]
goal: "[sprint goal]"
start: "[YYYY-MM-DD]"
end: "[YYYY-MM-DD]"
generated: "[YYYY-MM-DD]"
updated: "[YYYY-MM-DD]"

stories:
  - id: "[epic-story, e.g. 1-1]"
    name: "[story name]"
    file: "[production/stories/path.md]"
    priority: must-have        # must-have | should-have | nice-to-have
    status: ready-for-dev      # backlog | ready-for-dev | in-progress | review | done | blocked
    owner: ""
    estimate_days: 0
    blocker: ""
    completed: ""
```

从冲刺计划的任务表初始化每个故事：
- Must Have 任务 → `priority: must-have`、`status: ready-for-dev`
- Should Have 任务 → `priority: should-have`、`status: backlog`
- Nice to Have 任务 → `priority: nice-to-have`、`status: backlog`

对于 `update`：读取现有 `sprint-status.yaml`，为
未变更的故事保留状态，添加新故事，移除已删除的故事。

---

## Phase 4: Producer Feasibility Gate

**Review mode check** — apply before spawning PR-SPRINT:
- `solo` → skip. Note: "PR-SPRINT skipped — Solo mode." Proceed to Phase 5 (QA plan gate).
- `lean` → skip (not a PHASE-GATE). Note: "PR-SPRINT skipped — Lean mode." Proceed to Phase 5 (QA plan gate).
- `full` → spawn as normal.

Before finalising the sprint plan, spawn `producer` via Task using gate **PR-SPRINT** (`.claude/docs/director-gates.md`).

Pass: proposed story list (titles, estimates, dependencies), total team capacity in hours/days, any carryover from the previous sprint, milestone constraints and deadline.

Present the producer's assessment.

If UNREALISTIC: revise the story selection (defer stories to Should Have or Nice to Have) and re-present the updated plan before asking for write approval.

If CONCERNS, use `AskUserQuestion`:
- Prompt: "Producer flagged concerns with this sprint plan. How do you want to proceed?"
- Options:
  - `[A] Proceed as planned — I accept the risk`
  - `[B] Adjust scope — defer some Should Have stories`
  - `[C] Extend the sprint timeline`

If [A]: proceed to write approval.
If [B]: revise the story list, re-present the updated plan, then proceed to write approval.
If [C]: adjust sprint dates and capacity, re-present the updated plan, then proceed to write approval.

After handling the producer's verdict, ask: "May I write the sprint plan to `production/sprints/sprint-[N].md` and `production/sprint-status.yaml`?" If yes, write both files (creating directories as needed). Verdict: **COMPLETE** — sprint plan and status file created. If no: Verdict: **BLOCKED** — user declined write.

After writing, add:

> **Scope check:** If this sprint includes stories added beyond the original epic scope, run `/scope-check [epic]` to detect scope creep before implementation begins.

## 第 4 阶段：制作人可行性门禁

**评审模式检查** —— 在派生 PR-SPRINT 前应用：
- `solo` → 跳过。注："PR-SPRINT skipped — Solo mode." 进入第 5 阶段（QA 计划门禁）。
- `lean` → 跳过（非 PHASE-GATE）。注："PR-SPRINT skipped — Lean mode." 进入第 5 阶段（QA 计划门禁）。
- `full` → 正常派生。

最终确定冲刺计划前，通过 Task 使用门禁 **PR-SPRINT**（`.claude/docs/director-gates.md`）派生 `producer`。

传递：拟议故事列表（标题、估算、依赖）、团队总容量（小时/天）、上冲刺的任何结转、里程碑约束和截止日期。

呈现制作人评估。

如 UNREALISTIC：修订故事选择（将故事推迟到 Should Have 或 Nice to Have）并在请求写入批准前重新呈现更新后的计划。

如 CONCERNS，使用 `AskUserQuestion`：
- 提示："制作人为此冲刺计划标记了顾虑。你想如何处理？"
- 选项：
  - `[A] 按计划进行 —— 我接受风险`
  - `[B] 调整范围 —— 推迟一些 Should Have 故事`
  - `[C] 延长冲刺时间线`

如 [A]：进入写入批准。
如 [B]：修订故事列表，重新呈现更新后的计划，然后进入写入批准。
如 [C]：调整冲刺日期和容量，重新呈现更新后的计划，然后进入写入批准。

处理制作人判定后，询问："可以将冲刺计划写入 `production/sprints/sprint-[N].md` 和 `production/sprint-status.yaml` 吗？" 如同意，写入两个文件（如需要则创建目录）。判定：**COMPLETE** —— 冲刺计划和状态文件已创建。如不同意：判定：**BLOCKED** —— 用户拒绝写入。

写入后，添加：

> **范围检查：** 如此冲刺包含超出原始史诗范围的故事，运行 `/scope-check [epic]` 以在实现开始前检测范围蔓延。

---

## Phase 5: QA Plan Gate

Before closing the sprint plan, check whether a QA plan exists for this sprint.

Use `Glob` to look for `production/qa/qa-plan-sprint-[N].md` or any file in `production/qa/` referencing this sprint number.

**If a QA plan is found**: note it in the sprint plan output — "QA Plan: `[path]`" — and proceed.

**If no QA plan exists**: do not silently proceed. Surface this explicitly:

> "This sprint has no QA plan. A sprint plan without a QA plan means test requirements are undefined — developers won't know what 'done' looks like from a QA perspective, and the sprint cannot pass the Production → Polish gate without one.
>
> Run `/qa-plan sprint` now, before starting any implementation. It takes one session and produces the test case requirements each story needs."

Use `AskUserQuestion`:
- Prompt: "No QA plan found for this sprint. How do you want to proceed?"
- Options:
  - `[A] Run /qa-plan sprint now — I'll do that before starting implementation (Recommended)`
  - `[B] Skip for now — I understand QA sign-off will be blocked at the Production → Polish gate`

If [A]: close with "Sprint plan written. Run `/qa-plan sprint` next — then begin implementation."
If [B]: add a warning block to the sprint plan document:

```markdown
> ⚠️ **No QA Plan**: This sprint was started without a QA plan. Run `/qa-plan sprint`
> before the last story is implemented. The Production → Polish gate requires a QA
> sign-off report, which requires a QA plan.
```

## 第 5 阶段：QA 计划门禁

关闭冲刺计划前，检查此冲刺是否存在 QA 计划。

使用 `Glob` 查找 `production/qa/qa-plan-sprint-[N].md` 或 `production/qa/` 中引用此冲刺编号的任何文件。

**如找到 QA 计划**：在冲刺计划输出中注明 —— "QA Plan: `[path]`" —— 然后继续。

**如无 QA 计划**：不要静默继续。显式提示此问题：

> "此冲刺没有 QA 计划。没有 QA 计划的冲刺计划意味着测试需求未定义 —— 开发者将不知道从 QA 角度"完成"是什么样，且冲刺无法在没有它的情况下通过生产 → 打磨门禁。
>
> 在开始任何实现前立即运行 `/qa-plan sprint`。它需要一次会话并生成每个故事需要的测试用例需求。"

使用 `AskUserQuestion`：
- 提示："此冲刺未找到 QA 计划。你想如何处理？"
- 选项：
  - `[A] 现在运行 /qa-plan sprint —— 在开始实现前做（推荐）`
  - `[B] 暂时跳过 —— 我理解 QA 签署将在生产 → 打磨门禁处被阻塞`

如 [A]：以"Sprint plan written. Run `/qa-plan sprint` next — then begin implementation."（冲刺计划已写入。下一步运行 `/qa-plan sprint` —— 然后开始实现。）收尾。
如 [B]：向冲刺计划文档添加警告块：

```markdown
> ⚠️ **No QA Plan**: This sprint was started without a QA plan. Run `/qa-plan sprint`
> before the last story is implemented. The Production → Polish gate requires a QA
> sign-off report, which requires a QA plan.
```

---

## Phase 6: Next Steps

After the sprint plan is written and QA plan status is resolved:

- `/qa-plan sprint` — **required before implementation begins** — defines test cases per story so developers implement against QA specs, not a blank slate
- `/story-readiness [story-file]` — validate a story is ready before starting it
- `/dev-story [story-file]` — begin implementing the first story
- `/sprint-status` — check progress mid-sprint
- `/scope-check [epic]` — verify no scope creep before implementation begins

**Review mode configuration:** All director gates (producer feasibility, QA review, code review) respect the project review mode. The review mode is set in Phase 0 when the file does not exist (for `new` sprints), or can be overridden per-run with `--review full|lean|solo` as an argument. The file `production/review-mode.txt` contains one of:
- `lean` — skip automated director gates (default if file is absent — fastest for solo dev)
- `full` — run all director gates as spawned sub-agents
- `solo` — skip all gates unconditionally (single-developer, no review)

This file is read by `/sprint-plan`, `/story-readiness`, `/story-done`, and other skills at startup.

## 第 6 阶段：下一步

冲刺计划写入且 QA 计划状态解决后：

- `/qa-plan sprint` —— **实现开始前必需** —— 为每个故事定义测试用例，让开发者对照 QA 规格而非白板实现
- `/story-readiness [story-file]` —— 开始前验证故事是否就绪
- `/dev-story [story-file]` —— 开始实现第一个故事
- `/sprint-status` —— 冲刺中期检查进度
- `/scope-check [epic]` —— 实现开始前验证无范围蔓延

**评审模式配置：** 所有总监门禁（制作人可行性、QA 评审、代码评审）遵守项目评审模式。评审模式在第 0 阶段文件不存在时（对 `new` 冲刺）设置，或可通过 `--review full|lean|solo` 参数按运行覆盖。文件 `production/review-mode.txt` 包含以下之一：
- `lean` —— 跳过自动总监门禁（如文件缺失则默认 —— 对独立开发者最快）
- `full` —— 运行所有总监门禁作为派生子智能体
- `solo` —— 无条件跳过所有门禁（单开发者、无评审）

此文件在启动时由 `/sprint-plan`、`/story-readiness`、`/story-done` 和其他技能读取。
