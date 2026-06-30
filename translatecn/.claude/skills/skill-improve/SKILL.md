---
name: skill-improve
description: "Improve a skill using a test-fix-retest loop. Runs static checks, proposes targeted fixes, rewrites the skill, re-tests, and keeps or reverts based on score change."
argument-hint: "[skill-name]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash
model: sonnet
---
---
名称：skill-improve
描述："使用测试-修复-重测循环改进技能。运行静态检查、提出针对性修复、重写技能、重测，并基于分数变化保留或回滚。"
argument-hint："[skill-name]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Bash
model：sonnet
---

# Skill Improve
# 技能改进

Runs an improvement loop on a single skill:
test → fix → retest → keep or revert.
对单个技能运行改进循环：
测试 → 修复 → 重测 → 保留或回滚。

---

## Phase 1: Parse Argument
## 阶段 1：解析参数

Read the skill name from the first argument. If missing, output usage and stop:
从第一个参数读取技能名。若缺失，输出用法并停止：

```
Usage: /skill-improve [skill-name]
Example: /skill-improve tech-debt
```

Verify `.claude/skills/[name]/SKILL.md` exists. If not, stop with:
"Skill '[name]' not found."
验证 `.claude/skills/[name]/SKILL.md` 存在。若不，停止并显示：
"Skill '[name]' not found."

---

## Phase 2: Baseline Test
## 阶段 2：基线测试

Run `/skill-test static [name]` and record the baseline score:
- Count of FAILs
- Count of WARNs
- Which specific checks failed (Check 1–7)
运行 `/skill-test static [name]` 并记录基线分数：
- FAIL 计数
- WARN 计数
- 哪些具体检查失败（Check 1-7）

Display to the user:
向用户展示：
```
Static baseline:   [N] failures, [M] warnings
Failing: Check 4 (no ask-before-write), Check 5 (no handoff)
```

If baseline is 0 FAILs and 0 WARNs, note it and proceed to Phase 2b.
若基线为 0 FAILs 与 0 WARNs，注明并进入阶段 2b。

### Phase 2b: Category Baseline
### 阶段 2b：类别基线

Look up the skill's `category:` field in `CCGS Skill Testing Framework/catalog.yaml`.
在 `CCGS Skill Testing Framework/catalog.yaml` 中查找技能的 `category:` 字段。

If no `category:` field is found, display:
"Category: not yet assigned — skipping category checks."
and skip to Phase 3.
若无 `category:` 字段，显示：
"Category: not yet assigned — skipping category checks."
并跳到阶段 3。

If category is found, run `/skill-test category [name]` and record the category baseline:
- Count of FAILs
- Count of WARNs
- Which specific category rubric metrics failed
若找到类别，运行 `/skill-test category [name]` 并记录类别基线：
- FAIL 计数
- WARN 计数
- 哪些具体类别评分指标失败

Display to the user:
向用户展示：
```
Category baseline: [N] failures, [M] warnings  ([category] rubric)
```

If BOTH static and category baselines are 0 FAILs and 0 WARNs, stop:
"This skill already passes all static and category checks. No improvements needed."
若静态与类别基线都为 0 FAILs 与 0 WARNs，停止：
"This skill already passes all static and category checks. No improvements needed."

---

## Phase 3: Diagnose
## 阶段 3：诊断

Read the full skill file at `.claude/skills/[name]/SKILL.md`.
读取 `.claude/skills/[name]/SKILL.md` 的完整技能文件。

For each failing or warning **static** check, identify the exact gap:
对每个失败或警告的**静态**检查，识别确切缺漏：

- **Check 1 fail** → which frontmatter field is missing
- **Check 2 fail** → how many phases found vs. minimum required
- **Check 3 fail** → no verdict keywords anywhere in the skill body
- **Check 4 fail** → Write or Edit in allowed-tools but no ask-before-write language
- **Check 5 warn** → no follow-up or next-step section at the end
- **Check 6 warn** → `context: fork` set but fewer than 5 phases found
- **Check 7 warn** → argument-hint is empty or doesn't match documented modes
- **Check 1 fail** → 缺失哪个 frontmatter 字段
- **Check 2 fail** → 找到多少阶段 vs 最少要求
- **Check 3 fail** → 技能体任何地方都无结论关键词
- **Check 4 fail** → allowed-tools 中有 Write 或 Edit 但无 ask-before-write 语言
- **Check 5 warn** → 结尾无后续或下一步段落
- **Check 6 warn** → 设置了 `context: fork` 但找到的阶段少于 5 个
- **Check 7 warn** → argument-hint 为空或不匹配文档化模式

For each failing or warning **category** check (if category was assigned in Phase 2b),
identify the exact gap in the skill's text. For example:
- If G2 fails (gate mode, full directors not spawned): skill body never references all 4
  PHASE-GATE director prompts
- If A2 fails (authoring, no per-section May-I-write): skill asks once at the end, not
  before each section write
- If T3 fails (team, BLOCKED not surfaced): skill doesn't halt dependent work on blocked agent
对每个失败或警告的**类别**检查（若类别在阶段 2b 中已分配），识别技能文本中的
确切缺漏。例如：
- 若 G2 失败（gate 模式，未启动全部 director）：技能体从不引用全部 4 个
  PHASE-GATE director 提示
- 若 A2 失败（编写，无每段落 May-I-write）：技能仅在结尾询问一次，而非每次段落
  写入之前
- 若 T3 失败（团队，BLOCKED 未暴露）：技能不在被阻塞智能体上停止依赖工作

Show the full combined diagnosis to the user before proposing any changes.
在提出任何变更之前向用户展示完整合并诊断。

---

## Phase 4: Propose Fix
## 阶段 4：提出修复

Write a targeted fix for each failure and warning. Show the proposed changes
as clearly marked before/after blocks. Only change what is failing — do not
rewrite sections that are passing.
为每个失败与警告写针对性修复。以清晰标记的 before/after 块展示提议变更。仅
修改失败部分 —— 不要重写通过的部分。

Ask: "May I write this improved version to `.claude/skills/[name]/SKILL.md`?"
询问："May I write this improved version to `.claude/skills/[name]/SKILL.md`?"

If the user says no, stop here.
若用户说不，在此停止。

---

## Phase 5: Write and Retest
## 阶段 5：写入并重测

Record the current content of the skill file (for revert if needed).
记录技能文件的当前内容（用于必要时回滚）。

Write the improved skill to `.claude/skills/[name]/SKILL.md`.
将改进技能写入 `.claude/skills/[name]/SKILL.md`。

Re-run `/skill-test static [name]` and record the new static score.
If a category was assigned, also re-run `/skill-test category [name]` and record the new category score.
重跑 `/skill-test static [name]` 并记录新静态分数。
若已分配类别，也重跑 `/skill-test category [name]` 并记录新类别分数。

Display the comparison:
展示对比：
```
Static:   Before [N] failures, [M] warnings  →  After [N'] failures, [M'] warnings
Category: Before [N] failures, [M] warnings  →  After [N'] failures, [M'] warnings  (if applicable)
Combined change: improved / no change / worse
```
```
Static:   Before [N] failures, [M] warnings  →  After [N'] failures, [M'] warnings
Category: Before [N] failures, [M] warnings  →  After [N'] failures, [M'] warnings  (if applicable)
Combined change: improved / no change / worse
```

---

## Phase 6: Verdict
## 阶段 6：结论

Count the combined failure total: static FAILs + category FAILs + static WARNs + category WARNs.
统计合并失败总数：static FAILs + category FAILs + static WARNs + category WARNs。

**If combined score improved (combined failure count is lower than baseline):**
Report: "Score improved. Changes kept."
Show a summary of what was fixed in each dimension.
**若合并分数提升（合并失败计数低于基线）：**
报告："Score improved. Changes kept."
展示每个维度修复内容的摘要。

**If combined score is the same or worse:**
Report: "Combined score did not improve."
Show what changed and why it may not have helped.
Ask: "May I revert `.claude/skills/[name]/SKILL.md` using git checkout?"
If yes: run `git checkout -- .claude/skills/[name]/SKILL.md`
**若合并分数相同或更差：**
报告："Combined score did not improve."
展示改变了什么以及为何可能未帮助。
询问："May I revert `.claude/skills/[name]/SKILL.md` using git checkout?"
若是：运行 `git checkout -- .claude/skills/[name]/SKILL.md`

---

## Phase 7: Next Steps
## 阶段 7：下一步

- Run `/skill-test static all` to find the next skill with failures.
- Run `/skill-improve [next-name]` to continue the loop on another skill.
- Run `/skill-test audit` to see overall coverage progress.
- 运行 `/skill-test static all` 查找下一个有失败的技能。
- 运行 `/skill-improve [next-name]` 在另一技能上继续循环。
- 运行 `/skill-test audit` 查看整体覆盖进度。
