# Technical Design: [System Name]
# 技术设计：[系统名称]

## Document Status
## 文档状态
- **Version**: 1.0
- **版本**：1.0
- **Last Updated**: [Date]
- **最后更新**：[日期]
- **Author**: [Agent/Person]
- **作者**：[智能体/人员]
- **Reviewer**: lead-programmer
- **评审人**：首席程序员
- **Related ADR**: [ADR-XXXX if applicable]
- **相关 ADR**：[如适用，填写 ADR-XXXX]
- **Related Design Doc**: [Link to game design doc this implements]
- **相关设计文档**：[链接至本设计实现的游戏设计文档]

## Engine API Surface
## 引擎 API 接口面

| Field | Value |
|-------|-------|
| **Engine** | [e.g. Godot 4.6 / Unity 6 / Unreal Engine 5.4] |
| **APIs Depended On** | [Specific classes/methods/nodes used, version-pinned — e.g. `CharacterBody3D.move_and_slide() (Godot 4.x)`] |
| **References Consulted** | [engine-reference docs read before writing this — e.g. `docs/engine-reference/godot/modules/physics.md`] |
| **Post-Cutoff Features Used** | [Features from engine versions beyond LLM training cutoff, or "None"] |
| **Unverified Assumptions** | [API behaviours assumed but not yet tested against the target version, or "None"] |
| **Engine Upgrade Risk** | [LOW / MEDIUM / HIGH — how fragile is this design if the engine version changes?] |

| 字段 | 值 |
|-------|-------|
| **引擎** | [例如 Godot 4.6 / Unity 6 / Unreal Engine 5.4] |
| **依赖的 API** | [使用的具体类/方法/节点，已锁定版本 — 例如 `CharacterBody3D.move_and_slide() (Godot 4.x)`] |
| **已查阅参考资料** | [编写前阅读的 engine-reference 文档 — 例如 `docs/engine-reference/godot/modules/physics.md`] |
| **使用的训练截止后特性** | [来自 LLM 训练截止后引擎版本的特性，或 "无"] |
| **未经验证的假设** | [已假设但尚未针对目标版本测试的 API 行为，或 "无"] |
| **引擎升级风险** | [低 / 中 / 高 — 若引擎版本变化，本设计有多脆弱？] |

> **Rule**: If any **Unverified Assumptions** are listed, this document cannot be marked
> as Accepted until those assumptions are validated in the actual engine environment.
> **规则**：若列出任何 **未经验证的假设**，则在这些假设在实际引擎环境中得到验证之前，本文档不得标记为 "已接受"。

## Overview
## 概览
[2-3 sentence summary of what this system does and why it exists]
[2-3 句话概述本系统的功能及其存在原因]

## Requirements
## 需求
### Functional Requirements
### 功能需求
- [FR-1]: [Description]
- [FR-1]：[描述]
- [FR-2]: [Description]
- [FR-2]：[描述]

### Non-Functional Requirements
### 非功能需求
- **Performance**: [Budget — e.g., "< 1ms per frame"]
- **性能**：[预算 — 例如 "< 1ms/帧"]
- **Memory**: [Budget — e.g., "< 50MB at peak"]
- **内存**：[预算 — 例如 "峰值 < 50MB"]
- **Scalability**: [Limits — e.g., "Support up to 1000 entities"]
- **可扩展性**：[限制 — 例如 "支持最多 1000 个实体"]
- **Thread Safety**: [Requirements]
- **线程安全**：[要求]

## Architecture
## 架构

### System Diagram
### 系统图
```
[ASCII diagram showing components and data flow]
```
```
[展示组件与数据流的 ASCII 图]
```

### Component Breakdown
### 组件分解
| Component | Responsibility | Owns |
| --------- | -------------- | ---- |
| [Name] | [What it does] | [What data it owns] |

| 组件 | 职责 | 拥有 |
| --------- | -------------- | ---- |
| [名称] | [它做什么] | [拥有哪些数据] |

### Public API
### 公共 API
```
[Interface/API definition in pseudocode or target language]
```
```
[以伪代码或目标语言编写的接口/API 定义]
```

### Data Structures
### 数据结构
```
[Key data structures with field descriptions]
```
```
[关键数据结构及字段描述]
```

### Data Flow
### 数据流
[Step by step: how data moves through the system during a typical frame]
[逐步说明：典型帧中数据如何在系统中流动]

## Implementation Plan
## 实现计划

### Phase 1: [Core Functionality]
### 第 1 阶段：[核心功能]
- [ ] [Task 1]
- [ ] [任务 1]
- [ ] [Task 2]
- [ ] [任务 2]

### Phase 2: [Extended Features]
### 第 2 阶段：[扩展特性]
- [ ] [Task 3]
- [ ] [任务 3]
- [ ] [Task 4]
- [ ] [任务 4]

### Phase 3: [Optimization/Polish]
### 第 3 阶段：[优化/打磨]
- [ ] [Task 5]
- [ ] [任务 5]

## Dependencies
## 依赖
| Depends On | For What |
| ---------- | -------- |
| [System] | [Reason] |

| 依赖于 | 用途 |
| ---------- | -------- |
| [系统] | [原因] |

| Depended On By | For What |
| -------------- | -------- |
| [System] | [Reason] |

| 被依赖于 | 用途 |
| -------------- | -------- |
| [系统] | [原因] |

## Testing Strategy
## 测试策略
- **Unit Tests**: [What to test at unit level]
- **单元测试**：[在单元层面测试什么]
- **Integration Tests**: [Cross-system tests needed]
- **集成测试**：[所需的跨系统测试]
- **Performance Tests**: [Benchmarks to create]
- **性能测试**：[要建立的基准]
- **Edge Cases**: [Specific scenarios to test]
- **边界情况**：[要测试的具体场景]

## Known Limitations
## 已知局限
[What this design intentionally does NOT support and why]
[本设计有意不支持的内容及原因]

## Future Considerations
## 未来考量
[What might need to change if requirements evolve — but do NOT build for this now]
[若需求演进可能需要变更的内容 — 但现在不要为此构建]
