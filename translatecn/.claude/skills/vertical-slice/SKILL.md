---
name: vertical-slice
description: "Pre-Production validation — build a production-quality end-to-end build to confirm the full game loop is achievable before committing to Production. Run after GDDs, architecture, and UX specs are complete. Produces a PROCEED/PIVOT/KILL verdict that gates the Pre-Production → Production transition."
argument-hint: "[--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model: sonnet
agent: prototyper
isolation: worktree
---
---
名称：vertical-slice
描述："前期制作验证 — 构建生产质量的端到端版本，以确认在投入制作之前完整游戏循环是可行的。在 GDD、架构和 UX 规范完成后运行。产生 PROCEED/PIVOT/KILL 裁决，门禁前期制作 → 制作的过渡。"
argument-hint："[--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion
model：sonnet
agent：prototyper
isolation：worktree
---

## Purpose
## 目的

The **vertical slice** answers a different question from the concept prototype:
*"Can we build this full game loop at production quality, on schedule?"*
**垂直切片**回答的是与概念原型不同的问题：
*"我们能否按时以生产质量构建这个完整的游戏循环？"*

**Default use** — run late in Pre-Production, after GDDs, architecture, and UX
specs are complete. It is a near-production-quality build demonstrating one complete
[start → challenge → resolution] cycle.
**默认用途** — 在前期制作后期运行，在 GDD、架构和 UX
规范完成后。它是一个接近生产质量的构建，展示一个完整的
[开始 → 挑战 → 解决] 循环。

**Post-pivot?** If a PIVOT verdict from an earlier vertical slice sent you back to
revise GDDs and architecture, run this again after revisions to re-validate. It can
be run as many times as needed until a PROCEED or KILL verdict is reached.
**pivot 之后？** 如果早期垂直切片的 PIVOT 裁决让你回去
修订 GDD 和架构，修订后重新运行以再次验证。它可以
按需运行多次，直到达到 PROCEED 或 KILL 裁决。

It validates:
它验证：

1. The pipeline (can the team actually produce this quality of content?)
2. Execution feasibility (are the architecture decisions correct for this game?)
3. Fun survival (does the fun from the concept prototype survive full design?)
4. **Velocity** (how long did this take? That's your real production rate estimate.)
1. 管线（团队能否实际产出这种质量的内容？）
2. 执行可行性（架构决策对这款游戏是否正确？）
3. 趣味存续（概念原型的乐趣在完整设计后是否存续？）
4. **速度**（这花了多长时间？这就是你真正的生产速率估算。）

**Earlier in the project?** If you haven't written GDDs yet and want to validate
whether the core idea is worth designing, run `/prototype` (concept prototype) instead.
**项目更早期？** 如果你还没写 GDD 且想验证
核心想法是否值得设计，请改用 `/prototype`（概念原型）。

---

## Phase 1: Resolve Review Mode and Load Context
## 阶段 1：解析评审模式并加载上下文

Resolve the review mode:
解析评审模式：

1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`
1. 如果传入了 `--review [full|lean|solo]` → 使用该模式
2. 否则读取 `production/review-mode.txt` → 使用其中的值
3. 否则 → 默认为 `lean`

See `.claude/docs/director-gates.md` for the full check pattern.
有关完整的检查模式，请参见 `.claude/docs/director-gates.md`。

Read the following files to understand the full design intent:
读取以下文件以了解完整的设计意图：

- `CLAUDE.md` — tech stack and engine
- `design/gdd/game-concept.md` — core fantasy and game pillars
- `design/gdd/systems-index.md` — MVP systems and their priorities
- `docs/architecture/architecture.md` — layer structure
- `docs/architecture/control-manifest.md` — technical rules for implementation
- Key GDDs for the systems being sliced
- `CLAUDE.md` — 技术栈和引擎
- `design/gdd/game-concept.md` — 核心幻想和游戏支柱
- `design/gdd/systems-index.md` — MVP 系统及其优先级
- `docs/architecture/architecture.md` — 层级结构
- `docs/architecture/control-manifest.md` — 实施的技术规则
- 被切片系统的关键 GDD

---

## Phase 2: Define the Slice Scope and Validation Question
## 阶段 2：定义切片范围和验证问题

Before building, define the **falsifiable validation question**:
在构建之前，定义**可证伪的验证问题**：

> *"Does a player, starting from nothing, experience [core fantasy from game-concept.md]
> within [N] minutes, without developer guidance — and can we build one such loop
> in [X] days at representative quality?"*
> *"玩家从零开始，在没有开发者引导的情况下，能否在 [N] 分钟内体验到
> [game-concept.md 中的核心幻想] — 我们能否在 [X] 天内以代表性质量
> 构建一个这样的循环？"*

Both parts matter: player experience AND build feasibility.
两部分都重要：玩家体验和构建可行性。

**Scope discipline:**
- Include ALL core loop systems (minimum). If a system is required to complete one
  [start → challenge → resolution] cycle, it must be in the slice.
- **Target scope: 3–5 minutes of polished, continuous gameplay.** This is the
  industry-standard vertical slice length — long enough to demonstrate mechanics
  and tone, short enough to build at representative quality. If your slice would
  take longer than 5 minutes to play through, cut content, not quality.
- **Cut scope before cutting quality.** A low-quality slice that looks nothing like
  the intended game cannot validate production feasibility.
- If the scope feels too large to build in 1–3 weeks, the slice scope is wrong —
  not too big to build, but the slice is trying to prove too much at once.
**范围纪律：**
- 包含所有核心循环系统（最低限度）。如果某个系统是完成一个
  [开始 → 挑战 → 解决] 循环所必需的，它必须在切片中。
- **目标范围：3-5 分钟打磨过的、连续的游戏玩法。** 这是
  行业标准的垂直切片长度 — 足够长以展示机制
  和基调，足够短以代表性质量构建。如果你的切片
  需要超过 5 分钟才能玩完，削减内容，而非质量。
- **先削减范围，再削减质量。** 看起来与
  预期游戏毫无相似之处的低质量切片无法验证生产可行性。
- 如果范围感觉太大而无法在 1-3 周内构建，则切片范围是错误的 —
  不是太大无法构建，而是切片试图一次证明太多。

**Scope creep warning:** The vertical slice is the highest-risk moment for scope
creep in the pre-production phase. Features feel "almost there" and it's tempting
to add "just one more system." Resist this. Cut, do not extend.
**范围蔓延警告：** 垂直切片是前期制作阶段范围蔓延
风险最高的时刻。功能感觉"快完成了"，很容易诱惑你
添加"再来一个系统"。抵制这种诱惑。削减，而非扩展。

Present scope to the user before building and get confirmation.
在构建之前向用户呈现范围并获得确认。

---

## Phase 3: Plan the Build
## 阶段 3：规划构建

Define in bullet points:
以要点形式定义：

- Systems implemented (which GDD sections are being exercised)
- The complete game loop cycle ([start] → [challenge] → [resolution] exactly)
- Art and audio quality level (placeholder acceptable, representative preferred)
- Specific, measurable success criteria for the validation question
- Hard time limit: [X] days. If exceeded, scope was wrong — stop and reassess.
- 实施的系统（正在演练哪些 GDD 部分）
- 完整的游戏循环周期（确切地 [开始] → [挑战] → [解决]）
- 美术和音频质量水平（可接受占位符，优先代表性）
- 验证问题的具体、可衡量成功标准
- 硬性时间限制：[X] 天。如果超出，则范围错误 — 停止并重新评估。

Ask the user to confirm scope before building.
要求用户在构建之前确认范围。

Once confirmed, write a session checkpoint to `production/session-state/active.md`
(create `production/session-state/` if it does not exist). Include: concept name,
validation question, systems in scope, art quality level, and current phase ("Phase
4 — Implement"). Update this file at the end of each build day with what was
completed. This is the primary recovery mechanism if the session ends mid-slice —
multi-week Engine builds will span many sessions.
确认后，向 `production/session-state/active.md` 写入会话检查点
（如果 `production/session-state/` 不存在则创建）。包括：概念名、
验证问题、范围内的系统、美术质量水平和当前阶段（"Phase
4 — Implement"）。在每个构建日结束时用已完成的内容
更新此文件。这是会话在切片中途结束时的主要恢复机制 —
多周的引擎构建将跨越多个会话。

---

## Phase 4: Implement
## 阶段 4：实施

Ask: "May I create the vertical slice directory at
`prototypes/[concept-name]-vertical-slice/` and begin implementation?"
询问："我可以在
`prototypes/[concept-name]-vertical-slice/` 创建垂直切片目录并开始实施吗？"

If yes, create the directory. Every file must begin with:
如果是，创建目录。每个文件必须以以下内容开头：

```
// VERTICAL SLICE - NOT FOR PRODUCTION
// Validation Question: [What this build is proving]
// Date: [Current date]
```

**Quality standards** — higher than concept prototype, not full production:
- Follow architecture layers from `docs/architecture/control-manifest.md`
- Naming conventions from `.claude/docs/technical-preferences.md`
- No hardcoded gameplay values — use constants or config files
- Basic error handling on critical paths
- Placeholder art acceptable; representative art preferred
**质量标准** — 高于概念原型，未达完整生产：
- 遵循 `docs/architecture/control-manifest.md` 的架构层级
- `.claude/docs/technical-preferences.md` 的命名约定
- 无硬编码游戏玩法数值 — 使用常量或配置文件
- 关键路径上的基本错误处理
- 可接受占位美术；优先代表性美术

**Multi-turn loop:** After writing the initial files, ask the user to run the
build and report what they observe. Iterate until the complete game loop cycle
is demonstrable. Each round:
**多轮循环：** 写入初始文件后，要求用户运行
构建并报告观察结果。迭代直到完整的游戏循环周期
可演示。每轮：

1. User runs → reports errors or observations
2. Agent fixes errors or adjusts systems
3. Repeat until the full [start → challenge → resolution] cycle is playable
1. 用户运行 → 报告错误或观察结果
2. 智能体修复错误或调整系统
3. 重复直到完整的 [开始 → 挑战 → 解决] 循环可玩

**Sunk cost checkpoint (day 3 of planned timeline):** If the full game loop cycle
is not yet demonstrable, stop and reassess. Either the scope is too large or an
architectural assumption is wrong. Surface the blocker explicitly rather than
continuing to iterate.
**沉没成本检查点（计划时间线的第 3 天）：** 如果完整游戏循环周期
尚不可演示，停止并重新评估。要么范围太大，要么
架构假设错误。明确暴露阻塞问题，而非
继续迭代。

Conduct at least 1 playtest session once the loop is demonstrable.
一旦循环可演示，至少进行 1 次试玩会话。

**Playtesting tip:** If you can get anyone who hasn't seen the game to play it —
a friend, family member, online community — watch them silently without explaining
anything. Don't guide them. Their confusion reveals what the game isn't
communicating on its own. This gives much better signal than self-testing.
**试玩提示：** 如果你能让任何没看过这款游戏的人来玩 —
朋友、家人、在线社区 — 默默地看着他们，不解释
任何东西。不要引导他们。他们的困惑揭示了游戏自身
未能传达的内容。这比自测提供更好的信号。

**No external testers available?** Use rotation within the team: Dev A built
system X, so Dev A is a naive tester for system Y. Even a two-person team can
rotate effectively. Solo? Step away for 2-3 days then play through as a new
player — you won't have perfect first-impression signal but you'll catch the
critical blockers. Also try a "silent walkthrough": play your own slice in one
sitting without stopping to fix anything and log every moment you hesitate.
**没有外部测试者？** 在团队内轮换：开发者 A 构建了
系统 X，所以开发者 A 是系统 Y 的新手测试者。即使是两人团队也能
有效轮换。独立开发？离开 2-3 天然后以新
玩家身份玩通 — 你不会有完美的第一印象信号，但你会抓住
关键阻塞问题。还可以尝试"无声通关"：一次性
玩完你自己的切片，不停下来修复任何东西，记录你犹豫的每一刻。

**Want richer observation data?** Ask the tester to **think aloud** as they play —
narrate what they're doing and why in real time. "I'm trying to figure out how to
attack... I pressed E... nothing... is it click?" This surfaces confusion the
instant it occurs rather than in retrospect. Best for onboarding and UI clarity
validation. Silent observation is still better for feel testing; think-aloud
changes the experience slightly but produces far more granular UX data.
**想要更丰富的观察数据？** 要求测试者在游玩时**出声思考** —
实时叙述他们在做什么以及为什么。"我在想办法怎么
攻击……我按了 E……没反应……是点击吗？"这在困惑
发生的瞬间就暴露出来，而非事后回忆。最适合新手引导和 UI 清晰度
验证。无声观察对于手感测试仍然更好；出声思考
会略微改变体验，但产生更细粒度的 UX 数据。

**Async remote option:** Record a Loom or OBS session — give someone the build,
ask them to record their screen + audio, and send you the video. You get genuine
first-impression data without synchronous scheduling. Works across timezones.
**异步远程选项：** 录制 Loom 或 OBS 会话 — 给某人构建版本，
要求他们录制屏幕 + 音频，并发送视频给你。你无需同步调度即可获得
真实的第一印象数据。跨时区有效。

**Testing AI, NPC, or complex system behavior before it's fully implemented?** Use the
**Wizard of Oz** technique: one person plays normally while a second person secretly
controls the NPC or system behavior in real time. The player believes it's automated.
This validates the *design intent* of an AI or economy system before the implementation
is complete — and reveals exactly what behaviors the system must produce to feel correct.
Particularly useful for vertical slices where an AI system is in scope but not yet
polished enough for unguided testing.
**在完全实施之前测试 AI、NPC 或复杂系统行为？** 使用
**Wizard of Oz** 技术：一个人正常游玩，另一个人秘密地
实时控制 NPC 或系统行为。玩家相信这是自动的。
这验证了 AI 或经济系统的*设计意图* — 并准确揭示了系统必须产生什么行为才能感觉正确。
在实施完成之前
对于 AI 系统在范围内但尚未打磨到可进行无引导测试的垂直切片特别有用。

---

## Phase 5: Playtest Debrief
## 阶段 5：试玩复盘

The loop is demonstrable. Before writing the report, collect structured observations
from actually playing it. Do NOT skip to report generation — the report is only as
good as the observations you capture here.
循环可演示。在写报告之前，从实际游玩中收集结构化观察。
不要跳到报告生成 — 报告仅与
你在此处捕获的观察同样好。

Say exactly this:
确切地说：

> "Play through the complete [start → challenge → resolution] cycle from scratch,
> as if you're a new player with no knowledge of how it was built. Don't skip ahead
> or use developer shortcuts. Come back when you've completed the full loop —
> or when you've hit something that stopped you."
> "从零开始玩通完整的 [开始 → 挑战 → 解决] 循环，
> 就像你是一个对构建方式一无所知的新玩家。不要跳过
> 或使用开发者快捷方式。完成完整循环后回来 —
> 或者当你遇到阻止你的东西时回来。"

Once the user returns, ask these questions **one at a time**:
用户返回后，**逐一**提出这些问题：

1. **Loop completion:**
   > "Did you complete the full [start → challenge → resolution] cycle on your own,
   > without needing any guidance from me or prior knowledge of the build?"
1. **循环完成：**
   > "你是否自己完成了完整的 [开始 → 挑战 → 解决] 循环，
   > 不需要我的任何引导或对构建的先验知识？"

2. **Time check:**
   > "How long did it take to reach the first meaningful action — the first moment
   > where you felt like you were actually playing the game?"
2. **时间检查：**
   > "到达第一个有意义的动作花了多长时间 — 你感觉
   > 自己真正在玩游戏的第一个瞬间？"

3. **Core fantasy:**
   > "The game is supposed to make you feel [core fantasy from game-concept.md].
   > Did it? Be honest — not 'kind of' but specifically what you felt and when."
3. **核心幻想：**
   > "这款游戏应该让你感到 [game-concept.md 中的核心幻想]。
   > 它做到了吗？诚实回答 — 不是'有点'，而是具体你感受到了什么以及何时。"

4. **Blockers:**
   > "What stopped you, confused you, or pulled you out of the experience? Any
   > moment where you weren't sure what to do, or where something broke?"
4. **阻塞问题：**
   > "什么阻止了你、让你困惑或让你脱离了体验？任何
   > 你不确定该做什么或某些东西出错的时刻？"

5. **Pipeline check:**
   > "As the developer — not the player — does this feel achievable at this quality
   > for the full game? What surprised you about how long things took to build?"
5. **管线检查：**
   > "作为开发者 — 而非玩家 — 这种质量对于完整游戏
   > 是否感觉可实现？关于构建花费的时间，什么让你惊讶？"

6. **Verdict:**
   > "PROCEED, PIVOT, or KILL — and the specific reason."
6. **裁决：**
   > "PROCEED、PIVOT 还是 KILL — 以及具体原因。"

If any answer is vague, ask: "Can you give me the specific moment where that happened?"
Precise observations populate the report. Vague ones produce a useless report.
如果任何回答模糊，询问："你能告诉我具体发生在哪个时刻吗？"
精确的观察填充报告。模糊的观察产生无用的报告。

---

## Phase 6: Generate Vertical Slice Report
## 阶段 6：生成垂直切片报告


Track velocity throughout the build. Log:
在整个构建过程中跟踪速度。记录：

- Day 1: what was built
- Day 2: what was built
- etc.
- 第 1 天：构建了什么
- 第 2 天：构建了什么
- 等

This is the most honest data you will ever have about your production rate. Do not
skip it. It feeds directly into sprint planning.
这是你将拥有的关于生产速率的最诚实的数据。不要
跳过。它直接输入冲刺规划。

Read `.claude/docs/templates/vertical-slice-report.md` to get the report structure.
If the template file is not found, use this fallback structure:
读取 `.claude/docs/templates/vertical-slice-report.md` 获取报告结构。
如果未找到模板文件，使用此回退结构：

- `## Vertical Slice Report — [Game Title] — [Date]`
- `### Executive Summary` (PROCEED / PIVOT / STOP verdict + 2-sentence rationale)
- `### Core Loop Validation` (what was tested, what passed, what failed)
- `### Feel Assessment` (animation, controls, feedback — subjective notes)
- `### Technical Findings` (performance, engine issues, architectural risks)
- `### Velocity Log` (day-by-day actual progress — do not skip)
- `### Recommended Next Steps`
- `## Vertical Slice Report — [游戏标题] — [日期]`
- `### Executive Summary`（PROCEED / PIVOT / STOP 裁决 + 2 句话理由）
- `### Core Loop Validation`（测试了什么、通过了什么、失败了什么）
- `### Feel Assessment`（动画、控制、反馈 — 主观备注）
- `### Technical Findings`（性能、引擎问题、架构风险）
- `### Velocity Log`（逐日实际进度 — 不要跳过）
- `### Recommended Next Steps`

Fill in every section based on what was observed and built during this session.
The velocity log must reflect actual day-by-day progress, not estimates — this is
the most honest production rate data you will ever have. Replace all placeholder
text with real observations.
根据本次会话中观察和构建的内容填写每个部分。
速度日志必须反映实际的逐日进度，而非估算 — 这是你
将拥有的最诚实的生产速率数据。用真实观察替换所有占位
文本。

### Lessons Learned
### 经验教训

- What assumptions were broken by actually building to near-production quality?
- What surprised us about the pipeline or architecture?
- What would we change about the slice scope if we ran this again?
- 实际以接近生产质量构建打破了哪些假设？
- 关于管线或架构有什么让我们惊讶的？
- 如果再次运行，我们会如何改变切片范围？

```

Ask: "May I write this report to
`prototypes/[concept-name]-vertical-slice/REPORT.md`?"
询问："我可以将此报告写入
`prototypes/[concept-name]-vertical-slice/REPORT.md` 吗？"

If yes, write the file. Then update `prototypes/index.md` (create if it does not
exist) — append one row to the vertical slice table: concept name, date, verdict,
and a link to the REPORT.md. Note whether this was a first-run slice or a re-run
after a PIVOT. The velocity log in this report is some of the most valuable data in
the project — cross-reference it with sprint estimates.
如果是，写入文件。然后更新 `prototypes/index.md`（如果不存在
则创建） — 向垂直切片表追加一行：概念名、日期、裁决，
以及指向 REPORT.md 的链接。注意这是首次运行切片还是 PIVOT 后
的重新运行。此报告中的速度日志是项目中
最有价值的数据之一 — 将其与冲刺估算交叉引用。

---

## Phase 7: Creative Director Review
## 阶段 7：创意总监评审

**Review mode check:**
- `solo` → skip. Note: "CD-PLAYTEST skipped — Solo mode."
- `lean` → skip (not a PHASE-GATE). Note: "CD-PLAYTEST skipped — Lean mode."
- `full` → spawn `creative-director` via Task using gate **CD-PLAYTEST**
  (`.claude/docs/director-gates.md`).
**评审模式检查：**
- `solo` → 跳过。备注："CD-PLAYTEST skipped — Solo mode."
- `lean` → 跳过（非 PHASE-GATE）。备注："CD-PLAYTEST skipped — Lean mode."
- `full` → 通过 Task 使用门禁 **CD-PLAYTEST**
  （`.claude/docs/director-gates.md`）生成 `creative-director`。

Pass: the full REPORT.md content, the validation question, game pillars and core
fantasy from `design/gdd/game-concept.md`.
传递：完整 REPORT.md 内容、验证问题、游戏支柱和
来自 `design/gdd/game-concept.md` 的核心幻想。

The creative director evaluates the vertical slice result against the game's
creative vision and pillars, then confirms, modifies, or overrides the
recommendation. Their verdict is final. Update REPORT.md if the verdict differs.
创意总监根据游戏的创意愿景和支柱评估垂直切片结果，
然后确认、修改或推翻
建议。其裁决是最终决定。如果裁决不同，更新 REPORT.md。

---

## Phase 8: Summary and Next Steps
## 阶段 8：摘要和后续步骤

Output a summary: the validation question, velocity data, and final recommendation.
Link to `prototypes/[concept-name]-vertical-slice/REPORT.md`.
输出摘要：验证问题、速度数据和最终建议。
链接到 `prototypes/[concept-name]-vertical-slice/REPORT.md`。

**If PROCEED:**
Your vertical slice validated the full game loop. The project is ready for
Production.
**如果 PROCEED：**
你的垂直切片验证了完整的游戏循环。项目已准备好
进入制作。

Recommended next steps:
推荐后续步骤：

- `/create-epics layer:foundation` — plan Foundation layer epics
- `/create-epics layer:core` — plan Core layer epics
- `/create-stories [epic-slug]` — break each epic into implementable stories
- `/sprint-plan` — plan the first sprint using velocity data from the slice
- `/gate-check pre-production` — formally advance the stage to Production
- `/create-epics layer:foundation` — 规划基础层史诗
- `/create-epics layer:core` — 规划核心层史诗
- `/create-stories [epic-slug]` — 将每个史诗分解为可实施的故事
- `/sprint-plan` — 使用切片的速度数据规划第一个冲刺
- `/gate-check pre-production` — 正式将阶段推进到制作

**Playtest note:** `/gate-check` will look for documented playtest evidence.
At minimum, 1 documented session with a REPORT.md showing PROCEED is required
to pass the gate. More sessions give more reliable signal — 3+ is recommended
before committing the full team to Production, but is not a hard gate.
**试玩注意：** `/gate-check` 将查找记录的试玩证据。
至少需要 1 次记录的会话，REPORT.md 显示 PROCEED 才能
通过门禁。更多会话提供更可靠的信号 — 在投入
整个团队到制作之前推荐 3 次以上，但非硬性门禁。

**If PIVOT:**
**如果 PIVOT：**

Before routing back to GDD revision, capture the carry-forward note. Ask these
two questions (plain text, one at a time):
在路由回 GDD 修订之前，捕获延续备注。提出这两个
问题（纯文本，逐一）：

1. "What systems or mechanics worked at this quality level and should be preserved in the revised design?"
2. "What specifically failed — the core loop, the architecture, the pipeline, or the fun?"
1. "哪些系统或机制在此质量水平下有效，应在修订设计中保留？"
2. "具体什么失败了 — 核心循环、架构、管线还是趣味？"

Ask: "May I write this to `prototypes/[concept-name]-vertical-slice/PIVOT-NOTE.md`?"
询问："我可以将此写入 `prototypes/[concept-name]-vertical-slice/PIVOT-NOTE.md` 吗？"

If yes, write the file with: what worked, what failed, the specific systems or
architecture decisions that need revision, and what the next slice should prove
differently. When `/vertical-slice` is next run after a PIVOT, check the
`prototypes/` directory for a `PIVOT-NOTE.md` — use it to frame the new validation
question and inform scope decisions.
如果是，写入文件：什么有效、什么失败、需要修订的具体系统或
架构决策，以及下一个切片应证明
什么不同的内容。当 PIVOT 后下次运行 `/vertical-slice` 时，检查
`prototypes/` 目录中的 `PIVOT-NOTE.md` — 用它来构建新的验证
问题并为范围决策提供信息。

- Revise affected GDDs with `/design-system [mechanic]`
- Address architecture issues via `/architecture-decision`
- Then re-run `/vertical-slice` to validate the revised direction
- 使用 `/design-system [mechanic]` 修订受影响的 GDD
- 通过 `/architecture-decision` 解决架构问题
- 然后重新运行 `/vertical-slice` 验证修订方向

**If KILL:**
**如果 KILL：**

Before abandoning the concept, confirm the verdict is sound:
在放弃概念之前，确认裁决是否合理：

- [ ] Full game loop takes >5 minutes even for an experienced player?
- [ ] No emotional high point (delight, surprise, satisfaction) observed in any playtest session?
- [ ] 50%+ of testers confused or stuck at the same point after 2+ slice attempts?
- [ ] Architecture issues would require rebuilding more than 50% of what was built?
- [ ] This is the 3rd vertical slice attempt on the same concept?
- [ ] 即使对有经验的玩家，完整游戏循环也需要超过 5 分钟？
- [ ] 在任何试玩会话中未观察到情感高点（愉悦、惊喜、满足）？
- [ ] 2 次以上切片尝试后，50% 以上的测试者在同一处困惑或卡住？
- [ ] 架构问题需要重建超过 50% 的已构建内容？
- [ ] 这是同一概念的第 3 次垂直切片尝试？

If 2+ boxes apply → KILL verdict is sound. If 0–1 apply → one targeted PIVOT may recover the concept.
如果 2 个以上勾选 → KILL 裁决合理。如果 0-1 个勾选 → 一次有针对性的 PIVOT 可能挽救概念。

**Document the kill in `prototypes/GRAVEYARD.md`** (create if it doesn't exist).
Ask: "May I append this to `prototypes/GRAVEYARD.md`?" If yes, add one entry:
**在 `prototypes/GRAVEYARD.md` 中记录废弃**（如果不存在则创建）。
询问："我可以将此追加到 `prototypes/GRAVEYARD.md` 吗？" 如果是，添加一条：

```
## [Concept Name] Vertical Slice — YYYY-MM-DD
- **Kill reason:** [what specifically prevented the player from experiencing the core fantasy]
- **What worked at slice quality:** [systems or mechanics that held up]
- **What failed:** [core loop issue, architecture decision, or pipeline blocker]
- **Next time:** [one specific change for the next time a similar concept is attempted]
```

- Return to `/brainstorm` with what you learned
- Or run `/prototype [new-concept]` to test a new direction cheaply first
- 带着你学到的返回 `/brainstorm`
- 或运行 `/prototype [new-concept]` 先低成本测试新方向

---

### Important Constraints
### 重要约束

- Vertical slice code must NEVER be refactored into production — it is reference only
- Production code must NEVER import from `prototypes/`
- If recommendation is PROCEED, production implementation is written from scratch
  using the slice as a design reference only
- Scope cuts are acceptable; quality cuts are not — a low-quality slice proves nothing
- Total effort: 1–3 weeks. If longer, scope is too large — cut the slice, not the quality.
- Day 3 sunk cost rule: if the full game loop cycle is not demonstrable by then,
  stop and surface the blocker
- **Networked/multiplayer games:** A local vertical slice cannot validate the feel
  of a networked mechanic. Latency fundamentally changes how combat, movement, and
  prediction feel — testing locally at 0ms will feel entirely different at 80ms
  network delay. The slice can validate that the game loop is interesting and
  complete; it cannot validate that networked mechanics feel good under real
  conditions. Network feel requires real peers or simulated latency.
- 垂直切片代码永远不能重构到生产中 — 它仅供参考
- 生产代码永远不能从 `prototypes/` 导入
- 如果建议是 PROCEED，生产实现从零开始编写，
  仅将切片用作设计参考
- 范围削减可接受；质量削减不可接受 — 低质量切片证明不了什么
- 总工作量：1-3 周。如果更长，范围太大 — 削减切片，而非质量。
- 第 3 天沉没成本规则：如果完整游戏循环周期在那时
  尚不可演示，停止并暴露阻塞问题
- **联网/多人游戏：** 本地垂直切片无法验证
  联网机制的手感。延迟从根本上改变了战斗、移动和
  预判的手感 — 在 0ms 本地测试与在 80ms
  网络延迟下感觉完全不同。切片可以验证游戏循环是否有趣和
  完整；它无法验证联网机制在真实
  条件下的手感。网络手感需要真实对端或模拟延迟。
