---
name: playtest-report
description: "Generates a structured playtest report template or analyzes existing playtest notes into a structured format. Use this to standardize playtest feedback collection and analysis."
argument-hint: "[new|analyze path-to-notes] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
model: sonnet
---
---
name: playtest-report
description: "生成结构化的试玩报告模板，或将现有试玩笔记分析为结构化格式。用于标准化试玩反馈收集与分析。"
argument-hint: "[new|analyze path-to-notes] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
model: sonnet
---

## Phase 1: Parse Arguments

Resolve the review mode (once, store for all gate spawns this run):
1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`

See `.claude/docs/director-gates.md` for the full check pattern.

Determine the mode:

- `new` → generate a blank playtest report template
- `analyze [path]` → read raw notes and fill in the template with structured findings

## 第 1 阶段：解析参数

解析评审模式（仅一次，保存供本次运行所有门禁派生使用）：
1. 如传入 `--review [full|lean|solo]` → 使用该值
2. 否则读取 `production/review-mode.txt` → 使用该值
3. 否则 → 默认为 `lean`

完整检查模式见 `.claude/docs/director-gates.md`。

确定模式：

- `new` → 生成空白试玩报告模板
- `analyze [path]` → 读取原始笔记并用结构化发现填充模板

---

## Phase 2A: New Template Mode

Generate this template and output it to the user:

```markdown
# Playtest Report

## Session Info
- **Date**: [Date]
- **Build**: [Version/Commit]
- **Duration**: [Time played]
- **Tester**: [Name/ID]
- **Platform**: [PC/Console/Mobile]
- **Input Method**: [KB+M / Gamepad / Touch]
- **Session Type**: [First time / Returning / Targeted test]

## Test Focus
[What specific features or flows were being tested]

## First Impressions (First 5 minutes)
- **Understood the goal?** [Yes/No/Partially]
- **Understood the controls?** [Yes/No/Partially]
- **Emotional response**: [Engaged/Confused/Bored/Frustrated/Excited]
- **Notes**: [Observations]

## Gameplay Flow
### What worked well
- [Observation 1]

### Pain points
- [Issue 1 -- Severity: High/Medium/Low]

### Confusion points
- [Where the player was confused and why]

### Moments of delight
- [What surprised or pleased the player]

## Bugs Encountered
| # | Description | Severity | Reproducible |
|---|-------------|----------|-------------|

## Feature-Specific Feedback
### [Feature 1]
- **Understood purpose?** [Yes/No]
- **Found engaging?** [Yes/No]
- **Suggestions**: [Tester suggestions]

## Quantitative Data (if available)
- **Deaths**: [Count and locations]
- **Time per area**: [Breakdown]
- **Items used**: [What and when]
- **Features discovered vs missed**: [List]

## Overall Assessment
- **Would play again?** [Yes/No/Maybe]
- **Difficulty**: [Too Easy / Just Right / Too Hard]
- **Pacing**: [Too Slow / Good / Too Fast]
- **Session length preference**: [Shorter / Good / Longer]

## Top 3 Priorities from this session
1. [Most important finding]
2. [Second priority]
3. [Third priority]
```

## 第 2A 阶段：新模板模式

生成此模板并输出给用户：

```markdown
# Playtest Report

## Session Info
- **Date**: [Date]
- **Build**: [Version/Commit]
- **Duration**: [Time played]
- **Tester**: [Name/ID]
- **Platform**: [PC/Console/Mobile]
- **Input Method**: [KB+M / Gamepad / Touch]
- **Session Type**: [First time / Returning / Targeted test]

## Test Focus
[What specific features or flows were being tested]

## First Impressions (First 5 minutes)
- **Understood the goal?** [Yes/No/Partially]
- **Understood the controls?** [Yes/No/Partially]
- **Emotional response**: [Engaged/Confused/Bored/Frustrated/Excited]
- **Notes**: [Observations]

## Gameplay Flow
### What worked well
- [Observation 1]

### Pain points
- [Issue 1 -- Severity: High/Medium/Low]

### Confusion points
- [Where the player was confused and why]

### Moments of delight
- [What surprised or pleased the player]

## Bugs Encountered
| # | Description | Severity | Reproducible |
|---|-------------|----------|-------------|

## Feature-Specific Feedback
### [Feature 1]
- **Understood purpose?** [Yes/No]
- **Found engaging?** [Yes/No]
- **Suggestions**: [Tester suggestions]

## Quantitative Data (if available)
- **Deaths**: [Count and locations]
- **Time per area**: [Breakdown]
- **Items used**: [What and when]
- **Features discovered vs missed**: [List]

## Overall Assessment
- **Would play again?** [Yes/No/Maybe]
- **Difficulty**: [Too Easy / Just Right / Too Hard]
- **Pacing**: [Too Slow / Good / Too Fast]
- **Session length preference**: [Shorter / Good / Longer]

## Top 3 Priorities from this session
1. [Most important finding]
2. [Second priority]
3. [Third priority]
```

---

## Phase 2B: Analyze Mode

Read the raw notes at the provided path. Cross-reference with existing design documents. Fill in the template above with structured findings. Flag any playtest observations that conflict with design intent.

## 第 2B 阶段：分析模式

读取所提供路径的原始笔记。与现有设计文档交叉引用。用结构化发现填充上面的模板。标记任何与设计意图冲突的试玩观察。

---

## Phase 3: Action Routing

Categorize all findings into four buckets:

- **Design changes needed** — fun issues, player confusion, broken mechanics, observations that conflict with the GDD's intended experience
- **Balance adjustments** — numbers feel wrong, difficulty too spiked or too flat
- **Bug reports** — clear implementation defects that are reproducible
- **Polish items** — not blocking progress, but friction or feel issues for later

Present the categorized list, then route:

- **Design changes:** "Run `/propagate-design-change [path]` on the affected design document to find downstream impacts before making changes."
- **Balance adjustments:** "Run `/balance-check [system]` to verify the full balance picture before tuning values."
- **Bugs:** "Use `/bug-report` to formally track these."
- **Polish items:** "Add to the polish backlog in `production/` when the team reaches that phase."

## 第 3 阶段：行动路由

将所有发现分类到四个桶：

- **需要设计变更** —— 乐趣问题、玩家困惑、损坏机制、与 GDD 预期体验冲突的观察
- **平衡调整** —— 数值感觉不对、难度过于陡峭或过于平缓
- **Bug 报告** —— 可复现的明确实现缺陷
- **打磨项** —— 不阻塞进度但属于摩擦或手感问题，留待后续

呈现分类后的清单，然后路由：

- **设计变更：** "对受影响的设计文档运行 `/propagate-design-change [path]` 以在做变更前查找下游影响。"
- **平衡调整：** "运行 `/balance-check [system]` 以在调整数值前验证完整平衡情况。"
- **Bug：** "使用 `/bug-report` 正式跟踪这些。"
- **打磨项：** "当团队到达该阶段时添加到 `production/` 的打磨积压中。"

---

## Phase 3b: Creative Director Player Experience Review

**Review mode check** — apply before spawning CD-PLAYTEST:
- `solo` → skip. Note: "CD-PLAYTEST skipped — Solo mode." Proceed to Phase 4 (save the report).
- `lean` → skip (not a PHASE-GATE). Note: "CD-PLAYTEST skipped — Lean mode." Proceed to Phase 4 (save the report).
- `full` → spawn as normal.

After categorising findings, spawn `creative-director` via Task using gate **CD-PLAYTEST** (`.claude/docs/director-gates.md`).

Pass: the structured report content, game pillars and core fantasy (from `design/gdd/game-concept.md`), the specific hypothesis being tested.

Present the creative director's assessment before saving the report. If CONCERNS or REJECT, add a `## Creative Director Assessment` section to the report capturing the verdict and feedback. If APPROVE, note the approval in the report.

## 第 3b 阶段：创意总监玩家体验评审

**评审模式检查** —— 在派生 CD-PLAYTEST 前应用：
- `solo` → 跳过。注："CD-PLAYTEST skipped — Solo mode."（CD-PLAYTEST 已跳过 —— Solo 模式。）进入第 4 阶段（保存报告）。
- `lean` → 跳过（非 PHASE-GATE）。注："CD-PLAYTEST skipped — Lean mode."（CD-PLAYTEST 已跳过 —— Lean 模式。）进入第 4 阶段（保存报告）。
- `full` → 正常派生。

分类发现后，通过 Task 使用门禁 **CD-PLAYTEST**（`.claude/docs/director-gates.md`）派生 `creative-director`。

传递：结构化报告内容、游戏支柱和核心幻想（来自 `design/gdd/game-concept.md`）、正在测试的具体假设。

在保存报告前呈现创意总监的评估。如 CONCERNS 或 REJECT，向报告添加 `## Creative Director Assessment` 章节以记录判定和反馈。如 APPROVE，在报告中注明批准。

---

## Phase 4: Save Report

Ask: "May I write this playtest report to `production/qa/playtests/playtest-[date]-[tester].md`?"

If yes, write the file, creating the directory if needed.

## 第 4 阶段：保存报告

询问："我可以将此试玩报告写入 `production/qa/playtests/playtest-[date]-[tester].md` 吗？"

如同意，写入文件，如需要则创建目录。

---

## Phase 5: Next Steps

Verdict: **COMPLETE** — playtest report generated.

- Act on the highest-priority finding category first.
- After addressing design changes: re-run `/design-review` on the updated GDD.
- After fixing bugs: re-run `/bug-triage` to update priorities.

## 第 5 阶段：下一步

判定：**COMPLETE** —— 试玩报告已生成。

- 首先处理最高优先级的发现类别。
- 处理设计变更后：对更新的 GDD 重新运行 `/design-review`。
- 修复 Bug 后：重新运行 `/bug-triage` 以更新优先级。
