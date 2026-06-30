---
name: create-epics
description: "Translate approved GDDs + architecture into epics — one epic per architectural module. Defines scope, governing ADRs, engine risk, and untraced requirements. Does NOT break into stories — run /create-stories [epic-slug] after each epic is created."
argument-hint: "[system-name | layer: foundation|core|feature|presentation | all] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
model: sonnet
agent: technical-director
---
---
名称：create-epics
描述："将批准的 GDD + 架构转换为史诗 — 每个架构模块一个史诗。定义范围、管辖 ADR、引擎风险和未追溯的需求。不分解为故事 — 每个史诗创建后运行 /create-stories [epic-slug]。"
argument-hint："[系统名 | layer: foundation|core|feature|presentation | all] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Task, AskUserQuestion
model：sonnet
agent：technical-director
---

# Create Epics
# 创建史诗

An epic is a named, bounded body of work that maps to one architectural module.
It defines **what** needs to be built and **who owns it architecturally**. It
does not prescribe implementation steps — that is the job of stories.
史诗是一个命名的、有边界的工作体，映射到一个架构模块。
它定义需要构建**什么**以及**谁在架构上拥有它**。
它不规定实施步骤 — 那是故事的工作。

**Run this skill once per layer** as you approach that layer in development.
Do not create Feature layer epics until Core is nearly complete — the design
will have changed.
**在开发中接近每个层级时运行此技能一次。**
在核心接近完成之前不要创建功能层史诗 — 设计
已经改变了。

**Output:** `production/epics/[epic-slug]/EPIC.md` + `production/epics/index.md`
**输出：** `production/epics/[epic-slug]/EPIC.md` + `production/epics/index.md`

**Next step after each epic:** `/create-stories [epic-slug]`
**每个史诗后的下一步：** `/create-stories [epic-slug]`

**When to run:** After `/create-control-manifest` and `/architecture-review` pass.
**何时运行：** `/create-control-manifest` 和 `/architecture-review` 通过后。

---

## 1. Parse Arguments
## 1. 解析参数

Resolve the review mode (once, store for all gate spawns this run):
解析评审模式（仅一次，本次运行的所有门禁生成均使用此模式）：

1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`
1. 如果传入了 `--review [full|lean|solo]` → 使用该模式
2. 否则读取 `production/review-mode.txt` → 使用其中的值
3. 否则 → 默认为 `lean`

See `.claude/docs/director-gates.md` for the full check pattern.
有关完整的检查模式，请参见 `.claude/docs/director-gates.md`。

**Modes:**
- `/create-epics all` — process all systems in layer order
- `/create-epics layer: foundation` — Foundation layer only
- `/create-epics layer: core` — Core layer only
- `/create-epics layer: feature` — Feature layer only
- `/create-epics layer: presentation` — Presentation layer only
- `/create-epics [system-name]` — one specific system
- No argument — ask: "Which layer or system would you like to create epics for?"
**模式：**
- `/create-epics all` — 按层级顺序处理所有系统
- `/create-epics layer: foundation` — 仅基础层
- `/create-epics layer: core` — 仅核心层
- `/create-epics layer: feature` — 仅功能层
- `/create-epics layer: presentation` — 仅表现层
- `/create-epics [系统名]` — 一个特定系统
- 无参数 — 询问："你想为哪个层级或系统创建史诗？"

---

## 2. Load Inputs
## 2. 加载输入

### Step 2a — Summary scan (fast)
### 步骤 2a — 摘要扫描（快速）

Grep all GDDs for their `## Summary` sections before reading anything fully:
在完整读取任何内容之前，grep 所有 GDD 的 `## Summary` 部分：

```
Grep pattern="## Summary" glob="design/gdd/*.md" output_mode="content" -A 5
```

For `layer:` or `[system-name]` modes: filter to only in-scope GDDs based on
the Summary quick-reference. Skip full-reading anything out of scope.
对于 `layer:` 或 `[系统名]` 模式：基于
Summary 快速参考过滤为仅范围内的 GDD。跳过完整读取任何范围外的内容。

### Step 2b — Full document load (in-scope systems only)
### 步骤 2b — 完整文档加载（仅范围内的系统）

Using the Step 2a grep results, identify which systems are in scope. Read full documents **only for in-scope systems** — do not read GDDs or ADRs for out-of-scope systems or layers.
使用步骤 2a 的 grep 结果，识别哪些系统在范围内。**仅针对范围内的系统**读取完整文档 — 不要读取范围外系统或层级的 GDD 或 ADR。

Read for in-scope systems:
为范围内的系统读取：

- `design/gdd/systems-index.md` — authoritative system list, layers, priority
- In-scope GDDs only (Approved or Designed status, filtered by Step 2a results)
- `docs/architecture/architecture.md` — module ownership and API boundaries
- Accepted ADRs **whose domains cover in-scope systems only** — read the "GDD Requirements Addressed", "Decision", and "Engine Compatibility" sections; skip ADRs for unrelated domains
- `docs/architecture/control-manifest.md` — manifest version date from header
- `docs/architecture/tr-registry.yaml` — for tracing requirements to ADR coverage
- `docs/engine-reference/[engine]/VERSION.md` — engine name, version, risk levels
- `design/gdd/systems-index.md` — 权威系统列表、层级、优先级
- 仅范围内的 GDD（Approved 或 Designed 状态，按步骤 2a 结果过滤）
- `docs/architecture/architecture.md` — 模块所有权和 API 边界
- **其域仅覆盖范围内系统**的已接受 ADR — 读取"GDD Requirements Addressed"、"Decision"和"Engine Compatibility"部分；跳过不相关域的 ADR
- `docs/architecture/control-manifest.md` — 来自头部的清单版本日期
- `docs/architecture/tr-registry.yaml` — 用于追溯需求到 ADR 覆盖
- `docs/engine-reference/[engine]/VERSION.md` — 引擎名、版本、风险等级

Report: "Loaded [N] GDDs, [M] ADRs, engine: [name + version]."
报告："加载了 [N] 个 GDD，[M] 个 ADR，引擎：[名称 + 版本]。"

---

## 3. Processing Order
## 3. 处理顺序

Process in dependency-safe layer order:
按依赖安全的层级顺序处理：

1. **Foundation** (no dependencies)
2. **Core** (depends on Foundation)
3. **Feature** (depends on Core)
4. **Presentation** (depends on Feature + Core)
1. **基础**（无依赖）
2. **核心**（依赖基础）
3. **功能**（依赖核心）
4. **表现**（依赖功能 + 核心）

Within each layer, use the order from `systems-index.md`.
在每个层级内，使用 `systems-index.md` 中的顺序。

---

## 4. Define Each Epic
## 4. 定义每个史诗

For each system, map it to an architectural module from `architecture.md`.
对于每个系统，将其映射到 `architecture.md` 中的架构模块。

Check ADR coverage against the TR registry:
对照 TR 注册表检查 ADR 覆盖：

- **Traced requirements**: TR-IDs that have an Accepted ADR covering them
- **Untraced requirements**: TR-IDs with no ADR — warn before proceeding
- **已追溯的需求**：有已接受 ADR 覆盖的 TR-ID
- **未追溯的需求**：无 ADR 的 TR-ID — 在继续之前警告

Present to user before writing anything:
在写入任何内容之前向用户呈现：

```
## Epic: [System Name]

**Layer**: [Foundation / Core / Feature / Presentation]
**GDD**: design/gdd/[filename].md
**Architecture Module**: [module name from architecture.md]
**Governing ADRs**: [ADR-NNNN, ADR-MMMM]
**Engine Risk**: [LOW / MEDIUM / HIGH — highest risk among governing ADRs]
**GDD Requirements Covered by ADRs**: [N / total]
**Untraced Requirements**: [list TR-IDs with no ADR, or "None"]
```

If there are untraced requirements:
如果有未追溯的需求：

> "⚠️ [N] requirements in [system] have no ADR. The epic can be created, but
> stories for these requirements will be marked Blocked until ADRs exist.
> Run `/architecture-decision` first, or proceed with placeholders."
> "⚠️ [系统] 中的 [N] 个需求没有 ADR。可以创建史诗，但
> 这些需求的故事将标记为 Blocked，直到 ADR 存在。
> 先运行 `/architecture-decision`，或使用占位符继续。"

Use `AskUserQuestion`:
使用 `AskUserQuestion`：

- Prompt: "Shall I create Epic: [name]?"
- Options:
  - `[A] Yes, create it`
  - `[B] Skip this epic`
  - `[C] Pause — I need to write ADRs first`
- 提示："我是否创建史诗：[名称]？"
- 选项：
  - `[A] 是，创建它`
  - `[B] 跳过此史诗`
  - `[C] 暂停 — 我需要先写 ADR`

---

## 4b. Producer Epic Structure Gate
## 4b. 制作人史诗结构门禁

**Review mode check** — apply before spawning PR-EPIC:
**评审模式检查** — 在生成 PR-EPIC 之前应用：

- `solo` → skip. Note: "PR-EPIC skipped — Solo mode." Proceed to Step 5 (write epic files).
- `lean` → skip (not a PHASE-GATE). Note: "PR-EPIC skipped — Lean mode." Proceed to Step 5 (write epic files).
- `full` → spawn as normal.
- `solo` → 跳过。备注："PR-EPIC skipped — Solo mode." 进入步骤 5（写入史诗文件）。
- `lean` → 跳过（非 PHASE-GATE）。备注："PR-EPIC skipped — Lean mode." 进入步骤 5（写入史诗文件）。
- `full` → 正常生成。

After all epics for the current layer are defined (Step 4 completed for all in-scope systems), and before writing any files, spawn `producer` via Task using gate **PR-EPIC** (`.claude/docs/director-gates.md`).
当前层级的所有史诗定义后（所有范围内系统完成步骤 4），在写入任何文件之前，通过 Task 使用门禁 **PR-EPIC**（`.claude/docs/director-gates.md`）生成 `producer`。

Pass: the full epic structure summary (all epics, their scope summaries, governing ADR counts), the layer being processed, milestone timeline and team capacity.
传递：完整史诗结构摘要（所有史诗、其范围摘要、管辖 ADR 计数）、正在处理的层级、里程碑时间线和团队容量。

Present the producer's assessment.
呈现制作人的评估。

If UNREALISTIC: offer to revise epic boundaries (split overscoped or merge underscoped epics). Revise and re-run the gate before writing.
如果 UNREALISTIC：提供修订史诗边界（拆分范围过大或合并范围过小的史诗）的选项。修订并在写入前重新运行门禁。

If CONCERNS, use `AskUserQuestion`:
如果 CONCERNS，使用 `AskUserQuestion`：

- Prompt: "Producer raised concerns about the epic structure. How do you want to proceed?"
- Options:
  - `[A] Proceed as planned — I accept the producer's concerns`
  - `[B] Revise epic boundaries — split or merge as recommended`
  - `[C] Stop — I want to reconsider the scope`
- 提示："制作人对史诗结构提出顾虑。你想如何处理？"
- 选项：
  - `[A] 按计划进行 — 我接受制作人的顾虑`
  - `[B] 修订史诗边界 — 按建议拆分或合并`
  - `[C] 停止 — 我想重新考虑范围`

If [A]: proceed to Step 5.
If [B]: revise epic definitions from Step 4 and re-run the producer gate.
If [C]: stop. Verdict: **BLOCKED** — user wants to reconsider epic scope.
如果 [A]：进入步骤 5。
如果 [B]：从步骤 4 修订史诗定义并重新运行制作人门禁。
如果 [C]：停止。裁决：**BLOCKED** — 用户想重新考虑史诗范围。

Do not write epic files until the producer gate resolves.
在制作人门禁解决之前不要写入史诗文件。

---

## 5. Write Epic Files
## 5. 写入史诗文件

After approval, ask: "May I write the epic file to `production/epics/[epic-slug]/EPIC.md`?"
批准后，询问："我可以将史诗文件写入 `production/epics/[epic-slug]/EPIC.md` 吗？"

After user confirms, write:
用户确认后，写入：

### `production/epics/[epic-slug]/EPIC.md`
### `production/epics/[epic-slug]/EPIC.md`

```markdown
# Epic: [System Name]

> **Layer**: [Foundation / Core / Feature / Presentation]
> **GDD**: design/gdd/[filename].md
> **Architecture Module**: [module name]
> **Status**: Ready
> **Stories**: Not yet created — run `/create-stories [epic-slug]`

## Overview

[1 paragraph describing what this epic implements, derived from the GDD Overview
and the architecture module's stated responsibilities]

## Governing ADRs

| ADR | Decision Summary | Engine Risk |
|-----|-----------------|-------------|
| ADR-NNNN: [title] | [1-line summary] | LOW/MEDIUM/HIGH |

## GDD Requirements

| TR-ID | Requirement | ADR Coverage |
|-------|-------------|--------------|
| TR-[system]-001 | [requirement text from registry] | ADR-NNNN ✅ |
| TR-[system]-002 | [requirement text] | ❌ No ADR |

## Definition of Done

This epic is complete when:
- All stories are implemented, reviewed, and closed via `/story-done`
- All acceptance criteria from `design/gdd/[filename].md` are verified
- All Logic and Integration stories have passing test files in `tests/`
- All Visual/Feel and UI stories have evidence docs with sign-off in `production/qa/evidence/`

## Next Step

Run `/create-stories [epic-slug]` to break this epic into implementable stories.
```

### Update `production/epics/index.md`
### 更新 `production/epics/index.md`

Create or update the master index:
创建或更新主索引：

```markdown
# Epics Index

Last Updated: [date]
Engine: [name + version]

| Epic | Layer | System | GDD | Stories | Status |
|------|-------|--------|-----|---------|--------|
| [name] | Foundation | [system] | [file] | Not yet created | Ready |
```

---

## 6. Gate-Check Reminder
## 6. 门禁检查提醒

After writing all epics for the requested scope:
为请求范围写入所有史诗后：

- **Foundation + Core complete**: These are required for the Pre-Production →
  Production gate. Run `/gate-check production` to check readiness.
- **Reminder**: Epics define scope. Stories define implementation steps. Run
  `/create-stories [epic-slug]` for each epic before developers can pick up work.
- **基础 + 核心完成**：这些是前期制作 →
  制作门禁所必需的。运行 `/gate-check production` 检查就绪度。
- **提醒**：史诗定义范围。故事定义实施步骤。在开发者可以
  开始工作之前为每个史诗运行 `/create-stories [epic-slug]`。

---

## Collaborative Protocol
## 协作协议

1. **One epic at a time** — present each epic definition before asking to create it
2. **Warn on gaps** — flag untraced requirements before proceeding
3. **Ask before writing** — per-epic approval before writing any file
4. **No invention** — all content comes from GDDs, ADRs, and architecture docs
5. **Never create stories** — this skill stops at the epic level
1. **一次一个史诗** — 在请求创建之前呈现每个史诗定义
2. **警告缺口** — 在继续之前标记未追溯的需求
3. **写入前询问** — 写入任何文件之前获得每个史诗的批准
4. **不发明** — 所有内容来自 GDD、ADR 和架构文档
5. **从不创建故事** — 此技能在史诗层级停止

After all requested epics are processed:
所有请求的史诗处理后：

- **Verdict: COMPLETE** — [N] epic(s) written. Run `/create-stories [epic-slug]` per epic.
- **Verdict: BLOCKED** — user declined all epics, or no eligible systems found.
- **裁决：COMPLETE** — 已写入 [N] 个史诗。为每个史诗运行 `/create-stories [epic-slug]`。
- **裁决：BLOCKED** — 用户拒绝了所有史诗，或未找到符合条件的系统。
