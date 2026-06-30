---
name: team-qa
description: "Orchestrate the QA team through a full testing cycle. Coordinates qa-lead (strategy + test plan) and qa-tester (test case writing + bug reporting) to produce a complete QA package for a sprint or feature. Covers: test plan generation, test case writing, smoke check gate, manual QA execution, and sign-off report."
argument-hint: "[sprint | feature: system-name] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
model: sonnet
agent: qa-lead
---
---
名称：team-qa
描述："在完整测试周期中编排 QA 团队。协调 qa-lead（策略 + 测试计划）和 qa-tester（测试用例编写 + 缺陷报告），为冲刺或功能生成完整的 QA 包。涵盖：测试计划生成、测试用例编写、冒烟检查关卡、手动 QA 执行和签收报告。"
argument-hint："[sprint | feature: system-name] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Task, AskUserQuestion
model：sonnet
agent：qa-lead
---

When this skill is invoked, orchestrate the QA team through a structured testing cycle.
当此技能被调用时，通过结构化的测试周期编排 QA 团队。

**Decision Points:** At each phase transition, use `AskUserQuestion` to present
the user with the subagent's proposals as selectable options. Write the agent's
full analysis in conversation, then capture the decision with concise labels.
The user must approve before moving to the next phase.
**决策点：** 在每个阶段转换时，使用 `AskUserQuestion` 向用户
呈现子智能体的建议作为可选项。在对话中写入智能体的
完整分析，然后用简洁的标签捕获决策。
用户必须在进入下一阶段前批准。

## Phase 0: Resolve Review Mode
## 阶段 0：解析评审模式

1. If `--review [mode]` was passed as an argument, use that mode.
1. 如果作为参数传递了 `--review [mode]`，使用该模式。
2. Else read `production/review-mode.txt` — use whatever is written there.
2. 否则读取 `production/review-mode.txt` —— 使用其中写入的内容。
3. Else default to `lean`.
3. 否则默认为 `lean`。

Modes:
模式：
- `full` — spawn all director and lead gates as described
- `full` —— 如所述生成所有主管和负责人关卡
- `lean` — skip director gates unless they are PHASE-GATE type (CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE)
- `lean` —— 除非是 PHASE-GATE 类型（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE），否则跳过主管关卡
- `solo` — skip all director gate spawning entirely; run the skill without any agent gates
- `solo` —— 完全跳过所有主管关卡生成；在没有任何智能体关卡的情况下运行技能

Store the resolved mode for use in all subsequent phases.
存储解析的模式以用于所有后续阶段。

## Team Composition
## 团队组成

- **qa-lead** — QA strategy, test plan generation, story classification, sign-off report
- **qa-lead** —— QA 策略、测试计划生成、故事分类、签收报告
- **qa-tester** — Test case writing, bug report writing, manual QA documentation
- **qa-tester** —— 测试用例编写、缺陷报告编写、手动 QA 文档

## How to Delegate
## 如何委派

Use the Task tool to spawn each team member as a subagent:
使用 Task 工具将每个团队成员生成为子智能体：
- `subagent_type: qa-lead` — Strategy, planning, classification, sign-off
- `subagent_type: qa-lead` —— 策略、计划、分类、签收
- `subagent_type: qa-tester` — Test case writing and bug report writing
- `subagent_type: qa-tester` —— 测试用例编写和缺陷报告编写

Always provide full context in each agent's prompt (story file paths, QA plan path, scope constraints). Launch independent qa-tester tasks in parallel where possible (e.g., multiple stories in Phase 5 can be scaffolded simultaneously).
始终在每个智能体的提示中提供完整上下文（故事文件路径、QA 计划路径、范围约束）。在可能的情况下并行启动独立的 qa-tester 任务（例如，阶段 5 中的多个故事可以同时构建）。

## Pipeline
## 流水线

### Phase 1: Load Context
### 阶段 1：加载上下文

Before doing anything else, gather the full scope:
在做任何其他事情之前，收集完整范围：

1. Detect the current sprint or feature scope from the argument:
1. 从参数中检测当前冲刺或功能范围：
   - If argument is a sprint identifier (e.g., `sprint-03`): Glob `production/sprints/` for files matching `*[sprint-identifier]*.md`. Read the matched file. If multiple match, use the most recently modified.
   - 如果参数是冲刺标识符（例如，`sprint-03`）：在 `production/sprints/` 中通配匹配 `*[sprint-identifier]*.md` 的文件。读取匹配的文件。如果多个匹配，使用最近修改的。
   - If argument is `feature: [system-name]`: glob story files tagged for that system
   - 如果参数是 `feature: [system-name]`：通配为该系统标记的故事文件
   - If no argument: read `production/session-state/active.md` and `production/sprint-status.yaml` (if present) to infer the active sprint
   - 如果无参数：读取 `production/session-state/active.md` 和 `production/sprint-status.yaml`（如果存在）以推断活跃冲刺

2. Read `production/stage.txt` to confirm the current project phase.
2. 读取 `production/stage.txt` 以确认当前项目阶段。

3. Count stories found and report to the user:
3. 计算找到的故事并向用户报告：
   > "QA cycle starting for [sprint/feature]. Found [N] stories. Current stage: [stage]. Ready to begin QA strategy?"
   > "正在为 [sprint/feature] 启动 QA 周期。找到 [N] 个故事。当前阶段：[stage]。准备好开始 QA 策略了吗？"

### Phase 2: QA Strategy (qa-lead)
### 阶段 2：QA 策略（qa-lead）

Spawn `qa-lead` via Task to review all in-scope stories and produce a QA strategy.
通过 Task 生成 `qa-lead` 以审查所有范围内故事并生成 QA 策略。

Prompt the qa-lead to:
提示 qa-lead：
- Read each story file
- 读取每个故事文件
- Classify each story by type: **Logic** / **Integration** / **Visual/Feel** / **UI** / **Config/Data**
- 按类型对每个故事分类：**Logic** / **Integration** / **Visual/Feel** / **UI** / **Config/Data**
- Identify which stories require automated test evidence vs. manual QA
- 识别哪些故事需要自动化测试证据 vs. 手动 QA
- Flag any stories with missing acceptance criteria or missing test evidence that would block QA
- 标记任何缺少验收标准或缺少测试证据从而会阻塞 QA 的故事
- Estimate manual QA effort (number of test sessions needed)
- 估算手动 QA 工作量（需要的测试会话数）
- **Before assessing smoke status, check for an existing smoke check report**: Glob `production/qa/smoke-*.md` and read the most recently modified file (if found). If a report exists, use its verdict and findings directly — do not re-interview the user. If no report exists, note: "No prior smoke check report found — run `/smoke-check sprint` before proceeding." and set smoke check status to UNKNOWN (treat as PASS WITH WARNINGS for the purpose of continuing). Produce a smoke check verdict: **PASS** / **PASS WITH WARNINGS [list]** / **FAIL [list of failures]** / **UNKNOWN (no report found)**
- **在评估冒烟状态之前，检查现有的冒烟检查报告**：通配 `production/qa/smoke-*.md` 并读取最近修改的文件（如果找到）。如果报告存在，直接使用其结论和发现 —— 不要重新访谈用户。如果报告不存在，注明："未找到先前冒烟检查报告 —— 在继续之前运行 `/smoke-check sprint`。"并将冒烟检查状态设置为 UNKNOWN（出于继续的目的视为 PASS WITH WARNINGS）。生成冒烟检查结论：**PASS** / **PASS WITH WARNINGS [列表]** / **FAIL [失败列表]** / **UNKNOWN（未找到报告）**
- Produce a strategy summary table and smoke check result:
- 生成策略汇总表和冒烟检查结果：

  | Story | Type | Automated Required | Manual Required | Blocker? |
  |-------|------|--------------------|-----------------|----------|

  **Smoke Check**: [PASS / PASS WITH WARNINGS / FAIL / UNKNOWN] — [source: `production/qa/smoke-[date].md` or "no report found"] — [details if not PASS]

If the smoke check result is **FAIL**, the qa-lead must list the failures prominently. QA cannot proceed past the strategy phase with a failed smoke check.
如果冒烟检查结果为 **FAIL**，qa-lead 必须显著列出失败。QA 在冒烟检查失败的情况下无法越过策略阶段继续。

Present the qa-lead's full strategy to the user, then use `AskUserQuestion`:
向用户呈现 qa-lead 的完整策略，然后使用 `AskUserQuestion`：

```
question: "QA Strategy Review"
options:
  - "Looks good — proceed to test plan"
  - "Adjust story types before proceeding"
  - "Skip blocked stories and proceed with the rest"
  - "Smoke check failed — fix issues and re-run /team-qa"
  - "Cancel — resolve blockers first"
```

If smoke check **FAIL**: do not proceed to Phase 3. Surface the failures from the smoke check report and stop. The user must fix them, re-run `/smoke-check sprint`, and then re-run `/team-qa`.
如果冒烟检查 **FAIL**：不要进入阶段 3。显示冒烟检查报告中的失败并停止。用户必须修复它们，重新运行 `/smoke-check sprint`，然后重新运行 `/team-qa`。
If smoke check **UNKNOWN**: surface a warning — "No smoke check report found. Recommend running `/smoke-check sprint` before QA. Proceeding with caution."
如果冒烟检查 **UNKNOWN**：显示警告 —— "未找到冒烟检查报告。建议在 QA 之前运行 `/smoke-check sprint`。谨慎继续。"
If smoke check **PASS WITH WARNINGS**: note the warnings for the sign-off report and continue.
如果冒烟检查 **PASS WITH WARNINGS**：为签收报告注明警告并继续。
If blockers are present: list them explicitly. The user may choose to skip blocked stories or cancel the cycle.
如果存在阻塞：明确列出它们。用户可以选择跳过被阻塞的故事或取消周期。

### Phase 3: Test Plan Generation
### 阶段 3：测试计划生成

Using the strategy from Phase 2, produce a structured test plan document.
使用阶段 2 的策略，生成结构化的测试计划文档。

The test plan should cover:
测试计划应涵盖：
- **Scope**: sprint/feature name, story count, dates
- **范围**：冲刺/功能名称、故事数、日期
- **Story Classification Table**: from Phase 2 strategy
- **故事分类表**：来自阶段 2 策略
- **Automated Test Requirements**: which stories need test files, expected paths in `tests/`
- **自动化测试要求**：哪些故事需要测试文件，`tests/` 中的预期路径
- **Manual QA Scope**: which stories need manual walkthrough and what to validate
- **手动 QA 范围**：哪些故事需要手动演练以及验证什么
- **Out of Scope**: what is explicitly not being tested this cycle and why
- **范围外**：本周期明确不测试什么以及为什么
- **Entry Criteria**: what must be true before QA can begin. Always include: (1) Smoke check PASS or PASS WITH WARNINGS report exists at `production/qa/smoke-*.md`, (2) build is stable (no crashes on launch), (3) all Must Have stories have Status: in-progress or done in `production/sprint-status.yaml`. Add any sprint-specific criteria beyond these.
- **入口标准**：QA 开始前必须为真的条件。始终包括：(1) 在 `production/qa/smoke-*.md` 存在冒烟检查 PASS 或 PASS WITH WARNINGS 报告，(2) 构建稳定（启动时无崩溃），(3) 所有 Must Have 故事在 `production/sprint-status.yaml` 中具有 Status: in-progress 或 done。添加这些之外的任何冲刺特定标准。
- **Exit Criteria**: what constitutes a completed QA cycle (all stories PASS or FAIL with bugs filed)
- **出口标准**：什么构成完成的 QA 周期（所有故事 PASS 或 FAIL 并已提交缺陷）

Ask: "May I write the QA plan to `production/qa/qa-plan-[sprint]-[date].md`?"
询问："我是否可以将 QA 计划写入 `production/qa/qa-plan-[sprint]-[date].md`？"

Write only after receiving approval.
仅在收到批准后写入。

### Phase 4: Test Case Writing (qa-tester)
### 阶段 4：测试用例编写（qa-tester）

> **Smoke check** is performed as part of Phase 2 (QA Strategy). If the smoke check returned FAIL in Phase 2, the cycle was stopped there. This phase only runs when the Phase 2 smoke check was PASS, PASS WITH WARNINGS, or UNKNOWN.
> **冒烟检查**作为阶段 2（QA 策略）的一部分执行。如果冒烟检查在阶段 2 返回 FAIL，周期在那里停止。此阶段仅在阶段 2 冒烟检查为 PASS、PASS WITH WARNINGS 或 UNKNOWN 时运行。

For each story requiring manual QA (Visual/Feel, UI, Integration without automated tests):
对于需要手动 QA 的每个故事（Visual/Feel、UI、没有自动化测试的 Integration）：

Spawn `qa-tester` via Task for each story (run in parallel where possible), providing:
通过 Task 为每个故事生成 `qa-tester`（在可能的情况下并行运行），提供：
- The story file path
- 故事文件路径
- The relevant section of the QA plan for that story
- 该故事 QA 计划的相关章节
- The GDD acceptance criteria for the system being tested (if available)
- 正在测试系统的 GDD 验收标准（如果可用）
- Instructions to write detailed test cases covering all acceptance criteria
- 编写涵盖所有验收标准的详细测试用例的说明

Each test case set should include:
每个测试用例集应包括：
- **Preconditions**: game state required before testing begins
- **前置条件**：测试开始前所需的游戏状态
- **Steps**: numbered, unambiguous actions
- **步骤**：编号的、明确的操作
- **Expected Result**: what should happen
- **预期结果**：应该发生什么
- **Actual Result**: field left blank for the tester to fill in
- **实际结果**：留空供测试者填写的字段
- **Pass/Fail**: field left blank
- **Pass/Fail**：留空的字段

Present the test cases to the user for review before execution. Group by story.
在执行前向用户呈现测试用例以供审查。按故事分组。

Use `AskUserQuestion` per story group (batched 3-4 at a time):
每个故事组使用 `AskUserQuestion`（一次批处理 3-4 个）：

```
question: "Test cases ready for [Story Group]. Review before manual QA begins?"
options:
  - "Approved — begin manual QA for these stories"
  - "Revise test cases for [story name]"
  - "Skip manual QA for [story name] — not ready"
```

### Phase 5: Manual QA Execution
### 阶段 5：手动 QA 执行

Walk through each story in the approved manual QA list.
演练已批准手动 QA 列表中的每个故事。

Batch stories into groups of 3-4 and use `AskUserQuestion` for each:
将故事批处理为 3-4 个一组，并为每组使用 `AskUserQuestion`：

```
question: "Manual QA — [Story Title]\n[brief description of what to test]"
options:
  - "PASS — all acceptance criteria verified"
  - "PASS WITH NOTES — minor issues found (describe after)"
  - "FAIL — criteria not met (describe after)"
  - "BLOCKED — cannot test yet (reason)"
```

After each FAIL result: use `AskUserQuestion` to collect the failure description, then spawn `qa-tester` via Task to write a formal bug report in `production/qa/bugs/`.
每次 FAIL 结果后：使用 `AskUserQuestion` 收集失败描述，然后通过 Task 生成 `qa-tester` 在 `production/qa/bugs/` 中编写正式缺陷报告。

Bug report naming: `BUG-[NNN]-[short-slug].md` (increment NNN from existing bugs in the directory).
缺陷报告命名：`BUG-[NNN]-[short-slug].md`（从目录中现有缺陷递增 NNN）。

After collecting all results, summarize:
收集所有结果后，汇总：
- Stories PASS: [count]
- Stories PASS（通过）：[计数]
- Stories PASS WITH NOTES: [count]
- Stories PASS WITH NOTES（带备注通过）：[计数]
- Stories FAIL: [count] — bugs filed: [IDs]
- Stories FAIL（失败）：[计数] —— 已提交缺陷：[ID]
- Stories BLOCKED: [count]
- Stories BLOCKED（阻塞）：[计数]

### Phase 6: QA Sign-Off Report
### 阶段 6：QA 签收报告

Spawn `qa-lead` via Task to produce the sign-off report using all results from Phases 4–6.
通过 Task 生成 `qa-lead` 以使用阶段 4–6 的所有结果生成签收报告。

The sign-off report format:
签收报告格式：

```markdown
## QA Sign-Off Report: [Sprint/Feature]
**Date**: [date]

### Test Coverage Summary
| Story | Type | Auto Test | Manual QA | Result |
|-------|------|-----------|-----------|--------|
| [title] | Logic | PASS | — | PASS |
| [title] | Visual | — | PASS | PASS |

### Bugs Found
| ID | Story | Severity | Status |
|----|-------|----------|--------|
| BUG-001 | [story] | S2 | Open |

### Verdict: APPROVED / APPROVED WITH CONDITIONS / NOT APPROVED

**Conditions** (if any): [list what must be fixed before the build advances]

### Next Step
[guidance based on verdict]
```

Verdict rules:
结论规则：
- **APPROVED**: All stories PASS or PASS WITH NOTES; no S1/S2 bugs open
- **APPROVED（批准）**：所有故事 PASS 或 PASS WITH NOTES；没有未关闭的 S1/S2 缺陷
- **APPROVED WITH CONDITIONS**: S3/S4 bugs open, or PASS WITH NOTES issues documented; no S1/S2 bugs
- **APPROVED WITH CONDITIONS（有条件批准）**：S3/S4 缺陷未关闭，或 PASS WITH NOTES 问题已记录；没有 S1/S2 缺陷
- **NOT APPROVED**: Any S1/S2 bugs open; or stories FAIL without documented workaround
- **NOT APPROVED（未批准）**：任何 S1/S2 缺陷未关闭；或故事 FAIL 没有记录的变通方案

Next step guidance by verdict:
按结论的下一步指导：
- APPROVED: "Build is ready for the next phase. Run `/gate-check` to validate advancement."
- APPROVED："构建已准备好进入下一阶段。运行 `/gate-check` 验证推进。"
- APPROVED WITH CONDITIONS: "Resolve conditions before advancing. S3/S4 bugs may be deferred to polish."
- APPROVED WITH CONDITIONS："在推进前解决条件。S3/S4 缺陷可推迟到打磨阶段。"
- NOT APPROVED: "Resolve S1/S2 bugs and re-run `/team-qa` or targeted manual QA before advancing."
- NOT APPROVED："解决 S1/S2 缺陷并在推进前重新运行 `/team-qa` 或定向手动 QA。"

Ask: "May I write this QA sign-off report to `production/qa/qa-signoff-[sprint]-[date].md`?"
询问："我是否可以将此 QA 签收报告写入 `production/qa/qa-signoff-[sprint]-[date].md`？"

Write only after receiving approval.
仅在收到批准后写入。

## Error Recovery Protocol
## 错误恢复协议

If any spawned agent (via Task) returns BLOCKED, errors, or cannot complete:
如果任何生成的智能体（通过 Task）返回 BLOCKED、错误或无法完成：

1. **Surface immediately**: Report "[AgentName]: BLOCKED — [reason]" to the user before continuing to dependent phases
1. **立即显示**：在继续依赖阶段之前向用户报告"[AgentName]：BLOCKED —— [原因]"
2. **Assess dependencies**: Check whether the blocked agent's output is required by subsequent phases. If yes, do not proceed past that dependency point without user input.
2. **评估依赖**：检查后续阶段是否需要被阻塞智能体的输出。如果是，在没有用户输入的情况下不要越过该依赖点。
3. **Offer options** via AskUserQuestion with choices:
3. 通过 AskUserQuestion 提供选项，可选：
   - Skip this agent and note the gap in the final report
   - 跳过此智能体并在最终报告中注明差距
   - Retry with narrower scope
   - 以更窄的范围重试
   - Stop here and resolve the blocker first
   - 在此停止并先解决阻塞
4. **Always produce a partial report** — output whatever was completed. Never discard work because one agent blocked.
4. **始终生成部分报告** —— 输出已完成的任何内容。不要因为一个智能体阻塞而丢弃工作。

Common blockers:
常见阻塞：
- Input file missing (story not found, GDD absent) → redirect to the skill that creates it
- 输入文件缺失（未找到故事，GDD 缺失）→ 重定向到创建它的技能
- ADR status is Proposed → do not implement; run `/architecture-decision` first
- ADR 状态是 Proposed → 不要实施；先运行 `/architecture-decision`
- Scope too large → split into two stories via `/create-stories`
- 范围太大 → 通过 `/create-stories` 拆分为两个故事
- Conflicting instructions between ADR and story → surface the conflict, do not guess
- ADR 和故事之间的指令冲突 → 显示冲突，不要猜测

## Output
## 输出

A summary covering: stories in scope, smoke check result, manual QA results, bugs filed (with IDs and severities), and the final APPROVED / APPROVED WITH CONDITIONS / NOT APPROVED verdict.
涵盖以下内容的汇总：范围内故事、冒烟检查结果、手动 QA 结果、已提交缺陷（带 ID 和严重性）以及最终 APPROVED / APPROVED WITH CONDITIONS / NOT APPROVED 结论。

Verdict: **COMPLETE** — QA cycle finished.
结论：**COMPLETE（完成）** —— QA 周期完成。
Verdict: **BLOCKED** — smoke check failed or critical blocker prevented cycle completion; partial report produced.
结论：**BLOCKED（阻塞）** —— 冒烟检查失败或关键阻塞阻止了周期完成；已生成部分报告。

## Session State Update
## 会话状态更新

After the final phase completes (sign-off report written or BLOCKED verdict reached), silently append to `production/session-state/active.md`:
在最终阶段完成后（写入签收报告或达到 BLOCKED 结论），静默追加到 `production/session-state/active.md`：

```
<!-- QA RUN: [date] | Sprint: [sprint identifier or "ad-hoc"] | Verdict: [PASS/FAIL/CONCERNS] | Report: production/qa/qa-[date].md -->
```
