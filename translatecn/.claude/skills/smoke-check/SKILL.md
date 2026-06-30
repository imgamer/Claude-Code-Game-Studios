---
name: smoke-check
description: "Run the critical path smoke test gate before QA hand-off. Executes the automated test suite, verifies core functionality, and produces a PASS/FAIL report. Run after a sprint's stories are implemented and before manual QA begins. A failed smoke check means the build is not ready for QA."
argument-hint: "[sprint | quick | --platform pc|console|mobile|all]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, AskUserQuestion
model: sonnet
---
---
name: smoke-check
description: "在 QA 移交之前运行关键路径冒烟测试门禁。执行自动化测试套件，验证核心功能，并生成 PASS/FAIL 报告。在冲刺故事实现之后、手动 QA 开始之前运行。冒烟检查失败意味着构建未准备好进行 QA。"
argument-hint: "[sprint | quick | --platform pc|console|mobile|all]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, AskUserQuestion
model: sonnet
---

# Smoke Check
# 冒烟检查

This skill is the gate between "implementation done" and "ready for QA
hand-off". It runs the automated test suite, checks for test coverage gaps,
batch-verifies critical paths with the developer, and produces a PASS/FAIL
report.
此技能是 "实现完成" 和 "准备好 QA 移交" 之间的门禁。它运行自动化测试套件，检查测试覆盖缺口，与开发者批量验证关键路径，并生成 PASS/FAIL 报告。

The rule is simple: **a build that fails smoke check does not go to QA.**
Handing a broken build to QA wastes their time and demoralises the team.
规则很简单：**冒烟检查失败的构建不会进入 QA。**
将损坏的构建交给 QA 会浪费他们的时间并打击团队士气。

**Output:** `production/qa/smoke-[date].md`
**输出：** `production/qa/smoke-[date].md`

---

## Parse Arguments
## 解析参数

Arguments can be combined: `/smoke-check sprint --platform console`
参数可以组合使用：`/smoke-check sprint --platform console`

**Base mode** (first argument, default: `sprint`):
- `sprint` — full smoke check against the current sprint's stories
- `quick` — skip coverage scan (Phase 3) and Batch 3; use for rapid re-checks
**基础模式**（第一个参数，默认：`sprint`）：
- `sprint` — 针对当前冲刺故事的完整冒烟检查
- `quick` — 跳过覆盖率扫描（阶段 3）和批次 3；用于快速重新检查

**Platform flag** (`--platform`, default: none):
- `--platform pc` — add PC-specific checks (keyboard, mouse, windowed mode)
- `--platform console` — add console-specific checks (gamepad, TV safe zones,
  platform certification requirements)
- `--platform mobile` — add mobile-specific checks (touch, portrait/landscape,
  battery/thermal behaviour)
- `--platform all` — add all platform variants; output per-platform verdict table
**平台标志**（`--platform`，默认：无）：
- `--platform pc` — 添加 PC 特定检查（键盘、鼠标、窗口模式）
- `--platform console` — 添加主机特定检查（手柄、TV 安全区域、
  平台认证要求）
- `--platform mobile` — 添加移动特定检查（触摸、竖屏/横屏、
  电池/热行为）
- `--platform all` — 添加所有平台变体；输出按平台的结论表

If `--platform` is provided, Phase 4 adds platform-specific batches and
Phase 5 outputs a per-platform verdict table in addition to the overall verdict.
如果提供 `--platform`，阶段 4 添加平台特定批次，
阶段 5 除总体结论外还输出按平台的结论表。

---

## Phase 1: Detect Test Setup
## 阶段 1：检测测试设置

Before running anything, understand the environment:
在运行任何内容之前，了解环境：

1. **Test framework check**: verify `tests/` directory exists.
   If it does not: "No test directory found at `tests/`. Run `/test-setup`
   to scaffold the testing infrastructure, or create the directory manually
   if tests live elsewhere." Then stop.
1. **测试框架检查**：验证 `tests/` 目录存在。
   如果不存在："No test directory found at `tests/`. Run `/test-setup`
   to scaffold the testing infrastructure, or create the directory manually
   if tests live elsewhere." 然后停止。

2. **CI check**: check whether `.github/workflows/` contains a workflow file
   referencing tests. Note in the report whether CI is configured.
2. **CI 检查**：检查 `.github/workflows/` 是否包含引用
   测试的工作流文件。在报告中注明 CI 是否已配置。

3. **Engine detection**: read `.claude/docs/technical-preferences.md` and
   extract the `Engine:` value. Store this for test command selection in
   Phase 2.
3. **引擎检测**：读取 `.claude/docs/technical-preferences.md` 并
   提取 `Engine:` 值。存储此值以供阶段 2 选择测试命令。

4. **Smoke test list**: check whether `production/qa/smoke-tests.md` or
   `tests/smoke/` exists. If a smoke test list is found, load it for use in
   Phase 4. If neither exists, smoke tests will be drawn from the current QA
   plan (Phase 4 fallback).
4. **冒烟测试列表**：检查 `production/qa/smoke-tests.md` 或
   `tests/smoke/` 是否存在。如果找到冒烟测试列表，加载它以供
   阶段 4 使用。如果两者都不存在，冒烟测试将从当前 QA
   计划中提取（阶段 4 后备）。

5. **QA plan check**: glob `production/qa/qa-plan-*.md` and take the most
   recently modified file. If found, note the path — it will be used in
   Phase 3 and Phase 4. If not found, note: "No QA plan found. Run
   `/qa-plan sprint` before smoke-checking for best results."
5. **QA 计划检查**：glob `production/qa/qa-plan-*.md` 并取最近
   修改的文件。如果找到，注明路径 — 它将用于
   阶段 3 和阶段 4。如果未找到，注明："No QA plan found. Run
   `/qa-plan sprint` before smoke-checking for best results."

Report findings before proceeding: "Environment: [engine]. Test directory:
[found / not found]. CI configured: [yes / no]. QA plan: [path / not found]."
在继续之前报告发现："Environment: [engine]. Test directory:
[found / not found]. CI configured: [yes / no]. QA plan: [path / not found]."

---

## Phase 2: Run Automated Tests
## 阶段 2：运行自动化测试

Attempt to run the test suite via Bash. Select the command based on the engine
detected in Phase 1:
尝试通过 Bash 运行测试套件。根据阶段 1 检测到的引擎选择命令：

**Godot 4:**
```bash
godot --headless --script tests/gdunit4_runner.gd 2>&1
```
If the GDUnit4 runner script does not exist at that path, try:
```bash
godot --headless -s addons/gdunit4/GdUnitRunner.gd 2>&1
```
If neither path exists, note: "GDUnit4 runner not found — confirm the runner
path for your test framework."
**Godot 4：**
```bash
godot --headless --script tests/gdunit4_runner.gd 2>&1
```
如果该路径不存在 GDUnit4 运行器脚本，尝试：
```bash
godot --headless -s addons/gdunit4/GdUnitRunner.gd 2>&1
```
如果两个路径都不存在，注明："GDUnit4 runner not found — confirm the runner
path for your test framework."

**Unity:**
Unity tests require the editor and cannot be run headlessly via shell in most
environments. Check for recent test result artifacts:
```bash
# List most recent test results (bash) — on Windows PowerShell use the fallback below
ls -t test-results/ 2>/dev/null | head -5 \
  || powershell -Command "Get-ChildItem test-results/ -ErrorAction SilentlyContinue | Sort-Object LastWriteTime -Descending | Select-Object -First 5 -ExpandProperty Name"
```
If test result files exist (XML or JSON), read the most recent one and parse
PASS/FAIL counts. If no artifacts exist: "Unity tests must be run from the
editor or CI pipeline. Please confirm test status manually before proceeding."
**Unity：**
Unity 测试需要编辑器，在大多数环境中无法通过 shell 无头运行。检查最近的测试结果工件：
```bash
# List most recent test results (bash) — on Windows PowerShell use the fallback below
ls -t test-results/ 2>/dev/null | head -5 \
  || powershell -Command "Get-ChildItem test-results/ -ErrorAction SilentlyContinue | Sort-Object LastWriteTime -Descending | Select-Object -First 5 -ExpandProperty Name"
```
如果测试结果文件存在（XML 或 JSON），读取最近的一个并解析
PASS/FAIL 计数。如果不存在工件："Unity tests must be run from the
editor or CI pipeline. Please confirm test status manually before proceeding."

**Unreal Engine:**
```bash
# List most recent Unreal automation logs (bash) — on Windows PowerShell use the fallback below
ls -t Saved/Logs/ 2>/dev/null | grep -i "test\|automation" | head -5 \
  || powershell -Command "Get-ChildItem Saved/Logs/ -ErrorAction SilentlyContinue | Where-Object { $_.Name -match 'test|automation' } | Sort-Object LastWriteTime -Descending | Select-Object -First 5 -ExpandProperty Name"
```
If no matching log found: "UE automation tests must be run via the Session
Frontend or CI pipeline. Please confirm test status manually."
**Unreal Engine：**
```bash
# List most recent Unreal automation logs (bash) — on Windows PowerShell use the fallback below
ls -t Saved/Logs/ 2>/dev/null | grep -i "test\|automation" | head -5 \
  || powershell -Command "Get-ChildItem Saved/Logs/ -ErrorAction SilentlyContinue | Where-Object { $_.Name -match 'test|automation' } | Sort-Object LastWriteTime -Descending | Select-Object -First 5 -ExpandProperty Name"
```
如果未找到匹配的日志："UE automation tests must be run via the Session
Frontend or CI pipeline. Please confirm test status manually."

**Unknown engine / not configured:**
"Engine not configured in `.claude/docs/technical-preferences.md`. Run
`/setup-engine` to specify the engine, then re-run `/smoke-check`."
**未知引擎 / 未配置：**
"Engine not configured in `.claude/docs/technical-preferences.md`. Run
`/setup-engine` to specify the engine, then re-run `/smoke-check`."

**If the test runner is not available in this environment** (engine binary not
on PATH, runner script not found, etc.), report clearly:
**如果测试运行器在此环境中不可用**（引擎二进制文件不在
PATH 上、未找到运行器脚本等），明确报告：

"Automated tests could not be executed — engine binary not found on PATH.
Status will be recorded as NOT RUN. Confirm test results from your local IDE
or CI pipeline. Unconfirmed NOT RUN is treated as PASS WITH WARNINGS, not
FAIL — the developer must manually confirm results."
"Automated tests could not be executed — engine binary not found on PATH.
Status will be recorded as NOT RUN. Confirm test results from your local IDE
or CI pipeline. Unconfirmed NOT RUN is treated as PASS WITH WARNINGS, not
FAIL — the developer must manually confirm results."

Do not treat NOT RUN as an automatic FAIL. Record it as a warning. The
developer's manual confirmation in Phase 4 can resolve it.
不要将 NOT RUN 视为自动 FAIL。将其记录为警告。
开发者在阶段 4 的手动确认可以解决它。

Parse runner output and extract:
- Total tests run
- Passing count
- Failing count
- Names of any failing tests (up to 10; if more, note the count)
- Any crash or error output from the runner itself
解析运行器输出并提取：
- 运行的测试总数
- 通过计数
- 失败计数
- 任何失败测试的名称（最多 10 个；如果更多，注明计数）
- 运行器本身的任何崩溃或错误输出

---

## Phase 3: Check Test Coverage
## 阶段 3：检查测试覆盖率

Draw the story list from, in priority order:
1. The QA plan found in Phase 1 (its Test Summary table lists expected test
   file paths per story)
2. The current sprint plan from `production/sprints/` (most recently modified
   file)
3. If the `quick` argument was passed, skip this phase entirely and note:
   "Coverage scan skipped — run `/smoke-check sprint` for full coverage
   analysis."
按优先顺序从以下来源提取故事列表：
1. 阶段 1 中找到的 QA 计划（其 Test Summary 表列出了每个故事的预期测试
   文件路径）
2. `production/sprints/` 中的当前冲刺计划（最近修改的
   文件）
3. 如果传入了 `quick` 参数，完全跳过此阶段并注明：
   "Coverage scan skipped — run `/smoke-check sprint` for full coverage
   analysis."

For each story in scope:
对于范围内的每个故事：

1. Extract the system slug from the story's file path
   (e.g., `production/epics/combat/story-001.md` → `combat`)
2. Glob `tests/unit/[system]/` and `tests/integration/[system]/` for files
   whose name contains the story slug or a closely related term
3. Check the story file itself for a `Test file:` header field or a
   "Test Evidence" section
1. 从故事文件路径提取系统 slug
   （例如 `production/epics/combat/story-001.md` → `combat`）
2. Glob `tests/unit/[system]/` 和 `tests/integration/[system]/` 查找
   名称包含故事 slug 或密切相关术语的文件
3. 检查故事文件本身是否有 `Test file:` 标头字段或
   "Test Evidence" 章节

Assign a coverage status to each story:
为每个故事分配覆盖率状态：

| Status | Meaning |
|--------|---------|
| **COVERED** | A test file was found matching this story's system and scope |
| **MANUAL** | Story type is Visual/Feel or UI; a test evidence document was found |
| **MISSING** | Logic or Integration story with no matching test file |
| **EXPECTED** | Config/Data story — no test file required; spot-check is sufficient |
| **UNKNOWN** | Story file missing or unreadable |
| 状态 | 含义 |
|--------|---------|
| **COVERED** | 找到匹配此故事系统和范围的测试文件 |
| **MANUAL** | 故事类型为 Visual/Feel 或 UI；找到测试证据文档 |
| **MISSING** | Logic 或 Integration 故事没有匹配的测试文件 |
| **EXPECTED** | Config/Data 故事 — 不需要测试文件；抽查即可 |
| **UNKNOWN** | 故事文件缺失或不可读 |

MISSING entries are advisory gaps. They do not cause a FAIL verdict but must
appear prominently in the report and must be resolved before `/story-done` can
fully close those stories.
MISSING 条目是建议性缺口。它们不会导致 FAIL 结论，但必须
在报告中突出显示，并且必须在 `/story-done` 完全关闭这些故事之前
解决。

---

## Phase 4: Run Manual Smoke Checks
## 阶段 4：运行手动冒烟检查

Draw the smoke test checklist from, in priority order:
1. The QA plan's "Smoke Test Scope" section (if QA plan was found in Phase 1)
2. `production/qa/smoke-tests.md` (if it exists)
3. `tests/smoke/` directory contents (if it exists)
4. The standard fallback list below (used only when none of the above exist)
按优先顺序从以下来源提取冒烟测试检查清单：
1. QA 计划的 "Smoke Test Scope" 章节（如果在阶段 1 找到 QA 计划）
2. `production/qa/smoke-tests.md`（如果存在）
3. `tests/smoke/` 目录内容（如果存在）
4. 下面的标准后备列表（仅当以上都不存在时使用）

Tailor batches 2 and 3 to the actual systems identified from the sprint or QA
plan. Replace bracketed placeholders with real mechanic names from the current
sprint's stories.
将批次 2 和 3 调整为从冲刺或 QA
计划识别的实际系统。用当前冲刺故事中的实际机制名称替换带括号的占位符。

Use `AskUserQuestion` to batch-verify. Keep to at most 3 calls.
使用 `AskUserQuestion` 进行批量验证。最多保持 3 次调用。

**Batch 1 — Core stability (always run):**
**批次 1 — 核心稳定性（始终运行）：**
```
question: "Core stability — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Game does not launch or crashes before reaching the main menu"
  - "New game / session fails to start"
  - "Main menu does not respond to inputs"
  - "Crash or hang observed during basic navigation"
```
```
question: "Core stability — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Game does not launch or crashes before reaching the main menu"
  - "New game / session fails to start"
  - "Main menu does not respond to inputs"
  - "Crash or hang observed during basic navigation"
```

For any selected item, ask the user to briefly describe what failed before generating the report.
对于任何选中的项，在生成报告之前要求用户简要描述失败的内容。

**Batch 2 — Sprint changes and regression (always run):**
**批次 2 — 冲刺变更和回归（始终运行）：**
```
question: "Sprint changes and regression — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "[Primary mechanic this sprint] — FAILED"
  - "[Second notable change this sprint, if any] — FAILED"
  - "Regression in a previous sprint's feature — FAILED"
  - "Other unexpected breakage observed — FAILED"
```
```
question: "Sprint changes and regression — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "[Primary mechanic this sprint] — FAILED"
  - "[Second notable change this sprint, if any] — FAILED"
  - "Regression in a previous sprint's feature — FAILED"
  - "Other unexpected breakage observed — FAILED"
```

For any selected item, ask the user to briefly describe what broke before generating the report.
对于任何选中的项，在生成报告之前要求用户简要描述损坏的内容。

**Batch 3 — Data integrity and performance (run unless `quick` argument):**
**批次 3 — 数据完整性和性能（除非 `quick` 参数，否则运行）：**
```
question: "Data integrity and performance — select any items that FAILED or were skipped (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Save / load — FAILED (data loss or corruption observed)"
  - "Save / load — N/A (save system not yet implemented)"
  - "Frame rate drops or hitches observed — FAILED"
  - "Performance not checked this session"
```
```
question: "Data integrity and performance — select any items that FAILED or were skipped (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Save / load — FAILED (data loss or corruption observed)"
  - "Save / load — N/A (save system not yet implemented)"
  - "Frame rate drops or hitches observed — FAILED"
  - "Performance not checked this session"
```

For any FAILED item selected, ask the user to describe what broke before generating the report.
对于任何选中的 FAILED 项，在生成报告之前要求用户描述损坏的内容。

Record each response verbatim for the Phase 5 report.
逐字记录每个响应以供阶段 5 报告使用。

**Platform Batches** *(run only if `--platform` argument was provided)*:
**平台批次** *（仅在提供 `--platform` 参数时运行）*：

**PC platform** (`--platform pc` or `--platform all`):
**PC 平台**（`--platform pc` 或 `--platform all`）：
```
question: "PC Platform — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Keyboard controls — FAILED (describe issue after)"
  - "Mouse input or cursor visibility — FAILED (describe issue after)"
  - "Windowed / fullscreen mode — FAILED (describe issue after)"
  - "Resolution change — FAILED (describe issue after)"
```
```
question: "PC Platform — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Keyboard controls — FAILED (describe issue after)"
  - "Mouse input or cursor visibility — FAILED (describe issue after)"
  - "Windowed / fullscreen mode — FAILED (describe issue after)"
  - "Resolution change — FAILED (describe issue after)"
```

For any selected item, ask the user to briefly describe what failed before generating the report.
对于任何选中的项，在生成报告之前要求用户简要描述失败的内容。

**Console platform** (`--platform console` or `--platform all`):
**主机平台**（`--platform console` 或 `--platform all`）：
```
question: "Console Platform — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Gamepad input — FAILED (describe issue after)"
  - "UI outside TV safe zone / text clipped — FAILED (describe what is clipped after)"
  - "Keyboard/mouse fallback shown to gamepad user — FAILED (describe after)"
  - "Cold start (no prior save) — FAILED (describe issue after)"
```
```
question: "Console Platform — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Gamepad input — FAILED (describe issue after)"
  - "UI outside TV safe zone / text clipped — FAILED (describe what is clipped after)"
  - "Keyboard/mouse fallback shown to gamepad user — FAILED (describe after)"
  - "Cold start (no prior save) — FAILED (describe issue after)"
```

For any selected item, ask the user to briefly describe what failed before generating the report.
对于任何选中的项，在生成报告之前要求用户简要描述失败的内容。

**Mobile platform** (`--platform mobile` or `--platform all`):
**移动平台**（`--platform mobile` 或 `--platform all`）：
```
question: "Mobile Platform — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Touch controls — FAILED (describe issue after)"
  - "Orientation change (portrait ↔ landscape) — FAILED (describe what breaks after)"
  - "Background / foreground transition (home button) — FAILED (describe issue after)"
  - "Performance / thermal throttling on target device — FAILED (describe after)"
```
```
question: "Mobile Platform — select any items that FAILED (leave all unselected if everything passed):"
multiSelect: true
options:
  - "Touch controls — FAILED (describe issue after)"
  - "Orientation change (portrait ↔ landscape) — FAILED (describe what breaks after)"
  - "Background / foreground transition (home button) — FAILED (describe issue after)"
  - "Performance / thermal throttling on target device — FAILED (describe after)"
```

For any selected item, ask the user to briefly describe what failed before generating the report.
对于任何选中的项，在生成报告之前要求用户简要描述失败的内容。

---

## Phase 5: Generate Report
## 阶段 5：生成报告

Assemble the full smoke check report:
组装完整的冒烟检查报告：

````markdown
## Smoke Check Report
**Date**: [date]
**Sprint**: [sprint name / number, or "Not identified"]
**Engine**: [engine]
**QA Plan**: [path, or "Not found — run /qa-plan first"]
**Argument**: [sprint | quick | blank]

---

### Automated Tests

**Status**: [PASS ([N] tests, [N] passing) | FAIL ([N] failures) |
NOT RUN ([reason])]

[If FAIL, list failing tests:]
- `[test name]` — [brief failure description from runner output]

[If NOT RUN:]
"Manual confirmation required: did tests pass in your local IDE or CI? This
will determine whether the automated test row contributes to a FAIL verdict."

---

### Test Coverage

| Story | Type | Test File | Coverage Status |
|-------|------|-----------|----------------|
| [title] | Logic | `tests/unit/[system]/[slug]_test.[ext]` | COVERED |
| [title] | Visual/Feel | `tests/evidence/[slug]-screenshots.md` | MANUAL |
| [title] | Logic | — | MISSING ⚠ |
| [title] | Config/Data | — | EXPECTED |

**Summary**: [N] covered, [N] manual, [N] missing, [N] expected.

---

### Manual Smoke Checks

- [x] Game launches without crash — PASS
- [x] New game starts — PASS
- [x] [Core mechanic] — PASS
- [ ] [Other check] — FAIL: [user's description]
- [x] Save / load — PASS
- [-] Performance — not checked this session

---

### Missing Test Evidence

Stories that must have test evidence before they can be marked COMPLETE via
`/story-done`:

- **[story title]** (`[path]`) — Logic story has no test file.
  Expected location: `tests/unit/[system]/[story-slug]_test.[ext]`

[If none:] "All Logic and Integration stories have test coverage."

---

### Platform-Specific Results *(only if `--platform` was provided)*

| Platform | Checks Run | Passed | Failed | Platform Verdict |
|----------|-----------|--------|--------|-----------------|
| PC | [N] | [N] | [N] | PASS / FAIL |
| Console | [N] | [N] | [N] | PASS / FAIL |
| Mobile | [N] | [N] | [N] | PASS / FAIL |

**Platform notes**: [any platform-specific observations not captured in pass/fail]

Any platform with one or more FAIL checks contributes to the overall FAIL verdict.

---

### Verdict: [PASS | PASS WITH WARNINGS | FAIL]

[Verdict rules — first matching rule wins:]

**FAIL** if ANY of:
- Automated test suite ran and reported one or more test failures
- Any Batch 1 (core stability) check returned FAIL
- Any Batch 2 (primary sprint mechanic or regression check) returned FAIL

**PASS WITH WARNINGS** if ALL of:
- Automated tests PASS or NOT RUN (developer has not yet confirmed)
- All Batch 1 and Batch 2 smoke checks PASS
- One or more Logic/Integration stories have MISSING test evidence

**PASS** if ALL of:
- Automated tests PASS
- All smoke checks in all batches PASS or N/A
- No MISSING test evidence entries
````
````markdown
## Smoke Check Report
**Date**: [date]
**Sprint**: [sprint name / number, or "Not identified"]
**Engine**: [engine]
**QA Plan**: [path, or "Not found — run /qa-plan first"]
**Argument**: [sprint | quick | blank]

---

### Automated Tests

**Status**: [PASS ([N] tests, [N] passing) | FAIL ([N] failures) |
NOT RUN ([reason])]

[If FAIL, list failing tests:]
- `[test name]` — [brief failure description from runner output]

[If NOT RUN:]
"Manual confirmation required: did tests pass in your local IDE or CI? This
will determine whether the automated test row contributes to a FAIL verdict."

---

### Test Coverage

| Story | Type | Test File | Coverage Status |
|-------|------|-----------|----------------|
| [title] | Logic | `tests/unit/[system]/[slug]_test.[ext]` | COVERED |
| [title] | Visual/Feel | `tests/evidence/[slug]-screenshots.md` | MANUAL |
| [title] | Logic | — | MISSING ⚠ |
| [title] | Config/Data | — | EXPECTED |

**Summary**: [N] covered, [N] manual, [N] missing, [N] expected.

---

### Manual Smoke Checks

- [x] Game launches without crash — PASS
- [x] New game starts — PASS
- [x] [Core mechanic] — PASS
- [ ] [Other check] — FAIL: [user's description]
- [x] Save / load — PASS
- [-] Performance — not checked this session

---

### Missing Test Evidence

Stories that must have test evidence before they can be marked COMPLETE via
`/story-done`:

- **[story title]** (`[path]`) — Logic story has no test file.
  Expected location: `tests/unit/[system]/[story-slug]_test.[ext]`

[If none:] "All Logic and Integration stories have test coverage."

---

### Platform-Specific Results *(only if `--platform` was provided)*

| Platform | Checks Run | Passed | Failed | Platform Verdict |
|----------|-----------|--------|--------|-----------------|
| PC | [N] | [N] | [N] | PASS / FAIL |
| Console | [N] | [N] | [N] | PASS / FAIL |
| Mobile | [N] | [N] | [N] | PASS / FAIL |

**Platform notes**: [any platform-specific observations not captured in pass/fail]

Any platform with one or more FAIL checks contributes to the overall FAIL verdict.

---

### Verdict: [PASS | PASS WITH WARNINGS | FAIL]

[Verdict rules — first matching rule wins:]

**FAIL** if ANY of:
- Automated test suite ran and reported one or more test failures
- Any Batch 1 (core stability) check returned FAIL
- Any Batch 2 (primary sprint mechanic or regression check) returned FAIL

**PASS WITH WARNINGS** if ALL of:
- Automated tests PASS or NOT RUN (developer has not yet confirmed)
- All Batch 1 and Batch 2 smoke checks PASS
- One or more Logic/Integration stories have MISSING test evidence

**PASS** if ALL of:
- Automated tests PASS
- All smoke checks in all batches PASS or N/A
- No MISSING test evidence entries
````

---

## Phase 6: Write and Gate
## 阶段 6：写入和门控

Present the full report in conversation, then ask:
在对话中呈现完整报告，然后询问：

"May I write this smoke check report to `production/qa/smoke-[date].md`?"
"May I write this smoke check report to `production/qa/smoke-[date].md`?"

Write only after approval.
仅在批准后写入。

After writing, deliver the gate verdict:
写入后，交付门禁结论：

**If verdict is FAIL:**
**如果结论为 FAIL：**

"The smoke check failed. Do not hand off to QA until these failures are
resolved:
"冒烟检查失败。在这些失败解决之前不要移交 QA：

[List each failing automated test or smoke check with a one-line description]
[列出每个失败的自动化测试或冒烟检查并附一行描述]

Fix the failures and run `/smoke-check` again to re-gate before QA hand-off."
修复失败并再次运行 `/smoke-check` 以在 QA 移交之前重新门控。"

**If verdict is PASS WITH WARNINGS:**
**如果结论为 PASS WITH WARNINGS：**

"Smoke check passed with warnings. The build is ready for manual QA.
"冒烟检查通过但有警告。构建已准备好进行手动 QA。

Advisory items to resolve before running `/story-done` on affected stories:
[list MISSING test evidence entries]
在受影响的故事上运行 `/story-done` 之前要解决的建议项：
[列出 MISSING 测试证据条目]

QA hand-off: share `production/qa/qa-plan-[sprint].md` with the qa-tester
agent to begin manual verification."
QA 移交：与 qa-tester 智能体分享 `production/qa/qa-plan-[sprint].md`
以开始手动验证。"

**If verdict is PASS:**
**如果结论为 PASS：**

"Smoke check passed cleanly. The build is ready for manual QA.
"冒烟检查干净通过。构建已准备好进行手动 QA。

QA hand-off: share `production/qa/qa-plan-[sprint].md` with the qa-tester
agent to begin manual verification."
QA 移交：与 qa-tester 智能体分享 `production/qa/qa-plan-[sprint].md`
以开始手动验证。"

---

## Collaborative Protocol
## 协作协议

- **Never treat NOT RUN as automatic FAIL** — record it as NOT RUN and let
  the developer confirm status manually. Unconfirmed NOT RUN contributes to
  PASS WITH WARNINGS, not FAIL.
- **Never auto-fix failures** — report them and state what must be resolved.
  Do not attempt to edit source code or test files.
- **PASS WITH WARNINGS does not block QA hand-off** — it records advisory
  gaps for `/story-done` to follow up on.
- **`quick` argument** skips Phase 3 (coverage scan) and Phase 4 Batch 3.
  Use it for rapid re-checks after fixing a specific failure.
- Use `AskUserQuestion` for all manual smoke check verification.
- **Never write the report without asking** — Phase 6 requires explicit
  approval before any file is created.
- **切勿将 NOT RUN 视为自动 FAIL** — 将其记录为 NOT RUN 并让
  开发者手动确认状态。未确认的 NOT RUN 计入
  PASS WITH WARNINGS，而非 FAIL。
- **切勿自动修复失败** — 报告它们并说明必须解决什么。
  不要尝试编辑源代码或测试文件。
- **PASS WITH WARNINGS 不阻塞 QA 移交** — 它记录建议性
  缺口供 `/story-done` 跟进。
- **`quick` 参数** 跳过阶段 3（覆盖率扫描）和阶段 4 批次 3。
  使用它进行修复特定失败后的快速重新检查。
- 使用 `AskUserQuestion` 进行所有手动冒烟检查验证。
- **切勿未经询问就写入报告** — 阶段 6 在创建任何文件之前要求明确
  批准。
