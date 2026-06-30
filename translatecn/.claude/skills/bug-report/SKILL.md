---
name: bug-report
description: "Creates a structured bug report from a description, or analyzes code to identify potential bugs. Ensures every bug report has full reproduction steps, severity assessment, and context."
argument-hint: "[description] | analyze [path-to-file]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---
---
name: bug-report
description: "从描述创建结构化缺陷报告，或分析代码以识别潜在缺陷。确保每个缺陷报告都有完整的复现步骤、严重度评估和上下文。"
argument-hint: "[description] | analyze [path-to-file]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---

## Phase 1: Parse Arguments
## 阶段 1：解析参数

Determine the mode from the argument:
从参数确定模式：

- No keyword → **Description Mode**: generate a structured bug report from the provided description
- `analyze [path]` → **Analyze Mode**: read the target file(s) and identify potential bugs
- `verify [BUG-ID]` → **Verify Mode**: confirm a reported fix actually resolved the bug
- `close [BUG-ID]` → **Close Mode**: mark a verified bug as closed with resolution record
- 无关键字 → **Description Mode**：从提供的描述生成结构化缺陷报告
- `analyze [path]` → **Analyze Mode**：读取目标文件并识别潜在缺陷
- `verify [BUG-ID]` → **Verify Mode**：确认报告的修复确实解决了缺陷
- `close [BUG-ID]` → **Close Mode**：将已验证的缺陷标记为关闭并附解决记录

If no argument is provided, ask the user for a bug description before proceeding.
如果未提供参数，在继续之前向用户询问缺陷描述。

---

## Phase 2A: Description Mode
## 阶段 2A：Description 模式

1. **Parse the description** for key information: what broke, when, how to reproduce it, and what the expected behavior is.
2. **Search the codebase** for related files using Grep/Glob to add context (affected system, likely files).
3. **Draft the bug report**:
1. **解析描述** 获取关键信息：什么损坏了、何时、如何复现它，以及预期行为是什么。
2. **搜索代码库** 使用 Grep/Glob 查找相关文件以添加上下文（受影响的系统、可能的文件）。
3. **起草缺陷报告**：

```markdown
# Bug Report

## Summary
**Title**: [Concise, descriptive title]
**ID**: BUG-[NNNN]
**Severity**: [S1-Critical / S2-Major / S3-Minor / S4-Trivial]
**Priority**: [P1-Immediate / P2-Next Sprint / P3-Backlog / P4-Wishlist]
**Status**: Open
**Reported**: [Date]
**Reporter**: [Name]

## Classification
- **Category**: [Gameplay / UI / Audio / Visual / Performance / Crash / Network]
- **System**: [Which game system is affected]
- **Frequency**: [Always / Often (>50%) / Sometimes (10-50%) / Rare (<10%)]
- **Regression**: [Yes/No/Unknown -- was this working before?]

## Environment
- **Build**: [Version or commit hash]
- **Platform**: [OS, hardware if relevant]
- **Scene/Level**: [Where in the game]
- **Game State**: [Relevant state -- inventory, quest progress, etc.]

## Reproduction Steps
**Preconditions**: [Required state before starting]

1. [Exact step 1]
2. [Exact step 2]
3. [Exact step 3]

**Expected Result**: [What should happen]
**Actual Result**: [What actually happens]

## Technical Context
- **Likely affected files**: [List of files based on codebase search]
- **Related systems**: [What other systems might be involved]
- **Possible root cause**: [If identifiable from the description]

## Evidence
- **Logs**: [Relevant log output if available]
- **Visual**: [Description of visual evidence]

## Related Issues
- [Links to related bugs or design documents]

## Notes
[Any additional context or observations]
```
```markdown
# Bug Report

## Summary
**Title**: [Concise, descriptive title]
**ID**: BUG-[NNNN]
**Severity**: [S1-Critical / S2-Major / S3-Minor / S4-Trivial]
**Priority**: [P1-Immediate / P2-Next Sprint / P3-Backlog / P4-Wishlist]
**Status**: Open
**Reported**: [Date]
**Reporter**: [Name]

## Classification
- **Category**: [Gameplay / UI / Audio / Visual / Performance / Crash / Network]
- **System**: [Which game system is affected]
- **Frequency**: [Always / Often (>50%) / Sometimes (10-50%) / Rare (<10%)]
- **Regression**: [Yes/No/Unknown -- was this working before?]

## Environment
- **Build**: [Version or commit hash]
- **Platform**: [OS, hardware if relevant]
- **Scene/Level**: [Where in the game]
- **Game State**: [Relevant state -- inventory, quest progress, etc.]

## Reproduction Steps
**Preconditions**: [Required state before starting]

1. [Exact step 1]
2. [Exact step 2]
3. [Exact step 3]

**Expected Result**: [What should happen]
**Actual Result**: [What actually happens]

## Technical Context
- **Likely affected files**: [List of files based on codebase search]
- **Related systems**: [What other systems might be involved]
- **Possible root cause**: [If identifiable from the description]

## Evidence
- **Logs**: [Relevant log output if available]
- **Visual**: [Description of visual evidence]

## Related Issues
- [Links to related bugs or design documents]

## Notes
[Any additional context or observations]
```

---

## Phase 2B: Analyze Mode
## 阶段 2B：Analyze 模式

1. **Read the target file(s)** specified in the argument.
2. **Identify potential bugs**: null references, off-by-one errors, race conditions, unhandled edge cases, resource leaks, incorrect state transitions.
3. **For each potential bug**, generate a bug report using the template above, with the likely trigger scenario and recommended fix filled in.
1. **读取参数中指定的目标文件**。
2. **识别潜在缺陷**：空引用、差一错误、竞态条件、未处理的边缘情况、资源泄漏、不正确的状态转换。
3. **对于每个潜在缺陷**，使用上面的模板生成缺陷报告，并填入可能的触发场景和建议的修复。

---

## Phase 2C: Verify Mode
## 阶段 2C：Verify 模式

Read `production/qa/bugs/[BUG-ID].md`. Extract the reproduction steps and expected result.
读取 `production/qa/bugs/[BUG-ID].md`。提取复现步骤和预期结果。

1. **Re-run reproduction steps** — use Grep/Glob to check whether the root cause code path still exists as described. If the fix removed or changed it, note the change.
2. **Run the related test** — if the bug's system has a test file in `tests/`, run it via Bash and report pass/fail.
3. **Check for regression** — grep the codebase for any new occurrence of the pattern that caused the bug.
1. **重新运行复现步骤** — 使用 Grep/Glob 检查根因代码路径是否仍按描述存在。如果修复移除或更改了它，注明变更。
2. **运行相关测试** — 如果缺陷的系统在 `tests/` 中有测试文件，通过 Bash 运行它并报告通过/失败。
3. **检查回归** — grep 代码库查找导致缺陷的模式是否有任何新出现。

Produce a verification verdict:
生成验证结论：

- **VERIFIED FIXED** — reproduction steps no longer produce the bug; related tests pass
- **STILL PRESENT** — bug reproduces as described; fix did not resolve the issue
- **CANNOT VERIFY** — automated checks inconclusive; manual playtest required
- **VERIFIED FIXED** — 复现步骤不再产生缺陷；相关测试通过
- **STILL PRESENT** — 缺陷按描述复现；修复未解决问题
- **CANNOT VERIFY** — 自动化检查不明确；需要手动试玩

Ask: "May I update `production/qa/bugs/[BUG-ID].md` to set Status: Verified Fixed / Still Present / Cannot Verify?"
询问："May I update `production/qa/bugs/[BUG-ID].md` to set Status: Verified Fixed / Still Present / Cannot Verify?"

If STILL PRESENT: reopen the bug, set Status back to Open, and suggest re-running `/hotfix [BUG-ID]`.
如果 STILL PRESENT：重新开放缺陷，将 Status 设置回 Open，并建议重新运行 `/hotfix [BUG-ID]`。

---

## Phase 2D: Close Mode
## 阶段 2D：Close 模式

Read `production/qa/bugs/[BUG-ID].md`. Confirm Status is `Verified Fixed` before closing. If status is anything else, stop: "Bug [ID] must be Verified Fixed before it can be closed. Run `/bug-report verify [BUG-ID]` first."
读取 `production/qa/bugs/[BUG-ID].md`。在关闭之前确认 Status 为 `Verified Fixed`。如果状态是其他任何值，停止："Bug [ID] must be Verified Fixed before it can be closed. Run `/bug-report verify [BUG-ID]` first."

Append a closure record to the bug file:
向缺陷文件追加关闭记录：

```markdown
## Closure Record
**Closed**: [date]
**Resolution**: Fixed — [one-line description of what was changed]
**Fix commit / PR**: [if known]
**Verified by**: qa-tester
**Closed by**: [user]
**Regression test**: [test file path, or "Manual verification"]
**Status**: Closed
```
```markdown
## Closure Record
**Closed**: [date]
**Resolution**: Fixed — [one-line description of what was changed]
**Fix commit / PR**: [if known]
**Verified by**: qa-tester
**Closed by**: [user]
**Regression test**: [test file path, or "Manual verification"]
**Status**: Closed
```

Update the top-level `**Status**: Open` field to `**Status**: Closed`.
将顶层的 `**Status**: Open` 字段更新为 `**Status**: Closed`。

Ask: "May I update `production/qa/bugs/[BUG-ID].md` to mark it Closed?"
询问："May I update `production/qa/bugs/[BUG-ID].md` to mark it Closed?"

After closing, check `production/qa/bug-triage-*.md` — if the bug appears in an open triage report, note: "Bug [ID] is referenced in the triage report. Run `/bug-triage` to refresh the open bug count."
关闭后，检查 `production/qa/bug-triage-*.md` — 如果缺陷出现在开放的分诊报告中，注明："Bug [ID] is referenced in the triage report. Run `/bug-triage` to refresh the open bug count."

---

## Phase 3: Save Report
## 阶段 3：保存报告

Present the completed bug report(s) to the user.
向用户呈现完成的缺陷报告。

Ask: "May I write this to `production/qa/bugs/BUG-[NNNN].md`?"
询问："May I write this to `production/qa/bugs/BUG-[NNNN].md`?"

If yes, write the file, creating the directory if needed. Verdict: **COMPLETE** — bug report filed.
如果是，写入文件，如果需要则创建目录。Verdict: **COMPLETE** — 缺陷报告已提交。

If no, stop here. Verdict: **BLOCKED** — user declined write.
如果否，在此停止。Verdict: **BLOCKED** — 用户拒绝写入。

---

## Phase 4: Next Steps
## 阶段 4：后续步骤

After saving, suggest based on mode:
保存后，根据模式建议：

**After filing (Description/Analyze mode):**
- Run `/bug-triage` to prioritize alongside existing open bugs
- If S1 or S2: run `/hotfix [BUG-ID]` for emergency fix workflow
**提交之后（Description/Analyze 模式）：**
- 运行 `/bug-triage` 与现有开放缺陷一起确定优先级
- 如果是 S1 或 S2：运行 `/hotfix [BUG-ID]` 进行紧急修复工作流

**After fixing the bug (developer confirms fix is in):**
- Run `/bug-report verify [BUG-ID]` — confirm the fix actually works before closing
- Never mark a bug closed without verification — a fix that doesn't verify is still Open
**修复缺陷之后（开发者确认修复已就位）：**
- 运行 `/bug-report verify [BUG-ID]` — 在关闭之前确认修复确实有效
- 切勿在未验证的情况下将缺陷标记为关闭 — 未验证通过的修复仍然是 Open

**After verify returns VERIFIED FIXED:**
- Run `/bug-report close [BUG-ID]` — write the closure record and update status
- Run `/bug-triage` to refresh the open bug count and remove it from the active list
**verify 返回 VERIFIED FIXED 之后：**
- 运行 `/bug-report close [BUG-ID]` — 写入关闭记录并更新状态
- 运行 `/bug-triage` 刷新开放缺陷计数并将其从活跃列表中移除
