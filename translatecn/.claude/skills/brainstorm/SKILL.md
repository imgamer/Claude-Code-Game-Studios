---
name: brainstorm
description: "Guided game concept ideation — from zero idea to a structured game concept document. Uses professional studio ideation techniques, player psychology frameworks, and structured creative exploration."
argument-hint: "[genre or theme hint, or 'open'] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, WebSearch, Task, AskUserQuestion
model: sonnet
---
---
名称：brainstorm
描述："引导式游戏概念构思 — 从零创意到结构化的游戏概念文档。使用专业工作室的构思技术、玩家心理学框架和结构化的创意探索。"
argument-hint："[类型或主题提示，或 'open'] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, WebSearch, Task, AskUserQuestion
model：sonnet
---

When this skill is invoked:
当此技能被调用时：

1. **Parse the argument** for an optional genre/theme hint (e.g., `roguelike`,
   `space survival`, `cozy farming`). If `open` or no argument, start from
   scratch. Also resolve the review mode (once, store for all gate spawns this run):
   1. If `--review [full|lean|solo]` was passed → use that
   2. Else read `production/review-mode.txt` → use that value
   3. Else → default to `lean`
1. **解析参数**，获取可选的类型/主题提示（例如 `roguelike`、
   `space survival`、`cozy farming`）。如果是 `open` 或无参数，则从零开始。
   同时解析评审模式（仅一次，本次运行的所有门禁生成均使用此模式）：
   1. 如果传入了 `--review [full|lean|solo]` → 使用该模式
   2. 否则读取 `production/review-mode.txt` → 使用其中的值
   3. 否则 → 默认为 `lean`

   See `.claude/docs/director-gates.md` for the full check pattern.
   有关完整的检查模式，请参见 `.claude/docs/director-gates.md`。

2. **Check for existing concept work**:
   - Read `design/gdd/game-concept.md` if it exists (resume, don't restart)
   - Read `design/gdd/game-pillars.md` if it exists (build on established pillars)
2. **检查已有的概念工作**：
   - 如果存在 `design/gdd/game-concept.md`，则读取（恢复，而非重启）
   - 如果存在 `design/gdd/game-pillars.md`，则读取（在已确立的支柱上构建）

3. **Run through ideation phases** interactively, asking the user questions at
   each phase. Do NOT generate everything silently — the goal is **collaborative
   exploration** where the AI acts as a creative facilitator, not a replacement
   for the human's vision.
3. **以交互方式运行各构思阶段**，在每个阶段向用户提问。
   不要默默生成所有内容 — 目标是**协作式探索**，AI 充当创意引导者，
   而非替代人类的愿景。

   **Use `AskUserQuestion`** at key decision points throughout brainstorming:
   - Constrained taste questions (genre preferences, scope, team size)
   - Concept selection ("Which 2-3 concepts resonate?") after presenting options
   - Direction choices ("Develop further, explore more, or prototype?")
   - Pillar ranking after concepts are refined
   Write full creative analysis in conversation text first, then use
   `AskUserQuestion` to capture the decision with concise labels.
   **在头脑风暴的关键决策点使用 `AskUserQuestion`**：
   - 受限偏好问题（类型偏好、范围、团队规模）
   - 在呈现选项后进行概念选择（"哪 2-3 个概念引起共鸣？"）
   - 方向选择（"深入开发、继续探索，还是制作原型？"）
   - 概念完善后的支柱排序
   先在对话文本中写入完整的创意分析，然后使用 `AskUserQuestion`
   以简洁的标签捕获决策。

   Professional studio brainstorming principles to follow:
   - Withhold judgment — no idea is bad during exploration
   - Encourage unusual ideas — outside-the-box thinking sparks better concepts
   - Build on each other — "yes, and..." responses, not "but..."
   - Use constraints as creative fuel — limitations often produce the best ideas
   - Time-box each phase — keep momentum, don't over-deliberate early
   遵循专业工作室的头脑风暴原则：
   - 暂缓评判 — 探索阶段没有坏想法
   - 鼓励不寻常的想法 — 打破常规的思维能激发更好的概念
   - 相互借鉴 — 用"是的，而且……"回应，而非"但是……"
   - 将约束作为创意燃料 — 限制往往能产生最佳创意
   - 为每个阶段设定时间盒 — 保持势头，早期不要过度斟酌

---

### Phase 1: Creative Discovery
### 阶段 1：创意发现

Start by understanding the person, not the game. Ask these questions
conversationally (not as a checklist):
从理解人开始，而不是理解游戏。以对话方式提出这些问题
（而非作为清单）：

**Emotional anchors**:
- What's a moment in a game that genuinely moved you, thrilled you, or made
  you lose track of time? What specifically created that feeling?
- Is there a fantasy or power trip you've always wanted in a game but never
  quite found?
**情感锚点**：
- 游戏中是否有哪个时刻真正打动过你、让你兴奋，或让你忘记时间？
  具体是什么创造了那种感觉？
- 是否有一种你在游戏中一直想体验但从未真正找到的幻想或力量感？

**Taste profile**:
- What 3 games have you spent the most time with? What kept you coming back?
  *(Ask this as plain text — the user must be able to type specific game names freely.
  Do NOT put this in an AskUserQuestion with preset options.)*
- Are there genres you love? Genres you avoid? Why?
- Do you prefer games that challenge you, relax you, tell you stories,
  or let you express yourself? *(Use `AskUserQuestion` for this — constrained choice.)*
**偏好画像**：
- 你花时间最多的 3 款游戏是什么？是什么让你不断回头？
  *（以纯文本方式提问 — 用户必须能自由输入具体的游戏名。
  不要将其放入带有预设选项的 AskUserQuestion 中。）*
- 有你喜欢的类型吗？有你想避免的类型吗？为什么？
- 你更喜欢挑战你、让你放松、给你讲故事，还是让你表达自我的游戏？
  *（对此使用 `AskUserQuestion` — 受限选择。）*

**Practical constraints** (shape the sandbox before brainstorming).
Bundle these into a single multi-tab `AskUserQuestion` with these exact tab labels:
- Tab "Experience" — "What kind of experience do you most want players to have?" (Challenge & Mastery / Story & Discovery / Expression & Creativity / Relaxation & Flow)
- Tab "Timeline" — "What's your realistic development timeline?" (Weeks / Months / 1-2 years / Multi-year)
- Tab "Dev level" — "Where are you in your dev journey?" (First game / Shipped before / Professional background)
**实际约束**（在头脑风暴前塑造沙盒）。
将这些问题打包到一个多标签 `AskUserQuestion` 中，使用以下确切的标签名：
- 标签 "Experience" — "你最希望玩家获得什么样的体验？"（挑战与精通 / 故事与发现 / 表达与创意 / 放松与心流）
- 标签 "Timeline" — "你实际的开发时间线是怎样的？"（数周 / 数月 / 1-2 年 / 多年）
- 标签 "Dev level" — "你在开发之旅中处于什么阶段？"（首款游戏 / 之前发布过 / 专业背景）

Use exactly these tab names — do not rename or duplicate them.
使用确切的这些标签名 — 不要重命名或重复。

**Synthesize** the answers into a **Creative Brief** — a 3-5 sentence
summary of the person's emotional goals, taste profile, and constraints.
Read the brief back and confirm it captures their intent.
**综合**答案形成**创意简报** — 一段 3-5 句话的摘要，
涵盖该人的情感目标、偏好画像和约束。
将简报读回并确认它捕捉了其意图。

---

### Phase 2: Concept Generation
### 阶段 2：概念生成

Using the creative brief as a foundation, generate **3 distinct concepts**
that each take a different creative direction. Use these ideation techniques:
以创意简报为基础，生成**3 个不同的概念**，
每个概念采用不同的创意方向。使用以下构思技术：

**Technique 1: Verb-First Design**
Start with the core player verb (build, fight, explore, solve, survive,
create, manage, discover) and build outward from there. The verb IS the game.
**技术 1：动词优先设计**
从核心玩家动词开始（建造、战斗、探索、解谜、生存、
创造、管理、发现），然后向外构建。动词就是游戏。

**Technique 2: Mashup Method**
Combine two unexpected elements: [Genre A] + [Theme B]. The tension between
the two creates the unique hook. (e.g., "farming sim + cosmic horror",
"roguelike + dating sim", "city builder + real-time combat")
**技术 2：混搭法**
组合两个意想不到的元素：[类型 A] + [主题 B]。两者之间的张力
创造了独特的卖点。（例如，"农场模拟 + 宇宙恐怖"、
"Roguelike + 约会模拟"、"城市建造 + 即时战斗"）

**Technique 3: Experience-First Design (MDA Backward)**
Start from the desired player emotion (aesthetic goal from MDA framework:
sensation, fantasy, narrative, challenge, fellowship, discovery, expression,
submission) and work backward to the dynamics and mechanics that produce it.
**技术 3：体验优先设计（MDA 逆向）**
从期望的玩家情感开始（MDA 框架的审美目标：
感觉、幻想、叙事、挑战、 fellowship、发现、表达、
顺从），然后逆向推导出产生该情感的动态和机制。

For each concept, present:
- **Working Title**
- **Elevator Pitch** (1-2 sentences — must pass the "10-second test")
- **Core Verb** (the single most common player action)
- **Core Fantasy** (the emotional promise)
- **Unique Hook** (passes the "and also" test: "Like X, AND ALSO Y")
- **Primary MDA Aesthetic** (which emotion dominates?)
- **Estimated Scope** (small / medium / large)
- **Why It Could Work** (1 sentence on market/audience fit)
- **Biggest Risk** (1 sentence on the hardest unanswered question)
对于每个概念，呈现：
- **工作标题**
- **电梯演讲**（1-2 句话 — 必须通过"10 秒测试"）
- **核心动词**（最常见的玩家动作）
- **核心幻想**（情感承诺）
- **独特卖点**（通过"而且"测试："像 X，而且还有 Y"）
- **主要 MDA 审美**（哪种情感占主导？）
- **预计范围**（小 / 中 / 大）
- **为什么可行**（关于市场/受众契合度的 1 句话）
- **最大风险**（关于最难回答的问题的 1 句话）

Present all three. Then use `AskUserQuestion` to capture the selection.
呈现全部三个。然后使用 `AskUserQuestion` 捕获选择。

**CRITICAL**: This MUST be a plain list call — no tabs, no form fields. Use exactly this structure:
**关键**：这必须是纯列表调用 — 无标签、无表单字段。使用确切的结构：

```
AskUserQuestion(
  prompt: "Which concept resonates with you? You can pick one, combine elements, or ask for fresh directions.",
  options: [
    "Concept 1 — [Title]",
    "Concept 2 — [Title]",
    "Concept 3 — [Title]",
    "Combine elements across concepts",
    "Generate fresh directions"
  ]
)
```

Do NOT use a `tabs` field here. The `tabs` form is for multi-field input only — using it here causes an "Invalid tool parameters" error. This is a plain `prompt` + `options` call.
此处不要使用 `tabs` 字段。`tabs` 表单仅用于多字段输入 — 在此处使用会导致"Invalid tool parameters"错误。这是一个纯 `prompt` + `options` 调用。

Never pressure toward a choice — let them sit with it.
永远不要施加选择压力 — 让他们从容思考。

---

### Phase 3: Core Loop Design
### 阶段 3：核心循环设计

For the chosen concept, use structured questioning to build the core loop.
The core loop is the beating heart of the game — if it isn't fun in
isolation, no amount of content or polish will save the game.
对于选定的概念，使用结构化提问来构建核心循环。
核心循环是游戏跳动的核心 — 如果它单独玩起来不有趣，
任何内容或打磨都无法拯救这款游戏。

**30-Second Loop** (moment-to-moment):
**30 秒循环**（即时时刻）：

Ask these as `AskUserQuestion` calls — derive the options from the chosen concept, don't hardcode them:
将这些作为 `AskUserQuestion` 调用来提问 — 从所选概念中推导选项，不要硬编码：

1. **Core action feel** — prompt: "What's the primary feel of the core action?" Generate 3-4 options that fit the concept's genre and tone, plus a free-text escape (`I'll describe it`).
1. **核心动作手感** — 提示："核心动作的主要手感是什么？"生成 3-4 个符合概念类型和基调的选项，外加一个自由文本出口（`我来描述`）。

2. **Key design dimension** — identify the most important design variable for this specific concept (e.g., world reactivity, pacing, player agency) and ask about it. Generate options that match the concept. Always include a free-text escape.
2. **关键设计维度** — 识别此特定概念最重要的设计变量（例如世界反应性、节奏、玩家自主权）并就此提问。生成符合概念的选项。始终包含一个自由文本出口。

After capturing answers, analyze: Is this action intrinsically satisfying? What makes it feel good? (Audio feedback, visual juice, timing satisfaction, tactical depth?)
捕获答案后进行分析：这个动作本身是否令人满足？是什么让它感觉好？（音频反馈、视觉冲击力、时机满足感、战术深度？）

**5-Minute Loop** (short-term goals):
- What structures the moment-to-moment play into cycles?
- Where does "one more turn" / "one more run" psychology kick in?
- What choices does the player make at this level?
**5 分钟循环**（短期目标）：
- 是什么将即时游玩结构化为周期？
- "再来一回合" / "再来一局"的心理在哪里产生？
- 玩家在此层面做什么选择？

**Session Loop** (30-120 minutes):
- What does a complete session look like?
- Where are the natural stopping points?
- What's the "hook" that makes them think about the game when not playing?
**单次会话循环**（30-120 分钟）：
- 一次完整的会话是什么样的？
- 自然的停止点在哪里？
- 是什么"钩子"让他们在不玩的时候也想着游戏？

**Progression Loop** (days/weeks):
- How does the player grow? (Power? Knowledge? Options? Story?)
- What's the long-term goal? When is the game "done"?
**进度循环**（天/周）：
- 玩家如何成长？（力量？知识？选项？故事？）
- 长期目标是什么？游戏什么时候算"完成"？

**Player Motivation Analysis** (based on Self-Determination Theory):
- **Autonomy**: How much meaningful choice does the player have?
- **Competence**: How does the player feel their skill growing?
- **Relatedness**: How does the player feel connected (to characters,
  other players, or the world)?
**玩家动机分析**（基于自我决定理论）：
- **自主性**：玩家有多少有意义的选择？
- **胜任感**：玩家如何感受到自己的技能在成长？
- **关联性**：玩家如何感到联系（与角色、其他玩家或世界）？

---

### Phase 4: Pillars and Boundaries
### 阶段 4：支柱与边界

Game pillars are used by real AAA studios (God of War, Hades, The Last of
Us) to keep hundreds of team members making decisions that all point the
same direction. Even for solo developers, pillars prevent scope creep and
keep the vision sharp.
游戏支柱被真正的 AAA 工作室（God of War、Hades、The Last of
Us）使用，以保持数百名团队成员做出方向一致的决策。
即使对于独立开发者，支柱也能防止范围蔓延并保持愿景清晰。

Collaboratively define **3-5 pillars**:
- Each pillar has a **name** and **one-sentence definition**
- Each pillar has a **design test**: "If we're debating between X and Y,
  this pillar says we choose __"
- Pillars should feel like they create tension with each other — if all
  pillars point the same way, they're not doing enough work
协作定义**3-5 个支柱**：
- 每个支柱有一个**名称**和**一句话定义**
- 每个支柱有一个**设计测试**："如果我们在 X 和 Y 之间争论，
  这个支柱说我们选择 __"
- 支柱之间应感觉能产生张力 — 如果所有支柱指向同一方向，
  它们就没有发挥足够的作用

Then define **3+ anti-pillars** (what this game is NOT):
- Anti-pillars prevent the most common form of scope creep: "wouldn't it
  be cool if..." features that don't serve the core vision
- Frame as: "We will NOT do [thing] because it would compromise [pillar]"
然后定义**3 个以上的反支柱**（这款游戏不是什么）：
- 反支柱防止最常见的范围蔓延形式："如果……岂不是很酷"
  这类不服务于核心愿景的功能
- 表述为："我们不会做 [事情]，因为它会损害 [支柱]"

**Pillar confirmation**: After presenting the full pillar set, use `AskUserQuestion`:
- Prompt: "Do these pillars feel right for your game?"
- Options: `[A] Lock these in` / `[B] Rename or reframe one` / `[C] Swap a pillar out` / `[D] Something else`
**支柱确认**：呈现完整支柱集后，使用 `AskUserQuestion`：
- 提示："这些支柱对你的游戏合适吗？"
- 选项：`[A] 锁定这些` / `[B] 重命名或重新表述一个` / `[C] 替换一个支柱` / `[D] 其他`

If the user selects B, C, or D, make the revision, then use `AskUserQuestion` again:
- Prompt: "Pillars updated. Ready to lock these in?"
- Options: `[A] Lock these in` / `[B] Revise another pillar` / `[C] Something else`
如果用户选择 B、C 或 D，进行修订，然后再次使用 `AskUserQuestion`：
- 提示："支柱已更新。准备好锁定这些了吗？"
- 选项：`[A] 锁定这些` / `[B] 修订另一个支柱` / `[C] 其他`

Repeat until the user selects [A] Lock these in.
重复直到用户选择 [A] 锁定这些。

**Review mode check** — apply before spawning CD-PILLARS and AD-CONCEPT-VISUAL:
- `solo` → skip both. Note: "CD-PILLARS skipped — Solo mode. AD-CONCEPT-VISUAL skipped — Solo mode." Proceed to Phase 5.
- `lean` → skip both (not PHASE-GATEs). Note: "CD-PILLARS skipped — Lean mode. AD-CONCEPT-VISUAL skipped — Lean mode." Proceed to Phase 5.
- `full` → spawn as normal.
**评审模式检查** — 在生成 CD-PILLARS 和 AD-CONCEPT-VISUAL 之前应用：
- `solo` → 跳过两者。备注："CD-PILLARS skipped — Solo mode. AD-CONCEPT-VISUAL skipped — Solo mode." 进入阶段 5。
- `lean` → 跳过两者（非 PHASE-GATE）。备注："CD-PILLARS skipped — Lean mode. AD-CONCEPT-VISUAL skipped — Lean mode." 进入阶段 5。
- `full` → 正常生成。

**After pillars and anti-pillars are agreed, spawn BOTH `creative-director` AND `art-director` via Task in parallel before moving to Phase 5. Issue both Task calls simultaneously — do not wait for one before starting the other.**
**在支柱和反支柱达成一致后，通过 Task 并行生成 `creative-director` 和 `art-director`，然后进入阶段 5。同时发出两个 Task 调用 — 不要等一个完成再开始另一个。**

- **`creative-director`** — gate **CD-PILLARS** (`.claude/docs/director-gates.md`)
  Pass: full pillar set with design tests, anti-pillars, core fantasy, unique hook.
- **`art-director`** — gate **AD-CONCEPT-VISUAL** (`.claude/docs/director-gates.md`)
  Pass: game concept elevator pitch, full pillar set with design tests, target platform (if known), any reference games or visual touchstones the user mentioned.
- **`creative-director`** — 门禁 **CD-PILLARS**（`.claude/docs/director-gates.md`）
  传递：带设计测试的完整支柱集、反支柱、核心幻想、独特卖点。
- **`art-director`** — 门禁 **AD-CONCEPT-VISUAL**（`.claude/docs/director-gates.md`）
  传递：游戏概念电梯演讲、带设计测试的完整支柱集、目标平台（如已知）、用户提到的任何参考游戏或视觉基准。

Collect both verdicts, then present them together using a two-tab `AskUserQuestion`:
- Tab **"Pillars"**: present creative-director feedback. Options mirror the standard CD-PILLARS handling — `Lock in as-is` / `Revise [specific pillar]` / `Discuss further`.
- Tab **"Visual anchor"**: present the art-director's 2-3 named visual direction options. Options: each named direction (one per option) + `Combine elements across directions` + `Describe my own direction`.
收集两个裁决，然后使用双标签 `AskUserQuestion` 一起呈现：
- 标签 **"Pillars"**：呈现 creative-director 反馈。选项映射标准 CD-PILLARS 处理 — `Lock in as-is` / `Revise [specific pillar]` / `Discuss further`。
- 标签 **"Visual anchor"**：呈现 art-director 的 2-3 个命名视觉方向选项。选项：每个命名方向（每个一个选项）+ `Combine elements across directions` + `Describe my own direction`。

The user's selected visual anchor (the named direction or their custom description) is stored as the **Visual Identity Anchor** — it will be written into the game-concept document and becomes the foundation of the art bible.
用户选择的视觉锚点（命名方向或其自定义描述）被存储为**视觉身份锚点** — 它将被写入游戏概念文档，并成为美术圣经的基础。

If the creative-director returns CONCERNS or REJECT on pillars, resolve pillar issues before asking for the visual anchor selection — visual direction should flow from confirmed pillars.
如果 creative-director 对支柱返回 CONCERNS 或 REJECT，则在请求视觉锚点选择之前先解决支柱问题 — 视觉方向应从已确认的支柱中延伸出来。

---

### Phase 5: Player Type Validation
### 阶段 5：玩家类型验证

Using the Bartle taxonomy and Quantic Foundry motivation model, validate
who this game is actually for:
使用 Bartle 分类法和 Quantic Foundry 动机模型，验证这款游戏实际面向的人群：

- **Primary player type**: Who will LOVE this game? (Achievers, Explorers,
  Socializers, Competitors, Creators, Storytellers)
- **Secondary appeal**: Who else might enjoy it?
- **Who is this NOT for**: Being clear about who won't like this game is as
  important as knowing who will
- **Market validation**: Are there successful games that serve a similar
  player type? What can we learn from their audience size?
- **主要玩家类型**：谁会爱这款游戏？（成就者、探索者、
  社交者、竞争者、创造者、叙事者）
- **次要吸引力**：还有谁可能喜欢它？
- **这不适合谁**：明确谁不会喜欢这款游戏与知道谁会喜欢同样重要
- **市场验证**：是否有服务相似玩家类型的成功游戏？
  我们能从其受众规模中学到什么？

---

### Phase 6: Scope and Feasibility
### 阶段 6：范围与可行性

Ground the concept in reality:
将概念立足于现实：

- **Target platform**: Use `AskUserQuestion` — "What platforms are you targeting for this game?"
  Options: `PC (Steam / Epic)` / `Mobile (iOS / Android)` / `Console` / `Web / Browser` / `Multiple platforms`
  Record the answer — it directly shapes the engine recommendation and will be passed to `/setup-engine`.
  Note platform implications if relevant (e.g., mobile means Unity is strongly preferred; console means Godot has limitations; web means Godot exports cleanly).
- **目标平台**：使用 `AskUserQuestion` — "你为这款游戏瞄准什么平台？"
  选项：`PC (Steam / Epic)` / `Mobile (iOS / Android)` / `Console` / `Web / Browser` / `Multiple platforms`
  记录答案 — 它直接影响引擎推荐，并将传递给 `/setup-engine`。
  如相关，注意平台影响（例如，移动端意味着 Unity 是首选；主机端意味着 Godot 有局限；Web 端意味着 Godot 能干净导出）。

- **Engine experience**: Use `AskUserQuestion` — "Do you already have an engine you work in?"
  Options: `Godot` / `Unity` / `Unreal Engine 5` / `No preference — help me decide`
  - If they pick an engine → record it as their preference and move on. Do NOT second-guess it.
  - If "No preference" → tell them: "Run `/setup-engine` after this session — it will walk you through the full decision based on your concept and platform target." Do not make a recommendation here.
- **引擎经验**：使用 `AskUserQuestion` — "你已经有使用的引擎了吗？"
  选项：`Godot` / `Unity` / `Unreal Engine 5` / `No preference — help me decide`
  - 如果他们选择一个引擎 → 记录为他们的偏好并继续。不要质疑它。
  - 如果选"No preference" → 告诉他们："本次会话后运行 `/setup-engine` — 它将根据你的概念和平台目标引导你完成完整决策。"此处不要给出建议。
- **Art pipeline**: What's the art style and how labor-intensive is it?
- **Content scope**: Estimate level/area count, item count, gameplay hours
- **MVP definition**: What's the absolute minimum build that tests "is the
  core loop fun?"
- **Biggest risks**: Technical risks, design risks, market risks
- **Scope tiers**: What's the full vision vs. what ships if time runs out?
- **美术管线**：美术风格是什么，劳动强度如何？
- **内容范围**：估算关卡/区域数量、物品数量、游戏时长
- **MVP 定义**：测试"核心循环是否有趣"的绝对最小构建是什么？
- **最大风险**：技术风险、设计风险、市场风险
- **范围层级**：完整愿景 vs. 时间不够时发布的版本是什么？

**Review mode check** — apply before spawning TD-FEASIBILITY:
- `solo` → skip. Note: "TD-FEASIBILITY skipped — Solo mode." Proceed directly to scope tier definition.
- `lean` → skip (not a PHASE-GATE). Note: "TD-FEASIBILITY skipped — Lean mode." Proceed directly to scope tier definition.
- `full` → spawn as normal.
**评审模式检查** — 在生成 TD-FEASIBILITY 之前应用：
- `solo` → 跳过。备注："TD-FEASIBILITY skipped — Solo mode." 直接进入范围层级定义。
- `lean` → 跳过（非 PHASE-GATE）。备注："TD-FEASIBILITY skipped — Lean mode." 直接进入范围层级定义。
- `full` → 正常生成。

**After identifying biggest technical risks, spawn `technical-director` via Task using gate TD-FEASIBILITY (`.claude/docs/director-gates.md`) before scope tiers are defined.**
**在识别最大技术风险后，通过 Task 使用门禁 TD-FEASIBILITY（`.claude/docs/director-gates.md`）生成 `technical-director`，在定义范围层级之前。**

Pass: core loop description, platform target, engine choice (or "undecided"), list of identified technical risks.
传递：核心循环描述、平台目标、引擎选择（或"未定"）、已识别的技术风险列表。

Present the assessment to the user. If HIGH RISK, offer to revisit scope before finalising. If CONCERNS, note them and continue.
向用户呈现评估。如果是 HIGH RISK，提供在最终确定前重新审视范围的选项。如果是 CONCERNS，记录并继续。

**Review mode check** — apply before spawning PR-SCOPE:
- `solo` → skip. Note: "PR-SCOPE skipped — Solo mode." Proceed to document generation.
- `lean` → skip (not a PHASE-GATE). Note: "PR-SCOPE skipped — Lean mode." Proceed to document generation.
- `full` → spawn as normal.
**评审模式检查** — 在生成 PR-SCOPE 之前应用：
- `solo` → 跳过。备注："PR-SCOPE skipped — Solo mode." 进入文档生成。
- `lean` → 跳过（非 PHASE-GATE）。备注："PR-SCOPE skipped — Lean mode." 进入文档生成。
- `full` → 正常生成。

**After scope tiers are defined, spawn `producer` via Task using gate PR-SCOPE (`.claude/docs/director-gates.md`).**
**范围层级定义后，通过 Task 使用门禁 PR-SCOPE（`.claude/docs/director-gates.md`）生成 `producer`。**

Pass: full vision scope, MVP definition, timeline estimate, team size.
传递：完整愿景范围、MVP 定义、时间线估算、团队规模。

Present the assessment to the user. If UNREALISTIC, offer to adjust the MVP definition or scope tiers before writing the document.
向用户呈现评估。如果是 UNREALISTIC，提供在写入文档前调整 MVP 定义或范围层级的选项。

---

4. **Generate the game concept document** using the template at
   `.claude/docs/templates/game-concept.md`. Fill in ALL sections from the
   brainstorm conversation, including the MDA analysis, player motivation
   profile, and flow state design sections.
4. **使用模板生成游戏概念文档**
   `.claude/docs/templates/game-concept.md`。用头脑风暴对话中的内容填写所有部分，
   包括 MDA 分析、玩家动机画像和心流状态设计部分。

   **Include a Visual Identity Anchor section** in the game concept document with:
   - The selected visual direction name
   - The one-line visual rule
   - The 2-3 supporting visual principles with their design tests
   - The color philosophy summary
   **在游戏概念文档中包含视觉身份锚点部分**，包括：
   - 选定的视觉方向名称
   - 一行视觉规则
   - 2-3 条支撑视觉原则及其设计测试
   - 色彩哲学摘要

   This section is the seed of the art bible — it captures the "everything must
   move" decision before it can be forgotten between sessions.
   此部分是美术圣经的种子 — 它在"一切皆须运动"决策被会话间遗忘之前将其捕获。

5. Use `AskUserQuestion` for write approval:
- Prompt: "Game concept is ready. May I write it to `design/gdd/game-concept.md`?"
- Options: `[A] Yes — write it` / `[B] Not yet — revise a section first`
5. 使用 `AskUserQuestion` 获取写入批准：
- 提示："游戏概念已就绪。我可以将其写入 `design/gdd/game-concept.md` 吗？"
- 选项：`[A] 是 — 写入` / `[B] 还不行 — 先修订某个部分`

If [B]: ask which section to revise using `AskUserQuestion` with options: `Elevator Pitch` / `Core Fantasy & Unique Hook` / `Pillars` / `Core Loop` / `MVP Definition` / `Scope Tiers` / `Risks` / `Something else — I'll describe`
如果 [B]：使用 `AskUserQuestion` 询问要修订哪个部分，选项：`Elevator Pitch` / `Core Fantasy & Unique Hook` / `Pillars` / `Core Loop` / `MVP Definition` / `Scope Tiers` / `Risks` / `Something else — I'll describe`

After revising, show the updated section as a diff or clear before/after, then use `AskUserQuestion` — "Ready to write the updated concept document?"
Options: `[A] Yes — write it` / `[B] Revise another section`
Repeat until the user selects [A].
修订后，以差异或清晰的前后对比显示更新的部分，然后使用 `AskUserQuestion` — "准备好写入更新后的概念文档了吗？"
选项：`[A] 是 — 写入` / `[B] 修订另一个部分`
重复直到用户选择 [A]。

If yes, generate the document using the template at `.claude/docs/templates/game-concept.md`, fill in ALL sections from the brainstorm conversation, and write the file, creating directories as needed.
如果是，使用模板 `.claude/docs/templates/game-concept.md` 生成文档，用头脑风暴对话中的内容填写所有部分，并写入文件，按需创建目录。

**Scope consistency rule**: The "Estimated Scope" field in the Core Identity table must match the full-vision timeline from the Scope Tiers section — not just say "Large (9+ months)". Write it as "Large (X–Y months, solo)" or "Large (X–Y months, team of N)" so the summary table is accurate.
**范围一致性规则**：核心身份表中的"Estimated Scope"字段必须与范围层级部分中的完整愿景时间线匹配 — 而不是仅写"Large (9+ months)"。写为"Large (X–Y months, solo)"或"Large (X–Y months, team of N)"，以使摘要表准确。

6. **Suggest next steps** (in this order — this is the professional studio
   pre-production pipeline). List ALL steps — do not abbreviate or truncate:
6. **建议后续步骤**（按此顺序 — 这是专业工作室的
   前期制作流水线）。列出所有步骤 — 不要缩写或截断：

**Path A — Design-First** (recommended if the concept is well-defined):
**路径 A — 设计优先**（如果概念已明确定义则推荐）：

   1. "Run `/setup-engine` to configure the engine and populate version-aware reference docs"
   2. "Run `/art-bible` to create the visual identity specification — do this BEFORE writing GDDs. **The art bible is required before the Technical Setup gate.** It gates asset production and shapes technical architecture decisions (rendering, VFX, UI systems)."
   3. "Use `/design-review design/gdd/game-concept.md` to validate concept completeness before going downstream"
   4. "Discuss vision with the `creative-director` agent for pillar refinement"
   5. "Decompose the concept into individual systems with `/map-systems` — maps dependencies, assigns priorities, and creates the systems index"
   6. "Author per-system GDDs with `/design-system` — guided, section-by-section GDD writing for each system identified in step 5"
   7. "Plan the technical architecture with `/create-architecture` — produces the master architecture blueprint and Required ADR list"
   8. "Record key architectural decisions with `/architecture-decision (×N)` — write one ADR per decision in the Required ADR list from `/create-architecture`"
   9. "Run `/architecture-review` — bootstraps the TR registry and Requirements Traceability Matrix from your GDDs and ADRs (required before the Pre-Production gate)"
   10. "Validate readiness to advance with `/gate-check` — phase gate before committing to production"
   1. "运行 `/setup-engine` 以配置引擎并填充版本感知的参考文档"
   2. "运行 `/art-bible` 创建视觉身份规范 — 在编写 GDD 之前完成。**美术圣经是技术设置门禁之前所必需的。**它门禁资产生产并塑造技术架构决策（渲染、VFX、UI 系统）。"
   3. "使用 `/design-review design/gdd/game-concept.md` 在进入下游之前验证概念完整性"
   4. "与 `creative-director` 智能体讨论愿景以完善支柱"
   5. "使用 `/map-systems` 将概念分解为各个系统 — 映射依赖关系、分配优先级并创建系统索引"
   6. "使用 `/design-system` 编写各系统 GDD — 为步骤 5 中识别的每个系统进行引导式、逐节 GDD 编写"
   7. "使用 `/create-architecture` 规划技术架构 — 生成主架构蓝图和所需 ADR 列表"
   8. "使用 `/architecture-decision (×N)` 记录关键架构决策 — 为 `/create-architecture` 中所需 ADR 列表中的每个决策编写一个 ADR"
   9. "运行 `/architecture-review` — 从你的 GDD 和 ADR 引导启动 TR 注册表和需求追溯矩阵（前期制作门禁之前必需）"
   10. "使用 `/gate-check` 验证推进就绪 — 进入制作之前的阶段门禁"

**Path B — Prototype-First** (use if the core mechanic is unproven or the concept needs validation):
**路径 B — 原型优先**（如果核心机制未经验证或概念需要验证时使用）：

   1. "Run `/setup-engine` to configure the engine"
   2. "Run `/prototype [core-mechanic]` — validate the core idea is fun before writing any GDDs (1–3 days throwaway code)"
   3. "If prototype PROCEEDS: run `/art-bible`, then continue with Path A steps 5–10 above, using prototype learnings to inform your GDDs"
   4. "If prototype PIVOTS: return to `/brainstorm` with the learnings and reshape the concept"
   5. "After full design and architecture, build the `/vertical-slice` to validate production readiness before committing to sprints"
   1. "运行 `/setup-engine` 配置引擎"
   2. "运行 `/prototype [core-mechanic]` — 在编写任何 GDD 之前验证核心想法是否有趣（1-3 天一次性代码）"
   3. "如果原型 PROCEEDS：运行 `/art-bible`，然后继续上述路径 A 的步骤 5-10，利用原型的学习成果为 GDD 提供信息"
   4. "如果原型 PIVOTS：带着学习成果返回 `/brainstorm` 并重塑概念"
   5. "在完成完整设计和架构后，构建 `/vertical-slice` 以在投入冲刺之前验证生产就绪度"

7. **Output a summary** with the chosen concept's elevator pitch, pillars,
   primary player type, engine recommendation, biggest risk, and file path.
7. **输出摘要**，包含所选概念的电梯演讲、支柱、
   主要玩家类型、引擎推荐、最大风险和文件路径。

Verdict: **COMPLETE** — game concept created and handed off for next steps.
裁决：**COMPLETE** — 游戏概念已创建并移交后续步骤。

---

## Context Window Awareness
## 上下文窗口感知

This is a multi-phase skill. If context reaches or exceeds 70% during any phase,
append this notice to the current response before continuing:
这是一个多阶段技能。如果上下文在任何阶段达到或超过 70%，
在继续之前将此通知附加到当前响应：

> **Context is approaching the limit (≥70%).** The game concept document is saved
> to `design/gdd/game-concept.md`. Open a fresh Claude Code session to continue
> if needed — progress is not lost.
> **上下文即将达到上限（≥70%）。** 游戏概念文档已保存
> 到 `design/gdd/game-concept.md`。如有需要，打开新的 Claude Code 会话继续
> — 进度不会丢失。

---

## Recommended Next Steps
## 推荐后续步骤

After the game concept is written, follow the pre-production pipeline in order:
游戏概念写入后，按顺序遵循前期制作流水线：

1. `/setup-engine` — configure the engine and populate version-aware reference docs
2. `/art-bible` — establish visual identity before writing any GDDs
3. `/map-systems` — decompose the concept into individual systems with dependencies
4. `/design-system [first-system]` — author per-system GDDs in dependency order
5. `/create-architecture` — produce the master architecture blueprint
6. `/architecture-review` — bootstrap TR registry and Requirements Traceability Matrix
7. `/gate-check pre-production` — validate readiness before committing to production
1. `/setup-engine` — 配置引擎并填充版本感知的参考文档
2. `/art-bible` — 在编写任何 GDD 之前确立视觉身份
3. `/map-systems` — 将概念分解为带依赖关系的各个系统
4. `/design-system [first-system]` — 按依赖顺序编写各系统 GDD
5. `/create-architecture` — 生成主架构蓝图
6. `/architecture-review` — 引导启动 TR 注册表和需求追溯矩阵
7. `/gate-check pre-production` — 在投入制作之前验证就绪度
