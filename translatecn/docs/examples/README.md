# Collaborative Session Examples
# 协作会话示例

This directory contains realistic, end-to-end session transcripts showing how the Game Studio Agent Architecture works in practice. Each example demonstrates the **collaborative workflow** where agents ask questions, present options, and wait for user approval rather than autonomously generating content.
本目录包含真实、端到端的会话记录，展示游戏工作室智能体架构（Game Studio Agent Architecture）在实际中的工作方式。每个示例都演示了**协作工作流**：智能体提出问题、呈现选项并等待用户批准，而不是自动生成内容。

---

## Visual Reference
## 视觉参考

**New to the system? Start here:**
**刚接触本系统？从这里开始：**
[Skill Flow Diagrams](skill-flow-diagrams.md) — visual maps of all 7 phases and how skills chain together.
[技能流程图](skill-flow-diagrams.md) —— 全部 7 个阶段的可视化地图，以及技能之间如何串联。

---

## 📚 **Available Examples**
## 📚 **可用示例**

### CORE WORKFLOW
### 核心工作流

### [Skill Flow Diagrams](skill-flow-diagrams.md)
### [技能流程图](skill-flow-diagrams.md)
**Type:** Visual Reference
**类型：** 视觉参考
**Complexity:** All levels
**复杂度：** 所有级别

Full pipeline overview (zero to ship), plus detailed chain diagrams for:
design-system, story lifecycle, UX pipeline, and brownfield onboarding.
**Start here if you want to understand how the pieces fit together.**
完整的流水线概览（从零到发布），以及以下内容的详细链路图：
design-system、故事生命周期、UX 流水线和棕地项目接入。
**如果你想了解各部分如何组合在一起，请从这里开始。**

---

### [Session: Authoring a GDD with /design-system](session-design-system-skill.md)
### [会话：使用 /design-system 编写 GDD](session-design-system-skill.md)
**Type:** Design (skill-driven)
**类型：** 设计（技能驱动）
**Skill:** `/design-system`
**技能：** `/design-system`
**Duration:** ~60 minutes (14 turns)
**时长：** 约 60 分钟（14 个回合）
**Complexity:** Medium
**复杂度：** 中等

**Scenario:**
**场景：**
Dev runs `/design-system movement` after `/map-systems` produced the systems index. The skill loads context from the game concept and dependency GDDs, runs a technical feasibility pre-check, then guides through all 8 GDD sections one at a time — drafting, approving, and writing each section to disk before moving to the next.
开发者在 `/map-systems` 生成系统索引后运行 `/design-system movement`。该技能从游戏概念和依赖 GDD 加载上下文，运行技术可行性预检查，然后逐个引导完成全部 8 个 GDD 章节——起草、批准，并将每个章节写入磁盘后再进入下一个章节。

**Key Moments:**
**关键时刻：**
- Technical feasibility pre-check flags Jolt physics default change (Godot 4.6)
- 技术可行性预检查标记 Jolt 物理默认变更（Godot 4.6）
- Incremental writing: each section on disk immediately after approval
- 增量写入：每个章节在批准后立即写入磁盘
- Session crash during section 5 → agent resumes from first empty section
- 在第 5 章期间会话崩溃 → 智能体从第一个空章节恢复
- Dependency signals (stamina, inventory) surfaced during the Dependencies section
- 依赖信号（耐力、库存）在依赖章节中被呈现
- Ends with explicit handoff: "run `/design-review` before the next system"
- 以明确交接结束："在下一个系统之前运行 `/design-review`"

**Learn:**
**学习要点：**
- How `/design-system` is different from asking an agent to "write a GDD"
- `/design-system` 与直接让智能体"编写 GDD"有何不同
- How the section-by-section cycle prevents 30k-token context bloat
- 逐章节循环如何避免 3 万 token 的上下文膨胀
- How incremental file writing survives session crashes
- 增量文件写入如何在会话崩溃时幸存
- How the skill surfaces downstream dependency contracts
- 技能如何呈现下游依赖契约

---

### [Session: Full Story Lifecycle](session-story-lifecycle.md)
### [会话：完整故事生命周期](session-story-lifecycle.md)
**Type:** Full Workflow
**类型：** 完整工作流
**Skills:** `/story-readiness` → implementation → `/story-done`
**技能：** `/story-readiness` → 实现 → `/story-done`
**Duration:** ~50 minutes (13 turns)
**时长：** 约 50 分钟（13 个回合）
**Complexity:** Medium
**复杂度：** 中等

**Scenario:**
**场景：**
Dev picks up a story from the sprint backlog. `/story-readiness` catches a roll-direction ambiguity before any code is written. After implementation, `/story-done` verifies 9 acceptance criteria, identifies 2 deferred criteria (inventory not integrated yet), and closes the story with notes.
开发者从冲刺待办列表中领取一个故事。`/story-readiness` 在编写任何代码之前捕获了一个翻滚方向的歧义。实现完成后，`/story-done` 验证了 9 项验收标准，识别出 2 项延迟标准（库存尚未集成），并附带说明关闭了该故事。

**Key Moments:**
**关键时刻：**
- `/story-readiness` catches spec ambiguity in Turn 2 — resolved before implementation starts
- `/story-readiness` 在第 2 回合捕获规格歧义——在实现开始之前解决
- ADR status check: story would be BLOCKED if ADR was still Proposed
- ADR 状态检查：如果 ADR 仍为 Proposed，故事将被 BLOCKED
- Manifest version check: confirms story's guidance hasn't drifted from current architecture
- 清单版本检查：确认故事的指引未偏离当前架构
- Deferred criteria tracked (not lost) when integration not yet possible
- 当尚无法集成时，延迟标准被跟踪（而非丢失）
- `sprint-status.yaml` updated at story close, next ready story surfaced automatically
- 故事关闭时 `sprint-status.yaml` 被更新，下一个就绪故事自动呈现

**Learn:**
**学习要点：**
- Why `/story-readiness` prevents late-implementation ambiguity
- 为什么 `/story-readiness` 能防止实现后期的歧义
- How deferred criteria work (COMPLETE WITH NOTES vs. BLOCKED)
- 延迟标准如何工作（COMPLETE WITH NOTES 对比 BLOCKED）
- How TR-ID references prevent false deviation flags
- TR-ID 引用如何防止误报的偏离标记
- The full loop from backlog → implemented → closed
- 从待办列表 → 已实现 → 已关闭的完整循环

---

### [Session: Gate Check and Phase Transition](session-gate-check-phase-transition.md)
### [会话：关卡检查与阶段过渡](session-gate-check-phase-transition.md)
**Type:** Phase Gate
**类型：** 阶段关卡
**Skill:** `/gate-check`
**技能：** `/gate-check`
**Duration:** ~20 minutes (7 turns)
**时长：** 约 20 分钟（7 个回合）
**Complexity:** Low
**复杂度：** 低

**Scenario:**
**场景：**
Dev completes the Systems Design phase and runs `/gate-check` to advance. The gate finds all 6 MVP GDDs complete, cross-review passed with one low-severity concern. Gate passes, `stage.txt` updated, and the agent provides a specific ordered checklist for Technical Setup.
开发者完成系统设计阶段并运行 `/gate-check` 推进。关卡发现全部 6 个 MVP GDD 已完成，交叉评审通过且仅有一个低严重性的关注点。关卡通过，`stage.txt` 被更新，智能体为技术设置提供了具体的有序清单。

**Key Moments:**
**关键时刻：**
- Gate validates artifact presence AND internal completeness (8 sections per GDD)
- 关卡验证产物存在性以及内部完整性（每个 GDD 8 个章节）
- CONCERNS ≠ FAIL: low-severity cross-review note passes the gate
- CONCERNS 不等于 FAIL：低严重性的交叉评审注释通过关卡
- stage.txt update changes what `/help`, `/sprint-status`, and all skills see going forward
- stage.txt 更改会改变 `/help`、`/sprint-status` 以及所有技能之后看到的内容
- Agent surfaces the cross-review concern as a concrete ADR to write next
- 智能体将交叉评审关注点呈现为下一步要编写的具体 ADR
- Next phase checklist is specific and ordered, not generic
- 下一阶段的清单具体且有序，而非泛泛

**Learn:**
**学习要点：**
- What a gate check actually validates (not just "do files exist?")
- 关卡检查实际验证什么（不只是"文件是否存在？"）
- How PASS/CONCERNS/FAIL verdicts work
- PASS/CONCERNS/FAIL 裁决如何工作
- Why stage.txt is the authority for phase tracking
- 为什么 stage.txt 是阶段跟踪的权威来源
- What changes after a phase transition
- 阶段过渡后会发生什么变化

---

### [Session: UX Pipeline — /ux-design → /ux-review → /team-ui](session-ux-pipeline.md)
### [会话：UX 流水线 —— /ux-design → /ux-review → /team-ui](session-ux-pipeline.md)
**Type:** UX Design Pipeline
**类型：** UX 设计流水线
**Skills:** `/ux-design`, `/ux-review`, `/team-ui`
**技能：** `/ux-design`、`/ux-review`、`/team-ui`
**Duration:** ~90 minutes (16 turns)
**时长：** 约 90 分钟（16 个回合）
**Complexity:** Medium-High
**复杂度：** 中高

**Scenario:**
**场景：**
Dev designs the HUD and inventory screen. `/ux-design` reads the player journey and GDDs to ground decisions in player emotional state. `/ux-review` catches a blocking accessibility gap (no keyboard alternative to drag-drop) and an advisory colorblind issue. After fixes, `/team-ui` accepts the handoff.
开发者设计 HUD 和库存界面。`/ux-design` 阅读玩家旅程和 GDD，以便将决策建立在玩家情绪状态之上。`/ux-review` 捕获到一个阻塞性的可访问性缺陷（拖放没有键盘替代方案）和一个建议性的色盲问题。修复后，`/team-ui` 接受交接。

**Key Moments:**
**关键时刻：**
- HUD philosophy choice (diegetic vs. persistent vs. tactical) grounded in survival genre conventions
- HUD 理念选择（叙事性 vs. 持续性 vs. 战术性）建立在生存类型惯例之上
- `/ux-review` distinguishes BLOCKING (stops handoff) vs. ADVISORY (can fix in visual pass)
- `/ux-review` 区分 BLOCKING（阻止交接）与 ADVISORY（可在视觉阶段修复）
- Accessibility caught before implementation, not during QA
- 可访问性在实现之前被捕获，而不是在 QA 期间
- Keyboard alternative added in one turn; review re-runs and passes
- 在一个回合内添加键盘替代方案；评审重新运行并通过
- `/team-ui` checks for a passing `/ux-review` before starting visual design
- `/team-ui` 在开始视觉设计之前检查是否有通过的 `/ux-review`

**Learn:**
**学习要点：**
- How `/ux-design` uses player journey context to ground UI decisions
- `/ux-design` 如何使用玩家旅程上下文来奠定 UI 决策
- What `/ux-review` actually checks (not just "does a spec exist?")
- `/ux-review` 实际检查什么（不只是"规格是否存在？"）
- The difference between HUD doc (`design/ux/hud.md`) and per-screen specs
- HUD 文档（`design/ux/hud.md`）与单屏规格之间的区别
- How accessibility issues are handled at design time vs. implementation time
- 可访问性问题在设计阶段与实现阶段如何处理

---

### [Session: Brownfield Onboarding with /adopt](session-adopt-brownfield.md)
### [会话：使用 /adopt 进行棕地接入](session-adopt-brownfield.md)
**Type:** Brownfield Adoption
**类型：** 棕地接入
**Skill:** `/adopt`
**技能：** `/adopt`
**Duration:** ~30 minutes (8 turns)
**时长：** 约 30 分钟（8 个回合）
**Complexity:** Low-Medium
**复杂度：** 低中

**Scenario:**
**场景：**
Dev has 3 months of existing code and rough design notes but nothing in the right format. `/adopt` audits format compliance (not just file existence), classifies 4 gaps by severity, builds an ordered 7-step migration plan, and immediately fixes the BLOCKING gap (missing systems index) by inferring it from the codebase.
开发者拥有 3 个月的现有代码和粗略设计笔记，但没有任何内容符合正确格式。`/adopt` 审计格式合规性（不只是文件存在性），按严重性对 4 个差距进行分类，构建一个有序的 7 步迁移计划，并通过从代码库推断立即修复 BLOCKING 差距（缺失的系统索引）。

**Key Moments:**
**关键时刻：**
- FORMAT audit distinguishes "file exists" from "file has required internal structure"
- FORMAT 审计区分"文件存在"与"文件具有所需的内部结构"
- BLOCKING gap identified: missing systems index prevents 4+ skills from running
- 识别出 BLOCKING 差距：缺失的系统索引阻止了 4 个以上技能运行
- Migration plan is ordered: blocking gaps first, then high, then medium
- 迁移计划有序：先阻塞差距，再高严重性，最后中等
- Systems index bootstrapped from code structure — brownfield code contains the answer
- 系统索引从代码结构引导生成——棕地代码本身就包含答案
- Retrofit mode vs. new authoring: `/design-system retrofit` fills gaps without overwriting
- 改造模式 vs. 全新编写：`/design-system retrofit` 填补差距而不覆盖

**Learn:**
**学习要点：**
- The difference between `/adopt` and `/project-stage-detect`
- `/adopt` 与 `/project-stage-detect` 的区别
- How format compliance is checked (section detection, not just file presence)
- 如何检查格式合规性（章节检测，不只是文件存在）
- How brownfield projects can onboard without losing existing work
- 棕地项目如何在不丢失现有工作的情况下接入
- When to use retrofit mode vs. full authoring
- 何时使用改造模式 vs. 全新编写

---

### FOUNDATIONAL EXAMPLES
### 基础示例

### [Session: Designing the Crafting System](session-design-crafting-system.md)
### [会话：设计制作系统](session-design-crafting-system.md)
**Type:** Design
**类型：** 设计
**Agent:** game-designer
**智能体：** game-designer
**Duration:** ~45 minutes (12 turns)
**时长：** 约 45 分钟（12 个回合）
**Complexity:** Medium
**复杂度：** 中等

**Scenario:**
**场景：**
Solo dev needs to design a crafting system that serves Pillar 2 ("Emergent Discovery Through Experimentation"). The agent guides them through question/answer, presents 3 design options with game theory analysis, incorporates user modifications, and iteratively drafts the GDD with approval at each step.
独立开发者需要设计一个服务于支柱 2（"通过实验的涌现式发现"）的制作系统。智能体通过问答引导他们，呈现 3 个带有游戏理论分析的设计选项，整合用户修改，并在每一步批准后迭代地起草 GDD。

**Key Collaborative Moments:**
**关键协作时刻：**
- Agent asks 5 clarifying questions upfront
- 智能体预先提出 5 个澄清问题
- Presents 3 distinct options with pros/cons + MDA alignment
- 呈现 3 个不同选项，附带优缺点 + MDA 对齐
- User modifies recommended option, agent incorporates immediately
- 用户修改推荐选项，智能体立即整合
- Edge case flagged proactively ("what if non-recipe combo?")
- 主动标记边界情况（"非配方组合怎么办？"）
- Each GDD section shown for approval before moving to next
- 每个 GDD 章节在进入下一个之前展示以供批准
- Explicit "May I write to [file]?" before creating file
- 在创建文件之前明确询问"我可以写入 [file] 吗？"

**Learn:**
**学习要点：**
- How design agents ask about goals, constraints, references
- 设计智能体如何询问目标、约束、参考
- How to present options using game design theory (MDA, SDT, Bartle)
- 如何使用游戏设计理论（MDA、SDT、Bartle）呈现选项
- How to iterate on drafts section-by-section
- 如何逐章节迭代草稿
- When to delegate to specialists (systems-designer, economy-designer)
- 何时委托给专家（systems-designer、economy-designer）

---

### [Session: Implementing Combat Damage Calculation](session-implement-combat-damage.md)
### [会话：实现战斗伤害计算](session-implement-combat-damage.md)
**Type:** Implementation
**类型：** 实现
**Agent:** gameplay-programmer
**智能体：** gameplay-programmer
**Duration:** ~30 minutes (10 turns)
**时长：** 约 30 分钟（10 个回合）
**Complexity:** Low-Medium
**复杂度：** 低中

**Scenario:**
**场景：**
User has a complete design doc and wants the damage calculation implemented. Agent reads the spec, identifies 7 ambiguities/gaps, asks clarifying questions, proposes architecture for approval, implements with rule enforcement, and proactively writes tests.
用户拥有完整的设计文档，并希望实现伤害计算。智能体阅读规格，识别出 7 个歧义/差距，提出澄清问题，提议架构以供批准，通过规则强制执行进行实现，并主动编写测试。

**Key Collaborative Moments:**
**关键协作时刻：**
- Agent reads design doc first, identifies 7 spec ambiguities
- 智能体先阅读设计文档，识别出 7 个规格歧义
- Architecture proposed with code samples BEFORE implementation
- 在实现之前提出带有代码示例的架构
- User requests type safety, agent refines and re-proposes
- 用户请求类型安全，智能体优化并重新提议
- Rules catch issues (hardcoded values), agent fixes transparently
- 规则捕获问题（硬编码值），智能体透明地修复
- Tests written proactively following verification-driven development
- 遵循验证驱动开发主动编写测试
- Agent offers options for next steps rather than assuming
- 智能体提供下一步选项而非假设

**Learn:**
**学习要点：**
- How implementation agents clarify specs before coding
- 实现智能体如何在编码之前澄清规格
- How to propose architecture with code samples for approval
- 如何提出带有代码示例的架构以供批准
- How rules enforce standards automatically
- 规则如何自动强制执行标准
- How to handle spec gaps (ask, don't assume)
- 如何处理规格差距（询问，不要假设）
- Verification-driven development (tests prove it works)
- 验证驱动开发（测试证明其有效）

---

### [Session: Scope Crisis - Strategic Decision Making](session-scope-crisis-decision.md)
### [会话：范围危机 —— 战略决策](session-scope-crisis-decision.md)
**Type:** Strategic Decision
**类型：** 战略决策
**Agent:** creative-director
**智能体：** creative-director
**Duration:** ~25 minutes (8 turns)
**时长：** 约 25 分钟（8 个回合）
**Complexity:** High
**复杂度：** 高

**Scenario:**
**场景：**
Solo dev faces crisis: Alpha milestone in 2 weeks, crafting system needs 3 weeks, investor demo is make-or-break. Creative director gathers context, frames the decision, presents 3 strategic options with honest trade-off analysis, makes recommendation but defers to user, then documents decision with ADR and demo script.
独立开发者面临危机：Alpha 里程碑还有 2 周，制作系统需要 3 周，投资人演示是成败关键。创意总监收集上下文，框定决策，呈现 3 个带有诚实权衡分析的战略选项，给出推荐但交由用户决定，然后通过 ADR 和演示脚本记录决策。

**Key Collaborative Moments:**
**关键协作时刻：**
- Agent reads context docs before proposing solutions
- 智能体在提出解决方案之前阅读上下文文档
- Asks 5 questions to understand decision constraints
- 提出 5 个问题以理解决策约束
- Frames decision properly (what's at stake, evaluation criteria)
- 正确框定决策（什么处于利害关系、评估标准）
- Presents 3 options with risk analysis and historical precedent
- 呈现 3 个带有风险分析和历史先例的选项
- Makes strong recommendation but explicitly: "this is your call"
- 给出强烈推荐但明确表示："这是你的决定"
- Documents decision + provides demo script to support user
- 记录决策 + 提供演示脚本以支持用户

**Learn:**
**学习要点：**
- How leadership agents frame strategic decisions
- 领导层智能体如何框定战略决策
- How to present options with trade-off analysis
- 如何呈现带有权衡分析的选项
- How to use game dev precedent and theory in recommendations
- 如何在推荐中使用游戏开发先例和理论
- How to document decisions (ADRs)
- 如何记录决策（ADR）
- How to cascade decisions to affected departments
- 如何将决策级联到受影响的部门

---

### [Reverse Documentation Workflow](reverse-document-workflow-example.md)
### [反向文档工作流](reverse-document-workflow-example.md)
**Type:** Brownfield Documentation
**类型：** 棕地文档
**Agent:** game-designer
**智能体：** game-designer
**Duration:** ~20 minutes
**时长：** 约 20 分钟
**Complexity:** Low
**复杂度：** 低

**Scenario:**
**场景：**
Developer built a skill tree system but never wrote a design doc. Agent reads the code, infers the design intent, asks clarifying questions about ambiguous decisions, and produces a retroactive GDD.
开发者构建了一个技能树系统但从未编写设计文档。智能体阅读代码，推断设计意图，针对含糊决策提出澄清问题，并生成一份追溯性 GDD。

---

## 🎯 **What These Examples Demonstrate**
## 🎯 **这些示例展示了什么**

All examples follow the **collaborative workflow pattern:**
所有示例都遵循**协作工作流模式：**

```
Question → Options → Decision → Draft → Approval
```

> **Note:** These examples show the collaborative pattern as conversational text.
> In practice, agents now use the `AskUserQuestion` tool at decision points to
> present structured option pickers (with labels, descriptions, and multi-select).
> The pattern is **Explain → Capture**: agents explain their analysis in
> conversation first, then present a structured UI picker for the user's decision.

> **注意：** 这些示例以对话文本形式展示协作模式。
> 在实践中，智能体现在在决策点使用 `AskUserQuestion` 工具来
> 呈现结构化的选项选择器（带有标签、描述和多选）。
> 模式是**解释 → 捕获**：智能体先在对话中解释其分析，
> 然后呈现结构化 UI 选择器供用户决策。

### ✅ **Collaborative Behaviors Shown:**
### ✅ **展示的协作行为：**

1. **Agents Ask Before Assuming**
1. **智能体先询问再假设**
   - Design agents ask about goals, constraints, references
   - 设计智能体询问目标、约束、参考
   - Implementation agents clarify spec ambiguities
   - 实现智能体澄清规格歧义
   - Leadership agents gather full context before recommending
   - 领导层智能体在推荐之前收集完整上下文

2. **Agents Present Options, Not Dictates**
2. **智能体呈现选项，而非命令**
   - 2-4 options with pros/cons
   - 2-4 个带有优缺点的选项
   - Reasoning based on theory, precedent, project pillars
   - 推理基于理论、先例、项目支柱
   - Recommendation made, but user decides
   - 给出推荐，但由用户决定

3. **Agents Show Work Before Finalizing**
3. **智能体在定稿前展示工作**
   - Design drafts shown section-by-section
   - 设计草稿逐章节展示
   - Architecture proposals shown before implementation
   - 架构提议在实现之前展示
   - Strategic analysis presented before decisions
   - 战略分析在决策之前呈现

4. **Agents Get Approval Before Writing Files**
4. **智能体在写入文件前获取批准**
   - Explicit "May I write to [file]?" before using Write/Edit tools
   - 在使用 Write/Edit 工具之前明确询问"我可以写入 [file] 吗？"
   - Multi-file changes list all affected files first
   - 多文件更改首先列出所有受影响文件
   - User says "Yes" before any file is created
   - 在创建任何文件之前用户说"是"

5. **Agents Iterate on Feedback**
5. **智能体根据反馈迭代**
   - User modifications incorporated immediately
   - 用户修改立即整合
   - No defensiveness when user changes recommendations
   - 用户更改推荐时没有防御性
   - Celebrate when user improves agent's suggestion
   - 当用户改进智能体的建议时给予庆祝

---

## 📖 **How to Use These Examples**
## 📖 **如何使用这些示例**

### For New Users:
### 新用户：
Read these examples BEFORE your first session. They show realistic expectations for how agents work:
在第一次会话之前阅读这些示例。它们展示了智能体工作的真实期望：
- Agents are consultants, not autonomous executors
- 智能体是顾问，而非自主执行者
- You make all creative/strategic decisions
- 你做出所有创意/战略决策
- Agents provide expert guidance and options
- 智能体提供专家指导和选项

### For Understanding Specific Workflows:
### 理解特定工作流：
- **New to the system?** → Read skill-flow-diagrams.md first
- **刚接触本系统？** → 先阅读 skill-flow-diagrams.md
- **Running /design-system for the first time?** → Read session-design-system-skill.md
- **第一次运行 /design-system？** → 阅读 session-design-system-skill.md
- **Picking up a story?** → Read session-story-lifecycle.md
- **领取一个故事？** → 阅读 session-story-lifecycle.md
- **Finishing a phase?** → Read session-gate-check-phase-transition.md
- **完成一个阶段？** → 阅读 session-gate-check-phase-transition.md
- **Starting UI work?** → Read session-ux-pipeline.md
- **开始 UI 工作？** → 阅读 session-ux-pipeline.md
- **Have an existing project?** → Read session-adopt-brownfield.md
- **有现有项目？** → 阅读 session-adopt-brownfield.md
- **Designing a system (agent-driven)?** → Read session-design-crafting-system.md
- **设计一个系统（智能体驱动）？** → 阅读 session-design-crafting-system.md
- **Implementing code?** → Read session-implement-combat-damage.md
- **实现代码？** → 阅读 session-implement-combat-damage.md
- **Making strategic decisions?** → Read session-scope-crisis-decision.md
- **做出战略决策？** → 阅读 session-scope-crisis-decision.md

### For Training:
### 用于培训：
If you're teaching someone to use this system, walk through one example turn-by-turn to show:
如果你在教别人使用这个系统，逐回合地走查一个示例以展示：
- What good questions look like
- 好的问题是什么样的
- How to evaluate presented options
- 如何评估呈现的选项
- When to approve vs. request changes
- 何时批准 vs. 请求更改
- How to maintain creative control while leveraging AI expertise
- 如何在利用 AI 专业知识的同时保持创意控制

---

## 🔍 **Common Patterns Across All Examples**
## 🔍 **所有示例的共同模式**

### Turn 1-2: **Understand Before Acting**
### 回合 1-2：**行动之前先理解**
- Agent reads context (design docs, specs, constraints)
- 智能体阅读上下文（设计文档、规格、约束）
- Agent asks clarifying questions
- 智能体提出澄清问题
- No assumptions or guesses
- 没有假设或猜测

### Turn 3-5: **Present Options with Reasoning**
### 回合 3-5：**带有推理地呈现选项**
- 2-4 distinct approaches
- 2-4 种不同方法
- Pros/cons for each
- 每种的优缺点
- Theory/precedent supporting the analysis
- 支持分析的理论/先例
- Recommendation made, decision deferred to user
- 给出推荐，决策交由用户

### Turn 6-8: **Iterate on Drafts**
### 回合 6-8：**迭代草稿**
- Show work incrementally
- 增量地展示工作
- Incorporate feedback immediately
- 立即整合反馈
- Flag edge cases or ambiguities proactively
- 主动标记边界情况或歧义

### Turn 9-10: **Approval and Completion**
### 回合 9-10：**批准和完成**
- "May I write to [file]?"
- "我可以写入 [file] 吗？"
- User: "Yes"
- 用户："是"
- Agent writes files
- 智能体写入文件
- Agent offers next steps (tests, review, integration)
- 智能体提供下一步（测试、评审、集成）

---

## 🚀 **Try It Yourself**
## 🚀 **自己尝试**

After reading these examples, try this exercise:
阅读这些示例之后，尝试以下练习：

1. Pick one of your game systems (combat, inventory, progression, etc.)
1. 选择你的一个游戏系统（战斗、库存、进阶等）
2. Ask the relevant agent to design or implement it
2. 请求相关智能体设计或实现它
3. Notice if the agent:
3. 注意智能体是否：
   - ✅ Asks clarifying questions upfront
   - ✅ 预先提出澄清问题
   - ✅ Presents options with reasoning
   - ✅ 带有推理地呈现选项
   - ✅ Shows drafts before finalizing
   - ✅ 在定稿前展示草稿
   - ✅ Requests approval before writing files
   - ✅ 在写入文件之前请求批准

If the agent skips any of these, remind it:
如果智能体跳过了其中任何一项，提醒它：
> "Please follow the collaborative protocol from docs/COLLABORATIVE-DESIGN-PRINCIPLE.md"

---

## 📝 **Additional Resources**
## 📝 **附加资源**

- **Full Principle Documentation:** [docs/COLLABORATIVE-DESIGN-PRINCIPLE.md](../COLLABORATIVE-DESIGN-PRINCIPLE.md)
- **完整原则文档：** [docs/COLLABORATIVE-DESIGN-PRINCIPLE.md](../COLLABORATIVE-DESIGN-PRINCIPLE.md)
- **Workflow Guide:** [docs/WORKFLOW-GUIDE.md](../WORKFLOW-GUIDE.md)
- **工作流指南：** [docs/WORKFLOW-GUIDE.md](../WORKFLOW-GUIDE.md)
- **Agent Roster:** [.claude/docs/agent-roster.md](../../.claude/docs/agent-roster.md)
- **智能体名册：** [.claude/docs/agent-roster.md](../../.claude/docs/agent-roster.md)
- **CLAUDE.md (Collaboration Protocol):** [CLAUDE.md](../../CLAUDE.md#collaboration-protocol)
- **CLAUDE.md（协作协议）：** [CLAUDE.md](../../CLAUDE.md#collaboration-protocol)
