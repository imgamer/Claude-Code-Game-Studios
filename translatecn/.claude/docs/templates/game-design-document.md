# [Mechanic/System Name]
# [机制/系统名称]

> **Status**: Draft | In Review | Approved | Implemented
> **状态**：草稿 | 评审中 | 已批准 | 已实现
> **Author**: [Agent or person]
> **作者**：[智能体或人员]
> **Last Updated**: [Date]
> **最后更新**：[日期]
> **Last Verified**: [Date — when this doc was last confirmed accurate against current design]
> **最后验证**：[日期 — 本文档最后一次确认与当前设计一致的时间]
> **Implements Pillar**: [Which game pillar this supports]
> **实现的支柱**：[本系统支持哪个游戏支柱]

## Summary
## 摘要

[2–3 sentences: what this system is, what it does for the player, and why it
exists in this game. Written for tiered context loading — a skill scanning
20 GDDs uses this section to decide whether to read further. No jargon.]
[2–3 句话：本系统是什么、为玩家提供什么、为何存在于本游戏中。本段面向分层上下文加载编写 — 当技能扫描 20 份 GDD 时，会用本节决定是否继续阅读。不要使用术语。]

> **Quick reference** — Layer: `[Foundation | Core | Feature | Presentation]` · Priority: `[MVP | Vertical Slice | Alpha | Full Vision]` · Key deps: `[System names or "None"]`
> **快速参考** — 层：`[基础 | 核心 | 功能 | 表现]` · 优先级：`[MVP | 垂直切片 | Alpha | 完整愿景]` · 关键依赖：`[系统名或 "无"]`

## Overview
## 概览

[One paragraph that explains this mechanic to someone who knows nothing about
the project. What is it, what does the player do, and why does it exist?]
[一段向对本项目一无所知者解释本机制的话。它是什么、玩家做什么、为何存在？]

## Player Fantasy
## 玩家幻想

[What should the player FEEL when engaging with this mechanic? What is the
emotional or power fantasy being served? This section guides all detail
decisions below.]
[玩家在体验本机制时应"感受"到什么？服务于何种情感或力量幻想？本节指导下方所有细节决策。]

## Detailed Design
## 详细设计

### Core Rules
### 核心规则

[Precise, unambiguous rules. A programmer should be able to implement this
section without asking questions. Use numbered rules for sequential processes
and bullet points for properties.]
[精确、无歧义的规则。程序员应能在不提问的情况下实现本节内容。对顺序流程使用编号规则，对属性使用项目符号。]

### States and Transitions
### 状态与转换

[If this system has states (e.g., weapon states, status effects, phases),
document every state and every valid transition between states.]
[若本系统具有状态（例如武器状态、状态效果、阶段），请记录每个状态及状态间所有合法的转换。]

| State | Entry Condition | Exit Condition | Behavior |
|-------|----------------|----------------|----------|

| 状态 | 进入条件 | 退出条件 | 行为 |
|-------|----------------|----------------|----------|

### Interactions with Other Systems
### 与其他系统的交互

[How does this system interact with combat? Inventory? Progression? UI?
For each interaction, specify the interface: what data flows in, what flows
out, and who is responsible for what.]
[本系统如何与战斗交互？与库存？与进度？与 UI？
对每种交互，指明接口：什么数据流入、什么流出、谁负责什么。]

## Formulas
## 公式

[Every mathematical formula used by this system. For each formula:]
[本系统使用的每个数学公式。对每个公式：]

### [Formula Name]
### [公式名称]

```
result = base_value * (1 + modifier_sum) * scaling_factor
```

| Variable | Type | Range | Source | Description |
|----------|------|-------|--------|-------------|
| base_value | float | 1-100 | data file | The base amount before modifiers |
| modifier_sum | float | -0.9 to 5.0 | calculated | Sum of all active modifiers |
| scaling_factor | float | 0.5-2.0 | data file | Level-based scaling |

| 变量 | 类型 | 范围 | 来源 | 描述 |
|----------|------|-------|--------|-------------|
| base_value | float | 1-100 | 数据文件 | 修饰符之前的基准量 |
| modifier_sum | float | -0.9 至 5.0 | 计算 | 所有生效修饰符之和 |
| scaling_factor | float | 0.5-2.0 | 数据文件 | 基于等级的缩放 |

**Expected output range**: [min] to [max]
**预期输出范围**：[最小] 至 [最大]
**Edge case**: When modifier_sum < -0.9, clamp to -0.9 to prevent negative results.
**边界情况**：当 modifier_sum < -0.9 时，钳制为 -0.9 以防止负值结果。

## Edge Cases
## 边界情况

[Explicitly document what happens in unusual situations. Each edge case
should have a clear resolution.]
[明确记录在异常情况下会发生什么。每个边界情况都应有明确的解决方式。]

| Scenario | Expected Behavior | Rationale |
|----------|------------------|-----------|
| [What if X is zero?] | [This happens] | [Because of this reason] |
| [What if both effects trigger?] | [Priority rule] | [Design reasoning] |

| 场景 | 预期行为 | 原因 |
|----------|------------------|-----------|
| [若 X 为零？] | [发生此事] | [因为此原因] |
| [若两个效果同时触发？] | [优先级规则] | [设计理由] |

## Dependencies
## 依赖

[List every system this mechanic depends on or that depends on this mechanic.]
[列出本机制所依赖的、或依赖于本机制的每个系统。]

| System | Direction | Nature of Dependency |
|--------|-----------|---------------------|
| [Combat] | This depends on Combat | Needs damage calculation results |
| [Inventory] | Inventory depends on this | Provides item effect data |

| 系统 | 方向 | 依赖性质 |
|--------|-----------|---------------------|
| [战斗] | 本系统依赖于战斗 | 需要伤害计算结果 |
| [库存] | 库存依赖于本系统 | 提供物品效果数据 |

## Tuning Knobs
## 调节旋钮

[Every value that should be adjustable for balancing. Include the current
value, the safe range, and what happens at the extremes.]
[每个应可调节以进行平衡的数值。包括当前值、安全范围，以及在极端值时会发生什么。]

| Parameter | Current Value | Safe Range | Effect of Increase | Effect of Decrease |
|-----------|--------------|------------|-------------------|-------------------|

| 参数 | 当前值 | 安全范围 | 增大效果 | 减小效果 |
|-----------|--------------|------------|-------------------|-------------------|

## Visual/Audio Requirements
## 视觉/音频需求

[What visual and audio feedback does this mechanic need?]
[本机制需要什么视觉与音频反馈？]

| Event | Visual Feedback | Audio Feedback | Priority |
|-------|----------------|---------------|----------|

| 事件 | 视觉反馈 | 音频反馈 | 优先级 |
|-------|----------------|---------------|----------|

## Game Feel
## 游戏手感

> **Why this section exists separately from Visual/Audio Requirements**: Visual/Audio
> Requirements document WHAT feedback events occur (tables of events mapped to assets).
> Game Feel documents HOW the mechanic feels to operate — the responsiveness, weight,
> snap, and kinesthetic quality of the interaction. These are design targets for timing,
> frame data, and physical sensation of control. Game feel must be specified at design
> time because it drives animation budgets, input handling architecture, and hitbox
> timing. Retrofitting feel targets after implementation is expensive and often requires
> fundamental rework.
> **为何本节独立于视觉/音频需求存在**：视觉/音频需求记录的是"哪些"反馈事件发生（事件到资产的映射表）。
> 游戏手感记录的是机制操作起来"感觉如何" — 响应度、重量感、干脆度与交互的动觉品质。这些是时序、
> 帧数据与控制物理感受的设计目标。游戏手感必须在设计阶段就指明，因为它驱动动画预算、输入处理架构与
> 受击框时序。在实现后再补加手感目标成本高昂，且往往需要根本性返工。

### Feel Reference
### 手感参考

[Name a specific game, mechanic, or moment that captures the target feel. Be precise —
cite the exact mechanic, not just the game. Explain what quality you are borrowing.
Optionally include an anti-reference (what this should NOT feel like).]
[列举一个能体现目标手感的具体游戏、机制或时刻。要精确 — 引用确切的机制，而不仅是游戏。说明你借鉴的是什么品质。
可选地加入反例参考（这应当"不像"什么）。]

> Example: "Should feel like Dark Souls weapon swings — weighty, committed, and
> telegraphed, but satisfying on contact. NOT floaty like early Halo melee."
> 示例："应像 Dark Souls 的武器挥击 — 沉重、有承诺、有预警，但命中时令人满足。不应像早期 Halo 的近战那样轻飘。"

### Input Responsiveness
### 输入响应度

[Maximum acceptable latency from player input to visible/audible response, per action.]
[每个动作从玩家输入到可见/可听响应的最大可接受延迟。]

| Action | Max Input-to-Response Latency (ms) | Frame Budget (at 60fps) | Notes |
|--------|-----------------------------------|------------------------|-------|
| [Primary action] | [e.g., 50ms] | [e.g., 3 frames] | |
| [Secondary action] | | | |

| 动作 | 最大输入至响应延迟（ms） | 帧预算（60fps 时） | 备注 |
|--------|-----------------------------------|------------------------|-------|
| [主要动作] | [例如 50ms] | [例如 3 帧] | |
| [次要动作] | | | |

### Animation Feel Targets
### 动画手感目标

[Frame data targets for each animation in this mechanic. Startup = windup before the
action has any effect. Active = frames when the action is "happening" (hitbox live,
ability firing, etc.). Recovery = committed/vulnerable frames after the action resolves.]
[本机制中每个动画的帧数据目标。启动 = 动作产生任何效果之前的蓄力。
激活 = 动作"正在发生"的帧（受击框激活、技能释放等）。恢复 = 动作结束后承诺的/易受攻击的帧。]

| Animation | Startup Frames | Active Frames | Recovery Frames | Feel Goal | Notes |
|-----------|---------------|--------------|----------------|-----------|-------|
| [e.g., Light attack] | | | | [e.g., Snappy, low commitment] | |
| [e.g., Heavy attack] | | | | [e.g., Weighty, high commitment] | |

| 动画 | 启动帧 | 激活帧 | 恢复帧 | 手感目标 | 备注 |
|-----------|---------------|--------------|----------------|-----------|-------|
| [例如 轻攻击] | | | | [例如 干脆、低承诺] | |
| [例如 重攻击] | | | | [例如 沉重、高承诺] | |

### Impact Moments
### 冲击时刻

[Defines the punctuation of the mechanic — the moments of peak feedback intensity that
make actions feel consequential. Every high-stakes event should have at least one entry.]
[定义机制的标点 — 使动作产生后果感的反馈强度峰值时刻。每个高风险事件应至少有一条记录。]

| Impact Type | Duration (ms) | Effect Description | Configurable? |
|-------------|--------------|-------------------|---------------|
| Hit-stop (freeze frames) | [e.g., 80ms] | [Freeze both objects on contact] | Yes |
| Screen shake | [e.g., 150ms] | [Directional, decaying] | Yes |
| Camera impact | | | |
| Controller rumble | | | |
| Time-scale slowdown | | | |

| 冲击类型 | 持续时间（ms） | 效果描述 | 可配置？ |
|-------------|--------------|-------------------|---------------|
| 命中停顿（冻结帧） | [例如 80ms] | [接触时冻结双方对象] | 是 |
| 屏幕震动 | [例如 150ms] | [方向性、衰减] | 是 |
| 镜头冲击 | | | |
| 控制器震动 | | | |
| 时间缩放慢放 | | | |

### Weight and Responsiveness Profile
### 重量与响应度画像

[A short prose description of the overall feel target. Answer the following:]
[对整体手感目标的简短文字描述。请回答以下问题：]

- **Weight**: Does this feel heavy and deliberate, or light and reactive?
- **重量**：感觉沉重而刻意，还是轻巧而反应灵敏？
- **Player control**: How much does the player feel in control at every moment?
  (High control = can course-correct mid-action; Low control = committed, momentum-based)
- **玩家控制感**：玩家在每个瞬间感到多大程度的掌控？
  （高控制 = 可在动作中纠偏；低控制 = 承诺式、基于动量）
- **Snap quality**: Does this feel crisp and binary, or smooth and analog?
- **干脆度**：感觉干脆二元化，还是平滑模拟化？
- **Acceleration model**: Does movement/action start instantly (arcade feel) or
  ramp up from zero (simulation feel)? Same question for deceleration.
- **加速度模型**：移动/动作是瞬间启动（街机感）还是从零开始爬升（拟真感）？减速同理。
- **Failure texture**: When the player makes an error, does the mechanic feel fair
  or punishing? What is the read on WHY they failed?
- **失败质感**：当玩家失误时，机制感觉公平还是惩罚？玩家能读出"为何"失败吗？

### Feel Acceptance Criteria
### 手感验收标准

[Specific, testable criteria a playtester can verify without measurement instruments.
These are subjective targets stated precisely enough to get consistent verdicts.]
[可在无测量仪器情况下由试玩者验证的具体、可测试的标准。
这些主观目标被精确表述以获得一致的判定。]

- [ ] [e.g., "Combat feels impactful — playtesters comment on weight unprompted"]
- [ ] [例如 "战斗具有冲击力 — 试玩者会主动评论重量感"]
- [ ] [e.g., "No reviewer uses the words 'floaty', 'slippery', or 'unresponsive'"]
- [ ] [例如 "无评审者使用 '轻飘'、'打滑' 或 '无响应' 等词"]
- [ ] [e.g., "Input latency is imperceptible at target 60fps framerate"]
- [ ] [例如 "在目标 60fps 帧率下输入延迟不可察觉"]
- [ ] [e.g., "Hit-stop reads as satisfying, not as lag or stutter"]
- [ ] [例如 "命中停顿读起来令人满足，而非卡顿或滞后"]

## UI Requirements
## UI 需求

[What information needs to be displayed to the player and when?]
[需要向玩家显示哪些信息、何时显示？]

| Information | Display Location | Update Frequency | Condition |
|-------------|-----------------|-----------------|-----------|

| 信息 | 显示位置 | 更新频率 | 条件 |
|-------------|-----------------|-----------------|-----------|

## Cross-References
## 交叉引用

[Declare every explicit dependency on another GDD's specific mechanic, value, or
rule. This table is machine-checked by `/review-all-gdds` Phase 2c — it replaces
implicit prose references with verifiable declarations. If you reference another
system's behaviour anywhere in this document, it must appear here.]
[声明对另一份 GDD 的具体机制、数值或规则的每一处显式依赖。本表由 `/review-all-gdds` 第 2c 阶段进行机器校验 —
它用可验证的声明取代隐式的文字引用。若本文档任意位置引用了另一系统的行为，必须在此出现。]

| This Document References | Target GDD | Specific Element Referenced | Nature |
|--------------------------|-----------|----------------------------|--------|
| [e.g., "combo multiplier feeds score"] | `design/gdd/score.md` | `combo_multiplier` output value | Data dependency |
| [e.g., "death triggers respawn"] | `design/gdd/respawn.md` | Death state transition | State trigger |
| [e.g., "stamina gates dodge"] | `design/gdd/stamina.md` | Stamina depletion rule | Rule dependency |

| 本文档引用 | 目标 GDD | 引用的具体元素 | 性质 |
|--------------------------|-----------|----------------------------|--------|
| [例如 "连击倍率馈送分数"] | `design/gdd/score.md` | `combo_multiplier` 输出值 | 数据依赖 |
| [例如 "死亡触发重生"] | `design/gdd/respawn.md` | 死亡状态转换 | 状态触发 |
| [例如 "体力门控闪避"] | `design/gdd/stamina.md` | 体力耗尽规则 | 规则依赖 |

> **Note on "Nature"**: use one of — `Data dependency` (we consume their output),
> `State trigger` (their state change triggers our behaviour), `Rule dependency`
> (our rule assumes their rule is also true), `Ownership handoff` (we hand off
> ownership of a value to them).
> **关于"性质"的说明**：使用以下之一 — `数据依赖`（我们消费其输出）、
> `状态触发`（其状态变化触发我们的行为）、`规则依赖`（我们的规则假定其规则也成立）、
> `归属交接`（我们将某值的归属交还给它）。

## Acceptance Criteria
## 验收标准

[Testable criteria that confirm this mechanic is working as designed.]
[可测试的标准，用于确认本机制按设计运作。]

- [ ] [Criterion 1: specific, measurable, testable]
- [ ] [标准 1：具体、可度量、可测试]
- [ ] [Criterion 2]
- [ ] [标准 2]
- [ ] [Criterion 3]
- [ ] [标准 3]
- [ ] Performance: System update completes within [X]ms
- [ ] 性能：系统更新在 [X]ms 内完成
- [ ] No hardcoded values in implementation
- [ ] 实现中无硬编码值

## Open Questions
## 待解决问题

[Anything not yet decided. Each question should have an owner and deadline.]
[任何尚未决定的事项。每个问题都应有负责人和截止日期。]

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|

| 问题 | 负责人 | 截止日期 | 决议 |
|----------|-------|----------|-----------|
