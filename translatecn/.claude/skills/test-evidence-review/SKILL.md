---
name: test-evidence-review
description: "Quality review of test files and manual evidence documents. Goes beyond existence checks — evaluates assertion coverage, edge case handling, naming conventions, and evidence completeness. Produces ADEQUATE/INCOMPLETE/MISSING verdict per story. Run before QA sign-off or on demand."
argument-hint: "[story-path | sprint | system-name]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---
---
名称：test-evidence-review
描述："对测试文件和手动证据文档进行质量评审。超越存在性检查 —— 评估断言覆盖、边界情况处理、命名约定与证据完整性。为每个故事产出 ADEQUATE/INCOMPLETE/MISSING 结论。在 QA 签收之前或按需运行。"
argument-hint："[story-path | sprint | system-name]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write
model：sonnet
---

# Test Evidence Review
# 测试证据评审

`/smoke-check` verifies that test files **exist** and **pass**. This skill
goes further — it reviews the **quality** of those tests and evidence documents.
A test file that exists and passes may still leave critical behaviour uncovered.
A manual evidence doc that exists may lack the sign-offs required for closure.
`/smoke-check` 验证测试文件**存在**且**通过**。本技能走得更远 —— 它评审这些
测试和证据文档的**质量**。一个存在并通过的测试文件可能仍未覆盖关键行为。
一个存在的手动证据文档可能缺乏完成关闭所需的签收。

**Output:** Summary report (in conversation) + optional `production/qa/evidence-review-[date].md`
**产出：** 摘要报告（在对话中）+ 可选的 `production/qa/evidence-review-[date].md`

**When to run:**
- Before QA hand-off sign-off (`/team-qa` Phase 5)
- On any story where test quality is in question
- As part of milestone review for Logic and Integration story quality audit
**何时运行：**
- 在 QA 交接签收之前（`/team-qa` 阶段 5）
- 对任何测试质量存疑的故事
- 作为里程碑评审的一部分，对 Logic 和 Integration 故事做质量审计

---

## 1. Parse Arguments
## 1. 解析参数

**Modes:**
- `/test-evidence-review [story-path]` — review a single story's evidence
- `/test-evidence-review sprint` — review all stories in the current sprint
- `/test-evidence-review [system-name]` — review all stories in an epic/system
- No argument — ask which scope: "Single story", "Current sprint", "A system"
**模式：**
- `/test-evidence-review [story-path]` —— 评审单个故事的证据
- `/test-evidence-review sprint` —— 评审当前冲刺中所有故事
- `/test-evidence-review [system-name]` —— 评审一个史诗/系统中的所有故事
- 无参数 —— 询问范围："Single story"、"Current sprint"、"A system"

---

## 2. Load Stories in Scope
## 2. 加载范围内的故事

Based on the argument:
基于参数：

**Single story**: Read the story file directly. Extract: Story Type, Test
Evidence section, story slug, system name.
**单故事**：直接读取故事文件。提取：Story Type、Test Evidence 段落、
story slug、系统名。

**Sprint**: Read the most recently modified file in `production/sprints/`.
Extract the list of story file paths from the sprint plan. Read each story file.
**冲刺**：读取 `production/sprints/` 中最近修改的文件。从冲刺计划提取故事文件
路径列表。读取每个故事文件。

**System**: Glob `production/epics/[system-name]/story-*.md`. Read each.
**系统**：Glob `production/epics/[system-name]/story-*.md`。读取每个。

For each story, collect:
- `Type:` field (Logic / Integration / Visual/Feel / UI / Config/Data)
- `## Test Evidence` section — the stated expected test file path or evidence doc
- Story slug (from file name)
- System name (from directory path)
- Acceptance Criteria list (all checkbox items)
对每个故事，收集：
- `Type:` 字段（Logic / Integration / Visual/Feel / UI / Config/Data）
- `## Test Evidence` 段落 —— 声明的预期测试文件路径或证据文档
- Story slug（来自文件名）
- 系统名（来自目录路径）
- Acceptance Criteria 列表（所有复选框项）

---

## 3. Locate Evidence Files
## 3. 定位证据文件

For each story, find the evidence:
对每个故事，查找证据：

**Logic stories**: Glob `tests/unit/[system]/[story-slug]_test.*`
  - If not found, also try: Grep in `tests/unit/[system]/` for files
    containing the story slug
**Logic 故事**：Glob `tests/unit/[system]/[story-slug]_test.*`
  - 若未找到，也尝试：在 `tests/unit/[system]/` 中 Grep 包含 story slug 的文件

**Integration stories**: Glob `tests/integration/[system]/[story-slug]_test.*`
  - Also check `production/session-logs/` for playtest records mentioning the story
**Integration 故事**：Glob `tests/integration/[system]/[story-slug]_test.*`
  - 同时检查 `production/session-logs/` 中提及该故事的试玩记录

**Visual/Feel and UI stories**: Glob `production/qa/evidence/[story-slug]-evidence.*`
**Visual/Feel 与 UI 故事**：Glob `production/qa/evidence/[story-slug]-evidence.*`

**Config/Data stories**: Glob `production/qa/smoke-*.md` (any smoke check report)
**Config/Data 故事**：Glob `production/qa/smoke-*.md`（任意 smoke 检查报告）

Note what was found (path) or not found (gap) for each story.
为每个故事记录找到的内容（路径）或未找到的内容（缺漏）。

---

## 4. Review Automated Test Quality (Logic / Integration)
## 4. 评审自动化测试质量（Logic / Integration）

For each test file found, read it and evaluate:
对找到的每个测试文件，读取并评估：

### Assertion coverage
### 断言覆盖

Count the number of distinct assertions (lines containing assert, expect,
check, verify, or engine-specific assertion patterns). Low assertion count is
a quality signal — a test that makes only 1 assertion per test function may
not cover the range of expected behaviour.
统计不同断言数量（包含 assert、expect、check、verify 或引擎特定断言模式的行）。
低断言数是质量信号 —— 一个测试函数只做 1 个断言的测试可能未覆盖预期行为的范围。

Thresholds:
- **3+ assertions per test function** → normal
- **1-2 assertions per test function** → note as potentially thin
- **0 assertions** (test exists but no asserts) → flag as BLOCKING — the
  test passes vacuously and proves nothing
阈值：
- **每个测试函数 3+ 断言** → 正常
- **每个测试函数 1-2 断言** → 注为可能单薄
- **0 断言**（测试存在但无 assert）→ 标记为 BLOCKING —— 测试空泛通过、未证明
  任何事

### Edge case coverage
### 边界情况覆盖

For each acceptance criterion in the story that contains a number, threshold,
or "when X happens" conditional: check whether a test function name or
test body references that specific case.
对故事中包含数字、阈值或 "when X happens" 条件的每个验收标准：检查是否有
测试函数名或测试体引用该具体情形。

Heuristics:
- Grep test file for "zero", "max", "null", "empty", "min", "invalid",
  "boundary", "edge" — presence of any is a positive signal
- If the story has a Formulas section with specific bounds: check whether
  tests exercise at minimum/maximum values
启发式：
- 在测试文件中 Grep "zero"、"max"、"null"、"empty"、"min"、"invalid"、
  "boundary"、"edge" —— 任一存在都是积极信号
- 若故事有带具体边界的 Formulas 段落：检查测试是否在最小/最大值处演练

### Naming quality
### 命名质量

Test function names should describe: the scenario + the expected result.
Pattern: `test_[scenario]_[expected_outcome]`
测试函数名应描述：场景 + 预期结果。
模式：`test_[scenario]_[expected_outcome]`

Flag functions named generically (`test_1`, `test_run`, `testBasic`) as
**naming issues** — they make failures harder to diagnose.
将通用命名（`test_1`、`test_run`、`testBasic`）的函数标记为 **命名问题** ——
它们使失败更难诊断。

### Formula traceability
### 公式可追溯性

For Logic stories where the GDD has a Formulas section: check that the test
file contains at least one test whose name or comment references the formula
name or a formula value. A test that exercises a formula without mentioning
it by name is harder to maintain when the formula changes.
对于 GDD 含 Formulas 段落的 Logic 故事：检查测试文件是否包含至少一个
名称或注释引用公式名或公式值的测试。一个使用公式但未提及公式名的测试，在
公式变更时更难维护。

---

## 5. Review Manual Evidence Quality (Visual/Feel / UI)
## 5. 评审手动证据质量（Visual/Feel / UI）

For each evidence document found, read it and evaluate:
对找到的每个证据文档，读取并评估：

### Criterion linkage
### 标准关联

The evidence doc should reference each acceptance criterion from the story.
Check: does the evidence doc contain each criterion (or a clear rephrasing)?
Missing criteria mean a criterion was never verified.
证据文档应引用故事的每个验收标准。检查：证据文档是否包含每个标准
（或清晰改写）？缺失的标准意味着该标准从未被验证。

### Sign-off completeness
### 签收完整性

Check for three sign-off lines (or equivalent fields):
- Developer sign-off
- Designer / art-lead sign-off (for Visual/Feel)
- QA lead sign-off
检查三行签收（或等价字段）：
- 开发者签收
- 设计师 / 美术主管签收（针对 Visual/Feel）
- QA 主管签收

If any are missing or blank: flag as INCOMPLETE — the story cannot be fully
closed without all required sign-offs.
若任一缺失或为空：标记为 INCOMPLETE —— 没有所有必需签收，故事无法完整关闭。

### Screenshot / artefact completeness
### 截图 / 产物完整性

For Visual/Feel stories: check whether screenshot file paths are referenced
in the evidence doc. If referenced, Glob for them to confirm they exist.
对于 Visual/Feel 故事：检查证据文档是否引用了截图文件路径。若引用，Glob 确认
它们存在。

For UI stories: check whether a walkthrough sequence (step-by-step interaction
log) is present.
对于 UI 故事：检查是否有 walkthrough 序列（逐步交互日志）。

### Date coverage
### 日期覆盖

Evidence doc should have a date. If the date is earlier than the story's
last major change (heuristic: compare against sprint start date from the sprint
plan), flag as POTENTIALLY STALE — the evidence may not cover the final
implementation.
证据文档应有日期。若日期早于故事的最后一次重大变更（启发式：与冲刺计划中的
冲刺开始日期比较），标记为 POTENTIALLY STALE —— 证据可能未覆盖最终实现。

---

## 6. Build the Review Report
## 6. 构建评审报告

For each story, assign a verdict:
对每个故事，赋予结论：

| Verdict | Meaning |
|---------|---------|
| **ADEQUATE** | Test/evidence exists, passes quality checks, all criteria covered |
| **INCOMPLETE** | Test/evidence exists but has quality gaps (thin assertions, missing sign-offs) |
| **MISSING** | No test or evidence found for a story type that requires it |
| 结论 | 含义 |
|---------|---------|
| **ADEQUATE** | 测试/证据存在，通过质量检查，所有标准已覆盖 |
| **INCOMPLETE** | 测试/证据存在但有质量缺漏（断言单薄、签收缺失） |
| **MISSING** | 对于需要测试/证据的故事类型，未找到任何测试或证据 |

The overall sprint/system verdict is the worst story verdict present.
整体冲刺/系统结论是当前最差的故事结论。

```markdown
## Test Evidence Review

> **Date**: [date]
> **Scope**: [single story path | Sprint [N] | [system name]]
> **Stories reviewed**: [N]
> **Overall verdict**: ADEQUATE / INCOMPLETE / MISSING

---

### Story-by-Story Results

#### [Story Title] — [Type] — [ADEQUATE/INCOMPLETE/MISSING]

**Test/evidence path**: `[path]` (found) / (not found)

**Automated test quality** *(Logic/Integration only)*:
- Assertion coverage: [N per function on average] — [adequate / thin / none]
- Edge cases: [covered / partial / not found]
- Naming: [consistent / [N] generic names flagged]
- Formula traceability: [yes / no — formula names not referenced in tests]

**Manual evidence quality** *(Visual/Feel/UI only)*:
- Criterion linkage: [N/M criteria referenced]
- Sign-offs: [Developer ✓ | Designer ✗ | QA Lead ✗]
- Artefacts: [screenshots present / missing / N/A]
- Freshness: [dated [date] — current / potentially stale]

**Issues**:
- BLOCKING: [description] *(prevents story-done)*
- ADVISORY: [description] *(should fix before release)*

---

### Summary

| Story | Type | Verdict | Issues |
|-------|------|---------|--------|
| [title] | Logic | ADEQUATE | None |
| [title] | Integration | INCOMPLETE | Thin assertions (avg 1.2/function) |
| [title] | Visual/Feel | INCOMPLETE | QA lead sign-off missing |
| [title] | Logic | MISSING | No test file found |

**BLOCKING items** (must resolve before story can be closed): [N]
**ADVISORY items** (should address before release): [N]
```

---

## 7. Write Output (Optional)
## 7. 写入输出（可选）

Present the report in conversation.
在对话中展示报告。

Ask: "May I write this test evidence review to
`production/qa/evidence-review-[date].md`?"
询问："May I write this test evidence review to
`production/qa/evidence-review-[date].md`?"

This is optional — the report is useful standalone. Write only if the user
wants a persistent record.
这是可选的 —— 报告可独立使用。仅当用户需要持久化记录时写入。

After the report:
报告之后：

- For BLOCKING items: "These must be resolved before `/story-done` can mark the
  story Complete. Would you like to address any of them now?"
- For thin assertions: "Consider running `/test-helpers [system]` to see
  scaffolded assertion patterns for common cases."
- For missing sign-offs: "Manual sign-off is required from [role]. Share
  `[evidence-path]` with them to complete sign-off."
- 对于 BLOCKING 项："These must be resolved before `/story-done` can mark the
  story Complete. Would you like to address any of them now?"
- 对于断言单薄："Consider running `/test-helpers [system]` to see
  scaffolded assertion patterns for common cases."
- 对于签收缺失："Manual sign-off is required from [role]. Share
  `[evidence-path]` with them to complete sign-off."

Verdict: **COMPLETE** — evidence review finished. Use CONCERNS if BLOCKING items were found.
结论：**COMPLETE** —— 证据评审已完成。若发现 BLOCKING 项则使用 CONCERNS。

---

## Collaborative Protocol
## 协作协议

- **Report quality issues, do not fix them** — this skill reads and evaluates;
  it does not modify test files or evidence documents
- **ADEQUATE means adequate for shipping, not perfect** — avoid nitpicking
  tests that are functioning and comprehensive enough to give confidence
- **BLOCKING vs. ADVISORY distinction is important** — only flag BLOCKING when
  the gap leaves a story criterion genuinely unverified
- **Ask before writing** — the report file is optional; always confirm before writing
- **报告质量问题，不修复它们** —— 本技能读取并评估；不修改测试文件或证据文档
- **ADEQUATE 指可发行级别足够，而非完美** —— 不要对功能正常且足够全面的测试
  吹毛求疵
- **BLOCKING 与 ADVISORY 的区分很重要** —— 仅当缺漏导致某条故事标准真正
  未被验证时才标记 BLOCKING
- **写入前询问** —— 报告文件可选；始终在写入前确认
