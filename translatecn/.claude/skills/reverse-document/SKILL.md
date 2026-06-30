---
name: reverse-document
description: "Generate design or architecture documents from existing implementation. Works backwards from code/prototypes to create missing planning docs."
argument-hint: "<type> <path> (e.g., 'design src/gameplay/combat' or 'architecture src/core')"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
model: sonnet
# Read-only diagnostic skill — no specialist agent delegation needed
---
---
名称：reverse-document
描述："从现有实现生成设计或架构文档。从代码/原型逆向工作以创建缺失的规划文档。"
argument-hint："<类型> <路径>（例如，'design src/gameplay/combat' 或 'architecture src/core'）"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash
model：sonnet
# Read-only diagnostic skill — no specialist agent delegation needed
---

# Reverse Documentation
# 逆向文档

This skill analyzes existing implementation (code, prototypes, systems) and generates
appropriate design or architecture documentation. Use this when:
此技能分析现有实现（代码、原型、系统）并生成
适当的设计或架构文档。在以下情况使用：

- You built a feature without writing a design doc first
- You inherited a codebase without documentation
- You prototyped a mechanic and need to formalize it
- You need to document "why" behind existing code
- 你构建了功能但先写了设计文档
- 你继承了没有文档的代码库
- 你制作了机制原型并需要将其正式化
- 你需要记录现有代码背后的"为什么"

---

## Workflow
## 工作流

## Phase 1: Parse Arguments
## 阶段 1：解析参数

**Format**: `/reverse-document <type> <path>`
**格式**：`/reverse-document <类型> <路径>`

**Type options**:
- `design` → Generate a game design document (GDD section)
- `architecture` → Generate an Architecture Decision Record (ADR)
- `concept` → Generate a concept document from prototype
**类型选项**：
- `design` → 生成游戏设计文档（GDD 部分）
- `architecture` → 生成架构决策记录（ADR）
- `concept` → 从原型生成概念文档

**Path**: Directory or file to analyze
- `src/gameplay/combat/` → All combat-related code
- `src/core/event-system.cpp` → Specific file
- `prototypes/stealth-mech/` → Prototype directory
**路径**：要分析的目录或文件
- `src/gameplay/combat/` → 所有战斗相关代码
- `src/core/event-system.cpp` → 特定文件
- `prototypes/stealth-mech/` → 原型目录

**Examples**:
**示例**：

```bash
/reverse-document design src/gameplay/magic-system
/reverse-document architecture src/core/entity-component
/reverse-document concept prototypes/vehicle-combat
```

## Phase 2: Analyze Implementation
## 阶段 2：分析实现

**Read and understand the code/prototype**:
**阅读并理解代码/原型**：

**For design docs (GDD):**
- Identify mechanics, rules, formulas
- Extract gameplay values (damage, cooldowns, ranges)
- Find state machines, ability systems, progression
- Detect edge cases handled in code
- Map dependencies (what systems interact?)
**对于设计文档（GDD）：**
- 识别机制、规则、公式
- 提取游戏玩法数值（伤害、冷却、范围）
- 查找状态机、能力系统、进度
- 检测代码中处理的边界情况
- 映射依赖关系（哪些系统交互？）

**For architecture docs (ADR):**
- Identify patterns (ECS, singleton, observer, etc.)
- Understand technical decisions (threading, serialization, etc.)
- Map dependencies and coupling
- Assess performance characteristics
- Find constraints and trade-offs
**对于架构文档（ADR）：**
- 识别模式（ECS、单例、观察者等）
- 理解技术决策（线程、序列化等）
- 映射依赖和耦合
- 评估性能特征
- 查找约束和权衡

**For concept docs (prototype analysis):**
- Identify core mechanic
- Extract emergent gameplay patterns
- Note what worked vs what didn't
- Find technical feasibility insights
- Document player fantasy / feel
**对于概念文档（原型分析）：**
- 识别核心机制
- 提取涌现的游戏玩法模式
- 记录什么有效 vs 什么无效
- 查找技术可行性洞察
- 记录玩家幻想 / 手感

## Phase 3: Ask Clarifying Questions
## 阶段 3：提出澄清问题

**DO NOT** just describe the code. **ASK** about intent:
**不要**仅描述代码。**询问**意图：

**Design questions**:
- "I see a [resource] system that depletes during [activity]. Was this for:
  - Pacing (prevent spam)?
  - Resource management (strategic depth)?
  - Or something else?"
- "The [mechanic] seems central. Is this a core pillar, or supporting feature?"
- "[Value] scales exponentially with [factor]. Intentional design, or needs rebalancing?"
**设计问题**：
- "我看到一个在 [活动] 期间消耗的 [资源] 系统。这是为了：
  - 节奏（防止滥用）？
  - 资源管理（战略深度）？
  - 还是其他？"
- "[机制] 似乎是核心。这是核心支柱还是支撑功能？"
- "[数值] 随 [因素] 呈指数增长。有意设计还是需要重新平衡？"

**Architecture questions**:
- "You're using a service locator pattern. Was this chosen for:
  - Testability (mock dependencies)?
  - Decoupling (reduce hard references)?
  - Or inherited from existing code?"
- "I see manual memory management instead of smart pointers. Performance requirement, or legacy?"
**架构问题**：
- "你在使用服务定位器模式。这是为了：
  - 可测试性（模拟依赖）？
  - 解耦（减少硬引用）？
  - 还是从现有代码继承？"
- "我看到手动内存管理而非智能指针。性能要求还是遗留？"

**Concept questions**:
- "The prototype emphasizes stealth over combat. Is that the intended pillar?"
- "Players seem to exploit the grappling hook for speed. Feature or bug?"
**概念问题**：
- "原型强调潜行而非战斗。这是预期的支柱吗？"
- "玩家似乎利用抓钩加速。功能还是 bug？"

## Phase 4: Present Findings
## 阶段 4：呈现发现

Before drafting, show what you discovered:
起草之前，展示你的发现：

```
I've analyzed [path]/. Here's what I found:

MECHANICS IMPLEMENTED:
- [mechanic-a] with [property] (e.g. timing windows, cooldowns)
- [mechanic-b] (e.g. interaction between two states)
- [resource] system (depletes on [action], regens on [condition])
- [state] system (builds up, triggers [effect])

FORMULAS DISCOVERED:
- [Output] = [formula using discovered variables]
- [Secondary output] = [formula]

UNCLEAR INTENT AREAS:
1. [Resource] system — pacing or resource management?
2. [Mechanic] — core pillar or supporting feature?
3. [Value] scaling — intentional design or needs tuning?

Before I draft the design doc, could you clarify these points?
```

```
I've analyzed [path]/. Here's what I found:

MECHANICS IMPLEMENTED:
- [mechanic-a] with [property] (e.g. timing windows, cooldowns)
- [mechanic-b] (e.g. interaction between two states)
- [resource] system (depletes on [action], regens on [condition])
- [state] system (builds up, triggers [effect])

FORMULAS DISCOVERED:
- [Output] = [formula using discovered variables]
- [Secondary output] = [formula]

UNCLEAR INTENT AREAS:
1. [Resource] system — pacing or resource management?
2. [Mechanic] — core pillar or supporting feature?
3. [Value] scaling — intentional design or needs tuning?

Before I draft the design doc, could you clarify these points?
```

Wait for user to clarify intent before drafting.
等待用户澄清意图后再起草。

## Phase 5: Draft Document Using Template
## 阶段 5：使用模板起草文档

Based on type, use appropriate template:
根据类型使用适当模板：

| Type | Template | Output Path |
|------|----------|-------------|
| `design` | `templates/design-doc-from-implementation.md` | `design/gdd/[system-name].md` |
| `architecture` | `templates/architecture-doc-from-code.md` | `docs/architecture/[decision-name].md` |
| `concept` | `templates/concept-doc-from-prototype.md` | `prototypes/[name]/CONCEPT.md` or `design/concepts/[name].md` |
| 类型 | 模板 | 输出路径 |
|------|----------|-------------|
| `design` | `templates/design-doc-from-implementation.md` | `design/gdd/[system-name].md` |
| `architecture` | `templates/architecture-doc-from-code.md` | `docs/architecture/[decision-name].md` |
| `concept` | `templates/concept-doc-from-prototype.md` | `prototypes/[name]/CONCEPT.md` or `design/concepts/[name].md` |

**Draft structure**:
- Capture **what exists** (mechanics, patterns, implementation)
- Document **why it exists** (intent clarified with user)
- Identify **what's missing** (edge cases not handled, gaps in design)
- Flag **follow-up work** (balance tuning, missing features)
**草稿结构**：
- 捕获**存在什么**（机制、模式、实现）
- 记录**为什么存在**（与用户澄清的意图）
- 识别**缺失什么**（未处理的边界情况、设计缺口）
- 标记**后续工作**（平衡调整、缺失功能）

## Phase 6: Show Draft and Request Approval
## 阶段 6：展示草稿并请求批准

**Collaborative protocol**:
**协作协议**：

```
I've drafted the [system-name] design doc based on your code and clarifications.

[Show key sections: Overview, Mechanics, Formulas, Design Intent]

ADDITIONS I MADE:
- Documented [mechanic] as "[intent]" per your clarification
- Added edge cases not in code (e.g., what if [resource] hits 0 mid-[action]?)
- Flagged balance concern: [scaling type] scaling at [boundary condition]

SECTIONS MARKED AS INCOMPLETE:
- "[System] interaction with [other-system]" (not fully implemented yet)
- "[Variant or feature]" (only [subset] implemented so far)

May I write this to design/gdd/[system-name].md?
```

Wait for approval. User may request changes before writing.
等待批准。用户可能在写入前请求修改。

## Phase 7: Write Document with Metadata
## 阶段 7：写入带元数据的文档

When approved, write the file with special markers:
批准后，写入带特殊标记的文件：

```markdown
---
status: reverse-documented
source: [path/]
date: [today]
verified-by: [User name]
---

# [System Name] Design

> **Note**: This document was reverse-engineered from the existing implementation.
> It captures current behavior and clarified design intent. Some sections may be
> incomplete where implementation is partial or intent was unclear.

[Rest of document...]
```

## Phase 8: Flag Follow-Up Work
## 阶段 8：标记后续工作

After writing, suggest next steps:
写入后，建议后续步骤：

```
✅ Written to design/gdd/combat-system.md

FOLLOW-UP RECOMMENDED:
1. Run /balance-check on combat formulas (exponential scaling concern)
2. Create ADR for stamina system architecture decision
3. Implement missing edge cases:
   - Stamina depletion mid-combo behavior
   - Stagger state during dodge
4. Extend design doc when weapon variety is implemented

Would you like me to tackle any of these now?
```

---

## Template Selection Logic
## 模板选择逻辑

| If analyzing... | Use template... | Because... |
|----------------|-----------------|------------|
| `src/gameplay/*` | design-doc-from-implementation.md | Gameplay mechanics → GDD |
| `src/core/*`, `src/ai/*` | architecture-doc-from-code.md | Core systems → ADR |
| `prototypes/*` | concept-doc-from-prototype.md | Experiments → concept doc |
| `src/networking/*` | architecture-doc-from-code.md | Technical systems → ADR |
| `src/ui/*` | design-doc-from-implementation.md | UI/UX → design spec |
| 如果分析... | 使用模板... | 因为... |
|----------------|-----------------|------------|
| `src/gameplay/*` | design-doc-from-implementation.md | 游戏玩法机制 → GDD |
| `src/core/*`、`src/ai/*` | architecture-doc-from-code.md | 核心系统 → ADR |
| `prototypes/*` | concept-doc-from-prototype.md | 实验 → 概念文档 |
| `src/networking/*` | architecture-doc-from-code.md | 技术系统 → ADR |
| `src/ui/*` | design-doc-from-implementation.md | UI/UX → 设计规范 |

---

## Example Session: Reverse-Document a System
## 示例会话：逆向文档化一个系统

```
User: /reverse-document design src/gameplay/[system]

Agent: I'll analyze your [system] implementation to create a design doc.
       [Reads code, discovers mechanics, formulas]

Agent: I've analyzed src/gameplay/[system]/. Here's what I found:
       [Shows mechanics, formulas, unclear areas]

       Before drafting, could you clarify:
       1. [Resource] system intent?
       2. Is [mechanic] a core pillar?
       3. [Value] scaling — intentional or needs tuning?

User: 1. [Resource] is for pacing, prevent [unwanted behavior]
      2. [Mechanic] is core — I want [design intent]
      3. Yeah, exponential is too much, should be linear

Agent: Perfect, that clarifies the vision. Let me draft the design doc.
       [Shows draft with clarified intent]

       May I write this to design/gdd/[system-name].md?

User: Yes, write it.

Agent: ✅ Written to design/gdd/[system-name].md
       ✅ Marked as [REVERSE-DOCUMENTED]
       ✅ Flagged [value] scaling for rebalancing

       Next steps:
       - Update [formula] to [corrected scaling]
       - Run /balance-check to validate [curve]
       - Document [mechanic] as core pillar in game-pillars.md
```

---

## Collaborative Protocol
## 协作协议

This skill follows the collaborative design principle:
此技能遵循协作设计原则：

1. **Analyze First**: Read code, understand implementation
2. **Question Intent**: Ask about "why", not just "what"
3. **Present Findings**: Show discoveries, highlight unclear areas
4. **User Clarifies**: Separate intent from accidents
5. **Draft Document**: Create doc based on reality + intent
6. **Show Draft**: Display key sections, explain additions
7. **Get Approval**: "May I write to [filepath]?" On approval: Verdict: **COMPLETE** — document generated. On decline: Verdict: **BLOCKED** — user declined write.
8. **Flag Follow-Up**: Suggest related work, don't auto-execute
1. **先分析**：阅读代码，理解实现
2. **询问意图**：询问"为什么"，而非仅"是什么"
3. **呈现发现**：展示发现，突出不清楚的区域
4. **用户澄清**：区分意图和偶然
5. **起草文档**：基于现实 + 意图创建文档
6. **展示草稿**：显示关键部分，解释新增内容
7. **获取批准**："我可以写入 [filepath] 吗？" 批准：裁决：**COMPLETE** — 文档已生成。拒绝：裁决：**BLOCKED** — 用户拒绝写入。
8. **标记后续**：建议相关工作，不自动执行

**Never assume intent. Always ask before documenting "why".**
**永远不要假设意图。在记录"为什么"之前始终询问。**
