---
name: scope-check
description: "Analyze a feature or sprint for scope creep by comparing current scope against the original plan. Flags additions, quantifies bloat, and recommends cuts. Use when user says 'any scope creep', 'scope review', 'are we staying in scope'."
argument-hint: "[feature-name or sprint-N]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash
model: haiku
---
---
名称：scope-check
描述："通过将当前范围与原始计划进行对比，分析功能或冲刺的范围蔓延。标记新增内容、量化膨胀并建议裁剪。当用户说'有没有范围蔓延'、'范围评审'、'我们是否保持在范围内'时使用。"
argument-hint："[功能名或 sprint-N]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Bash
model：haiku
---

# Scope Check
# 范围检查

This skill is read-only — it reports findings but writes no files.
此技能是只读的 — 它报告发现但不写入文件。

Compares original planned scope against current state to detect, quantify, and triage
scope creep.
将原始计划范围与当前状态进行对比，以检测、量化和分诊范围蔓延。

**Argument:** `$ARGUMENTS[0]` — feature name, sprint number, or milestone name.
**参数：** `$ARGUMENTS[0]` — 功能名、冲刺编号或里程碑名。

---

## Phase 1: Find the Original Plan
## 阶段 1：查找原始计划

Locate the baseline scope document for the given argument:
为给定参数定位基线范围文档：

- **Feature name** → read `design/gdd/[feature].md` or matching file in `design/`
- **Sprint number** (e.g., `sprint-3`) → read `production/sprints/sprint-03.md` or similar
- **Milestone** → read `production/milestones/[name].md`
- **功能名** → 读取 `design/gdd/[feature].md` 或 `design/` 中的匹配文件
- **冲刺编号**（例如 `sprint-3`）→ 读取 `production/sprints/sprint-03.md` 或类似文件
- **里程碑** → 读取 `production/milestones/[name].md`

If the document is not found, report the missing file and stop. Do not proceed without
a baseline to compare against.
如果未找到文档，则报告缺失文件并停止。没有可对比的基线，不要继续。

---

## Phase 2: Read the Current State
## 阶段 2：读取当前状态

Check what has actually been implemented or is in progress:
检查实际已实施或正在进行的内容：

- Scan the codebase for files related to the feature/sprint
- Read git log for commits related to this work (`git log --oneline --since=[start-date]`)
- Check for TODO/FIXME comments that indicate unfinished scope additions
- Check active sprint plan if the feature is mid-sprint
- 扫描代码库中与该功能/冲刺相关的文件
- 读取与该工作相关的 git 日志（`git log --oneline --since=[start-date]`）
- 检查 TODO/FIXME 注释，这些注释指示未完成的新增范围
- 如果功能处于冲刺中，检查活跃的冲刺计划

---

## Phase 3: Compare Original vs Current Scope
## 阶段 3：对比原始范围与当前范围

Produce the comparison report:
生成对比报告：

```markdown
## Scope Check: [Feature/Sprint Name]
Generated: [Date]

### Original Scope
[List of items from the original plan]

### Current Scope
[List of items currently implemented or in progress]

### Scope Additions (not in original plan)
| Addition | Source | When | Justified? | Effort |
|----------|--------|------|------------|--------|
| [item] | [commit/person] | [date] | [Yes/No/Unclear] | [S/M/L] |

### Scope Removals (in original but dropped)
| Removed Item | Reason | Impact |
|-------------|--------|--------|
| [item] | [why removed] | [what's affected] |

### Bloat Score
- Original items: [N]
- Current items: [N]
- Items added: [N] (+[X]%)
- Items removed: [N]
- Net scope change: [+/-N] ([X]%)

### Risk Assessment
- **Schedule Risk**: [Low/Medium/High] — [explanation]
- **Quality Risk**: [Low/Medium/High] — [explanation]
- **Integration Risk**: [Low/Medium/High] — [explanation]

### Recommendations
1. **Cut**: [Items that should be removed to stay on schedule]
2. **Defer**: [Items that can move to a future sprint/version]
3. **Keep**: [Additions that are genuinely necessary]
4. **Flag**: [Items that need a decision from producer/creative-director]
```

---

## Phase 4: Verdict
## 阶段 4：裁决

Assign a canonical verdict based on net scope change:
根据净范围变化分配标准裁决：

| Net Change | Verdict | Meaning |
|-----------|---------|---------|
| ≤10% | **PASS** | On Track — within acceptable variance |
| 10–25% | **CONCERNS** | Minor Creep — manageable with targeted cuts |
| 25–50% | **FAIL** | Significant Creep — must cut or formally extend timeline |
| >50% | **FAIL** | Out of Control — stop, re-plan, escalate to producer |
| 净变化 | 裁决 | 含义 |
|-----------|---------|---------|
| ≤10% | **PASS** | 进度正常 — 在可接受偏差范围内 |
| 10–25% | **CONCERNS** | 轻微蔓延 — 通过有针对性的裁剪可控 |
| 25–50% | **FAIL** | 严重蔓延 — 必须裁剪或正式延长时间线 |
| >50% | **FAIL** | 失控 — 停止、重新规划、上报给制作人 |

Output the verdict prominently:
突出显示裁决：

```
**Scope Verdict: [PASS / CONCERNS / FAIL]**
Net change: [+X%] — [On Track / Minor Creep / Significant Creep / Out of Control]
```

---

## Phase 5: Next Steps
## 阶段 5：后续步骤

After presenting the report, offer concrete follow-up:
呈现报告后，提供具体的后续行动：

- **PASS** → no action required. Suggest re-running before next milestone.
- **CONCERNS** → offer to identify the 2–3 additions with best cut ratio. Reference `/sprint-plan update` to formally re-scope.
- **FAIL** → recommend escalating to producer. Reference `/sprint-plan update` for re-planning or `/estimate` to re-baseline timeline.
- **PASS** → 无需行动。建议在下一个里程碑前重新运行。
- **CONCERNS** → 主动识别 2–3 个裁剪性价比最高的新增项。引用 `/sprint-plan update` 以正式重新规划范围。
- **FAIL** → 建议上报给制作人。引用 `/sprint-plan update` 进行重新规划，或引用 `/estimate` 重新设定时间线基线。

Always end with:
始终以此结尾：

> "Run `/scope-check [name]` again after cuts are made to verify the verdict improves."
> "在完成裁剪后再次运行 `/scope-check [name]`，以验证裁决是否改善。"

---

### Rules
### 规则

- Scope creep is additions without corresponding cuts or timeline extensions
- Not all additions are bad — some are discovered requirements. But they must be acknowledged and accounted for
- When recommending cuts, prioritize preserving the core player experience over nice-to-haves
- Always quantify scope changes — "it feels bigger" is not actionable, "+35% items" is
- 范围蔓延是指没有相应裁剪或时间线延长的新增内容
- 并非所有新增都是坏事 — 有些是发现的需求。但必须予以承认并纳入考量
- 在建议裁剪时，优先保留核心玩家体验，而非可有可无的功能
- 始终量化范围变化 — "感觉变大了"不可操作，"+35% 条目"才是
