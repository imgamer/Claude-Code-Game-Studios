---
name: bug-triage
description: "Read all open bugs in production/qa/bugs/, re-evaluate priority vs. severity, assign to sprints, surface systemic trends, and produce a triage report. Run at sprint start or when the bug count grows enough to need re-prioritization."
argument-hint: "[sprint | full | trend]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit
model: sonnet
---
---
name: bug-triage
description: "读取 production/qa/bugs/ 中的所有开放缺陷，重新评估优先级 vs. 严重度，分配到冲刺，浮现系统性趋势，并生成分诊报告。在冲刺开始时或当缺陷数量增长到需要重新确定优先级时运行。"
argument-hint: "[sprint | full | trend]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit
model: sonnet
---

# Bug Triage
# 缺陷分诊

This skill processes the open bug backlog into a prioritised, sprint-assigned
action list. It distinguishes between **severity** (how bad is the impact?) and
**priority** (how urgently must we fix it?), detects systemic trends, and
ensures no critical bug is lost between sprints.
此技能将开放的缺陷积压处理为已确定优先级、已分配冲刺的
行动列表。它区分 **severity**（影响有多坏？）和
**priority**（我们必须多紧急地修复它？），检测系统性趋势，并
确保没有关键缺陷在冲刺之间丢失。

**Output:** `production/qa/bug-triage-[date].md`
**输出：** `production/qa/bug-triage-[date].md`

**When to run:**
- Sprint start — assign open bugs to the new sprint or backlog
- After `/team-qa` completes and new bugs have been filed
- When the bug count crosses 10+ open items
**何时运行：**
- 冲刺开始 — 将开放缺陷分配到新冲刺或积压
- 在 `/team-qa` 完成并提交新缺陷之后
- 当缺陷数量超过 10 个以上开放项时

---

## 1. Parse Arguments
## 1. 解析参数

**Modes:**
- `/bug-triage sprint` — triage against the current sprint; assign fixable bugs
  to the sprint backlog; defer the rest
- `/bug-triage full` — full triage of all bugs regardless of sprint scope
- `/bug-triage trend` — trend analysis only (no assignment); read-only report
- No argument — run sprint mode if a current sprint exists, else full mode
**模式：**
- `/bug-triage sprint` — 针对当前冲刺分诊；将可修复的缺陷
  分配到冲刺积压；推迟其余的
- `/bug-triage full` — 对所有缺陷进行完整分诊，无论冲刺范围
- `/bug-triage trend` — 仅趋势分析（无分配）；只读报告
- 无参数 — 如果当前冲刺存在则运行冲刺模式，否则运行完整模式

---

## 2. Load Bug Backlog
## 2. 加载缺陷积压

### Step 2a — Discover bug files
### 步骤 2a — 发现缺陷文件

Glob for bug reports in priority order:
1. `production/qa/bugs/*.md` — individual bug report files (preferred format)
2. `production/qa/bugs.md` — single consolidated bug log (fallback)
3. Any `production/qa/qa-plan-*.md` "Bugs Found" table (last resort)
按优先顺序 glob 缺陷报告：
1. `production/qa/bugs/*.md` — 单个缺陷报告文件（首选格式）
2. `production/qa/bugs.md` — 单个合并的缺陷日志（后备）
3. 任何 `production/qa/qa-plan-*.md` "Bugs Found" 表（最后手段）

If no bug files found:
> "No bug files found in `production/qa/bugs/`. If bugs are tracked in a
> different location, adjust the glob pattern. If no bugs exist yet, there is
> nothing to triage."
如果未找到缺陷文件：
> "No bug files found in `production/qa/bugs/`. If bugs are tracked in a
> different location, adjust the glob pattern. If no bugs exist yet, there is
> nothing to triage."

Stop and report. Do not proceed if no bugs exist.
停止并报告。如果不存在缺陷则不要继续。

### Step 2b — Load sprint context
### 步骤 2b — 加载冲刺上下文

Read the most recently modified file in `production/sprints/` to understand:
- Current sprint number / name
- Stories in scope (for assignment target)
- Sprint capacity constraints (if noted)
读取 `production/sprints/` 中最近修改的文件以了解：
- 当前冲刺编号 / 名称
- 范围内的故事（用于分配目标）
- 冲刺容量约束（如果有记录）

If no sprint file exists: note "No sprint plan found — assigning to backlog only."
如果不存在冲刺文件：注明 "No sprint plan found — assigning to backlog only."

### Step 2c — Load severity reference
### 步骤 2c — 加载严重度参考

Read `.claude/docs/coding-standards.md` for severity/priority definitions if they
exist. If they do not exist, use the standard definitions in Step 3.
读取 `.claude/docs/coding-standards.md` 获取严重度/优先级定义（如果
存在）。如果不存在，使用步骤 3 中的标准定义。

---

## 3. Classify Each Bug
## 3. 分类每个缺陷

For each bug, extract or infer:
对每个缺陷，提取或推断：

### Severity (impact of the bug)
### Severity（缺陷的影响）

| Severity | Definition |
|----------|-----------|
| **S1 — Critical** | Game crashes, data loss, or complete feature failure. Cannot proceed past this point. |
| **S2 — High** | Major feature broken but game is still playable. Significant wrong behaviour. |
| **S3 — Medium** | Feature degraded but a workaround exists. Minor wrong behaviour. |
| **S4 — Low** | Visual glitch, cosmetic issue, typo. No gameplay impact. |
| Severity | 定义 |
|----------|-----------|
| **S1 — Critical** | 游戏崩溃、数据丢失或完全的功能失败。无法越过此点继续。 |
| **S2 — High** | 主要功能损坏但游戏仍可玩。显著的错误行为。 |
| **S3 — Medium** | 功能退化但存在变通方法。轻微的错误行为。 |
| **S4 — Low** | 视觉故障、外观问题、错别字。无游戏玩法影响。 |

### Priority (urgency of the fix)
### Priority（修复的紧迫性）

| Priority | Definition |
|----------|-----------|
| **P1 — Fix this sprint** | Blocks QA, blocks release, or is regression from last sprint |
| **P2 — Fix soon** | Should be resolved before the next major milestone |
| **P3 — Backlog** | Would be good to fix, but no active blocking impact |
| **P4 — Won't fix / Deferred** | Accepted risk or out of scope for current product scope |
| Priority | 定义 |
|----------|-----------|
| **P1 — Fix this sprint** | 阻塞 QA、阻塞发布，或是上一个冲刺的回归 |
| **P2 — Fix soon** | 应该在下一个主要里程碑之前解决 |
| **P3 — Backlog** | 修复会很好，但没有活跃的阻塞影响 |
| **P4 — Won't fix / Deferred** | 接受的风险或超出当前产品范围 |

### Assignment
### 分配

For each P1/P2 bug in `sprint` mode:
- Identify which story or epic the fix belongs to
- Check whether the current sprint has remaining capacity
- If capacity exists: assign to sprint (`Sprint: [current]`)
- If capacity is full: flag as `Priority overflow — consider pulling from sprint`
对于 `sprint` 模式中的每个 P1/P2 缺陷：
- 识别修复属于哪个故事或史诗
- 检查当前冲刺是否有剩余容量
- 如果容量存在：分配到冲刺（`Sprint: [current]`）
- 如果容量已满：标记为 `Priority overflow — consider pulling from sprint`

For `full` mode: assign all P1 to current sprint, P2 to next sprint estimate,
P3+ to backlog.
对于 `full` 模式：将所有 P1 分配到当前冲刺，P2 分配到下一个冲刺估算，
P3+ 分配到积压。

### Deviation check
### 偏差检查

Flag bugs that suggest **systematic problems**:
- 3+ bugs from the same system in the same sprint → "Potential design or
  implementation quality issue in [system]"
- 2+ S1/S2 bugs in the same story → "Story may need to be reopened and
  re-reviewed before shipping"
- Bug filed against a story marked Complete → "Regression in completed story —
  story should be re-opened in sprint tracking"
标记暗示 **系统性问题** 的缺陷：
- 同一冲刺中同一系统的 3 个以上缺陷 → "[system] 中潜在的设计或
  实现质量问题"
- 同一故事中的 2 个以上 S1/S2 缺陷 → "故事可能需要在交付之前重新开放并
  重新评审"
- 针对标记为 Complete 的故事提交的缺陷 → "已完成故事中的回归 —
  故事应该在冲刺跟踪中重新开放"

---

## 4. Trend Analysis
## 4. 趋势分析

After classifying all bugs, generate trend metrics:
分类所有缺陷后，生成趋势指标：

### Volume trends
- Total open bugs: [N]
- Opened this sprint: [N]
- Closed this sprint: [N]
- Net change: [+N / -N]
### 数量趋势
- 总开放缺陷：[N]
- 本冲刺开放的：[N]
- 本冲刺关闭的：[N]
- 净变化：[+N / -N]

### System hot spots
- Which system has the most open bugs?
- Which system has the highest S1/S2 ratio?
### 系统热点
- 哪个系统的开放缺陷最多？
- 哪个系统的 S1/S2 比率最高？

### Age analysis
- How many bugs are older than 2 sprints?
- Are any S1/S2 bugs un-assigned (sprint = none)?
### 年龄分析
- 有多少缺陷超过 2 个冲刺？
- 是否有任何 S1/S2 缺陷未分配（sprint = none）？

### Regression indicator
- Any bugs filed against previously-completed stories?
- Count: [N] regression bugs (story reopened implied)
### 回归指标
- 是否有针对之前完成的故事提交的缺陷？
- 计数：[N] 个回归缺陷（暗示故事重新开放）

---

## 5. Generate Triage Report
## 5. 生成分诊报告

```markdown
# Bug Triage Report

> **Date**: [date]
> **Mode**: [sprint | full | trend]
> **Generated by**: /bug-triage
> **Open bugs processed**: [N]
> **Sprint in scope**: [sprint name, or "N/A"]

---

## Triage Summary

| Priority | Count | Notes |
|----------|-------|-------|
| P1 — Fix this sprint | [N] | [N] assigned to sprint, [N] overflow |
| P2 — Fix soon | [N] | Scheduled for next sprint |
| P3 — Backlog | [N] | Deferred |
| P4 — Won't fix | [N] | Accepted risk |

**Critical (S1/S2) unfixed count**: [N]

---

## P1 Bugs — Fix This Sprint

| ID | System | Severity | Summary | Assigned to | Story |
|----|--------|----------|---------|-------------|-------|
| BUG-NNN | [system] | S[1-4] | [one-line description] | [sprint] | [story path] |

---

## P2 Bugs — Fix Soon

| ID | System | Severity | Summary | Target Sprint |
|----|--------|----------|---------|---------------|
| BUG-NNN | [system] | S[1-4] | [one-line description] | Sprint [N+1] |

---

## P3/P4 Bugs — Backlog / Won't Fix

| ID | System | Severity | Summary | Disposition |
|----|--------|----------|---------|-------------|
| BUG-NNN | [system] | S4 | [one-line description] | Backlog |

---

## Systemic Issues Flagged

[List any patterns from Step 3 deviation check, or "None identified."]

---

## Trend Analysis

**Volume**: [N] open / [+N] net change this sprint
**Hot spot**: [system with most bugs]
**Regressions**: [N] bugs against completed stories
**Aged bugs (>2 sprints old)**: [N]

[If N aged S1/S2 bugs > 0:]
> ⚠️ [N] high-severity bugs have been open for more than 2 sprints without
> assignment. These represent accepted risk that should be explicitly reviewed.

---

## Recommended Actions

1. [Most urgent action — usually "fix P1 bugs before QA hand-off"]
2. [Second action — usually "investigate [hot spot system] quality"]
3. [Third action — optional improvement]
```
```markdown
# Bug Triage Report

> **Date**: [date]
> **Mode**: [sprint | full | trend]
> **Generated by**: /bug-triage
> **Open bugs processed**: [N]
> **Sprint in scope**: [sprint name, or "N/A"]

---

## Triage Summary

| Priority | Count | Notes |
|----------|-------|-------|
| P1 — Fix this sprint | [N] | [N] assigned to sprint, [N] overflow |
| P2 — Fix soon | [N] | Scheduled for next sprint |
| P3 — Backlog | [N] | Deferred |
| P4 — Won't fix | [N] | Accepted risk |

**Critical (S1/S2) unfixed count**: [N]

---

## P1 Bugs — Fix This Sprint

| ID | System | Severity | Summary | Assigned to | Story |
|----|--------|----------|---------|-------------|-------|
| BUG-NNN | [system] | S[1-4] | [one-line description] | [sprint] | [story path] |

---

## P2 Bugs — Fix Soon

| ID | System | Severity | Summary | Target Sprint |
|----|--------|----------|---------|---------------|
| BUG-NNN | [system] | S[1-4] | [one-line description] | Sprint [N+1] |

---

## P3/P4 Bugs — Backlog / Won't Fix

| ID | System | Severity | Summary | Disposition |
|----|--------|----------|---------|-------------|
| BUG-NNN | [system] | S4 | [one-line description] | Backlog |

---

## Systemic Issues Flagged

[List any patterns from Step 3 deviation check, or "None identified."]

---

## Trend Analysis

**Volume**: [N] open / [+N] net change this sprint
**Hot spot**: [system with most bugs]
**Regressions**: [N] bugs against completed stories
**Aged bugs (>2 sprints old)**: [N]

[If N aged S1/S2 bugs > 0:]
> ⚠️ [N] high-severity bugs have been open for more than 2 sprints without
> assignment. These represent accepted risk that should be explicitly reviewed.

---

## Recommended Actions

1. [Most urgent action — usually "fix P1 bugs before QA hand-off"]
2. [Second action — usually "investigate [hot spot system] quality"]
3. [Third action — optional improvement]
```

---

## 6. Write and Gate
## 6. 写入和门控

Present the report in conversation, then ask:
在对话中呈现报告，然后询问：

"May I write this triage report to `production/qa/bug-triage-[date].md`?"
"May I write this triage report to `production/qa/bug-triage-[date].md`?"

Write only after approval.
仅在批准后写入。

After writing:
- If any S1 bugs are unassigned: "S1 bugs must be assigned before the sprint
  can be considered healthy. Run `/sprint-status` to see current capacity."
- If regression bugs exist: "Regressions found — consider re-opening the
  affected stories in sprint tracking and running `/smoke-check` to re-gate."
- If no P1 bugs exist: "No P1 bugs — build is in good shape for QA hand-off." Verdict: **COMPLETE** — triage report written.
写入后：
- 如果任何 S1 缺陷未分配："S1 bugs must be assigned before the sprint
  can be considered healthy. Run `/sprint-status` to see current capacity."
- 如果存在回归缺陷："Regressions found — consider re-opening the
  affected stories in sprint tracking and running `/smoke-check` to re-gate."
- 如果不存在 P1 缺陷："No P1 bugs — build is in good shape for QA hand-off." Verdict: **COMPLETE** — 分诊报告已写入。

If user declined write: Verdict: **BLOCKED** — user declined write.
如果用户拒绝写入：Verdict: **BLOCKED** — 用户拒绝写入。

---

## Collaborative Protocol
## 协作协议

- **Never close or mark bugs Won't Fix without user approval** — surface them
  as P4 candidates and ask: "Are these acceptable as Won't Fix?"
- **Never auto-assign to a sprint at capacity** — flag overflow and let the
  sprint owner decide what to pull
- **Severity is objective; priority is a team decision** — present severity
  classifications as recommendations, not mandates
- **Trend data is informational** — do not block work on trend findings alone;
  surface them as observations
- **切勿在未经用户批准的情况下关闭或标记缺陷为 Won't Fix** — 将它们
  作为 P4 候选呈现并询问："这些作为 Won't Fix 可接受吗？"
- **切勿自动分配到已满容量的冲刺** — 标记溢出并让
  冲刺负责人决定拉取什么
- **Severity 是客观的；priority 是团队决策** — 将 severity
  分类作为建议呈现，而非强制
- **趋势数据是信息性的** — 不要仅凭趋势发现阻塞工作；
  将它们作为观察呈现
