---
name: project-stage-detect
description: "Automatically analyze project state, detect stage, identify gaps, and recommend next steps based on existing artifacts. Use when user asks 'where are we in development', 'what stage are we in', 'full project audit'."
argument-hint: "[optional: role filter like 'programmer' or 'designer']"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write
model: haiku
# Read-only diagnostic skill — no specialist agent delegation needed
---
---
name: project-stage-detect
description: "自动分析项目状态、检测阶段、识别缺口，并根据现有工件推荐下一步。当用户询问 '我们在开发哪个阶段'、'我们处于什么阶段'、'完整项目审计' 时使用。"
argument-hint: "[optional: role filter like 'programmer' or 'designer']"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write
model: haiku
# Read-only diagnostic skill — no specialist agent delegation needed
---

# Project Stage Detection
# 项目阶段检测

This skill scans your project to determine its current development stage, completeness
of artifacts, and gaps that need attention. It's especially useful when:
- Starting with an existing project
- Onboarding to a codebase
- Checking what's missing before a milestone
- Understanding "where are we?"
此技能扫描你的项目以确定其当前开发阶段、工件的完整度，
以及需要关注的缺口。它在以下情况特别有用：
- 接手一个现有项目
- 入职一个代码库
- 在里程碑之前检查缺少什么
- 理解 "我们在哪里？"

---

## Workflow
## 工作流

### 1. Scan Key Directories
### 1. 扫描关键目录

Analyze project structure and content:
分析项目结构和内容：

**Design Documentation** (`design/`):
- Count GDD files in `design/gdd/*.md`
- Check for game-concept.md, game-pillars.md, systems-index.md
- If systems-index.md exists, count total systems vs. designed systems
- Analyze completeness (Overview, Detailed Design, Edge Cases, etc.)
- Count narrative docs in `design/narrative/`
- Count level designs in `design/levels/`
**设计文档**（`design/`）：
- 统计 `design/gdd/*.md` 中的 GDD 文件
- 检查是否存在 game-concept.md、game-pillars.md、systems-index.md
- 如果 systems-index.md 存在，统计总系统数 vs. 已设计系统数
- 分析完整度（概述、详细设计、边缘情况等）
- 统计 `design/narrative/` 中的叙事文档
- 统计 `design/levels/` 中的关卡设计

**Source Code** (`src/`):
- Count source files (language-agnostic)
- Identify major systems (directories with 5+ files)
- Check for core/, gameplay/, ai/, networking/, ui/ directories
- Estimate lines of code (rough scale)
**源代码**（`src/`）：
- 统计源文件（与语言无关）
- 识别主要系统（包含 5 个以上文件的目录）
- 检查 core/、gameplay/、ai/、networking/、ui/ 目录
- 估算代码行数（粗略规模）

**Production Artifacts** (`production/`):
- Check for active sprint plans
- Look for milestone definitions
- Find roadmap documents
**生产工件**（`production/`）：
- 检查是否有活跃的冲刺计划
- 查找里程碑定义
- 查找路线图文档

**Prototypes** (`prototypes/`):
- Count prototype directories
- Check for READMEs (documented vs undocumented)
- Assess if prototypes are archived or active
**原型**（`prototypes/`）：
- 统计原型目录
- 检查是否有 README（有文档 vs 无文档）
- 评估原型是已归档还是活跃

**Architecture Docs** (`docs/architecture/`):
- Count ADRs (Architecture Decision Records)
- Check for overview/index documents
**架构文档**（`docs/architecture/`）：
- 统计 ADR（架构决策记录）
- 检查是否有概述/索引文档

**Tests** (`tests/`):
- Count test files
- Estimate test coverage (rough heuristic)
**测试**（`tests/`）：
- 统计测试文件
- 估算测试覆盖率（粗略启发式）

### 2. Classify Project Stage
### 2. 分类项目阶段

Based on scanned artifacts, determine stage. Check `production/stage.txt` first —
if it exists, use its value (explicit override from `/gate-check`). Otherwise,
auto-detect using these heuristics (check from most-advanced backward):
基于扫描的工件，确定阶段。首先检查 `production/stage.txt` —
如果存在，使用其值（来自 `/gate-check` 的显式覆盖）。否则，
使用以下启发式规则自动检测（从最先进的向后检查）：

| Stage | Indicators |
|-------|-----------|
| **Concept** | No game concept doc, brainstorming phase |
| **Systems Design** | Game concept exists, systems index missing or incomplete |
| **Technical Setup** | Systems index exists, engine not configured |
| **Pre-Production** | Engine configured, `src/` has <10 source files |
| **Production** | `src/` has 10+ source files, active development |
| **Polish** | Explicit only (set by `/gate-check` Production → Polish gate) |
| **Release** | Explicit only (set by `/gate-check` Polish → Release gate) |
| 阶段 | 指标 |
|-------|-----------|
| **Concept** | 无游戏概念文档，头脑风暴阶段 |
| **Systems Design** | 游戏概念存在，系统索引缺失或不完整 |
| **Technical Setup** | 系统索引存在，引擎未配置 |
| **Pre-Production** | 引擎已配置，`src/` 少于 10 个源文件 |
| **Production** | `src/` 有 10 个以上源文件，活跃开发 |
| **Polish** | 仅显式设置（由 `/gate-check` Production → Polish 门禁设置） |
| **Release** | 仅显式设置（由 `/gate-check` Polish → Release 门禁设置） |

### 3. Collaborative Gap Identification
### 3. 协作式缺口识别

**DO NOT** just list missing files. Instead, **ask clarifying questions**:
**不要**只是列出缺失的文件。相反，**提出澄清问题**：

- "I see combat code (`src/gameplay/combat/`) but no `design/gdd/combat-system.md`. Was this prototyped first, or should we reverse-document?"
- "You have 15 ADRs but no architecture overview. Should I create one to help new contributors?"
- "No sprint plans in `production/`. Are you tracking work elsewhere (Jira, Trello, etc.)?"
- "I found a game concept but no systems index. Have you decomposed the concept into individual systems yet, or should we run `/map-systems`?"
- "Prototypes directory has 3 projects with no READMEs. Were these experiments, or do they need documentation?"
- "我看到战斗代码（`src/gameplay/combat/`）但没有 `design/gdd/combat-system.md`。这是先做原型的，还是我们应该反向文档化？"
- "你有 15 个 ADR 但没有架构概述。我应该创建一个来帮助新贡献者吗？"
- "`production/` 中没有冲刺计划。你是否在其他地方跟踪工作（Jira、Trello 等）？"
- "我找到了游戏概念但没有系统索引。你是否已经将概念分解为单个系统，还是我们应该运行 `/map-systems`？"
- "原型目录有 3 个项目没有 README。这些是实验，还是需要文档？"

### 4. Generate Stage Report
### 4. 生成阶段报告

Use template: `.claude/docs/templates/project-stage-report.md`
使用模板：`.claude/docs/templates/project-stage-report.md`

**Report structure**:
**报告结构**：
```markdown
# Project Stage Analysis

**Date**: [date]
**Stage**: [Concept/Systems Design/Technical Setup/Pre-Production/Production/Polish/Release]
**Stage Confidence**: [PASS — clearly detected / CONCERNS — ambiguous signals / FAIL — critical gaps block progress]

## Completeness Overview
- Design: [X%] ([N] docs, [gaps])
- Code: [X%] ([N] files, [systems])
- Architecture: [X%] ([N] ADRs, [gaps])
- Production: [X%] ([status])
- Tests: [X%] ([coverage estimate])

## Gaps Identified
1. [Gap description + clarifying question]
2. [Gap description + clarifying question]

## Recommended Next Steps
[Priority-ordered list based on stage and role]
```
```markdown
# Project Stage Analysis

**Date**: [date]
**Stage**: [Concept/Systems Design/Technical Setup/Pre-Production/Production/Polish/Release]
**Stage Confidence**: [PASS — clearly detected / CONCERNS — ambiguous signals / FAIL — critical gaps block progress]

## Completeness Overview
- Design: [X%] ([N] docs, [gaps])
- Code: [X%] ([N] files, [systems])
- Architecture: [X%] ([N] ADRs, [gaps])
- Production: [X%] ([status])
- Tests: [X%] ([coverage estimate])

## Gaps Identified
1. [Gap description + clarifying question]
2. [Gap description + clarifying question]

## Recommended Next Steps
[Priority-ordered list based on stage and role]
```

### 5. Role-Filtered Recommendations (Optional)
### 5. 按角色过滤的推荐（可选）

If user provided a role argument (e.g., `/project-stage-detect programmer`):
如果用户提供了角色参数（例如 `/project-stage-detect programmer`）：

**Programmer**:
- Focus on architecture docs, test coverage, missing ADRs
- Code-to-docs gaps
**程序员**：
- 关注架构文档、测试覆盖率、缺失的 ADR
- 代码到文档的缺口

**Designer**:
- Focus on GDD completeness, missing design sections
- Prototype documentation
**设计师**：
- 关注 GDD 完整度、缺失的设计章节
- 原型文档

**Producer**:
- Focus on sprint plans, milestone tracking, roadmap
- Cross-team coordination docs
**制作人**：
- 关注冲刺计划、里程碑跟踪、路线图
- 跨团队协调文档

**General** (no role):
- Holistic view of all gaps
- Highest-priority items across domains
**通用**（无角色）：
- 所有缺口的全局视图
- 跨领域的最高优先级项

### 6. Request Approval Before Writing
### 6. 在写入之前请求批准

**Collaborative protocol**:
**协作协议**：
```
I've analyzed your project. Here's what I found:

[Show summary]

Gaps identified:
1. [Gap 1 + question]
2. [Gap 2 + question]

Recommended next steps:
- [Priority 1]
- [Priority 2]
- [Priority 3]

May I write the full stage analysis to production/project-stage-report.md?
```
```
I've analyzed your project. Here's what I found:

[Show summary]

Gaps identified:
1. [Gap 1 + question]
2. [Gap 2 + question]

Recommended next steps:
- [Priority 1]
- [Priority 2]
- [Priority 3]

May I write the full stage analysis to production/project-stage-report.md?
```

Wait for user approval before creating the file.
在创建文件之前等待用户批准。

---

## Example Usage
## 示例用法

```bash
# General project analysis
/project-stage-detect

# Programmer-focused analysis
/project-stage-detect programmer

# Designer-focused analysis
/project-stage-detect designer
```
```bash
# General project analysis
/project-stage-detect

# Programmer-focused analysis
/project-stage-detect programmer

# Designer-focused analysis
/project-stage-detect designer
```

---

## Follow-Up Actions
## 后续行动

After generating the report, suggest relevant next steps:
生成报告后，建议相关的后续步骤：

- **Concept exists but no systems index?** → `/map-systems` to decompose into systems
- **Missing design docs?** → `/reverse-document design src/[system]`
- **Missing architecture docs?** → `/architecture-decision` or `/reverse-document architecture`
- **Prototypes need documentation?** → `/reverse-document concept prototypes/[name]`
- **No sprint plan?** → `/sprint-plan`
- **Approaching milestone?** → `/milestone-review`
- **概念存在但没有系统索引？** → `/map-systems` 分解为系统
- **缺少设计文档？** → `/reverse-document design src/[system]`
- **缺少架构文档？** → `/architecture-decision` 或 `/reverse-document architecture`
- **原型需要文档？** → `/reverse-document concept prototypes/[name]`
- **没有冲刺计划？** → `/sprint-plan`
- **接近里程碑？** → `/milestone-review`

---

## Collaborative Protocol
## 协作协议

This skill follows the collaborative design principle:
此技能遵循协作设计原则：

1. **Question First**: Ask about gaps, don't assume
2. **Present Options**: "Should I create X, or is it tracked elsewhere?"
3. **User Decides**: Wait for direction
4. **Show Draft**: Display report summary
5. **Get Approval**: "May I write to production/project-stage-report.md?"
1. **先提问**：询问缺口，不要假设
2. **呈现选项**："我应该创建 X，还是它在其他地方被跟踪？"
3. **用户决定**：等待指示
4. **展示草稿**：显示报告摘要
5. **获得批准**："我可以写入 production/project-stage-report.md 吗？"

**Never** silently write files. **Always** show findings and ask before creating artifacts.
**切勿**静默写入文件。**始终**在创建工件之前展示发现并询问。
