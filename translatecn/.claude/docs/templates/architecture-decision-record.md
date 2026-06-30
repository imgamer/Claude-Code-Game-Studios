# ADR-[NNNN]: [Title]
# ADR-[NNNN]：[标题]

## Status
## 状态

[Proposed | Accepted | Deprecated | Superseded by ADR-XXXX]
[提议中 | 已接受 | 已弃用 | 被 ADR-XXXX 取代]

## Date
## 日期

[YYYY-MM-DD — when this ADR was written]
[YYYY-MM-DD — 本 ADR 编写日期]

## Last Verified
## 最后验证

[YYYY-MM-DD — when this ADR was last confirmed accurate against the current
engine version and design. Update this date when you re-read and confirm it
is still correct, even if nothing changed.]
[YYYY-MM-DD — 本 ADR 最后一次确认与当前引擎版本和设计一致的时间。当你重新阅读并确认其仍然正确时，即使内容未变也应更新此日期。]

## Decision Makers
## 决策者

[Who was involved in this decision]
[参与本决策的人员]

## Summary
## 摘要

[2 sentences: what problem this ADR solves, and what was decided. Written for
tiered context loading — a skill scanning 20 ADRs uses this to decide whether
to read the full decision. Be specific: name the system, the problem, and the
chosen approach.]
[2 句话：本 ADR 解决什么问题、决定了什么。本段面向分层上下文加载编写 — 当技能扫描 20 份 ADR 时，会用本节决定是否阅读完整决策。要具体：指明系统、问题与所选方案。]

## Engine Compatibility
## 引擎兼容性

| Field | Value |
|-------|-------|
| **Engine** | [e.g. Godot 4.6 / Unity 6 / Unreal Engine 5.4] |
| **Domain** | [Physics / Rendering / UI / Audio / Navigation / Animation / Networking / Core / Input / Scripting] |
| **Knowledge Risk** | [LOW — in training data / MEDIUM — near cutoff, verify / HIGH — post-cutoff, must verify] |
| **References Consulted** | [e.g. `docs/engine-reference/godot/modules/physics.md`, `breaking-changes.md`] |
| **Post-Cutoff APIs Used** | [Specific APIs from post-cutoff engine versions this decision depends on, or "None"] |
| **Verification Required** | [Concrete behaviours to test against the target engine version before shipping, or "None"] |

| 字段 | 值 |
|-------|-------|
| **引擎** | [例如 Godot 4.6 / Unity 6 / Unreal Engine 5.4] |
| **领域** | [物理 / 渲染 / UI / 音频 / 导航 / 动画 / 网络 / 核心 / 输入 / 脚本] |
| **知识风险** | [低 — 在训练数据中 / 中 — 接近截止，需验证 / 高 — 截止之后，必须验证] |
| **已查阅参考资料** | [例如 `docs/engine-reference/godot/modules/physics.md`、`breaking-changes.md`] |
| **使用的训练截止后 API** | [本决策依赖的、来自训练截止后引擎版本的具体 API，或 "无"] |
| **所需验证** | [发布前须针对目标引擎版本测试的具体行为，或 "无"] |

> **Note**: If Knowledge Risk is MEDIUM or HIGH, this ADR must be re-validated if the
> project upgrades engine versions. Flag it as "Superseded" and write a new ADR.
> **注意**：若知识风险为中或高，则当项目升级引擎版本时必须重新验证本 ADR。将其标记为 "已取代" 并编写新的 ADR。

## ADR Dependencies
## ADR 依赖

| Field | Value |
|-------|-------|
| **Depends On** | [ADR-NNNN (must be Accepted before this can be implemented), or "None"] |
| **Enables** | [ADR-NNNN (this ADR unlocks that decision), or "None"] |
| **Blocks** | [Epic/Story name — cannot start until this ADR is Accepted, or "None"] |
| **Ordering Note** | [Any sequencing constraint that isn't captured above] |

| 字段 | 值 |
|-------|-------|
| **依赖于** | [ADR-NNNN（须先被接受才能实现本 ADR），或 "无"] |
| **解锁** | [ADR-NNNN（本 ADR 解锁该决策），或 "无"] |
| **阻塞** | [史诗/故事名 — 本 ADR 被接受前不可启动，或 "无"] |
| **顺序备注** | [上述未涵盖的任何排序约束] |

## Context
## 上下文

### Problem Statement
### 问题陈述

[What problem are we solving? Why must this decision be made now? What is the
cost of not deciding?]
[我们在解决什么问题？为何必须现在做此决策？不做决策的代价是什么？]

### Current State
### 当前状态

[How does the system work today? What is wrong with the current approach?]
[系统今日如何运作？当前方法有何问题？]

### Constraints
### 约束

- [Technical constraints -- engine limitations, platform requirements]
- [技术约束 — 引擎限制、平台要求]
- [Timeline constraints -- deadline pressures, dependencies]
- [进度约束 — 截止压力、依赖]
- [Resource constraints -- team size, expertise available]
- [资源约束 — 团队规模、可用专业能力]
- [Compatibility requirements -- must work with existing systems]
- [兼容性要求 — 必须与现有系统协同]

### Requirements
### 需求

- [Functional requirement 1]
- [功能需求 1]
- [Functional requirement 2]
- [功能需求 2]
- [Performance requirement -- specific, measurable]
- [性能需求 — 具体、可度量]
- [Scalability requirement]
- [可扩展性需求]

## Decision
## 决策

[The specific technical decision, described in enough detail for someone to
implement it without further clarification.]
[具体的技术决策，描述足够详细，使他人无需进一步澄清即可实现。]

### Architecture
### 架构

```
[ASCII diagram showing the system architecture this decision creates.
Show components, data flow direction, and key interfaces.]
```
```
[展示本决策所创建系统架构的 ASCII 图。
展示组件、数据流向与关键接口。]
```

### Key Interfaces
### 关键接口

```
[Pseudocode or language-specific interface definitions that this decision
creates. These become the contracts that implementers must respect.]
```
```
[本决策创建的伪代码或语言特定的接口定义。
这些成为实现者必须遵守的契约。]
```

### Implementation Guidelines
### 实现指南

[Specific guidance for the programmer implementing this decision.]
[为实施本决策的程序员提供的具体指南。]

## Alternatives Considered
## 考虑过的备选方案

### Alternative 1: [Name]
### 备选方案 1：[名称]

- **Description**: [How this approach would work]
- **描述**：[该方案如何运作]
- **Pros**: [What is good about this approach]
- **优点**：[该方案的优点]
- **Cons**: [What is bad about this approach]
- **缺点**：[该方案的缺点]
- **Estimated Effort**: [Relative effort compared to chosen approach]
- **预计工作量**：[相对于所选方案的相对工作量]
- **Rejection Reason**: [Why this was not chosen]
- **否决原因**：[为何未选择此方案]

### Alternative 2: [Name]
### 备选方案 2：[名称]

[Same structure as above]
[结构与上述相同]

## Consequences
## 后果

### Positive
### 正面

- [Good outcomes of this decision]
- [本决策带来的良好结果]

### Negative
### 负面

- [Trade-offs and costs we are accepting]
- [我们接受的权衡与代价]

### Neutral
### 中性

- [Changes that are neither good nor bad, just different]
- [既非好也非坏、仅是不同的变化]

## Risks
## 风险

| Risk | Probability | Impact | Mitigation |
|------|------------|--------|-----------|

| 风险 | 概率 | 影响 | 缓解措施 |
|------|------------|--------|-----------|

## Performance Implications
## 性能影响

| Metric | Before | Expected After | Budget |
|--------|--------|---------------|--------|
| CPU (frame time) | [X]ms | [Y]ms | [Z]ms |
| Memory | [X]MB | [Y]MB | [Z]MB |
| Load Time | [X]s | [Y]s | [Z]s |
| Network (if applicable) | [X]KB/s | [Y]KB/s | [Z]KB/s |

| 指标 | 之前 | 预期之后 | 预算 |
|--------|--------|---------------|--------|
| CPU（帧时间） | [X]ms | [Y]ms | [Z]ms |
| 内存 | [X]MB | [Y]MB | [Z]MB |
| 加载时间 | [X]s | [Y]s | [Z]s |
| 网络（如适用） | [X]KB/s | [Y]KB/s | [Z]KB/s |

## Migration Plan
## 迁移计划

[If this changes existing systems, the step-by-step plan to migrate.]
[若本决策改变现有系统，请提供逐步迁移计划。]

1. [Step 1 -- what changes, what breaks, how to verify]
1. [步骤 1 — 改变什么、破坏什么、如何验证]
2. [Step 2]
2. [步骤 2]
3. [Step 3]
3. [步骤 3]

**Rollback plan**: [How to revert if this decision proves wrong]
**回滚计划**：[若本决策被证明错误，如何回退]

## Validation Criteria
## 验证标准

[How we will know this decision was correct after implementation.]
[实现后我们如何知道本决策正确。]

- [ ] [Measurable criterion 1]
- [ ] [可度量标准 1]
- [ ] [Measurable criterion 2]
- [ ] [可度量标准 2]
- [ ] [Performance criterion]
- [ ] [性能标准]

## GDD Requirements Addressed
## 已解决的 GDD 需求

<!-- This section is MANDATORY. Every ADR must trace back to at least one GDD
     requirement, or explicitly state it is a foundational decision with no GDD
     dependency. Traceability is audited by /architecture-review. -->
<!-- 本节为必填。每份 ADR 必须追溯到至少一条 GDD 需求，
     或明确声明其为无 GDD 依赖的基础性决策。可追溯性由 /architecture-review 审计。 -->

| GDD Document | System | Requirement | How This ADR Satisfies It |
|-------------|--------|-------------|--------------------------|
| [e.g. `design/gdd/combat.md`] | [e.g. Combat] | [e.g. "Hitbox detection must resolve within 1 frame"] | [e.g. "Jolt physics collision queries run synchronously in _physics_process"] |

| GDD 文档 | 系统 | 需求 | 本 ADR 如何满足 |
|-------------|--------|-------------|--------------------------|
| [例如 `design/gdd/combat.md`] | [例如 战斗] | [例如 "受击框检测须在 1 帧内解决"] | [例如 "Jolt 物理碰撞查询在 _physics_process 中同步运行"] |

> If this is a foundational decision with no direct GDD dependency, write:
> "Foundational — no GDD requirement. Enables: [list what GDD systems this
> decision unlocks or constrains]"
> 若这是无直接 GDD 依赖的基础性决策，请写：
> "基础性 — 无 GDD 需求。解锁：[列出本决策解锁或约束的 GDD 系统]"

## Related
## 相关

- [Link to related ADRs — note if supersedes, contradicts, or depends on]
- [相关 ADR 链接 — 注明是取代、矛盾还是依赖]
- [Link to relevant code files once implemented]
- [实现后相关代码文件的链接]
