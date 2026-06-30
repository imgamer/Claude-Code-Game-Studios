---
name: architecture-decision
description: "Creates an Architecture Decision Record (ADR) documenting a significant technical decision, its context, alternatives considered, and consequences. Every major technical choice should have an ADR."
argument-hint: "[title] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---
---
名称：architecture-decision
描述："创建一份架构决策记录（ADR），记录重要的技术决策、其背景、所考虑的替代方案以及后果。每个主要技术选择都应有对应的 ADR。"
argument-hint："[title] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model：sonnet
---

When this skill is invoked:
当此技能被调用时：

## 0. Parse Arguments — Detect Retrofit Mode
## 0. 解析参数 —— 检测改造模式

Resolve the review mode (once, store for all gate spawns this run):
解析评审模式（解析一次，本次运行中所有门禁派生都使用该模式）：
1. If `--review [full|lean|solo]` was passed → use that
1. 如果传入了 `--review [full|lean|solo]` → 使用该值
2. Else read `production/review-mode.txt` → use that value
2. 否则读取 `production/review-mode.txt` → 使用该值
3. Else → default to `lean`
3. 否则 → 默认为 `lean`

See `.claude/docs/director-gates.md` for the full check pattern.
完整的检查模式请参见 `.claude/docs/director-gates.md`。

**If the argument starts with `retrofit` followed by a file path**
(e.g., `/architecture-decision retrofit docs/architecture/adr-0001-event-system.md`):
**如果参数以 `retrofit` 开头，后跟文件路径**
（例如：`/architecture-decision retrofit docs/architecture/adr-0001-event-system.md`）：

Enter **retrofit mode**:
进入**改造模式**：

1. Read the existing ADR file completely.
1. 完整读取现有 ADR 文件。
2. Identify which template sections are present by scanning headings:
2. 通过扫描标题识别已存在的模板章节：
   - `## Status` — **BLOCKING if missing**: `/story-readiness` cannot check ADR acceptance
   - `## Status` —— **若缺失则阻断**：`/story-readiness` 无法检查 ADR 验收
   - `## ADR Dependencies` — HIGH if missing: dependency ordering breaks
   - `## ADR Dependencies` —— 若缺失为 HIGH：依赖排序将失效
   - `## Engine Compatibility` — HIGH if missing: post-cutoff risk unknown
   - `## Engine Compatibility` —— 若缺失为 HIGH：截止后风险未知
   - `## GDD Requirements Addressed` — MEDIUM if missing: traceability lost
   - `## GDD Requirements Addressed` —— 若缺失为 MEDIUM：可追溯性丢失
3. Present to the user:
3. 向用户呈现：
   ```
   ## Retrofit: [ADR title]
   File: [path]

   Sections already present (will not be touched):
   ✓ Status: [current value, or "MISSING — will add"]
   ✓ [section]

   Missing sections to add:
   ✗ Status — BLOCKING (stories cannot validate ADR acceptance without this)
   ✗ ADR Dependencies — HIGH
   ✗ Engine Compatibility — HIGH
   ```
4. Ask: "Shall I add the [N] missing sections? I will not modify any existing content."
4. 询问："是否添加这 [N] 个缺失章节？我不会修改任何现有内容。"
5. If yes:
5. 若是：
   - For **Status**: ask the user — "What is the current status of this decision?"
   - 对于 **Status**：询问用户 —— "此决策的当前状态是什么？"
     Options: "Proposed", "Accepted", "Deprecated", "Superseded by ADR-XXXX"
     选项："Proposed"、"Accepted"、"Deprecated"、"Superseded by ADR-XXXX"
   - For **ADR Dependencies**: ask — "Does this decision depend on any other ADR?
   - 对于 **ADR Dependencies**：询问 —— "此决策是否依赖任何其他 ADR？
     Does it enable or block any other ADR or epic?" Accept "None" for each field.
     是否启用或阻塞任何其他 ADR 或史诗？" 每个字段可接受 "None"。
   - For **Engine Compatibility**: read the engine reference docs (same as Step 1 below)
   - 对于 **Engine Compatibility**：读取引擎参考文档（与下方第 1 步相同）
     and ask the user to confirm the domain. Then generate the table with verified data.
     并请用户确认领域。然后使用经过验证的数据生成表格。
   - For **GDD Requirements Addressed**: ask — "Which GDD systems motivated this decision?
   - 对于 **GDD Requirements Addressed**：询问 —— "哪些 GDD 系统促成了此决策？
     What specific requirement in each GDD does this ADR address?"
     每个 GDD 中此 ADR 具体解决哪些需求？"
   - Append each missing section to the ADR file using the Edit tool.
   - 使用 Edit 工具将每个缺失章节追加到 ADR 文件中。
   - **Never modify any existing section.** Only append or fill absent sections.
   - **绝不修改任何现有章节。** 仅追加或填充缺失章节。
6. After adding all missing sections, update the ADR's `## Date` field if it is absent.
6. 添加所有缺失章节后，若 ADR 的 `## Date` 字段缺失则更新它。
7. Suggest: "Run `/architecture-review` to re-validate coverage now that this ADR
   has its Status and Dependencies fields."
7. 建议："现在此 ADR 已具备 Status 和 Dependencies 字段，运行 `/architecture-review` 重新验证覆盖范围。"

If NOT in retrofit mode, proceed to Step 1 below (normal ADR authoring).
若不在改造模式，则进入下方第 1 步（正常 ADR 编写）。

**No-argument guard**: If no argument was provided (title is empty), ask before
running Phase 0:
**无参数保护**：若未提供参数（标题为空），在运行阶段 0 前询问：

> "What technical decision are you documenting? Please provide a short title
> (e.g., `event-system-architecture`, `physics-engine-choice`)."
> "您正在记录什么技术决策？请提供一个简短标题
> （例如：`event-system-architecture`、`physics-engine-choice`）。"

Use the user's response as the title, then proceed to Step 1.
使用用户的回答作为标题，然后进入第 1 步。

---

## 1. Load Engine Context (ALWAYS FIRST)
## 1. 加载引擎上下文（始终优先）

Before doing anything else, establish the engine environment:
在做任何事之前，先确立引擎环境：

1. Read `docs/engine-reference/[engine]/VERSION.md` to get:
1. 读取 `docs/engine-reference/[engine]/VERSION.md` 以获取：
   - Engine name and version
   - 引擎名称和版本
   - LLM knowledge cutoff date
   - LLM 知识截止日期
   - Post-cutoff version risk levels (LOW / MEDIUM / HIGH)
   - 截止后版本风险级别（LOW / MEDIUM / HIGH）

2. Identify the **domain** of this architecture decision from the title or
   user description. Common domains: Physics, Rendering, UI, Audio, Navigation,
   Animation, Networking, Core, Input, Scripting.
2. 从标题或用户描述中识别此架构决策的**领域**。常见领域：Physics、Rendering、UI、Audio、Navigation、
   Animation、Networking、Core、Input、Scripting。

3. Read the corresponding module reference if it exists:
   `docs/engine-reference/[engine]/modules/[domain].md`
3. 若存在，读取对应的模块参考文档：
   `docs/engine-reference/[engine]/modules/[domain].md`

4. Read `docs/engine-reference/[engine]/breaking-changes.md` — flag any
   changes in the relevant domain that post-date the LLM's training cutoff.
4. 读取 `docs/engine-reference/[engine]/breaking-changes.md` —— 标记相关领域中晚于 LLM 训练截止日期的任何变更。

5. Read `docs/engine-reference/[engine]/deprecated-apis.md` — flag any APIs
   in the relevant domain that should not be used.
5. 读取 `docs/engine-reference/[engine]/deprecated-apis.md` —— 标记相关领域中不应使用的任何 API。

6. **Display a knowledge gap warning** before proceeding if the domain carries
   MEDIUM or HIGH risk:
6. 若领域带有 MEDIUM 或 HIGH 风险，在继续之前**显示知识缺口警告**：

   ```
   ⚠️  ENGINE KNOWLEDGE GAP WARNING
   Engine: [name + version]
   Domain: [domain]
   Risk Level: HIGH — This version is post-LLM-cutoff.

   Key changes verified from engine-reference docs:
   - [Change 1 relevant to this domain]
   - [Change 2]

   This ADR will be cross-referenced against the engine reference library.
   Proceed with verified information only — do NOT rely solely on training data.
   ```
   ```
   ⚠️  引擎知识缺口警告
   引擎：[名称 + 版本]
   领域：[领域]
   风险级别：HIGH —— 此版本晚于 LLM 截止日期。

   已从引擎参考文档验证的关键变更：
   - [与此领域相关的变更 1]
   - [变更 2]

   此 ADR 将与引擎参考库进行交叉引用。
   仅使用经过验证的信息进行 —— 不要仅依赖训练数据。
   ```

   If no engine has been configured yet, prompt: "No engine is configured.
   Run `/setup-engine` first, or tell me which engine you are using."
   若尚未配置引擎，提示："未配置引擎。
   请先运行 `/setup-engine`，或告诉我您使用的引擎。"

---

## 2. Determine the next ADR number
## 2. 确定下一个 ADR 编号

Scan `docs/architecture/` for existing ADRs to find the next number.
扫描 `docs/architecture/` 中现有的 ADR 以查找下一个编号。

---

## 3. Gather context
## 3. 收集上下文

Read related code, existing ADRs, and relevant GDDs from `design/gdd/`.
从 `design/gdd/` 读取相关代码、现有 ADR 和相关 GDD。

### 3a: Architecture Registry Check (BLOCKING gate)
### 3a：架构注册表检查（阻断门禁）

Read `docs/registry/architecture.yaml`. Extract entries relevant to this ADR's
domain and decision (grep by system name, domain keyword, or state being touched).
读取 `docs/registry/architecture.yaml`。提取与此 ADR 领域和决策相关的条目（按系统名、领域关键词或所触及的状态进行 grep）。

Present any relevant stances to the user **before** the collaborative design
begins, as locked constraints:
在协作设计开始**之前**，将任何相关既定立场作为锁定约束呈现给用户：

```
## Existing Architectural Stances (must not contradict)

State Ownership:
  player_health → owned by health-system (ADR-0001)
  Interface: HealthComponent.current_health (read-only float)
  → If this ADR reads or writes player health, it must use this interface.

Interface Contracts:
  damage_delivery → signal pattern (ADR-0003)
  Signal: damage_dealt(amount, target, is_crit)
  → If this ADR delivers or receives damage events, it must use this signal.

Forbidden Patterns:
  ✗ autoload_singleton_coupling (ADR-0001)
  ✗ direct_cross_system_state_write (ADR-0000)
  → The proposed approach must not use these patterns.
```
```
## 现有架构立场（不得违反）

状态所有权：
  player_health → 由 health-system 拥有 (ADR-0001)
  接口：HealthComponent.current_health（只读 float）
  → 若此 ADR 读取或写入玩家生命值，必须使用此接口。

接口契约：
  damage_delivery → 信号模式 (ADR-0003)
  信号：damage_dealt(amount, target, is_crit)
  → 若此 ADR 传递或接收伤害事件，必须使用此信号。

禁止模式：
  ✗ autoload_singleton_coupling (ADR-0001)
  ✗ direct_cross_system_state_write (ADR-0000)
  → 提议的方法不得使用这些模式。
```

If the user's proposed decision would contradict any registered stance, surface
the conflict immediately:
若用户提议的决策与任何已注册立场相冲突，立即暴露冲突：

> "⚠️ Conflict: This ADR proposes [X], but ADR-[NNNN] established that [Y] is
> the accepted pattern for this purpose. Proceeding without resolving this will
> produce contradictory ADRs and inconsistent stories.
> Options: (1) Align with the existing stance, (2) Supersede ADR-[NNNN] with
> an explicit replacement, (3) Explain why this case is an exception."
> "⚠️ 冲突：此 ADR 提议 [X]，但 ADR-[NNNN] 已确立 [Y] 为此目的的既定模式。
> 不解决此冲突继续进行将产生相互矛盾的 ADR 和不一致的故事。
> 选项：(1) 与现有立场对齐，(2) 用明确的替代方案取代 ADR-[NNNN]，(3) 解释为何此情况为例外。"

Do not proceed to Step 4 (collaborative design) until any conflict is resolved
or explicitly accepted as an intentional exception.
在冲突得到解决或明确接受为有意例外之前，不得进入第 4 步（协作设计）。

---

## 4. Guide the decision collaboratively
## 4. 协作引导决策

Before asking anything, derive the skill's best guesses from the context already
gathered (GDDs read, engine reference loaded, existing ADRs scanned). Then present
a **confirm/adjust** prompt using `AskUserQuestion` — not open-ended questions.
在询问任何内容之前，从已收集的上下文（已读 GDD、已加载引擎参考、已扫描现有 ADR）推导技能的最佳猜测。然后使用 `AskUserQuestion` 呈现**确认/调整**提示 —— 而非开放式问题。

**Derive assumptions first:**
**先推导假设：**
- **Problem**: Infer from the title + GDD context what decision needs to be made
- **问题**：从标题 + GDD 上下文推断需要做出什么决策
- **Alternatives**: Propose 2-3 concrete options from engine reference + GDD requirements
- **替代方案**：从引擎参考 + GDD 需求中提出 2-3 个具体选项
- **Dependencies**: Scan existing ADRs for upstream dependencies; assume None if unclear
- **依赖**：扫描现有 ADR 中的上游依赖；若不清楚则假定为 None
- **GDD linkage**: Extract which GDD systems the title directly relates to
- **GDD 关联**：提取标题直接相关的 GDD 系统
- **Status**: Always `Proposed` for new ADRs — never ask the user what the status is
- **状态**：新 ADR 始终为 `Proposed` —— 绝不询问用户状态是什么

**Scope of assumptions tab**: Assumptions cover only: problem framing, alternative approaches, upstream dependencies, GDD linkage, and status. Schema design questions (e.g., "How should spawn timing work?", "Should data be inline or external?") are NOT assumptions — they are design decisions belonging to a separate step after the assumptions are confirmed. Do not include schema design questions in the assumptions AskUserQuestion widget.
**假设标签范围**：假设仅涵盖：问题界定、替代方法、上游依赖、GDD 关联和状态。架构设计问题（如"生成时机应如何运作？"、"数据应内联还是外部化？"）不是假设 —— 它们是在假设确认后属于单独步骤的设计决策。不要在假设的 AskUserQuestion 部件中包含架构设计问题。

**After assumptions are confirmed**, if the ADR involves schema or data design choices, use a separate multi-tab `AskUserQuestion` to ask each design question independently before drafting.
**假设确认后**，若 ADR 涉及架构或数据设计选择，在起草前使用单独的多标签 `AskUserQuestion` 独立询问每个设计问题。

**Present assumptions with `AskUserQuestion`:**
**使用 `AskUserQuestion` 呈现假设：**

```
Here's what I'm assuming before drafting:

Problem: [one-sentence problem statement derived from context]
Alternatives I'll consider:
  A) [option derived from engine reference]
  B) [option derived from GDD requirements]
  C) [option from common patterns]
GDD systems driving this: [list derived from context]
Dependencies: [upstream ADRs if any, otherwise "None"]
Status: Proposed

[A] Proceed — draft with these assumptions
[B] Change the alternatives list
[C] Adjust the GDD linkage
[D] Add a performance budget constraint
[E] Something else needs changing first
```
```
起草前我的假设如下：

问题：[从上下文推导的一句话问题陈述]
我将考虑的替代方案：
  A) [源自引擎参考的选项]
  B) [源自 GDD 需求的选项]
  C) [源自常见模式的选项]
驱动此决策的 GDD 系统：[从上下文推导的列表]
依赖：[如有上游 ADR，否则为 "None"]
状态：Proposed

[A] 继续 —— 使用这些假设起草
[B] 更改替代方案列表
[C] 调整 GDD 关联
[D] 添加性能预算约束
[E] 需要先更改其他内容
```

Do not generate the ADR until the user confirms assumptions or provides corrections.
在用户确认假设或提供更正之前，不要生成 ADR。

**After engine specialist and TD reviews return** (Step 5.5/5.6), if unresolved
decisions remain, present each one as a separate `AskUserQuestion` with the proposed
options as choices plus a free-text escape:
**引擎专家和 TD 评审返回后**（步骤 5.5/5.6），若仍有未解决的决策，将每项作为单独的 `AskUserQuestion` 呈现，以提议的选项作为选择并加上自由文本逃生口：

```
Decision: [specific unresolved point]
[A] [option from specialist review]
[B] [alternative option]
[C] Different approach — I'll describe it
```
```
决策：[具体的未解决点]
[A] [来自专家评审的选项]
[B] [替代选项]
[C] 不同方法 —— 我来描述
```

**ADR Dependencies** — derive from existing ADRs, then confirm:
**ADR 依赖** —— 从现有 ADR 推导，然后确认：
- Does this decision depend on any other ADR not yet Accepted?
- 此决策是否依赖任何尚未 Accepted 的其他 ADR？
- Does it unlock or unblock any other ADR or epic?
- 是否解锁或解除任何其他 ADR 或史诗的阻塞？
- Does it block any specific epic from starting?
- 是否阻塞任何特定史诗的启动？

Record answers in the **ADR Dependencies** section. Write "None" for each field if no constraints apply.
将答案记录在 **ADR Dependencies** 章节。若无约束，每个字段填写 "None"。

---

## 5. Generate the ADR
## 5. 生成 ADR

Following this format:
按以下格式：

```markdown
# ADR-[NNNN]: [Title]
```
```markdown
# ADR-[NNNN]: [标题]
```

```markdown
## Status
[Proposed | Accepted | Deprecated | Superseded by ADR-XXXX]
```
```markdown
## 状态
[Proposed | Accepted | Deprecated | Superseded by ADR-XXXX]
```

```markdown
## Date
[Date of decision]
```
```markdown
## 日期
[决策日期]
```

```markdown
## Engine Compatibility

| Field | Value |
|-------|-------|
| **Engine** | [e.g. Godot 4.6] |
| **Domain** | [Physics / Rendering / UI / Audio / Navigation / Animation / Networking / Core / Input] |
| **Knowledge Risk** | [LOW / MEDIUM / HIGH — from VERSION.md] |
| **References Consulted** | [List engine-reference docs read, e.g. `docs/engine-reference/godot/modules/physics.md`] |
| **Post-Cutoff APIs Used** | [Any APIs from post-LLM-cutoff versions this decision depends on, or "None"] |
| **Verification Required** | [Specific behaviours to test before shipping, or "None"] |
```
```markdown
## 引擎兼容性

| 字段 | 值 |
|-------|-------|
| **引擎** | [例如 Godot 4.6] |
| **领域** | [Physics / Rendering / UI / Audio / Navigation / Animation / Networking / Core / Input] |
| **知识风险** | [LOW / MEDIUM / HIGH —— 来自 VERSION.md] |
| **已查阅参考** | [已读引擎参考文档列表，例如 `docs/engine-reference/godot/modules/physics.md`] |
| **使用的截止后 API** | [此决策依赖的任何截止后 LLM 版本 API，或 "None"] |
| **需要验证** | [发布前需测试的具体行为，或 "None"] |
```

```markdown
## ADR Dependencies

| Field | Value |
|-------|-------|
| **Depends On** | [ADR-NNNN (must be Accepted before this can be implemented), or "None"] |
| **Enables** | [ADR-NNNN (this ADR unlocks that decision), or "None"] |
| **Blocks** | [Epic/Story name — cannot start until this ADR is Accepted, or "None"] |
| **Ordering Note** | [Any sequencing constraint that isn't captured above] |
```
```markdown
## ADR 依赖

| 字段 | 值 |
|-------|-------|
| **依赖于** | [ADR-NNNN（必须在实施前 Accepted），或 "None"] |
| **启用** | [ADR-NNNN（此 ADR 解锁该决策），或 "None"] |
| **阻塞** | [史诗/故事名 —— 在此 ADR Accepted 前无法启动，或 "None"] |
| **排序说明** | [上述未涵盖的任何排序约束] |
```

```markdown
## Context
```
```markdown
## 上下文
```

```markdown
### Problem Statement
[What problem are we solving? Why does this decision need to be made now?]
```
```markdown
### 问题陈述
[我们在解决什么问题？为什么现在需要做此决策？]
```

```markdown
### Constraints
- [Technical constraints]
- [Timeline constraints]
- [Resource constraints]
- [Compatibility requirements]
```
```markdown
### 约束
- [技术约束]
- [时间线约束]
- [资源约束]
- [兼容性要求]
```

```markdown
### Requirements
- [Must support X]
- [Must perform within Y budget]
- [Must integrate with Z]
```
```markdown
### 需求
- [必须支持 X]
- [必须在 Y 预算内执行]
- [必须与 Z 集成]
```

```markdown
## Decision

[The specific technical decision made, described in enough detail for someone
to implement it.]
```
```markdown
## 决策

[做出的具体技术决策，描述需足够详细以使他人能够实施。]
```

```markdown
### Architecture Diagram
[ASCII diagram or description of the system architecture this creates]
```
```markdown
### 架构图
[ASCII 图或此决策所创建的系统架构描述]
```

```markdown
### Key Interfaces
[API contracts or interface definitions this decision creates]
```
```markdown
### 关键接口
[此决策创建的 API 契约或接口定义]
```

```markdown
## Alternatives Considered
```
```markdown
## 考虑过的替代方案
```

```markdown
### Alternative 1: [Name]
- **Description**: [How this would work]
- **Pros**: [Advantages]
- **Cons**: [Disadvantages]
- **Rejection Reason**: [Why this was not chosen]
```
```markdown
### 替代方案 1：[名称]
- **描述**：[将如何运作]
- **优点**：[优势]
- **缺点**：[劣势]
- **拒绝原因**：[为何未被选中]
```

```markdown
### Alternative 2: [Name]
- **Description**: [How this would work]
- **Pros**: [Advantages]
- **Cons**: [Disadvantages]
- **Rejection Reason**: [Why this was not chosen]
```
```markdown
### 替代方案 2：[名称]
- **描述**：[将如何运作]
- **优点**：[优势]
- **缺点**：[劣势]
- **拒绝原因**：[为何未被选中]
```

```markdown
## Consequences
```
```markdown
## 后果
```

```markdown
### Positive
- [Good outcomes of this decision]
```
```markdown
### 正面
- [此决策的良好结果]
```

```markdown
### Negative
- [Trade-offs and costs accepted]
```
```markdown
### 负面
- [接受的权衡和成本]
```

```markdown
### Risks
- [Things that could go wrong]
- [Mitigation for each risk]
```
```markdown
### 风险
- [可能出错的事情]
- [每项风险的缓解措施]
```

```markdown
## GDD Requirements Addressed

| GDD System | Requirement | How This ADR Addresses It |
|------------|-------------|--------------------------|
| [system-name].md | [specific rule, formula, or performance constraint from that GDD] | [how this decision satisfies it] |
```
```markdown
## 已解决的 GDD 需求

| GDD 系统 | 需求 | 此 ADR 如何解决 |
|------------|-------------|--------------------------|
| [系统名].md | [该 GDD 中的具体规则、公式或性能约束] | [此决策如何满足它] |
```

```markdown
## Performance Implications
- **CPU**: [Expected impact]
- **Memory**: [Expected impact]
- **Load Time**: [Expected impact]
- **Network**: [Expected impact, if applicable]
```
```markdown
## 性能影响
- **CPU**：[预期影响]
- **内存**：[预期影响]
- **加载时间**：[预期影响]
- **网络**：[预期影响，若适用]
```

```markdown
## Migration Plan
[If this changes existing code, how do we get from here to there?]
```
```markdown
## 迁移计划
[若此变更现有代码，我们如何从现状迁移到目标？]
```

```markdown
## Validation Criteria
[How will we know this decision was correct? What metrics or tests?]
```
```markdown
## 验证标准
[我们如何知道此决策正确？用什么指标或测试？]
```

```markdown
## Related Decisions
- [Links to related ADRs]
- [Links to related design documents]
```
```markdown
## 相关决策
- [相关 ADR 链接]
- [相关设计文档链接]
```

5.5. **Engine Specialist Validation** — Before saving, spawn the **primary engine specialist** via Task to validate the drafted ADR:
5.5. **引擎专家验证** —— 保存前，通过 Task 派生 **主要引擎专家** 验证起草的 ADR：
   - Read `.claude/docs/technical-preferences.md` `Engine Specialists` section to get the primary specialist
   - 读取 `.claude/docs/technical-preferences.md` 的 `Engine Specialists` 章节以获取主要专家
   - If no engine is configured (`[TO BE CONFIGURED]`), skip this step
   - 若未配置引擎（`[TO BE CONFIGURED]`），跳过此步骤
   - Spawn `subagent_type: [primary specialist]` with: the ADR's Engine Compatibility section, Decision section, Key Interfaces, and the engine reference docs path. Ask them to:
   - 派生 `subagent_type: [primary specialist]`，传递：ADR 的引擎兼容性章节、决策章节、关键接口和引擎参考文档路径。要求他们：
     1. Confirm the proposed approach is idiomatic for the pinned engine version
     1. 确认提议的方法对所固定的引擎版本是惯用的
     2. Flag any APIs or patterns that are deprecated or changed post-training-cutoff
     2. 标记任何已弃用或在训练截止后变更的 API 或模式
     3. Identify engine-specific risks or gotchas not captured in the current ADR draft
     3. 识别当前 ADR 草稿中未涵盖的引擎特定风险或陷阱
   - If the specialist identifies a **blocking issue** (wrong API, deprecated approach, engine version incompatibility): revise the Decision and Engine Compatibility sections accordingly, then confirm the changes with the user before proceeding
   - 若专家识别出**阻断性问题**（错误 API、已弃用方法、引擎版本不兼容）：相应修订决策和引擎兼容性章节，然后在继续前与用户确认变更
   - If the specialist finds **minor notes** only: incorporate them into the ADR's Risks subsection
   - 若专家仅发现**次要提示**：将其纳入 ADR 的风险子章节

**Review mode check** — apply before spawning TD-ADR:
**评审模式检查** —— 在派生 TD-ADR 前应用：
- `solo` → skip. Note: "TD-ADR skipped — Solo mode." Proceed to Step 5.7 (GDD sync check).
- `solo` → 跳过。备注："TD-ADR 已跳过 —— Solo 模式。" 进入步骤 5.7（GDD 同步检查）。
- `lean` → skip (not a PHASE-GATE). Note: "TD-ADR skipped — Lean mode." Proceed to Step 5.7 (GDD sync check).
- `lean` → 跳过（非 PHASE-GATE）。备注："TD-ADR 已跳过 —— Lean 模式。" 进入步骤 5.7（GDD 同步检查）。
- `full` → spawn as normal.
- `full` → 正常派生。

5.6. **Technical Director Strategic Review** — After the engine specialist validation, spawn `technical-director` via Task using gate **TD-ADR** (`.claude/docs/director-gates.md`):
5.6. **技术总监战略评审** —— 引擎专家验证后，通过 Task 使用门禁 **TD-ADR**（`.claude/docs/director-gates.md`）派生 `technical-director`：
   - Pass: the ADR file path (or draft content), engine version, domain, any existing ADRs in the same domain
   - 传递：ADR 文件路径（或草稿内容）、引擎版本、领域、同领域的任何现有 ADR
   - The TD validates architectural coherence (is this decision consistent with the whole system?) — distinct from the engine specialist's API-level check
   - TD 验证架构一致性（此决策是否与整个系统一致？）—— 与引擎专家的 API 级别检查不同
   - If CONCERNS or REJECT: revise the Decision or Alternatives sections accordingly before proceeding
   - 若 CONCERNS 或 REJECT：在继续前相应修订决策或替代方案章节

5.7. **GDD Sync Check** — Before presenting the write approval, scan all GDDs
referenced in the "GDD Requirements Addressed" section for naming inconsistencies
with the ADR's Key Interfaces and Decision sections (renamed signals, API methods,
or data types). If any are found, surface them as a **prominent warning block**
immediately before the write approval — not as a footnote:
5.7. **GDD 同步检查** —— 在呈交写入批准前，扫描 "GDD Requirements Addressed" 章节中引用的所有 GDD，查找与 ADR 的关键接口和决策章节的命名不一致（重命名的信号、API 方法或数据类型）。若发现任何不一致，作为**醒目警告块**呈现在写入批准之前 —— 而非脚注：

```
⚠️ GDD SYNC REQUIRED
[gdd-filename].md uses names this ADR has renamed:
  [old_name] → [new_name_from_adr]
  [old_name_2] → [new_name_2_from_adr]
The GDD must be updated before or alongside writing this ADR to prevent
developers reading the GDD from implementing the wrong interface.
```
```
⚠️ 需要 GDD 同步
[gdd-filename].md 使用了此 ADR 已重命名的名称：
  [old_name] → [new_name_from_adr]
  [old_name_2] → [new_name_2_from_adr]
GDD 必须在写入此 ADR 之前或同时更新，以防止阅读 GDD 的开发人员实现错误的接口。
```

If no inconsistencies: skip this block silently.
若无不一致：静默跳过此块。

5. **Write approval** — Use `AskUserQuestion`:
5. **写入批准** —— 使用 `AskUserQuestion`：

If GDD sync issues were found:
若发现 GDD 同步问题：
- "ADR draft is complete. How would you like to proceed?"
- "ADR 草稿已完成。您希望如何进行？"
  - [A] Write ADR + update GDD in the same pass
  - [A] 写入 ADR + 在同一次中更新 GDD
  - [B] Write ADR only — I'll update the GDD manually
  - [B] 仅写入 ADR —— 我将手动更新 GDD
  - [C] Not yet — I need to review further
  - [C] 暂不 —— 我需要进一步评审

If no GDD sync issues:
若无 GDD 同步问题：
- "ADR draft is complete. May I write it?"
- "ADR 草稿已完成。我可以写入吗？"
  - [A] Write ADR to `docs/architecture/adr-[NNNN]-[slug].md`
  - [A] 将 ADR 写入 `docs/architecture/adr-[NNNN]-[slug].md`
  - [B] Not yet — I need to review further
  - [B] 暂不 —— 我需要进一步评审

If yes to any write option, write the file, creating the directory if needed.
For option [A] with GDD update: also update the GDD file(s) to use the new names.
若任何写入选项为是，写入文件，必要时创建目录。
对于 [A] 选项含 GDD 更新：同时更新 GDD 文件以使用新名称。

6. **Update Architecture Registry**
6. **更新架构注册表**

Scan the written ADR for new architectural stances that should be registered:
扫描已写入的 ADR，查找应注册的新架构立场：
- State it claims ownership of
- 它声明拥有的状态
- Interface contracts it defines (signal signatures, method APIs)
- 它定义的接口契约（信号签名、方法 API）
- Performance budget it claims
- 它声明的性能预算
- API choices it makes explicitly
- 它明确做出的 API 选择
- Patterns it bans (Consequences → Negative or explicit "do not use X")
- 它禁止的模式（后果 → 负面或明确的 "do not use X"）

Present candidates:
呈现候选：
```
Registry candidates from this ADR:
  NEW state ownership:      player_stamina → stamina-system
  NEW interface contract:   stamina_depleted signal
  NEW performance budget:   stamina-system: 0.5ms/frame
  NEW forbidden pattern:    polling stamina each frame (use signal instead)
  EXISTING (referenced_by update only): player_health → already registered ✅
```
```
来自此 ADR 的注册候选：
  新状态所有权：      player_stamina → stamina-system
  新接口契约：   stamina_depleted signal
  新性能预算：   stamina-system: 0.5ms/frame
  新禁止模式：    每帧轮询 stamina（改用信号）
  已存在（仅更新 referenced_by）：player_health → 已注册 ✅
```

**Registry append logic**: When writing to `docs/registry/architecture.yaml`, do NOT assume sections are empty. The file may already have entries from previous ADRs written in this session. Before each Edit call:
**注册表追加逻辑**：写入 `docs/registry/architecture.yaml` 时，不要假设章节为空。文件可能已包含本次会话中先前写入的 ADR 条目。每次 Edit 调用前：
1. Read the current state of `docs/registry/architecture.yaml`
1. 读取 `docs/registry/architecture.yaml` 的当前状态
2. Find the correct section (state_ownership, interfaces, forbidden_patterns, api_decisions)
2. 查找正确的章节（state_ownership、interfaces、forbidden_patterns、api_decisions）
3. Append the new entry AFTER the last existing entry in that section — do not try to replace a `[]` placeholder that may no longer exist
3. 在该章节最后一个现有条目之后追加新条目 —— 不要尝试替换可能已不存在的 `[]` 占位符
4. If the section has entries already, use the closing content of the last entry as the `old_string` anchor, and append the new entry after it
4. 若该章节已有条目，使用最后一个条目的结束内容作为 `old_string` 锚点，在其后追加新条目

**BLOCKING — do not write to `docs/registry/architecture.yaml` without explicit user approval.**
**阻断 —— 未经用户明确批准，不得写入 `docs/registry/architecture.yaml`。**

Ask using `AskUserQuestion`:
使用 `AskUserQuestion` 询问：
- "May I update `docs/registry/architecture.yaml` with these [N] new stances?"
- "我可以用这些 [N] 个新立场更新 `docs/registry/architecture.yaml` 吗？"
  - Options: "Yes — update the registry", "Not yet — I want to review the candidates", "Skip registry update"
  - 选项："是 —— 更新注册表"、"暂不 —— 我想评审候选"、"跳过注册表更新"

Only proceed if the user selects yes. If yes: append new entries. Never modify existing entries — if a stance is
changing, set the old entry to `status: superseded_by: ADR-[NNNN]` and add the new entry.
仅在用户选择"是"时继续。若是：追加新条目。绝不修改现有条目 —— 若立场正在变更，将旧条目设为 `status: superseded_by: ADR-[NNNN]` 并添加新条目。

---

## 6. Closing Next Steps
## 6. 收尾后续步骤

After the ADR is written (and registry optionally updated), close with `AskUserQuestion`.
ADR 写入（并选择性更新注册表）后，用 `AskUserQuestion` 收尾。

Before generating the widget:
生成部件前：
1. Read `docs/registry/architecture.yaml` — check if any priority ADRs are still unwritten (look for ADRs flagged in technical-preferences.md or systems-index.md as prerequisites)
1. 读取 `docs/registry/architecture.yaml` —— 检查是否还有优先 ADR 未编写（查找在 technical-preferences.md 或 systems-index.md 中标记为先决条件的 ADR）
2. Check if all prerequisite ADRs are now written. If yes, include a "Start writing GDDs" option.
2. 检查所有先决 ADR 是否已编写。若是，包含 "开始编写 GDD" 选项。
3. List ALL remaining priority ADRs as individual options — not just the next one or two.
3. 将所有剩余的优先 ADR 列为单独选项 —— 不仅是接下来的一两个。

Widget format:
部件格式：
```
ADR-[NNNN] written and registry updated. What would you like to do next?
[1] Write [next-priority-adr-name] — [brief description from prerequisites list]
[2] Write [another-priority-adr] — [brief description]  (include ALL remaining ones)
[N] Start writing GDDs — run `/design-system [first-undesigned-system]` (only show if all prerequisite ADRs are written)
[N+1] Stop here for this session
```
```
ADR-[NNNN] 已写入且注册表已更新。您接下来想做什么？
[1] 编写 [next-priority-adr-name] —— [来自先决条件列表的简短描述]
[2] 编写 [another-priority-adr] —— [简短描述]  （包含所有剩余的）
[N] 开始编写 GDD —— 运行 `/design-system [first-undesigned-system]`（仅当所有先决 ADR 已编写时显示）
[N+1] 在此会话停止
```

If there are no remaining priority ADRs and no undesigned GDD systems, offer only "Stop here" and suggest running `/architecture-review` in a fresh session.
若无剩余的优先 ADR 且无未设计的 GDD 系统，仅提供"在此停止"并建议在全新会话中运行 `/architecture-review`。

**Always include this fixed notice in the closing output (do NOT omit it):**
**始终在收尾输出中包含此固定通知（不要省略）：**

> To validate ADR coverage against your GDDs, open a **fresh Claude Code session**
> and run `/architecture-review`.
>
> **Never run `/architecture-review` in the same session as `/architecture-decision`.**
> The reviewing agent must be independent of the authoring context to give an unbiased
> assessment. Running it here would invalidate the review.
> 要针对您的 GDD 验证 ADR 覆盖范围，请打开**全新的 Claude Code 会话**并运行 `/architecture-review`。
>
> **绝不要在与 `/architecture-decision` 相同的会话中运行 `/architecture-review`。**
> 评审智能体必须独立于编写上下文才能给出无偏评估。在此运行将使评审失效。

Update any stories that were `Status: Blocked` pending this ADR to `Status: Ready`.
将所有因等待此 ADR 而 `Status: Blocked` 的故事更新为 `Status: Ready`。
