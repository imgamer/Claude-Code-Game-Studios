---
name: regression-suite
description: "Map test coverage to GDD critical paths, identify fixed bugs without regression tests, flag coverage drift from new features, and maintain tests/regression-suite.md. Run after implementing a bug fix or before a release gate."
argument-hint: "[update | audit | report]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion
model: sonnet
---
---
名称：regression-suite
描述："将测试覆盖映射到 GDD 关键路径，识别没有回归测试的已修复 bug，标记新功能的覆盖漂移，并维护 tests/regression-suite.md。在实施 bug 修复后或发布门禁前运行。"
argument-hint："[update | audit | report]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, AskUserQuestion
model：sonnet
---

# Regression Suite
# 回归套件

This skill ensures that every bug fix is backed by a test that would have
caught the original bug — and that the regression suite stays current as the
game evolves. It also detects when new features have been added without
corresponding regression coverage.
此技能确保每个 bug 修复都有本可捕获原始 bug 的测试支持 —— 并确保回归套件随游戏演进保持最新。它还检测何时添加了新功能却没有相应的回归覆盖。

A regression suite is not a new test category — it is a **curated list of
tests already in `tests/`** that collectively cover the game's critical paths
and known failure points. This skill maintains that list.
回归套件不是新的测试类别 —— 它是**已在 `tests/` 中的测试的精选列表**，共同覆盖游戏的关键路径和已知失败点。此技能维护该列表。

**Output:** `tests/regression-suite.md`
**输出：** `tests/regression-suite.md`

**When to run:**
**何时运行：**
- After fixing a bug (confirm a regression test was written or identify gap)
- 修复 bug 后（确认已编写回归测试或识别缺口）
- Before a release gate (`/gate-check polish` requires regression suite exists)
- 发布门禁前（`/gate-check polish` 要求回归套件存在）
- As part of sprint close to detect coverage drift
- 作为冲刺收尾的一部分以检测覆盖漂移

---

## 1. Parse Arguments
## 1. 解析参数

**Modes:**
**模式：**
- `/regression-suite update` — scan new bug fixes this sprint and check
  for regression test presence; add new tests to the suite manifest
- `/regression-suite update` —— 扫描本冲刺的新 bug 修复并检查回归测试存在性；将新测试添加到套件清单
- `/regression-suite audit` — full audit of all GDD critical paths vs.
  existing test coverage; flag paths with no regression test
- `/regression-suite audit` —— 完整审计所有 GDD 关键路径 vs 现有测试覆盖；标记无回归测试的路径
- `/regression-suite report` — read-only status report (no writes); suitable
  for sprint reviews
- `/regression-suite report` —— 只读状态报告（不写入）；适合冲刺评审
- No argument — if a sprint is clearly active (sprint plan exists with in-progress stories), run `update`. If ambiguous or no active sprint is detected, use `AskUserQuestion`:
- 无参数 —— 若冲刺明显活跃（冲刺计划存在且故事进行中），运行 `update`。若不明确或未检测到活跃冲刺，使用 `AskUserQuestion`：
  - Prompt: "No subcommand specified. Which mode do you want to run?"
  - 提示："未指定子命令。您要运行哪个模式？"
  - Options:
  - 选项：
    - `[A] update — scan new bug fixes this sprint and add missing regression tests`
    - `[A] update —— 扫描本冲刺的新 bug 修复并添加缺失的回归测试`
    - `[B] audit — full audit of all GDD critical paths vs. existing test coverage`
    - `[B] audit —— 完整审计所有 GDD 关键路径 vs 现有测试覆盖`
    - `[C] report — read-only status report (no writes)`
    - `[C] report —— 只读状态报告（不写入）`

---

## 2. Load Context
## 2. 加载上下文

### Step 2a — Load existing regression suite
### 步骤 2a —— 加载现有回归套件

Read `tests/regression-suite.md` if it exists. Extract:
若存在，读取 `tests/regression-suite.md`。提取：
- Total registered regression tests
- 已注册的回归测试总数
- Last updated date
- 最后更新日期
- Any tests flagged as `STALE` or `QUARANTINED`
- 任何标记为 `STALE` 或 `QUARANTINED` 的测试

If it does not exist: note "No regression suite found — will create one."
若不存在：注明"未找到回归套件 —— 将创建一个。"

### Step 2b — Load test inventory
### 步骤 2b —— 加载测试清单

Glob all test files:
Glob 所有测试文件：
```
tests/unit/**/*_test.*
tests/integration/**/*_test.*
tests/regression/**/*
```

For each file, note the system (from directory path) and file name.
Do not read test file contents unless needed for name-to-test mapping.
对每个文件，注明系统（来自目录路径）和文件名。
除非需要名称到测试的映射，否则不要读取测试文件内容。

### Step 2c — Load GDD critical paths
### 步骤 2c —— 加载 GDD 关键路径

For `audit` mode: read `design/gdd/systems-index.md` to get all systems.
For each MVP-tier system, read its GDD and extract:
对于 `audit` 模式：读取 `design/gdd/systems-index.md` 获取所有系统。
对于每个 MVP 层级系统，读取其 GDD 并提取：
- Acceptance Criteria (these define the critical paths)
- 验收标准（这些定义关键路径）
- Formulas section (formulas must have regression tests)
- 公式章节（公式必须有回归测试）
- Edge Cases section (known edge cases should have regression tests)
- 边界情况章节（已知边界情况应有回归测试）

For `update` mode: skip full GDD scan. Instead read the current sprint plan
and story files to find stories with Status: Complete this sprint.
对于 `update` 模式：跳过完整 GDD 扫描。改为读取当前冲刺计划和故事文件以查找本冲刺 Status: Complete 的故事。

### Step 2d — Load closed bugs
### 步骤 2d —— 加载已关闭的 bug

Glob `production/qa/bugs/*.md` and filter for bugs with a `Status: Closed`
or `Status: Fixed` field. Note:
Glob `production/qa/bugs/*.md` 并筛选具有 `Status: Closed` 或 `Status: Fixed` 字段的 bug。注明：
- Which story or system the bug was in
- bug 所在的故事或系统
- Whether a regression test was mentioned in the fix description
- 修复描述中是否提及回归测试

---

## 3. Map Coverage — Critical Paths
## 3. 映射覆盖 —— 关键路径

For `audit` mode only:
仅对于 `audit` 模式：

For each GDD acceptance criterion, determine whether a test exists:
对每个 GDD 验收标准，确定是否存在测试：

1. Grep `tests/unit/[system]/` and `tests/integration/[system]/` for file names
   and function names related to the criterion's key noun/verb
1. Grep `tests/unit/[system]/` 和 `tests/integration/[system]/` 以查找与标准关键名词/动词相关的文件名和函数名
2. Assign coverage:
2. 分配覆盖：

| Status | Meaning |
|--------|---------|
| **COVERED** | A test file exists that targets this criterion's logic |
| **PARTIAL** | A test exists but doesn't cover all cases (e.g. happy path only) |
| **MISSING** | No test found for this critical path |
| **EXEMPT** | Visual/Feel or UI criterion — not automatable by design |
| 状态 | 含义 |
|--------|---------|
| **COVERED** | 存在针对此标准逻辑的测试文件 |
| **PARTIAL** | 测试存在但未覆盖所有情况（例如仅快乐路径） |
| **MISSING** | 未找到此关键路径的测试 |
| **EXEMPT** | Visual/Feel 或 UI 标准 —— 设计上不可自动化 |

3. Elevate MISSING items that correspond to formulas or state machines to
   **HIGH PRIORITY** gap — these are the most likely regression sources.
3. 将对应于公式或状态机的 MISSING 项提升为 **HIGH PRIORITY** 缺口 —— 这些是最可能的回归源。

---

## 4. Map Coverage — Fixed Bugs
## 4. 映射覆盖 —— 已修复的 bug

For each closed bug:
对每个已关闭的 bug：

1. Extract the system slug from the bug's metadata
1. 从 bug 的元数据中提取系统 slug
2. Grep `tests/unit/[system]/` and `tests/integration/[system]/` for a test
   that references the bug ID or the specific failure scenario
2. Grep `tests/unit/[system]/` 和 `tests/integration/[system]/` 以查找引用 bug ID 或特定失败场景的测试
3. Assign:
3. 分配：
   - **HAS REGRESSION TEST** — a test was found that would catch this bug
   - **HAS REGRESSION TEST** —— 找到本可捕获此 bug 的测试
   - **MISSING REGRESSION TEST** — bug was fixed but no test guards against recurrence
   - **MISSING REGRESSION TEST** —— bug 已修复但没有防止复发的测试

For MISSING REGRESSION TEST items:
对于 MISSING REGRESSION TEST 项：
- Flag them as regression gaps
- 将它们标记为回归缺口
- Suggest the test file path: `tests/unit/[system]/[bug-slug]_regression_test.[ext]`
- 建议测试文件路径：`tests/unit/[system]/[bug-slug]_regression_test.[ext]`
- Note: "Without this test, this bug can silently return in a future sprint."
- 注明："没有此测试，此 bug 可能在未来冲刺中静默回归。"

---

## 5. Detect Coverage Drift
## 5. 检测覆盖漂移

Coverage drift occurs when the game grows but the regression suite doesn't.
覆盖漂移发生在游戏增长但回归套件未增长时。

Check for drift indicators:
检查漂移指标：
- Stories completed this sprint with no corresponding test files in `tests/`
- 本冲刺完成的故事在 `tests/` 中没有对应的测试文件
- New systems added to `systems-index.md` since the last regression-suite update
- 自上次回归套件更新以来添加到 `systems-index.md` 的新系统
- GDD sections added or revised since the regression suite was last updated
  (use Grep on GDD file modification hints if available, or ask the user)
- 自回归套件上次更新以来添加或修订的 GDD 章节（若可用则对 GDD 文件修改提示使用 Grep，或询问用户）
- `tests/regression-suite.md` last-updated date vs. current date — if gap >
  2 sprints, flag as likely stale
- `tests/regression-suite.md` 最后更新日期 vs 当前日期 —— 若差距 > 2 个冲刺，标记为可能过时

---

## 6. Generate Report and Suite Manifest
## 6. 生成报告和套件清单

### Report format (in conversation)
### 报告格式（对话内）

```
## Regression Suite Status

**Mode**: [update | audit | report]
**Existing registered tests**: [N]
**Test files scanned**: [N]

### Critical Path Coverage (audit mode only)
| System | Total ACs | Covered | Partial | Missing | Exempt |
|--------|-----------|---------|---------|---------|--------|
| [name] | [N] | [N] | [N] | [N] | [N] |

**Coverage rate (non-exempt)**: [N]%

### Bug Regression Coverage
| Bug ID | System | Severity | Has Regression Test? |
|--------|--------|----------|----------------------|
| BUG-NNN | [system] | S[N] | YES / NO ⚠ |

**Bugs without regression tests**: [N]

### Coverage Drift Indicators
[List new systems or stories with no test coverage, or "None detected."]

### Recommended New Regression Tests
| Priority | System | Suggested Test File | Covers |
|----------|--------|---------------------|--------|
| HIGH | [system] | `tests/unit/[system]/[slug]_regression_test.[ext]` | BUG-NNN / AC-[N] |
| MEDIUM | [system] | `tests/unit/[system]/[slug]_test.[ext]` | [criterion] |
```
```
## 回归套件状态

**模式**：[update | audit | report]
**现有已注册测试**：[N]
**已扫描测试文件**：[N]

### 关键路径覆盖（仅 audit 模式）
| 系统 | 总 AC | 已覆盖 | 部分 | 缺失 | 豁免 |
|--------|-----------|---------|---------|---------|--------|
| [name] | [N] | [N] | [N] | [N] | [N] |

**覆盖率（非豁免）**：[N]%

### Bug 回归覆盖
| Bug ID | 系统 | 严重性 | 有回归测试？ |
|--------|--------|----------|----------------------|
| BUG-NNN | [system] | S[N] | YES / NO ⚠ |

**无回归测试的 bug**：[N]

### 覆盖漂移指标
[列出无测试覆盖的新系统或故事，或 "None detected."]

### 建议的新回归测试
| 优先级 | 系统 | 建议测试文件 | 覆盖 |
|----------|--------|---------------------|--------|
| HIGH | [system] | `tests/unit/[system]/[slug]_regression_test.[ext]` | BUG-NNN / AC-[N] |
| MEDIUM | [system] | `tests/unit/[system]/[slug]_test.[ext]` | [标准] |
```

### Suite manifest format (`tests/regression-suite.md`)
### 套件清单格式（`tests/regression-suite.md`）

The manifest is a curated index — not the tests themselves, but a registry
of which tests should always pass before a release:
清单是精选索引 —— 不是测试本身，而是哪些测试在发布前应始终通过的注册表：

```markdown
# Regression Suite Manifest

> Last Updated: [date]
> Total registered tests: [N]
> Coverage: [N]% of GDD critical paths

## How to run

[Engine-specific command to run all regression tests]

## Registered Regression Tests

### [System Name]

| Test File | Test Function (if known) | Covers | Added |
|-----------|--------------------------|--------|-------|
| `tests/unit/[system]/[file]_test.[ext]` | `test_[scenario]` | AC-N / BUG-NNN | [date] |

## Known Gaps

Tests that should exist but don't yet:

| Priority | System | Suggested Path | Covers | Reason Not Yet Written |
|----------|--------|----------------|--------|------------------------|
| HIGH | [system] | `tests/unit/[system]/[path]` | BUG-NNN | Bug fixed without test |

## Quarantined Tests

Tests that are flaky or disabled (do not run in CI):

| Test File | Function | Reason | Quarantined Since |
|-----------|----------|--------|-------------------|
| (none) | | | |
```
```markdown
# 回归套件清单

> 最后更新：[date]
> 已注册测试总数：[N]
> 覆盖：GDD 关键路径的 [N]%

## 如何运行

[运行所有回归测试的引擎特定命令]

## 已注册的回归测试

### [系统名]

| 测试文件 | 测试函数（若已知） | 覆盖 | 添加 |
|-----------|--------------------------|--------|-------|
| `tests/unit/[system]/[file]_test.[ext]` | `test_[scenario]` | AC-N / BUG-NNN | [date] |

## 已知缺口

应存在但尚未编写的测试：

| 优先级 | 系统 | 建议路径 | 覆盖 | 尚未编写原因 |
|----------|--------|----------------|--------|------------------------|
| HIGH | [system] | `tests/unit/[system]/[path]` | BUG-NNN | Bug 修复时无测试 |

## 已隔离测试

不稳定或禁用的测试（不在 CI 中运行）：

| 测试文件 | 函数 | 原因 | 隔离起始 |
|-----------|----------|--------|-------------------|
| (none) | | | |
```

---

## 7. Write Output
## 7. 写入输出

Ask: "May I write/update `tests/regression-suite.md` with the current
regression suite manifest?"
询问："我可以用当前回归套件清单写入/更新 `tests/regression-suite.md` 吗？"

For `update` mode: append new entries; never remove existing entries
(use `Edit` with targeted insertions).
对于 `update` 模式：追加新条目；绝不移除现有条目
（使用带定向插入的 `Edit`）。
For `audit` mode: rewrite the full manifest with updated coverage data.
对于 `audit` 模式：用更新后的覆盖数据重写完整清单。
For `report` mode: do not write anything.
对于 `report` 模式：不写入任何内容。

After writing (if approved):
写入后（若批准）：

- For each HIGH priority gap: "Consider creating the missing regression test
  before the next sprint. Run `/test-helpers` to scaffold the test file."
- 对每个 HIGH 优先级缺口："考虑在下一个冲刺前创建缺失的回归测试。运行 `/test-helpers` 脚手架测试文件。"
- If bug regression gaps > 0: "These bugs can silently return without regression
  tests. The next sprint should include a story to write the missing tests."
- 若 bug 回归缺口 > 0："这些 bug 没有回归测试可能静默回归。下一个冲刺应包含编写缺失测试的故事。"
- If coverage drift detected: "Regression suite may be drifting. Consider
  running `/regression-suite audit` at the next sprint boundary."
- 若检测到覆盖漂移："回归套件可能在漂移。考虑在下一个冲刺边界运行 `/regression-suite audit`。"

Verdict: **COMPLETE** — regression suite updated. (If user declined write: Verdict: **BLOCKED**.)
裁定：**COMPLETE** —— 回归套件已更新。（若用户拒绝写入：裁定：**BLOCKED**。）

---

## Collaborative Protocol
## 协作协议

- **Never remove existing regression tests from the manifest** without
  explicit user approval — removing a test that was deliberately written is a
  regression risk itself
- **未经用户明确批准，绝不从清单中移除现有回归测试** —— 移除特意编写的测试本身就是回归风险
- **Gaps are advisory, not blocking** — surface them clearly but do not prevent
  other work from proceeding (except at release gate where regression suite is required)
- **缺口是建议而非阻断** —— 清晰呈现但不要阻止其他工作进行（回归套件必需的发布门禁除外）
- **Quarantine is not deletion** — tests with intermittent failures should be
  quarantined (noted in manifest) but not removed; they should be fixed by
  `/test-flakiness`
- **隔离不是删除** —— 间歇性失败的测试应被隔离（在清单中注明）但不移除；应由 `/test-flakiness` 修复
- **Ask before writing** — always confirm before creating or updating the manifest
- **写入前询问** —— 创建或更新清单前始终确认
