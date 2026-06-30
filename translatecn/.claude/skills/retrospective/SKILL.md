---
name: retrospective
description: "Generates a sprint or milestone retrospective by analyzing completed work, velocity, blockers, and patterns. Produces actionable insights for the next iteration."
argument-hint: "[sprint-N|milestone-name]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, AskUserQuestion
model: sonnet
---
---
名称：retrospective
描述："通过分析已完成的工作、速度、阻塞问题和模式，生成冲刺或里程碑回顾。为下一次迭代产生可操作的洞察。"
argument-hint："[sprint-N|milestone-name]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Bash, AskUserQuestion
model：sonnet
---

## Phase 1: Parse Arguments
## 阶段 1：解析参数

Determine whether this is a sprint retrospective (`sprint-N`) or a milestone retrospective (`milestone-name`).
确定这是冲刺回顾（`sprint-N`）还是里程碑回顾（`milestone-name`）。

---

## Phase 1b: Check for Existing Retrospective
## 阶段 1b：检查已有回顾

Before loading any data, glob for an existing retrospective file:
在加载任何数据之前，glob 查找已有的回顾文件：

- For sprint retrospectives: `production/retrospectives/retro-[sprint-slug]-*.md`
  (also check `production/sprints/sprint-[N]-retrospective.md` as an alternate location)
- For milestone retrospectives: `production/retrospectives/retro-[milestone-name]-*.md`
- 对于冲刺回顾：`production/retrospectives/retro-[sprint-slug]-*.md`
  （也检查 `production/sprints/sprint-[N]-retrospective.md` 作为备选位置）
- 对于里程碑回顾：`production/retrospectives/retro-[milestone-name]-*.md`

If a matching file is found, use `AskUserQuestion`:
如果找到匹配文件，使用 `AskUserQuestion`：

- Prompt: "An existing retrospective was found: [filename]. How do you want to proceed?"
- Options:
  - `[A] Update existing — load it and add/revise sections with new data`
  - `[B] Start fresh — generate a new retrospective (archive the old one)`
- 提示："已找到已有回顾：[文件名]。你想如何处理？"
- 选项：
  - `[A] 更新已有 — 加载并添加/修订部分以纳入新数据`
  - `[B] 重新开始 — 生成新回顾（归档旧的）`

If [A]: read the existing file and carry its content forward, revising sections with new data.
If [B]: continue to Phase 2 with a blank slate. Before writing the new file, rename the existing one with a `-archived-[date]` suffix.
如果 [A]：读取已有文件并延续其内容，用新数据修订部分。
如果 [B]：以空白状态继续阶段 2。在写入新文件之前，将已有文件重命名并添加 `-archived-[date]` 后缀。

---

## Phase 2: Load Sprint or Milestone Data
## 阶段 2：加载冲刺或里程碑数据

Read the sprint or milestone plan from the appropriate location:
从适当位置读取冲刺或里程碑计划：

- Sprint plans: `production/sprints/`
- Milestone definitions: `production/milestones/`
- 冲刺计划：`production/sprints/`
- 里程碑定义：`production/milestones/`

**Also check for `production/sprint-status.yaml`**: if it exists, read it alongside the sprint plan. It is the authoritative source for actual story completion status (status: done, completed dates, blockers). Use it as the primary source for completion metrics in Phase 3. Fall back to markdown scanning only if the yaml does not exist. Note discrepancies between the yaml and the sprint plan (e.g., stories in yaml not in plan, or vice versa).
**同时检查 `production/sprint-status.yaml`**：如果存在，与冲刺计划一起读取。它是实际故事完成状态的权威来源（status: done、完成日期、阻塞问题）。在阶段 3 中将其作为完成度指标的主要来源。仅当 yaml 不存在时回退到 markdown 扫描。注意 yaml 与冲刺计划之间的差异（例如，yaml 中的故事不在计划中，或反之）。

**If the file does not exist or is empty**, output:
**如果文件不存在或为空**，输出：

> "No sprint data found for [sprint/milestone]. Run `/sprint-status` to generate
> sprint data first, or provide the sprint details manually."
> "未找到 [sprint/milestone] 的冲刺数据。先运行 `/sprint-status` 生成
> 冲刺数据，或手动提供冲刺详情。"

Then use `AskUserQuestion` to present two options:
然后使用 `AskUserQuestion` 呈现两个选项：

- **[A] Provide data manually** — ask the user to paste or describe the sprint
  tasks, dates, and outcomes; use that as the source of truth for the retrospective.
- **[B] Stop** — abort the skill. Verdict: **BLOCKED** — no sprint data available.
- **[A] 手动提供数据** — 要求用户粘贴或描述冲刺
  任务、日期和结果；将其用作回顾的真实来源。
- **[B] 停止** — 中止技能。裁决：**BLOCKED** — 无可用冲刺数据。

If the user chooses [A], collect the data and continue to Phase 3 using what they provide.
If the user chooses [B], stop here.
如果用户选择 [A]，收集数据并使用其提供的内容继续阶段 3。
如果用户选择 [B]，在此停止。

Extract: planned tasks, estimated effort, owners, and goals.
提取：计划任务、预估工作量、负责人和目标。

Run git log for the sprint period to understand what was actually committed and when. Use the Bash tool (which uses Git Bash on Windows — the `2>/dev/null` is bash syntax, not PowerShell):
运行该冲刺期间的 git 日志以了解实际提交了什么以及何时提交。使用 Bash 工具（在 Windows 上使用 Git Bash — `2>/dev/null` 是 bash 语法，不是 PowerShell）：

```
Bash: git log --oneline --since="4 weeks ago" 2>/dev/null || git log --oneline -20
```

Adjust the `--since` date to match the sprint duration if known from the sprint plan.
如果从冲刺计划中得知冲刺时长，调整 `--since` 日期以匹配。

---

## Phase 3: Analyze Completion and Trends
## 阶段 3：分析完成度和趋势

Scan for completed and incomplete tasks by comparing the plan against actual deliverables. Check for:
通过将计划与实际交付物对比，扫描已完成和未完成的任务。检查：

- Tasks completed as planned
- Tasks completed but modified from the plan
- Tasks carried over (not completed)
- Tasks added mid-sprint (unplanned work)
- Tasks removed or descoped
- 按计划完成的任务
- 已完成但与计划不同的任务
- 延期的任务（未完成）
- 冲刺中途新增的任务（计划外工作）
- 被移除或取消范围的任务

Scan the codebase for TODO/FIXME trends:
扫描代码库以获取 TODO/FIXME 趋势：

- Count current TODO/FIXME/HACK comments
- Compare to previous sprint counts if available (check previous retrospectives)
- Note whether technical debt is growing or shrinking
- 统计当前 TODO/FIXME/HACK 注释
- 如果可用，与之前冲刺计数对比（检查之前的回顾）
- 注意技术债务是在增长还是缩减

Read previous retrospectives (if any) from `production/retrospectives/` to check:
从 `production/retrospectives/` 读取之前的回顾（如果有）以检查：

- Were previous action items addressed?
- Are the same problems recurring?
- How has velocity trended?
- 之前的行动项是否已解决？
- 相同的问题是否在重复出现？
- 速度趋势如何？

---

## Phase 4: Generate the Retrospective
## 阶段 4：生成回顾

```markdown
## Retrospective: [Sprint N / Milestone Name]
Period: [Start Date] -- [End Date]
Generated: [Date]

### Metrics

| Metric | Planned | Actual | Delta |
|--------|---------|--------|-------|
| Tasks | [X] | [Y] | [+/- Z] |
| Completion Rate | -- | [Z%] | -- |
| Story Points / Effort Days | [X] | [Y] | [+/- Z] |
| Bugs Found | -- | [N] | -- |
| Bugs Fixed | -- | [N] | -- |
| Unplanned Tasks Added | -- | [N] | -- |
| Commits | -- | [N] | -- |

### Velocity Trend

| Sprint | Planned | Completed | Rate |
|--------|---------|-----------|------|
| [N-2] | [X] | [Y] | [Z%] |
| [N-1] | [X] | [Y] | [Z%] |
| [N] (current) | [X] | [Y] | [Z%] |

**Trend**: [Increasing / Stable / Decreasing]
[One sentence explaining the trend]

### What Went Well
- [Observation backed by specific data or examples]
- [Another positive observation]
- [Recognize specific contributions or decisions that paid off]

### What Went Poorly
- [Specific issue with measurable impact -- e.g., "Feature X took 5 days
  instead of estimated 2, blocking tasks Y and Z"]
- [Another issue with impact]
- [Do not assign blame -- focus on systemic causes]

### Blockers Encountered

| Blocker | Duration | Resolution | Prevention |
|---------|----------|------------|------------|
| [What blocked progress] | [How long] | [How it was resolved] | [How to prevent recurrence] |

### Estimation Accuracy

| Task | Estimated | Actual | Variance | Likely Cause |
|------|-----------|--------|----------|--------------|
| [Most overestimated task] | [X] | [Y] | [+Z] | [Why] |
| [Most underestimated task] | [X] | [Y] | [-Z] | [Why] |

**Overall estimation accuracy**: [X%] of tasks within +/- 20% of estimate

[Analysis: Are we consistently over- or under-estimating? For which types of
tasks? What adjustment should we apply?]

### Carryover Analysis

| Task | Original Sprint | Times Carried | Reason | Action |
|------|----------------|---------------|--------|--------|
| [Task that was not completed] | [Sprint N-X] | [N] | [Why] | [Complete / Descope / Redesign] |

### Technical Debt Status
- Current TODO count: [N] (previous: [N])
- Current FIXME count: [N] (previous: [N])
- Current HACK count: [N] (previous: [N])
- Trend: [Growing / Stable / Shrinking]
- [Note any areas of concern]

### Previous Action Items Follow-Up

| Action Item (from Sprint N-1) | Status | Notes |
|-------------------------------|--------|-------|
| [Previous action] | [Done / In Progress / Not Started] | [Context] |

### Action Items for Next Iteration

| # | Action | Owner | Priority | Deadline |
|---|--------|-------|----------|----------|
| 1 | [Specific, measurable action] | [Who] | [High/Med/Low] | [When] |
| 2 | [Another action] | [Who] | [Priority] | [When] |

### Process Improvements
- [Specific change to how we work, with expected benefit]
- [Another improvement -- keep it to 2-3 actionable items, not a wish list]

### Summary
[2-3 sentence overall assessment: Was this a good sprint/milestone? What is
the single most important thing to change going forward?]
```

---

## Phase 5: Save Retrospective
## 阶段 5：保存回顾

Present the retrospective and top findings to the user (completion rate, velocity trend, top blocker, most important action item).
向用户呈现回顾和主要发现（完成率、速度趋势、首要阻塞问题、最重要的行动项）。

Ask: "May I write this to `production/retrospectives/retro-sprint-[N]-[date].md`?" (or `production/retrospectives/retro-[milestone-name]-[date].md` for milestone retrospectives)
询问："我可以将其写入 `production/retrospectives/retro-sprint-[N]-[date].md` 吗？"（或里程碑回顾使用 `production/retrospectives/retro-[milestone-name]-[date].md`）

If yes, write the file, creating the `production/retrospectives/` directory if needed. Verdict: **COMPLETE** — retrospective saved.
如果是，写入文件，按需创建 `production/retrospectives/` 目录。裁决：**COMPLETE** — 回顾已保存。

If no, stop here. Verdict: **BLOCKED** — user declined write.
如果否，在此停止。裁决：**BLOCKED** — 用户拒绝写入。

---

## Phase 6: Next Steps
## 阶段 6：后续步骤

Use `AskUserQuestion`:
使用 `AskUserQuestion`：

- Prompt: "Retrospective complete. The action items and velocity data are ready. Would you like to start sprint planning now with this data pre-loaded?"
- Options:
  - `[A] Yes — open sprint planning with retro action items and velocity delta pre-populated`
  - `[B] No — I'll reference the retrospective file manually when I'm ready`
- 提示："回顾完成。行动项和速度数据已就绪。你想现在带着预加载的这些数据开始冲刺规划吗？"
- 选项：
  - `[A] 是 — 打开冲刺规划，预填回顾行动项和速度差值`
  - `[B] 否 — 我准备好时会手动参考回顾文件`

If the user selects [A]: Proceed to invoke `/sprint-plan new`, passing the retrospective file path and a summary of the action items and velocity change so the sprint planner can reference them.
如果用户选择 [A]：继续调用 `/sprint-plan new`，传递回顾文件路径以及行动项和速度变化的摘要，以便冲刺规划器引用。

- If this was a milestone retrospective, run `/gate-check` to formally assess readiness for the next phase.
- 如果这是里程碑回顾，运行 `/gate-check` 正式评估进入下一阶段的就绪度。

### Guidelines
### 指导原则

- Be honest and specific. Vague retrospectives ("communication could be better") produce vague improvements. Use data and examples.
- Focus on systemic issues, not individual blame.
- Limit action items to 3-5. More than that dilutes focus.
- Every action item must have an owner and a deadline.
- Check whether previous action items were completed. Recurring unaddressed items are a process smell.
- If this is a milestone retrospective, also evaluate whether the milestone goals were achieved and what that means for the overall project timeline.
- 诚实且具体。模糊的回顾（"沟通可以更好"）产生模糊的改进。使用数据和示例。
- 关注系统性问题，而非个人指责。
- 将行动项限制在 3-5 个。更多会分散注意力。
- 每个行动项必须有负责人和截止日期。
- 检查之前的行动项是否已完成。反复出现的未处理项是流程问题的征兆。
- 如果这是里程碑回顾，还应评估里程碑目标是否达成，以及这对整体项目时间线意味着什么。
