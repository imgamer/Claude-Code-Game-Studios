---
name: prototype
description: "Concept prototype — validate the core idea is worth designing before writing GDDs. Run right after /brainstorm and /setup-engine. Routes to HTML, Engine, or Paper path based on game type. Produces a throwaway build and a PROCEED/PIVOT/KILL verdict."
argument-hint: "[concept-description] [--path html|engine|paper] [--review full|lean|solo] [--spike]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model: sonnet
agent: prototyper
isolation: worktree
---
---
名称：prototype
描述："概念原型 —— 在编写 GDD 之前验证核心创意是否值得设计。在 /brainstorm 与 /setup-engine 之后立即运行。根据游戏类型路由到 HTML、引擎或纸上路径。产出一次性构建以及 PROCEED/PIVOT/KILL 评审结论。"
argument-hint："[concept-description] [--path html|engine|paper] [--review full|lean|solo] [--spike]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model：sonnet
agent：prototyper
isolation：worktree
---

## Purpose
## 目的

This is the **concept prototype** — a fast, throwaway build that answers one question:
*"Is this core idea actually fun to interact with?"*
这是**概念原型** —— 一个快速、一次性的构建，回答一个问题：
*"这个核心创意真的有趣到值得互动吗？"*

**Default use** — run right after `/brainstorm` and `/setup-engine`, before writing
GDDs or architecture docs. Its verdict determines whether the concept is worth the
investment of full design documentation.
**默认用法** —— 在 `/brainstorm` 和 `/setup-engine` 之后立即运行，在编写 GDD 或
架构文档之前。其结论决定该概念是否值得投入完整设计文档的成本。

**Mid-production?** You can also run this at any stage to test a specific mechanic,
design change, or technical question. Pass `--spike` to activate spike mode: a
lightweight ~4-hour build with no GDD prerequisites and no phase gate implications.
**正在制作中期？** 您也可以在任何阶段运行此技能以测试特定机制、设计变更或
技术问题。传入 `--spike` 以激活 spike 模式：一次轻量级的约 4 小时构建，无
GDD 前置要求，无阶段门禁含义。

**Already have GDDs and architecture complete?** To validate the full game loop
before committing to Production, run `/vertical-slice` instead.
**GDD 和架构已经完成？** 若要在投入 Production 之前验证完整游戏循环，请改用
`/vertical-slice`。

---

## Phase 1: Define the Question
## 阶段 1：定义问题

Resolve the review mode (once, store for all gate spawns this run):
解析评审模式（仅一次，为本轮所有门禁启动存储）：
1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`
1. 若传入了 `--review [full|lean|solo]` → 使用该值
2. 否则读取 `production/review-mode.txt` → 使用该值
3. 否则 → 默认为 `lean`

**Check for spike mode:** If `--spike` was passed, skip to the **Spike Mode** section
at the bottom of this skill.
**检查 spike 模式：** 若传入了 `--spike`，跳到本技能底部的 **Spike Mode** 段落。

Otherwise, use `AskUserQuestion` to confirm intent before proceeding:
否则，使用 `AskUserQuestion` 在继续之前确认意图：

- **Prompt**: "How would you like to use this prototype session?"
- **Options**:
  - `Prototype this concept` — build a throwaway build to validate the core idea is fun before writing GDDs (1–3 days)
  - `Skip — concept already proven` — I have enough evidence this works; log it and proceed directly to design
  - `Mid-production spike` — I'm already in Production and want to test a specific mechanic or technical question quickly (~4 hours, no phase gate implications)
- **提示**："How would you like to use this prototype session?"
- **选项**：
  - `Prototype this concept` —— 构建一次性构建以验证核心创意有趣，再编写 GDD（1-3 天）
  - `Skip — concept already proven` —— 我已有足够证据表明它可行；记录下来并直接进入设计
  - `Mid-production spike` —— 我已在 Production 中，想快速测试某个特定机制或技术问题（约 4 小时，无阶段门禁含义）

**If "Skip — concept already proven":**
Ask (plain text, not a widget): "What evidence do you have that the concept works?"
Record the one-line answer, then stop. Note: "Concept prototype skipped — evidence:
[answer]." Suggest next step: `/map-systems` or `/design-system [mechanic]`.
**若是 "Skip — concept already proven"：**
（用纯文本而非组件）询问："What evidence do you have that the concept works?"
记录一行答案后停止。注明："Concept prototype skipped — evidence:
[answer]." 建议下一步：`/map-systems` 或 `/design-system [mechanic]`。

**If "Mid-production spike"**: skip to the **Spike Mode** section below.
**若是 "Mid-production spike"**：跳到下方的 **Spike Mode** 段落。

**If "Prototype this concept"**: continue with Phase 1 below.
**若是 "Prototype this concept"**：继续下方阶段 1。

---

**A note on prototype strategy:** The research on successful indie development
is consistent — building 2-3 concept variants and letting the best one win is
far more likely to succeed than iterating one concept until it works. This is
your first prototype, not necessarily your only one. If this prototype produces
a PIVOT verdict, consider whether to refine this concept OR start fresh with a
different angle on the same game idea and prototype that instead.
**关于原型策略的说明：** 关于成功的独立游戏开发的研究结论一致 —— 构建 2-3
个概念变体并让最优者胜出，比反复迭代一个概念直到它可行要更有可能成功。这是
您的第一个原型，不一定是唯一的原型。若此原型产出 PIVOT 结论，考虑是改进这个
概念，还是用同一个游戏想法的不同角度重新开始并为之构建原型。

**Game jam as a prototype vehicle:** If you're planning a concept prototype anyway,
consider timing it to a game jam (Ludum Dare, GMTK Game Jam, Global Game Jam). Jams
provide a forced timebox (48-72 hours), instant distribution to thousands of players
who rate and review early builds, and a deadline that prevents scope creep by design.
Many shipped games (Celeste, VVVVVV) began as jam prototypes. Not required — but
worth considering if the timing is right.
**以 Game Jam 作为原型的载体：** 若您本来就在计划概念原型，可考虑将其与某次
Game Jam（Ludum Dare、GMTK Game Jam、Global Game Jam）时机对齐。Jam 提供强制
时间盒（48-72 小时）、对早期构建评分和评论的数千名玩家的即时分发，以及一个
天然防止范围蔓延的截止日。许多已发行的游戏（Celeste、VVVVVV）起源于 Jam
原型。并非必需 —— 但若时机合适，值得考虑。

Read the concept description from the argument. Before building anything, define
the **falsifiable hypothesis** this prototype must answer:
从参数读取概念描述。在构建任何东西之前，定义此原型必须回答的**可证伪假设**：

> *"If the player [does X], they will feel [Y] — we will know this is true if [measurable signal Z]."*
> *"如果玩家 [做 X]，他们会感觉到 [Y] —— 若 [可测量信号 Z] 出现，我们即知此为真。"*

Good: "If the player swings on grapple hooks, traversal will feel fluid — we'll know if
players chain 3+ swings without stopping within 2 minutes of picking it up."
好："If the player swings on grapple hooks, traversal will feel fluid — we'll know if
players chain 3+ swings without stopping within 2 minutes of picking it up."

Bad: "Does this feel fun?" ← not testable, not falsifiable.
坏："Does this feel fun?" ← 不可测试、不可证伪。

**If the concept is too vague to form a hypothesis, stop here.** Ask the user to
narrow the question before proceeding. A prototype without a clear question wastes time.
**若概念过于模糊以至于无法形成假设，在此停止。** 请用户在继续之前缩小问题范围。
没有清晰问题的原型会浪费时间。

Also ask: **"What is the riskiest assumption in this concept?"** That is the first
thing the prototype should test — not the easiest part, the riskiest.
同时询问：**"此概念中最冒险的假设是什么？"** 那是原型应当首先测试的内容 ——
不是最易部分，而是最冒险的部分。

---

## Phase 2: Load Concept Context
## 阶段 2：加载概念上下文

Read `design/gdd/game-concept.md` if it exists. Extract:
- Core fantasy (what the player is supposed to feel)
- Core loop (the moment-to-moment action being tested)
读取 `design/gdd/game-concept.md`（若存在）。提取：
- 核心幻想（玩家应当感受到什么）
- 核心循环（正在被测试的、当下进行的动作）

Read `CLAUDE.md` and `.claude/docs/technical-preferences.md` for the engine and
language in use.
读取 `CLAUDE.md` 与 `.claude/docs/technical-preferences.md`，了解所用的引擎
和语言。

---

## Phase 3: Choose the Prototype Path
## 阶段 3：选择原型路径

Select the prototype path. If `--path [html|engine|paper]` was passed, use that.
Otherwise, use this quick-reference first, then read the full path details below:
选择原型路径。若传入了 `--path [html|engine|paper]`，则使用该值。否则，先使用
此快速参考表，再阅读下方完整路径详情：

| Genre | Recommended path | Key reason |
|-------|-----------------|------------|
| Platformer / action / fighter | **Engine** | Feel IS the hypothesis; browser latency produces false results |
| Racing / sports | **Engine** | Same — timing and physics feedback are the point |
| Top-down shooter / twin-stick | **Engine** | Aim feel is timing-sensitive |
| Puzzle (logic) | **HTML** or **Paper** | Timing is not the point; logic and clarity are |
| Card game | **Paper** first | Fastest iteration by hand before touching code |
| Narrative / visual novel | **Paper** (Twine / Ink / Yarn Spinner) | Story is the mechanic — test it without code overhead |
| Strategy / 4X / city builder | **Paper** (spreadsheet sim) | Validate economy and progression rules before building |
| Roguelike (systems-heavy) | **Paper** → Engine | Validate that the ruleset is interesting before building |
| Idle / clicker / incremental | **HTML** | Turn-based logic, no feel sensitivity required |
| Rhythm game | **Paper** first (design levels in audio) | Design levels before the engine exists |
| RPG / open world | **Paper** → Engine | Systems complexity: validate rules, then validate feel |
| Horror / atmospheric | **Engine** | Atmosphere requires real rendering |
| 类型 | 推荐路径 | 关键原因 |
|-------|-----------------|------------|
| 平台/动作/格斗 | **引擎** | 手感即假设；浏览器延迟会产生错误结果 |
| 竞速/体育 | **引擎** | 同上 —— 时机与物理反馈是核心 |
| 俯视角射击/双摇杆 | **引擎** | 瞄准手感对时机敏感 |
| 解谜（逻辑类） | **HTML** 或 **纸上** | 时机不是核心；逻辑与清晰度才是 |
| 卡牌游戏 | **先纸上** | 接触代码之前用手迭代最快 |
| 叙事/视觉小说 | **纸上**（Twine / Ink / Yarn Spinner） | 故事即机制 —— 无需代码开销即可测试 |
| 策略/4X/城建 | **纸上**（电子表格模拟） | 在构建之前验证经济与进度规则 |
| Roguelike（系统密集型） | **纸上** → 引擎 | 在构建之前验证规则集是否有趣 |
| 放置/点击/增量 | **HTML** | 回合制逻辑，无手感敏感性要求 |
| 节奏游戏 | **先纸上**（用音频设计关卡） | 在引擎存在之前设计关卡 |
| RPG/开放世界 | **纸上** → 引擎 | 系统复杂度：先验证规则，再验证手感 |
| 恐怖/氛围 | **引擎** | 氛围需要真实渲染 |

**Rule of thumb:** "Does this feel right?" → Engine. "Are these rules interesting?" → Paper. "Is this logic correct?" → HTML or Paper.
**经验法则：** "手感对吗？" → 引擎。"规则有趣吗？" → 纸上。"逻辑正确吗？" → HTML 或纸上。

### Path: HTML (browser-playable)
### 路径：HTML（浏览器可玩）

**Best for:** Puzzle games, card games, turn-based strategy, word games, idle games,
top-down logic games. Anything where timing precision doesn't matter.
**最适合：** 解谜游戏、卡牌游戏、回合制策略、文字游戏、放置游戏、俯视角逻辑
游戏。任何时机精度无关紧要的品类。

**Reliability:** ~85–90% one-shot. The agent writes a single self-contained HTML
file the user opens in a browser — no install required.
**可靠性：** 约 85-90% 一次成功。智能体写出一个自包含 HTML 文件，用户在浏览器
中打开 —— 无需安装。

**Limitation — browser latency lies about game feel.** Browsers introduce
50–133ms of rendering variance. This makes HTML prototypes fundamentally unreliable
for action games, platformers, fighting games, or anything where input timing,
jump arcs, or collision feel are what you're testing. If feel is the hypothesis,
use the Engine path instead.
**局限 —— 浏览器延迟会撒谎关于游戏手感。** 浏览器引入 50-133ms 的渲染差异。
这使 HTML 原型在动作游戏、平台跳跃、格斗游戏或任何输入时机、跳跃弧线、
碰撞手感是测试目标的情况下根本不可靠。若手感是假设，请改用引擎路径。

**Alternative tools for this path:** PICO-8 (extreme constraints, great for retro
arcade concepts, web-export in one command), Phaser.js (more capable browser game
framework, still no install needed), or Twine (narrative/choice-based games).
These are faster than raw HTML for their respective genres — suggest them if appropriate.
**此路径的替代工具：** PICO-8（极限约束，适合复古街机概念，一条命令导出 Web）、
Phaser.js（功能更强的浏览器游戏框架，仍无需安装）、或 Twine（叙事/选项向游戏）。
对于各自的类型，这些比原始 HTML 更快 —— 若合适，请建议使用。

**Output:** A single `prototype.html` (or PICO-8/Phaser equivalent) the user opens in any browser.
**产出：** 单个 `prototype.html`（或 PICO-8/Phaser 等价物），用户可在任意浏览器打开。

**Distribution — the HTML path's biggest advantage:** Unlike Engine prototypes, this
build can reach real players globally in minutes. Use this actively:
- **itch.io** — upload the file, share the link, get play counts and written feedback
  within hours. Free. The indie community plays rough builds here without expecting
  polish. This is genuine external validation at zero cost.
- **Loom + file share** — share via Google Drive/Dropbox, ask someone to record their
  screen + audio with Loom while playing. You get a video of real first-impression
  reactions and confusion without synchronous scheduling.
- **r/playmygame or r/WebGames** (Reddit) — active communities that specifically
  test early builds and give unsolicited honest feedback.
- **Game dev Discord servers** (GMTK, Brackeys, GameDev.tv) — members test each
  other's prototypes routinely; an HTML file is the easiest possible ask.
**分发 —— HTML 路径的最大优势：** 与引擎原型不同，此构建可在数分钟内触达
全球真实玩家。积极利用此优势：
- **itch.io** —— 上传文件、分享链接，在数小时内获得游玩次数和书面反馈。
  免费。独立游戏社区在此游玩粗糙构建而不期待打磨。这是零成本的真实外部
  验证。
- **Loom + 文件分享** —— 通过 Google Drive/Dropbox 分享，请人用 Loom 录屏
  + 音频游玩。您会得到真实第一印象反应与困惑的视频，无需同步协调时间。
- **r/playmygame 或 r/WebGames**（Reddit）—— 专门测试早期构建并主动提供
  诚实反馈的活跃社区。
- **Game dev Discord 服务器**（GMTK、Brackeys、GameDev.tv）—— 成员间经常
  互测原型；一个 HTML 文件是最容易请求的东西。

---

### Path: Engine (engine project)
### 路径：引擎（引擎项目）

**Best for:** Action games, platformers, physics-heavy games, anything where
moment-to-moment feel IS the hypothesis. Use this when HTML latency would lie about
the result.
**最适合：** 动作游戏、平台跳跃、物理密集型游戏，任何当下手感即假设的情况。
当 HTML 延迟会撒谎时使用此路径。

**Reliability:** ~50–60% one-shot. Expect 2–4 rounds of iteration — this is
normal, not a failure.
**可靠性：** 约 50-60% 一次成功。预计 2-4 轮迭代 —— 这正常，非失败。

**Limitation — requires engine installed and running.** This path is a
multi-turn collaborative loop:
1. Agent writes the code
2. User runs it in the engine
3. User reports errors or observations
4. Agent fixes and iterates
**局限 —— 需要引擎已安装并运行。** 此路径是多轮协作循环：
1. 智能体写代码
2. 用户在引擎中运行
3. 用户报告错误或观察
4. 智能体修复并迭代

**Sunk cost rule:** If the user has been iterating for more than 2 hours without
reaching a playable state, stop. The scope is too large or the question is wrong.
Reframe the hypothesis and simplify aggressively, or switch to Paper path.
**沉没成本规则：** 若用户已迭代超过 2 小时仍未达到可玩状态，停止。范围过大
或问题错误。重新构造假设并大力简化，或切换到纸上路径。

**Output:** A minimal runnable engine project in `prototypes/[name]-concept/`.
**产出：** `prototypes/[name]-concept/` 中的最小可运行引擎项目。

**Lighter alternative — Love2D (Lua):** If the project engine (Godot, Unity, Unreal)
feels too heavy to stand up for a throwaway build, consider Love2D — a minimal 2D
framework that installs in minutes, requires no project scaffolding, and renders
natively with no browser latency. Used by many indie devs for rapid 2D action and
platformer prototypes (Balatro prototyped in Love2D; Nuclear Throne's early builds
used it). It sits between HTML overhead and full engine overhead: heavier than
opening a browser, lighter than setting up a full engine project. Best for 2D
action/platformer feel validation when the project engine is 3D-first or takes
significant time to configure.
**更轻的替代方案 —— Love2D (Lua)：** 若项目引擎（Godot、Unity、Unreal）对于
一次性构建来说过重，可考虑 Love2D —— 一个最小 2D 框架，几分钟内安装完成，
无需项目脚手架，原生渲染无浏览器延迟。许多独立开发者用它做快速 2D 动作和平台
原型（Balatro 在 Love2D 中原型化；Nuclear Throne 早期构建使用它）。它位于
HTML 开销和完整引擎开销之间：比打开浏览器重，比搭建完整引擎项目轻。当项目
引擎是 3D 优先或需要大量时间配置时，最适合 2D 动作/平台手感验证。

---

### Path: Paper (rules document + play log)
### 路径：纸上（规则文档 + 游玩日志）

**Best for:** Strategy games, card games, board game-style mechanics, economy
systems, progression loops, any game where the logic can be simulated by hand.
Works for any genre when you need to validate rules, not feel.
**最适合：** 策略游戏、卡牌游戏、桌游式机制、经济系统、进度循环，任何可手工
模拟逻辑的游戏。当您需要验证规则而非手感时，适用于任何类型。

**Reliability:** 100%. No code, no engine, no install.
**可靠性：** 100%。无代码、无引擎、无安装。

**Limitation — cannot validate moment-to-moment feel.** Paper prototypes prove
that the rules are internally consistent and the decisions are interesting. They
cannot tell you whether jumping feels right or whether explosions feel satisfying.
**局限 —— 无法验证当下手感。** 纸上原型证明规则内部一致且决策有趣。它们无法
告诉您跳跃手感是否正确或爆炸是否令人满意。

**Paper playtest observation protocol (run this with 5+ people):**
1. Brief the rules once. Hand them the rule summary sheet. Then step back.
2. Do NOT explain further. Do NOT help. Do NOT clarify. Confusion is data.
3. Watch silently. Note every moment they slow down, re-read, or ask a question.
4. After the session, ask one question only: "What was confusing?" — not "Did you like it?"
5. Use fresh testers for each iteration. The same person cannot give new first-impression data.
6. If 3+ testers hit the same confusion point, that rule is broken — redesign it before re-testing.
**纸上试玩观察协议（对 5+ 人执行）：**
1. 只讲解一次规则。把规则摘要交给他们。然后退后。
2. 不要进一步解释。不要帮助。不要澄清。困惑就是数据。
3. 静默观察。记录他们每一次慢下来、重读或提问的时刻。
4. 会话之后，只问一个问题："What was confusing?" —— 不要问 "Did you like it?"
5. 每次迭代用新测试者。同一人不能提供新的第一印象数据。
6. 若 3+ 测试者卡在同一个困惑点，那条规则就有问题 —— 在重测前重新设计。

**Output:** A printable rules document + a completed play log showing one simulated session.
**产出：** 可打印规则文档 + 一份完成的游玩日志，展示一次模拟会话。

**Narrative tools for this path:** For dialogue-heavy and story-driven games, skip the
generic rules doc — use a dedicated narrative scripting tool instead:
- **Twine** — zero-code hypertext fiction; ideal for branching structure experiments and choice-impact testing
- **Ink** (Inkle) — plain-text scripting language used in *80 Days*, *Heaven's Vault*, and *Overboard*; exports directly to Unity and Godot
- **Yarn Spinner** — dialogue scripting used in *A Short Hike*, *DREDGE*, and *Night in the Woods*; integrates natively with Unity and Godot
**此路径的叙事工具：** 对于对话密集和故事驱动的游戏，跳过通用规则文档 —— 改用
专门的叙事脚本工具：
- **Twine** —— 零代码超文本小说；适合分支结构实验和选项影响测试
- **Ink**（Inkle）—— 纯文本脚本语言，用于 *80 Days*、*Heaven's Vault*、
  *Overboard*；可直接导出到 Unity 和 Godot
- **Yarn Spinner** —— 对话脚本，用于 *A Short Hike*、*DREDGE*、*Night in
  the Woods*；与 Unity 和 Godot 原生集成

All three let you write and playtest branching dialogue in minutes. Key metric for
narrative prototypes: **time to first emotional beat** — how many exchanges before
the player feels something? If it takes more than 3-4 exchanges, the opening is too slow.
三者都可在几分钟内编写并试玩分支对话。叙事原型的关键指标：**首个情感节点
耗时** —— 玩家在多少次交流后会感到某种情绪？若超过 3-4 次交流，开头就太慢了。

---

Assess which path best fits the hypothesis, then use `AskUserQuestion` with your
recommendation pre-stated:
评估哪条路径最贴合假设，然后用 `AskUserQuestion` 并预先陈述您的建议：

- **Prompt**: "Which prototype path would you like to use? (Based on your concept, I'd recommend [path] — [one sentence reason].)"
- **Options**:
  - `HTML — browser prototype` — puzzle, card, turn-based, strategy, idle. Opens by double-clicking, no install. 85–90% reliable. **Not suitable for action games** — browser latency lies about feel.
  - `Engine — native prototype` — action, platformer, physics, or anything where feel IS the hypothesis. 50–60% one-shot; 2–4 iteration rounds are normal. Requires engine installed.
  - `Paper — rules document + play log` — strategy, economy, logic, board-game-style mechanics. 100% reliable. Cannot validate feel.
- **提示**："Which prototype path would you like to use? (Based on your concept, I'd recommend [path] — [one sentence reason].)"
- **选项**：
  - `HTML — browser prototype` —— 解谜、卡牌、回合制、策略、放置。双击打开，无需安装。85-90% 可靠。**不适合动作游戏** —— 浏览器延迟对手感撒谎。
  - `Engine — native prototype` —— 动作、平台、物理，或任何手感即假设的情况。50-60% 一次成功；2-4 轮迭代正常。需引擎已安装。
  - `Paper — rules document + play log` —— 策略、经济、逻辑、桌游式机制。100% 可靠。无法验证手感。

---

## Phase 4: Plan the Prototype
## 阶段 4：规划原型

Define in 3–5 bullet points the minimum viable prototype:
用 3-5 个要点定义最小可行原型：

- What is the falsifiable hypothesis?
- What is the riskiest assumption — and how does this prototype test it first?
- What is the absolute minimum needed to answer the question?
- What is explicitly cut? (menus, save systems, error handling, polish, architecture — all of it)
- 可证伪假设是什么？
- 最冒险的假设是什么 —— 此原型如何首先测试它？
- 回答问题所需的绝对最小内容是什么？
- 明确砍掉什么？（菜单、存档系统、错误处理、打磨、架构 —— 全部）

**Scope constraint:** A concept prototype tests ONE mechanic — not the whole game.
If scope covers more than one mechanic, cut it down. When in doubt, cut more.
**范围约束：** 一个概念原型测试一个机制 —— 而非整款游戏。若范围覆盖超过一个
机制，砍掉。犹豫时，砍更多。

Present this plan to the user before building. Get confirmation before proceeding.
在构建之前向用户展示此计划。在继续之前取得确认。

Once confirmed, write a session checkpoint to `production/session-state/active.md`
(create `production/session-state/` if it does not exist). Include: concept name,
hypothesis, path chosen, scope bullet points, and current phase ("Phase 5 —
Implement"). This lets the next session resume without starting over if the session
ends mid-build — especially important for multi-day Engine path work.
确认后，向 `production/session-state/active.md` 写入会话检查点（若
`production/session-state/` 不存在则创建）。包含：概念名、假设、所选路径、
范围要点、当前阶段（"Phase 5 — Implement"）。这样若会话在构建途中结束，下个
会话可继续而不重新开始 —— 这对多日引擎路径工作尤其重要。

---

## Phase 5: Implement
## 阶段 5：实现

Ask: "May I create the prototype directory at `prototypes/[concept-name]-concept/`
and begin implementation?"
询问："May I create the prototype directory at `prototypes/[concept-name]-concept/`
and begin implementation?"

If yes, create the directory. Every file must begin with:
若是，创建目录。每个文件必须以下面开头：

```
// PROTOTYPE - NOT FOR PRODUCTION
// Question: [Core question being tested]
// Date: [Current date]
```

Standards are intentionally relaxed:
标准被刻意放宽：

- Hardcode values freely
- Use placeholder assets (colored rectangles, debug shapes)
- Skip error handling entirely
- Use the simplest approach that works
- Copy code rather than importing from production
- No architecture, no patterns, no abstractions
- 自由硬编码数值
- 使用占位资产（彩色矩形、调试形状）
- 完全跳过错误处理
- 用最简单可行的方法
- 复制代码而非从生产环境导入
- 无架构、无模式、无抽象

**Do not add polish.** No menus, no game over screens, no music, no tutorial text
unless the tutorial IS the mechanic being tested. Every addition beyond the
hypothesis is waste.
**不要添加打磨。** 无菜单、无游戏结束画面、无音乐、无教程文本，除非教程本身
就是被测机制。任何超出假设的添加都是浪费。

**Playtesting tip:** If you have access to anyone who hasn't seen the game —
friends, family, strangers online — watching them play without explanation gives
far better signal than testing it yourself. Watch silently; don't guide them.
Confusion is data. Ask one question after: "What was confusing?" Not "Did you
like it?"
**试玩提示：** 若您能接触到未见过此游戏的任何人 —— 朋友、家人、网上陌生人 ——
观看他们无解释地游玩，比您自己测试提供好得多的信号。静默观察；不要引导他们。
困惑就是数据。之后只问一个问题："What was confusing?" 而非 "Did you like it?"

**No external testers available?** Use rotation: if you built system A, you're a
naive tester for system B. In a two-person team this works well. Solo developer?
Step away for 2-3 days before playing fresh — you won't have perfect first-impression
signal, but you'll surface the worst blockers. Another option: play your own
prototype as a speedrun (force yourself through it in 5 minutes without stopping
to fix things) — the friction you feel is what strangers will hit.
**没有外部测试者？** 用轮换：若您构建了系统 A，您就是系统 B 的天真测试者。
在两人团队中这很有效。独立开发者？在重新游玩前离开 2-3 天 —— 您不会有完美
的第一印象信号，但会浮现最严重的阻塞点。另一选项：把自己的原型当作速通来玩
（强迫自己在 5 分钟内通关而不停下来修东西）—— 您感到的摩擦就是陌生人会撞
上的摩擦。

**Want more granular UX data?** Ask the tester to **think aloud** as they play —
narrate their thoughts in real time: "I'm pressing space... nothing happened... is
that the jump key?" This surfaces confusion the moment it happens rather than
waiting for a post-play debrief. Best for UI/UX and onboarding clarity. Silent
observation is still better for testing raw feel; think-aloud changes how people
play slightly but gives much richer data about why they're confused.
**想要更细粒度的 UX 数据？** 请测试者在游玩时**出声思考** —— 实时叙述想法：
"I'm pressing space... nothing happened... is that the jump key?" 这能在
困惑发生时立即浮现，而非等待游玩后复盘。最适合 UI/UX 与上手清晰度。对于测试
原始手感，静默观察仍更好；出声思考会略微改变人的游玩方式，但提供关于他们为何
困惑的丰富得多的数据。

**HTML prototype?** itch.io, Reddit (r/playmygame), and Discord (GMTK, Brackeys)
let you reach strangers today at zero cost — see the distribution options in the
HTML path section above.
**HTML 原型？** itch.io、Reddit（r/playmygame）和 Discord（GMTK、Brackeys）让您
今天就能零成本触达陌生人 —— 见上面 HTML 路径段落的分发选项。

**Testing AI, NPC, or complex system behavior before writing the code?** Use the
**Wizard of Oz** technique: one person plays normally while a second person secretly
controls the NPC, enemy, or system behavior in real time — making the decisions a
human would make, not an algorithm. The player believes it's automated. This lets
you validate whether your AI design *feels right* before writing a single line of
pathfinding or decision tree code. When you observe what responses the human
controller naturally produces, you learn exactly what the AI needs to do.
**在编写代码前测试 AI、NPC 或复杂系统行为？** 用**奥兹巫师**技巧：一人正常
游玩，另一人实时秘密操控 NPC、敌人或系统行为 —— 做出人会做的决策，而非算法
决策。玩家以为它是自动的。这让您在写一行寻路或决策树代码前就能验证 AI 设计
是否 *手感对*。当您观察人类操控者自然产生什么响应时，您就确切学到 AI 需要
做什么。

### Engine path: multi-turn loop
### 引擎路径：多轮循环

After writing the initial code:
写完初始代码后：

> "The prototype files are written. Run the project in your engine now.
> If there are errors, paste them here and I'll fix them. If it runs,
> describe what you see and whether it feels like it's answering the question."
> "The prototype files are written. Run the project in your engine now.
> If there are errors, paste them here and I'll fix them. If it runs,
> describe what you see and whether it feels like it's answering the question."

Iterate until the prototype is playable. Each loop:
1. User runs → reports errors or observations
2. Agent fixes errors or adjusts the mechanic
3. Repeat until playable or sunk cost rule triggers
迭代直到原型可玩。每轮：
1. 用户运行 → 报告错误或观察
2. 智能体修错或调整机制
3. 重复直到可玩或触发沉没成本规则

### HTML path: single output
### HTML 路径：单一产出

Write a single `prototype.html` to `prototypes/[concept-name]-concept/`. Include
all styles, logic, and assets inline. The file must be openable by double-clicking
with no server required.
写一个单独的 `prototype.html` 到 `prototypes/[concept-name]-concept/`。内联所有
样式、逻辑和资产。文件必须可双击打开，无需服务器。

### Paper path: document + log
### 纸上路径：文档 + 日志

Write `prototypes/[concept-name]-concept/rules.md` (the game rules) and
`prototypes/[concept-name]-concept/play-log.md` (a simulated session walking
through one complete play cycle step by step with dice rolls, decisions, and
outcomes narrated).
写 `prototypes/[concept-name]-concept/rules.md`（游戏规则）和
`prototypes/[concept-name]-concept/play-log.md`（一次模拟会话，逐步走查一个
完整游玩循环，叙述掷骰、决策和结果）。

---

## Phase 6: Playtest Debrief
## 阶段 6：试玩复盘

The prototype is built. Now hand it to the user and capture what they actually
experienced. Do NOT skip to report generation — the report is only as good as the
observations you collect here.
原型已构建。现在交给用户并捕捉他们实际体验到的内容。不要跳到报告生成 ——
报告只能与您在此收集的观察一样好。

**For HTML path:** Say exactly this:
> "The prototype is ready. Open `prototypes/[name]-concept/prototype.html` in your
> browser and play it. Take as long as you need. Don't rush through it — try to
> approach it the way a new player would. Come back here when you're done."
**对于 HTML 路径：** 准确地说：
> "The prototype is ready. Open `prototypes/[name]-concept/prototype.html` in your
> browser and play it. Take as long as you need. Don't rush through it — try to
> approach it the way a new player would. Come back here when you're done."

**For Engine path:** The multi-turn iteration loop already captured errors and
behavior. Now ask for the overall assessment:
> "Now that it's running — play through it a few times as if you're the player,
> not the developer. Come back when you have a feel for it."
**对于引擎路径：** 多轮迭代循环已经捕捉了错误和行为。现在询问整体评估：
> "Now that it's running — play through it a few times as if you're the player,
> not the developer. Come back when you have a feel for it."

**For Paper path:** Say exactly this:
> "Read through `prototypes/[name]-concept/rules.md` and walk through the
> `play-log.md` as if you're playing it for the first time. If you have someone
> nearby, try running the rules with them. Come back when you've seen at least one
> full play cycle."
**对于纸上路径：** 准确地说：
> "Read through `prototypes/[name]-concept/rules.md` and walk through the
> `play-log.md` as if you're playing it for the first time. If you have someone
> nearby, try running the rules with them. Come back when you've seen at least one
> full play cycle."

Once the user returns, ask these questions **one at a time** — wait for each answer
before asking the next:
用户返回后，**一次一个问题** —— 在问下一个之前等待每个答案：

1. **Hypothesis check:**
   > "The hypothesis was: [restate the hypothesis from Phase 1]. Did it hold up —
   > CONFIRMED, PARTIALLY CONFIRMED, or REFUTED? Tell me what you saw."
1. **假设检查：**
   > "The hypothesis was: [restate the hypothesis from Phase 1]. Did it hold up —
   > CONFIRMED, PARTIALLY CONFIRMED, or REFUTED? Tell me what you saw."

2. **Best moment:**
   > "What was the moment — if any — where it felt like it was working? Be specific."
2. **最佳时刻：**
   > "What was the moment — if any — where it felt like it was working? Be specific."

3. **Worst moment:**
   > "What was the most frustrating, confusing, or broken moment? Be specific —
   > not 'it felt slow' but 'the jump took about half a second to respond and it
   > felt like I was fighting the controls'."
3. **最差时刻：**
   > "What was the most frustrating, confusing, or broken moment? Be specific —
   > not 'it felt slow' but 'the jump took about half a second to respond and it
   > felt like I was fighting the controls'."

4. **Surprise:**
   > "Did anything happen that you didn't expect — good or bad?"
4. **意外：**
   > "Did anything happen that you didn't expect — good or bad?"

5. **Verdict:**
   > "PROCEED, PIVOT, or KILL — and one sentence why."
5. **结论：**
   > "PROCEED, PIVOT, or KILL — and one sentence why."

Collect all answers before moving to report generation. If any answer is vague
("it felt fine", "pretty good"), ask a follow-up: "Can you be more specific?
What exactly felt fine about it?" Precise observations make the report useful.
Vague ones make it useless.
在进入报告生成之前收集所有答案。若任何答案模糊（"it felt fine"、"pretty good"），
追问："Can you be more specific? What exactly felt fine about it?" 精确观察使报告
有用。模糊观察使报告无用。

---

## Phase 7: Generate Prototype Report
## 阶段 7：生成原型报告

Read `.claude/docs/templates/prototype-report.md` to get the report structure.
Fill in every section based on what was observed during this session. Replace all
placeholder text with real observations — no generic filler.
读取 `.claude/docs/templates/prototype-report.md` 获取报告结构。根据本会话
观察填充每一段。用真实观察替换所有占位文本 —— 不要通用填充。

Ask: "May I write this report to `prototypes/[concept-name]-concept/REPORT.md`?"
询问："May I write this report to `prototypes/[concept-name]-concept/REPORT.md`?"

If yes, write the file. Then update `prototypes/index.md` (create if it does not
exist) — append one row to the concept prototype table: concept name, date, path
used, verdict (PROCEED/PIVOT/KILL), and a link to the REPORT.md. If a PIVOT chain
exists (prior PIVOT-NOTE.md in a related concept folder), note the chain. This file
is the project's complete history of what was tried and what was learned.
若是，写入文件。然后更新 `prototypes/index.md`（若不存在则创建）—— 向概念
原型表追加一行：概念名、日期、所用路径、结论（PROCEED/PIVOT/KILL）以及指向
REPORT.md 的链接。若存在 PIVOT 链（相关概念文件夹中先前的 PIVOT-NOTE.md），
注明该链。此文件是项目关于已尝试和已学到内容的完整历史。

---

## Phase 8: Creative Director Review
## 阶段 8：创意总监评审

**Review mode check:**
- `solo` → skip. Note: "CD-PLAYTEST skipped — Solo mode."
- `lean` → skip. Note: "CD-PLAYTEST skipped — Lean mode."
- `full` → spawn `creative-director` via Task using gate **CD-PLAYTEST** if
  `design/gdd/game-concept.md` exists with game pillars defined. If pillars are
  not yet defined, note: "CD-PLAYTEST skipped — game pillars not yet defined at
  concept prototype stage."
**评审模式检查：**
- `solo` → 跳过。注明："CD-PLAYTEST skipped — Solo mode."
- `lean` → 跳过。注明："CD-PLAYTEST skipped — Lean mode."
- `full` → 若存在 `design/gdd/game-concept.md` 且定义了游戏支柱，则通过 Task
  使用门禁 **CD-PLAYTEST** 启动 `creative-director`。若支柱尚未定义，注明：
  "CD-PLAYTEST skipped — game pillars not yet defined at concept prototype stage."

Pass: the full REPORT.md content, the original hypothesis, and game pillars /
core fantasy from `design/gdd/game-concept.md`.
传递：完整 REPORT.md 内容、原始假设、以及来自 `design/gdd/game-concept.md`
的游戏支柱/核心幻想。

The creative director evaluates the result against the game's creative vision and
confirms, modifies, or overrides the recommendation. Their verdict is final. Update
REPORT.md if the verdict differs.
创意总监根据游戏的创意愿景评估结果，确认、修改或推翻建议。其结论为最终。
若结论不同则更新 REPORT.md。

---

## Phase 9: Summary and Next Steps
## 阶段 9：总结与下一步

Output a summary: the hypothesis, the result, and the final recommendation.
Link to `prototypes/[concept-name]-concept/REPORT.md`.
输出总结：假设、结果、最终建议。链接到
`prototypes/[concept-name]-concept/REPORT.md`。

**If PROCEED:**
Your concept prototype validated the core idea. Now design it properly, informed by
what you just learned.
**若 PROCEED：**
您的概念原型验证了核心创意。现在正式设计它，依据您刚刚学到的内容。

Recommended path (in order):
1. `/design-review design/gdd/game-concept.md` — validate the concept doc against what the prototype revealed
2. `/gate-check` — confirm readiness to advance to Systems Design
3. `/art-bible` — define visual identity (optional but worth doing before GDDs)
4. `/map-systems` — decompose the concept into all game systems
5. `/design-system [mechanic]` — GDD for each MVP system; use prototype learnings
   in the Tuning Knobs and Formulas sections
6. `/review-all-gdds` — cross-system consistency check
推荐路径（按顺序）：
1. `/design-review design/gdd/game-concept.md` —— 根据原型揭示的内容验证概念文档
2. `/gate-check` —— 确认推进到 Systems Design 的就绪性
3. `/art-bible` —— 定义视觉身份（可选但值得在 GDD 之前做）
4. `/map-systems` —— 将概念分解为所有游戏系统
5. `/design-system [mechanic]` —— 为每个 MVP 系统编写 GDD；在 Tuning Knobs 与
   Formulas 段落中使用原型学到的内容
6. `/review-all-gdds` —— 跨系统一致性检查

**Note:** If you used the HTML path and feel is still uncertain, consider running
a quick engine path prototype targeting feel before writing GDDs.
**注意：** 若您用了 HTML 路径且手感仍不确定，考虑在编写 GDD 之前运行一次针对
手感的快速引擎路径原型。

**If PIVOT:**
**若 PIVOT：**

Before routing to the next prototype, capture the carry-forward note. Ask these
two questions (plain text, one at a time):
在路由到下一个原型之前，捕捉承接说明。询问这两个问题（纯文本，一次一个）：

1. "What specifically worked in this prototype that we should preserve in the next version?"
2. "What is the single most important thing to change?"
1. "What specifically worked in this prototype that we should preserve in the next version?"
2. "What is the single most important thing to change?"

Ask: "May I write this to `prototypes/[concept-name]-concept/PIVOT-NOTE.md`?"
询问："May I write this to `prototypes/[concept-name]-concept/PIVOT-NOTE.md`?"

If yes, write the file with: original hypothesis, what to keep, what to change, and
the revised hypothesis for the next prototype. When `/prototype` is next run, check
`prototypes/` for any `PIVOT-NOTE.md` files — if found, read them and use the
revised hypothesis as the starting point rather than forming one from scratch.
若是，写入文件：原始假设、要保留的内容、要改变的内容、以及下一个原型的修订
假设。下次运行 `/prototype` 时，检查 `prototypes/` 中是否有 `PIVOT-NOTE.md`
文件 —— 若找到，读取它们并将修订假设作为起点，而非从头形成假设。

- Run `/prototype [revised-concept]` to test the adjusted direction
- Or `/brainstorm [hint]` if the concept needs more fundamental rethinking
- 运行 `/prototype [revised-concept]` 测试调整后的方向
- 若概念需要更根本的再思考，运行 `/brainstorm [hint]`

**If KILL:**
**若 KILL：**

Before moving on, run this check to confirm the verdict is sound and not temporary frustration:
继续之前，运行此检查以确认结论稳妥而非临时挫败：

- [ ] Core mechanic still unclear to testers after 2+ playtests?
- [ ] No "fun moment" (smile, laugh, or retry by choice) observed in any session?
- [ ] 3+ PIVOT iterations on the same concept with no clear improvement?
- [ ] Concept only works when heavily explained or when the dev guides the player?
- [ ] Building this feels like obligation, not excitement?
- [ ] 2+ 次试玩后测试者仍不清楚核心机制？
- [ ] 任何会话中都没有观察到 "趣味时刻"（微笑、大笑或主动重试）？
- [ ] 同一概念已 3+ 次 PIVOT 迭代但仍无清晰改进？
- [ ] 概念只有在大量解释或开发者引导玩家时才可行？
- [ ] 构建它感觉像义务，而非兴奋？

If 2+ boxes apply → KILL verdict is sound. If 0–1 apply → consider one more focused PIVOT before killing.
若 2+ 项适用 → KILL 结论稳妥。若 0-1 项适用 → 在 KILL 之前考虑再做一次聚焦的
PIVOT。

**Document the kill in `prototypes/GRAVEYARD.md`** (create if it doesn't exist).
Ask: "May I append this concept to `prototypes/GRAVEYARD.md`?" If yes, add one entry:
**在 `prototypes/GRAVEYARD.md` 中记录 kill**（若不存在则创建）。询问：
"May I append this concept to `prototypes/GRAVEYARD.md`?" 若是，添加一条：

```
## [Concept Name] — YYYY-MM-DD
- **Kill reason:** [specific blocker — not "it was boring" but "players never understood the core action"]
- **What worked:** [2-3 things worth carrying forward to future concepts]
- **What failed:** [the specific mechanic, design decision, or scope issue]
- **Next time:** [one explicit action to try differently on a similar concept]
```

This file exists so the same mistake doesn't get made twice on the next concept.
此文件存在是为了让相同错误不在下一个概念上重蹈覆辙。

- Run `/brainstorm open` or `/brainstorm [new-hint]` to explore a different concept
- The prototype report is the deliverable — no further action needed
- 运行 `/brainstorm open` 或 `/brainstorm [new-hint]` 探索不同概念
- 原型报告即交付物 —— 无需进一步行动

---

---

## Spike Mode
## Spike 模式

**Triggered by:** `--spike` flag OR "Mid-production spike" entry choice in Phase 1.
**触发条件：** `--spike` 标志或阶段 1 中的 "Mid-production spike" 入口选项。

**Purpose:** Test a specific technical or design question mid-production, without
the overhead of a full concept prototype workflow. No GDD prerequisites. No phase
gate implications. Hard cap: ~4 hours.
**目的：** 在制作中期测试特定技术或设计问题，无需完整概念原型工作流的开销。
无 GDD 前置要求。无阶段门禁含义。硬上限：约 4 小时。

**When to use:**
- You're in Production and want to test whether a new mechanic should be added
- You're unsure if a technical approach will work before building it properly
- A design change is being considered and you want a quick before/after comparison
- A GDD system is proving harder than expected and you want to prototype the hard part
- You need to confirm target hardware can sustain the required framerate before writing gameplay code (**performance spike** — see below)
**何时使用：**
- 您在 Production 中并想测试是否应添加某个新机制
- 您不确定某个技术方法在正式构建之前是否可行
- 正考虑一项设计变更，您想做一次快速前后对比
- 某 GDD 系统比预期更难，您想原型化难点部分
- 您需要在编写玩法代码之前确认目标硬件能维持所需帧率（**性能 spike** —— 见下）

**Spike Mode workflow (replaces Phases 1–9):**
**Spike 模式工作流（替代阶段 1-9）：**

1. **Define the spike question** (plain text, not a widget): "What specific question does this spike answer? Give me one sentence: 'Can we [do X] using [approach Y]?'"
1. **定义 spike 问题**（纯文本，非组件）："What specific question does this spike answer? Give me one sentence: 'Can we [do X] using [approach Y]?'"

2. **Choose path** — same AskUserQuestion widget as Phase 3 (HTML / Engine / Paper).
2. **选择路径** —— 同阶段 3 的 AskUserQuestion 组件（HTML / 引擎 / 纸上）。

3. **Scope** — maximum 2-3 bullet points. One mechanic, one technical question, nothing else.
3. **范围** —— 最多 2-3 个要点。一个机制、一个技术问题，无其他。

4. **Build** — same relaxed standards as concept prototype. Hard cap: 4 hours. If not demonstrable in 4 hours, the question is too large. Split it.
4. **构建** —— 同概念原型的放宽标准。硬上限：4 小时。若 4 小时内不可演示，则
   问题太大。拆分它。

5. **Observe and decide** — no formal playtest debrief. Ask: "Did the spike answer the question? YES or NO, and why in one sentence."
5. **观察并决策** —— 无正式试玩复盘。询问："Did the spike answer the question? YES or NO, and why in one sentence."

6. **Write a spike note** (not a full report) to `prototypes/[concept-name]-spike-[date]/SPIKE-NOTE.md`:
   - Question tested
   - Result (YES it works / NO it doesn't / PARTIAL — needs more investigation)
   - What to do next (add to current sprint / investigate further / abandon the idea)
6. **写 spike 笔记**（非完整报告）到 `prototypes/[concept-name]-spike-[date]/SPIKE-NOTE.md`：
   - 测试的问题
   - 结果（YES 可行 / NO 不可行 / PARTIAL —— 需更多调研）
   - 下一步（加入当前冲刺 / 进一步调研 / 放弃该想法）

7. **Update `production/session-state/active.md`** to clear the spike and return to the current sprint state.
7. **更新 `production/session-state/active.md`** 清除 spike 并返回当前冲刺状态。

**No CD gate. No phase gate. No PROCEED/PIVOT/KILL.** Spike results inform decisions; they don't make them. The developer decides whether to add the mechanic/approach to the sprint backlog based on what the spike revealed.
**无 CD 门禁。无阶段门禁。无 PROCEED/PIVOT/KILL。** Spike 结果用于决策；不
代为决策。开发者根据 spike 揭示的内容决定是否将该机制/方法加入冲刺待办。

**Performance spike (special case):** If the game involves demanding rendering —
large open worlds, hundreds of simultaneous physics bodies, heavy particle systems,
complex shaders — run a performance spike before writing gameplay code to confirm
the target hardware can sustain the required framerate. This is distinct from other
spikes in two ways:
- The question is "can the engine render [scene X] at 60fps on [minimum spec hardware]?"
  not "does this mechanic feel good?"
- The output is a benchmark number, not a feel verdict
- No gameplay logic is needed — just the maximum intended scene load (terrain, draw
  calls, physics objects, particles) running at once
- Build time stays within the ~4-hour cap; the spike is setting up the rendering
  load, not the game
- If the answer is NO at this scope, this is an architecture or scope constraint
  that affects everything downstream — better to surface it now than during Sprint 8
**性能 spike（特殊情况）：** 若游戏涉及重度渲染 —— 大型开放世界、数百个同时
物理体、重型粒子系统、复杂着色器 —— 在编写玩法代码之前运行性能 spike 以确认
目标硬件能维持所需帧率。这与其他 spike 在两方面不同：
- 问题是 "引擎能否在 [最低规格硬件] 上以 60fps 渲染 [场景 X]？" 而非 "此机制
  手感好吗？"
- 输出是基准数值，而非手感结论
- 无需玩法逻辑 —— 只需同时运行最大预期场景负载（地形、绘制调用、物理对象、
  粒子）
- 构建时间仍在约 4 小时上限内；spike 是设置渲染负载，而非游戏
- 若在此范围内答案是 NO，则这是一项影响下游一切的架构或范围约束 —— 早暴露
  比在第 8 个冲刺才暴露好

---

### Important Constraints
### 重要约束

- Prototype code must NEVER import from production source files
- Production code must NEVER import from prototype directories
- If the recommendation is PROCEED, production implementation is written from
  scratch — prototype code is never refactored into production
- Total effort is hard-capped at 1 day (concept prototypes test one mechanic)
- Test ONE mechanic — if scope grows, stop and simplify the question
- No polish. No menus, no game over, no music, no UI unless it IS the mechanic
- If stuck after 2 hours of engine iteration, reframe the question or switch paths
- **3 PIVOT iterations → force a KILL decision.** If this is the third time the
  same concept has produced a PIVOT verdict, the concept likely doesn't work.
  Ask: "Is this the right idea, or am I in the sunk cost trap?" A new concept
  prototyped fresh will almost always beat a fourth iteration of a struggling one.
- Building 2-3 different concept variants and picking the best one is a healthier
  strategy than iterating one concept to death. Natural selection between prototypes
  beats willpower.
- **Networked/multiplayer games:** A local prototype cannot validate the feel of a
  networked mechanic. Latency fundamentally changes how combat, movement, and
  prediction feel — a prototype running at 0ms local will feel entirely different at
  80ms network delay. Use a local prototype to validate that the mechanic is
  *interesting*. Do not use it as evidence that it *feels good* under real network
  conditions. Network feel requires real peers or simulated latency (e.g., throttle
  tools, network condition simulators).
- 原型代码绝不能从生产源码导入
- 生产代码绝不能从原型目录导入
- 若建议是 PROCEED，生产实现从零编写 —— 原型代码绝不重构进生产
- 总投入硬上限 1 天（概念原型测试一个机制）
- 测试一个机制 —— 若范围扩大，停下并简化问题
- 无打磨。无菜单、无游戏结束、无音乐、无 UI，除非 UI 即机制
- 若引擎迭代 2 小时后卡住，重新构造问题或切换路径
- **3 次 PIVOT 迭代 → 强制 KILL 决策。** 若同一概念第三次产出 PIVOT 结论，
  该概念很可能行不通。询问："Is this the right idea, or am I in the sunk cost
  trap?" 全新原型化一个新概念几乎总能胜过挣扎概念的第四次迭代。
- 构建 2-3 个不同概念变体并挑最好的，比把一个概念迭代到死更健康。原型间的
  自然选择胜过意志力。
- **联网/多人游戏：** 本地原型无法验证联网机制的手感。延迟从根本上改变战斗、
  移动和预测的手感 —— 0ms 本地运行的原型在 80ms 网络延迟下手感完全不同。用
  本地原型验证机制是否 *有趣*。不要用它作为在真实网络条件下 *手感好* 的证据。
  网络手感需要真实对端或模拟延迟（如限速工具、网络条件模拟器）。
