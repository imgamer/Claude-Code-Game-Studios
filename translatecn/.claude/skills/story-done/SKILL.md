---
name: story-done
description: "End-of-story completion review. Reads the story file, verifies each acceptance criterion against the implementation, checks for GDD/ADR deviations, prompts code review, updates story status to Complete, and surfaces the next ready story from the sprint."
argument-hint: "[story-file-path] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion, Task
model: sonnet
---
---
name: story-done
description: "故事收尾完成评审。读取故事文件，针对实现验证每个验收标准，检查 GDD/ADR 偏差，提示代码评审，将故事状态更新为 Complete，并呈现冲刺中下一个就绪的故事。"
argument-hint: "[story-file-path] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion, Task
model: sonnet
---

# Story Done
# 故事完成

This skill closes the loop between design and implementation. Run it at the end
of implementing any story. It ensures every acceptance criterion is verified
before the story is marked done, GDD and ADR deviations are explicitly
documented rather than silently introduced, code review is prompted rather than
forgotten, and the story file reflects actual completion status.
此技能在设计稿与实现之间闭环。在实现任何故事结束时运行它。它确保在故事标记为完成之前验证每个验收标准，GDD 和 ADR 偏差被显式记录而非悄悄引入，代码评审被提示而非被遗忘，且故事文件反映实际的完成状态。

**Output:** Updated story file (Status: Complete) + surfaced next story.
**输出：** 更新后的故事文件（Status: Complete）+ 呈现的下一个故事。

---

## Phase 1: Find the Story
## 阶段 1：查找故事

Resolve the review mode (once, store for all gate spawns this run):
解析审查模式（仅一次，本次运行的所有门禁派生均存储此值）：
1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`
1. 如果传入了 `--review [full|lean|solo]` → 使用该值
2. 否则读取 `production/review-mode.txt` → 使用其中的值
3. 否则 → 默认为 `lean`

See `.claude/docs/director-gates.md` for the full check pattern.
完整检查模式请参见 `.claude/docs/director-gates.md`。

**If a file path is provided** (e.g., `/story-done production/epics/core/story-damage-calculator.md`):
read that file directly.
**如果提供了文件路径**（例如 `/story-done production/epics/core/story-damage-calculator.md`）：
直接读取该文件。

**If no argument is provided:**
**如果未提供参数：**

1. Check `production/session-state/active.md` for the currently active story.
2. If not found there, read the most recent file in `production/sprints/` and
   look for stories marked IN PROGRESS.
3. If multiple in-progress stories are found, use `AskUserQuestion`:
   - "Which story are we completing?"
   - Options: list the in-progress story file names.
4. If no story can be found, ask the user to provide the path.
1. 检查 `production/session-state/active.md` 获取当前活动的故事。
2. 如果未在那里找到，读取 `production/sprints/` 中最近的文件并
   查找标记为 IN PROGRESS 的故事。
3. 如果找到多个进行中的故事，使用 `AskUserQuestion`：
   - "我们要完成哪个故事？"
   - 选项：列出进行中的故事文件名。
4. 如果找不到故事，请用户提供路径。

---

## Phase 2: Read the Story
## 阶段 2：读取故事

Read the full story file. Extract and hold in context:
读取完整的故事文件。提取并在上下文中保留：

- **Story name and ID**
- **GDD Requirement TR-ID(s)** referenced (e.g., `TR-combat-001`)
- **Manifest Version** embedded in the story header (e.g., `2026-03-10`)
- **ADR reference(s)** referenced
- **Acceptance Criteria** — the complete list (every checkbox item)
- **Implementation files** — files listed under "files to create/modify"
- **Story Type** — the `Type:` field from the story header (Logic / Integration / Visual/Feel / UI / Config/Data)
- **Engine notes** — any engine-specific constraints noted
- **Definition of Done** — if present, the story-level DoD
- **Estimated vs actual scope** — if an estimate was noted
- **故事名称和 ID**
- 引用的 **GDD 需求 TR-ID**（例如 `TR-combat-001`）
- 故事标头中嵌入的 **清单版本**（例如 `2026-03-10`）
- 引用的 **ADR 参考**
- **验收标准** — 完整列表（每个复选框项）
- **实现文件** — 在 "files to create/modify" 下列出的文件
- **故事类型** — 故事标头中的 `Type:` 字段（Logic / Integration / Visual/Feel / UI / Config/Data）
- **引擎备注** — 注明的任何引擎特定约束
- **完成定义** — 如果存在，故事级别的 DoD
- **估算与实际范围** — 如果注明了估算

Also read:
- `docs/architecture/tr-registry.yaml` — look up each TR-ID in the story.
  Read the *current* `requirement` text from the registry entry. This is the
  source of truth for what the GDD required — do not use any requirement text
  that may be quoted inline in the story (it may be stale).
- The referenced GDD section — just the acceptance criteria and key rules, not
  the full document. Use this to cross-check the registry text is still accurate.
- The referenced ADR(s) — just the Decision and Consequences sections
- `docs/architecture/control-manifest.md` header — extract the current
  `Manifest Version:` date (used in Phase 4 staleness check)
还需读取：
- `docs/architecture/tr-registry.yaml` — 查找故事中的每个 TR-ID。
  从注册表条目中读取*当前*的 `requirement` 文本。这是
  GDD 所需内容的真实来源 — 不要使用故事中可能内联引用的任何需求文本
  （它可能已过时）。
- 引用的 GDD 章节 — 仅验收标准和关键规则，而非
  完整文档。用此交叉检查注册表文本是否仍然准确。
- 引用的 ADR — 仅 Decision 和 Consequences 章节
- `docs/architecture/control-manifest.md` 标头 — 提取当前
  `Manifest Version:` 日期（用于阶段 4 的过时检查）

---

## Phase 3: Verify Acceptance Criteria
## 阶段 3：验证验收标准

For each acceptance criterion in the story, attempt verification using one of
three methods:
对于故事中的每个验收标准，使用以下三种方法之一尝试验证：

### Automatic verification (run without asking)
### 自动验证（无需询问直接运行）

- **File existence check**: `Glob` for files the story said would be created.
- **Test pass check**: if a test file path is mentioned, run it via `Bash`.
- **No hardcoded values check**: `Grep` for numeric literals in gameplay code
  paths that should be in config files.
- **No hardcoded strings check**: `Grep` for player-facing strings in `src/`
  that should be in localization files.
- **Dependency check**: if a criterion says "depends on X", check that X exists.
- **文件存在性检查**：对故事中说要创建的文件执行 `Glob`。
- **测试通过检查**：如果提到了测试文件路径，通过 `Bash` 运行它。
- **无硬编码值检查**：在应在配置文件中的玩法代码
  路径中 `Grep` 数值字面量。
- **无硬编码字符串检查**：在 `src/` 中 `Grep` 应在本地化文件中的
  面向玩家的字符串。
- **依赖检查**：如果某标准说 "depends on X"，检查 X 是否存在。

### Manual verification with confirmation (use `AskUserQuestion`)
### 带确认的手动验证（使用 `AskUserQuestion`）

- Criteria about subjective qualities ("feels responsive", "animations play correctly")
- Criteria about gameplay behaviour ("player takes damage when...", "enemy responds to...")
- Performance criteria ("completes within Xms") — ask if profiled or accept as assumed
- 关于主观质量的标准（"感觉响应灵敏"、"动画播放正确"）
- 关于玩法行为的标准（"玩家在……时受到伤害"、"敌人响应……"）
- 性能标准（"在 Xms 内完成"）— 询问是否已进行性能分析或接受为假设

Batch up to 4 manual verification questions into a single `AskUserQuestion` call:
将最多 4 个手动验证问题批量放入一个 `AskUserQuestion` 调用中：

```
question: "Does [criterion]?"
options: "Yes — passes", "No — fails", "Not tested yet"
```
```
question: "Does [criterion]?"
options: "Yes — passes", "No — fails", "Not tested yet"
```

### Unverifiable (flag without blocking)
### 无法验证（标记但不阻塞）

- Criteria that require a full game build to test (end-to-end gameplay scenarios)
- Mark as: `DEFERRED — requires playtest session`
- 需要完整游戏构建才能测试的标准（端到端玩法场景）
- 标记为：`DEFERRED — requires playtest session`

### Test-Criterion Traceability
### 测试-标准可追溯性

After completing the pass/fail/deferred check above, map each acceptance
criterion to the test that covers it:
在完成上述通过/失败/延迟检查之后，将每个验收标准映射到覆盖它的测试：

For each acceptance criterion in the story:
对于故事中的每个验收标准：

1. Ask: is there a test — unit, integration, or confirmed manual playtest — that
   directly verifies this criterion?
   - **Unit test**: check `tests/unit/` for a test file or function name that
     matches the criterion's subject (use `Glob` and `Grep`)
   - **Integration test**: check `tests/integration/` similarly
   - **Manual confirmation**: if the criterion was verified via `AskUserQuestion`
     above with a "Yes — passes" answer, count that as a manual test
1. 询问：是否存在一个测试 — 单元测试、集成测试或确认的手动试玩 — 直接
   验证此标准？
   - **单元测试**：检查 `tests/unit/` 中是否有匹配该标准主题的
     测试文件或函数名（使用 `Glob` 和 `Grep`）
   - **集成测试**：类似地检查 `tests/integration/`
   - **手动确认**：如果该标准是通过上述 `AskUserQuestion`
     以 "Yes — passes" 答案验证的，则计为手动测试

2. Produce a traceability table:
2. 生成可追溯性表：

```
| Criterion | Test | Status |
|-----------|------|--------|
| AC-1: [criterion text] | tests/unit/test_foo.gd::test_bar | COVERED |
| AC-2: [criterion text] | Manual playtest confirmation | COVERED |
| AC-3: [criterion text] | — | UNTESTED |
```
```
| Criterion | Test | Status |
|-----------|------|--------|
| AC-1: [criterion text] | tests/unit/test_foo.gd::test_bar | COVERED |
| AC-2: [criterion text] | Manual playtest confirmation | COVERED |
| AC-3: [criterion text] | — | UNTESTED |
```

3. Apply these escalation rules:
3. 应用以下升级规则：

   - If **>50% of criteria are UNTESTED**: escalate to **BLOCKING** — test
     coverage is insufficient to confirm the story is actually done. The verdict
     in Phase 6 cannot be COMPLETE until coverage improves.
   - If **some (≤50%) criteria are UNTESTED**: remain ADVISORY — does not block
     completion, but must appear in Completion Notes.
   - If **all criteria are COVERED**: no action needed beyond including the
     table in the report.
   - 如果 **>50% 的标准未测试**：升级为 **BLOCKING** — 测试
     覆盖率不足以确认故事真正完成。阶段 6 中的结论
     在覆盖率改善之前不能为 COMPLETE。
   - 如果 **部分（≤50%）标准未测试**：保持 ADVISORY — 不阻塞
     完成，但必须出现在完成备注中。
   - 如果 **所有标准都已覆盖**：除在报告中包含
     该表外，无需其他操作。

4. For any ADVISORY untested criteria, add to the Completion Notes in Phase 7:
   `"Untested criteria: [AC-N list]. Recommend adding tests in a follow-up story."`
4. 对于任何 ADVISORY 未测试的标准，添加到阶段 7 的完成备注中：
   `"Untested criteria: [AC-N list]. Recommend adding tests in a follow-up story."`

### Test Evidence Requirement
### 测试证据要求

Based on the Story Type extracted in Phase 2, check for required evidence:
基于阶段 2 提取的故事类型，检查所需证据：

| Story Type | Required Evidence | Gate Level |
|---|---|---|
| **Logic** | Automated unit test in `tests/unit/[system]/` — must exist and pass | BLOCKING |
| **Integration** | Integration test in `tests/integration/[system]/` OR playtest doc | BLOCKING |
| **Visual/Feel** | Screenshot + sign-off in `production/qa/evidence/` | ADVISORY |
| **UI** | Manual walkthrough doc OR interaction test in `production/qa/evidence/` | ADVISORY |
| **Config/Data** | Smoke check pass report in `production/qa/smoke-*.md` | ADVISORY |
| 故事类型 | 所需证据 | 门禁级别 |
|---|---|---|
| **Logic** | `tests/unit/[system]/` 中的自动化单元测试 — 必须存在且通过 | BLOCKING |
| **Integration** | `tests/integration/[system]/` 中的集成测试 或 试玩文档 | BLOCKING |
| **Visual/Feel** | `production/qa/evidence/` 中的截图 + 签署 | ADVISORY |
| **UI** | `production/qa/evidence/` 中的手动走查文档 或 交互测试 | ADVISORY |
| **Config/Data** | `production/qa/smoke-*.md` 中的冒烟检查通过报告 | ADVISORY |

**For Logic stories**: first read the story's **Test Evidence** section to extract the
exact required file path. Use `Glob` to check that exact path. If the exact path is not
found, also search `tests/unit/[system]/` broadly (the file may have been placed at a
slightly different location). If no test file is found at either location:
- Flag as **BLOCKING**: "Logic story has no unit test file. Story requires it at
  `[exact-path-from-Test-Evidence-section]`. Create and run the test before marking
  this story Complete."
**对于 Logic 故事**：首先读取故事的 **Test Evidence** 章节以提取
确切的所需文件路径。使用 `Glob` 检查该确切路径。如果未找到确切路径，
也广泛搜索 `tests/unit/[system]/`（该文件可能被放置在
稍有不同的位置）。如果在任一位置都未找到测试文件：
- 标记为 **BLOCKING**："Logic 故事没有单元测试文件。故事要求它位于
  `[exact-path-from-Test-Evidence-section]`。在标记此故事 Complete 之前
  创建并运行该测试。"

**For Integration stories**: read the story's **Test Evidence** section for the exact
required path. Use `Glob` to check that exact path first, then search
`tests/integration/[system]/` broadly, then check `production/session-logs/` for a
playtest record referencing this story.
If none found: flag as **BLOCKING** (same rule as Logic).
**对于 Integration 故事**：读取故事的 **Test Evidence** 章节以获取确切
所需路径。首先使用 `Glob` 检查该确切路径，然后广泛搜索
`tests/integration/[system]/`，再检查 `production/session-logs/` 中是否有
引用此故事的试玩记录。
如果均未找到：标记为 **BLOCKING**（规则与 Logic 相同）。

**For Visual/Feel and UI stories**: glob `production/qa/evidence/` for a file
referencing this story.
- If none: flag as **ADVISORY** — "No manual test evidence found. Create `production/qa/evidence/[story-slug]-evidence.md` using the test-evidence template and obtain sign-off before final closure."
- If found: read the file and check the sign-off table for unchecked boxes. Grep for lines matching `| .* | .* | .* | \[ \] Approved` (a sign-off row with an unchecked checkbox). If any unchecked sign-off rows are found: flag as **ADVISORY** — "Evidence file found at `[path]` but [N] sign-off(s) are still pending (shown as `[ ] Approved` in the sign-off table). Obtain required sign-offs before final closure. Note: for solo developers, all roles may be signed off by the same person."
- If all sign-off rows show `[x] Approved` or equivalent: note "Evidence file found and all sign-offs complete — ADVISORY passed."
**对于 Visual/Feel 和 UI 故事**：glob `production/qa/evidence/` 查找引用
此故事的文件。
- 如果未找到：标记为 **ADVISORY** — "未找到手动测试证据。使用 test-evidence 模板创建 `production/qa/evidence/[story-slug]-evidence.md` 并在最终关闭前获得签署。"
- 如果找到：读取文件并检查签署表中是否有未勾选的复选框。Grep 匹配 `| .* | .* | .* | \[ \] Approved` 的行（未勾选复选框的签署行）。如果找到任何未勾选的签署行：标记为 **ADVISORY** — "在 `[path]` 找到证据文件，但仍有 [N] 项签署待处理（在签署表中显示为 `[ ] Approved`）。在最终关闭前获取所需签署。注意：对于单人开发者，所有角色可由同一人签署。"
- 如果所有签署行都显示 `[x] Approved` 或等价内容：注明 "证据文件已找到且所有签署已完成 — ADVISORY 通过。"

**For Config/Data stories**: check for any `production/qa/smoke-*.md` file.
If none: flag as **ADVISORY** — "No smoke check report found. Run `/smoke-check`."
**对于 Config/Data 故事**：检查是否存在任何 `production/qa/smoke-*.md` 文件。
如果未找到：标记为 **ADVISORY** — "未找到冒烟检查报告。运行 `/smoke-check`。"

**If no Story Type is set**: flag as **ADVISORY** —
"Story Type not declared. Add `Type: [Logic|Integration|Visual/Feel|UI|Config/Data]`
to the story header to enable test evidence gate enforcement in future stories."
**如果未设置故事类型**：标记为 **ADVISORY** —
"未声明故事类型。在故事标头中添加 `Type: [Logic|Integration|Visual/Feel|UI|Config/Data]`
以在未来的故事中启用测试证据门禁强制执行。"

Any BLOCKING test evidence gap prevents the COMPLETE verdict in Phase 6.
任何 BLOCKING 级别的测试证据缺失都会阻止阶段 6 中的 COMPLETE 结论。

---

## Phase 4: Check for Deviations
## 阶段 4：检查偏差

Compare the implementation against the design documents.
将实现与设计文档进行比较。

Run these checks automatically:
自动运行以下检查：

1. **GDD rules check**: Using the current requirement text from `tr-registry.yaml`
   (looked up by the story's TR-ID), check that the implementation reflects what
   the GDD actually requires now — not what it required when the story was written.
   `Grep` the implemented files for key function names, data structures, or class
   names mentioned in the current GDD section.
1. **GDD 规则检查**：使用 `tr-registry.yaml` 中的当前需求文本
   （通过故事的 TR-ID 查找），检查实现是否反映了
   GDD 现在实际要求的内容 — 而非编写故事时要求的内容。
   在已实现文件中 `Grep` 当前 GDD 章节中提到的关键函数名、数据结构或
   类名。

2. **Manifest version staleness check**: Compare the `Manifest Version:` date
   embedded in the story header against the `Manifest Version:` date in the
   current `docs/architecture/control-manifest.md` header.
   - If they match → pass silently.
   - If the story's version is older → flag as ADVISORY:
     `ADVISORY: Story was written against manifest v[story-date]; current manifest
     is v[current-date]. New rules may apply. Run /story-readiness to check.`
   - If control-manifest.md does not exist → skip this check.
2. **清单版本过时检查**：将故事标头中嵌入的 `Manifest Version:` 日期
   与当前 `docs/architecture/control-manifest.md` 标头中的 `Manifest Version:` 日期进行比较。
   - 如果匹配 → 静默通过。
   - 如果故事版本较旧 → 标记为 ADVISORY：
     `ADVISORY: Story was written against manifest v[story-date]; current manifest
     is v[current-date]. New rules may apply. Run /story-readiness to check.`
   - 如果 control-manifest.md 不存在 → 跳过此检查。

3. **ADR constraints check**: Read the referenced ADR's Decision section. Check
   for forbidden patterns from `docs/architecture/control-manifest.md` (if it
   exists). `Grep` for patterns explicitly forbidden in the ADR.
3. **ADR 约束检查**：读取引用 ADR 的 Decision 章节。检查
   `docs/architecture/control-manifest.md` 中的禁止模式（如果
   存在）。`Grep` ADR 中明确禁止的模式。

4. **Hardcoded values check**: `Grep` the implemented files for numeric literals
   in gameplay logic that should be in data files.
4. **硬编码值检查**：在已实现文件中 `Grep` 玩法逻辑中
   应在数据文件中的数值字面量。

5. **Scope check**: Did the implementation touch files outside the story's stated
   scope? (files not listed in "files to create/modify")
5. **范围检查**：实现是否触及了故事所述范围之外的文件？
   （未在 "files to create/modify" 中列出的文件）

For each deviation found, categorize:
对于发现的每个偏差，分类如下：

- **BLOCKING** — implementation contradicts the GDD or ADR (must fix before
  marking complete)
- **ADVISORY** — implementation drifts slightly from spec but is functionally
  equivalent (document, user decides)
- **OUT OF SCOPE** — additional files were touched beyond the story's stated
  boundary (flag for awareness — may be valid or scope creep)
- **BLOCKING** — 实现与 GDD 或 ADR 矛盾（标记完成前必须修复）
- **ADVISORY** — 实现略微偏离规范但功能
  等价（记录，由用户决定）
- **OUT OF SCOPE** — 触及了故事所述边界之外的
  额外文件（标记以引起注意 — 可能是有效的或范围蔓延）

---

## Phase 4b: QA Coverage Gate
## 阶段 4b：QA 覆盖门禁

**Review mode check** — apply before spawning QL-TEST-COVERAGE:
**审查模式检查** — 在派生 QL-TEST-COVERAGE 之前应用：
- `solo` → skip. Note: "QL-TEST-COVERAGE skipped — Solo mode." Proceed to Phase 5.
- `lean` → skip (not a PHASE-GATE). Note: "QL-TEST-COVERAGE skipped — Lean mode." Proceed to Phase 5.
- `full` → spawn as normal.
- `solo` → 跳过。备注："QL-TEST-COVERAGE 已跳过 — 单人模式。"继续至阶段 5。
- `lean` → 跳过（非 PHASE-GATE）。备注："QL-TEST-COVERAGE 已跳过 — 精简模式。"继续至阶段 5。
- `full` → 正常派生。

After completing the deviation checks in Phase 4, spawn `qa-lead` via Task using gate **QL-TEST-COVERAGE** (`.claude/docs/director-gates.md`).
在完成阶段 4 的偏差检查后，通过 Task 使用门禁 **QL-TEST-COVERAGE**（`.claude/docs/director-gates.md`）派生 `qa-lead`。

Pass:
- The story file path and story type
- Test file paths found during Phase 3 (exact paths, or "none found")
- The story's `## QA Test Cases` section (the pre-written test specs from story creation)
- The story's `## Acceptance Criteria` list
传递：
- 故事文件路径和故事类型
- 阶段 3 中找到的测试文件路径（确切路径，或 "none found"）
- 故事的 `## QA Test Cases` 章节（故事创建时预先编写的测试规范）
- 故事的 `## Acceptance Criteria` 列表

The qa-lead reviews whether the tests actually cover what was specified — not just whether files exist.
qa-lead 审查测试是否真正覆盖了所规定的内容 — 而非仅检查文件是否存在。

Apply the verdict:
- **ADEQUATE** → proceed to Phase 5
- **GAPS** → flag as **ADVISORY**: "QA lead identified coverage gaps: [list]. Story can complete but gaps should be addressed in a follow-up story."
- **INADEQUATE** → flag as **BLOCKING**: "QA lead: critical logic is untested. Verdict cannot be COMPLETE until coverage improves. Specific gaps: [list]."
应用结论：
- **充足** → 继续至阶段 5
- **有缺口** → 标记为 **ADVISORY**："QA lead 识别出覆盖缺口：[list]。故事可以完成，但缺口应在后续故事中解决。"
- **不充足** → 标记为 **BLOCKING**："QA lead：关键逻辑未测试。在覆盖率改善之前结论不能为 COMPLETE。具体缺口：[list]。"

Skip this phase for Config/Data stories (no code tests required).
对于 Config/Data 故事跳过此阶段（不需要代码测试）。

---

## Phase 5: Lead Programmer Code Review Gate
## 阶段 5：主程序员代码评审门禁

**Review mode check** — apply before spawning LP-CODE-REVIEW:
**审查模式检查** — 在派生 LP-CODE-REVIEW 之前应用：
- `solo` → skip. Note: "LP-CODE-REVIEW skipped — Solo mode." Proceed to Phase 6 (completion report).
- `lean` → use `AskUserQuestion` before proceeding:
  - Prompt: "Code review is skipped in lean mode. Did you run `/code-review` on the implemented files?"
  - Options:
    - `Yes — /code-review passed or was approved with suggestions`
    - `No — skipping code review for this story`
    - `No — I'll run /code-review before the sprint close-out`
  - Record the answer in the completion notes (Phase 7). All three options proceed to Phase 6.
- `full` → spawn as normal.
- `solo` → 跳过。备注："LP-CODE-REVIEW 已跳过 — 单人模式。"继续至阶段 6（完成报告）。
- `lean` → 在继续之前使用 `AskUserQuestion`：
  - 提示："在精简模式下跳过了代码评审。您是否对已实现文件运行了 `/code-review`？"
  - 选项：
    - `是 — /code-review 通过或被批准并附建议`
    - `否 — 此故事跳过代码评审`
    - `否 — 我会在冲刺收尾前运行 /code-review`
  - 在完成备注中记录答案（阶段 7）。所有三个选项都继续至阶段 6。
- `full` → 正常派生。

Spawn `lead-programmer` via Task using gate **LP-CODE-REVIEW** (`.claude/docs/director-gates.md`).
通过 Task 使用门禁 **LP-CODE-REVIEW**（`.claude/docs/director-gates.md`）派生 `lead-programmer`。

Pass: implementation file paths, story file path, relevant GDD section, governing ADR.
传递：实现文件路径、故事文件路径、相关 GDD 章节、管辖 ADR。

Present the verdict to the user. If CONCERNS, surface them via `AskUserQuestion`:
- Options: `Revise flagged issues` / `Accept and proceed` / `Discuss further`
If REJECT, do not proceed to Phase 6 verdict until the issues are resolved.
向用户呈现结论。如果有顾虑，通过 `AskUserQuestion` 突出显示：
- 选项：`修订被标记的问题` / `接受并继续` / `进一步讨论`
如果拒绝，在问题解决之前不要继续至阶段 6 结论。

If the story has no implementation files yet (verdict is being run before coding is done), skip this phase and note: "LP-CODE-REVIEW skipped — no implementation files found. Run after implementation is complete."
如果故事还没有实现文件（结论在编码完成之前运行），跳过此阶段并备注："LP-CODE-REVIEW 已跳过 — 未找到实现文件。在实现完成后再运行。"

---

## Phase 6: Present the Completion Report
## 阶段 6：呈现完成报告

Before updating any files, present the full report:
在更新任何文件之前，呈现完整报告：

```markdown
## Story Done: [Story Name]
**Story**: [file path]
**Date**: [today]

### Acceptance Criteria: [X/Y passing]
- [x] [Criterion 1] — auto-verified (test passes)
- [x] [Criterion 2] — confirmed
- [ ] [Criterion 3] — FAILS: [reason]
- [?] [Criterion 4] — DEFERRED: requires playtest

### Test-Criterion Traceability
| Criterion | Test | Status |
|-----------|------|--------|
| AC-1: [text] | [test file::test name] | COVERED |
| AC-2: [text] | Manual confirmation | COVERED |
| AC-3: [text] | — | UNTESTED |

### Test Evidence
**Story Type**: [Logic | Integration | Visual/Feel | UI | Config/Data | Not declared]
**Required evidence**: [unit test file | integration test or playtest | screenshot + sign-off | walkthrough doc | smoke check pass]
**Evidence found**: [YES — `[path]` | NO — BLOCKING | NO — ADVISORY]

### Deviations
[NONE] OR:
- BLOCKING: [description] — [GDD/ADR reference]
- ADVISORY: [description] — user accepted / flagged for tech debt

### Scope
[All changes within stated scope] OR:
- Extra files touched: [list] — [note whether valid or scope creep]

### Verdict: COMPLETE / COMPLETE WITH NOTES / BLOCKED
```
```markdown
## Story Done: [Story Name]
**Story**: [file path]
**Date**: [today]

### Acceptance Criteria: [X/Y passing]
- [x] [Criterion 1] — auto-verified (test passes)
- [x] [Criterion 2] — confirmed
- [ ] [Criterion 3] — FAILS: [reason]
- [?] [Criterion 4] — DEFERRED: requires playtest

### Test-Criterion Traceability
| Criterion | Test | Status |
|-----------|------|--------|
| AC-1: [text] | [test file::test name] | COVERED |
| AC-2: [text] | Manual confirmation | COVERED |
| AC-3: [text] | — | UNTESTED |

### Test Evidence
**Story Type**: [Logic | Integration | Visual/Feel | UI | Config/Data | Not declared]
**Required evidence**: [unit test file | integration test or playtest | screenshot + sign-off | walkthrough doc | smoke check pass]
**Evidence found**: [YES — `[path]` | NO — BLOCKING | NO — ADVISORY]

### Deviations
[NONE] OR:
- BLOCKING: [description] — [GDD/ADR reference]
- ADVISORY: [description] — user accepted / flagged for tech debt

### Scope
[All changes within stated scope] OR:
- Extra files touched: [list] — [note whether valid or scope creep]

### Verdict: COMPLETE / COMPLETE WITH NOTES / BLOCKED
```

**Verdict definitions:**
- **COMPLETE**: all criteria pass, no blocking deviations
- **COMPLETE WITH NOTES**: all criteria pass, advisory deviations documented
- **BLOCKED**: failing criteria or blocking deviations must be resolved first
**结论定义：**
- **COMPLETE**：所有标准通过，无阻塞性偏差
- **COMPLETE WITH NOTES**：所有标准通过，建议性偏差已记录
- **BLOCKED**：必须先解决失败标准或阻塞性偏差

If the verdict is **BLOCKED**: do not proceed to Phase 7. List what must be
fixed. Offer to help fix the blocking items.
如果结论为 **BLOCKED**：不要继续至阶段 7。列出必须
修复的内容。提供帮助修复阻塞项。

---

## Phase 7: Update Story Status
## 阶段 7：更新故事状态

Use `AskUserQuestion` before writing anything:
- Prompt: "Verification complete. How do you want to proceed?"
- Options:
  - `Close the story — update file, mark Complete, log notes (Recommended)`
  - `Close and log advisory deviations as tech debt in docs/tech-debt-register.md`
  - `There are issues I want to fix first — don't close yet`
  - `Accept deviations as-is and close anyway`
在写入任何内容之前使用 `AskUserQuestion`：
- 提示："验证完成。您想如何继续？"
- 选项：
  - `关闭故事 — 更新文件，标记 Complete，记录备注（推荐）`
  - `关闭并将建议性偏差作为技术债务记录到 docs/tech-debt-register.md`
  - `有问题我想先修复 — 暂不关闭`
  - `按原样接受偏差并仍然关闭`

If "Close", "Close and log tech debt", or "Accept deviations": edit the story file.
If "Close and log tech debt": after updating the story file, also append the advisory deviations to `docs/tech-debt-register.md` (create the file if it does not exist).
If "Fix first": stop here and list what the user flagged. Do not write any files.
如果选择 "Close"、"Close and log tech debt" 或 "Accept deviations"：编辑故事文件。
如果选择 "Close and log tech debt"：在更新故事文件后，也将建议性偏差追加到 `docs/tech-debt-register.md`（如果文件不存在则创建）。
如果选择 "Fix first"：在此停止并列出用户标记的内容。不写入任何文件。

1. Update the status field: `Status: Complete`
2. Update the `Last Updated:` field in the story header to today's date (format: `YYYY-MM-DD`). If the field does not exist, add it after the `Status:` line.
3. Add a `## Completion Notes` section at the bottom:
1. 更新状态字段：`Status: Complete`
2. 将故事标头中的 `Last Updated:` 字段更新为今天的日期（格式：`YYYY-MM-DD`）。如果该字段不存在，在 `Status:` 行之后添加。
3. 在底部添加 `## Completion Notes` 章节：

```markdown
## Completion Notes
**Completed**: [date]
**Criteria**: [X/Y passing] ([any deferred items listed])
**Deviations**: [None] or [list of advisory deviations]
**Test Evidence**: [Logic: test file at path | Visual/Feel: evidence doc at path | None required (Config/Data)]
**Code Review**: [Pending / Complete / Skipped]
```
```markdown
## Completion Notes
**Completed**: [date]
**Criteria**: [X/Y passing] ([any deferred items listed])
**Deviations**: [None] or [list of advisory deviations]
**Test Evidence**: [Logic: test file at path | Visual/Feel: evidence doc at path | None required (Config/Data)]
**Code Review**: [Pending / Complete / Skipped]
```

4. If the user chose "Close and log tech debt": append each advisory deviation to `docs/tech-debt-register.md` in this format:
   ```
   - **[date]** ([story title]): [deviation description] — tracked from [story file path]
   ```
   Create the file with a `# Tech Debt Register` heading if it does not exist.
4. 如果用户选择 "Close and log tech debt"：将每个建议性偏差追加到 `docs/tech-debt-register.md`，格式如下：
   ```
   - **[date]** ([story title]): [deviation description] — tracked from [story file path]
   ```
   如果文件不存在，使用 `# Tech Debt Register` 标题创建该文件。

5. **Update `production/sprint-status.yaml`** (if it exists):
   - Find the entry matching this story's file path or ID
   - Set `status: done` and `completed: [today's date]`
   - Update the top-level `updated` field
   - This is a silent update — no extra approval needed (already approved in step above)
5. **更新 `production/sprint-status.yaml`**（如果存在）：
   - 找到匹配此故事文件路径或 ID 的条目
   - 设置 `status: done` 和 `completed: [today's date]`
   - 更新顶层 `updated` 字段
   - 这是静默更新 — 无需额外批准（已在上一步批准）

6. **Suggest a git commit**: Output a ready-to-use commit command covering the implementation files from the dev-story summary and the updated story file:
6. **建议 git 提交**：输出一个可直接使用的提交命令，覆盖 dev-story 摘要中的实现文件和更新后的故事文件：

```
Suggested commit:
git add [src/ and tests/ files changed during implementation] [story-file-path]
git commit -m "feat: [story title] ([TR-ID])"
```
```
Suggested commit:
git add [src/ and tests/ files changed during implementation] [story-file-path]
git commit -m "feat: [story title] ([TR-ID])"
```

The `validate-commit.sh` hook will verify design doc references and check for hardcoded values automatically.
`validate-commit.sh` 钩子将自动验证设计文档引用并检查硬编码值。

### Session State Update
### 会话状态更新

After updating the story file, silently append to
`production/session-state/active.md`:
更新故事文件后，静默追加到
`production/session-state/active.md`：

    ## Session Extract — /story-done [date]
    - Verdict: [COMPLETE / COMPLETE WITH NOTES / BLOCKED]
    - Story: [story file path] — [story title]
    - Tech debt logged: [N items, or "None"]
    - Next recommended: [next ready story title and path, or "None identified"]
    ## Session Extract — /story-done [date]
    - Verdict: [COMPLETE / COMPLETE WITH NOTES / BLOCKED]
    - Story: [story file path] — [story title]
    - Tech debt logged: [N items, or "None"]
    - Next recommended: [next ready story title and path, or "None identified"]

If `active.md` does not exist, create it with this block as the initial content.
Confirm in conversation: "Session state updated."
如果 `active.md` 不存在，使用此块作为初始内容创建它。
在对话中确认："会话状态已更新。"

---

## Phase 8: Surface the Next Story
## 阶段 8：呈现下一个故事

After completion, help the developer keep momentum:
完成后，帮助开发者保持节奏：

1. Read the current sprint plan from `production/sprints/`.
2. Find stories that are:
   - Status: READY or NOT STARTED
   - Not blocked by other incomplete stories
   - In the Must Have or Should Have tier
1. 从 `production/sprints/` 读取当前冲刺计划。
2. 查找符合以下条件的故事：
   - 状态：READY 或 NOT STARTED
   - 未被其他未完成的故事阻塞
   - 处于 Must Have 或 Should Have 层级

Present:
呈现：

```
### Next Up
The following stories are ready to pick up:
1. [Story name] — [1-line description] — Est: [X hrs]
2. [Story name] — [1-line description] — Est: [X hrs]

Run `/story-readiness [path]` to confirm a story is implementation-ready
before starting.
```
```
### Next Up
The following stories are ready to pick up:
1. [Story name] — [1-line description] — Est: [X hrs]
2. [Story name] — [1-line description] — Est: [X hrs]

Run `/story-readiness [path]` to confirm a story is implementation-ready
before starting.
```

If no more Must Have stories remain in this sprint (all are Complete or Blocked):
如果此冲刺中不再有 Must Have 故事（全部为 Complete 或 Blocked）：

```
### Sprint Close-Out Sequence

All Must Have stories are complete. QA sign-off is required before advancing.
Run these in order:

1. `/smoke-check sprint` — verify the critical path still works end-to-end
2. `/team-qa sprint` — full QA cycle: test case execution, bug triage, sign-off report
3. `/retrospective` — capture what went well, what didn't, and action items for the next sprint
4. `/gate-check` — advance to the next phase once QA approves (only if advancing a phase)
5. `/sprint-plan new` — plan the next sprint, incorporating velocity data and retrospective action items

Do not run `/gate-check` until `/team-qa` returns APPROVED or APPROVED WITH CONDITIONS.
```
```
### Sprint Close-Out Sequence

All Must Have stories are complete. QA sign-off is required before advancing.
Run these in order:

1. `/smoke-check sprint` — verify the critical path still works end-to-end
2. `/team-qa sprint` — full QA cycle: test case execution, bug triage, sign-off report
3. `/retrospective` — capture what went well, what didn't, and action items for the next sprint
4. `/gate-check` — advance to the next phase once QA approves (only if advancing a phase)
5. `/sprint-plan new` — plan the next sprint, incorporating velocity data and retrospective action items

Do not run `/gate-check` until `/team-qa` returns APPROVED or APPROVED WITH CONDITIONS.
```

If there are Should Have stories still unstarted, surface them alongside the close-out sequence so the user can choose: close the sprint now, or pull in more work first.
如果还有 Should Have 故事未开始，将其与收尾序列一起呈现，以便用户选择：现在关闭冲刺，或先引入更多工作。

If no more stories are ready but Must Have stories are still In Progress (not Complete):
"No more stories ready to start — [N] Must Have stories still in progress. Continue implementing those before sprint close-out."
如果没有更多就绪的故事但 Must Have 故事仍进行中（未 Complete）：
"没有更多就绪的故事可开始 — 仍有 [N] 个 Must Have 故事在进行中。在冲刺收尾之前继续实现它们。"

---

## Collaborative Protocol
## 协作协议

- **Never mark a story complete without user approval** — Phase 7 requires an
  explicit "yes" before any file is edited.
- **Never auto-fix failing criteria** — report them and ask what to do.
- **Deviations are facts, not judgments** — present them neutrally; the user
  decides if they are acceptable.
- **BLOCKED verdict is advisory** — the user can override and mark complete
  anyway; document the risk explicitly if they do.
- Use `AskUserQuestion` for the code review prompt and for batching manual
  criteria confirmations.
- **未经用户批准切勿将故事标记为完成** — 阶段 7 在编辑任何文件之前要求
  明确的 "yes"。
- **切勿自动修复失败标准** — 报告它们并询问如何处理。
- **偏差是事实，而非判断** — 中立地呈现它们；由用户
  决定它们是否可接受。
- **BLOCKED 结论是建议性的** — 用户可以覆盖并仍标记为完成；
  如果他们这样做，明确记录风险。
- 使用 `AskUserQuestion` 进行代码评审提示和批量手动
  标准确认。

---

## Recommended Next Steps
## 推荐的后续步骤

- Run `/story-readiness [next-story-path]` to validate the next story before starting implementation
- If all Must Have stories are complete: run `/smoke-check sprint` → `/team-qa sprint` → `/gate-check`
- If tech debt was logged: track it via `/tech-debt` to keep the register current
- 运行 `/story-readiness [next-story-path]` 在开始实现之前验证下一个故事
- 如果所有 Must Have 故事都已完成：运行 `/smoke-check sprint` → `/team-qa sprint` → `/gate-check`
- 如果记录了技术债务：通过 `/tech-debt` 跟踪它以保持登记表最新
