---
name: skill-test
description: "Validate skill files for structural compliance and behavioral correctness. Three modes: static (linter), spec (behavioral), audit (coverage report)."
argument-hint: "static [skill-name | all] | spec [skill-name] | category [skill-name | all] | audit"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---
---
name: skill-test
description: "验证技能文件的结构合规性和行为正确性。三种模式：static（检查器）、spec（行为）、audit（覆盖率报告）。"
argument-hint: "static [skill-name | all] | spec [skill-name] | category [skill-name | all] | audit"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---

# Skill Test
# 技能测试

Validates `.claude/skills/*/SKILL.md` files for structural compliance and
behavioral correctness. No external dependencies — runs entirely within the
existing skill/hook/template architecture.
验证 `.claude/skills/*/SKILL.md` 文件的结构合规性和
行为正确性。无外部依赖 — 完全在
现有的技能/钩子/模板架构内运行。

**Four modes:**
**四种模式：**

| Mode | Command | Purpose | Token Cost |
|------|---------|---------|------------|
| `static` | `/skill-test static [name\|all]` | Structural linter — 7 compliance checks per skill | Low (~1k/skill) |
| `spec` | `/skill-test spec [name]` | Behavioral verifier — evaluates assertions in test spec | Medium (~5k/skill) |
| `category` | `/skill-test category [name\|all]` | Category rubric — checks skill against its category-specific metrics | Low (~2k/skill) |
| `audit` | `/skill-test audit` | Coverage report — skills, agent specs, last test dates | Low (~3k total) |
| 模式 | 命令 | 用途 | Token 成本 |
|------|---------|---------|------------|
| `static` | `/skill-test static [name\|all]` | 结构检查器 — 每个技能 7 项合规性检查 | 低（~1k/技能） |
| `spec` | `/skill-test spec [name]` | 行为验证器 — 评估测试规范中的断言 | 中（~5k/技能） |
| `category` | `/skill-test category [name\|all]` | 类别评估准则 — 根据类别特定指标检查技能 | 低（~2k/技能） |
| `audit` | `/skill-test audit` | 覆盖率报告 — 技能、智能体规范、上次测试日期 | 低（~3k 总计） |

---

## Phase 1: Parse Arguments
## 阶段 1：解析参数

Determine mode from the first argument:
从第一个参数确定模式：

- `static [name]` → run 7 structural checks on one skill
- `static all` → run 7 structural checks on all skills (Glob `.claude/skills/*/SKILL.md`)
- `spec [name]` → read skill + test spec, evaluate assertions
- `category [name]` → run category-specific rubric from `CCGS Skill Testing Framework/quality-rubric.md`
- `category all` → run category rubric for every skill that has a `category:` in catalog
- `audit` (or no argument) → read catalog, list all skills and agents, show coverage
- `static [name]` → 对一个技能运行 7 项结构检查
- `static all` → 对所有技能运行 7 项结构检查（Glob `.claude/skills/*/SKILL.md`）
- `spec [name]` → 读取技能 + 测试规范，评估断言
- `category [name]` → 从 `CCGS Skill Testing Framework/quality-rubric.md` 运行类别特定评估准则
- `category all` → 为目录中有 `category:` 的每个技能运行类别评估准则
- `audit`（或无参数）→ 读取目录，列出所有技能和智能体，显示覆盖率

If argument is missing or unrecognized, output usage and stop.
如果参数缺失或无法识别，输出用法并停止。

---

## Phase 2A: Static Mode — Structural Linter
## 阶段 2A：静态模式 — 结构检查器

For each skill being tested, read its `SKILL.md` fully and run all 7 checks:
对于每个被测试的技能，完整读取其 `SKILL.md` 并运行全部 7 项检查：

### Check 1 — Required Frontmatter Fields
### 检查 1 — 必需的 Frontmatter 字段
The file must contain all of these in the YAML frontmatter block:
- `name:`
- `description:`
- `argument-hint:`
- `user-invocable:`
- `allowed-tools:`
文件必须在 YAML frontmatter 块中包含以下所有内容：
- `name:`
- `description:`
- `argument-hint:`
- `user-invocable:`
- `allowed-tools:`

**FAIL** if any are absent.
如果缺少任何字段，则**失败**。

### Check 2 — Multiple Phases
### 检查 2 — 多阶段
The skill must have ≥2 numbered phase headings. Look for patterns like:
- `## Phase N` or `## Phase N:`
- `## N.` (numbered top-level sections)
- At least 2 distinct `##` headings if phases aren't explicitly numbered
技能必须具有 ≥2 个编号的阶段标题。查找以下模式：
- `## Phase N` 或 `## Phase N:`
- `## N.`（编号的顶级章节）
- 如果阶段未明确编号，则至少 2 个不同的 `##` 标题

**FAIL** if fewer than 2 phase-like headings are found.
如果找到的阶段式标题少于 2 个，则**失败**。

### Check 3 — Verdict Keywords
### 检查 3 — 结论关键字
The skill must contain at least one of: `PASS`, `FAIL`, `CONCERNS`, `APPROVED`,
`BLOCKED`, `COMPLETE`, `READY`, `COMPLIANT`, `NON-COMPLIANT`
技能必须至少包含以下之一：`PASS`、`FAIL`、`CONCERNS`、`APPROVED`、
`BLOCKED`、`COMPLETE`、`READY`、`COMPLIANT`、`NON-COMPLIANT`

**FAIL** if none are present.
如果均不存在，则**失败**。

### Check 4 — Collaborative Protocol Language
### 检查 4 — 协作协议语言
The skill must contain ask-before-write language. Look for:
- `"May I write"` (canonical form)
- `"before writing"` or `"approval"` near file-write instructions
- `"ask"` + `"write"` in close proximity (within same section)
技能必须包含写入前询问的语言。查找：
- `"May I write"`（规范形式）
- 在文件写入指令附近的 `"before writing"` 或 `"approval"`
- 在同一章节内相近出现的 `"ask"` + `"write"`

**WARN** if absent (some read-only skills legitimately skip this).
**FAIL** if `allowed-tools` includes `Write` or `Edit` but no ask-before-write language is found.
如果缺失则**警告**（一些只读技能合理地跳过此项）。
如果 `allowed-tools` 包含 `Write` 或 `Edit` 但未找到写入前询问的语言，则**失败**。

### Check 5 — Next-Step Handoff
### 检查 5 — 后续步骤移交
The skill must end with a recommended next action or follow-up path. Look for:
- A final section mentioning another skill (e.g., `/story-done`, `/gate-check`)
- "Recommended next" or "next step" phrasing
- A "Follow-Up" or "After this" section
技能必须以建议的后续操作或后续路径结束。查找：
- 提及另一个技能的最终章节（例如 `/story-done`、`/gate-check`）
- "Recommended next" 或 "next step" 措辞
- "Follow-Up" 或 "After this" 章节

**WARN** if absent.
如果缺失则**警告**。

### Check 6 — Fork Context Complexity
### 检查 6 — Fork 上下文复杂度
If frontmatter contains `context: fork`, the skill should have ≥5 phase headings
(`##` level or numbered Phase N headers). Fork context is for complex multi-phase
skills; simple skills should not use it.
如果 frontmatter 包含 `context: fork`，技能应具有 ≥5 个阶段标题
（`##` 级别或编号的 Phase N 标题）。Fork 上下文用于复杂的多阶段
技能；简单技能不应使用它。

**WARN** if `context: fork` is set but fewer than 5 phases found.
如果设置了 `context: fork` 但找到的阶段少于 5 个，则**警告**。

### Check 7 — Argument Hint Plausibility
### 检查 7 — 参数提示合理性
`argument-hint` must be non-empty. If the skill body mentions multiple modes
(e.g., "Mode A | Mode B"), the hint should reflect them. Cross-reference the
hint against the first phase's "Parse Arguments" section.
`argument-hint` 必须非空。如果技能正文提及多个模式
（例如 "Mode A | Mode B"），提示应反映它们。将
提示与第一阶段的 "Parse Arguments" 章节交叉引用。

**WARN** if hint is `""` or if documented modes don't match hint.
如果提示为 `""` 或文档化的模式与提示不匹配，则**警告**。

---

### Static Mode Output Format
### 静态模式输出格式

For a single skill:
对于单个技能：
```
=== Skill Static Check: /[name] ===

Check 1 — Frontmatter Fields:    PASS
Check 2 — Multiple Phases:       PASS (7 phases found)
Check 3 — Verdict Keywords:      PASS (PASS, FAIL, CONCERNS)
Check 4 — Collaborative Protocol: PASS ("May I write" found)
Check 5 — Next-Step Handoff:     WARN (no follow-up section found)
Check 6 — Fork Context Complexity: PASS (8 phases, context: fork set)
Check 7 — Argument Hint:         PASS

Verdict: WARNINGS (1 warning, 0 failures)
Recommended: Add a "Follow-Up Actions" section at the end of the skill.
```
```
=== Skill Static Check: /[name] ===

Check 1 — Frontmatter Fields:    PASS
Check 2 — Multiple Phases:       PASS (7 phases found)
Check 3 — Verdict Keywords:      PASS (PASS, FAIL, CONCERNS)
Check 4 — Collaborative Protocol: PASS ("May I write" found)
Check 5 — Next-Step Handoff:     WARN (no follow-up section found)
Check 6 — Fork Context Complexity: PASS (8 phases, context: fork set)
Check 7 — Argument Hint:         PASS

Verdict: WARNINGS (1 warning, 0 failures)
Recommended: Add a "Follow-Up Actions" section at the end of the skill.
```

For `static all`, produce a summary table then list any non-compliant skills:
对于 `static all`，生成摘要表然后列出任何不合规的技能：
```
=== Skill Static Check: All 52 Skills ===

Skill                  | Result       | Issues
-----------------------|--------------|-------
gate-check             | COMPLIANT    |
design-review          | COMPLIANT    |
story-readiness        | WARNINGS     | Check 5: no handoff
...

Summary: 48 COMPLIANT, 3 WARNINGS, 1 NON-COMPLIANT
Aggregate Verdict: N WARNINGS / N FAILURES
```
```
=== Skill Static Check: All 52 Skills ===

Skill                  | Result       | Issues
-----------------------|--------------|-------
gate-check             | COMPLIANT    |
design-review          | COMPLIANT    |
story-readiness        | WARNINGS     | Check 5: no handoff
...

Summary: 48 COMPLIANT, 3 WARNINGS, 1 NON-COMPLIANT
Aggregate Verdict: N WARNINGS / N FAILURES
```

---

## Phase 2B: Spec Mode — Behavioral Verifier
## 阶段 2B：规范模式 — 行为验证器

### Step 1 — Locate Files
### 步骤 1 — 定位文件

Find skill at `.claude/skills/[name]/SKILL.md`.
Look up the spec path from `CCGS Skill Testing Framework/catalog.yaml` — use the
`spec:` field for the matching skill entry.
在 `.claude/skills/[name]/SKILL.md` 查找技能。
从 `CCGS Skill Testing Framework/catalog.yaml` 查找规范路径 — 使用
匹配技能条目的 `spec:` 字段。

If either is missing:
- Missing skill: "Skill '[name]' not found in `.claude/skills/`."
- Missing spec path in catalog: "No spec path set for '[name]' in catalog.yaml."
- Spec file not found at path: "Spec file missing at [path]. Run `/skill-test audit`
  to see coverage gaps."
如果任一缺失：
- 缺失技能："Skill '[name]' not found in `.claude/skills/`."
- 目录中缺失规范路径："No spec path set for '[name]' in catalog.yaml."
- 在路径未找到规范文件："Spec file missing at [path]. Run `/skill-test audit`
  to see coverage gaps."

### Step 2 — Read Both Files
### 步骤 2 — 读取两个文件

Read the skill file and test spec file completely.
完整读取技能文件和测试规范文件。

### Step 3 — Evaluate Assertions
### 步骤 3 — 评估断言

For each **Test Case** in the spec:
对于规范中的每个 **Test Case**：

1. Read the **Fixture** description (assumed state of project files)
2. Read the **Expected behavior** steps
3. Read each **Assertion** checkbox
1. 读取 **Fixture** 描述（项目文件的假定状态）
2. 读取 **Expected behavior** 步骤
3. 读取每个 **Assertion** 复选框

For each assertion, evaluate whether the skill's written instructions, if
followed correctly given the fixture state, would satisfy it. This is a
Claude-evaluated reasoning check, not code execution.
对于每个断言，评估技能的书面指令如果
在给定 fixture 状态下正确执行，是否会满足它。这是
Claude 评估的推理检查，而非代码执行。

Mark each assertion:
- **PASS** — skill instructions clearly satisfy this assertion
- **PARTIAL** — skill instructions partially address it, but with ambiguity
- **FAIL** — skill instructions would NOT satisfy this assertion given the fixture
标记每个断言：
- **PASS** — 技能指令明确满足此断言
- **PARTIAL** — 技能指令部分涉及它，但有歧义
- **FAIL** — 给定 fixture 下技能指令不会满足此断言

For **Protocol Compliance** assertions (always present):
- Check whether the skill requires "May I write" before file writes
- Check whether the skill presents findings before requesting approval
- Check whether the skill ends with a recommended next step
- Check whether the skill avoids auto-creating files without approval
对于 **Protocol Compliance** 断言（始终存在）：
- 检查技能是否在文件写入前要求 "May I write"
- 检查技能是否在请求批准前呈现发现
- 检查技能是否以建议的后续步骤结束
- 检查技能是否避免未经批准自动创建文件

### Step 4 — Build Report
### 步骤 4 — 构建报告

```
=== Skill Spec Test: /[name] ===
Date: [date]
Spec: CCGS Skill Testing Framework/skills/[category]/[name].md

Case 1: [Happy Path — name]
  Fixture: [summary]
  Assertions:
    [PASS] [assertion text]
    [FAIL] [assertion text]
       Reason: The skill's Phase 3 says "..." but the fixture state means "..."
  Case Verdict: FAIL

Case 2: [Edge Case — name]
  ...
  Case Verdict: PASS

Protocol Compliance:
  [PASS] Uses "May I write" before file writes
  [PASS] Presents findings before asking approval
  [WARN] No explicit next-step handoff at end

Overall Verdict: FAIL (1 case failed, 1 warning)
```
```
=== Skill Spec Test: /[name] ===
Date: [date]
Spec: CCGS Skill Testing Framework/skills/[category]/[name].md

Case 1: [Happy Path — name]
  Fixture: [summary]
  Assertions:
    [PASS] [assertion text]
    [FAIL] [assertion text]
       Reason: The skill's Phase 3 says "..." but the fixture state means "..."
  Case Verdict: FAIL

Case 2: [Edge Case — name]
  ...
  Case Verdict: PASS

Protocol Compliance:
  [PASS] Uses "May I write" before file writes
  [PASS] Presents findings before asking approval
  [WARN] No explicit next-step handoff at end

Overall Verdict: FAIL (1 case failed, 1 warning)
```

### Step 5 — Offer to Write Results
### 步骤 5 — 提供写入结果

"May I write these results to `CCGS Skill Testing Framework/results/skill-test-spec-[name]-[date].md`
and update `CCGS Skill Testing Framework/catalog.yaml`?"
"May I write these results to `CCGS Skill Testing Framework/results/skill-test-spec-[name]-[date].md`
and update `CCGS Skill Testing Framework/catalog.yaml`?"

If yes:
- Write results file to `CCGS Skill Testing Framework/results/`
- Update the skill's entry in `CCGS Skill Testing Framework/catalog.yaml`:
  - `last_spec: [date]`
  - `last_spec_result: PASS|PARTIAL|FAIL`
如果同意：
- 将结果文件写入 `CCGS Skill Testing Framework/results/`
- 更新 `CCGS Skill Testing Framework/catalog.yaml` 中的技能条目：
  - `last_spec: [date]`
  - `last_spec_result: PASS|PARTIAL|FAIL`

---

## Phase 2D: Category Mode — Rubric Evaluation
## 阶段 2D：类别模式 — 评估准则评估

### Step 1 — Locate Skill and Category
### 步骤 1 — 定位技能和类别

Find skill at `.claude/skills/[name]/SKILL.md`.
Look up `category:` field in `CCGS Skill Testing Framework/catalog.yaml`.
在 `.claude/skills/[name]/SKILL.md` 查找技能。
在 `CCGS Skill Testing Framework/catalog.yaml` 中查找 `category:` 字段。

If skill not found: "Skill '[name]' not found."
If no `category:` field: "No category assigned for '[name]' in catalog.yaml.
Add `category: [name]` to the skill entry first."
如果未找到技能："Skill '[name]' not found."
如果没有 `category:` 字段："No category assigned for '[name]' in catalog.yaml.
Add `category: [name]` to the skill entry first."

For `category all`: collect all skills with a `category:` field and process each.
`category: utility` skills are evaluated against U1 (static checks pass) and U2
(gate mode correct if applicable) only — skip to the static mode for U1.
对于 `category all`：收集所有具有 `category:` 字段的技能并处理每个。
`category: utility` 技能仅根据 U1（静态检查通过）和 U2
（如适用，门禁模式正确）进行评估 — 跳到静态模式处理 U1。

### Step 2 — Read Rubric Section
### 步骤 2 — 读取评估准则章节

Read `CCGS Skill Testing Framework/quality-rubric.md`.
Extract the section matching the skill's category (e.g., `### gate`, `### team`).
读取 `CCGS Skill Testing Framework/quality-rubric.md`。
提取匹配技能类别的章节（例如 `### gate`、`### team`）。

### Step 3 — Read Skill
### 步骤 3 — 读取技能

Read the skill's `SKILL.md` fully.
完整读取技能的 `SKILL.md`。

### Step 4 — Evaluate Rubric Metrics
### 步骤 4 — 评估评估准则指标

For each metric in the category's rubric table:
1. Check whether the skill's written instructions clearly satisfy the criterion
2. Mark PASS, FAIL, or WARN
3. For FAIL/WARN, identify the exact gap in the skill text (quote the relevant section
   or note its absence)
对于类别评估准则表中的每个指标：
1. 检查技能的书面指令是否明确满足该标准
2. 标记 PASS、FAIL 或 WARN
3. 对于 FAIL/WARN，识别技能文本中的确切缺口（引用相关章节
   或注明其缺失）

### Step 5 — Output Report
### 步骤 5 — 输出报告

```
=== Skill Category Check: /[name] ([category]) ===

Metric G1 — Review mode read:      PASS
Metric G2 — Full mode directors:   FAIL
  Gap: Phase 3 spawns only CD-PHASE-GATE; TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE absent
Metric G3 — Lean mode: PHASE-GATE only: PASS
Metric G4 — Solo mode: no directors:    PASS
Metric G5 — No auto-advance:       PASS

Verdict: FAIL (1 failure, 0 warnings)
Fix: Add TD-PHASE-GATE, PR-PHASE-GATE, and AD-PHASE-GATE to the full-mode director
     panel in Phase 3.
```
```
=== Skill Category Check: /[name] ([category]) ===

Metric G1 — Review mode read:      PASS
Metric G2 — Full mode directors:   FAIL
  Gap: Phase 3 spawns only CD-PHASE-GATE; TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE absent
Metric G3 — Lean mode: PHASE-GATE only: PASS
Metric G4 — Solo mode: no directors:    PASS
Metric G5 — No auto-advance:       PASS

Verdict: FAIL (1 failure, 0 warnings)
Fix: Add TD-PHASE-GATE, PR-PHASE-GATE, and AD-PHASE-GATE to the full-mode director
     panel in Phase 3.
```

### Step 6 — Offer to Update Catalog
### 步骤 6 — 提供更新目录

"May I update `CCGS Skill Testing Framework/catalog.yaml` to record this category check
(`last_category`, `last_category_result`) for [name]?"
"May I update `CCGS Skill Testing Framework/catalog.yaml` to record this category check
(`last_category`, `last_category_result`) for [name]?"

---

## Phase 2C: Audit Mode — Coverage Report
## 阶段 2C：审计模式 — 覆盖率报告

### Step 1 — Read Catalog
### 步骤 1 — 读取目录

Read `CCGS Skill Testing Framework/catalog.yaml`. If missing, note that catalog doesn't exist
yet (first-run state).
读取 `CCGS Skill Testing Framework/catalog.yaml`。如果缺失，注明目录尚不存在
（首次运行状态）。

### Step 2 — Enumerate All Skills and Agents
### 步骤 2 — 枚举所有技能和智能体

Glob `.claude/skills/*/SKILL.md` to get the complete list of skills.
Extract skill name from each path (directory name).
Glob `.claude/skills/*/SKILL.md` 获取完整的技能列表。
从每个路径（目录名）提取技能名。

Also read the `agents:` section from `CCGS Skill Testing Framework/catalog.yaml` to get the
complete list of agents.
同时从 `CCGS Skill Testing Framework/catalog.yaml` 读取 `agents:` 章节以获取
完整的智能体列表。

### Step 3 — Build Skill Coverage Table
### 步骤 3 — 构建技能覆盖率表

For each skill:
- Check if a spec file exists (use the `spec:` path from catalog, or glob `CCGS Skill Testing Framework/skills/*/[name].md`)
- Look up `last_static`, `last_static_result`, `last_spec`, `last_spec_result`,
  `last_category`, `last_category_result`, `category` from catalog (or mark as
  "never" / "—" if not in catalog)
- Priority comes from catalog `priority:` field (critical/high/medium/low)
对于每个技能：
- 检查规范文件是否存在（使用目录中的 `spec:` 路径，或 glob `CCGS Skill Testing Framework/skills/*/[name].md`）
- 从目录查找 `last_static`、`last_static_result`、`last_spec`、`last_spec_result`、
  `last_category`、`last_category_result`、`category`（如果不在目录中则标记为
  "never" / "—"）
- 优先级来自目录的 `priority:` 字段（critical/high/medium/low）

### Step 3b — Build Agent Coverage Table
### 步骤 3b — 构建智能体覆盖率表

For each agent in catalog's `agents:` section:
- Check if a spec file exists (use the `spec:` path from catalog, or glob `CCGS Skill Testing Framework/agents/*/[name].md`)
- Look up `last_spec`, `last_spec_result`, `category` from catalog
对于目录 `agents:` 章节中的每个智能体：
- 检查规范文件是否存在（使用目录中的 `spec:` 路径，或 glob `CCGS Skill Testing Framework/agents/*/[name].md`）
- 从目录查找 `last_spec`、`last_spec_result`、`category`

### Step 4 — Output Report
### 步骤 4 — 输出报告

```
=== Skill Test Coverage Audit ===
Date: [date]

SKILLS (72 total)
Specs written: 72 (100%) | Never static tested: 72 | Never category tested: 72

Skill                  | Cat      | Has Spec | Last Static | S.Result | Last Cat | C.Result | Priority
-----------------------|----------|----------|-------------|----------|----------|----------|----------
gate-check             | gate     | YES      | never       | —        | never    | —        | critical
design-review          | review   | YES      | never       | —        | never    | —        | critical
...

AGENTS (49 total)
Agent specs written: 49 (100%)

Agent                  | Category   | Has Spec | Last Spec   | Result
-----------------------|------------|----------|-------------|--------
creative-director      | director   | YES      | never       | —
technical-director     | director   | YES      | never       | —
...

Top 5 Priority Gaps (skills with no spec, critical/high priority):
(none if all specs are written)

Skill coverage:  72/72 specs (100%)
Agent coverage:  49/49 specs (100%)
```
```
=== Skill Test Coverage Audit ===
Date: [date]

SKILLS (72 total)
Specs written: 72 (100%) | Never static tested: 72 | Never category tested: 72

Skill                  | Cat      | Has Spec | Last Static | S.Result | Last Cat | C.Result | Priority
-----------------------|----------|----------|-------------|----------|----------|----------|----------
gate-check             | gate     | YES      | never       | —        | never    | —        | critical
design-review          | review   | YES      | never       | —        | never    | —        | critical
...

AGENTS (49 total)
Agent specs written: 49 (100%)

Agent                  | Category   | Has Spec | Last Spec   | Result
-----------------------|------------|----------|-------------|--------
creative-director      | director   | YES      | never       | —
technical-director     | director   | YES      | never       | —
...

Top 5 Priority Gaps (skills with no spec, critical/high priority):
(none if all specs are written)

Skill coverage:  72/72 specs (100%)
Agent coverage:  49/49 specs (100%)
```

No file writes in audit mode.
审计模式下不写入文件。

Offer: "Would you like to run `/skill-test static all` to check structural
compliance across all skills? `/skill-test category all` to run category rubric
checks? Or `/skill-test spec [name]` to run a specific behavioral test?"
提供："Would you like to run `/skill-test static all` to check structural
compliance across all skills? `/skill-test category all` to run category rubric
checks? Or `/skill-test spec [name]` to run a specific behavioral test?"

---

## Phase 3: Recommended Next Steps
## 阶段 3：推荐的后续步骤

After any mode completes, offer contextual follow-up:
任何模式完成后，提供上下文相关的后续操作：

- After `static [name]`: "Run `/skill-test spec [name]` to validate behavioral
  correctness if a test spec exists."
- After `static all` with failures: "Address NON-COMPLIANT skills first. Run
  `/skill-test static [name]` individually for detailed remediation guidance."
- After `spec [name]` PASS: "Update `CCGS Skill Testing Framework/catalog.yaml` to record this
  pass date. Consider running `/skill-test audit` to find the next spec gap."
- After `spec [name]` FAIL: "Review the failing assertions and update the skill
  or the test spec to resolve the mismatch."
- After `audit`: "Start with the critical-priority gaps. Use the spec template
  at `CCGS Skill Testing Framework/templates/skill-test-spec.md` to create new specs."
- `static [name]` 之后："Run `/skill-test spec [name]` to validate behavioral
  correctness if a test spec exists."
- 带有失败的 `static all` 之后："Address NON-COMPLIANT skills first. Run
  `/skill-test static [name]` individually for detailed remediation guidance."
- `spec [name]` PASS 之后："Update `CCGS Skill Testing Framework/catalog.yaml` to record this
  pass date. Consider running `/skill-test audit` to find the next spec gap."
- `spec [name]` FAIL 之后："Review the failing assertions and update the skill
  or the test spec to resolve the mismatch."
- `audit` 之后："Start with the critical-priority gaps. Use the spec template
  at `CCGS Skill Testing Framework/templates/skill-test-spec.md` to create new specs."
