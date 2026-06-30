---
name: day-one-patch
description: "Prepare a day-one patch for a game launch. Scopes, prioritises, implements, and QA-gates a focused patch addressing known issues discovered after gold master but before or immediately after public launch. Treats the patch as a mini-sprint with its own QA gate and rollback plan."
argument-hint: "[scope: known-bugs | cert-feedback | all]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model: sonnet
---
---
名称：day-one-patch
描述："为游戏发行准备首日补丁。范围界定、优先级排序、实现并通过 QA 门禁一个聚焦的补丁，处理在 gold master 之后但在公开发行之前或刚发行后发现的已知问题。将补丁视为带自身 QA 门禁与回滚计划的迷你冲刺。"
argument-hint："[scope: known-bugs | cert-feedback | all]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model：sonnet
---

# Day-One Patch
# 首日补丁

Every shipped game has a day-one patch. Planning it before launch day prevents
chaos. This skill scopes the patch to only what is safe and necessary, gates it
through a lightweight QA pass, and ensures a rollback plan exists before anything
ships. It is a mini-sprint — not a hotfix, not a full sprint.
每款已发行游戏都有首日补丁。在发行日之前规划它能避免混乱。本技能将补丁范围
界定为仅安全且必要的内容，通过轻量级 QA 检查，并确保在任何内容发行之前存在
回滚计划。它是迷你冲刺 —— 不是热修，也不是完整冲刺。

**When to run:**
- After the gold master build is locked (cert approved or launch candidate tagged)
- When known bugs exist that are too risky to address in the gold master
- When cert feedback requires minor fixes post-submission
- When a pre-launch playtest surfaces must-fix issues after the release gate passed
**何时运行：**
- 在 gold master 构建锁定后（cert 已批准或发行候选已打标签）
- 当存在已知 bug 但在 gold master 中修复风险过大
- 当 cert 反馈要求在提交后做小修复
- 当发行前试玩暴露了在发行门禁通过之后必须修复的问题

**Day-one patch scope rules:**
- Only P1/P2 bugs that are SAFE to fix quickly
- No new features — this is fix-only
- No refactoring — minimum viable change
- Any fix that requires more than 4 hours of dev time belongs in patch 1.1, not day-one
**首日补丁范围规则：**
- 仅限能快速安全修复的 P1/P2 bug
- 不加新功能 —— 仅限修复
- 不重构 —— 最小可行变更
- 任何需要超过 4 小时开发时间的修复应归入 1.1 补丁，而非首日补丁

**Output:** `production/releases/day-one-patch-[version].md`
**产出：** `production/releases/day-one-patch-[version].md`

---

## Phase 1: Load Release Context
## 阶段 1：加载发行上下文

Read:
- `production/stage.txt` — confirm project is in Release stage
- The most recent file in `production/gate-checks/` — read the release gate verdict
- `production/qa/bugs/*.md` — load all bugs with Status: Open or Fixed — Pending Verification
- `production/sprints/` most recent — understand what shipped
- `production/security/security-audit-*.md` most recent — check for any open security items
读取：
- `production/stage.txt` —— 确认项目处于 Release 阶段
- `production/gate-checks/` 中最新文件 —— 读取发行门禁结论
- `production/qa/bugs/*.md` —— 加载所有 Status 为 Open 或 Fixed — Pending Verification 的 bug
- `production/sprints/` 中最新的 —— 了解发行了什么
- `production/security/security-audit-*.md` 中最新的 —— 检查有无未关闭的安全项

If `production/stage.txt` is not `Release` or `Polish`:
> "Day-one patch prep is for Release-stage projects. Current stage: [stage]. This skill is not appropriate until you are approaching launch."
若 `production/stage.txt` 不是 `Release` 或 `Polish`：
> "Day-one patch prep is for Release-stage projects. Current stage: [stage]. This skill is not appropriate until you are approaching launch."

---

## Phase 2: Scope the Patch
## 阶段 2：界定补丁范围

### Step 2a — Classify open bugs for patch inclusion
### 步骤 2a —— 对开放 bug 分类以决定是否纳入补丁

For each open bug, evaluate:
对每个开放 bug 评估：

| Criterion | Include in day-one? |
|-----------|-------------------|
| S1 or S2 severity | Yes — must include if safe to fix |
| P1 priority | Yes |
| Fix estimated < 4 hours | Yes |
| Fix requires architecture change | No — defer to 1.1 |
| Fix introduces new code paths | No — too risky |
| Fix is data/config only (no code change) | Yes — very low risk |
| Cert feedback requirement | Yes — required for platform approval |
| S3/S4 severity | Only if trivial config fix; otherwise defer |
| 标准 | 纳入首日补丁？ |
|-----------|-------------------|
| S1 或 S2 严重度 | 是 —— 若可安全修复则必须纳入 |
| P1 优先级 | 是 |
| 修复预估 < 4 小时 | 是 |
| 修复需架构变更 | 否 —— 推迟到 1.1 |
| 修复引入新代码路径 | 否 —— 风险过大 |
| 修复仅涉及数据/配置（无代码变更） | 是 —— 风险极低 |
| Cert 反馈要求 | 是 —— 平台审批所需 |
| S3/S4 严重度 | 仅当是琐碎配置修复；否则推迟 |

### Step 2b — Present patch scope to user
### 步骤 2b —— 向用户展示补丁范围

Use `AskUserQuestion`:
- Prompt: "Based on open bugs and cert feedback, here is the proposed day-one patch scope. Does this look right?"
- Show: table of included bugs (ID, severity, description, estimated effort)
- Show: table of deferred bugs (ID, severity, reason deferred)
- Options: `[A] Approve this scope` / `[B] Adjust — I want to add or remove items` / `[C] No day-one patch needed`
使用 `AskUserQuestion`：
- 提示："Based on open bugs and cert feedback, here is the proposed day-one patch scope. Does this look right?"
- 展示：纳入 bug 表（ID、严重度、描述、预估工作量）
- 展示：推迟 bug 表（ID、严重度、推迟原因）
- 选项：`[A] Approve this scope` / `[B] Adjust — I want to add or remove items` / `[C] No day-one patch needed`

If [C]: output "No day-one patch required. Proceed to `/launch-checklist`." Stop.
若 [C]：输出 "No day-one patch required. Proceed to `/launch-checklist`." 停止。

### Step 2c — Check total scope
### 步骤 2c —— 检查总范围

Sum estimated effort. If total exceeds 1 day of work:
> "⚠️ Patch scope is [N hours] — this exceeds a safe day-one window. Consider deferring lower-priority items to patch 1.1. A bloated day-one patch introduces more risk than it removes."
求和预估工作量。若总数超过 1 个工作日：
> "⚠️ Patch scope is [N hours] — this exceeds a safe day-one window. Consider deferring lower-priority items to patch 1.1. A bloated day-one patch introduces more risk than it removes."

Use `AskUserQuestion` to confirm proceeding or reduce scope.
使用 `AskUserQuestion` 确认继续或缩减范围。

---

## Phase 3: Rollback Plan
## 阶段 3：回滚计划

Before any code is written, define the rollback procedure. This is non-negotiable.
在任何代码编写之前，定义回滚流程。这不容商量。

Spawn `release-manager` via Task. Ask them to produce a rollback plan covering:
- How to revert to the gold master build on each target platform
- Platform-specific rollback constraints (some platforms cannot roll back cert builds)
- Who is responsible for triggering the rollback
- What player communication is required if a rollback occurs
通过 Task 启动 `release-manager`。请其产出覆盖以下内容的回滚计划：
- 如何在每个目标平台上回滚到 gold master 构建
- 平台特定回滚约束（某些平台无法回滚 cert 构建）
- 谁负责触发回滚
- 若发生回滚需要向玩家传达什么

Present the rollback plan. Ask: "May I write this rollback plan to `production/releases/rollback-plan-[version].md`?"
展示回滚计划。询问："May I write this rollback plan to `production/releases/rollback-plan-[version].md`?"

Do not proceed to Phase 4 until the rollback plan is written.
回滚计划未写入之前不要进入阶段 4。

---

## Phase 4: Implement Fixes
## 阶段 4：实现修复

For each bug in the approved scope, spawn a focused implementation loop:
对批准范围内的每个 bug，启动一个聚焦实现循环：

1. Spawn `lead-programmer` via Task with:
   - The bug report (exact reproduction steps and root cause if known)
   - The constraint: minimum viable fix only, no cleanup
   - The affected files (from bug report Technical Context section)
1. 通过 Task 启动 `lead-programmer`，附带：
   - bug 报告（确切复现步骤及已知根因）
   - 约束：仅最小可行修复，无清理
   - 受影响文件（来自 bug 报告的 Technical Context 段落）

2. The lead-programmer implements and runs targeted tests.
2. lead-programmer 实现并运行针对性测试。

3. Spawn `qa-tester` via Task to verify: does the bug reproduce after the fix?
3. 通过 Task 启动 `qa-tester` 验证：修复后 bug 是否仍能复现？

For config/data-only fixes: make the change directly (no programmer agent needed). Confirm the value changed and re-run any relevant smoke test.
对于仅配置/数据的修复：直接修改（无需程序员智能体）。确认数值已变更并重跑相关
smoke 测试。

---

## Phase 5: Patch QA Gate
## 阶段 5：补丁 QA 门禁

This is a lightweight QA pass — not a full `/team-qa`. The patch is already QA-approved from the release gate; we are only re-verifying the changed areas.
这是轻量级 QA 检查 —— 不是完整 `/team-qa`。补丁已在发行门禁通过 QA 批准；
我们只重新验证变更区域。

Spawn `qa-lead` via Task with:
- List of all changed files
- List of bugs fixed (with verification status from Phase 4)
- The smoke check scope for the affected systems
通过 Task 启动 `qa-lead`，附带：
- 所有变更文件列表
- 已修复 bug 列表（含阶段 4 验证状态）
- 受影响系统的 smoke 检查范围

Ask qa-lead to determine: **Is a targeted smoke check sufficient, or do any fixes touch systems that require a broader regression?**
请 qa-lead 决定：**针对性 smoke 检查是否足够，或某些修复触及需要更广回归的系统？**

Run the required QA scope:
- **Targeted smoke check** — run `/smoke-check [affected-systems]`
- **Broader regression** — run targeted tests in `tests/unit/` and `tests/integration/` for affected systems
运行所需 QA 范围：
- **针对性 smoke 检查** —— 运行 `/smoke-check [affected-systems]`
- **更广回归** —— 对受影响系统运行 `tests/unit/` 和 `tests/integration/` 中针对性测试

QA verdict must be PASS or PASS WITH WARNINGS before proceeding. If FAIL: scope the failing fix out of the day-one patch and defer to 1.1.
继续之前 QA 结论必须为 PASS 或 PASS WITH WARNINGS。若 FAIL：将失败的修复从首日
补丁中剔除并推迟到 1.1。

---

## Phase 6: Generate Patch Record
## 阶段 6：生成补丁记录

```markdown
# Day-One Patch: [Game Name] v[version]

**Date prepared**: [date]
**Target release**: [launch date or "day of launch"]
**Base build**: [gold master tag or commit]
**Patch build**: [patch tag or commit]

---

## Patch Notes (Internal)

### Bugs Fixed
| BUG-ID | Severity | Description | Fix summary |
|--------|----------|-------------|-------------|
| BUG-NNN | S[1-4] | [description] | [one-line fix] |

### Deferred to 1.1
| BUG-ID | Severity | Description | Reason deferred |
|--------|----------|-------------|-----------------|
| BUG-NNN | S[1-4] | [description] | [reason] |

---

## QA Sign-Off

**QA scope**: [Targeted smoke / Broader regression]
**Verdict**: [PASS / PASS WITH WARNINGS]
**QA lead**: qa-lead agent
**Date**: [date]
**Warnings (if any)**: [list or "None"]

---

## Rollback Plan

See: `production/releases/rollback-plan-[version].md`

**Trigger condition**: If [N] or more S1 bugs are reported within [X] hours of launch, execute rollback.
**Rollback owner**: [user / producer]

---

## Approvals Required Before Deploy

- [ ] lead-programmer: all fixes reviewed
- [ ] qa-lead: QA gate PASS confirmed
- [ ] producer: deployment timing approved
- [ ] release-manager: platform submission confirmed

---

## Player-Facing Patch Notes

[Draft for community-manager to review before publishing]

[list player-facing changes in plain language]
```

Ask: "May I write this patch record to `production/releases/day-one-patch-[version].md`?"
询问："May I write this patch record to `production/releases/day-one-patch-[version].md`?"

---

## Phase 7: Next Steps
## 阶段 7：下一步

After the patch record is written:
补丁记录写入后：

1. Run `/patch-notes` to generate the player-facing version of the patch notes
2. Run `/bug-report verify [BUG-ID]` for each fixed bug after the patch is live
3. Run `/bug-report close [BUG-ID]` for each verified fix
4. Schedule a post-launch review 48–72 hours after launch using `/retrospective launch`
1. 运行 `/patch-notes` 生成面向玩家的补丁说明版本
2. 补丁上线后对每个修复 bug 运行 `/bug-report verify [BUG-ID]`
3. 对每个已验证修复运行 `/bug-report close [BUG-ID]`
4. 在发行后 48-72 小时使用 `/retrospective launch` 安排发行后复盘

**If any S1 bugs remain open after the patch:**
> "⚠️ S1 bugs remain open and were not patched. These are accepted risks. Document them in the rollback plan trigger conditions — if they occur at scale, rollback may be preferable to a follow-up patch."
**若补丁后仍有 S1 bug 处于开放状态：**
> "⚠️ S1 bugs remain open and were not patched. These are accepted risks. Document them in the rollback plan trigger conditions — if they occur at scale, rollback may be preferable to a follow-up patch."

Use `AskUserQuestion`:
- Prompt: "Day-one patch complete. What's next?"
- Options:
  - `[A] Run /patch-notes — generate player-facing patch notes`
  - `[B] Run /bug-report to log any issues found post-deploy`
  - `[C] Stop here`
使用 `AskUserQuestion`：
- 提示："Day-one patch complete. What's next?"
- 选项：
  - `[A] Run /patch-notes — generate player-facing patch notes`
  - `[B] Run /bug-report to log any issues found post-deploy`
  - `[C] Stop here`

---

## Collaborative Protocol
## 协作协议

- **Scope discipline is everything** — resist scope creep; every addition increases risk
- **Rollback plan first, always** — a patch without a rollback plan is irresponsible
- **Deferred is not forgotten** — every deferred bug gets a 1.1 ticket automatically
- **Player communication is part of the patch** — `/patch-notes` is a required output, not optional
- **范围纪律高于一切** —— 抵抗范围蔓延；每个添加都增加风险
- **回滚计划优先，始终如此** —— 没有回滚计划的补丁是不负责任的
- **推迟不等于遗忘** —— 每个推迟的 bug 自动获得 1.1 工单
- **玩家沟通是补丁的一部分** —— `/patch-notes` 是必需产出，非可选
