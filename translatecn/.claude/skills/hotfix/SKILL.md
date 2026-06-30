---
name: hotfix
description: "Emergency fix workflow that bypasses normal sprint processes with a full audit trail. Creates hotfix branch, tracks approvals, and ensures the fix is backported correctly."
argument-hint: "[bug-id or description]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model: sonnet
---
---
名称：hotfix
描述："绕过正常冲刺流程并带完整审计追踪的紧急修复工作流。创建热修复分支，跟踪批准，并确保修复正确回传。"
argument-hint："[bug-id or description]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model：sonnet
---

> **Explicit invocation only**: This skill should only run when the user explicitly requests it with `/hotfix`. Do not auto-invoke based on context matching.
> **仅显式调用**：此技能仅应在用户明确请求 `/hotfix` 时运行。不要基于上下文匹配自动调用。

## Phase 1: Assess Severity
## 阶段 1：评估严重性

Read the bug description or ID. Assess severity using these criteria:
读取 bug 描述或 ID。使用以下标准评估严重性：

- **S1 (Critical)**: Game unplayable, data loss, security vulnerability
- **S1（关键）**：游戏无法游玩、数据丢失、安全漏洞
- **S2 (Major)**: Significant feature broken, workaround exists
- **S2（重大）**：重要功能损坏，存在变通方法
- **S3 or lower**: Minor issue — normal bug fix workflow applies
- **S3 或更低**：次要问题 —— 适用正常 bug 修复工作流

Confirm with `AskUserQuestion`:
使用 `AskUserQuestion` 确认：
- Prompt: "I've assessed this as **[assessed severity]** — [brief rationale]. Confirm severity to proceed:"
- 提示："我已评估为 **[评估的严重性]** —— [简短理由]。确认严重性以继续："
- Options:
- 选项：
  - `[A] S1 (Critical) — game unplayable, data loss, or security issue`
  - `[A] S1（关键）—— 游戏无法游玩、数据丢失或安全问题`
  - `[B] S2 (Major) — significant feature broken, workaround exists`
  - `[B] S2（重大）—— 重要功能损坏，存在变通方法`
  - `[C] S3 or lower — redirect to normal bug fix workflow`
  - `[C] S3 或更低 —— 重定向到正常 bug 修复工作流`

If [C]: stop. Verdict: **REDIRECTED** — use the normal bug fix workflow for S3 and below.
若 [C]：停止。裁定：**REDIRECTED** —— 对 S3 及以下使用正常 bug 修复工作流。

---

## Phase 2: Create Hotfix Record
## 阶段 2：创建热修复记录

Draft the hotfix record:
起草热修复记录：

```markdown
## Hotfix: [Short Description]
Date: [Date]
Severity: [S1/S2]
Reporter: [Who found it]
Status: IN PROGRESS

### Problem
[Clear description of what is broken and the player impact]

### Root Cause
[To be filled during investigation]

### Fix
[To be filled during implementation]

### Testing
[What was tested and how]

### Approvals
- [ ] Fix reviewed by lead-programmer
- [ ] Regression test passed (qa-tester)
- [ ] Release approved (producer)

### Rollback Plan
[How to revert if the fix causes new issues]
```
```markdown
## 热修复：[简短描述]
日期：[日期]
严重性：[S1/S2]
报告者：[发现者]
状态：进行中

### 问题
[对什么损坏及对玩家影响的清晰描述]

### 根本原因
[调查期间填写]

### 修复
[实施期间填写]

### 测试
[测试了什么及如何测试]

### 批准
- [ ] 修复由 lead-programmer 评审
- [ ] 回归测试通过（qa-tester）
- [ ] 发布已批准（producer）

### 回滚计划
[若修复引起新问题如何回退]
```

Ask: "May I write this to `production/hotfixes/hotfix-[date]-[short-name].md`?"
询问："我可以将此写入 `production/hotfixes/hotfix-[date]-[short-name].md` 吗？"

If yes, write the file, creating the directory if needed.
若是，写入文件，必要时创建目录。

---

## Phase 3: Create Hotfix Branch
## 阶段 3：创建热修复分支

Check whether this is a git repository:
检查这是否是 git 仓库：

`Bash: git rev-parse --is-inside-work-tree 2>/dev/null`

If this command fails or returns empty: note "Not a git repository — create the branch manually." and skip branch creation.
若此命令失败或返回空：注明"非 git 仓库 —— 手动创建分支。"并跳过分支创建。

If the check passes, use `AskUserQuestion` before creating the branch:
若检查通过，在创建分支前使用 `AskUserQuestion`：
- Prompt: "Ready to create hotfix branch 'hotfix/[short-name]' from [base-ref]?"
- 提示："准备好从 [base-ref] 创建热修复分支 'hotfix/[short-name]' 吗？"
- Options:
- 选项：
  - `[A] Yes — create branch`
  - `[A] 是 —— 创建分支`
  - `[B] Use a different base ref — I'll specify it`
  - `[B] 使用不同基础引用 —— 我来指定`
  - `[C] Skip — I'll create the branch myself`
  - `[C] 跳过 —— 我自己创建分支`

Only run `git checkout -b hotfix/[short-name] [base-ref]` if user selects [A]. If [B]: ask the user for the base ref, then run the command with that ref. If [C]: skip branch creation and proceed to Phase 4.
仅当用户选择 [A] 时运行 `git checkout -b hotfix/[short-name] [base-ref]`。若 [B]：询问用户基础引用，然后用该引用运行命令。若 [C]：跳过分支创建并进入阶段 4。

---

## Phase 4: Investigate and Implement
## 阶段 4：调查和实施

Focus on the minimal change that resolves the issue. Do NOT refactor, clean up, or add features alongside the hotfix.
专注于解决问题的最小变更。不要在热修复中重构、清理或添加功能。

Validate the fix by running targeted tests for the affected system. Check for regressions in adjacent systems.
通过对受影响系统运行定向测试来验证修复。检查相邻系统的回归。

Update the hotfix record with root cause, fix details, and test results.
用根本原因、修复详情和测试结果更新热修复记录。

---

## Phase 5: Collect Approvals
## 阶段 5：收集批准

Use the Task tool to request sign-off in parallel:
使用 Task 工具并行请求签字：

- `subagent_type: lead-programmer` — Review the fix for correctness and side effects
- `subagent_type: lead-programmer` —— 评审修复的正确性和副作用
- `subagent_type: qa-tester` — Run targeted regression tests on the affected system
- `subagent_type: qa-tester` —— 对受影响系统运行定向回归测试
- `subagent_type: producer` — Approve deployment timing and communication plan
- `subagent_type: producer` —— 批准部署时机和沟通计划

All three must return APPROVE before proceeding. If any returns CONCERNS or REJECT, do not deploy — surface the issue and resolve it first.
三者都必须返回 APPROVE 才能继续。若任一返回 CONCERNS 或 REJECT，不要部署 —— 暴露问题并先解决。

---

## Phase 5b: QA Re-Entry Gate
## 阶段 5b：QA 重入门禁

After approvals, determine the QA scope required before deploying the hotfix. Spawn `qa-lead` via Task with:
批准后，确定部署热修复前所需的 QA 范围。通过 Task 派生 `qa-lead`，传递：
- The hotfix description and affected system
- 热修复描述和受影响系统
- The regression test results from Phase 5
- 阶段 5 的回归测试结果
- A list of all systems that touch the changed files (use Grep to find callers)
- 触及变更文件的所有系统列表（使用 Grep 查找调用方）

Ask qa-lead: **Is a full smoke check sufficient, or does this fix require a targeted team-qa pass?**
询问 qa-lead：**完整的冒烟检查是否足够，还是此修复需要定向的 team-qa 通过？**

Apply the verdict:
应用裁定：
- **Smoke check sufficient** — run `/smoke-check` against the hotfix build. If PASS, proceed to Phase 6.
- **冒烟检查足够** —— 对热修复构建运行 `/smoke-check`。若 PASS，进入阶段 6。
- **Targeted QA pass required** — run `/team-qa [affected-system]` scoped to the changed system only. If QA returns APPROVED or APPROVED WITH CONDITIONS, proceed to Phase 6.
- **需要定向 QA 通过** —— 运行 `/team-qa [affected-system]`，范围仅限于变更的系统。若 QA 返回 APPROVED 或 APPROVED WITH CONDITIONS，进入阶段 6。
- **Full QA required** — S1 fixes that touch core systems may require a full `/team-qa sprint`. This delays deployment but prevents a bad patch.
- **需要完整 QA** —— 触及核心系统的 S1 修复可能需要完整的 `/team-qa sprint`。这会延迟部署但防止糟糕的补丁。

Do not skip this gate. A hotfix that breaks something else is worse than the original bug.
不要跳过此门禁。破坏其他东西的热修复比原始 bug 更糟糕。

---

## Phase 6: Update Bug Status and Deploy
## 阶段 6：更新 Bug 状态并部署

Update the original bug file if one exists:
若存在原始 bug 文件则更新它：

```markdown
## Fix Record
**Fixed in**: hotfix/[branch-name] — [commit hash or description]
**Fixed date**: [date]
**Status**: Fixed — Pending Verification
```
```markdown
## 修复记录
**修复于**：hotfix/[branch-name] —— [提交哈希或描述]
**修复日期**：[日期]
**状态**：已修复 —— 待验证
```

Set `**Status**: Fixed — Pending Verification` in the bug file header.
在 bug 文件头中设置 `**Status**: Fixed — Pending Verification`。

Output a deployment summary:
输出部署摘要：

```
## Hotfix Ready to Deploy: [short-name]

**Severity**: [S1/S2]
**Root cause**: [one line]
**Fix**: [one line]
**QA gate**: [Smoke check PASS / Team-QA APPROVED]
**Approvals**: lead-programmer ✓ / qa-tester ✓ / producer ✓
**Rollback plan**: [from Phase 2 record]

Merge to: release branch AND development branch
Next: /bug-report verify [BUG-ID] after deploy to confirm resolution
```
```
## 热修复准备部署：[short-name]

**严重性**：[S1/S2]
**根本原因**：[一行]
**修复**：[一行]
**QA 门禁**：[冒烟检查 PASS / Team-QA APPROVED]
**批准**：lead-programmer ✓ / qa-tester ✓ / producer ✓
**回滚计划**：[来自阶段 2 记录]

合并到：发布分支 AND 开发分支
下一步：部署后运行 /bug-report verify [BUG-ID] 确认解决
```

### Rules
### 规则
- Hotfixes must be the MINIMUM change to fix the issue — no cleanup, no refactoring
- 热修复必须是解决问题的最小变更 —— 无清理、无重构
- Every hotfix must have a rollback plan documented before deployment
- 每个热修复在部署前必须有记录的回滚计划
- Hotfix branches merge to BOTH the release branch AND the development branch
- 热修复分支合并到发布分支和开发分支两者
- All hotfixes require a post-incident review within 48 hours
- 所有热修复需要在 48 小时内进行事后复盘
- If the fix is complex enough to need more than 4 hours, escalate to `technical-director`
- 若修复复杂到需要超过 4 小时，升级到 `technical-director`

---

## Phase 7: Post-Deploy Verification
## 阶段 7：部署后验证

After deploying, run `/bug-report verify [BUG-ID]` to confirm the fix resolved the issue in the deployed build.
部署后，运行 `/bug-report verify [BUG-ID]` 确认修复在部署的构建中解决了问题。

If VERIFIED FIXED: run `/bug-report close [BUG-ID]` to formally close it.
若 VERIFIED FIXED：运行 `/bug-report close [BUG-ID]` 正式关闭它。
If STILL PRESENT: the hotfix failed — immediately re-open, assess rollback, and escalate.
若 STILL PRESENT：热修复失败 —— 立即重新打开，评估回滚并升级。

Schedule a post-incident review within 48 hours using `/retrospective hotfix`.
使用 `/retrospective hotfix` 安排 48 小时内的事后复盘。

Use `AskUserQuestion`:
使用 `AskUserQuestion`：
- Prompt: "Hotfix complete. What's the next step?"
- 提示："热修复完成。下一步是什么？"
- Options:
- 选项：
  - `[A] Run /smoke-check to verify the fix`
  - `[A] 运行 /smoke-check 验证修复`
  - `[B] Run /patch-notes to document this hotfix`
  - `[B] 运行 /patch-notes 记录此热修复`
  - `[C] Stop here`
  - `[C] 在此停止`
