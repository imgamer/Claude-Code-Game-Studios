---
name: test-flakiness
description: "Detect non-deterministic (flaky) tests by reading CI run logs or test result history. Aggregates pass rates per test, identifies intermittent failures, recommends quarantine or fix, and maintains a flaky test registry. Best run during Polish phase or after multiple CI runs."
argument-hint: "[ci-log-path | scan | registry]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
model: sonnet
---
---
名称：test-flakiness
描述："通过读取 CI 运行日志或测试结果历史检测非确定性（不稳定）测试。聚合每个测试的通过率，识别间歇性失败，建议隔离或修复，并维护不稳定测试注册表。最好在打磨阶段或多次 CI 运行后运行。"
argument-hint："[ci-log-path | scan | registry]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash
model：sonnet
---

# Test Flakiness Detection
# 测试不稳定性检测

A flaky test is one that sometimes passes and sometimes fails without any code
change. Flaky tests are worse than no tests in some ways — they train the team
to ignore red CI runs, masking genuine failures. This skill identifies them,
explains likely causes, and recommends whether to quarantine or fix each one.
不稳定测试是指在没有任何代码变更的情况下有时通过、有时失败的测试。在某些方面，不稳定测试比没有测试更糟糕 —— 它们使团队习惯于忽略红色 CI 运行，掩盖真正的失败。此技能识别它们，解释可能的原因，并建议是隔离还是修复每个测试。

**Output:** Updated `tests/regression-suite.md` quarantine section + optional
`production/qa/flakiness-report-[date].md`
**输出：** 更新后的 `tests/regression-suite.md` 隔离章节 + 可选的 `production/qa/flakiness-report-[date].md`

**When to run:**
**何时运行：**
- Polish phase (tests have had many runs; statistical signal is reliable)
- 打磨阶段（测试已运行多次；统计信号可靠）
- When developers start dismissing CI failures as "probably flaky"
- 当开发人员开始将 CI 失败视为"可能不稳定"而置之不理时
- After `/regression-suite` identifies quarantined tests that need diagnosis
- 在 `/regression-suite` 识别出需要诊断的隔离测试后

---

## 1. Parse Arguments
## 1. 解析参数

**Modes:**
**模式：**
- `/test-flakiness [ci-log-path]` — analyse a specific CI run log file
- `/test-flakiness [ci-log-path]` —— 分析特定 CI 运行日志文件
- `/test-flakiness scan` — scan all available CI logs in `.github/` or
  standard log output directories
- `/test-flakiness scan` —— 扫描 `.github/` 或标准日志输出目录中的所有可用 CI 日志
- `/test-flakiness registry` — read existing regression-suite.md quarantine
  section and provide remediation guidance for already-known flaky tests
- `/test-flakiness registry` —— 读取现有 regression-suite.md 隔离章节，并为已知不稳定测试提供修复指导
- No argument — auto-detect: run `scan` if CI logs are accessible, else
  `registry`
- 无参数 —— 自动检测：若可访问 CI 日志则运行 `scan`，否则 `registry`

---

## 2. Locate CI Log Data
## 2. 定位 CI 日志数据

### Option A — GitHub Actions (preferred)
### 选项 A —— GitHub Actions（首选）

Check for test result artifacts:
检查测试结果产物：
```bash
ls -t .github/ 2>/dev/null
ls -t test-results/ 2>/dev/null
```

For Godot projects: GdUnit4 outputs XML results compatible with JUnit format.
Check `test-results/` for `.xml` files.
对于 Godot 项目：GdUnit4 输出与 JUnit 格式兼容的 XML 结果。
检查 `test-results/` 中的 `.xml` 文件。

For Unity projects: game-ci test runner outputs NUnit XML to `test-results/`
by default.
对于 Unity 项目：game-ci 测试运行器默认将 NUnit XML 输出到 `test-results/`。

For Unreal projects: automation logs go to `Saved/Logs/`. Grep for
`Result: Success` and `Result: Fail` patterns.
对于 Unreal 项目：自动化日志位于 `Saved/Logs/`。Grep `Result: Success` 和 `Result: Fail` 模式。

### Option B — Local log files
### 选项 B —— 本地日志文件

If a path argument is provided, read that file directly.
若提供了路径参数，直接读取该文件。

### Option C — No log data available
### 选项 C —— 无可用日志数据

If no logs found:
若未找到日志：
> "No CI log data found. To detect flaky tests, this skill needs test result
> history from multiple runs. Options:
> 1. Run the test suite at least 3 times and collect the output logs
> 2. Check CI pipeline output and save a log to `test-results/`
> 3. Run `/test-flakiness registry` to review tests already flagged as flaky
>    in `tests/regression-suite.md`"
> "未找到 CI 日志数据。要检测不稳定测试，此技能需要来自多次运行的测试结果历史。选项：
> 1. 运行测试套件至少 3 次并收集输出日志
> 2. 检查 CI 流水线输出并将日志保存到 `test-results/`
> 3. 运行 `/test-flakiness registry` 以评审已在 `tests/regression-suite.md` 中标记为不稳定的测试"

Stop and ask the user which option to pursue.
停止并询问用户要采用哪个选项。

---

## 3. Parse Test Results
## 3. 解析测试结果

For each CI log or result file found, parse:
对找到的每个 CI 日志或结果文件，解析：

**JUnit XML format** (GdUnit4 / Unity):
**JUnit XML 格式**（GdUnit4 / Unity）：
- Grep for `<testcase name=` to get test names
- Grep `<testcase name=` 以获取测试名称
- Grep for `<failure` or `<error` to identify failures
- Grep `<failure` 或 `<error` 以识别失败
- Parse `classname` and `name` attributes for full test identifiers
- 解析 `classname` 和 `name` 属性以获取完整测试标识符

**Plain text logs**:
**纯文本日志**：
- Grep for pass/fail patterns:
- Grep 通过/失败模式：
  - Godot: `PASSED` / `FAILED` adjacent to test names
  - Godot：`PASSED` / `FAILED` 紧邻测试名称
  - Unreal: `Result: Success` / `Result: Fail`
  - Unreal：`Result: Success` / `Result: Fail`
  - Unity: `Test passed` / `Test failed`
  - Unity：`Test passed` / `Test failed`

Build a table: `test_id → [run1_result, run2_result, run3_result, ...]`
构建表格：`test_id → [run1_result, run2_result, run3_result, ...]`

---

## 4. Identify Flaky Tests
## 4. 识别不稳定测试

A test is **flaky** if it appears in the result history with both PASS and
FAIL outcomes across runs with no code changes between them.
若一个测试在结果历史中跨运行（其间无代码变更）同时出现 PASS 和 FAIL 结果，则该测试为**不稳定**。

Flakiness thresholds:
不稳定性阈值：
- **High flakiness**: Fails in >25% of runs — quarantine immediately
- **高不稳定性**：在 >25% 的运行中失败 —— 立即隔离
- **Moderate flakiness**: Fails in 5–25% of runs — investigate and fix soon
- **中度不稳定性**：在 5–25% 的运行中失败 —— 调查并尽快修复
- **Low/suspected flakiness**: Fails in 1–5% of runs — monitor; may be
  genuinely rare failure
- **低/疑似不稳定性**：在 1–5% 的运行中失败 —— 监控；可能是真正罕见的失败

For each flaky test, classify the likely cause:
对每个不稳定测试，分类可能的原因：

### Cause classification
### 原因分类

| Cause | Symptoms | Fix direction |
|-------|----------|---------------|
| **Timing / async** | Fails after awaiting signals or timers; pass rate correlates with system load | Add explicit await/synchronisation; avoid time-based delays |
| **Order dependency** | Fails when run after specific other tests; passes in isolation | Add proper setup/teardown; ensure test isolation |
| **Random seed** | Fails intermittently with no pattern; involves RNG | Pass explicit seed; don't use `randf()` in tests |
| **Resource leak** | Fails more often later in a test run | Fix cleanup in teardown; check orphan nodes (Godot) or object disposal (Unity) |
| **External state** | Fails when a file, scene, or global exists from a prior test | Isolate test from file system; use in-memory mocks |
| **Floating point** | Fails on comparisons like `== 0.5` | Use epsilon comparison (`is_equal_approx`, `Assert.AreApproximately`) |
| **Scene/prefab load race** | Fails when scenes are not yet ready | Await one frame after instantiation; use `await get_tree().process_frame` |
| 原因 | 症状 | 修复方向 |
|-------|----------|---------------|
| **时序/异步** | 在等待信号或计时器后失败；通过率与系统负载相关 | 添加显式 await/同步；避免基于时间的延迟 |
| **顺序依赖** | 在特定其他测试之后运行时失败；单独运行时通过 | 添加正确的 setup/teardown；确保测试隔离 |
| **随机种子** | 间歇性失败无规律；涉及 RNG | 传递显式种子；不要在测试中使用 `randf()` |
| **资源泄漏** | 在测试运行后期更频繁失败 | 修复 teardown 中的清理；检查孤儿节点（Godot）或对象处置（Unity） |
| **外部状态** | 当来自先前测试的文件、场景或全局存在时失败 | 将测试与文件系统隔离；使用内存模拟 |
| **浮点** | 在 `== 0.5` 等比较上失败 | 使用 epsilon 比较（`is_equal_approx`、`Assert.AreApproximately`） |
| **场景/预制体加载竞争** | 当场景尚未就绪时失败 | 在实例化后等待一帧；使用 `await get_tree().process_frame` |

Use Grep to check the test file for timing calls, randf, global state access,
or equality comparisons on floats to narrow down the cause.
使用 Grep 检查测试文件中的时序调用、randf、全局状态访问或浮点相等比较以缩小原因范围。

---

## 5. Recommend Action
## 5. 建议行动

For each flaky test:
对每个不稳定测试：

**Quarantine (High flakiness):**
**隔离（高不稳定性）：**
> "Quarantine this test immediately. Disable it in CI by adding
> `@pytest.mark.skip` / `[Ignore]` / `GdUnitSkip` annotation. Log it in
> `tests/regression-suite.md` quarantine section. The test is now opt-in only.
> Fix the root cause before removing quarantine."
> "立即隔离此测试。通过添加 `@pytest.mark.skip` / `[Ignore]` / `GdUnitSkip` 注解在 CI 中禁用它。在 `tests/regression-suite.md` 隔离章节中记录它。该测试现在仅可选启用。在移除隔离前修复根本原因。"

**Investigate and fix soon (Moderate):**
**尽快调查并修复（中度）：**
> "This test is intermittently unreliable. Root cause appears to be [cause].
> Suggested fix: [specific fix based on cause classification]. Do not quarantine
> yet — fix the test directly."
> "此测试间歇性不可靠。根本原因似乎是 [cause]。建议修复：[基于原因分类的具体修复]。暂不要隔离 —— 直接修复测试。"

**Monitor (Low/suspected):**
**监控（低/疑似）：**
> "This test shows suspected flakiness. Collect more run data before
> quarantining. Note it as 'suspected' in the regression suite."
> "此测试显示疑似不稳定性。在隔离前收集更多运行数据。在回归套件中将其标记为 'suspected'。"

---

## 6. Generate Reports
## 6. 生成报告

### In-conversation summary
### 对话内摘要

```
## Flakiness Detection Results

**Runs analysed**: [N]
**Tests tracked**: [N]

### Flaky Tests Found

| Test | System | Fail Rate | Likely Cause | Recommendation |
|------|--------|-----------|--------------|----------------|
| [test_name] | [system] | [N]% | Timing | Quarantine + fix async |
| [test_name] | [system] | [N]% | Float comparison | Fix: use epsilon compare |
| [test_name] | [system] | [N]% | Order dependency | Investigate teardown |

### Clean Tests (no flakiness detected)

[N] tests ran across [N] runs with consistent results — no flakiness detected.

### Data Limitations

[Note if fewer than 5 runs were available — fewer runs = less statistical confidence]
```
```
## 不稳定性检测结果

**已分析运行**：[N]
**已跟踪测试**：[N]

### 发现的不稳定测试

| 测试 | 系统 | 失败率 | 可能原因 | 建议 |
|------|--------|-----------|--------------|----------------|
| [test_name] | [system] | [N]% | 时序 | 隔离 + 修复异步 |
| [test_name] | [system] | [N]% | 浮点比较 | 修复：使用 epsilon 比较 |
| [test_name] | [system] | [N]% | 顺序依赖 | 调查 teardown |

### 干净测试（未检测到不稳定性）

[N] 个测试跨 [N] 次运行结果一致 —— 未检测到不稳定性。

### 数据限制

[若可用运行少于 5 次则注明 —— 运行越少 = 统计置信度越低]
```

---

## 7. Update Regression Suite + Optional Report File
## 7. 更新回归套件 + 可选报告文件

Ask: "May I update the quarantine section of `tests/regression-suite.md`
with the flaky tests found?"
询问："我可以用发现的不稳定测试更新 `tests/regression-suite.md` 的隔离章节吗？"

If yes: use `Edit` to append entries to the Quarantined Tests table.
若是：使用 `Edit` 将条目追加到隔离测试表。
Never remove existing quarantine entries — only add new ones.
绝不移除现有隔离条目 —— 仅添加新条目。

Ask (separately): "May I write a full flakiness report to
`production/qa/flakiness-report-[date].md`?"
（单独）询问："我可以将完整不稳定性报告写入 `production/qa/flakiness-report-[date].md` 吗？"

The full report includes per-test analysis with cause details and
engine-specific fix snippets.
完整报告包括每个测试的分析，含原因详情和引擎特定的修复代码片段。

After writing:
写入后：

- For each quarantined test: "Add the engine-specific skip annotation to
  disable this test in CI. Re-enable after the root cause is fixed."
- 对每个隔离的测试："添加引擎特定的跳过注解以在 CI 中禁用此测试。在根本原因修复后重新启用。"
- For fix-eligible tests: "The fix for [test] is straightforward —
  change the equality comparison on line [N] to use `is_equal_approx`."
- 对可修复测试："[test] 的修复很简单 —— 将第 [N] 行的相等比较改为使用 `is_equal_approx`。"
- Summary: "Once all quarantine annotations are applied, CI should run green.
  Schedule fix work for the [N] quarantined tests before the release gate."
- 摘要："一旦应用所有隔离注解，CI 应运行通过。在发布门禁前为 [N] 个隔离测试安排修复工作。"

---

## Collaborative Protocol
## 协作协议

- **Never delete test files** — quarantine means annotate + list, not remove
- **绝不删除测试文件** —— 隔离意味着注解 + 列表，而非移除
- **Statistical confidence matters** — with < 3 runs, flag findings as
  "suspected" not "confirmed"; ask if more run data is available
- **统计置信度很重要** —— 运行少于 3 次时，将发现标记为 "suspected" 而非 "confirmed"；询问是否有更多运行数据可用
- **Fix is always the goal** — quarantine is temporary; surface the fix
  direction even when recommending quarantine
- **修复始终是目标** —— 隔离是临时的；即使建议隔离也要呈现修复方向
- **Ask before writing** — both the regression-suite update and the report
  file require explicit approval. On write: Verdict: **COMPLETE** — flakiness report written. On decline: Verdict: **BLOCKED** — user declined write.
- **写入前询问** —— 回归套件更新和报告文件均需明确批准。写入时：裁定：**COMPLETE** —— 不稳定性报告已写入。拒绝时：裁定：**BLOCKED** —— 用户拒绝写入。
- **Flakiness in CI is a team problem** — surface the list and recommended
  actions clearly; do not just silently quarantine without the team knowing
- **CI 中的不稳定性是团队问题** —— 清晰呈现列表和建议行动；不要在团队不知情的情况下静默隔离
