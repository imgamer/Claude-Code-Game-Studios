---
name: start
description: "First-time onboarding — asks where you are, then guides you to the right workflow. No assumptions."
argument-hint: "[no arguments]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
---
---
名称：start
描述："首次入门 —— 询问您所处的阶段，然后引导您到正确的工作流。不做任何假设。"
argument-hint："[no arguments]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, AskUserQuestion
model：sonnet
---

# Guided Onboarding
# 引导式入门

This skill writes one file: `production/review-mode.txt` (review mode config set in Phase 3b).
此技能写入一个文件：`production/review-mode.txt`（评审模式配置在阶段 3b 中设定）。

This skill is the entry point for new users. It does NOT assume you have a game idea, an engine preference, or any prior experience. It asks first, then routes you to the right workflow.
此技能是新用户的入口点。它不假设您有游戏想法、引擎偏好或任何先前经验。它先询问，然后引导您到正确的工作流。

---

## Phase 1: Detect Project State
## 阶段 1：检测项目状态

Before asking anything, silently gather context so you can tailor your guidance. Do NOT show these results unprompted — they inform your recommendations, not the conversation opener.
在询问任何内容之前，静默收集上下文以便定制指导。不要未经提示显示这些结果 —— 它们为您的建议提供信息，而非对话开场白。

Check:
检查：
- **Engine configured?** Read `.claude/docs/technical-preferences.md`. If the Engine field contains `[TO BE CONFIGURED]`, the engine is not set.
- **引擎已配置？** 读取 `.claude/docs/technical-preferences.md`。若 Engine 字段包含 `[TO BE CONFIGURED]`，则引擎未设置。
- **Game concept exists?** Check for `design/gdd/game-concept.md`.
- **游戏概念存在？** 检查 `design/gdd/game-concept.md`。
- **Source code exists?** Glob for source files in `src/` (`*.gd`, `*.cs`, `*.cpp`, `*.h`, `*.rs`, `*.py`, `*.js`, `*.ts`).
- **源代码存在？** Glob `src/` 中的源文件（`*.gd`、`*.cs`、`*.cpp`、`*.h`、`*.rs`、`*.py`、`*.js`、`*.ts`）。
- **Prototypes exist?** Check for subdirectories in `prototypes/`.
- **原型存在？** 检查 `prototypes/` 中的子目录。
- **Design docs exist?** Count markdown files in `design/gdd/`.
- **设计文档存在？** 计数 `design/gdd/` 中的 markdown 文件。
- **Production artifacts?** Check for files in `production/sprints/` or `production/milestones/`.
- **生产产物？** 检查 `production/sprints/` 或 `production/milestones/` 中的文件。

Store these findings internally to validate the user's self-assessment and tailor recommendations.
将这些发现内部存储以验证用户的自我评估并定制建议。

---

## Phase 2: Ask Where the User Is
## 阶段 2：询问用户所处的阶段

This is the first thing the user sees. Use `AskUserQuestion` with these exact options so the user can click rather than type:
这是用户看到的第一件事。使用 `AskUserQuestion` 提供这些确切选项，以便用户点击而非输入：

- **Prompt**: "Welcome to Claude Code Game Studios! Before I suggest anything, I'd like to understand where you're starting from. Where are you at with your game idea right now?"
- **提示**："欢迎使用 Claude Code Game Studios！在我提出任何建议之前，我想了解您的起点。您目前对游戏想法处于什么阶段？"
- **Options**:
- **选项**：
  - `A) No idea yet` — I don't have a game concept at all. I want to explore and figure out what to make.
  - `A) 尚无想法` —— 我根本没有游戏概念。我想探索并弄清楚要做什么。
  - `B) Vague idea` — I have a rough theme, feeling, or genre in mind (e.g., "something with space" or "a cozy farming game") but nothing concrete.
  - `B) 模糊想法` —— 我脑海中有一个粗略的主题、感觉或类型（例如"与太空有关的东西"或"一个温馨的农场游戏"），但没有具体内容。
  - `C) Clear concept` — I know the core idea — genre, basic mechanics, maybe a pitch sentence — but haven't formalized it into documents yet.
  - `C) 清晰概念` —— 我知道核心想法 —— 类型、基本机制，也许还有一句推介 —— 但尚未将其形式化为文档。
  - `D) Existing work` — I already have design docs, prototypes, code, or significant planning done. I want to organize or continue the work.
  - `D) 现有工作` —— 我已有设计文档、原型、代码或大量规划。我想组织或继续工作。

Wait for the user's selection. Do not proceed until they respond.
等待用户选择。在他们响应之前不要继续。

---

## Phase 3: Route Based on Answer
## 阶段 3：基于回答路由

#### If A: No idea yet
#### 若 A：尚无想法

The user needs creative exploration before anything else.
用户需要先进行创意探索。

1. Acknowledge that starting from zero is completely fine
1. 承认从零开始完全没问题
2. Briefly explain what `/brainstorm` does (guided ideation using professional frameworks — MDA, player psychology, verb-first design). Mention that it has two modes: `/brainstorm open` for fully open exploration, or `/brainstorm [hint]` if they have even a vague theme (e.g., "space", "cozy", "horror").
2. 简要解释 `/brainstorm` 的作用（使用专业框架的引导式构思 —— MDA、玩家心理学、动词优先设计）。提及它有两种模式：`/brainstorm open` 用于完全开放探索，或 `/brainstorm [hint]` 如果他们有哪怕模糊的主题（例如 "space"、"cozy"、"horror"）。
3. Recommend running `/brainstorm open` as the next step, but invite them to use a hint if something comes to mind
3. 建议运行 `/brainstorm open` 作为下一步，但若想到什么邀请他们使用提示
4. Show the recommended path:
4. 显示推荐路径：
   **Concept phase:**
   **概念阶段：**
   - `/brainstorm open` — discover your game concept
   - `/brainstorm open` —— 发现您的游戏概念
   - `/setup-engine` — configure the engine (brainstorm will recommend one)
   - `/setup-engine` —— 配置引擎（brainstorm 会推荐一个）
   - `/prototype` — throwaway concept build: validate the core idea is fun before designing (1–3 days)
   - `/prototype` —— 一次性概念构建：在设计前验证核心想法是否有趣（1–3 天）
   - `/art-bible` — define visual identity (uses the Visual Identity Anchor brainstorm produces)
   - `/art-bible` —— 定义视觉身份（使用 brainstorm 生成的 Visual Identity Anchor）
   - `/map-systems` — decompose the concept into systems
   - `/map-systems` —— 将概念分解为系统
   - `/design-system` — author a GDD for each MVP system
   - `/design-system` —— 为每个 MVP 系统编写 GDD
   - `/review-all-gdds` — cross-system consistency check
   - `/review-all-gdds` —— 跨系统一致性检查
   - `/gate-check` — validate readiness before architecture work
   - `/gate-check` —— 验证架构工作前的就绪状态
   **Architecture phase:**
   **架构阶段：**
   - `/create-architecture` — produce the master architecture blueprint and Required ADR list
   - `/create-architecture` —— 生成主架构蓝图和必需的 ADR 列表
   - `/architecture-decision (×N)` — record key technical decisions, following the Required ADR list
   - `/architecture-decision (×N)` —— 按照必需的 ADR 列表记录关键技术决策
   - `/create-control-manifest` — compile decisions into an actionable rules sheet
   - `/create-control-manifest` —— 将决策编译为可执行的规则表
   - `/architecture-review` — validate architecture coverage
   - `/architecture-review` —— 验证架构覆盖范围
   **Pre-Production phase:**
   **预制作阶段：**
   - `/ux-design` — author UX specs for key screens (main menu, HUD, core interactions)
   - `/ux-design` —— 为关键屏幕编写 UX 规范（主菜单、HUD、核心交互）
   - `/vertical-slice` — production-quality end-to-end build to validate the full game loop
   - `/vertical-slice` —— 生产质量的端到端构建以验证完整游戏循环
   - `/playtest-report (×1+)` — document each vertical slice playtest session
   - `/playtest-report (×1+)` —— 记录每个垂直切片试玩会话
   - `/create-epics` — map systems to epics
   - `/create-epics` —— 将系统映射到史诗
   - `/create-stories` — break epics into implementable stories
   - `/create-stories` —— 将史诗拆分为可实施的故事
   - `/sprint-plan` — plan the first sprint
   - `/sprint-plan` —— 规划第一个冲刺
   **Production phase:** → pick up stories with `/dev-story`
   **制作阶段：** → 用 `/dev-story` 接手故事

#### If B: Vague idea
#### 若 B：模糊想法

1. Ask them to share their vague idea — even a few words is enough
1. 请他们分享模糊想法 —— 即使几个字也足够
2. Validate the idea as a starting point (don't judge or redirect)
2. 验证该想法作为起点（不要评判或重定向）
3. Recommend running `/brainstorm [their hint]` to develop it
3. 建议运行 `/brainstorm [their hint]` 来发展它
4. Show the recommended path:
4. 显示推荐路径：
   **Concept phase:**
   **概念阶段：**
   - `/brainstorm [hint]` — develop the idea into a full concept
   - `/brainstorm [hint]` —— 将想法发展为完整概念
   - `/setup-engine` — configure the engine
   - `/setup-engine` —— 配置引擎
   - `/prototype` — throwaway concept build: validate the core idea is fun before designing (1–3 days)
   - `/prototype` —— 一次性概念构建：在设计前验证核心想法是否有趣（1–3 天）
   - `/art-bible` — define visual identity (uses the Visual Identity Anchor brainstorm produces)
   - `/art-bible` —— 定义视觉身份（使用 brainstorm 生成的 Visual Identity Anchor）
   - `/map-systems` — decompose the concept into systems
   - `/map-systems` —— 将概念分解为系统
   - `/design-system` — author a GDD for each MVP system
   - `/design-system` —— 为每个 MVP 系统编写 GDD
   - `/review-all-gdds` — cross-system consistency check
   - `/review-all-gdds` —— 跨系统一致性检查
   - `/gate-check` — validate readiness before architecture work
   - `/gate-check` —— 验证架构工作前的就绪状态
   **Architecture phase:**
   **架构阶段：**
   - `/create-architecture` — produce the master architecture blueprint and Required ADR list
   - `/create-architecture` —— 生成主架构蓝图和必需的 ADR 列表
   - `/architecture-decision (×N)` — record key technical decisions, following the Required ADR list
   - `/architecture-decision (×N)` —— 按照必需的 ADR 列表记录关键技术决策
   - `/create-control-manifest` — compile decisions into an actionable rules sheet
   - `/create-control-manifest` —— 将决策编译为可执行的规则表
   - `/architecture-review` — validate architecture coverage
   - `/architecture-review` —— 验证架构覆盖范围
   **Pre-Production phase:**
   **预制作阶段：**
   - `/ux-design` — author UX specs for key screens (main menu, HUD, core interactions)
   - `/ux-design` —— 为关键屏幕编写 UX 规范（主菜单、HUD、核心交互）
   - `/vertical-slice` — production-quality end-to-end build to validate the full game loop
   - `/vertical-slice` —— 生产质量的端到端构建以验证完整游戏循环
   - `/playtest-report (×1+)` — document each vertical slice playtest session
   - `/playtest-report (×1+)` —— 记录每个垂直切片试玩会话
   - `/create-epics` — map systems to epics
   - `/create-epics` —— 将系统映射到史诗
   - `/create-stories` — break epics into implementable stories
   - `/create-stories` —— 将史诗拆分为可实施的故事
   - `/sprint-plan` — plan the first sprint
   - `/sprint-plan` —— 规划第一个冲刺
   **Production phase:** → pick up stories with `/dev-story`
   **制作阶段：** → 用 `/dev-story` 接手故事

#### If C: Clear concept
#### 若 C：清晰概念

1. Ask them to describe their concept in one sentence — genre and core mechanic. Use plain text, not AskUserQuestion (it's an open response).
1. 请他们用一句话描述概念 —— 类型和核心机制。使用纯文本，而非 AskUserQuestion（这是开放响应）。
2. Acknowledge the concept, then use `AskUserQuestion` to offer two paths:
2. 确认概念，然后使用 `AskUserQuestion` 提供两条路径：
   - **Prompt**: "How would you like to proceed?"
   - **提示**："您希望如何进行？"
   - **Options**:
   - **选项**：
     - `Formalize it first` — Run `/brainstorm [concept]` to structure it into a proper game concept document
     - `先形式化` —— 运行 `/brainstorm [concept]` 将其结构化为正式的游戏概念文档
     - `Jump straight in` — Go to `/setup-engine` now and write the GDD manually afterward
     - `直接开始` —— 现在进入 `/setup-engine`，之后手动编写 GDD
3. Show the recommended path:
3. 显示推荐路径：
   **Concept phase:**
   **概念阶段：**
   - `/brainstorm` or `/setup-engine` — (their pick from step 2)
   - `/brainstorm` 或 `/setup-engine` —— （他们在步骤 2 中的选择）
   - `/prototype` — throwaway concept build: validate the core idea is fun before designing (1–3 days)
   - `/prototype` —— 一次性概念构建：在设计前验证核心想法是否有趣（1–3 天）
   - `/art-bible` — define visual identity (after brainstorm if run, or after concept doc exists)
   - `/art-bible` —— 定义视觉身份（若运行则在 brainstorm 之后，或在概念文档存在后）
   - `/design-review` — validate the concept doc
   - `/design-review` —— 验证概念文档
   - `/map-systems` — decompose the concept into individual systems
   - `/map-systems` —— 将概念分解为单个系统
   - `/design-system` — author a GDD for each MVP system
   - `/design-system` —— 为每个 MVP 系统编写 GDD
   - `/review-all-gdds` — cross-system consistency check
   - `/review-all-gdds` —— 跨系统一致性检查
   - `/gate-check` — validate readiness before architecture work
   - `/gate-check` —— 验证架构工作前的就绪状态
   **Architecture phase:**
   **架构阶段：**
   - `/create-architecture` — produce the master architecture blueprint and Required ADR list
   - `/create-architecture` —— 生成主架构蓝图和必需的 ADR 列表
   - `/architecture-decision (×N)` — record key technical decisions, following the Required ADR list
   - `/architecture-decision (×N)` —— 按照必需的 ADR 列表记录关键技术决策
   - `/create-control-manifest` — compile decisions into an actionable rules sheet
   - `/create-control-manifest` —— 将决策编译为可执行的规则表
   - `/architecture-review` — validate architecture coverage
   - `/architecture-review` —— 验证架构覆盖范围
   **Pre-Production phase:**
   **预制作阶段：**
   - `/ux-design` — author UX specs for key screens (main menu, HUD, core interactions)
   - `/ux-design` —— 为关键屏幕编写 UX 规范（主菜单、HUD、核心交互）
   - `/vertical-slice` — production-quality end-to-end build to validate the full game loop
   - `/vertical-slice` —— 生产质量的端到端构建以验证完整游戏循环
   - `/playtest-report (×1+)` — document each vertical slice playtest session
   - `/playtest-report (×1+)` —— 记录每个垂直切片试玩会话
   - `/create-epics` — map systems to epics
   - `/create-epics` —— 将系统映射到史诗
   - `/create-stories` — break epics into implementable stories
   - `/create-stories` —— 将史诗拆分为可实施的故事
   - `/sprint-plan` — plan the first sprint
   - `/sprint-plan` —— 规划第一个冲刺
   **Production phase:** → pick up stories with `/dev-story`
   **制作阶段：** → 用 `/dev-story` 接手故事

#### If D: Existing work
#### 若 D：现有工作

1. Share what you found in Phase 1:
1. 分享您在阶段 1 中发现的：
   - "I can see you have [X source files / Y design docs / Z prototypes]..."
   - "我可以看到您有 [X 个源文件 / Y 个设计文档 / Z 个原型]..."
   - "Your engine is [configured as X / not yet configured]..."
   - "您的引擎 [已配置为 X / 尚未配置]..."

2. **Sub-case D1 — Early stage** (engine not configured or only a game concept exists):
2. **子情况 D1 —— 早期阶段**（引擎未配置或仅有游戏概念）：
   - Recommend `/setup-engine` first if engine not configured
   - 若引擎未配置，先建议 `/setup-engine`
   - Then `/project-stage-detect` for a gap inventory
   - 然后 `/project-stage-detect` 进行缺口盘点

   **Sub-case D2 — GDDs, ADRs, or stories already exist:**
   **子情况 D2 —— GDD、ADR 或故事已存在：**
   - Explain: "Having files isn't the same as the template's skills being able to use them. GDDs might be missing required sections. `/adopt` checks this specifically."
   - 解释："拥有文件不等于模板的技能能够使用它们。GDD 可能缺少必需章节。`/adopt` 专门检查这一点。"
   - Recommend:
   - 建议：
     1. `/project-stage-detect` — understand what phase and what's missing entirely
     1. `/project-stage-detect` —— 了解所处阶段及完全缺失的内容
     2. `/adopt` — audit whether existing artifacts are in the right internal format
     2. `/adopt` —— 审计现有产物是否为正确的内部格式

3. Show the recommended path for D2:
3. 显示 D2 的推荐路径：
   - `/project-stage-detect` — phase detection + existence gaps
   - `/project-stage-detect` —— 阶段检测 + 存在缺口
   - `/adopt` — format compliance audit + migration plan
   - `/adopt` —— 格式合规审计 + 迁移计划
   - `/setup-engine` — if engine not configured
   - `/setup-engine` —— 若引擎未配置
   - `/design-system retrofit [path]` — fill missing GDD sections
   - `/design-system retrofit [path]` —— 填充缺失的 GDD 章节
   - `/architecture-decision retrofit [path]` — add missing ADR sections
   - `/architecture-decision retrofit [path]` —— 添加缺失的 ADR 章节
   - `/architecture-review` — bootstrap the TR requirement registry
   - `/architecture-review` —— 引导 TR 需求注册表
   - `/gate-check` — validate readiness for next phase
   - `/gate-check` —— 验证下一阶段的就绪状态

---

## Phase 3c: Write Initial Stage File
## 阶段 3c：写入初始阶段文件

After confirming the starting path (and before asking about review mode), write the initial stage to `production/stage.txt`. Create the `production/` directory if it does not exist.
确认起始路径后（并在询问评审模式之前），将初始阶段写入 `production/stage.txt`。若 `production/` 目录不存在则创建它。

Stage mapping:
阶段映射：
- **Path A, B, or C (starting from scratch)**: write `Concept`
- **路径 A、B 或 C（从零开始）**：写入 `Concept`
- **Path D, existing project, engine not configured or only a game concept exists**: write `Concept`
- **路径 D，现有项目，引擎未配置或仅有游戏概念**：写入 `Concept`
- **Path D, existing project with GDDs but no architecture documents**: write `Systems Design`
- **路径 D，现有项目有 GDD 但无架构文档**：写入 `Systems Design`
- **Path D, existing project with full architecture (ADRs, architecture doc)**: write `Technical Setup`
- **路径 D，现有项目有完整架构（ADR、架构文档）**：写入 `Technical Setup`

Do this silently — no "May I write?" needed for this single-line file.
静默执行此操作 —— 此单行文件无需"我可以写入吗？"。

Say: "I've set `production/stage.txt` to `[stage]` — this anchors your status line and stage detection."
说："我已将 `production/stage.txt` 设为 `[stage]` —— 这锚定您的状态行和阶段检测。"

---

## Phase 3b: Set Review Mode
## 阶段 3b：设置评审模式

Check if `production/review-mode.txt` already exists.
检查 `production/review-mode.txt` 是否已存在。

**If it exists**: Read it and show the current mode — "Review mode is set to `[current]`." — then proceed to Phase 4. Do not ask again.
**若已存在**：读取它并显示当前模式 —— "评审模式设置为 `[current]`。" —— 然后进入阶段 4。不要再次询问。

**If it does not exist**: Use `AskUserQuestion`:
**若不存在**：使用 `AskUserQuestion`：

- **Prompt**: "One setup choice: how much design review would you want as you work through the workflow?"
- **提示**："一个设置选择：您在通过工作流工作时希望有多少设计评审？"
- **Options**:
- **选项**：
  - `Full` — Director specialists review at each key workflow step. Best for teams, learning the workflow, or when you want thorough feedback on every decision.
  - `Full` —— 总监专家在每个关键工作流步骤评审。最适合团队、学习工作流，或当您希望对每个决策获得彻底反馈时。
  - `Lean (recommended)` — Directors only at phase gate transitions (/gate-check). Skips per-skill reviews. Balanced approach for solo devs and small teams.
  - `Lean（推荐）` —— 总监仅在阶段门禁转换时（/gate-check）。跳过每个技能的评审。适合独立开发者和小型团队的平衡方法。
  - `Solo` — No director reviews at all. Maximum speed. Best for game jams, prototypes, or if the reviews feel like overhead.
  - `Solo` —— 完全无总监评审。最大速度。适合游戏 jam、原型，或当评审感觉是开销时。

Write the choice to `production/review-mode.txt` immediately after the user
selects — no separate "May I write?" needed, as the write is a direct
consequence of the selection:
用户选择后立即将选择写入 `production/review-mode.txt`
—— 无需单独的"我可以写入吗？"，因为写入是选择的直接
结果：
- `Full` → write `full`
- `Full` → 写入 `full`
- `Lean (recommended)` → write `lean`
- `Lean（推荐）` → 写入 `lean`
- `Solo` → write `solo`
- `Solo` → 写入 `solo`

Create the `production/` directory if it does not exist.
若 `production/` 目录不存在则创建它。

---

## Phase 4: Confirm Before Proceeding
## 阶段 4：继续前确认

After presenting the recommended path, use `AskUserQuestion` to ask the user which step they'd like to take first. Never auto-run the next skill.
呈现推荐路径后，使用 `AskUserQuestion` 询问用户想先采取哪个步骤。绝不自动运行下一个技能。

- **Prompt**: "Would you like to start with [recommended first step]?"
- **提示**："您想从 [推荐的第一个步骤] 开始吗？"
- **Options**:
- **选项**：
  - `Yes, let's start with [recommended first step]`
  - `是，让我们从 [推荐的第一个步骤] 开始`
  - `I'd like to do something else first`
  - `我想先做其他事`

---

## Phase 5: Hand Off
## 阶段 5：交接

When the user confirms their next step, respond with a single short line: "Type `[skill command]` to begin." Nothing else. Do not re-explain the skill or add encouragement. The `/start` skill's job is done.
当用户确认其下一步时，用一行简短文字响应："输入 `[skill command]` 开始。"仅此而已。不要重新解释技能或添加鼓励。`/start` 技能的工作已完成。

Verdict: **COMPLETE** — user oriented and handed off to next step.
裁定：**COMPLETE** —— 用户已定向并交接给下一步。

---

## Edge Cases
## 边缘情况

- **User picks D but project is empty**: Gently redirect — "It looks like the project is a fresh template with no artifacts yet. Would Path A or B be a better fit?"
- **用户选择 D 但项目为空**：温和地重定向 —— "看起来项目是没有任何产物的新模板。路径 A 或 B 是否更合适？"
- **User picks A but project has code**: Mention what you found — "I noticed there's already code in `src/`. Did you mean to pick D (existing work)?"
- **用户选择 A 但项目已有代码**：提及您的发现 —— "我注意到 `src/` 中已有代码。您是想选 D（现有工作）吗？"
- **User is returning (engine configured, concept exists)**: Skip onboarding entirely — "It looks like you're already set up! Your engine is [X] and you have a game concept at `design/gdd/game-concept.md`. Review mode: `[read from production/review-mode.txt, or 'lean (default)' if missing]`. Want to pick up where you left off? Try `/sprint-plan` or just tell me what you'd like to work on."
- **用户回归（引擎已配置，概念已存在）**：完全跳过入门 —— "看起来您已经设置好了！您的引擎是 [X]，且您在 `design/gdd/game-concept.md` 有游戏概念。评审模式：`[从 production/review-mode.txt 读取，若缺失则为 'lean (default)']`。想从上次中断的地方继续吗？尝试 `/sprint-plan` 或直接告诉我您想做什么。"
- **User doesn't fit any option**: Let them describe their situation in their own words and adapt.
- **用户不适合任何选项**：让他们用自己的话描述情况并适应。

---

## Collaborative Protocol
## 协作协议

1. **Ask first** — never assume the user's state or intent
1. **先询问** —— 绝不假设用户的状态或意图
2. **Present options** — give clear paths, not mandates
2. **呈现选项** —— 给出清晰路径，而非命令
3. **User decides** — they pick the direction
3. **用户决定** —— 他们选择方向
4. **No auto-execution** — recommend the next skill, don't run it without asking
4. **不自动执行** —— 建议下一个技能，不要未经询问就运行它
5. **Adapt** — if the user's situation doesn't fit a template, listen and adjust
5. **适应** —— 若用户的情况不适合模板，倾听并调整
