---
name: estimate
description: "Estimates task effort by analyzing complexity, dependencies, historical velocity, and risk factors. Produces a structured estimate with confidence levels."
argument-hint: "[task-description]"
user-invocable: true
allowed-tools: Read, Glob, Grep
model: sonnet
---
---
name: estimate
description: "通过分析复杂度、依赖、历史速度和风险因素估算任务工作量。生成带置信度级别的结构化估算。"
argument-hint: "[task-description]"
user-invocable: true
allowed-tools: Read, Glob, Grep
model: sonnet
---

## Phase 1: Understand the Task

Read the task description from the argument. If the description is too vague to estimate meaningfully, ask for clarification before proceeding.

Read CLAUDE.md for project context: tech stack, coding standards, architectural patterns, and any estimation guidelines.

Read relevant design documents from `design/gdd/` if the task relates to a documented feature or system.

## 第 1 阶段：理解任务

从参数读取任务描述。如描述过于模糊无法有意义估算，继续前请求澄清。

读取 CLAUDE.md 获取项目上下文：技术栈、编码标准、架构模式和任何估算指南。

如任务与已文档化的功能或系统相关，从 `design/gdd/` 读取相关设计文档。

---

## Phase 2: Scan Affected Code

Identify files and modules that would need to change:

- Assess complexity (size, dependency count, cyclomatic complexity)
- Identify integration points with other systems
- Check for existing test coverage in the affected areas
- Read past sprint data from `production/sprints/` for similar completed tasks and historical velocity

## 第 2 阶段：扫描受影响代码

识别需要更改的文件和模块：

- 评估复杂度（规模、依赖数、圈复杂度）
- 识别与其他系统的集成点
- 检查受影响区域的现有测试覆盖
- 从 `production/sprints/` 读取过往冲刺数据以了解类似已完成任务和历史速度

---

## Phase 3: Analyze Complexity Factors

**Code Complexity:**
- Lines of code in affected files
- Number of dependencies and coupling level
- Whether this touches core/engine code vs leaf/feature code
- Whether existing patterns can be followed or new patterns are needed

**Scope:**
- Number of systems touched
- New code vs modification of existing code
- Amount of new test coverage required
- Data migration or configuration changes needed

**Risk:**
- New technology or unfamiliar libraries
- Unclear or ambiguous requirements
- Dependencies on unfinished work
- Cross-system integration complexity
- Performance sensitivity

## 第 3 阶段：分析复杂度因素

**代码复杂度：**
- 受影响文件的代码行数
- 依赖数量和耦合程度
- 是否触及核心/引擎代码 vs 叶节点/功能代码
- 是否可遵循现有模式还是需要新模式

**范围：**
- 涉及的系统数量
- 新代码 vs 修改现有代码
- 所需新测试覆盖量
- 所需数据迁移或配置变更

**风险：**
- 新技术或不熟悉的库
- 不明确或含糊的需求
- 依赖未完成的工作
- 跨系统集成复杂度
- 性能敏感性

---

## Phase 4: Generate the Estimate

```markdown
## Task Estimate: [Task Name]
Generated: [Date]

### Task Description
[Restate the task clearly in 1-2 sentences]

### Complexity Assessment

| Factor | Assessment | Notes |
|--------|-----------|-------|
| Systems affected | [List] | [Core, gameplay, UI, etc.] |
| Files likely modified | [Count] | [Key files listed below] |
| New code vs modification | [Ratio] | |
| Integration points | [Count] | [Which systems interact] |
| Test coverage needed | [Low / Medium / High] | |
| Existing patterns available | [Yes / Partial / No] | |

**Key files likely affected:**
- `[path/to/file1]` -- [what changes here]

### Effort Estimate

| Scenario | Days | Assumption |
|----------|------|------------|
| Optimistic | [X] | Everything goes right, no surprises |
| Expected | [Y] | Normal pace, minor issues, one round of review |
| Pessimistic | [Z] | Significant unknowns surface, blocked for a day |

**Recommended budget: [Y days]**

### Confidence: [High / Medium / Low]

[Explain which factors drive the confidence level for this specific task.]

### Risk Factors

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|

### Dependencies

| Dependency | Status | Impact if Delayed |
|-----------|--------|-------------------|

### Suggested Breakdown

| # | Sub-task | Estimate | Notes |
|---|----------|----------|-------|
| 1 | [Research / spike] | [X days] | |
| 2 | [Core implementation] | [X days] | |
| 3 | [Testing and validation] | [X days] | |
| | **Total** | **[Y days]** | |

### Notes and Assumptions
- [Key assumption that affects the estimate]
- [Any caveats about scope boundaries]
```

Output the estimate with a brief summary: recommended budget, confidence level, and the single biggest risk factor.

This skill is read-only — no files are written. Verdict: **COMPLETE** — estimate generated.

## 第 4 阶段：生成估算

```markdown
## Task Estimate: [Task Name]
Generated: [Date]

### Task Description
[Restate the task clearly in 1-2 sentences]

### Complexity Assessment

| Factor | Assessment | Notes |
|--------|-----------|-------|
| Systems affected | [List] | [Core, gameplay, UI, etc.] |
| Files likely modified | [Count] | [Key files listed below] |
| New code vs modification | [Ratio] | |
| Integration points | [Count] | [Which systems interact] |
| Test coverage needed | [Low / Medium / High] | |
| Existing patterns available | [Yes / Partial / No] | |

**Key files likely affected:**
- `[path/to/file1]` -- [what changes here]

### Effort Estimate

| Scenario | Days | Assumption |
|----------|------|------------|
| Optimistic | [X] | Everything goes right, no surprises |
| Expected | [Y] | Normal pace, minor issues, one round of review |
| Pessimistic | [Z] | Significant unknowns surface, blocked for a day |

**Recommended budget: [Y days]**

### Confidence: [High / Medium / Low]

[Explain which factors drive the confidence level for this specific task.]

### Risk Factors

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|

### Dependencies

| Dependency | Status | Impact if Delayed |
|-----------|--------|-------------------|

### Suggested Breakdown

| # | Sub-task | Estimate | Notes |
|---|----------|----------|-------|
| 1 | [Research / spike] | [X days] | |
| 2 | [Core implementation] | [X days] | |
| 3 | [Testing and validation] | [X days] | |
| | **Total** | **[Y days]** | |

### Notes and Assumptions
- [Key assumption that affects the estimate]
- [Any caveats about scope boundaries]
```

输出估算并附简要总结：推荐预算、置信度级别和单个最大风险因素。

本技能只读 —— 不写入文件。判定：**COMPLETE** —— 估算已生成。

---

## Phase 5: Next Steps

- If confidence is Low: recommend a time-boxed spike (`/prototype`) before committing.
- If the task is > 10 days: recommend breaking it into smaller stories via `/create-stories`.
- To schedule the task: run `/sprint-plan update` to add it to the next sprint.

### Guidelines

- Always give a range (optimistic / expected / pessimistic), never a single number
- The recommended budget should be the expected estimate, not the optimistic one
- Round to half-day increments — estimating in hours implies false precision for tasks longer than a day
- Do not pad estimates silently — call out risk explicitly so the team can decide

## 第 5 阶段：下一步

- 如置信度为 Low：在承诺前建议限时 spike（`/prototype`）。
- 如任务 > 10 天：建议通过 `/create-stories` 拆分为更小的故事。
- 要安排任务：运行 `/sprint-plan update` 将其添加到下个冲刺。

### 指南

- 始终给出范围（乐观 / 预期 / 悲观），绝不给单一数字
- 推荐预算应为预期估算，而非乐观估算
- 四舍五入到半天增量 —— 以小时估算对超过一天的任务暗示虚假精度
- 不要静默填充估算 —— 显式指出风险让团队决定
