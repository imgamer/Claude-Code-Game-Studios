---
name: qa-plan
description: "Generate a QA test plan for a sprint or feature. Reads GDDs and story files, classifies stories by test type (Logic/Integration/Visual/UI), and produces a structured test plan covering automated tests required, manual test cases, smoke test scope, and playtest sign-off requirements. Run before sprint begins or when starting a major feature."
argument-hint: "[sprint | feature: system-name | story: path]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
agent: qa-lead
---
---
name: qa-plan
description: "为冲刺或功能生成 QA 测试计划。读取 GDD 和故事文件，按测试类型（Logic/Integration/Visual/UI）对故事进行分类，并生成结构化的测试计划，涵盖所需自动化测试、手动测试用例、冒烟测试范围和试玩签署要求。在冲刺开始前或开始重大功能时运行。"
argument-hint: "[sprint | feature: system-name | story: path]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
agent: qa-lead
---

# QA Plan
# QA 计划

This skill generates a structured QA plan for a sprint, feature, or individual
story. It reads all in-scope story files and their referenced GDDs, classifies
each story by test type, and produces a plan that tells developers exactly what
to automate, what to verify manually, what the smoke test scope is, and when
to bring in a playtester.
此技能为冲刺、功能或单个故事生成结构化的 QA 计划。它读取所有范围内的故事文件及其引用的 GDD，按测试类型对每个故事进行分类，并生成一个计划，明确告诉开发者要自动化什么、手动验证什么、冒烟测试范围是什么，以及何时引入试玩人员。

Run this before a sprint begins so the team knows upfront what testing work
is required. A test plan written after implementation is a post-mortem, not a
plan.
在冲刺开始之前运行此技能，以便团队预先了解所需的测试工作。
在实现之后编写的测试计划是事后剖析，而非
计划。

**Output:** `production/qa/qa-plan-[sprint-slug]-[date].md`
**输出：** `production/qa/qa-plan-[sprint-slug]-[date].md`

---

## Phase 1: Parse Scope
## 阶段 1：解析范围

**Argument:** `$ARGUMENTS` (blank = ask user via AskUserQuestion)
**参数：** `$ARGUMENTS`（空 = 通过 AskUserQuestion 询问用户）

Determine scope from the argument:
从参数确定范围：

- **`sprint`** — read the most recent file in `production/sprints/`, extract
  every story file path referenced. If `production/sprint-status.yaml` exists,
  use it as the primary story list and fall back to the sprint plan for story
  metadata.
- **`feature: [system-name]`** — glob `production/epics/*/story-*.md`, filter
  to stories whose file path or title contains the system name. Also check the
  epic index file (`EPIC.md`) in that system's directory.
- **`story: [path]`** — validate that the path exists and load that single file.
- **No argument** — use `AskUserQuestion`:
  - "What is the scope for this QA plan?"
  - Options: "Current sprint", "Specific feature (enter system name)",
    "Specific story (enter path)", "Full epic"
- **`sprint`** — 读取 `production/sprints/` 中最近的文件，提取
  引用的每个故事文件路径。如果 `production/sprint-status.yaml` 存在，
  将其作为主要故事列表，并回退到冲刺计划获取故事
  元数据。
- **`feature: [system-name]`** — glob `production/epics/*/story-*.md`，过滤
  到文件路径或标题包含该系统名称的故事。同时检查
  该系统目录中的史诗索引文件（`EPIC.md`）。
- **`story: [path]`** — 验证路径存在并加载该单个文件。
- **无参数** — 使用 `AskUserQuestion`：
  - "此 QA 计划的范围是什么？"
  - 选项："当前冲刺"、"特定功能（输入系统名称）"、
    "特定故事（输入路径）"、"完整史诗"

After resolving scope, report: "Building QA plan for [N] stories in [scope]."
解析范围后，报告："Building QA plan for [N] stories in [scope]."

If a story file path is referenced but the file does not exist, note it as
MISSING and continue with the remaining stories. Do not fail the entire plan
for one missing file.
如果引用了故事文件路径但文件不存在，将其标记为
MISSING 并继续处理其余故事。不要因为
一个文件缺失而使整个计划失败。

---

## Phase 2: Load Inputs
## 阶段 2：加载输入

For each in-scope story file, read the full file and extract:
对于每个范围内的故事文件，读取完整文件并提取：

- **Story title** and story ID (from filename or header)
- **Story Type** field (if present in the file header — e.g., `Type: Logic`)
- **Acceptance criteria** — the complete numbered/bulleted list
- **Implementation files** — listed under "Files to Create / Modify" or similar
- **Engine notes** — any engine API warnings or version-specific notes
- **GDD reference** — the GDD path(s) cited
- **ADR reference** — the ADR(s) cited
- **Estimate** — hours or story points if present
- **Dependencies** — other stories this one depends on
- **故事标题**和故事 ID（来自文件名或标头）
- **故事类型**字段（如果文件标头中存在 — 例如 `Type: Logic`）
- **验收标准** — 完整的编号/项目符号列表
- **实现文件** — 列在 "Files to Create / Modify" 或类似项下
- **引擎备注** — 任何引擎 API 警告或特定版本的备注
- **GDD 引用** — 引用的 GDD 路径
- **ADR 引用** — 引用的 ADR
- **估算** — 如果存在，小时或故事点
- **依赖** — 此故事依赖的其他故事

After reading stories, load supporting context once (not per story):
读取故事后，加载一次支持性上下文（而非每个故事都加载）：

- `design/gdd/systems-index.md` — to understand system priorities and which
  GDDs are approved
- For each unique GDD referenced across all stories: read the
  **Acceptance Criteria**, **Formulas**, and **Edge Cases** sections. Do not load
  the full GDD text. These three sections contain the testable requirements, the math
  to verify, and the boundary conditions that tests must cover. If an Edge Cases
  section is absent from the GDD, note it per GDD: "No Edge Cases section found — edge
  case coverage will be inferred from acceptance criteria only."
- `docs/architecture/control-manifest.md` — scan for forbidden patterns that
  automated tests should guard against (if the file exists)
- `design/gdd/systems-index.md` — 了解系统优先级以及哪些
  GDD 已获批准
- 对于所有故事引用的每个唯一 GDD：读取
  **Acceptance Criteria**、**Formulas** 和 **Edge Cases** 章节。不要加载
  完整 GDD 文本。这三个章节包含可测试的需求、要验证的数学
  以及测试必须覆盖的边界条件。如果 GDD 中缺少 Edge Cases
  章节，按 GDD 注明："No Edge Cases section found — edge
  case coverage will be inferred from acceptance criteria only."
- `docs/architecture/control-manifest.md` — 扫描自动化测试应防范的
  禁止模式（如果文件存在）

If no GDD is referenced in a story, note it as a gap but do not block the plan.
The story will be classified using acceptance criteria alone.
如果故事中没有引用 GDD，将其标记为缺口但不阻塞计划。
该故事将仅使用验收标准进行分类。

---

## Phase 3: Classify Each Story
## 阶段 3：对每个故事分类

For each story, assign a Story Type:
对于每个故事，分配一个故事类型：

- **If the story already has a `Type:` field in its header**: accept it as-is. Do NOT re-classify or validate against the criteria below — the Type was set by lead-programmer at story creation and is authoritative. Record it as-is.
- **If the `Type:` field is missing**: infer the type from the acceptance criteria using the table below, and note in the report that the type was inferred (not declared). Flag this as a gap — the story should have its Type declared explicitly before implementation begins.
- **如果故事标头中已有 `Type:` 字段**：原样接受。不要根据以下标准重新分类或验证 — 该类型由 lead-programmer 在故事创建时设定且为权威。原样记录。
- **如果 `Type:` 字段缺失**：使用下表从验收标准推断类型，并在报告中注明该类型是推断的（非声明的）。将此标记为缺口 — 故事应在实现开始前明确声明其类型。

| Story Type | Classification Indicators |
|---|---|
| **Logic** | Acceptance criteria reference calculations, formulas, numerical thresholds, state transitions, AI decisions, data validation, buff/debuff stacking, economy transactions, or any testable computation |
| **Integration** | Criteria involve two or more systems interacting, signals or events propagating across system boundaries, save/load round-trips, network sync, or persistence |
| **Visual/Feel** | Criteria reference animation behaviour, VFX, shader output, "feels responsive", perceived timing, screen shake, particle effects, audio sync, or visual feedback quality |
| **UI** | Criteria reference menus, HUD elements, buttons, screens, dialogue boxes, inventory panels, tooltips, or any player-facing interface element |
| **Config/Data** | Changes are limited to balance tuning values, data files, or configuration — no new code logic is involved |
| 故事类型 | 分类指标 |
|---|---|
| **Logic** | 验收标准涉及计算、公式、数值阈值、状态转换、AI 决策、数据验证、增益/减益叠加、经济交易或任何可测试的计算 |
| **Integration** | 标准涉及两个或多个系统交互、信号或事件跨系统边界传播、存档/读档往返、网络同步或持久化 |
| **Visual/Feel** | 标准涉及动画行为、VFX、着色器输出、"感觉响应灵敏"、感知时序、屏幕震动、粒子效果、音频同步或视觉反馈质量 |
| **UI** | 标准涉及菜单、HUD 元素、按钮、屏幕、对话框、库存面板、工具提示或任何面向玩家的界面元素 |
| **Config/Data** | 变更仅限于平衡调优值、数据文件或配置 — 不涉及新的代码逻辑 |

**Mixed stories** (e.g., a story that adds both a formula and a UI display):
assign the primary type based on which acceptance criteria carry the highest
implementation risk, and note the secondary type. Mixed Logic+Integration or
Visual+UI combinations are the most common.
**混合故事**（例如，同时添加公式和 UI 显示的故事）：
根据哪个验收标准带来最高的实现风险来分配主要类型，并注明次要类型。Logic+Integration 或
Visual+UI 混合组合最为常见。

After classifying all stories, produce a classification summary table in
conversation before proceeding to Phase 4. This gives the user visibility into
how tests will be allocated.
对所有故事分类后，在继续阶段 4 之前在
对话中生成分类摘要表。这使用户能够了解
测试将如何分配。

---

## Phase 4: Generate Test Plan
## 阶段 4：生成测试计划

Assemble the full QA plan document. Use this structure:
组装完整的 QA 计划文档。使用此结构：

````markdown
# QA Plan: [Sprint/Feature Name]
**Date**: [date]
**Generated by**: /qa-plan
**Scope**: [N stories across [N systems]]
**Engine**: [engine name from .claude/docs/technical-preferences.md, or "Not configured"]
**Sprint File**: [path to sprint plan if applicable]

---

## Test Summary

| Story | Type | Automated Test Required | Manual Verification Required |
|-------|------|------------------------|------------------------------|
| [story title] | Logic | Unit test — `tests/unit/[system]/` | None |
| [story title] | Integration | Integration test — `tests/integration/[system]/` | Smoke check |
| [story title] | Visual/Feel | None (not automatable) | Screenshot + lead sign-off |
| [story title] | UI | Interaction walkthrough | Manual step-through |
| [story title] | Config/Data | Data validation test | Spot-check in-game values |

---

## Automated Tests Required

### [Story Title] — [Type]
**Test file path**: `tests/[unit|integration]/[system]/[story-slug]_test.[ext]`
**What to test**:
- [Specific formula or rule from the GDD Formulas section]
- [Each named state transition or decision branch]
- [Each side effect that should or should not occur]

**Edge cases to cover**:
- Zero/minimum input values (e.g., 0 damage, empty inventory)
- Maximum/boundary input values (e.g., max level, stat cap)
- Invalid or null input (e.g., missing target, dead entity)
- [Any edge case explicitly called out in the GDD Edge Cases section]

**Estimated test count**: ~[N] unit tests

[If no GDD formula reference was found for this story, note:]
*No formula found in referenced GDD — test cases must be derived from acceptance
criteria directly. Review the GDD Formulas section before writing tests.*

---

## Manual QA Checklist

### [Story Title] — [Type]
**Verification method**: [Screenshot + designer sign-off | Playtest session |
Manual step-through | Comparison against reference footage]
**Who must sign off**: [designer / lead-programmer / qa-lead / art-lead]
**Evidence to capture**: [screenshot of X | video clip of Y | written playtest
notes | side-by-side comparison]

Checklist:
- [ ] [Specific observable condition — concrete and falsifiable]
- [ ] [Another condition]
- [ ] [Every acceptance criterion translated into a manual check item]

*If any criterion uses subjective language ("feels", "looks", "seems"), it must
be supplemented with a specific benchmark or a playtest protocol note.*

---

## Smoke Test Scope

Critical paths to verify before any QA hand-off for this sprint:

1. Game launches to main menu without crash
2. New game / new session can be started
3. [Primary mechanic introduced or changed this sprint]
4. [Any system with a regression risk from this sprint's changes]
5. Save / load cycle completes without data loss (if save system exists)
6. Performance is within budget on target hardware (no new frame spikes)

*Smoke tests are verified by the developer via `/smoke-check`. Reference this
list when running that skill.*

---

## Playtest Requirements

| Story | Playtest Goal | Min Sessions | Target Player Type |
|-------|--------------|--------------|-------------------|
| [story] | [What question must the session answer?] | [N] | [new player / experienced] |

**Sign-off requirement**: Playtest notes must be written to
`production/session-logs/playtest-[sprint]-[story-slug].md` and reviewed by
the [designer / qa-lead] before the story can be marked COMPLETE.

If no stories require playtest validation: *No playtest sessions required for
this sprint.*

---

## Definition of Done — This Sprint

A story is DONE when ALL of the following are true:

- [ ] All acceptance criteria verified — via automated test result OR documented
      manual evidence (screenshot, video, or playtest notes with sign-off)
- [ ] Test file exists at the specified path for all Logic and Integration stories
- [ ] Manual evidence document exists for all Visual/Feel and UI stories
- [ ] Smoke check passes (run `/smoke-check sprint` before QA hand-off)
- [ ] No regressions introduced
- [ ] Code reviewed (via `/code-review` or documented peer review)
- [ ] Story file updated to `Status: Complete` (via `/story-done`)
````
````markdown
# QA Plan: [Sprint/Feature Name]
**Date**: [date]
**Generated by**: /qa-plan
**Scope**: [N stories across [N systems]]
**Engine**: [engine name from .claude/docs/technical-preferences.md, or "Not configured"]
**Sprint File**: [path to sprint plan if applicable]

---

## Test Summary

| Story | Type | Automated Test Required | Manual Verification Required |
|-------|------|------------------------|------------------------------|
| [story title] | Logic | Unit test — `tests/unit/[system]/` | None |
| [story title] | Integration | Integration test — `tests/integration/[system]/` | Smoke check |
| [story title] | Visual/Feel | None (not automatable) | Screenshot + lead sign-off |
| [story title] | UI | Interaction walkthrough | Manual step-through |
| [story title] | Config/Data | Data validation test | Spot-check in-game values |

---

## Automated Tests Required

### [Story Title] — [Type]
**Test file path**: `tests/[unit|integration]/[system]/[story-slug]_test.[ext]`
**What to test**:
- [Specific formula or rule from the GDD Formulas section]
- [Each named state transition or decision branch]
- [Each side effect that should or should not occur]

**Edge cases to cover**:
- Zero/minimum input values (e.g., 0 damage, empty inventory)
- Maximum/boundary input values (e.g., max level, stat cap)
- Invalid or null input (e.g., missing target, dead entity)
- [Any edge case explicitly called out in the GDD Edge Cases section]

**Estimated test count**: ~[N] unit tests

[If no GDD formula reference was found for this story, note:]
*No formula found in referenced GDD — test cases must be derived from acceptance
criteria directly. Review the GDD Formulas section before writing tests.*

---

## Manual QA Checklist

### [Story Title] — [Type]
**Verification method**: [Screenshot + designer sign-off | Playtest session |
Manual step-through | Comparison against reference footage]
**Who must sign off**: [designer / lead-programmer / qa-lead / art-lead]
**Evidence to capture**: [screenshot of X | video clip of Y | written playtest
notes | side-by-side comparison]

Checklist:
- [ ] [Specific observable condition — concrete and falsifiable]
- [ ] [Another condition]
- [ ] [Every acceptance criterion translated into a manual check item]

*If any criterion uses subjective language ("feels", "looks", "seems"), it must
be supplemented with a specific benchmark or a playtest protocol note.*

---

## Smoke Test Scope

Critical paths to verify before any QA hand-off for this sprint:

1. Game launches to main menu without crash
2. New game / new session can be started
3. [Primary mechanic introduced or changed this sprint]
4. [Any system with a regression risk from this sprint's changes]
5. Save / load cycle completes without data loss (if save system exists)
6. Performance is within budget on target hardware (no new frame spikes)

*Smoke tests are verified by the developer via `/smoke-check`. Reference this
list when running that skill.*

---

## Playtest Requirements

| Story | Playtest Goal | Min Sessions | Target Player Type |
|-------|--------------|--------------|-------------------|
| [story] | [What question must the session answer?] | [N] | [new player / experienced] |

**Sign-off requirement**: Playtest notes must be written to
`production/session-logs/playtest-[sprint]-[story-slug].md` and reviewed by
the [designer / qa-lead] before the story can be marked COMPLETE.

If no stories require playtest validation: *No playtest sessions required for
this sprint.*

---

## Definition of Done — This Sprint

A story is DONE when ALL of the following are true:

- [ ] All acceptance criteria verified — via automated test result OR documented
      manual evidence (screenshot, video, or playtest notes with sign-off)
- [ ] Test file exists at the specified path for all Logic and Integration stories
- [ ] Manual evidence document exists for all Visual/Feel and UI stories
- [ ] Smoke check passes (run `/smoke-check sprint` before QA hand-off)
- [ ] No regressions introduced
- [ ] Code reviewed (via `/code-review` or documented peer review)
- [ ] Story file updated to `Status: Complete` (via `/story-done`)
````

When generating content, use the actual story titles, GDD formula text, and
acceptance criteria extracted in Phase 2. Do not use placeholder text — every
test entry should reflect the real requirements of these specific stories.
生成内容时，使用阶段 2 中提取的实际故事标题、GDD 公式文本和
验收标准。不要使用占位符文本 — 每个
测试条目都应反映这些特定故事的真实需求。

---

## Phase 5: Write Output
## 阶段 5：写入输出

Show the complete plan in conversation (or a summary if the plan is very long),
then ask two questions together using `AskUserQuestion`:
在对话中展示完整计划（如果计划很长则展示摘要），
然后使用 `AskUserQuestion` 一起询问两个问题：

```
question: "Ready to write the QA plan. Choose output options:"
multiSelect: true
options:
  - "Write QA plan to production/qa/qa-plan-[sprint-slug]-[date].md"
  - "Also back-fill test case specs into each story file's ## QA Test Cases section (Recommended — enables /dev-story and /code-review traceability)"
```
```
question: "Ready to write the QA plan. Choose output options:"
multiSelect: true
options:
  - "Write QA plan to production/qa/qa-plan-[sprint-slug]-[date].md"
  - "Also back-fill test case specs into each story file's ## QA Test Cases section (Recommended — enables /dev-story and /code-review traceability)"
```

If "Write QA plan" is selected: write the plan file exactly as generated — do not truncate.
如果选择 "Write QA plan"：按生成的原样写入计划文件 — 不要截断。

If "Also back-fill story files" is selected: for each Logic and Integration story in scope, edit the story file at its path. Find the `## QA Test Cases` section and replace its content with the test case specs generated in Phase 4 for that story. If a story has no `## QA Test Cases` section, append it before `## Test Evidence`. For Visual/Feel and UI stories, write the manual verification steps instead of test specs.
如果选择 "Also back-fill story files"：对于范围内的每个 Logic 和 Integration 故事，编辑其路径下的故事文件。找到 `## QA Test Cases` 章节并用阶段 4 中为该故事生成的测试用例规范替换其内容。如果故事没有 `## QA Test Cases` 章节，将其追加到 `## Test Evidence` 之前。对于 Visual/Feel 和 UI 故事，改写手动验证步骤而非测试规范。

After writing:
写入后：

"QA plan written to `production/qa/qa-plan-[sprint-slug]-[date].md`.
"QA plan written to `production/qa/qa-plan-[sprint-slug]-[date].md`.

Next steps:
- Share this plan with the team before sprint implementation begins
- Once all sprint stories are implemented, run `/smoke-check sprint` to gate QA hand-off — not yet, only after implementation is complete
- For Logic/Integration stories, create the test files at the listed paths
  before marking stories done — `/story-done` checks for them"
后续步骤：
- 在冲刺实现开始前与团队分享此计划
- 一旦所有冲刺故事实现完成，运行 `/smoke-check sprint` 来门控 QA 移交 — 现在还不行，仅在实现完成后
- 对于 Logic/Integration 故事，在标记故事完成之前在列出的路径创建测试文件
  — `/story-done` 会检查它们"

Silently append to `production/session-state/active.md` (create the file if it does not exist):
静默追加到 `production/session-state/active.md`（如果文件不存在则创建）：

```
<!-- QA-PLAN: [date] | System: [system/sprint identifier] | Plan written: production/qa/qa-plan-[identifier]-[date].md -->
```
```
<!-- QA-PLAN: [date] | System: [system/sprint identifier] | Plan written: production/qa/qa-plan-[identifier]-[date].md -->
```

---

## Collaborative Protocol
## 协作协议

- **Never write the plan without asking** — Phase 5 requires explicit approval.
- **Classify conservatively**: when a story is ambiguous between Logic and
  Integration, classify it as Integration — it requires both unit and
  integration tests.
- **Do not invent test cases** beyond what acceptance criteria and GDD formulas
  support. If a formula is absent from the GDD, flag it rather than guessing.
- **Playtest requirements are advisory**: the user decides whether a playtest
  is warranted for borderline Visual/Feel stories. Flag the case; do not mandate.
- Use `AskUserQuestion` for scope selection when no argument is provided.
  Keep all other phases non-interactive — present findings, then ask once to
  approve the write.
- **切勿未经询问就写入计划** — 阶段 5 要求明确批准。
- **保守分类**：当故事在 Logic 和
  Integration 之间模棱两可时，将其分类为 Integration — 它需要单元测试和
  集成测试。
- **不要凭空捏造测试用例**，超出验收标准和 GDD 公式
  所支持的范围。如果 GDD 中缺少公式，标记它而非猜测。
- **试玩要求是建议性的**：由用户决定是否值得为
  边缘的 Visual/Feel 故事进行试玩。标记情况；不要强制要求。
- 未提供参数时使用 `AskUserQuestion` 进行范围选择。
  保持所有其他阶段非交互式 — 呈现发现，然后询问一次以
  批准写入。
