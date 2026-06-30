---
name: adopt
description: "Brownfield onboarding — audits existing project artifacts for template format compliance (not just existence), classifies gaps by impact, and produces a numbered migration plan. Run this when joining an in-progress project or upgrading from an older template version. Distinct from /project-stage-detect (which checks what exists) — this checks whether what exists will actually work with the template's skills."
argument-hint: "[focus: full | gdds | adrs | stories | infra]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
agent: technical-director
---
---
name: adopt
description: "Brownfield 入门 —— 审计现有项目产出物是否符合模板格式（不仅是存在性），按影响分类缺口，并生成编号的迁移计划。在加入进行中的项目或从旧模板版本升级时运行。与 /project-stage-detect（检查存在什么）不同 —— 它检查现有的东西是否能真正与模板的技能配合工作。"
argument-hint: "[focus: full | gdds | adrs | stories | infra]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
agent: technical-director
---

# Adopt — Brownfield Template Adoption

This skill audits an existing project's artifacts for **format compliance** with
the template's skill pipeline, then produces a prioritised migration plan.

**This is not `/project-stage-detect`.**
`/project-stage-detect` answers: *what exists?*
`/adopt` answers: *will what exists actually work with the template's skills?*

A project can have GDDs, ADRs, and stories — and every format-sensitive skill
will still fail silently or produce wrong results if those artifacts are in the
wrong internal format.

**Output:** `docs/adoption-plan-[date].md` — a persistent, checkable migration plan.

**Argument modes:**

**Audit mode:** `$ARGUMENTS[0]` (blank = `full`)

- **No argument / `full`**: Complete audit — all artifact types
- **`gdds`**: GDD format compliance only
- **`adrs`**: ADR format compliance only
- **`stories`**: Story format compliance only
- **`infra`**: Infrastructure artifact gaps only (registry, manifest, sprint-status, stage.txt)

# Adopt —— Brownfield 模板采纳

本技能审计现有项目产出物是否符合
模板技能流水线的 **格式合规性**，然后生成优先级排序的迁移计划。

**这不是 `/project-stage-detect`。**
`/project-stage-detect` 回答：*存在什么？*
`/adopt` 回答：*存在的东西是否能真正与模板的技能配合工作？*

一个项目可以有 GDD、ADR 和故事 —— 但如果这些产出物
内部格式不对，每个对格式敏感的技能
仍会静默失败或产生错误结果。

**输出：** `docs/adoption-plan-[date].md` —— 一份持久的、可检查的迁移计划。

**参数模式：**

**审计模式：** `$ARGUMENTS[0]`（为空 = `full`）

- **无参数 / `full`**：完整审计 —— 所有产出物类型
- **`gdds`**：仅 GDD 格式合规性
- **`adrs`**：仅 ADR 格式合规性
- **`stories`**：仅故事格式合规性
- **`infra`**：仅基础设施产出物缺口（注册表、清单、sprint-status、stage.txt）

---

## Phase 1: Detect Project State

Emit one line before reading: `"Scanning project artifacts..."` — this confirms the
skill is running during the silent read phase.

Then read silently before presenting anything else.

### Existence check
- `production/stage.txt` — if present, read it (authoritative phase)
- `design/gdd/game-concept.md` — concept exists?
- `design/gdd/systems-index.md` — systems index exists?
- Count GDD files: `design/gdd/*.md` (excluding game-concept.md and systems-index.md)
- Count ADR files: `docs/architecture/adr-*.md`
- Count story files: `production/epics/**/*.md` (excluding EPIC.md)
- `.claude/docs/technical-preferences.md` — engine configured?
- `docs/engine-reference/` — engine reference docs present?
- Glob `docs/adoption-plan-*.md` — note the filename of the most recent prior plan if any exist

### Infer phase (if no stage.txt)
Use the same heuristic as `/project-stage-detect`:
- 10+ source files in `src/` → Production
- Stories in `production/epics/` → Pre-Production
- ADRs exist → Technical Setup
- systems-index.md exists → Systems Design
- game-concept.md exists → Concept
- Nothing → Fresh (not a brownfield project — suggest `/start`)

If the project appears fresh (no artifacts at all), use `AskUserQuestion`:
- "This looks like a fresh project — no existing artifacts found. `/adopt` is for
  projects with work to migrate. What would you like to do?"
  - "Run `/start` — begin guided first-time onboarding"
  - "My artifacts are in a non-standard location — help me find them"
  - "Cancel"

Then stop — do not proceed with the audit regardless of which option the user picks
(each option leads to a different skill or manual investigation).

Report: "Detected phase: [phase]. Found: [N] GDDs, [M] ADRs, [P] stories."

## 第 1 阶段：检测项目状态

读取前发出一行：`"Scanning project artifacts..."` —— 这确认
技能在静默读取阶段运行。

然后在呈现任何其他内容前静默读取。

### 存在性检查
- `production/stage.txt` —— 如存在，读取它（权威阶段）
- `design/gdd/game-concept.md` —— 概念存在？
- `design/gdd/systems-index.md` —— 系统索引存在？
- 计数 GDD 文件：`design/gdd/*.md`（排除 game-concept.md 和 systems-index.md）
- 计数 ADR 文件：`docs/architecture/adr-*.md`
- 计数故事文件：`production/epics/**/*.md`（排除 EPIC.md）
- `.claude/docs/technical-preferences.md` —— 引擎已配置？
- `docs/engine-reference/` —— 引擎参考文档存在？
- Glob `docs/adoption-plan-*.md`` —— 如存在，记录最近一次先前计划的文件名

### 推断阶段（如无 stage.txt）
使用与 `/project-stage-detect` 相同的启发式：
- `src/` 中 10+ 源文件 → 生产
- `production/epics/` 中有故事 → 预生产
- ADR 存在 → 技术设置
- systems-index.md 存在 → 系统设计
- game-concept.md 存在 → 概念
- 无 → 全新（非 brownfield 项目 —— 建议 `/start`）

如项目看起来全新（无任何产出物），使用 `AskUserQuestion`：
- "这看起来是一个全新项目 —— 未找到现有产出物。`/adopt` 适用于
  有工作要迁移的项目。你想做什么？"
  - "运行 `/start` —— 开始引导式首次入门"
  - "我的产出物在非标准位置 —— 帮我找到它们"
  - "取消"

然后停止 —— 无论用户选择哪个选项都不要继续审计
（每个选项都导向不同的技能或人工调查）。

报告："检测到阶段：[phase]。找到：[N] 个 GDD、[M] 个 ADR、[P] 个故事。"

---

## Phase 2: Format Audit

For each artifact type in scope (based on argument mode), check not just that
the file exists but that it contains the internal structure the template requires.

### 2a: GDD Format Audit

For each GDD file found, check for the 8 required sections by scanning headings:

| Required Section | Heading pattern to look for |
|---|---|
| Overview | `## Overview` |
| Player Fantasy | `## Player Fantasy` |
| Detailed Rules / Design | `## Detailed` or `## Core Rules` or `## Detailed Design` |
| Formulas | `## Formulas` or `## Formula` |
| Edge Cases | `## Edge Cases` |
| Dependencies | `## Dependencies` or `## Depends` |
| Tuning Knobs | `## Tuning` |
| Acceptance Criteria | `## Acceptance` |

For each GDD, record:
- Which sections are present
- Which sections are missing
- Whether it has any content in present sections or just placeholder text
  (`[To be designed]` or equivalent)

Also check: does each GDD have a `**Status**:` field in its header block?
Valid values: `In Design`, `Designed`, `In Review`, `Approved`, `Needs Revision`.

### 2b: ADR Format Audit

For each ADR file found, check for these critical sections:

| Section | Impact if missing |
|---|---|
| `## Status` | **BLOCKING** — `/story-readiness` ADR status check silently passes everything |
| `## ADR Dependencies` | HIGH — dependency ordering in `/architecture-review` breaks |
| `## Engine Compatibility` | HIGH — post-cutoff API risk is unknown |
| `## GDD Requirements Addressed` | MEDIUM — traceability matrix loses coverage |
| `## Performance Implications` | LOW — not pipeline-critical |

For each ADR, record: which sections present, which missing, current Status value
if the Status section exists.

### 2c: systems-index.md Format Audit

If `design/gdd/systems-index.md` exists:

1. **Parenthetical status values** — Grep for any Status cell containing
   parentheses: `"Needs Revision ("`, `"In Progress ("`, etc.
   These break exact-string matching in `/gate-check`, `/create-stories`,
   and `/architecture-review`. **BLOCKING.**

2. **Valid status values** — check that Status column values are only from:
   `Not Started`, `In Progress`, `In Review`, `Designed`, `Approved`, `Needs Revision`
   Flag any unrecognised values.

3. **Column structure** — check that the table has at minimum: System name,
   Layer, Priority, Status columns. Missing columns degrade skill functionality.

### 2d: Story Format Audit

For each story file found:

- **`Manifest Version:` field** — present in story header? (LOW — auto-passes if absent)
- **TR-ID reference** — does story contain `TR-[a-z]+-[0-9]+` pattern? (MEDIUM — no staleness tracking)
- **ADR reference** — does story reference at least one ADR? (check for `ADR-` pattern)
- **Status field** — present and readable?
- **Acceptance criteria** — does the story have a checkbox list (`- [ ]`)?

### 2e: Infrastructure Audit

| Artifact | Path | Impact if missing |
|---|---|---|
| TR registry | `docs/architecture/tr-registry.yaml` | HIGH — no stable requirement IDs |
| Control manifest | `docs/architecture/control-manifest.md` | HIGH — no layer rules for stories |
| Manifest version stamp | In manifest header: `Manifest Version:` | MEDIUM — staleness checks blind |
| Sprint status | `production/sprint-status.yaml` | MEDIUM — `/sprint-status` falls back to markdown |
| Stage file | `production/stage.txt` | MEDIUM — phase auto-detect unreliable |
| Engine reference | `docs/engine-reference/[engine]/VERSION.md` | HIGH — ADR engine checks blind |
| Architecture traceability | `docs/architecture/architecture-traceability.md` | MEDIUM — no persistent matrix |

### 2f: Technical Preferences Audit

Read `.claude/docs/technical-preferences.md`. Check each field for `[TO BE CONFIGURED]`:
- Engine, Language, Rendering, Physics → HIGH if unconfigured (ADR skills fail)
- Naming conventions → MEDIUM
- Performance budgets → MEDIUM
- Forbidden Patterns, Allowed Libraries → LOW (starts empty by design)

## 第 2 阶段：格式审计

对于范围内的每种产出物类型（基于参数模式），不仅检查
文件是否存在，还检查它是否包含模板要求的内部结构。

### 2a：GDD 格式审计

对于找到的每个 GDD 文件，通过扫描标题检查 8 个必需章节：

| 必需章节 | 要查找的标题模式 |
|---|---|
| Overview | `## Overview` |
| Player Fantasy | `## Player Fantasy` |
| Detailed Rules / Design | `## Detailed` or `## Core Rules` or `## Detailed Design` |
| Formulas | `## Formulas` or `## Formula` |
| Edge Cases | `## Edge Cases` |
| Dependencies | `## Dependencies` or `## Depends` |
| Tuning Knobs | `## Tuning` |
| Acceptance Criteria | `## Acceptance` |

对于每个 GDD，记录：
- 存在哪些章节
- 缺失哪些章节
- 现有章节中是否有真实内容还是仅占位符文本
  （`[To be designed]` 或同等文本）

同时检查：每个 GDD 的头部块中是否有 `**Status**:` 字段？
有效值：`In Design`、`Designed`、`In Review`、`Approved`、`Needs Revision`。

### 2b：ADR 格式审计

对于找到的每个 ADR 文件，检查这些关键章节：

| 章节 | 缺失的影响 |
|---|---|
| `## Status` | **BLOCKING** —— `/story-readiness` 的 ADR 状态检查会静默通过一切 |
| `## ADR Dependencies` | HIGH —— `/architecture-review` 中的依赖排序会断 |
| `## Engine Compatibility` | HIGH —— 截止线后 API 风险未知 |
| `## GDD Requirements Addressed` | MEDIUM —— 可追溯性矩阵丢失覆盖 |
| `## Performance Implications` | LOW —— 非流水线关键 |

对于每个 ADR，记录：现有章节、缺失章节、如 Status 章节存在则记录当前 Status 值。

### 2c：systems-index.md 格式审计

如 `design/gdd/systems-index.md` 存在：

1. **带括号的状态值** —— Grep 任何包含
   括号的 Status 单元格：`"Needs Revision ("`、`"In Progress ("` 等。
   这些会破坏 `/gate-check`、`/create-stories`、
   和 `/architecture-review` 中的精确字符串匹配。**BLOCKING。**

2. **有效状态值** —— 检查 Status 列值是否仅来自：
   `Not Started`、`In Progress`、`In Review`、`Designed`、`Approved`、`Needs Revision`
   标记任何无法识别的值。

3. **列结构** —— 检查表格至少包含：System name、
   Layer、Priority、Status 列。缺失列会降低技能功能。

### 2d：故事格式审计

对于找到的每个故事文件：

- **`Manifest Version:` 字段** —— 故事头部是否存在？（LOW —— 如缺失则自动通过）
- **TR-ID 引用** —— 故事是否包含 `TR-[a-z]+-[0-9]+` 模式？（MEDIUM —— 无陈旧性跟踪）
- **ADR 引用** —— 故事是否引用至少一个 ADR？（检查 `ADR-` 模式）
- **Status 字段** —— 存在且可读？
- **验收标准** —— 故事是否有复选框列表（`- [ ]`）？

### 2e：基础设施审计

| 产出物 | 路径 | 缺失的影响 |
|---|---|---|
| TR 注册表 | `docs/architecture/tr-registry.yaml` | HIGH —— 无稳定需求 ID |
| 控制清单 | `docs/architecture/control-manifest.md` | HIGH —— 故事无层规则 |
| 清单版本戳 | 在清单头部：`Manifest Version:` | MEDIUM —— 陈旧性检查失效 |
| 冲刺状态 | `production/sprint-status.yaml` | MEDIUM —— `/sprint-status` 回退到 markdown |
| 阶段文件 | `production/stage.txt` | MEDIUM —— 阶段自动检测不可靠 |
| 引擎参考 | `docs/engine-reference/[engine]/VERSION.md` | HIGH —— ADR 引擎检查失效 |
| 架构可追溯性 | `docs/architecture/architecture-traceability.md` | MEDIUM —— 无持久矩阵 |

### 2f：技术偏好审计

读取 `.claude/docs/technical-preferences.md`。检查每个字段是否有 `[TO BE CONFIGURED]`：
- Engine、Language、Rendering、Physics → 未配置则为 HIGH（ADR 技能失败）
- Naming conventions → MEDIUM
- Performance budgets → MEDIUM
- Forbidden Patterns、Allowed Libraries → LOW（按设计初始为空）

---

## Phase 3: Classify and Prioritise Gaps

Organise every gap found across all audits into four severity tiers:

**BLOCKING** — Will cause template skills to silently produce wrong results *right now*.
Examples: ADR missing Status field, systems-index parenthetical status values,
engine not configured when ADRs exist.

**HIGH** — Will cause stories to be generated with missing safety checks, or
infrastructure bootstrapping will fail.
Examples: ADRs missing Engine Compatibility, GDDs missing Acceptance Criteria
(stories can't be generated from them), tr-registry.yaml missing.

**MEDIUM** — Degrades quality and pipeline tracking but does not break functionality.
Examples: GDDs missing Tuning Knobs or Formulas sections, stories missing TR-IDs,
sprint-status.yaml missing.

**LOW** — Retroactive improvements that are nice-to-have but not urgent.
Examples: Stories missing Manifest Version stamps, GDDs missing Open Questions section.

Count totals per tier. If zero BLOCKING and zero HIGH gaps: report that the project
is template-compatible and only advisory improvements remain.

## 第 3 阶段：分类并优先级排序缺口

将所有审计中发现的每个缺口组织到四个严重性层级：

**BLOCKING** —— 会 *立即* 导致模板技能静默产生错误结果。
示例：ADR 缺少 Status 字段、systems-index 带括号的状态值、
ADR 存在时引擎未配置。

**HIGH** —— 会导致故事生成时缺少安全检查，或
基础设施引导会失败。
示例：ADR 缺少 Engine Compatibility、GDD 缺少 Acceptance Criteria
（无法从中生成故事）、tr-registry.yaml 缺失。

**MEDIUM** —— 降低质量和流水线跟踪但不破坏功能。
示例：GDD 缺少 Tuning Knobs 或 Formulas 章节、故事缺少 TR-ID、
sprint-status.yaml 缺失。

**LOW** —— 锦上添花但不紧急的回顾性改进。
示例：故事缺少 Manifest Version 戳、GDD 缺少 Open Questions 章节。

按层级统计总数。如 BLOCKING 和 HIGH 缺口都为零：报告项目
与模板兼容，仅剩建议性改进。

---

## Phase 4: Build the Migration Plan

Compose a numbered, ordered action plan. Ordering rules:
1. BLOCKING gaps first (must fix before any pipeline skill runs reliably)
2. HIGH gaps next, infrastructure before GDD/ADR content (bootstrapping needs correct formats)
3. MEDIUM gaps ordered: GDD gaps before ADR gaps before story gaps (stories depend on GDDs and ADRs)
4. LOW gaps last

For each gap, produce a plan entry with:
- A clear problem statement (one sentence, no jargon)
- The exact command to fix it, if a skill handles it
- Manual steps if it requires direct editing
- A time estimate (rough: 5 min / 30 min / 1 session)
- A checkbox `- [ ]` for tracking

**Special case — systems-index parenthetical status values:**
This is always the first item if present. Show the exact values that need changing
and the exact replacement text. Offer to fix this immediately before writing the plan.

**Special case — ADRs missing Status field:**
For each affected ADR, the fix is:
`/architecture-decision retrofit docs/architecture/adr-[NNNN]-[slug].md`
List each ADR as a separate checkable item.

**Special case — GDDs missing sections:**
For each affected GDD, list which sections are missing and the fix:
`/design-system retrofit design/gdd/[filename].md`

**Infrastructure bootstrap ordering** — always present in this sequence:
1. Fix ADR formats first (registry depends on reading ADR Status fields)
2. Run `/architecture-review` → bootstraps `tr-registry.yaml`
3. Run `/create-control-manifest` → creates manifest with version stamp
4. Run `/sprint-plan update` → creates `sprint-status.yaml`
5. Run `/gate-check [phase]` → writes `stage.txt` authoritatively

**Existing stories** — note explicitly:
> "Existing stories continue to work with all template skills — all new format
> checks auto-pass when the fields are absent. They won't benefit from TR-ID
> staleness tracking or manifest version checks until they're regenerated. This
> is intentional: do not regenerate stories that are already in progress."

## 第 4 阶段：构建迁移计划

编写编号、有序的行动计划。排序规则：
1. BLOCKING 缺口优先（必须先修复才能让任何流水线技能可靠运行）
2. HIGH 缺口其次，基础设施在 GDD/ADR 内容之前（引导需要正确格式）
3. MEDIUM 缺口排序：GDD 缺口在 ADR 缺口之前，在故事缺口之前（故事依赖 GDD 和 ADR）
4. LOW 缺口最后

对于每个缺口，生成一个计划条目，包含：
- 清晰的问题陈述（一句话，无术语）
- 修复它的确切命令，如有技能处理
- 如需直接编辑的人工步骤
- 时间估算（粗略：5 分钟 / 30 分钟 / 1 会话）
- 用于跟踪的复选框 `- [ ]`

**特殊情况 —— systems-index 带括号的状态值：**
如存在，此项始终是第一项。展示需要更改的确切值
和确切的替换文本。在写计划前主动提供立即修复。

**特殊情况 —— ADR 缺少 Status 字段：**
对于每个受影响的 ADR，修复方式是：
`/architecture-decision retrofit docs/architecture/adr-[NNNN]-[slug].md`
将每个 ADR 列为单独的可勾选项。

**特殊情况 —— GDD 缺少章节：**
对于每个受影响的 GDD，列出缺少哪些章节及修复：
`/design-system retrofit design/gdd/[filename].md`

**基础设施引导顺序** —— 始终按此顺序呈现：
1. 先修复 ADR 格式（注册表依赖读取 ADR Status 字段）
2. 运行 `/architecture-review` → 引导 `tr-registry.yaml`
3. 运行 `/create-control-manifest` → 创建带版本戳的清单
4. 运行 `/sprint-plan update` → 创建 `sprint-status.yaml`
5. 运行 `/gate-check [phase]` → 权威写入 `stage.txt`

**现有故事** —— 明确说明：
> "现有故事可与所有模板技能配合工作 —— 字段缺失时所有新格式
> 检查自动通过。它们不会从 TR-ID
> 陈旧性跟踪或清单版本检查中获益，直到重新生成。这是
> 有意的：不要重新生成进行中的故事。"

---

## Phase 5: Present Summary and Ask to Write

Present a compact summary before writing:

```
## Adoption Audit Summary
Phase detected: [phase]
Engine: [configured / NOT CONFIGURED]
GDDs audited: [N] ([X] fully compliant, [Y] with gaps)
ADRs audited: [N] ([X] fully compliant, [Y] with gaps)
Stories audited: [N]

Gap counts:
  BLOCKING: [N] — template skills will malfunction without these fixes
  HIGH:     [N] — unsafe to run /create-stories or /story-readiness
  MEDIUM:   [N] — quality degradation
  LOW:      [N] — optional improvements

Estimated remediation: [X blocking items × ~Y min each = roughly Z hours]
```

Before asking to write, show a **Gap Preview**:
- List every BLOCKING gap as a one-line bullet describing the actual problem
  (e.g. `systems-index.md: 3 rows have parenthetical status values`,
  `adr-0002.md: missing ## Status section`). No counts — show the actual items.
- Show HIGH / MEDIUM / LOW as counts only (e.g. `HIGH: 4, MEDIUM: 2, LOW: 1`).

This gives the user enough context to judge scope before committing to writing the file.

If a prior adoption plan was detected in Phase 1, add a note:
> "A previous plan exists at `docs/adoption-plan-[prior-date].md`. The new plan will
> reflect current project state — it does not diff against the prior run."

Use `AskUserQuestion`:
- "Ready to write the migration plan?"
  - "Yes — write `docs/adoption-plan-[date].md`"
  - "Show me the full plan preview first (don't write yet)"
  - "Cancel — I'll handle migration manually"

If the user picks "Show me the full plan preview", output the complete plan as a
fenced markdown block. Then ask again with the same three options.

## 第 5 阶段：呈现总结并请求写入

写入前呈现紧凑总结：

```
## Adoption Audit Summary
Phase detected: [phase]
Engine: [configured / NOT CONFIGURED]
GDDs audited: [N] ([X] fully compliant, [Y] with gaps)
ADRs audited: [N] ([X] fully compliant, [Y] with gaps)
Stories audited: [N]

Gap counts:
  BLOCKING: [N] — template skills will malfunction without these fixes
  HIGH:     [N] — unsafe to run /create-stories or /story-readiness
  MEDIUM:   [N] — quality degradation
  LOW:      [N] — optional improvements

Estimated remediation: [X blocking items × ~Y min each = roughly Z hours]
```

请求写入前，展示 **缺口预览**：
- 将每个 BLOCKING 缺口列为描述实际问题的一行要点
  （例如 `systems-index.md: 3 rows have parenthetical status values`、
  `adr-0002.md: missing ## Status section`）。不要计数 —— 展示实际项。
- HIGH / MEDIUM / LOW 仅显示计数（例如 `HIGH: 4, MEDIUM: 2, LOW: 1`）。

这给用户足够的上下文在提交写入文件前判断范围。

如在第 1 阶段检测到先前采纳计划，添加备注：
> "先前计划存在于 `docs/adoption-plan-[prior-date].md`。新计划将
> 反映当前项目状态 —— 它不会与先前运行进行 diff。"

使用 `AskUserQuestion`：
- "准备好写入迁移计划了吗？"
  - "是 —— 写入 `docs/adoption-plan-[date].md`"
  - "先给我看完整计划预览（暂不写入）"
  - "取消 —— 我手动处理迁移"

如用户选择 "先给我看完整计划预览"，将完整计划输出为
带围栏的 markdown 块。然后用同样的三个选项再次询问。

---

## Phase 6: Write the Adoption Plan

If approved, write `docs/adoption-plan-[date].md` with this structure:

```markdown
# Adoption Plan

> **Generated**: [date]
> **Project phase**: [phase]
> **Engine**: [name + version, or "Not configured"]
> **Template version**: v1.0+

Work through these steps in order. Check off each item as you complete it.
Re-run `/adopt` anytime to check remaining gaps.

---

## Step 1: Fix Blocking Gaps

[One sub-section per blocking gap with problem, fix command, time estimate, checkbox]

---

## Step 2: Fix High-Priority Gaps

[One sub-section per high gap]

---

## Step 3: Bootstrap Infrastructure

### 3a. Register existing requirements (creates tr-registry.yaml)
Run `/architecture-review` — even if ADRs already exist, this run bootstraps
the TR registry from your existing GDDs and ADRs.
**Time**: 1 session (review can be long for large codebases)
- [ ] tr-registry.yaml created

### 3b. Create control manifest
Run `/create-control-manifest`
**Time**: 30 min
- [ ] docs/architecture/control-manifest.md created

### 3c. Create sprint tracking file
Run `/sprint-plan update`
**Time**: 5 min (if sprint plan already exists as markdown)
- [ ] production/sprint-status.yaml created

### 3d. Set authoritative project stage
Run `/gate-check [current-phase]`
**Time**: 5 min
- [ ] production/stage.txt written

---

## Step 4: Medium-Priority Gaps

[One sub-section per medium gap]

---

## Step 5: Optional Improvements

[One sub-section per low gap]

---

## What to Expect from Existing Stories

Existing stories continue to work with all template skills. New format checks
(TR-ID validation, manifest version staleness) auto-pass when the fields are
absent — so nothing breaks. They won't benefit from staleness tracking until
regenerated. Do not regenerate stories that are in progress or done.

---

## Re-run

Run `/adopt` again after completing Step 3 to verify all blocking and high gaps
are resolved. The new run will reflect the current state of the project.
```

## 第 6 阶段：写入采纳计划

如获批准，按此结构写入 `docs/adoption-plan-[date].md`：

```markdown
# Adoption Plan

> **Generated**: [date]
> **Project phase**: [phase]
> **Engine**: [name + version, or "Not configured"]
> **Template version**: v1.0+

Work through these steps in order. Check off each item as you complete it.
Re-run `/adopt` anytime to check remaining gaps.

---

## Step 1: Fix Blocking Gaps

[One sub-section per blocking gap with problem, fix command, time estimate, checkbox]

---

## Step 2: Fix High-Priority Gaps

[One sub-section per high gap]

---

## Step 3: Bootstrap Infrastructure

### 3a. Register existing requirements (creates tr-registry.yaml)
Run `/architecture-review` — even if ADRs already exist, this run bootstraps
the TR registry from your existing GDDs and ADRs.
**Time**: 1 session (review can be long for large codebases)
- [ ] tr-registry.yaml created

### 3b. Create control manifest
Run `/create-control-manifest`
**Time**: 30 min
- [ ] docs/architecture/control-manifest.md created

### 3c. Create sprint tracking file
Run `/sprint-plan update`
**Time**: 5 min (if sprint plan already exists as markdown)
- [ ] production/sprint-status.yaml created

### 3d. Set authoritative project stage
Run `/gate-check [current-phase]`
**Time**: 5 min
- [ ] production/stage.txt written

---

## Step 4: Medium-Priority Gaps

[One sub-section per medium gap]

---

## Step 5: Optional Improvements

[One sub-section per low gap]

---

## What to Expect from Existing Stories

Existing stories continue to work with all template skills. New format checks
(TR-ID validation, manifest version staleness) auto-pass when the fields are
absent — so nothing breaks. They won't benefit from staleness tracking until
regenerated. Do not regenerate stories that are in progress or done.

---

## Re-run

Run `/adopt` again after completing Step 3 to verify all blocking and high gaps
are resolved. The new run will reflect the current state of the project.
```

---

## Phase 6b: Set Review Mode

After writing the adoption plan (or if the user cancels writing), check whether
`production/review-mode.txt` exists.

**If it exists**: Read it and note the current mode — "Review mode is already set to `[current]`." — skip the prompt.

**If it does not exist**: Use `AskUserQuestion`:

- **Prompt**: "One more setup step: how much design review would you like as you work through the workflow?"
- **Options**:
  - `Full` — Director specialists review at each key workflow step. Best for teams, learning the workflow, or when you want thorough feedback on every decision.
  - `Lean (recommended)` — Directors only at phase gate transitions (/gate-check). Skips per-skill reviews. Balanced for solo devs and small teams.
  - `Solo` — No director reviews at all. Maximum speed. Best for game jams, prototypes, or if reviews feel like overhead.

Write the choice to `production/review-mode.txt` immediately after selection — no separate "May I write?" needed:
- `Full` → write `full`
- `Lean (recommended)` → write `lean`
- `Solo` → write `solo`

Create the `production/` directory if it does not exist.

## 第 6b 阶段：设置评审模式

写入采纳计划后（或用户取消写入），检查
`production/review-mode.txt` 是否存在。

**如存在**：读取它并记录当前模式 —— "Review mode is already set to `[current]`."（评审模式已设置为 `[current]`。）—— 跳过提示。

**如不存在**：使用 `AskUserQuestion`：

- **提示**："再一个设置步骤：在工作流中你希望多大的设计评审深度？"
- **选项**：
  - `Full` —— 总监专家在每个关键工作流步骤评审。最适合团队、学习工作流或希望对每个决策获得详尽反馈的情况。
  - `Lean (recommended)` —— 总监仅在阶段门禁过渡时出现（/gate-check）。跳过每技能评审。对独立开发和小团队平衡。
  - `Solo` —— 完全无总监评审。最快速度。最适合 game jam、原型或觉得评审是负担时。

选择后立即将选择写入 `production/review-mode.txt` —— 无需单独的"可以写入吗？"：
- `Full` → 写入 `full`
- `Lean (recommended)` → 写入 `lean`
- `Solo` → 写入 `solo`

如 `production/` 目录不存在则创建它。

---

## Phase 7: Offer First Action

After writing the plan, don't stop there. Pick the single highest-priority gap
and offer to handle it immediately using `AskUserQuestion`. Choose the first
branch that applies:

**If there are parenthetical status values in systems-index.md:**
Use `AskUserQuestion`:
- "The most urgent fix is `systems-index.md` — [N] rows have parenthetical status
  values (e.g. `Needs Revision (see notes)`) that break /gate-check,
  /create-stories, and /architecture-review right now. I can fix these in-place."
  - "Fix it now — edit systems-index.md"
  - "I'll fix it myself"
  - "Done — leave me with the plan"

**If ADRs are missing `## Status` (and no parenthetical issue):**
Use `AskUserQuestion`:
- "The most urgent fix is adding `## Status` to [N] ADR(s): [list filenames].
  Without it, /story-readiness silently passes all ADR checks. Start with
  [first affected filename]?"
  - "Yes — retrofit [first affected filename] now"
  - "Retrofit all [N] ADRs one by one"
  - "I'll handle ADRs myself"

**If GDDs are missing Acceptance Criteria (and no blocking issues above):**
Use `AskUserQuestion`:
- "The most urgent gap is missing Acceptance Criteria in [N] GDD(s):
  [list filenames]. Without them, /create-stories can't generate stories.
  Start with [highest-priority GDD filename]?"
  - "Yes — add Acceptance Criteria to [GDD filename] now"
  - "Do all [N] GDDs one by one"
  - "I'll handle GDDs myself"

**If no BLOCKING or HIGH gaps exist:**
Use `AskUserQuestion`:
- "No blocking gaps — this project is template-compatible. What next?"
  - "Walk me through the medium-priority improvements"
  - "Run /project-stage-detect for a broader health check"
  - "Done — I'll work through the plan at my own pace"

> **Adoption plan saved to `docs/adoption-plan-[date].md`.** Re-run `/adopt` at any time to re-check remaining gaps as you complete them.

## 第 7 阶段：提供首个行动

写完计划后，不要止步于此。挑选优先级最高的单个缺口
并使用 `AskUserQuestion` 主动提供立即处理。选择第一个
适用的分支：

**如 systems-index.md 中存在带括号的状态值：**
使用 `AskUserQuestion`：
- "最紧急的修复是 `systems-index.md` —— [N] 行有带括号的状态
  值（例如 `Needs Revision (see notes)`），会立即破坏 /gate-check、
  /create-stories 和 /architecture-review。我可以就地修复这些。"
  - "现在就修复 —— 编辑 systems-index.md"
  - "我自己修复"
  - "完成 —— 把计划留给我"

**如 ADR 缺少 `## Status`（且无括号问题）：**
使用 `AskUserQuestion`：
- "最紧急的修复是为 [N] 个 ADR 添加 `## Status`：[列出文件名]。
  缺少它，/story-readiness 会静默通过所有 ADR 检查。从
  [第一个受影响的文件名] 开始？"
  - "是 —— 现在就为 [第一个受影响的文件名] 做改造"
  - "逐个改造全部 [N] 个 ADR"
  - "我自己处理 ADR"

**如 GDD 缺少 Acceptance Criteria（且上面无阻塞问题）：**
使用 `AskUserQuestion`：
- "最紧急的缺口是 [N] 个 GDD 缺少 Acceptance Criteria：
  [列出文件名]。缺少它们，/create-stories 无法生成故事。
  从 [最高优先级 GDD 文件名] 开始？"
  - "是 —— 现在就为 [GDD 文件名] 添加 Acceptance Criteria"
  - "逐个处理全部 [N] 个 GDD"
  - "我自己处理 GDD"

**如无 BLOCKING 或 HIGH 缺口：**
使用 `AskUserQuestion`：
- "无阻塞性缺口 —— 此项目与模板兼容。接下来做什么？"
  - "带我走一遍中等优先级改进"
  - "运行 /project-stage-detect 进行更广泛的健康检查"
  - "完成 —— 我会按自己的节奏走完计划"

> **采纳计划已保存到 `docs/adoption-plan-[date].md`。** 随时重新运行 `/adopt` 以在完成后重新检查剩余缺口。

---

## Collaborative Protocol

1. **Read silently** — complete the full audit before presenting anything
2. **Show the summary first** — let the user see scope before asking to write
3. **Ask before writing** — always confirm before creating the adoption plan file
4. **Offer, don't force** — the plan is advisory; the user decides what to fix and when
5. **One action at a time** — after handing off the plan, offer one specific next step,
   not a list of six things to do simultaneously
6. **Never regenerate existing artifacts** — only fill gaps in what exists;
   do not rewrite GDDs, ADRs, or stories that already have content

## 协作协议

1. **静默读取** —— 在呈现任何内容前完成完整审计
2. **先展示总结** —— 让用户在请求写入前看到范围
3. **写入前询问** —— 创建采纳计划文件前始终确认
4. **提供而非强制** —— 计划是建议性的；用户决定修复什么及何时修复
5. **一次一个行动** —— 交接计划后，提供一个具体的下一步，
   而非六件要同时做的事的清单
6. **永远不要重新生成现有产出物** —— 仅填充现有内容中的缺口；
   不要重写已有内容的 GDD、ADR 或故事
