---
name: quick-design
description: "Lightweight design spec for small changes — tuning adjustments, minor mechanics, balance tweaks. Skips full GDD authoring when a system GDD already exists or the change is too small to warrant one. Produces a Quick Design Spec that embeds directly into story files."
argument-hint: "[brief description of the change]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion
model: sonnet
---
---
名称：quick-design
描述："用于小型变更的轻量级设计规格 —— 调参调整、次要机制、平衡微调。当系统 GDD 已存在或变更太小不值得编写时，跳过完整的 GDD 编写。生成可直接嵌入故事文件的快速设计规格。"
argument-hint："[变更的简要描述]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, AskUserQuestion
model：sonnet
---

# Quick Design
# 快速设计

This is the **lightweight design path** for changes that don't need a full GDD.
Full GDD authoring via `/design-system` is the heavyweight path. Use this skill
for work under approximately 4 hours of implementation — tuning adjustments,
minor behavioral tweaks, small additions to existing systems, or standalone
features too small to warrant a full document.
这是针对不需要完整 GDD 的变更的**轻量级设计路径**。
通过 `/design-system` 编写完整 GDD 是重量级路径。对于实现工作约 4 小时以内的任务使用此技能 —— 调参调整、次要行为微调、对现有系统的小幅添加，或太小不值得编写完整文档的独立功能。

**Output:** `design/quick-specs/[name]-[date].md`
**输出：** `design/quick-specs/[name]-[date].md`

**When to run:** Anytime a change is too small for `/design-system` but too
meaningful to implement without a written rationale.
**何时运行：** 任何时候变更对于 `/design-system` 太小，但又太有意义而无法在没有书面理由的情况下实现时。

---

## 1. Classify the Change
## 1. 对变更进行分类

First, read the argument and determine which category this change falls into:
首先，读取参数并确定此变更属于哪个类别：

- **Tuning** — changing numbers or balance values in an existing system with no
  behavioral change (most minimal path). Example: "increase jump height from 5
  to 6 units", "reduce enemy patrol speed by 10%".
- **Tuning（调参）** —— 在现有系统中更改数字或平衡值，无行为变化（最简路径）。示例："将跳跃高度从 5 单位提高到 6 单位"、"将敌人巡逻速度降低 10%"。
- **Tweak** — a small behavioral change to an existing system that introduces no
  new states, branches, or systems. Example: "make dash invincible on frame 1",
  "allow combo to cancel into roll".
- **Tweak（微调）** —— 对现有系统的小幅行为变更，不引入新状态、分支或系统。示例："使冲刺在第 1 帧无敌"、"允许连招取消为翻滚"。
- **Addition** — adding a small mechanic to an existing system that may introduce
  1-2 new states or interactions. Example: "add a parry window to the block
  mechanic", "add a charge variant to the basic attack".
- **Addition（添加）** —— 向现有系统添加小机制，可能引入 1-2 个新状态或交互。示例："向格挡机制添加招架窗口"、"向基础攻击添加蓄力变体"。
- **New Small System** — a standalone feature small enough that it has no
  existing GDD and is under approximately one week of implementation work.
  Example: "achievement popup system", "simple day/night visual cycle".
- **New Small System（新小型系统）** —— 一个独立功能，足够小以至于没有现有 GDD，实现工作约一周以内。示例："成就弹窗系统"、"简单的昼夜视觉循环"。

If the change does NOT fit these categories — it introduces a new system with
significant cross-system dependencies, requires more than one week of
implementation, or fundamentally alters an existing system's core rules — stop
and redirect to `/design-system` instead.
如果变更不属于这些类别 —— 它引入了具有显著跨系统依赖的新系统，需要超过一周的实现工作，或从根本上改变了现有系统的核心规则 —— 停止并改为重定向到 `/design-system`。

If there is no argument, ask the user to describe the change (plain text prompt), then classify it using the criteria above.
如果没有参数，请用户描述变更（纯文本提示），然后使用上述标准进行分类。

Present the inferred classification using `AskUserQuestion`:
使用 `AskUserQuestion` 呈现推断的分类：
- Prompt: "I've classified this as **[inferred type]** — [brief reason]. Is that correct?"
- 提示："我已将其分类为 **[推断类型]** —— [简要原因]。是否正确？"
- Options:
- 选项：
  - `[A] Yes — [inferred type] is correct`
  - `[A] 是 —— [推断类型] 正确`
  - `[B] Tuning — changing numbers or balance values only`
  - `[B] Tuning —— 仅更改数字或平衡值`
  - `[C] Tweak — small behavioral change to an existing system`
  - `[C] Tweak —— 对现有系统的小幅行为变更`
  - `[D] Addition — adding a small mechanic to an existing system`
  - `[D] Addition —— 向现有系统添加小机制`
  - `[E] New Small System — standalone feature, under one week of work`
  - `[E] New Small System —— 独立功能，一周以内的工作`
  - `[F] This is too large — redirect me to /design-system`
  - `[F] 这太大了 —— 将我重定向到 /design-system`

If [F]: stop. Verdict: **REDIRECTED** — use `/design-system` for this change.
Otherwise: proceed with the selected type.
如果选择 [F]：停止。结论：**REDIRECTED（已重定向）** —— 对此变更使用 `/design-system`。
否则：使用所选类型继续。

---

## 2. Context Scan
## 2. 上下文扫描

Before drafting anything, read the relevant context:
在起草任何内容之前，读取相关上下文：

- Search `design/gdd/` for the GDD most relevant to this change. Read the
  sections that this change would affect.
- 在 `design/gdd/` 中搜索与此变更最相关的 GDD。读取此变更将影响的章节。
- Check whether `design/gdd/systems-index.md` exists. If it does, read it to
  understand where this system sits in the dependency graph and what tier it
  belongs to. If it does not exist, note "No systems index found — skipping
  dependency tier check." and continue.
- 检查 `design/gdd/systems-index.md` 是否存在。如果存在，读取它以了解此系统在依赖图中的位置及其所属层级。如果不存在，注明"未找到系统索引 —— 跳过依赖层级检查。"并继续。
- Check `design/quick-specs/` for any prior quick specs that touched this
  system — avoid contradicting them.
- 检查 `design/quick-specs/` 中是否有触及此系统的先前快速规格 —— 避免与它们冲突。
- If this is a Tuning change, also check `assets/data/` for the data file that
  holds the relevant values.
- 如果这是 Tuning 变更，还要检查 `assets/data/` 中保存相关值的数据文件。

Report what was found: "Found GDD at [path]. Relevant section: [section name].
No conflicting quick specs found." (or note any conflicts found.)
报告发现的内容："在 [path] 找到 GDD。相关章节：[章节名]。未找到冲突的快速规格。"（或注明发现的任何冲突。）

---

## 3. Draft the Quick Design Spec
## 3. 起草快速设计规格

Use the appropriate spec format for the change category.
根据变更类别使用适当的规格格式。

### For Tuning changes
### 对于 Tuning 变更

Produce a single table:
生成单个表格：

```markdown
# Quick Design Spec: [Title]

**Type**: Tuning
**System**: [System name]
**GDD Reference**: `design/gdd/[filename].md` — Tuning Knobs section
**Date**: [today]

## Change

| Parameter | Old Value | New Value | Rationale |
|-----------|-----------|-----------|-----------|
| [param]   | [old]     | [new]     | [why]     |

## Tuning Knob Mapping

Maps to GDD Tuning Knob: [knob name and its documented range].
New value is [within / at the edge of / outside] the documented range.
[If outside: explain why the range should be extended.]

## Acceptance Criteria

- [ ] [Parameter] reads [new value] from `assets/data/[file]`
- [ ] Behavior difference is observable in [specific context]
- [ ] No regression in [related behavior]
```

### For Tweak and Addition changes
### 对于 Tweak 和 Addition 变更

```markdown
# Quick Design Spec: [Title]

**Type**: [Tweak / Addition]
**System**: [System name]
**GDD Reference**: `design/gdd/[filename].md`
**Date**: [today]

## Change Summary

[1-2 sentences describing what changes and why.]

## Motivation

[Why is this change needed? What player experience problem does it solve?
Reference the relevant MDA aesthetic or player feedback if applicable.]

## Design Delta

Current GDD says (quoting `design/gdd/[filename].md`, [section]):

> [exact quote of the relevant rule or description]

This spec changes that to:

[New rule or description, written with the same precision as a GDD Detailed
Rules section. A programmer should be able to implement from this text alone.]

## New Rules / Values

[Full unambiguous statement of the replacement content. If this introduces
new states, list them. If it introduces new parameters, define their ranges.]

## Affected Systems

| System | Impact | Action Required |
|--------|--------|-----------------|
| [system] | [how it is affected] | [update GDD / update data file / no action] |

## Acceptance Criteria

- [ ] [Specific, testable criterion 1]
- [ ] [Specific, testable criterion 2]
- [ ] [Specific, testable criterion 3]
- [ ] No regression: [the original behavior this must not break]

## GDD Update Required?

[Yes / No]
[If yes: which file, which section, and what the update should say.]
```

### For New Small System changes
### 对于 New Small System 变更

Use a trimmed GDD structure. Include only the sections that are directly
necessary — skip Player Fantasy, full Formulas, and Edge Cases unless the
system specifically requires them.
使用精简的 GDD 结构。仅包含直接必要的章节 —— 除非系统特别需要，否则跳过玩家幻想、完整公式和边缘情况。

```markdown
# Quick Design Spec: [Title]

**Type**: New Small System
**Scope**: [1-2 sentence description of what this system does and doesn't do]
**Date**: [today]
**Estimated Implementation**: [hours]

## Overview

[One paragraph a new team member could understand. What does this system do,
when does it activate, and what does it produce?]

## Core Rules

[Unambiguous rules for the system. Use numbered lists for sequential behavior
and bullet lists for conditions. Be precise enough that a programmer can
implement without asking questions.]

## Tuning Knobs

| Knob | Default | Range | Category | Rationale |
|------|---------|-------|----------|-----------|
| [name] | [value] | [min–max] | [feel/curve/gate] | [why this default] |

All values must live in `assets/data/[appropriate-file].json`, not hardcoded.

## Acceptance Criteria

- [ ] [Functional criterion: does the right thing]
- [ ] [Functional criterion: handles the edge case]
- [ ] [Experiential criterion: feels right — what a playtest validates]
- [ ] [Regression criterion: does not break adjacent system]

## Systems Index

This system is not currently in `design/gdd/systems-index.md`.
[If it should be added: suggest which layer and priority tier.]
[If it is too small to track: state "This system is below systems-index
tracking threshold — quick spec is sufficient."]
```

---

## 4. Approval and Filing
## 4. 批准和归档

Present the draft to the user in full. Then use `AskUserQuestion`:
向用户完整呈现草稿。然后使用 `AskUserQuestion`：
- Prompt: "Here's the Quick Design Spec draft. How do you want to proceed?"
- 提示："这是快速设计规格草稿。您希望如何进行？"
- Options:
- 选项：
  - `[A] Approve — write it as shown`
  - `[A] 批准 —— 按所示内容写入`
  - `[B] Revise — I'll describe what to change`
  - `[B] 修改 —— 我将描述要更改的内容`
  - `[C] This grew too large — redirect to /design-system instead`
  - `[C] 这变得太大了 —— 改为重定向到 /design-system`

If [B]: collect the requested changes, revise the draft, and re-present this widget.
If [C]: stop. Verdict: **REDIRECTED** — use `/design-system` for this change.
如果选择 [B]：收集请求的更改，修订草稿，并重新呈现此小组件。
如果选择 [C]：停止。结论：**REDIRECTED（已重定向）** —— 对此变更使用 `/design-system`。

If [A]: ask "May I write this Quick Design Spec to
`design/quick-specs/[kebab-case-title]-[YYYY-MM-DD].md`?"
如果选择 [A]：询问"我是否可以将此快速设计规格写入
`design/quick-specs/[kebab-case-title]-[YYYY-MM-DD].md`？"

Use today's date in the filename. The title should be a kebab-case description
of the change (e.g., `jump-height-tuning-2026-03-10`,
`parry-window-addition-2026-03-10`).
在文件名中使用今天的日期。标题应为变更的 kebab-case 描述（例如，`jump-height-tuning-2026-03-10`、
`parry-window-addition-2026-03-10`）。

If yes, create the `design/quick-specs/` directory if it does not exist, then
write the file.
如果选择是，则创建 `design/quick-specs/` 目录（如果不存在），然后写入文件。

If a GDD update is required (flagged in the spec), ask separately after
writing the quick spec:
如果需要更新 GDD（在规格中标记），在写入快速规格后单独询问：

"This spec modifies rules in [System Name]. May I update
`design/gdd/[filename].md` — specifically the [section name] section?"
"此规格修改了 [System Name] 中的规则。我是否可以更新
`design/gdd/[filename].md` —— 特别是 [章节名] 章节？"

Show the exact text that would be changed (old vs. new) before asking. Do not
make GDD edits without explicit approval.
在询问之前显示将要更改的确切文本（旧与新）。未经明确批准，不要进行 GDD 编辑。

---

## 5. Handoff
## 5. 交接

After writing the file, output:
写入文件后，输出：

```
Quick Design Spec written to: design/quick-specs/[filename].md
Type: [Tuning / Tweak / Addition / New Small System]
System: [system name]
GDD update: [Required — pending approval / Applied / Not required]

Next step: This spec is ready for `/story-readiness` validation before
implementation. Reference this spec in the story's GDD Reference field.
```

```
快速设计规格已写入：design/quick-specs/[filename].md
类型：[Tuning / Tweak / Addition / New Small System]
系统：[系统名]
GDD 更新：[需要 —— 等待批准 / 已应用 / 不需要]

下一步：此规格已准备好接受 `/story-readiness` 验证，然后才能实施。在故事的 GDD Reference 字段中引用此规格。
```

### Pipeline Notes
### 流水线说明

Verdict: **COMPLETE** — quick design spec written and ready for implementation.
结论：**COMPLETE（完成）** —— 快速设计规格已写入并准备好实施。

Quick Design Specs **bypass** `/design-review` and `/review-all-gdds` by
design. They are for small, low-risk, well-scoped changes where the cost of
the full review pipeline exceeds the risk of the change itself.
快速设计规格按设计**绕过** `/design-review` 和 `/review-all-gdds`。
它们用于小型、低风险、范围明确的变更，在这些变更中，完整评审流水线的成本超过了变更本身的风险。

Redirect to the full pipeline if any of the following are true:
- The change adds a new system that belongs in the systems index
- The change significantly alters cross-system behavior or a system's
  contracts with other systems
- The change introduces new player-facing mechanics that affect the
  game's MDA aesthetic balance
- Implementation is likely to exceed one week of work
如果以下任何一项为真，则重定向到完整流水线：
- 变更添加了应属于系统索引的新系统
- 变更显著改变了跨系统行为或系统与其他系统的契约
- 变更引入了影响游戏 MDA 美学平衡的面向玩家的机制
- 实现可能超过一周的工作

In those cases: "This change has grown beyond quick-spec scope. I recommend
using `/design-system` to author a full GDD for this."
在这些情况下："此变更已超出快速规格范围。我建议使用 `/design-system` 为此编写完整 GDD。"

---

## Recommended Next Steps
## 推荐的下一步

- Run `/story-readiness [story-path]` to validate the story before implementation begins — reference this spec in the story's GDD Reference field
- 运行 `/story-readiness [story-path]` 在实施开始前验证故事 —— 在故事的 GDD Reference 字段中引用此规格
- Run `/dev-story [story-path]` to implement once the story passes readiness checks
- 一旦故事通过就绪检查，运行 `/dev-story [story-path]` 进行实施
- If the change is larger than expected, run `/design-system [system-name]` to author a full GDD instead
- 如果变更比预期大，改为运行 `/design-system [system-name]` 编写完整 GDD
