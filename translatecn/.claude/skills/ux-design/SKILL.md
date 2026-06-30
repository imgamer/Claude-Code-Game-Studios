---
name: ux-design
description: "Guided, section-by-section UX spec authoring for a screen, flow, or HUD. Reads game concept, player journey, and relevant GDDs to provide context-aware design guidance. Produces ux-spec.md (per screen/flow) or hud-design.md using the studio templates."
argument-hint: "[screen/flow name] or 'hud' or 'patterns'"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Task
model: sonnet
agent: ux-designer
---
---
名称：ux-design
描述："为屏幕、流程或 HUD 进行引导式、逐节的 UX 规范编写。读取游戏概念、玩家旅程和相关 GDD 以提供上下文感知的设计指导。使用工作室模板生成 ux-spec.md（每个屏幕/流程）或 hud-design.md。"
argument-hint："[屏幕/流程名] 或 'hud' 或 'patterns'"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, AskUserQuestion, Task
model：sonnet
agent：ux-designer
---

When this skill is invoked:
当此技能被调用时：

## 1. Parse Arguments & Determine Mode
## 1. 解析参数并确定模式

Three authoring modes exist based on the argument:
根据参数存在三种编写模式：

| Argument | Mode | Output file |
|----------|------|-------------|
| `hud` | HUD design | `design/ux/hud.md` |
| `patterns` | Interaction pattern library | `design/ux/interaction-patterns.md` |
| Any other value (e.g., `main-menu`, `inventory`) | UX spec for a screen or flow | `design/ux/[argument].md` |
| No argument | Ask the user | (see below) |
| 参数 | 模式 | 输出文件 |
|----------|------|-------------|
| `hud` | HUD 设计 | `design/ux/hud.md` |
| `patterns` | 交互模式库 | `design/ux/interaction-patterns.md` |
| 任何其他值（例如 `main-menu`、`inventory`） | 屏幕或流程的 UX 规范 | `design/ux/[argument].md` |
| 无参数 | 询问用户 | （见下文） |

**If no argument is provided**, do not fail — ask instead. Use `AskUserQuestion`:
- "What are we designing today?"
  - Options: "A specific screen or flow (I'll name it)", "The game HUD", "The interaction pattern library", "I'm not sure — help me figure it out"
**如果未提供参数**，不要失败 — 改为询问。使用 `AskUserQuestion`：
- "我们今天设计什么？"
  - 选项："一个特定的屏幕或流程（我来命名）"、"游戏 HUD"、"交互模式库"、"我不确定 — 帮我弄清楚"

If the user selects "I'll name it" or types a screen name, normalize it to kebab-case
for the filename (e.g., "Main Menu" becomes `main-menu`).
如果用户选择"我来命名"或输入屏幕名，将其规范化为 kebab-case
作为文件名（例如，"Main Menu" 变为 `main-menu`）。

---

## 2. Gather Context (Read Phase)
## 2. 收集上下文（阅读阶段）

Read all relevant context **before** asking the user anything. The skill's value
comes from arriving informed.
在向用户询问任何内容**之前**读取所有相关上下文。技能的价值
来自有备而来。

### 2a: Required Reads
### 2a：必读内容

- **Game concept**: Read `design/gdd/game-concept.md` — if missing, warn:
  > "No game concept found. Run `/brainstorm` first to establish the game's
  > foundation before designing UX."
  > Continue anyway if the user asks.
- **游戏概念**：读取 `design/gdd/game-concept.md` — 如果缺失，警告：
  > "未找到游戏概念。先运行 `/brainstorm` 在设计 UX 之前
  > 确立游戏的基础。"
  > 如果用户要求，仍可继续。

### 2b: Player Journey
### 2b：玩家旅程

Read `design/player-journey.md` if it exists. For each relevant section, extract:
- Which journey phase(s) does this screen appear in?
- What is the player's emotional state on arrival at this screen?
- What player need is this screen serving in the journey?
- What critical moments (from the journey map) does this screen deliver?
读取 `design/player-journey.md`（如果存在）。对于每个相关部分，提取：
- 此屏幕出现在哪些旅程阶段？
- 玩家到达此屏幕时的情感状态是什么？
- 此屏幕在旅程中满足什么玩家需求？
- 此屏幕传递了哪些关键时刻（来自旅程地图）？

If the player journey file does not exist, note the gap and proceed:
如果玩家旅程文件不存在，记录缺口并继续：

> "No player journey map found at `design/player-journey.md`. Designing without it
> means we'll be making assumptions about player context. Consider running a player
> journey session after this spec is drafted."
> "在 `design/player-journey.md` 未找到玩家旅程地图。没有它进行设计
> 意味着我们将对玩家上下文做出假设。考虑在此规范起草后
> 进行玩家旅程会话。"

Also add to the UX spec's Open Questions section:
同时添加到 UX 规范的待解决问题部分：

> "Player journey map not yet created. Template available at `.claude/docs/templates/player-journey.md`. Run `/ux-design` Phase 2b or create it manually to establish player context for this screen."
> "玩家旅程地图尚未创建。模板位于 `.claude/docs/templates/player-journey.md`。运行 `/ux-design` 阶段 2b 或手动创建以为此屏幕建立玩家上下文。"

### 2c: GDD UI Requirements
### 2c：GDD UI 需求

Glob `design/gdd/*.md` and grep for `UI Requirements` sections. Read any GDD whose
UI Requirements section references this screen by name or category.
Glob `design/gdd/*.md` 并 grep `UI Requirements` 部分。读取任何
UI Requirements 部分按名称或类别引用此屏幕的 GDD。

These GDD UI Requirements are the **requirements input** to this spec. Collect them
as a list of constraints the spec must satisfy.
这些 GDD UI 需求是此规范的**需求输入**。将它们收集为
规范必须满足的约束列表。

If designing the HUD, read ALL GDD UI Requirements sections — the HUD aggregates
requirements from every system.
如果设计 HUD，读取所有 GDD UI Requirements 部分 — HUD 聚合
每个系统的需求。

### 2d: Existing UX Specs
### 2d：已有 UX 规范

Glob `design/ux/*.md` and note which screens already have specs. For screens that
will link to or from the current screen, read their navigation/flow sections to
find the entry and exit points this spec must match.
Glob `design/ux/*.md` 并注意哪些屏幕已有规范。对于
将链接到或从当前屏幕链接的屏幕，读取其导航/流程部分以
找到此规范必须匹配的入口和出口点。

### 2e: Interaction Pattern Library
### 2e：交互模式库

If `design/ux/interaction-patterns.md` exists, read the pattern catalog index
(the list of pattern names and their one-line descriptions). Do not read full
pattern details — just the catalog. This tells you which patterns already exist
so you can reference them rather than reinvent them.
如果 `design/ux/interaction-patterns.md` 存在，读取模式目录索引
（模式名称及其一行描述列表）。不要读取完整
模式详情 — 仅目录。这告诉你已存在哪些模式，
以便引用而非重新发明。

### 2f: Art Bible
### 2f：美术圣经

Check for `design/art/art-bible.md`. If found, read the visual direction
section. UX layout must align with the aesthetic commitments already made.
检查 `design/art/art-bible.md`。如果找到，读取视觉方向
部分。UX 布局必须与已做出的美学承诺一致。

### 2g: Accessibility Requirements
### 2g：无障碍需求

Check for `design/accessibility-requirements.md`. If found, read it. The spec
must satisfy the accessibility tier committed to there.
检查 `design/accessibility-requirements.md`。如果找到，读取它。规范
必须满足其中承诺的无障碍等级。

### 2h: Input Method (from Project Config)
### 2h：输入方法（来自项目配置）

Read `.claude/docs/technical-preferences.md` and extract the `## Input & Platform`
section. Store these values for use throughout the skill — they drive the
Interaction Map and inform accessibility requirements:
读取 `.claude/docs/technical-preferences.md` 并提取 `## Input & Platform`
部分。存储这些值供整个技能使用 — 它们驱动
交互地图并为无障碍需求提供信息：

- **Input Methods** — e.g., Keyboard/Mouse, Gamepad, Touch, Mixed
- **Primary Input** — the dominant input for this game
- **Gamepad Support** — Full / Partial / None
- **Touch Support** — Full / Partial / None
- **Target Platforms** — for safe zone and aspect ratio decisions
- **输入方法** — 例如，键盘/鼠标、手柄、触摸、混合
- **主要输入** — 此游戏的主要输入方式
- **手柄支持** — 完整 / 部分 / 无
- **触摸支持** — 完整 / 部分 / 无
- **目标平台** — 用于安全区和宽高比决策

If the section is unconfigured (`[TO BE CONFIGURED]`), ask once:
如果该部分未配置（`[TO BE CONFIGURED]`），询问一次：

> "Input methods aren't configured yet. What does this game target?"
> Options: "Keyboard/Mouse only", "Gamepad only", "Both (PC + Console)", "Touch (mobile)", "All of the above"
>
> (Run `/setup-engine` to save this permanently so you won't be asked again.)
> "输入方法尚未配置。此游戏面向什么？"
> 选项："仅键盘/鼠标"、"仅手柄"、"两者都有（PC + 主机）"、"触摸（移动）"、"以上全部"
>
> （运行 `/setup-engine` 永久保存此项，这样你就不会再被询问。）

Store the answer for the rest of this session. Do **not** ask again per section
or per screen.
为此会话的其余部分存储答案。不要按部分
或按屏幕再次询问。

### 2i: Present Context Summary
### 2i：呈现上下文摘要

Before any design work, present a brief summary to the user:
在任何设计工作之前，向用户呈现简要摘要：

> **Designing: [Screen/Flow Name]**
> - Mode: [UX Spec / HUD Design / Pattern Library]
> - Journey phase(s): [from player-journey.md, or "unknown — no journey map"]
> - GDD requirements feeding this spec: [count and names, or "none found"]
> - Related screens already specced: [list, or "none yet"]
> - Known patterns available: [count, or "no pattern library yet"]
> - Accessibility tier: [from requirements doc, or "not yet defined"]
> - Input methods: [from technical-preferences.md, or "asked above"]
> **正在设计：[屏幕/流程名]**
> - 模式：[UX 规范 / HUD 设计 / 模式库]
> - 旅程阶段：[来自 player-journey.md，或"未知 — 无旅程地图"]
> - 输入此规范的 GDD 需求：[计数和名称，或"未找到"]
> - 已有规范的相关屏幕：[列表，或"尚无"]
> - 可用的已知模式：[计数，或"尚无模式库"]
> - 无障碍等级：[来自需求文档，或"未定义"]
> - 输入方法：[来自 technical-preferences.md，或"上方已询问"]

Then ask: "Anything else I should read before we start, or shall we proceed?"
然后询问："开始之前我还有什么应该读取的，还是我们继续？"

---

## 2b. Retrofit Mode Detection
## 2b. 改造模式检测

Before creating a skeleton, check if the target output file already exists.
创建骨架之前，检查目标输出文件是否已存在。

Glob `design/ux/[filename].md` (where `[filename]` is the resolved output path from Phase 1).
Glob `design/ux/[filename].md`（其中 `[filename]` 是阶段 1 解析的输出路径）。

**If the file exists — retrofit mode:**
- Read the file in full
- For each expected section, check whether the body has real content (more than a `[To be designed]` placeholder) or is empty/placeholder
- Present a section status summary to the user:
**如果文件已存在 — 改造模式：**
- 完整读取文件
- 对于每个预期部分，检查正文是否有实际内容（超过 `[To be designed]` 占位符）还是为空/占位符
- 向用户呈现部分状态摘要：

> "Found existing UX spec at `design/ux/[filename].md`. Here's what's already done:
>
> | Section | Status |
> |---------|--------|
> | Overview & Context | [Complete / Empty / Placeholder] |
> | Player Journey Integration | ... |
> | Screen Layout & Information Architecture | ... |
> | Interaction Model | ... |
> | Feedback & State Communication | ... |
> | Accessibility | ... |
> | Edge Cases & Error States | ... |
> | Open Questions | ... |
>
> I'll work on the [N] incomplete sections only — existing content will not be overwritten."
> "在 `design/ux/[filename].md` 找到已有 UX 规范。这是已完成的内容：
>
> | 部分 | 状态 |
> |---------|--------|
> | 概述与上下文 | [完成 / 空 / 占位符] |
> | 玩家旅程整合 | ... |
> | 屏幕布局与信息架构 | ... |
> | 交互模型 | ... |
> | 反馈与状态沟通 | ... |
> | 无障碍 | ... |
> | 边界情况与错误状态 | ... |
> | 待解决问题 | ... |
>
> 我将仅处理 [N] 个未完成部分 — 不会覆盖已有内容。"

- Skip Section 3 (skeleton creation) — the file already exists
- In Phase 4 (Section Authoring), only work on sections with Status: Empty or Placeholder
- Use `Edit` to fill placeholders in-place rather than creating a new skeleton
- 跳过第 3 部分（骨架创建）— 文件已存在
- 在阶段 4（部分编写）中，仅处理状态为空或占位符的部分
- 使用 `Edit` 原地填充占位符而非创建新骨架

**If the file does not exist — fresh authoring mode:**
Proceed to Phase 3 (Create File Skeleton) as normal.
**如果文件不存在 — 全新编写模式：**
正常进行阶段 3（创建文件骨架）。

---

## 3. Create File Skeleton
## 3. 创建文件骨架

Once the user confirms, **immediately** create the output file with empty section
headers. This ensures incremental writes have a target and work survives interruptions.
用户确认后，**立即**创建带空部分
标题的输出文件。这确保增量写入有目标且工作能在中断后保存。

Ask: "May I create the skeleton file at `design/ux/[filename].md`?"
询问："我可以在 `design/ux/[filename].md` 创建骨架文件吗？"

---

### Skeleton for UX Spec (screen or flow)
### UX 规范骨架（屏幕或流程）

```markdown
# UX Spec: [Screen/Flow Name]

> **Status**: In Design
> **Author**: [user + ux-designer]
> **Last Updated**: [today's date]
> **Journey Phase(s)**: [from context]
> **Template**: UX Spec

---

## Purpose & Player Need

[To be designed]

---

## Player Context on Arrival

[To be designed]

---

## Navigation Position

[To be designed]

---

## Entry & Exit Points

[To be designed]

---

## Layout Specification

### Information Hierarchy

[To be designed]

### Layout Zones

[To be designed]

### Component Inventory

[To be designed]

### ASCII Wireframe

[To be designed]

---

## States & Variants

[To be designed]

---

## Interaction Map

[To be designed]

---

## Events Fired

[To be designed]

---

## Transitions & Animations

[To be designed]

---

## Data Requirements

[To be designed]

---

## Accessibility

[To be designed]

---

## Localization Considerations

[To be designed]

---

## Acceptance Criteria

[To be designed]

---

## Open Questions

[To be designed]
```

---

### Skeleton for HUD Design
### HUD 设计骨架

```markdown
# HUD Design

> **Status**: In Design
> **Author**: [user + ux-designer]
> **Last Updated**: [today's date]
> **Template**: HUD Design

---

## HUD Philosophy

[To be designed]

---

## Information Architecture

### Full Information Inventory

[To be designed]

### Categorization

[To be designed]

---

## Layout Zones

[To be designed]

---

## HUD Elements

[To be designed]

---

## Dynamic Behaviors

[To be designed]

---

## Platform & Input Variants

[To be designed]

---

## Accessibility

[To be designed]

---

## Open Questions

[To be designed]
```

---

### Skeleton for Interaction Pattern Library
### 交互模式库骨架

```markdown
# Interaction Pattern Library

> **Status**: In Design
> **Author**: [user + ux-designer]
> **Last Updated**: [today's date]
> **Template**: Interaction Pattern Library

---

## Overview

[To be designed]

---

## Pattern Catalog

[To be designed]

---

## Patterns

[Individual pattern entries added here as they are defined]

---

## Gaps & Patterns Needed

[To be designed]

---

## Open Questions

[To be designed]
```

---

After writing the skeleton, update `production/session-state/active.md` with:
写入骨架后，更新 `production/session-state/active.md`：

- Task: Designing [screen/flow name] UX spec
- Current section: Starting (skeleton created)
- File: design/ux/[filename].md
- 任务：设计 [屏幕/流程名] UX 规范
- 当前部分：开始（骨架已创建）
- 文件：design/ux/[filename].md

---

## 4. Section-by-Section Authoring
## 4. 逐节编写

Walk through each section in order. For **each section**, follow this cycle:
按顺序遍历每个部分。对于**每个部分**，遵循此循环：

```
Context  ->  Questions  ->  Options  ->  Decision  ->  Draft  ->  Approval  ->  Write
```

1. **Context**: State what this section needs to contain and surface any relevant
   constraints from context gathered in Phase 2.
2. **Questions**: Ask what is needed to draft this section. Use `AskUserQuestion`
   for constrained choices, conversational text for open-ended exploration.
3. **Options**: Where design choices exist, present 2-4 approaches with pros/cons.
   Explain reasoning in conversation, then use `AskUserQuestion` to capture the decision.
4. **Decision**: User picks an approach or provides custom direction.
5. **Draft**: Write the section content in conversation for review. Flag provisional
   assumptions explicitly.
6. **Approval**: Use `AskUserQuestion`:
   - "Does this capture the [section name] correctly?"
   - Options: "Yes — write it to the file", "Small changes needed (describe below)", "Major rethink needed"
   Do not proceed to step 7 until the user selects "Yes".
7. **Write**: Use `AskUserQuestion`: "May I write the [section name] section to `[filepath]`?"
   - Options: "Yes, write it", "Wait — one more change"
   Once confirmed, use `Edit` to replace the `[To be designed]` placeholder with approved content.
1. **上下文**：说明此部分需要包含什么并展示阶段 2 中收集的
   上下文的任何相关约束。
2. **问题**：询问起草此部分需要什么。使用 `AskUserQuestion`
   进行受限选择，用对话文本进行开放式探索。
3. **选项**：存在设计选择时，呈现 2-4 种方法及其优缺点。
   在对话中解释理由，然后使用 `AskUserQuestion` 捕获决策。
4. **决策**：用户选择方法或提供自定义方向。
5. **草稿**：在对话中编写部分内容以供评审。明确标记
   暂定假设。
6. **批准**：使用 `AskUserQuestion`：
   - "这是否正确捕获了 [部分名]？"
   - 选项："是 — 写入文件"、"需要小修改（下方描述）"、"需要重大重新思考"
   在用户选择"是"之前不要进入步骤 7。
7. **写入**：使用 `AskUserQuestion`："我可以将 [部分名] 部分写入 `[filepath]` 吗？"
   - 选项："是，写入"、"等等 — 再改一处"
   确认后，使用 `Edit` 用批准的内容替换 `[To be designed]` 占位符。

After writing each section, update `production/session-state/active.md`.
写入每个部分后，更新 `production/session-state/active.md`。

---

### Section Guidance: UX Spec Mode
### 部分指导：UX 规范模式

#### Section A: Purpose & Player Need
#### A 部分：目的与玩家需求

This section is the foundation. Every other decision flows from it.
此部分是基础。所有其他决策都由此延伸。

**Questions to ask**:
- "What player goal does this screen serve? What is the player trying to DO here?"
- "What would go wrong if this screen didn't exist or was hard to use?"
- "Complete this sentence: 'The player arrives at this screen wanting to ___.' "
**要问的问题**：
- "此屏幕服务于什么玩家目标？玩家试图在这里做什么？"
- "如果此屏幕不存在或难以使用，会出什么问题？"
- "完成这句话：'玩家到达此屏幕时想要 ___。'"

Cross-reference the player journey context gathered in Phase 2. The stated purpose
must align with the journey phase and emotional state.
交叉引用阶段 2 中收集的玩家旅程上下文。声明的目的
必须与旅程阶段和情感状态一致。

---

#### Section B: Player Context on Arrival
#### B 部分：到达时的玩家上下文

**Questions to ask**:
- "When in the game does a player first encounter this screen?"
- "What were they just doing immediately before reaching this screen?"
- "What emotional state should the design assume? (calm, stressed, curious, time-pressured)"
- "Do players arrive at this screen voluntarily, or are they sent here by the game?"
**要问的问题**：
- "玩家在游戏中何时首次遇到此屏幕？"
- "到达此屏幕之前他们刚刚在做什么？"
- "设计应假设什么情感状态？（平静、紧张、好奇、时间紧迫）"
- "玩家是自愿到达此屏幕，还是被游戏引导到这里的？"

Offer to map this against the journey phases if the player journey doc exists.
如果玩家旅程文档存在，提议将此与旅程阶段映射。

---

#### Section B2: Navigation Position
#### B2 部分：导航位置

Where does this screen sit in the game's navigation hierarchy? This is a one-paragraph orientation map — not a full flow diagram.
此屏幕在游戏导航层级中的位置是什么？这是一段导向地图 — 而非完整流程图。

**Questions to ask**:
- "Is this screen accessed from the main menu, from pause, from within gameplay, or from another screen?"
- "Is it a top-level destination (always reachable) or a context-dependent one (only accessible in certain states)?"
- "Can the player reach this screen from more than one place in the game?"
**要问的问题**：
- "此屏幕是从主菜单、暂停、游戏内还是从另一个屏幕访问？"
- "它是顶级目标（始终可达）还是上下文相关的（仅在特定状态下可访问）？"
- "玩家能从游戏中多个地方到达此屏幕吗？"

Present as: "This screen lives at: [root] → [parent] → [this screen]" plus any alternate entry paths.
呈现为："此屏幕位于：[root] → [parent] → [this screen]"加上任何备用入口路径。

---

#### Section B3: Entry & Exit Points
#### B3 部分：入口和出口点

Map every way the player can arrive at and leave this screen.
映射玩家到达和离开此屏幕的所有方式。

**Questions to ask**:
- "What are all the ways a player can reach this screen?" (List each trigger: button press, game event, redirect from another screen, etc.)
- "What can the player do to exit? What happens when they do?" (Back button, confirm action, timeout, game event)
- "Are there any exits that are one-way — where the player cannot return to this screen without starting over?"
**要问的问题**：
- "玩家到达此屏幕的所有方式是什么？"（列出每个触发器：按钮按下、游戏事件、从另一个屏幕重定向等）
- "玩家可以做什么来退出？他们退出时会发生什么？"（返回按钮、确认操作、超时、游戏事件）
- "是否有任何单向出口 — 玩家无法在不重新开始的情况下返回此屏幕？"

Present as two tables:
呈现为两个表格：

| Entry Source | Trigger | Player carries this context |
|---|---|---|
| [screen/event] | [how] | [state/data they arrive with] |

| Exit Destination | Trigger | Notes |
|---|---|---|
| [screen/event] | [how] | [any irreversible state changes] |

| 入口来源 | 触发器 | 玩家携带的上下文 |
|---|---|---|
| [屏幕/事件] | [方式] | [他们到达时携带的状态/数据] |

| 出口目标 | 触发器 | 备注 |
|---|---|---|
| [屏幕/事件] | [方式] | [任何不可逆的状态变化] |

---

#### Section C: Layout Specification
#### C 部分：布局规范

This is the largest and most interactive section. Work through it in sub-sections:
这是最大且最互动的部分。按子部分处理：

**Sub-section 1 — Information Hierarchy** (establish this before any layout):
- Ask the user to list every piece of information this screen must communicate.
- Then ask them to rank the items: "What is the single most important thing a player
  needs to see first? What is second? What can be discovered rather than immediately visible?"
- Present the resulting hierarchy for approval before moving to zones.
**子部分 1 — 信息层级**（在任何布局之前建立此内容）：
- 要求用户列出此屏幕必须传达的每条信息。
- 然后要求他们对条目排序："玩家首先需要看到的
  最重要的一件事是什么？第二是什么？什么可以被发现而非立即可见？"
- 在进入区域之前呈现结果层级以获批准。

**Sub-section 2 — Layout Zones**:
- Based on the information hierarchy, propose rough screen zones (header, content
  area, action bar, sidebar, etc.).
- Offer 2-3 zone arrangements with rationale for each. Reference platform and
  input context gathered from game concept.
- Use `AskUserQuestion` to capture the choice:
  - "Which zone arrangement fits best?"
  - Options: [the 2-3 named arrangements you just presented] + "None — build a custom arrangement"
**子部分 2 — 布局区域**：
- 基于信息层级，提出粗略的屏幕区域（页眉、内容
  区域、操作栏、侧边栏等）。
- 提供 2-3 种区域排列方案并附理由。引用从游戏概念收集的
  平台和输入上下文。
- 使用 `AskUserQuestion` 捕获选择：
  - "哪种区域排列最合适？"
  - 选项：[你刚呈现的 2-3 种命名排列] + "无 — 构建自定义排列"

**Sub-section 3 — Component Inventory**:
- For each zone, list the UI components it contains. For each component, note:
  - Component type (button, list, card, stat display, input field, etc.)
  - Content it displays
  - Whether it is interactive
  - If it uses an existing pattern from the library (reference by pattern name)
  - If it introduces a new pattern (flag for later addition to the library)
**子部分 3 — 组件清单**：
- 对于每个区域，列出其包含的 UI 组件。对于每个组件，注意：
  - 组件类型（按钮、列表、卡片、状态显示、输入字段等）
  - 它显示的内容
  - 是否可交互
  - 是否使用库中的现有模式（按模式名引用）
  - 是否引入新模式（标记以便稍后添加到库中）

**Sub-section 4 — ASCII Wireframe**:
- Offer to generate an ASCII wireframe based on the zone layout and component list.
- Use `AskUserQuestion`: "Want an ASCII wireframe as part of this spec?"
  - Options: "Yes, include one", "No, I'll attach a separate file"
- If yes, produce the wireframe in conversation first. Ask for feedback before
  writing it to file.
**子部分 4 — ASCII 线框图**：
- 提议基于区域布局和组件列表生成 ASCII 线框图。
- 使用 `AskUserQuestion`："需要 ASCII 线框图作为此规范的一部分吗？"
  - 选项："是，包含一个"、"否，我会附上单独文件"
- 如果是，先在对话中生成线框图。在
  写入文件之前请求反馈。

---

#### Section D: States & Variants
#### D 部分：状态与变体

Guide the user to think beyond the happy path.
引导用户超越正常路径思考。

**Questions to ask** (work through these one at a time):
- "What does this screen look like the very first time a player sees it, when there
  is no data yet? (empty state)"
- "What happens when something goes wrong — an error, a failed action, a missing
  resource? (error state)"
- "Is there ever a loading wait on this screen? If so, what does it show? (loading state)"
- "Are there any player progression states that change what this screen shows? For
  example, locked content, premium content, or tutorial-mode overlays?"
- "Does this screen behave differently on any supported platform? (platform variant)"
**要问的问题**（逐一处理）：
- "当玩家第一次看到此屏幕时，还没有
  任何数据，它看起来什么样？（空状态）"
- "当出错时会发生什么 — 错误、失败操作、缺失
  资源？（错误状态）"
- "此屏幕上是否有加载等待？如果有，它显示什么？（加载状态）"
- "是否有任何玩家进度状态改变此屏幕显示的内容？例如，
  锁定内容、付费内容或教程模式覆盖？"
- "此屏幕在任何支持的平台上行为不同吗？（平台变体）"

Present the collected states as a table for approval:
将收集的状态呈现为表格以获批准：

| State / Variant | Trigger | What Changes |
|-----------------|---------|--------------|
| Default | Normal load | — |
| Empty | No data available | [content area description] |
| [etc.] | [trigger] | [changes] |

| 状态 / 变体 | 触发器 | 变化内容 |
|-----------------|---------|--------------|
| 默认 | 正常加载 | — |
| 空 | 无可用数据 | [内容区域描述] |
| [等] | [触发器] | [变化] |

---

#### Section E: Interaction Map
#### E 部分：交互地图

For each interactive component identified in the Layout Specification, define:
- The action (tap, click, press, hold, scroll, drag)
- The platform input(s) that trigger it (mouse click, gamepad A, keyboard Enter)
- The immediate feedback (visual, audio, haptic)
- The outcome (navigation target, state change, data write)
对于布局规范中识别的每个交互组件，定义：
- 动作（点击、单击、按下、长按、滚动、拖拽）
- 触发它的平台输入（鼠标单击、手柄 A、键盘回车）
- 即时反馈（视觉、音频、触觉）
- 结果（导航目标、状态变化、数据写入）

Use the input methods loaded from `technical-preferences.md` in Phase 2h — do
not ask the user again. State them upfront: "Mapping interactions for:
[Input Methods from tech-prefs]. Covering [Gamepad Support] gamepad support."
使用阶段 2h 中从 `technical-preferences.md` 加载的输入方法 — 不要
再次询问用户。预先声明："为以下内容映射交互：
[来自 tech-prefs 的输入方法]。覆盖 [手柄支持] 手柄支持。"

Work through components one at a time rather than asking for all at once.
For navigation actions (going to another screen), verify the target matches
an existing UX spec or note it as a spec dependency.
一次处理一个组件而非一次询问所有。
对于导航操作（转到另一个屏幕），验证目标是否匹配
已有 UX 规范或将其标记为规范依赖。

---

#### Section E2: Events Fired
#### E2 部分：触发事件

For every player action in the Interaction Map, document the corresponding event the game or analytics system should fire — or explicitly note "no event" if none applies.
对于交互地图中的每个玩家动作，记录游戏或分析系统应触发的相应事件 — 或如果无适用则明确注明"无事件"。

**Questions to ask**:
- "For each action, should the game fire an analytics event, trigger a game-state change, or both?"
- "Are there any actions that should NOT fire an event — and is that a deliberate choice?"
**要问的问题**：
- "对于每个动作，游戏应触发分析事件、触发游戏状态变化还是两者都有？"
- "是否有任何不应触发事件的动作 — 这是有意的选择吗？"

Present as a table alongside the Interaction Map:
与交互地图一起呈现为表格：

| Player Action | Event Fired | Payload / Data |
|---|---|---|
| [action] | [EventName] or none | [data passed with event] |

| 玩家动作 | 触发事件 | 载荷 / 数据 |
|---|---|---|
| [动作] | [事件名] 或无 | [随事件传递的数据] |

Flag any action that modifies persistent game state (save data, progress, economy) — these need explicit attention from the architecture team.
标记任何修改持久游戏状态（存档数据、进度、经济）的动作 — 这些需要架构团队明确关注。

---

#### Section E3: Transitions & Animations
#### E3 部分：过渡和动画

Specify how the screen enters and exits, and how it responds to state changes.
指定屏幕如何进入和退出，以及它如何响应状态变化。

**Questions to ask**:
- "How does this screen appear? (fade in, slide from right, instant pop, scale from button)"
- "How does it dismiss? (fade out, slide back, cut)"
- "Are there any in-screen state transitions that need animation? (loading spinner, success state, error flash)"
- "Is there any animation that could cause motion sickness — and does the game have a reduced-motion option?"
**要问的问题**：
- "此屏幕如何出现？（淡入、从右滑入、立即弹出、从按钮缩放）"
- "它如何消失？（淡出、滑回、剪切）"
- "是否有任何屏幕内状态过渡需要动画？（加载旋转器、成功状态、错误闪烁）"
- "是否有任何可能导致晕动症的动画 — 游戏是否有减少动效选项？"

Minimum required:
- Screen enter transition
- Screen exit transition
- At least one state-change animation if the screen has multiple states
最低要求：
- 屏幕进入过渡
- 屏幕退出过渡
- 如果屏幕有多个状态，至少一个状态变化动画

---

#### Section F: Data Requirements
#### F 部分：数据需求

Cross-reference the GDD UI Requirements sections gathered in Phase 2.
交叉引用阶段 2 中收集的 GDD UI Requirements 部分。

For each piece of information the screen displays, ask:
- "Where does this data come from? Which system owns it?"
- "Does this screen need to write data back, or is it read-only?"
- "Is any of this data time-sensitive or real-time? (health bars, cooldown timers)"
对于屏幕显示的每条信息，询问：
- "此数据来自哪里？哪个系统拥有它？"
- "此屏幕需要回写数据还是只读？"
- "是否有任何数据是时间敏感或实时的？（血条、冷却计时器）"

Flag any case where the UI would need to own or manage game state as an architectural
concern. UX specs define what the UI needs; they do not dictate how the data is
delivered. That is an architecture decision.
标记任何 UI 需要拥有或管理游戏状态的情况作为架构
关注点。UX 规范定义 UI 需要什么；它们不规定数据如何
传递。那是架构决策。

Present the data requirements as a table:
将数据需求呈现为表格：

| Data | Source System | Read / Write | Notes |
|------|--------------|--------------|-------|
| [item] | [system] | Read | — |
| [item] | [system] | Write | [concern if any] |

| 数据 | 源系统 | 读 / 写 | 备注 |
|------|--------------|--------------|-------|
| [条目] | [系统] | 读 | — |
| [条目] | [系统] | 写 | [如有顾虑] |

---

#### Section G: Accessibility
#### G 部分：无障碍

Cross-reference `design/accessibility-requirements.md` if it exists.
交叉引用 `design/accessibility-requirements.md`（如果存在）。

Walk through the ux-designer agent's standard checklist for this screen:
为 此屏幕走查 ux-designer 智能体的标准检查清单：

- Keyboard-only navigation path through all interactive elements
- Gamepad navigation order (if applicable)
- Text contrast and minimum readable font sizes
- Color-independent communication (no information conveyed by color alone)
- Screen reader considerations for any non-text elements
- Any motion or animation that needs a reduced-motion alternative
- 通过所有交互元素的仅键盘导航路径
- 手柄导航顺序（如适用）
- 文本对比度和最小可读字体大小
- 不依赖颜色的沟通（无信息仅通过颜色传达）
- 任何非文本元素的屏幕阅读器考虑
- 任何需要减少动效替代的动效或动画

If no accessibility tier has been defined for this project, note the gap in the UX spec's Open Questions section:
如果项目尚未定义无障碍等级，在 UX 规范的待解决问题部分记录缺口：

> "Accessibility tier not yet defined — consider WCAG-AA as a baseline. Run `/gate-check` to see whether this blocks any phase gates."
> "无障碍等级尚未定义 — 考虑以 WCAG-AA 作为基线。运行 `/gate-check` 查看是否阻塞任何阶段门禁。"

Then continue to the next section without stopping.
然后不停顿地继续下一部分。

---

#### Section H: Localization Considerations
#### H 部分：本地化考虑

Document constraints that affect how this screen behaves when text is translated.
记录影响文本翻译时此屏幕行为的约束。

**Questions to ask**:
- "Which text elements on this screen are the longest? What is the maximum character count that fits the layout?"
- "Are there any elements where text length is layout-critical — e.g., a button label that must stay on one line?"
- "Are there any elements that display numbers, dates, or currencies that need locale-specific formatting?"
**要问的问题**：
- "此屏幕上哪些文本元素最长？适合布局的最大字符数是多少？"
- "是否有任何元素文本长度对布局至关重要 — 例如，必须保持在一行的按钮标签？"
- "是否有任何元素显示需要区域特定格式的数字、日期或货币？"

Note: aim to flag any element where a 40% text expansion (common in translations from English to German or French) would break the layout. Mark those as HIGH PRIORITY for the localization engineer.
注意：目标是标记任何 40% 文本扩展（从英语到德语或法语翻译中常见）会破坏布局的元素。将这些标记为本地化工程师的高优先级。

---

#### Section I: Acceptance Criteria
#### I 部分：验收标准

Write at least 5 specific, testable criteria that a QA tester can verify without reading any other design document. These become the pass/fail conditions for `/story-done`.
编写至少 5 个 QA 测试员无需阅读任何其他设计文档即可验证的具体、可测试标准。这些成为 `/story-done` 的通过/失败条件。

**Format**: Use checkboxes. Each criterion must be verifiable by a human tester:
**格式**：使用复选框。每个标准必须可由人类测试员验证：

```
- [ ] Screen opens within [X]ms from [trigger]
- [ ] [Element] displays correctly at [minimum] and [maximum] values
- [ ] [Navigation action] correctly routes to [destination screen]
- [ ] Error state appears when [condition] and shows [specific message or icon]
- [ ] Keyboard/gamepad navigation reaches all interactive elements in logical order
- [ ] [Accessibility requirement] is met — e.g., "all interactive elements have focus indicators"
```

**Minimum required**:
- 1 performance criterion (load/open time)
- 1 navigation criterion (at least one entry or exit path verified)
- 1 error/empty state criterion
- 1 accessibility criterion (per committed tier)
- 1 criterion specific to this screen's core purpose
**最低要求**：
- 1 个性能标准（加载/打开时间）
- 1 个导航标准（至少验证一个入口或出口路径）
- 1 个错误/空状态标准
- 1 个无障碍标准（按承诺等级）
- 1 个此屏幕核心目的的特定标准

Use `AskUserQuestion` to confirm:
使用 `AskUserQuestion` 确认：

- "Do these acceptance criteria cover what would make this screen 'done' for your QA process?"
- Options: "Yes — these are solid", "Add one more criterion", "Remove or rephrase one"
- "这些验收标准是否涵盖了你的 QA 流程中此屏幕'完成'的条件？"
- 选项："是 — 这些很可靠"、"再添加一个标准"、"删除或重新表述一个"

---

### Section Guidance: HUD Design Mode
### 部分指导：HUD 设计模式

HUD design follows a different order from UX spec mode. Begin with philosophy;
do not touch layout until the information architecture is complete.
HUD 设计遵循与 UX 规范模式不同的顺序。从理念开始；
在信息架构完成之前不要触碰布局。

#### Section A: HUD Philosophy
#### A 部分：HUD 理念

Ask the user to describe the game's relationship with on-screen information in
1-2 sentences.
要求用户用 1-2 句话描述游戏与屏幕信息的关系。

Offer framing examples to help:
- "Nearly HUD-free — atmosphere requires unobstructed immersion (e.g., Hollow Knight, Firewatch)"
- "Minimal but present — only critical information visible, everything else contextual (e.g., Dark Souls)"
- "Information-dense — all decision-relevant data always visible (e.g., Diablo IV, StarCraft II)"
- "Adaptive — HUD density responds to combat state, exploration mode, menus (e.g., God of War)"
提供框架示例以帮助：
- "几乎无 HUD — 氛围需要不受阻碍的沉浸（例如，Hollow Knight、Firewatch）"
- "极简但存在 — 仅关键信息可见，其他均为上下文相关（例如，Dark Souls）"
- "信息密集 — 所有决策相关数据始终可见（例如，Diablo IV、StarCraft II）"
- "自适应 — HUD 密度响应战斗状态、探索模式、菜单（例如，God of War）"

This philosophy becomes the design constraint for every subsequent HUD decision.
If a proposed element conflicts with the stated philosophy, surface that conflict.
此理念成为每个后续 HUD 决策的设计约束。
如果提议的元素与声明的理念冲突，暴露该冲突。

---

#### Section B: Information Architecture
#### B 部分：信息架构

Complete this before any layout work. Do not skip it.
在任何布局工作之前完成此部分。不要跳过。

**Step 1 — Full information inventory**:
Pull all information from GDD UI Requirements sections gathered in Phase 2.
Present the full list: "These are all the things your game systems say they need
to communicate to the player on screen."
**步骤 1 — 完整信息清单**：
从阶段 2 收集的 GDD UI Requirements 部分提取所有信息。
呈现完整列表："这些是你的游戏系统表示需要
在屏幕上向玩家传达的所有内容。"

**Step 2 — Categorization**:
For each item, ask the user to categorize it:
**步骤 2 — 分类**：
对于每个条目，要求用户对其进行分类：

| Category | Description |
|----------|-------------|
| **Must Show** | Always visible, player needs it for core decisions |
| **Contextual** | Visible only when relevant (in combat, near interactable, etc.) |
| **On Demand** | Player must actively request it (toggle, hold button) |
| **Hidden** | Communicated through world/audio, never on-screen text |

| 类别 | 描述 |
|----------|-------------|
| **Must Show** | 始终可见，玩家需要它进行核心决策 |
| **Contextual** | 仅在相关时可见（战斗中、靠近可交互物等） |
| **On Demand** | 玩家必须主动请求（切换、长按按钮） |
| **Hidden** | 通过世界/音频传达，从不显示屏幕文本 |

Use `AskUserQuestion` to step through items in groups of 3-4, not all at once.
This is the most consequential design decision in the HUD — do not rush it.
使用 `AskUserQuestion` 以 3-4 个为一组分步处理条目，而非一次性全部。
这是 HUD 中最关键的设计决策 — 不要匆忙。

**Conflict check**: If the information philosophy (Section A) says "nearly HUD-free"
but the Must Show list is growing long, surface the conflict explicitly:
**冲突检查**：如果信息理念（A 部分）说"几乎无 HUD"
但 Must Show 列表越来越长，明确暴露冲突：

> "The current Must Show list has [N] items. That may conflict with the HUD-free
> philosophy. Options: reduce the Must Show list, revise the philosophy, or define
> a hybrid approach where HUD is absent in exploration and present in combat."
> "当前 Must Show 列表有 [N] 个条目。这可能与无 HUD
> 理念冲突。选项：减少 Must Show 列表、修订理念，或
> 定义混合方法：探索时无 HUD，战斗时有 HUD。"

---

#### Section C: Layout Zones
#### C 部分：布局区域

Only after the information architecture is approved, design layout zones.
仅在信息架构批准后，设计布局区域。

Base layout on:
- Which items are Must Show (they drive the permanent zone decisions)
- Where player attention naturally goes during gameplay (center-screen for action games,
  corners for strategy games)
- Platform and aspect ratio targets
布局基于：
- 哪些条目是 Must Show（它们驱动永久区域决策）
- 游戏过程中玩家注意力自然去往何处（动作游戏为屏幕中心，
  策略游戏为角落）
- 平台和宽高比目标

Offer 2-3 zone arrangements. Include rationale based on the HUD philosophy and the
categorization from Section B.
提供 2-3 种区域排列。包含基于 HUD 理念和
B 部分分类的理由。

---

#### Section D: HUD Elements
#### D 部分：HUD 元素

For each element in the layout, specify:
- Element name and category (Must Show / Contextual / On Demand)
- Content displayed
- Visual form (bar, number, icon, counter, map)
- Update behavior (real-time, event-driven, player-queried)
- Contextual trigger (if not always visible)
- Animation behavior (does it pulse when low? Fade in? Slam in?)
对于布局中的每个元素，指定：
- 元素名称和类别（Must Show / Contextual / On Demand）
- 显示的内容
- 视觉形式（条、数字、图标、计数器、地图）
- 更新行为（实时、事件驱动、玩家查询）
- 上下文触发器（如果不总是可见）
- 动画行为（低值时是否脉动？淡入？猛然出现？）

Work element by element. Reference the interaction pattern library if relevant patterns
exist for status displays, resource bars, or cooldown indicators.
逐个元素处理。如果相关模式存在于状态显示、资源条或冷却指示器，则引用交互模式库。

---

#### Sections E, F, G: Dynamic Behaviors, Platform Variants, Accessibility
#### E、F、G 部分：动态行为、平台变体、无障碍

These follow the same structure as the UX spec equivalents. See UX Spec section
guidance for D (States/Variants), E (Interactions), and G (Accessibility).
这些遵循与 UX 规范等效部分相同的结构。参见 UX 规范部分
指导中的 D（状态/变体）、E（交互）和 G（无障碍）。

For the HUD specifically, emphasize:
- Dynamic Behaviors: what causes the HUD to change density mid-gameplay?
- Platform Variants: does mobile/console require different element sizes or positions?
对于 HUD 特别强调：
- 动态行为：什么导致 HUD 在游戏过程中改变密度？
- 平台变体：移动/主机是否需要不同的元素大小或位置？

---

### Section Guidance: Interaction Pattern Library Mode
### 部分指导：交互模式库模式

Pattern library authoring is additive and catalog-driven, not linear.
模式库编写是增量和目录驱动的，非线性的。

#### Phase 1: Catalog Existing Patterns
#### 阶段 1：编目已有模式

Glob `design/ux/*.md` (excluding `interaction-patterns.md`) and read the Component
Inventory and Interaction Map sections of each spec. Extract every interaction
pattern used.
Glob `design/ux/*.md`（排除 `interaction-patterns.md`）并读取每个规范的组件
清单和交互地图部分。提取使用的每个交互
模式。

Present the extracted list: "Based on existing UX specs, these patterns are already
in use in the game:"
- [Pattern name]: used in [screen], [screen]
- [etc.]
呈现提取的列表："基于已有 UX 规范，这些模式已在游戏中使用："
- [模式名]：用于 [屏幕]、[屏幕]
- [等]

Ask: "Are there patterns you know exist but aren't in existing specs yet? List any
additional ones now."
询问："是否有你知道存在但尚未在已有规范中的模式？现在列出任何
额外的。"

---

#### Phase 2: Formalize Each Pattern
#### 阶段 2：正式化每个模式

For each pattern (existing or new), document:
对于每个模式（已有或新的），记录：

```markdown
### [Pattern Name]

**Category**: Navigation / Input / Feedback / Data Display / Modal / Overlay / [other]
**Used In**: [list of screens]

**Description**: [One paragraph explaining what this pattern is and when to use it]

**Specification**:
- [Component behavior]
- [Input mapping]
- [Visual/audio feedback]
- [Accessibility requirements for this pattern]

**When to Use**: [Conditions where this pattern is appropriate]
**When NOT to Use**: [Conditions where another pattern is more appropriate]

**Reference**: [Screenshot path or ASCII example, if available]
```

Work through patterns in groups. Use `AskUserQuestion`:
- "How do you want to work through these patterns?"
- Options: "Draft the first batch from existing specs (faster)", "Define them one by one (more control)", "Start with the most-used pattern first"
分组处理模式。使用 `AskUserQuestion`：
- "你想如何处理这些模式？"
- 选项："从已有规范中起草第一批（更快）"、"逐一定义（更可控）"、"从最常用的模式开始"

---

#### Phase 3: Identify Gaps
#### 阶段 3：识别缺口

After cataloging known patterns, ask:
编目已知模式后，询问：

- "Are there screens or interactions planned that would need patterns not yet
  in this library?"
- "Are there any patterns in existing specs that feel inconsistent with each
  other and should be consolidated?"
- "是否有计划中的屏幕或交互需要尚不在此库中的模式？"
- "是否有已有规范中的模式感觉彼此不一致
  应该合并？"

Document gaps in the Gaps section for follow-up.
在缺口部分记录缺口以便后续跟进。

---

## 5. Cross-Reference Check
## 5. 交叉引用检查

Before marking the spec as ready for review, run these checks:
在将规范标记为准备好评审之前，运行这些检查：

**1. GDD requirement coverage**: Does every GDD UI Requirement that references
this screen have a corresponding element in this spec? Present any gaps.
**1. GDD 需求覆盖**：引用此屏幕的每个 GDD UI Requirement
是否在此规范中有对应的元素？呈现任何缺口。

**2. Pattern library alignment**: Are all interaction patterns used in this spec
referenced by name? If a new pattern was invented during this spec session, flag
it for addition to the pattern library:
Use `AskUserQuestion`:
- "This spec uses [pattern name], which isn't in the pattern library yet. What should we do?"
- Options: "Add it to the pattern library now", "Flag it as a gap and continue", "Skip — this pattern is one-off"
**2. 模式库一致性**：此规范中使用的所有交互模式
是否按名称引用？如果在此规范会话期间发明了新模式，标记
它以便添加到模式库：
使用 `AskUserQuestion`：
- "此规范使用 [模式名]，它尚不在模式库中。我们该怎么办？"
- 选项："现在添加到模式库"、"标记为缺口并继续"、"跳过 — 此模式是一次性的"

**3. Navigation consistency**: Do the entry/exit points in this spec match the
navigation map in any related specs? Flag mismatches.
**3. 导航一致性**：此规范中的入口/出口点是否与
任何相关规范中的导航地图匹配？标记不匹配。

**4. Accessibility coverage**: Does the spec address the accessibility tier
committed to in `design/accessibility-requirements.md`? If not, flag open questions.
**4. 无障碍覆盖**：规范是否处理了
`design/accessibility-requirements.md` 中承诺的无障碍等级？如果没有，标记待解决问题。

**5. Empty states**: Does every data-dependent element have an empty state defined?
Flag any that don't.
**5. 空状态**：每个数据依赖元素是否定义了空状态？
标记任何未定义的。

Present the check results:
呈现检查结果：

> **Cross-Reference Check: [Screen Name]**
> - GDD requirements: [N of M covered / all covered]
> - New patterns to add to library: [list or "none"]
> - Navigation mismatches: [list or "none"]
> - Accessibility gaps: [list or "none"]
> - Missing empty states: [list or "none"]
> **交叉引用检查：[屏幕名]**
> - GDD 需求：[N of M 已覆盖 / 全部覆盖]
> - 要添加到库的新模式：[列表或"无"]
> - 导航不匹配：[列表或"无"]
> - 无障碍缺口：[列表或"无"]
> - 缺失空状态：[列表或"无"]

---

## 6. Handoff
## 6. 交接

When all sections are approved and written:
所有部分批准并写入后：

### 6a: Update Session State
### 6a：更新会话状态

Update `production/session-state/active.md` with:
- Task: [screen-name] UX spec
- Status: Complete (or In Review)
- File: design/ux/[filename].md
- Sections: All written
- Next: [suggestion]
更新 `production/session-state/active.md`：
- 任务：[屏幕名] UX 规范
- 状态：完成（或评审中）
- 文件：design/ux/[filename].md
- 部分：全部写入
- 下一步：[建议]

### 6b: Suggest Next Step
### 6b：建议下一步

Before presenting options, state clearly:
呈现选项之前，明确说明：

> "This spec should be validated with `/ux-review` before it enters the
> implementation pipeline. The Pre-Production gate requires all key screen specs
> to have a review verdict."
> "此规范在进入实施流水线之前应使用 `/ux-review` 验证。
> 前期制作门禁要求所有关键屏幕规范
> 有评审裁决。"

Then use `AskUserQuestion`:
然后使用 `AskUserQuestion`：

- "Run `/ux-review [filename]` now, or do something else first?"
  - Options:
    - "Run `/ux-review` now — validate this spec"
    - "Design another screen first, then review all specs together"
    - "Update the interaction pattern library with new patterns from this spec"
    - "Stop here for this session"
- "现在运行 `/ux-review [filename]`，还是先做别的？"
  - 选项：
    - "现在运行 `/ux-review` — 验证此规范"
    - "先设计另一个屏幕，然后一起评审所有规范"
    - "用此规范的新模式更新交互模式库"
    - "本次会话在此停止"

If the user picks "Design another screen first", add a note: "Reminder: run
`/ux-review` on all completed specs before running `/gate-check pre-production`."
如果用户选择"先设计另一个屏幕"，添加备注："提醒：运行
`/gate-check pre-production` 之前，在所有已完成的规范上运行
`/ux-review`。"

### 6c: Cross-Link Related Specs
### 6c：交叉链接相关规范

If other UX specs link to or from this screen, note which ones should reference
this spec. Do not edit those files without asking — just name them.
如果其他 UX 规范链接到或从此屏幕链接，注意哪些应该引用
此规范。不要在未询问的情况下编辑这些文件 — 仅命名。

---

## 7. Recovery & Resume
## 7. 恢复与续接

If the session is interrupted (compaction, crash, new session):
如果会话被中断（压缩、崩溃、新会话）：

1. Read `production/session-state/active.md` — it records the current screen
   and which sections are complete.
2. Read `design/ux/[filename].md` — sections with real content are done;
   sections with `[To be designed]` still need work.
3. Resume from the next incomplete section — no need to re-discuss completed ones.
1. 读取 `production/session-state/active.md` — 它记录当前屏幕
   和哪些部分已完成。
2. 读取 `design/ux/[filename].md` — 有实际内容的部分已完成；
   带有 `[To be designed]` 的部分仍需工作。
3. 从下一个未完成的部分续接 — 无需重新讨论已完成的部分。

This is why incremental writing matters: every approved section survives any
disruption.
这就是为什么增量写入很重要：每个批准的部分都能在任何
中断后保存。

---

## 8. Specialist Agent Routing
## 8. 专家智能体路由

This skill uses `ux-designer` as the primary agent (set in frontmatter). For
specific sub-topics, additional context or coordination may be needed:
此技能使用 `ux-designer` 作为主要智能体（在 frontmatter 中设置）。对于
特定子主题，可能需要额外上下文或协调：

| Topic | Coordinate with |
|-------|----------------|
| Visual aesthetics, color, layout feel | `art-director` — UX spec defines zones; art defines how they look |
| Implementation feasibility (engine constraints) | `ui-programmer` — before finalizing component inventory |
| Gameplay data requirements | `game-designer` — when data ownership is unclear |
| Narrative/lore visible in the UI | `narrative-director` — for flavor text, item names, lore panels |
| Accessibility tier decisions | Handled by this session — owned by ux-designer |
| 主题 | 协调对象 |
|-------|----------------|
| 视觉美学、色彩、布局感觉 | `art-director` — UX 规范定义区域；美术定义其外观 |
| 实施可行性（引擎约束） | `ui-programmer` — 在最终确定组件清单之前 |
| 游戏玩法数据需求 | `game-designer` — 当数据所有权不明确时 |
| UI 中可见的叙事/设定 | `narrative-director` — 用于风味文本、物品名称、设定面板 |
| 无障碍等级决策 | 由本次会话处理 — ux-designer 负责 |

When delegating to another agent via the Task tool:
- Provide: screen name, game concept summary, the specific question needing expert input
- The agent returns analysis to this session
- This session presents the agent's output to the user
- The user decides; this session writes to file
- Agents do NOT write to files directly — this session owns all file writes
通过 Task 工具委派给其他智能体时：
- 提供：屏幕名、游戏概念摘要、需要专家输入的具体问题
- 智能体将分析返回此会话
- 此会话向用户呈现智能体输出
- 用户决定；此会话写入文件
- 智能体不直接写入文件 — 此会话拥有所有文件写入

---

## Collaborative Protocol
## 协作协议

This skill follows the collaborative design principle at every step:
此技能在每个步骤遵循协作设计原则：

1. **Question -> Options -> Decision -> Draft -> Approval** for every section
2. **AskUserQuestion** at every decision point (Explain -> Capture pattern):
   - Phase 2: "Ready to start, or need more context?"
   - Phase 3: "May I create the skeleton?"
   - Phase 4 (each section): design questions, approach options, draft approval
   - Phase 5: "Run cross-reference check? What's next?"
3. **"May I write to [filepath]?"** before the skeleton and before each section write
4. **Incremental writing**: Each section is written to file immediately after approval
5. **Session state updates**: After every section write
1. **问题 -> 选项 -> 决策 -> 草稿 -> 批准** 用于每个部分
2. 在每个决策点使用 **AskUserQuestion**（解释 -> 捕获模式）：
   - 阶段 2："准备好开始了吗，还是需要更多上下文？"
   - 阶段 3："我可以创建骨架吗？"
   - 阶段 4（每个部分）：设计问题、方法选项、草稿批准
   - 阶段 5："运行交叉引用检查？下一步？"
3. 在骨架之前和每次部分写入之前使用 **"我可以写入 [filepath] 吗？"**
4. **增量写入**：每个部分在批准后立即写入文件
5. **会话状态更新**：每次部分写入后

**Aesthetic deference**: When layout or visual choices come down to personal taste,
present the options and ask. Do not select a layout because it is "standard" — always
confirm. The user is the creative director.
**美学尊重**：当布局或视觉选择归结为个人品味时，
呈现选项并询问。不要因为"标准"而选择布局 — 始终
确认。用户是创意总监。

**Conflict surfacing**: When a GDD requirement and the available screen real estate
conflict, surface the conflict and present resolution options. Never silently drop
a requirement. Never silently expand the layout without flagging it.
**冲突暴露**：当 GDD 需求和可用屏幕空间
冲突时，暴露冲突并呈现解决选项。永远不要默默丢弃
需求。永远不要在未标记的情况下默默扩展布局。

**Never** auto-generate the full spec and present it as a fait accompli.
**Never** write a section without user approval.
**Never** contradict an existing approved UX spec without flagging the conflict.
**Always** show where decisions come from (GDD requirements, player journey, user choices).
**永远不要**自动生成完整规范并将其作为既成事实呈现。
**永远不要**在未获用户批准的情况下写入部分。
**永远不要**在未标记冲突的情况下与已有批准的 UX 规范矛盾。
**始终**展示决策来源（GDD 需求、玩家旅程、用户选择）。

Verdict: **COMPLETE** — UX spec written and approved section by section.
裁决：**COMPLETE** — UX 规范逐节编写并批准。

---

## Recommended Next Steps
## 推荐后续步骤

- Run `/ux-review [filename]` to validate this spec before it enters the implementation pipeline
- Run `/ux-design [next-screen]` to continue designing remaining screens or flows
- Run `/gate-check pre-production` once all key screens have approved UX specs
- 运行 `/ux-review [filename]` 在进入实施流水线之前验证此规范
- 运行 `/ux-design [next-screen]` 继续设计剩余屏幕或流程
- 所有关键屏幕有批准的 UX 规范后运行 `/gate-check pre-production`
