---
name: art-bible
description: "Guided, section-by-section Art Bible authoring. Creates the visual identity specification that gates all asset production. Run after /brainstorm is approved and before /map-systems or any GDD authoring begins."
argument-hint: "[--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---
---
name: art-bible
description: "引导式、逐章撰写的美术风格指南编写。创建门禁所有资产制作的视觉标识规格。在 /brainstorm 获批准后、/map-systems 或任何 GDD 编写开始前运行。"
argument-hint: "[--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---

## Phase 0: Parse Arguments and Context Check

Resolve the review mode (once, store for all gate spawns this run):
1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`

See `.claude/docs/director-gates.md` for the full check pattern.

Read `design/gdd/game-concept.md`. If it does not exist, fail with:
> "No game concept found. Run `/brainstorm` first — the art bible is authored after the game concept is approved."

Extract from game-concept.md:
- Game title (working title)
- Core fantasy and elevator pitch
- Game pillars (all of them)
- **Visual Identity Anchor** section if present (from brainstorm Phase 4 art-director output)
- Target platform (if noted)

**Retrofit mode detection**: Glob `design/art/art-bible.md`. If the file exists:
- Read it in full
- For each of the 9 sections, check whether the body contains real content (more than a `[To be designed]` placeholder or similar) vs. is empty/placeholder
- Build a section status table:

```
Section | Status
--------|--------
1. Visual Identity Statement | [Complete / Empty / Placeholder]
2. Color Palette | ...
3. Lighting & Atmosphere | ...
4. Character Art Direction | ...
5. Environment & Level Art | ...
6. UI Visual Language | ...
7. VFX & Particle Style | ...
8. Asset Standards | ...
9. Style Prohibitions | ...
```

- Present this table to the user:
  > "Found existing art bible at `design/art/art-bible.md`. [N] sections are complete, [M] need content. I'll work on the incomplete sections only — existing content will not be touched."
- Only work on sections with Status: Empty or Placeholder. Do not re-author sections that are already complete.

If the file does not exist, this is a fresh authoring session — proceed normally.

Read `.claude/docs/technical-preferences.md` if it exists — extract performance budgets and engine for asset standard constraints.

## 第 0 阶段：解析参数和上下文检查

解析评审模式（仅一次，保存供本次运行所有门禁派生使用）：
1. 如传入 `--review [full|lean|solo]` → 使用该值
2. 否则读取 `production/review-mode.txt` → 使用该值
3. 否则 → 默认为 `lean`

完整检查模式见 `.claude/docs/director-gates.md`。

读取 `design/gdd/game-concept.md`。如不存在，失败并显示：
> "No game concept found. Run `/brainstorm` first — the art bible is authored after the game concept is approved."（未找到游戏概念。先运行 `/brainstorm` —— 美术风格指南在游戏概念获批准后编写。）

从 game-concept.md 提取：
- 游戏标题（工作标题）
- 核心幻想和电梯演讲
- 游戏支柱（全部）
- **Visual Identity Anchor** 章节（如存在，来自头脑风暴第 4 阶段艺术总监输出）
- 目标平台（如注明）

**改造模式检测**：Glob `design/art/art-bible.md`。如文件存在：
- 完整读取它
- 对 9 个章节的每一个，检查正文是否包含真实内容（多于 `[To be designed]` 占位符或类似）vs. 为空/占位符
- 构建章节状态表：

```
Section | Status
--------|--------
1. Visual Identity Statement | [Complete / Empty / Placeholder]
2. Color Palette | ...
3. Lighting & Atmosphere | ...
4. Character Art Direction | ...
5. Environment & Level Art | ...
6. UI Visual Language | ...
7. VFX & Particle Style | ...
8. Asset Standards | ...
9. Style Prohibitions | ...
```

- 向用户呈现此表：
  > "Found existing art bible at `design/art/art-bible.md`. [N] sections are complete, [M] need content. I'll work on the incomplete sections only — existing content will not be touched."（在 `design/art/art-bible.md` 找到现有美术风格指南。[N] 个章节完整，[M] 个需要内容。我将仅处理不完整章节 —— 现有内容不会被触碰。）
- 仅处理状态为 Empty 或 Placeholder 的章节。不要重新编写已完整的章节。

如文件不存在，这是全新的编写会话 —— 正常进行。

如 `.claude/docs/technical-preferences.md` 存在则读取它 —— 提取性能预算和引擎用于资产标准约束。

---

## Phase 1: Framing

Present the session context and ask two questions before authoring anything:

Use `AskUserQuestion` with two tabs:
- Tab **"Scope"** — "Which sections need to be authored today?"
  Options: `Full bible — all 9 sections` / `Visual identity core (sections 1–4 only)` / `Asset standards only (section 8)` / `Resume — fill in missing sections`
- Tab **"References"** — "Do you have reference games, films, or art that define the visual direction?"
  (Free text — let the user type specific titles. Do NOT preset options here.)

If the game-concept.md has a Visual Identity Anchor section, note it:
> "Found a visual identity anchor from brainstorm: '[anchor name] — [one-line rule]'. I'll use this as the foundation for the art bible."

## 第 1 阶段：框架

呈现会话上下文并在编写任何内容前问两个问题：

使用 `AskUserQuestion` 带两个标签页：
- 标签页 **"Scope"** —— "今天需要编写哪些章节？"
  选项：`Full bible — all 9 sections` / `Visual identity core (sections 1–4 only)` / `Asset standards only (section 8)` / `Resume — fill in missing sections`
- 标签页 **"References"** —— "你有定义视觉方向的参考游戏、电影或艺术作品吗？"
  （自由文本 —— 让用户键入具体标题。不要在此预设选项。）

如 game-concept.md 有 Visual Identity Anchor 章节，注明：
> "Found a visual identity anchor from brainstorm: '[anchor name] — [one-line rule]'. I'll use this as the foundation for the art bible."（从头脑风暴找到视觉标识锚点：'[锚点名称] —— [一行规则]'。我将以此作为美术风格指南的基础。）

---

## Phase 2: Visual Identity Foundation (Sections 1–4)

These four sections define the core visual language. **All other sections flow from them.** Author and write each to file before moving to the next.

### Section 1: Visual Identity Statement

**Goal**: A one-line visual rule plus 2–3 supporting principles that resolve visual ambiguity.

If a visual anchor exists from game-concept.md: present it and ask:
- "Build directly from this anchor?"
- "Revise it before expanding?"
- "Start fresh with new options?"

**Agent delegation (MANDATORY)**: Spawn `art-director` via Task:
- Provide: game concept (elevator pitch, core fantasy), full pillar set, platform target, any reference games/art from Phase 1 framing, the visual anchor if it exists
- Ask: "Draft a Visual Identity Statement for this game. Provide: (1) a one-line visual rule that could resolve any visual decision ambiguity, (2) 2–3 supporting visual principles, each with a one-sentence design test ('when X is ambiguous, this principle says choose Y'). Anchor all principles directly in the stated pillars — each principle must serve a specific pillar."

Present the art-director's draft to the user. Use `AskUserQuestion`:
- Options: `[A] Lock this in` / `[B] Revise the one-liner` / `[C] Revise a supporting principle` / `[D] Describe my own direction`

Write the approved section to file immediately.

### Section 2: Mood & Atmosphere

**Goal**: Emotional targets by game state — specific enough for a lighting artist to work from.

For each major game state (e.g., exploration, combat, victory, defeat, menus — adapt to this game's states), define:
- Primary emotion/mood target
- Lighting character (time of day, color temperature, contrast level)
- Atmospheric descriptors (3–5 adjectives)
- Energy level (frenetic / measured / contemplative / etc.)

**Agent delegation**: Spawn `art-director` via Task with the Visual Identity Statement and pillar set. Ask: "Define mood and atmosphere targets for each major game state in this game. Be specific — 'dark and foreboding' is not enough. Name the exact emotional target, the lighting character (warm/cool, high/low contrast, time of day direction), and at least one visual element that carries the mood. Each game state must feel visually distinct from the others."

Write the approved section to file immediately.

### Section 3: Shape Language

**Goal**: The geometric vocabulary that makes this game's world visually coherent and distinguishable.

Cover:
- Character silhouette philosophy (how readable at thumbnail size? Distinguishing trait per archetype?)
- Environment geometry (angular/curved/organic/geometric — which dominates and why?)
- UI shape grammar (does UI echo the world aesthetic, or is it a distinct HUD language?)
- Hero shapes vs. supporting shapes (what draws the eye, what recedes?)

**Agent delegation**: Spawn `art-director` via Task with Visual Identity Statement and mood targets. Ask: "Define the shape language for this game. Connect each shape principle back to the visual identity statement and a specific game pillar. Explain what these shape choices communicate to the player emotionally."

Write the approved section to file immediately.

### Section 4: Color System

**Goal**: A complete, producible palette system that serves both aesthetic and communication needs.

Cover:
- Primary palette (5–7 colors with roles — not just hex codes, but what each color means in this world)
- Semantic color usage (what does red communicate? Gold? Blue? White? Establish the color vocabulary)
- Per-biome or per-area color temperature rules (if the game has distinct areas)
- UI palette (may differ from world palette — define the divergence explicitly)
- Colorblind safety: which semantic colors need shape/icon/sound backup

**Agent delegation**: Spawn `art-director` via Task with Visual Identity Statement and mood targets. Ask: "Design the color system for this game. Every semantic color assignment must be explained — why does this color mean danger/safety/reward in this world? Identify which color pairs might fail colorblind players and specify what backup cues are needed."

Write the approved section to file immediately.

## 第 2 阶段：视觉标识基础（第 1-4 章）

这四章定义核心视觉语言。**所有其他章节都从中衍生。** 每章编写并写入文件后再进入下一章。

### 第 1 章：视觉标识陈述

**目标**：一行视觉规则加 2-3 条解决视觉歧义的支持性原则。

如 game-concept.md 中存在视觉锚点：呈现它并问：
- "直接从此锚点构建？"
- "扩展前修订它？"
- "用新选项重新开始？"

**智能体委派（强制）**：通过 Task 派生 `art-director`：
- 提供：游戏概念（电梯演讲、核心幻想）、完整支柱集、平台目标、第 1 阶段框架中的任何参考游戏/艺术、视觉锚点（如存在）
- 询问："为此游戏起草 Visual Identity Statement。提供：(1) 一行能解决任何视觉决策歧义的视觉规则，(2) 2-3 条支持性视觉原则，每条带一句设计测试（'当 X 不明确时，此原则说选择 Y'）。将所有原则直接锚定在所述支柱上 —— 每条原则必须服务于特定支柱。"

向用户呈现艺术总监的草稿。使用 `AskUserQuestion`：
- 选项：`[A] 锁定此版本` / `[B] 修订一行规则` / `[C] 修订某条支持性原则` / `[D] 描述我自己的方向`

立即将批准的章节写入文件。

### 第 2 章：氛围与气息

**目标**：按游戏状态的情感目标 —— 足够具体让灯光师可据以工作。

对每个主要游戏状态（例如探索、战斗、胜利、失败、菜单 —— 适配此游戏的状态），定义：
- 主要情感/氛围目标
- 灯光特性（一天中的时间、色温、对比度水平）
- 氛围描述词（3-5 个形容词）
- 能量水平（狂热 / 节制 / 沉思 / 等）

**智能体委派**：通过 Task 派生 `art-director`，传入 Visual Identity Statement 和支柱集。询问："定义此游戏每个主要游戏状态的氛围和气息目标。要具体 —— '黑暗且阴森'不够。命名确切的情感目标、灯光特性（暖/冷、高/低对比度、一天中的时间方向），以及至少一个承载氛围的视觉元素。每个游戏状态必须在视觉上与其他状态区别明显。"

立即将批准的章节写入文件。

### 第 3 章：形状语言

**目标**：使此游戏世界在视觉上连贯且可区分的几何词汇。

覆盖：
- 角色剪影哲学（缩略图大小下可读性如何？每个原型有何区分特征？）
- 环境几何（棱角/曲线/有机/几何 —— 哪个主导及为何？）
- UI 形状语法（UI 是否呼应世界美学，还是独立的 HUD 语言？）
- 主体形状 vs. 支撑形状（什么吸引眼球，什么退后？）

**智能体委派**：通过 Task 派生 `art-director`，传入 Visual Identity Statement 和氛围目标。询问："定义此游戏的形状语言。将每条形状原则与视觉标识陈述和具体游戏支柱联系起来。解释这些形状选择向玩家传达了什么情感。"

立即将批准的章节写入文件。

### 第 4 章：色彩系统

**目标**：完整、可生产的调色板系统，服务于美学和沟通需求。

覆盖：
- 主调色板（5-7 种颜色及其角色 —— 不仅是十六进制代码，而是每种颜色在此世界中的含义）
- 语义色彩使用（红色传达什么？金色？蓝色？白色？建立色彩词汇）
- 每个生物群系或区域的色温规则（如游戏有不同区域）
- UI 调色板（可能与世界调色板不同 —— 显式定义分歧）
- 色盲安全：哪些语义颜色需要形状/图标/声音备份

**智能体委派**：通过 Task 派生 `art-director`，传入 Visual Identity Statement 和氛围目标。询问："设计此游戏的色彩系统。每个语义色彩分配都必须解释 —— 为什么此颜色在此世界代表危险/安全/奖励？识别哪些颜色对可能让色盲玩家失败，并指定需要什么备份提示。"

立即将批准的章节写入文件。

---

## Phase 3: Production Guides (Sections 5–8)

These sections translate the visual identity into concrete production rules. They should be specific enough that an outsourcing team can follow them without additional briefing.

### Section 5: Character Design Direction

**Agent delegation**: Spawn `art-director` via Task with sections 1–4. Ask: "Define character design direction for this game. Cover: visual archetype for the player character (if any), distinguishing feature rules per character type (how do players tell enemies/NPCs/allies apart at a glance?), expression/pose style targets (stiff/expressive/realistic/exaggerated), and LOD philosophy (how much detail is preserved at game camera distance?)."

Write the approved section to file.

### Section 6: Environment Design Language

**Agent delegation**: Spawn `art-director` via Task with sections 1–4. Ask: "Define the environment design language for this game. Cover: architectural style and its relationship to the world's culture/history, texture philosophy (painted vs. PBR vs. stylized — why this choice for this game?), prop density rules (sparse/dense — what drives the choice per area type?), and environmental storytelling guidelines (what visual details should tell the story without text?)."

Write the approved section to file.

### Section 7: UI/HUD Visual Direction

**Agent delegation**: Spawn in parallel:
- **`art-director`**: Visual style for UI — diegetic vs. screen-space HUD, typography direction (font personality, weight, size hierarchy), iconography style (flat/outlined/illustrated/photorealistic), animation feel for UI elements
- **`ux-designer`**: UX alignment check — does the visual direction support the interaction patterns this game requires? Flag any conflicts between art direction and readability/accessibility needs.

Collect both. If they conflict (e.g., art-director wants elaborate diegetic UI but ux-designer flags it would reduce combat readability), surface the conflict explicitly with both positions. Do NOT silently resolve — use `AskUserQuestion` to let the user decide.

Write the approved section to file.

### Section 8: Asset Standards

**Agent delegation**: Spawn in parallel:
- **`art-director`**: File format preferences, naming convention direction, texture resolution tiers, LOD level expectations, export settings philosophy
- **`technical-artist`**: Engine-specific hard constraints — poly count budgets per asset category, texture memory limits, material slot counts, importer constraints, anything from the performance budgets in `.claude/docs/technical-preferences.md`

If any art preference conflicts with a technical constraint (e.g., art-director wants 4K textures but performance budget requires 2K for mobile), resolve the conflict explicitly — note both the ideal and the constrained standard, and explain the tradeoff. Ambiguity in asset standards is where production costs are born.

Write the approved section to file.

## 第 3 阶段：制作指南（第 5-8 章）

这些章节将视觉标识翻译为具体制作规则。它们应足够具体，让外包团队无需额外说明即可遵循。

### 第 5 章：角色设计方向

**智能体委派**：通过 Task 派生 `art-director`，传入第 1-4 章。询问："定义此游戏的角色设计方向。覆盖：玩家角色的视觉原型（如有）、每种角色类型的区分特征规则（玩家如何一眼区分敌人/NPC/盟友？）、表情/姿态风格目标（僵硬/富有表现力/写实/夸张），以及 LOD 哲学（游戏相机距离下保留多少细节？）。"

将批准的章节写入文件。

### 第 6 章：环境设计语言

**智能体委派**：通过 Task 派生 `art-director`，传入第 1-4 章。询问："定义此游戏的环境设计语言。覆盖：建筑风格及其与世界文化/历史的关系、贴图哲学（手绘 vs. PBR vs. 风格化 —— 为何此游戏选此选择？）、道具密度规则（稀疏/密集 —— 每种区域类型的选择由什么驱动？），以及环境叙事指南（哪些视觉细节应无文字地讲述故事？）。"

将批准的章节写入文件。

### 第 7 章：UI/HUD 视觉方向

**智能体委派**：并行派生：
- **`art-director`**：UI 视觉风格 —— 戏剧化 vs. 屏幕空间 HUD、排版方向（字体个性、字重、字号层级）、图标风格（扁平/描边/插画/照片级）、UI 元素动画感觉
- **`ux-designer`**：UX 对齐检查 —— 视觉方向是否支持此游戏所需的交互模式？标记艺术方向与可读性/可访问性需求之间的任何冲突。

收集两者。如它们冲突（例如艺术总监要精致的戏剧化 UI 但 UX 设计师标记这会降低战斗可读性），显式呈现冲突并附双方立场。不要静默解决 —— 使用 `AskUserQuestion` 让用户决定。

将批准的章节写入文件。

### 第 8 章：资产标准

**智能体委派**：并行派生：
- **`art-director`**：文件格式偏好、命名约定方向、贴图分辨率层级、LOD 级别期望、导出设置哲学
- **`technical-artist`**：引擎特定硬约束 —— 每个资产类别的多边形数预算、贴图内存限制、材质槽位数、导入器约束、`.claude/docs/technical-preferences.md` 性能预算中的任何内容

如任何艺术偏好与技术约束冲突（例如艺术总监要 4K 贴图但性能预算要求移动端 2K），显式解决冲突 —— 注明理想标准和受限标准，并解释权衡。资产标准中的歧义是生产成本产生之处。

将批准的章节写入文件。

---

## Phase 4: Reference Direction (Section 9)

**Goal**: A curated reference set that is specific about what to take and what to avoid from each source.

**Agent delegation**: Spawn `art-director` via Task with the completed sections 1–8. Ask: "Compile a reference direction for this game. Provide 3–5 reference sources (games, films, art styles, or specific artists). For each: name it, specify exactly what visual element to draw from it (not 'the general aesthetic' — a specific technique, color choice, or compositional rule), and specify what to explicitly avoid or diverge from (to prevent the 'trying to copy X' reading). References should be additive — no two references should be pointing in exactly the same direction."

Write the approved section to file.

## 第 4 阶段：参考方向（第 9 章）

**目标**：策划的参考集，对从每个来源取什么和避什么具体说明。

**智能体委派**：通过 Task 派生 `art-director`，传入已完成的第 1-8 章。询问："为此游戏编制参考方向。提供 3-5 个参考来源（游戏、电影、艺术风格或具体艺术家）。对每个：命名它，具体说明从中提取什么视觉元素（不是'整体美学' —— 具体技术、色彩选择或构图规则），并具体说明要明确避免或偏离什么（以防止'试图复制 X'的解读）。参考应是叠加性的 —— 任何两个参考不应指向完全相同的方向。"

将批准的章节写入文件。

---

## Phase 5: Art Director Sign-Off

**Review mode check** — apply before spawning AD-ART-BIBLE:
- `solo` → skip. Note: "AD-ART-BIBLE skipped — Solo mode." Proceed to Phase 6.
- `lean` → skip (not a PHASE-GATE). Note: "AD-ART-BIBLE skipped — Lean mode." Proceed to Phase 6.
- `full` → spawn as normal.

After all sections are complete (or the scoped set from Phase 1 is complete), spawn `creative-director` via Task using gate **AD-ART-BIBLE** (`.claude/docs/director-gates.md`).

Pass: art bible file path, game pillars, visual identity anchor.

Handle verdict per standard rules in `director-gates.md`. Record the verdict in the art bible's status header:
`> **Art Director Sign-Off (AD-ART-BIBLE)**: APPROVED [date] / CONCERNS (accepted) [date] / REVISED [date]`

## 第 5 阶段：艺术总监签署

**评审模式检查** —— 在派生 AD-ART-BIBLE 前应用：
- `solo` → 跳过。注："AD-ART-BIBLE skipped — Solo mode." 进入第 6 阶段。
- `lean` → 跳过（非 PHASE-GATE）。注："AD-ART-BIBLE skipped — Lean mode." 进入第 6 阶段。
- `full` → 正常派生。

所有章节完成（或第 1 阶段的范围集完成）后，通过 Task 使用门禁 **AD-ART-BIBLE**（`.claude/docs/director-gates.md`）派生 `creative-director`。

传递：美术风格指南文件路径、游戏支柱、视觉标识锚点。

按 `director-gates.md` 中的标准规则处理判定。在美术风格指南的状态头部记录判定：
`> **Art Director Sign-Off (AD-ART-BIBLE)**: APPROVED [date] / CONCERNS (accepted) [date] / REVISED [date]`

---

## Phase 6: Close

Before presenting next steps, check project state:
- Does `design/gdd/systems-index.md` exist? → map-systems is done, skip that option
- Does `.claude/docs/technical-preferences.md` contain a configured engine (not `[TO BE CONFIGURED]`)? → setup-engine is done, skip that option
- Does `design/gdd/` contain any `*.md` files? → design-system has been run, skip that option
- Does `design/gdd/gdd-cross-review-*.md` exist? → review-all-gdds is done
- Do GDDs exist (check above)? → include /consistency-check option

Use `AskUserQuestion` for next steps. Only include options that are genuinely next based on the state check above:

**Option pool — include only if not already done:**
- `[_] Run /map-systems — decompose the concept into systems before writing GDDs` (skip if systems-index.md exists)
- `[_] Run /setup-engine — configure the engine (asset standards may need revisiting after engine is set)` (skip if engine configured)
- `[_] Run /design-system — start the first GDD` (skip if any GDDs exist)
- `[_] Run /review-all-gdds — cross-GDD consistency check (required before Technical Setup gate)` (skip if gdd-cross-review-*.md exists)
- `[_] Run /asset-spec — generate per-asset visual specs and AI generation prompts from approved GDDs` (include if GDDs exist)
- `[_] Run /consistency-check — scan existing GDDs against the art bible for visual direction conflicts` (include if GDDs exist)
- `[_] Run /create-architecture — author the master architecture document (next Technical Setup step)`
- `[_] Stop here`

Assign letters A, B, C… only to the options actually included. Mark the most logical pipeline-advancing option as `(recommended)`.

> **Always include** `/create-architecture` and Stop here as options — these are always valid next steps once the art bible is complete.

## 第 6 阶段：收尾

呈现下一步前，检查项目状态：
- `design/gdd/systems-index.md` 是否存在？→ map-systems 已完成，跳过该选项
- `.claude/docs/technical-preferences.md` 是否包含已配置引擎（非 `[TO BE CONFIGURED]`）？→ setup-engine 已完成，跳过该选项
- `design/gdd/` 是否包含任何 `*.md` 文件？→ design-system 已运行，跳过该选项
- `design/gdd/gdd-cross-review-*.md` 是否存在？→ review-all-gdds 已完成
- GDD 是否存在（上面检查）？→ 包含 /consistency-check 选项

使用 `AskUserQuestion` 处理下一步。仅包含基于上面状态检查真正是下一步的选项：

**选项池 —— 仅在尚未完成时包含：**
- `[_] Run /map-systems — decompose the concept into systems before writing GDDs`（如 systems-index.md 存在则跳过）
- `[_] Run /setup-engine — configure the engine (asset standards may need revisiting after engine is set)`（如引擎已配置则跳过）
- `[_] Run /design-system — start the first GDD`（如任何 GDD 存在则跳过）
- `[_] Run /review-all-gdds — cross-GDD consistency check (required before Technical Setup gate)`（如 gdd-cross-review-*.md 存在则跳过）
- `[_] Run /asset-spec — generate per-asset visual specs and AI generation prompts from approved GDDs`（如 GDD 存在则包含）
- `[_] Run /consistency-check — scan existing GDDs against the art bible for visual direction conflicts`（如 GDD 存在则包含）
- `[_] Run /create-architecture — author the master architecture document (next Technical Setup step)`
- `[_] Stop here`

仅向实际包含的选项分配字母 A、B、C…。将最合理的推进流水线的选项标记为 `(recommended)`。

> **始终包含** `/create-architecture` 和 Stop here 作为选项 —— 美术风格指南完成后这些始终是有效的下一步。

---

## Collaborative Protocol

Every section follows: **Question → Options → Decision → Draft (from art-director agent) → Approval → Write to file**

- Never draft a section without first spawning the relevant agent(s)
- Write each section to file immediately after approval — do not batch
- Surface all agent disagreements to the user — never silently resolve conflicts between art-director and technical-artist
- The art bible is a constraint document: it restricts future decisions in exchange for visual coherence. Every section should feel like it narrows the solution space productively.

## 协作协议

每个章节遵循：**问题 → 选项 → 决策 → 草稿（来自艺术总监智能体）→ 批准 → 写入文件**

- 永远不要在未先派生相关智能体的情况下起草章节
- 每个章节批准后立即写入文件 —— 不要批量
- 向用户呈现所有智能体分歧 —— 永远不要静默解决艺术总监和技术美术师之间的冲突
- 美术风格指南是约束文档：它限制未来决策以换取视觉连贯性。每个章节应感觉像在高效地缩小解决方案空间。

---

## Recommended Next Steps

After the art bible is approved:
- Run `/map-systems` to decompose the concept into game systems before authoring GDDs
- Run `/setup-engine` if the engine is not yet configured (asset standards may need revisiting after engine selection)
- Run `/design-system [first-system]` to start authoring per-system GDDs
- Run `/consistency-check` once GDDs exist to validate them against the art bible's visual rules
- Run `/create-architecture` to produce the master architecture document

## 推荐的下一步

美术风格指南批准后：
- 运行 `/map-systems` 在编写 GDD 前将概念分解为游戏系统
- 如引擎尚未配置则运行 `/setup-engine`（资产标准可能在引擎选择后需要重新审视）
- 运行 `/design-system [first-system]` 开始编写每个系统的 GDD
- GDD 存在后运行 `/consistency-check` 对照美术风格指南的视觉规则验证它们
- 运行 `/create-architecture` 生成主架构文档
