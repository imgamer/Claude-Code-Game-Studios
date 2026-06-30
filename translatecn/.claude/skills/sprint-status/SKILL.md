---
name: sprint-status
description: "Fast sprint status check. Reads the current sprint plan, scans story files for status, and produces a concise progress snapshot with burndown assessment and emerging risks. Run at any time during a sprint for quick situational awareness. Use when user asks 'how is the sprint going', 'sprint update', 'show sprint progress'."
argument-hint: "[sprint-number or blank for current]"
user-invocable: true
allowed-tools: Read, Glob, Grep
model: haiku
---
---
name: sprint-status
description: "快速冲刺状态检查。读取当前冲刺计划，扫描故事文件状态，并生成带燃尽评估和新兴风险的简洁进度快照。可在冲刺期间任何时间运行以快速获取态势感知。当用户问'冲刺进展如何'、'冲刺更新'、'显示冲刺进度'时使用。"
argument-hint: "[sprint-number or blank for current]"
user-invocable: true
allowed-tools: Read, Glob, Grep
model: haiku
---

# Sprint Status

This is a fast situational awareness check, not a sprint review. It reads the
current sprint plan and story files, scans for status markers, and produces a
concise snapshot in under 30 lines. For detailed sprint management, use
`/sprint-plan update` or `/milestone-review`.

**This skill is read-only.** It never proposes changes, never asks to write
files, and makes at most one concrete recommendation.

# 冲刺状态

这是一次快速态势感知检查，不是冲刺评审。它读取
当前冲刺计划和故事文件，扫描状态标记，并生成
不超过 30 行的简洁快照。详细冲刺管理使用
`/sprint-plan update` 或 `/milestone-review`。

**此技能只读。** 它从不提议变更，从不请求写入
文件，最多给出一个具体建议。

---

## 1. Find the Sprint

**Argument:** `$ARGUMENTS[0]` (blank = use current sprint)

- If an argument is given (e.g., `/sprint-status 3`), search
  `production/sprints/` for a file matching `sprint-03.md`, `sprint-3.md`,
  or similar. Report which file was found.
- If no argument is given, find the most recently modified file in
  `production/sprints/` and treat it as the current sprint.
- If `production/sprints/` does not exist or is empty, report: "No sprint
  files found. Start a sprint with `/sprint-plan new`." Then stop.

Read the sprint file in full. Extract:
- Sprint number and goal
- Start date and end date
- All story or task entries with their priority (Must Have / Should Have /
  Nice to Have), owner, and estimate

## 1. 查找冲刺

**参数：** `$ARGUMENTS[0]`（为空 = 使用当前冲刺）

- 如给出参数（例如 `/sprint-status 3`），在
  `production/sprints/` 中搜索匹配 `sprint-03.md`、`sprint-3.md`
  或类似名称的文件。报告找到哪个文件。
- 如无参数，在
  `production/sprints/` 中查找最近修改的文件并将其视为当前冲刺。
- 如 `production/sprints/` 不存在或为空，报告："No sprint
  files found. Start a sprint with `/sprint-plan new`."（未找到冲刺文件。用 `/sprint-plan new` 开始冲刺。）然后停止。

完整读取冲刺文件。提取：
- 冲刺编号和目标
- 开始日期和结束日期
- 所有故事或任务条目及其优先级（Must Have / Should Have /
  Nice to Have）、负责人和估算

---

## 2. Calculate Days Remaining

Using today's date and the sprint end date from the sprint file, calculate:
- Total sprint days (end minus start)
- Days elapsed
- Days remaining
- Percentage of time consumed

If the sprint file does not include explicit dates, note "Sprint dates not
found — burndown assessment skipped."

## 2. 计算剩余天数

使用今天日期和冲刺文件中的冲刺结束日期，计算：
- 总冲刺天数（结束减开始）
- 已过天数
- 剩余天数
- 已消耗时间百分比

如冲刺文件未包含明确日期，注明 "Sprint dates not
found — burndown assessment skipped."（未找到冲刺日期 —— 跳过燃尽评估。）

---

## 3. Scan Story Status

**First: check for `production/sprint-status.yaml`.**

If it exists, read it directly — it is the authoritative source of truth.
Extract status for each story from the `status` field. No markdown scanning needed.
Use its `sprint`, `goal`, `start`, `end` fields instead of re-parsing the sprint plan.

**If `sprint-status.yaml` does not exist** (legacy sprint or first-time setup),
fall back to markdown scanning:

1. If the entry references a story file path, check if the file exists.
   Read the file and scan for status markers: DONE, COMPLETE, IN PROGRESS,
   BLOCKED, NOT STARTED (case-insensitive).
2. If the entry has no file path (inline task in the sprint plan), scan the
   sprint plan itself for status markers next to that entry.
3. If no status marker is found, classify as NOT STARTED.
4. If a file is referenced but does not exist, classify as MISSING and note it.

When using the fallback, add a note at the bottom of the output:
"⚠ No `sprint-status.yaml` found — status inferred from markdown. Run `/sprint-plan update` to generate one."

Optionally (fast check only — do not do a deep scan): grep `src/` for a
directory or file name that matches the story's system slug to check for
implementation evidence. This is a hint only, not a definitive status.

### Stale Story Detection

After collecting status for all stories, check each IN PROGRESS story for staleness:

- For each story that has a referenced file, read the file and look for a
  `Last Updated:` field in the frontmatter or header (e.g., `Last Updated: 2026-04-01`
  or `updated: 2026-04-01`). Accept any reasonable date field name: `Last Updated`,
  `Updated`, `last-updated`, `updated_at`.
- Calculate days since that date using today's date.
- If the date is more than 4 days ago, flag the story as **STALE**. (4-day threshold accounts for weekends — a story last touched on Friday won't appear stale until Wednesday.)
- If no date field is found in the story file, note "no timestamp — cannot check staleness."
- If the story has no referenced file (inline task), note "inline task — cannot check staleness."

STALE stories are included in the output table and collected into an "Attention Needed"
section (see Phase 5 output format).

**Stale story escalation**: If any IN PROGRESS story is flagged STALE (no progress in 4+ days), the burndown verdict
is upgraded to at least **At Risk** — even if the completion percentage is within the normal
On Track window. Record this escalation reason: "At Risk — [N] story(ies) with no progress in
[N] days."

## 3. 扫描故事状态

**首先：检查 `production/sprint-status.yaml`。**

如存在，直接读取它 —— 它是权威真实数据源。
从 `status` 字段提取每个故事的状态。无需 markdown 扫描。
使用其 `sprint`、`goal`、`start`、`end` 字段而非重新解析冲刺计划。

**如 `sprint-status.yaml` 不存在**（遗留冲刺或首次设置），
回退到 markdown 扫描：

1. 如条目引用故事文件路径，检查文件是否存在。
   读取文件并扫描状态标记：DONE、COMPLETE、IN PROGRESS、
   BLOCKED、NOT STARTED（不区分大小写）。
2. 如条目无文件路径（冲刺计划中的内联任务），扫描
   冲刺计划本身在该条目旁的状态标记。
3. 如未找到状态标记，分类为 NOT STARTED。
4. 如引用的文件不存在，分类为 MISSING 并注明。

使用回退时，在输出底部添加备注：
"⚠ No `sprint-status.yaml` found — status inferred from markdown. Run `/sprint-plan update` to generate one."（⚠ 未找到 `sprint-status.yaml` —— 状态从 markdown 推断。运行 `/sprint-plan update` 生成一个。）

可选地（仅快速检查 —— 不做深度扫描）：在 `src/` 中 grep
匹配故事系统 slug 的目录或文件名以检查
实现证据。这仅是提示，非确定状态。

### 陈旧故事检测

收集所有故事状态后，检查每个 IN PROGRESS 故事的陈旧性：

- 对每个有引用文件的故事，读取文件并查找
  frontmatter 或头部中的 `Last Updated:` 字段（例如 `Last Updated: 2026-04-01`
  或 `updated: 2026-04-01`）。接受任何合理的日期字段名：`Last Updated`、
  `Updated`、`last-updated`、`updated_at`。
- 使用今天日期计算距该日期的天数。
- 如日期超过 4 天前，将故事标记为 **STALE**。（4 天阈值考虑周末 —— 周五最后触及的故事直到周三才会显示陈旧。）
- 如故事文件中未找到日期字段，注明 "no timestamp — cannot check staleness."
- 如故事无引用文件（内联任务），注明 "inline task — cannot check staleness."

STALE 故事包含在输出表中并收集到"需关注"
章节（见第 5 阶段输出格式）。

**陈旧故事升级**：如任何 IN PROGRESS 故事被标记为 STALE（4+ 天无进展），燃尽判定
升级到至少 **At Risk** —— 即使完成百分比在正常
On Track 窗口内。记录此升级原因："At Risk — [N] story(ies) with no progress in
[N] days."（At Risk —— [N] 个故事 [N] 天无进展。）

---

## 4. Burndown Assessment

Calculate:
- Tasks complete (DONE or COMPLETE)
- Tasks in progress (IN PROGRESS)
- Tasks blocked (BLOCKED)
- Tasks not started (NOT STARTED or MISSING)
- Completion percentage: (complete / total) * 100

Assess burndown by comparing completion percentage to time consumed percentage:

- **On Track**: completion % is within 10 points of time consumed % or ahead
- **At Risk**: completion % is 10-25 points behind time consumed %
- **Behind**: completion % is more than 25 points behind time consumed %

If dates are unavailable, skip the burndown assessment and report "On Track /
At Risk / Behind: unknown — sprint dates not found."

## 4. 燃尽评估

计算：
- 已完成任务（DONE 或 COMPLETE）
- 进行中任务（IN PROGRESS）
- 阻塞任务（BLOCKED）
- 未开始任务（NOT STARTED 或 MISSING）
- 完成百分比：(complete / total) * 100

通过比较完成百分比与时间消耗百分比评估燃尽：

- **On Track（在轨）**：完成百分比在时间消耗百分比的 10 个点内或超前
- **At Risk（有风险）**：完成百分比落后时间消耗百分比 10-25 个点
- **Behind（落后）**：完成百分比落后时间消耗百分比超过 25 个点

如日期不可用，跳过燃尽评估并报告 "On Track /
At Risk / Behind: unknown — sprint dates not found."（On Track / At Risk / Behind：未知 —— 未找到冲刺日期。）

---

## 5. Output

Keep the output concise. The story status table is mandatory — do not truncate it. Aim for under 50 lines total; omit the Emerging Risks section if nothing notable was found. Use this format:

```markdown
## Sprint [N] Status — [Today's Date]
**Sprint Goal**: [from sprint plan]
**Days Remaining**: [N] of [total] ([% time consumed])

### Progress: [complete/total] tasks ([%])

| Story / Task         | Priority   | Status      | Owner   | Blocker        |
|----------------------|------------|-------------|---------|----------------|
| [title]              | Must Have  | DONE        | [owner] |                |
| [title]              | Must Have  | IN PROGRESS | [owner] |                |
| [title]              | Must Have  | BLOCKED     | [owner] | [brief reason] |
| [title]              | Should Have| NOT STARTED | [owner] |                |

### Attention Needed
| Story / Task         | Status      | Last Updated   | Days Stale | Note           |
|----------------------|-------------|----------------|------------|----------------|
| [title]              | IN PROGRESS | [date or N/A]  | [N days]   | [STALE / no timestamp — cannot check staleness / inline task — cannot check staleness] |

*(Omit this section entirely if no IN PROGRESS stories are stale or have timestamp concerns.)*

### Burndown: [On Track / At Risk / Behind]
[1-2 sentences. If behind: which Must Haves are at risk. If on track: confirm
and note any Should Haves the team could pull.]

### Must-Haves at Risk
[List any Must Have stories that are BLOCKED or NOT STARTED with less than
40% of sprint time remaining. If none, write "None."]

### Emerging Risks
[Any risks visible from the story scan: missing files, cascading blockers,
stories with no owner. If none, write "None identified."]

### Recommendation
[One concrete action, or "Sprint is on track — no action needed."]
```

## 5. 输出

保持输出简洁。故事状态表是强制的 —— 不要截断。总行数目标 50 行以下；如未发现显著问题则省略 Emerging Risks 章节。使用此格式：

```markdown
## Sprint [N] Status — [Today's Date]
**Sprint Goal**: [from sprint plan]
**Days Remaining**: [N] of [total] ([% time consumed])

### Progress: [complete/total] tasks ([%])

| Story / Task         | Priority   | Status      | Owner   | Blocker        |
|----------------------|------------|-------------|---------|----------------|
| [title]              | Must Have  | DONE        | [owner] |                |
| [title]              | Must Have  | IN PROGRESS | [owner] |                |
| [title]              | Must Have  | BLOCKED     | [owner] | [brief reason] |
| [title]              | Should Have| NOT STARTED | [owner] |                |

### Attention Needed
| Story / Task         | Status      | Last Updated   | Days Stale | Note           |
|----------------------|-------------|----------------|------------|----------------|
| [title]              | IN PROGRESS | [date or N/A]  | [N days]   | [STALE / no timestamp — cannot check staleness / inline task — cannot check staleness] |

*(Omit this section entirely if no IN PROGRESS stories are stale or have timestamp concerns.)*

### Burndown: [On Track / At Risk / Behind]
[1-2 sentences. If behind: which Must Haves are at risk. If on track: confirm
and note any Should Haves the team could pull.]

### Must-Haves at Risk
[List any Must Have stories that are BLOCKED or NOT STARTED with less than
40% of sprint time remaining. If none, write "None."]

### Emerging Risks
[Any risks visible from the story scan: missing files, cascading blockers,
stories with no owner. If none, write "None identified."]

### Recommendation
[One concrete action, or "Sprint is on track — no action needed."]
```

---

## 6. Fast Escalation Rules

Apply these rules before outputting, and place the flag at the TOP of the
output if triggered (above the status table):

**Critical flag** — if Must Have stories are BLOCKED or NOT STARTED and
less than 40% of the sprint time remains:

```
SPRINT AT RISK: [N] Must Have stories are not complete with [X]% of sprint
time remaining. Recommend replanning with `/sprint-plan update`.
```

**Completion flag** — if all Must Have stories are DONE:

```
All Must Haves complete. Team can pull from Should Have backlog.
```

**Missing stories flag** — if any referenced story files do not exist:

```
NOTE: [N] story files referenced in the sprint plan are missing.
Run `/story-readiness sprint` to validate story file coverage.
```

## 6. 快速升级规则

输出前应用这些规则，如触发则将标志置于
输出顶部（在状态表上方）：

**关键标志** —— 如 Must Have 故事为 BLOCKED 或 NOT STARTED 且
剩余冲刺时间少于 40%：

```
SPRINT AT RISK: [N] Must Have stories are not complete with [X]% of sprint
time remaining. Recommend replanning with `/sprint-plan update`.
```

**完成标志** —— 如所有 Must Have 故事为 DONE：

```
All Must Haves complete. Team can pull from Should Have backlog.
```

**缺失故事标志** —— 如任何引用的故事文件不存在：

```
NOTE: [N] story files referenced in the sprint plan are missing.
Run `/story-readiness sprint` to validate story file coverage.
```

---

## Collaborative Protocol

This skill is read-only. It reports observed facts from files on disk.

- It does not update the sprint plan
- It does not change story status
- It does not propose scope cuts (that is `/sprint-plan update`)
- It makes at most one recommendation per run

For more detail on a specific story, the user can read the story file directly
or run `/story-readiness [path]`.

For sprint replanning, use `/sprint-plan update`.
For end-of-sprint retrospective, use `/retrospective`.

## 协作协议

此技能只读。它报告磁盘上文件中观察到的事实。

- 它不更新冲刺计划
- 它不更改故事状态
- 它不提议范围裁剪（那是 `/sprint-plan update`）
- 它每次运行最多给出一个建议

如需某个故事的更多细节，用户可直接读取故事文件
或运行 `/story-readiness [path]`。

冲刺重新规划使用 `/sprint-plan update`。
冲刺末回顾使用 `/retrospective`。
