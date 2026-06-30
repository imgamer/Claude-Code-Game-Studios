# Systems Index: [Game Title]
# 系统索引：[游戏标题]

> **Status**: [Draft / Under Review / Approved]
> **Created**: [Date]
> **Last Updated**: [Date]
> **Source Concept**: design/gdd/game-concept.md
> **状态**：[草稿 / 审核中 / 已批准]
> **创建**：[Date]
> **最后更新**：[Date]
> **源概念**：design/gdd/game-concept.md

---

## Overview
## 概述

[One paragraph explaining the game's mechanical scope. What kind of systems does
this game need? Reference the core loop and game pillars. This should help any
team member understand the "big picture" of what needs to be designed and built.]
[一段说明游戏的机械范围。此游戏需要什么类型的系统？参考核心循环和游戏支柱。
这应帮助任何团队成员理解需要设计和构建的"全局图景"。]

---

## Systems Enumeration
## 系统枚举

| # | System Name | Category | Priority | Status | Design Doc | Depends On |
|---|-------------|----------|----------|--------|------------|------------|
| 1 | [e.g., Player Controller] | Core | MVP | [Not Started / In Design / In Review / Approved / Implemented] | [design/gdd/player-controller.md or "—"] | [e.g., Input System, Physics] |
| 2 | [e.g., Camera System] | Core | MVP | Not Started | — | Player Controller |
| # | 系统名称 | 类别 | 优先级 | 状态 | 设计文档 | 依赖于 |
|---|-------------|----------|----------|--------|------------|------------|
| 1 | [例如，Player Controller] | 核心 | MVP | [未开始 / 设计中 / 审核中 / 已批准 / 已实现] | [design/gdd/player-controller.md 或 "—"] | [例如，Input System, Physics] |
| 2 | [例如，Camera System] | 核心 | MVP | 未开始 | — | Player Controller |

[Add a row for every identified system. Use the categories and priority tiers
defined below. Mark systems that were inferred (not explicitly in the concept doc)
with "(inferred)" in the system name.]
[为每个已识别的系统添加一行。使用下面定义的类别和优先级层级。
用系统名中的 "(inferred)" 标记推断的系统（概念文档中未明确出现）。]

---

## Categories
## 类别

| Category | Description | Typical Systems |
|----------|-------------|-----------------|
| **Core** | Foundation systems everything depends on | Player controller, input, physics, camera, scene management, state machine |
| **Gameplay** | The systems that make the game fun | Combat, AI, stealth, movement abilities, interaction |
| **Progression** | How the player grows over time | XP/leveling, skill trees, unlocks, achievements, reputation |
| **Economy** | Resource creation and consumption | Currency, loot, crafting, shops, item database, drop tables |
| **Persistence** | Save state and continuity | Save/load, settings, cloud sync, profile management |
| **UI** | Player-facing information displays | HUD, menus, inventory screen, dialogue UI, map, notifications |
| **Audio** | Sound and music systems | Music manager, SFX bus, ambient audio, adaptive music, voice |
| **Narrative** | Story and dialogue delivery | Dialogue system, quest tracking, cutscenes, journal, lore entries |
| **Meta** | Systems outside the core game loop | Analytics, tutorials/onboarding, accessibility options, photo mode |
| 类别 | 描述 | 典型系统 |
|----------|-------------|-----------------|
| **核心** | 一切所依赖的基础系统 | 玩家控制器、输入、物理、相机、场景管理、状态机 |
| **玩法** | 使游戏有趣的系统 | 战斗、AI、潜行、移动能力、交互 |
| **进阶** | 玩家如何随时间成长 | 经验/升级、技能树、解锁、成就、声望 |
| **经济** | 资源创造与消耗 | 货币、战利品、制作、商店、物品数据库、掉落表 |
| **持久化** | 存档状态与连续性 | 存档/读档、设置、云同步、个人资料管理 |
| **UI** | 面向玩家的信息显示 | HUD、菜单、库存界面、对话 UI、地图、通知 |
| **音频** | 声音与音乐系统 | 音乐管理器、SFX 总线、环境音频、自适应音乐、语音 |
| **叙事** | 故事与对话交付 | 对话系统、任务追踪、过场、日志、背景设定条目 |
| **元** | 核心游戏循环外的系统 | 分析、教程/新手引导、无障碍选项、照片模式 |

[Not every game needs every category. Remove categories that don't apply.
Add custom categories if needed.]
[并非每款游戏都需要每个类别。删除不适用的类别。
如需要，添加自定义类别。]

---

## Priority Tiers
## 优先级层级

| Tier | Definition | Target Milestone | Design Urgency |
|------|------------|------------------|----------------|
| **MVP** | Required for the core loop to function. Without these, you can't test "is this fun?" | First playable prototype | Design FIRST |
| **Vertical Slice** | Required for one complete, polished area. Demonstrates the full experience. | Vertical slice / demo | Design SECOND |
| **Alpha** | All features present in rough form. Complete mechanical scope, placeholder content OK. | Alpha milestone | Design THIRD |
| **Full Vision** | Polish, edge cases, nice-to-haves, and content-complete features. | Beta / Release | Design as needed |
| 层级 | 定义 | 目标里程碑 | 设计紧迫性 |
|------|------------|------------------|----------------|
| **MVP** | 核心循环运作所必需。没有这些，无法测试"这是否有趣？" | 首个可玩原型 | 优先设计 |
| **垂直切片** | 一个完整、打磨区域所必需。演示完整体验。 | 垂直切片 / demo | 第二优先设计 |
| **Alpha** | 所有功能以粗糙形式呈现。完整机械范围，占位内容可接受。 | Alpha 里程碑 | 第三优先设计 |
| **完整愿景** | 打磨、边界情况、锦上添花与内容完整功能。 | Beta / 发布 | 按需设计 |

---

## Dependency Map
## 依赖图

[Systems sorted by dependency order — design and build from top to bottom.
Systems at the top are foundations; systems at the bottom are wrappers.]
[按依赖顺序排序的系统 — 从上到下设计与构建。顶部系统是基础；底部系统是包装层。]

### Foundation Layer (no dependencies)
### 基础层（无依赖）

1. [System] — [one-line rationale for why this is foundational]
1. [系统] — [为何这是基础的一行理由]

### Core Layer (depends on foundation)
### 核心层（依赖于基础）

1. [System] — depends on: [list]
1. [系统] — 依赖于：[列表]

### Feature Layer (depends on core)
### 功能层（依赖于核心）

1. [System] — depends on: [list]
1. [系统] — 依赖于：[列表]

### Presentation Layer (depends on features)
### 表现层（依赖于功能）

1. [System] — depends on: [list]
1. [系统] — 依赖于：[列表]

### Polish Layer (depends on everything)
### 打磨层（依赖一切）

1. [System] — depends on: [list]
1. [系统] — 依赖于：[列表]

---

## Recommended Design Order
## 建议设计顺序

[Combining dependency sort and priority tiers. Design these systems in this
order. Each system's GDD should be completed and reviewed before starting the
next, though independent systems at the same layer can be designed in parallel.]
[结合依赖排序与优先级层级。按此顺序设计这些系统。每个系统的 GDD 应在开始下一个之前完成
并审核，尽管同层独立系统可并行设计。]

| Order | System | Priority | Layer | Agent(s) | Est. Effort |
|-------|--------|----------|-------|----------|-------------|
| 1 | [First system to design] | MVP | Foundation | game-designer | [S/M/L] |
| 2 | [Second system] | MVP | Foundation | game-designer | [S/M/L] |
| 顺序 | 系统 | 优先级 | 层 | 智能体 | 估算工作量 |
|-------|--------|----------|-------|----------|-------------|
| 1 | [首个设计的系统] | MVP | 基础 | game-designer | [S/M/L] |
| 2 | [第二个系统] | MVP | 基础 | game-designer | [S/M/L] |

[Effort estimates: S = 1 session, M = 2-3 sessions, L = 4+ sessions.
A "session" is one focused design conversation producing a complete GDD.]
[工作量估算：S = 1 次会话，M = 2-3 次会话，L = 4+ 次会话。
"会话"指产生完整 GDD 的一次专注设计对话。]

---

## Circular Dependencies
## 循环依赖

[List any circular dependency chains found during analysis. These require
special architectural attention — either break the cycle with an interface,
or design the systems simultaneously.]
[列出分析中发现的任何循环依赖链。这些需要特别架构关注 — 要么用接口打破循环，
要么同时设计这些系统。]

- [None found] OR
- [System A <-> System B: Description of the circular relationship and
  proposed resolution]
- [未找到] 或
- [系统 A <-> 系统 B：循环关系描述与建议的解决方案]

---

## High-Risk Systems
## 高风险系统

[Systems that are technically unproven, design-uncertain, or scope-dangerous.
These should be prototyped early regardless of priority tier.]
[技术上未经验证、设计不确定或范围危险的系统。无论优先级层级，都应尽早原型化。]

| System | Risk Type | Risk Description | Mitigation |
|--------|-----------|-----------------|------------|
| [System] | [Technical / Design / Scope] | [What could go wrong] | [Prototype, research, or scope fallback] |
| 系统 | 风险类型 | 风险描述 | 缓解 |
|--------|-----------|-----------------|------------|
| [系统] | [技术 / 设计 / 范围] | [可能出什么错] | [原型、研究或范围退路] |

---

## Progress Tracker
## 进度追踪器

| Metric | Count |
|--------|-------|
| Total systems identified | [N] |
| Design docs started | [N] |
| Design docs reviewed | [N] |
| Design docs approved | [N] |
| MVP systems designed | [N/total MVP] |
| Vertical Slice systems designed | [N/total VS] |
| 指标 | 计数 |
|--------|-------|
| 已识别系统总数 | [N] |
| 已开始设计文档 | [N] |
| 已审核设计文档 | [N] |
| 已批准设计文档 | [N] |
| 已设计 MVP 系统 | [N/total MVP] |
| 已设计垂直切片系统 | [N/total VS] |

---

## Next Steps
## 后续步骤

- [ ] Review and approve this systems enumeration
- [ ] Design MVP-tier systems first (use `/design-system [system-name]`)
- [ ] Run `/design-review` on each completed GDD
- [ ] Run `/gate-check pre-production` when MVP systems are designed
- [ ] Validate the highest-risk systems with `/vertical-slice` before committing to Production
- [ ] 审核并批准此系统枚举
- [ ] 优先设计 MVP 层级系统（使用 `/design-system [system-name]`）
- [ ] 在每个完成的 GDD 上运行 `/design-review`
- [ ] MVP 系统设计完成后运行 `/gate-check pre-production`
- [ ] 在投入制作之前用 `/vertical-slice` 验证最高风险系统
