# Difficulty Curve: [Game Title]
# 难度曲线：[游戏名称]

> **Status**: Draft | In Review | Approved
> **状态**：草稿 | 评审中 | 已批准
> **Author**: [game-designer / systems-designer]
> **作者**：[游戏设计师 / 系统设计师]
> **Last Updated**: [Date]
> **最后更新**：[日期]
> **Links To**: `design/gdd/game-concept.md`
> **链接至**：`design/gdd/game-concept.md`
> **Relevant GDDs**: [e.g., `design/gdd/combat.md`, `design/gdd/progression.md`]
> **相关 GDD**：[例如 `design/gdd/combat.md`、`design/gdd/progression.md`]

---

## Difficulty Philosophy
## 难度哲学

[One paragraph establishing this game's relationship with difficulty. This is
not a mechanical description — it is a design value statement that all tuning
decisions must serve.
[一段确立本游戏与难度关系的话。这不是机制性描述 — 而是所有调参决策必须服务的设计价值声明。

The four common difficulty philosophies are:
四种常见的难度哲学是：

1. **Masochistic challenge as the core fantasy**: Difficulty is the product.
   Overcoming it is the emotional reward. Reducing difficulty removes the
   point. (Dark Souls, Celeste at max assist off)
1. **受虐式挑战作为核心幻想**：难度即产品本身。克服它是情感回报。降低难度就消除了意义。（Dark Souls、Celeste 在最大辅助关闭时）
2. **Accessible entry, optional depth**: The base experience is completable by
   most players; depth and challenge are opt-in for those who want them.
   (Hades, Hollow Knight with accessibility modes)
2. **可上手入口、可选深度**：基础体验大多数玩家可通关；深度与挑战对想要的人是可选的。（Hades、开启无障碍模式的 Hollow Knight）
3. **Difficulty serves narrative pacing**: Challenge rises and falls to match
   story beats. The player must feel capable during story resolution and
   threatened during story crisis. (The Last of Us, God of War)
3. **难度服务于叙事节奏**：挑战随故事节拍起伏。玩家须在故事收尾时感到有能力，在故事危机时感到受威胁。（The Last of Us、God of War）
4. **Relaxed engagement**: Challenge is present but never the focus. Failure
   is gentle and infrequent. The experience prioritizes comfort and expression
   over obstacle. (Stardew Valley, Animal Crossing)
4. **放松式参与**：存在挑战但绝非焦点。失败温和且罕见。体验优先舒适与表达，而非障碍。（Stardew Valley、Animal Crossing）

State the philosophy explicitly, then add one sentence on what the player is
permitted to feel: are they allowed to feel frustrated? For how long before the
design must intervene? What is the acceptable cost of failure?]
明确陈述哲学，再加一句关于允许玩家感受什么：他们可以感到沮丧吗？在设计必须干预之前允许持续多久？失败的可接受代价是什么？]

---

## Difficulty Axes
## 难度维度

> **Guidance**: Most games have multiple independent dimensions of challenge.
> Identifying them explicitly prevents the mistake of tuning only one axis
> (usually execution difficulty) while leaving others unexamined. A game can
> feel "easy" on execution but overwhelming on decision complexity — players
> experience this as confusing, not engaging.
> **指南**：大多数游戏具有多个独立的挑战维度。明确识别它们可避免只调一个维度（通常是执行难度）而忽视其他维度的错误。游戏可能在执行上感觉 "简单"，但在决策复杂度上压倒一切 — 玩家会感到困惑而非投入。
>
> For each axis, answer: can the player control or reduce this axis through
> choices, builds, or settings? If not, it is a forced challenge dimension —
> be very intentional about how it is used.
> 对每个维度回答：玩家能否通过选择、流派或设置控制或降低此维度？若不能，则它是强制挑战维度 — 使用时要非常刻意。

| Axis | Description | Primary Systems | Player Control? |
|------|-------------|----------------|-----------------|
| **Execution difficulty** | [The precision and timing demands of core actions. e.g., "Dodging enemy attacks requires correct timing within a 200ms window."] | [e.g., Combat, movement] | [Yes — practice reduces this / No — fixed mechanical threshold] |
| **Knowledge difficulty** | [The cost of not knowing information. e.g., "Enemy weaknesses are not telegraphed; players who have not discovered them take significantly more damage."] | [e.g., Enemy design, UI, lore] | [Yes — through in-game discovery / No — requires external knowledge] |
| **Resource pressure** | [How scarce are the resources needed to progress? e.g., "Health consumables are limited; efficient play is required to sustain long dungeon runs."] | [e.g., Economy, loot, crafting] | [Yes — through build optimization / Partially] |
| **Time pressure** | [Does the player have time to think, or does the game demand rapid decisions? e.g., "Enemy spawn timers and attack windows require real-time response."] | [e.g., Combat pacing, timers] | [Yes — through difficulty settings / No — core to genre] |
| **Decision complexity** | [How many meaningful choices must the player evaluate simultaneously? e.g., "Build decisions interact across 4 systems; suboptimal combinations create compounding disadvantage."] | [e.g., Progression, inventory, skills] | [Yes — through UI and tutorialization / No — inherent to strategy depth] |
| **[Add axis]** | [Description] | [Systems] | [Player control] |

| 维度 | 描述 | 主要系统 | 玩家可控？ |
|------|-------------|----------------|-----------------|
| **执行难度** | [核心动作对精度与时序的要求。例如 "闪避敌人攻击须在 200ms 窗口内正确时序。"] | [例如 战斗、移动] | [是 — 练习可降低 / 否 — 固定的机制阈值] |
| **知识难度** | [不知晓信息的代价。例如 "敌人弱点不被预警；未发现弱点的玩家承受显著更多伤害。"] | [例如 敌人设计、UI、背景设定] | [是 — 通过游戏内发现 / 否 — 需要外部知识] |
| **资源压力** | [推进所需资源有多稀缺？例如 "生命消耗品有限；高效游玩才能维持长副本。"] | [例如 经济、掉落、制作] | [是 — 通过流派优化 / 部分可控] |
| **时间压力** | [玩家有时间思考吗，还是游戏要求快速决策？例如 "敌人刷新计时器与攻击窗口要求实时反应。"] | [例如 战斗节奏、计时器] | [是 — 通过难度设置 / 否 — 类型核心] |
| **决策复杂度** | [玩家须同时评估多少有意义的选择？例如 "流派决策跨 4 个系统交互；次优组合造成复合劣势。"] | [例如 进度、库存、技能] | [是 — 通过 UI 与教学 / 否 — 策略深度固有] |
| **[新增维度]** | [描述] | [系统] | [玩家可控] |

---

## Difficulty Curve Overview
## 难度曲线概览

> **Guidance**: This table describes the intended challenge arc across the whole
> game. Difficulty levels use a 1-10 scale where 1 = no meaningful challenge,
> 10 = maximum challenge the game can produce. The scale is relative to THIS game's
> design intent — a 6/10 in a soulslike is not the same as a 6/10 in a cozy sim.
> **指南**：本表描述整局游戏的预期挑战弧线。难度等级采用 1-10 量表，其中 1 = 无有意义挑战，10 = 游戏可产生的最大挑战。该量表相对于"本游戏"的设计意图 — 类魂游戏的 6/10 与休闲模拟的 6/10 不同。
>
> "Primary challenge type" refers to the difficulty axis (from the table above)
> that is doing the most work in this phase. New systems introduced should list
> only systems introduced for the FIRST TIME — the cognitive load of learning
> a new system is itself a form of difficulty.
> "主要挑战类型"指本阶段承担最多工作的难度维度（来自上表）。新引入的系统只列出"首次"引入的系统 — 学习新系统的认知负荷本身就是一种难度。
>
> "Target player state" is the emotional state the designer intends. If the actual
> playtested state diverges from the intended state, this column is what needs
> to be achieved.
> "目标玩家状态"是设计师意图的情感状态。若实际试玩状态偏离意图状态，本列即需要达成的内容。

| Phase | Duration | Difficulty Level (1-10) | Primary Challenge Type | New Systems Introduced | Target Player State |
|-------|----------|------------------------|----------------------|----------------------|---------------------|
| [Prologue / Tutorial] | [e.g., 0-15 min] | [2/10] | [Knowledge] | [Core movement, basic interaction] | [Safe, curious, building confidence] |
| [Early game] | [e.g., 15 min - 2 hrs] | [3-5/10] | [Execution] | [Combat, inventory, first upgrade path] | [Learning, occasional failure, clear cause-effect] |
| [Mid game - opening] | [e.g., 2-6 hrs] | [5-7/10] | [Decision complexity] | [Build choices, advanced enemies, crafting] | [Engaged, strategizing, feeling growth] |
| [Mid game - depth] | [e.g., 6-15 hrs] | [6-8/10] | [Resource pressure] | [Elite enemies, optional hard content, endgame previews] | [Challenged, invested, approaching mastery] |
| [Late game] | [e.g., 15-25 hrs] | [7-9/10] | [Execution + knowledge] | [Endgame systems, NG+ or equivalent] | [Mastery, confident in build identity, seeking peak challenge] |
| [Optional / Endgame] | [e.g., 25+ hrs] | [8-10/10] | [All axes combined] | [Mastery challenges, achievement targets] | [Expert play, self-imposed goals, community comparison] |

| 阶段 | 时长 | 难度等级（1-10） | 主要挑战类型 | 新引入系统 | 目标玩家状态 |
|-------|----------|------------------------|----------------------|----------------------|---------------------|
| [序章 / 教程] | [例如 0-15 分钟] | [2/10] | [知识] | [核心移动、基础交互] | [安全、好奇、建立信心] |
| [早期] | [例如 15 分钟 - 2 小时] | [3-5/10] | [执行] | [战斗、库存、首条升级路径] | [学习、偶有失败、清晰因果] |
| [中期 - 开端] | [例如 2-6 小时] | [5-7/10] | [决策复杂度] | [流派选择、高级敌人、制作] | [投入、策略化、感受成长] |
| [中期 - 深度] | [例如 6-15 小时] | [6-8/10] | [资源压力] | [精英敌人、可选难内容、终局预告] | [被挑战、投入、接近精通] |
| [后期] | [例如 15-25 小时] | [7-9/10] | [执行 + 知识] | [终局系统、NG+ 或同等] | [精通、流派身份自信、寻求巅峰挑战] |
| [可选 / 终局] | [例如 25+ 小时] | [8-10/10] | [所有维度叠加] | [精通挑战、成就目标] | [专家级游玩、自设目标、社群比较] |

---

## Onboarding Ramp
## 上手斜坡

> **Guidance**: The first hour deserves its own detailed breakdown because it
> does the most difficult design work: it must teach every foundational skill
> without feeling like a lesson, and it must create enough investment that the
> player commits to the journey ahead. Research on player retention shows that
> most players who leave a game do so in the first 30 minutes — not because
> the game is bad, but because onboarding failed to connect them.
> **指南**：第一小时值得单独详细拆解，因为它承担最难的设计工作：必须在不像上课的情况下教会每项基础技能，并必须建立足够投入感让玩家承诺前方旅程。玩家留存研究表明，离开游戏的大多数玩家在前 30 分钟内离开 — 不是因为游戏糟糕，而是因为上手未能与他们建立连接。
>
> The scaffolding principle (Vygotsky's Zone of Proximal Development, adapted
> for game design): introduce each mechanic in isolation before combining it
> with others. A player cannot learn two skills simultaneously under pressure.
> 支架原则（维果茨基最近发展区，适配于游戏设计）：在与其他机制结合之前，先孤立地引入每个机制。玩家在压力下无法同时学习两项技能。

### What the Player Knows at Each Stage
### 各阶段玩家知道什么

| Time | What the Player Knows | What They Do Not Know Yet |
|------|-----------------------|--------------------------|
| [0 min] | [Literally nothing — treat this row as your most important UX audit. What can a player infer from the title screen alone?] | [Everything] |
| [5 min] | [Core movement verb, basic world reading] | [All progression systems, all secondary mechanics] |
| [15 min] | [Core interaction loop, first goal] | [Build depth, advanced mechanics, danger severity] |
| [30 min] | [Has made at least one strategic choice] | [Whether that choice was optimal] |
| [60 min] | [Has a working model of the core loop] | [Late-game depth, optional systems] |

| 时间 | 玩家已知 | 玩家尚不知晓 |
|------|-----------------------|--------------------------|
| [0 分钟] | [几乎一无所知 — 把本行视为你最重要的 UX 审计。玩家仅从标题屏能推断什么？] | [一切] |
| [5 分钟] | [核心移动动词、基础世界解读] | [所有进度系统、所有次要机制] |
| [15 分钟] | [核心交互循环、首个目标] | [流派深度、高级机制、危险程度] |
| [30 分钟] | [已做出至少一个策略选择] | [该选择是否最优] |
| [60 分钟] | [对核心循环有可用模型] | [后期深度、可选系统] |

### Mechanic Introduction Sequence
### 机制引入顺序

> The order mechanics are introduced is a design decision with real consequences.
> Introduce the most essential verb first. Introduce mechanics that modify other
> mechanics AFTER the base mechanic is internalized. Never introduce two new
> mechanics in the same encounter.
> 机制引入顺序是具有真实后果的设计决策。先引入最核心的动词。在基础机制被内化"之后"再引入修饰其他机制的机制。绝不在同一次遭遇中引入两个新机制。

| Mechanic | Introduced At | Introduction Method | Stakes at Introduction |
|----------|--------------|--------------------|-----------------------|
| [Core movement / primary verb] | [e.g., First 30 seconds] | [Tutorial prompt / environmental design / NPC instruction] | [None — safe space to experiment] |
| [Primary interaction / action] | [e.g., First 2 minutes] | [Method] | [Low — reversible, forgiving window] |
| [First resource mechanic] | [e.g., 5 min] | [Method] | [Low — abundant at introduction] |
| [First strategic choice] | [e.g., 15 min] | [Method] | [Low — choice can be changed or revisited] |
| [First real failure risk] | [e.g., 20-30 min] | [Method] | [Moderate — player should feel genuine threat but have fair tools to respond] |
| [Add mechanic] | [Timing] | [Method] | [Stakes] |

| 机制 | 引入时刻 | 引入方法 | 引入时赌注 |
|----------|--------------|--------------------|-----------------------|
| [核心移动 / 主要动词] | [例如 首个 30 秒] | [教程提示 / 环境设计 / NPC 指引] | [无 — 安全实验空间] |
| [主要交互 / 行动] | [例如 首个 2 分钟] | [方法] | [低 — 可逆、宽容窗口] |
| [首个资源机制] | [例如 5 分钟] | [方法] | [低 — 引入时充足] |
| [首个策略选择] | [例如 15 分钟] | [方法] | [低 — 选择可改可重访] |
| [首次真实失败风险] | [例如 20-30 分钟] | [方法] | [中 — 玩家应感受真实威胁但有公平工具应对] |
| [新增机制] | [时刻] | [方法] | [赌注] |

### The First Failure
### 首次失败

[Describe the intended design of the first moment the player can meaningfully
fail. This is one of the most important beats in the game.
[描述玩家首次可能有意义失败的时刻的预期设计。这是游戏中最重要的节拍之一。

A well-designed first failure teaches rather than punishes. The player should
be able to immediately identify what they did wrong and what they would do
differently. If the cause of failure is ambiguous, the player blames the game.
设计良好的首次失败是教育而非惩罚。玩家应能立即识别自己做错了什么以及会改做什么。若失败原因含糊，玩家会责怪游戏。

Answer: What causes the first failure? What does the player learn from it?
How quickly can they retry? What is the cost? Does the game provide any
feedback that bridges cause and effect?]
回答：首次失败由什么导致？玩家从中获得什么？能多快重试？代价是什么？游戏是否提供任何桥接因果的反馈？]

### When the Player First Feels Competent
### 玩家首次感到胜任的时刻

[Identify the specific moment — not a vague window, but a specific beat —
where the player should shift from "learning" to "doing." This is the moment
of first competence: the first time their prediction about the game comes true,
or the first time they execute a plan and it works.
[指明具体时刻 — 不是模糊窗口，而是具体节拍 — 玩家应从 "学习" 切换至 "做事"。这是首次胜任时刻：他们对游戏的预测首次成真，或他们首次执行计划并奏效。

This moment must happen within the first hour. If it does not, the player
will not reach Phase 3 of the journey (First Mastery). Design this moment
deliberately — do not leave it to chance.
此时刻必须在前一小时内发生。若不发生，玩家将无法抵达旅程第三阶段（首次精通）。请刻意设计此时刻 — 不要听天由命。

What is the moment? What systems create it? What does the player do to
trigger it? How does the game communicate that they have succeeded?]
该时刻是什么？由哪些系统创造？玩家做什么来触发？游戏如何传达他们成功了？]

---

## Difficulty Spikes and Valleys
## 难度峰值与谷底

> **Guidance**: A healthy difficulty curve follows a sawtooth pattern
> (Csikszentmihalyi's flow model applied to macro-structure): tension builds
> through a sequence, then releases at a milestone, then re-engages at a
> slightly higher baseline. Flat difficulty creates boredom; uninterrupted
> escalation creates fatigue.
> **指南**：健康的难度曲线遵循锯齿模式（Csikszentmihalyi 心流模型应用于宏观结构）：张力在一段序列中累积，然后在里程碑处释放，再在略高的基线上重新进入。平坦的难度造成无聊；不间断的升级造成疲劳。
>
> Spikes are intentional peaks that test accumulated skills. Valleys are
> intentional troughs that give the player space to breathe, experiment, and
> feel powerful before the next escalation. Both are designed, not emergent.
> 峰值是测试累积技能的有意顶点。谷底是在下一次升级之前给玩家喘息、实验并感到强大的有意低处。两者都是设计的，而非涌现的。
>
> "Recovery design" is critical: what happens immediately after a spike? The
> player should exit a hard moment feeling accomplished, not depleted. Give
> them a valley, a reward, or a narrative payoff.
> "恢复设计" 至关重要：峰值之后立即发生什么？玩家应带着成就感而非耗竭感离开艰难时刻。给他们一个谷底、一份奖励或一个叙事回报。

| Name | Location in Game | Type | Purpose | Recovery Design |
|------|-----------------|------|---------|-----------------|
| [e.g., "The First Boss"] | [e.g., End of Area 1, ~1 hr] | [Spike] | [Tests all skills introduced in Area 1. Acts as a gate confirming the player is ready for increased complexity.] | [Post-boss: safe area, upgrade opportunity, story beat that provides emotional relief before Area 2 escalation begins.] |
| [e.g., "The Safe Zone"] | [e.g., Hub area between Areas 1 and 2, ~1.5 hrs] | [Valley] | [Player feels powerful from boss win. Space to experiment with build options before stakes rise.] | [N/A — this IS the recovery from the preceding spike.] |
| [e.g., "The Knowledge Wall"] | [e.g., Area 3 first encounter, ~4 hrs] | [Spike — knowledge type] | [Forces players to engage with a mechanic they may have been avoiding. Survival requires understanding it.] | [Clear feedback on what killed them. Tutorial hint surfaces on third failure. Mechanic becomes standard after this point.] |
| [e.g., "Pre-Climax Valley"] | [e.g., Just before final act, ~20 hrs] | [Valley] | [Emotional breathing room before the final escalation. Player reflects on how far they have come.] | [N/A — designed as relief before the finale's spike.] |
| [Add spike/valley] | [Location] | [Type] | [Purpose] | [Recovery] |

| 名称 | 游戏内位置 | 类型 | 目的 | 恢复设计 |
|------|-----------------|------|---------|-----------------|
| [例如 "首个 Boss"] | [例如 区域 1 末尾，约 1 小时] | [峰值] | [测试区域 1 引入的所有技能。作为门槛确认玩家准备好应对更高复杂度。] | [Boss 之后：安全区、升级机会、在区域 2 升级开始前提供情感舒缓的故事节拍。] |
| [例如 "安全区"] | [例如 区域 1 与 2 之间的枢纽，约 1.5 小时] | [谷底] | [玩家因击败 Boss 感到强大。赌注上升前实验流派选择的空间。] | [不适用 — 它本身即是前一峰值的恢复。] |
| [例如 "知识之墙"] | [例如 区域 3 首次遭遇，约 4 小时] | [峰值 — 知识型] | [迫使玩家与他们可能一直回避的机制交互。生存要求理解它。] | [对杀死他们的原因提供清晰反馈。第三次失败时浮现教程提示。此后该机制成为标准。] |
| [例如 "终幕前谷底"] | [例如 终幕前，约 20 小时] | [谷底] | [最终升级前的情感喘息空间。玩家反思走了多远。] | [不适用 — 设计为终幕峰值前的舒缓。] |
| [新增峰值/谷底] | [位置] | [类型] | [目的] | [恢复] |

---

## Balancing Levers
## 平衡调节杆

> **Guidance**: Balancing levers are the specific values and parameters that
> tune difficulty at each phase. Centralizing them here makes it possible to
> tune the whole-game difficulty curve without hunting through individual GDDs.
> For each lever, the GDD that owns it should be cross-referenced.
> **指南**：平衡调节杆是在每个阶段调节难度的具体数值与参数。在此集中化使得无需翻找各份 GDD 即可调节整局难度曲线。对每个调节杆，应交叉引用拥有它的 GDD。
>
> "Current setting" is the design intent at the time of writing — implementation
> values live in `assets/data/`. The tuning range is the safe operating range:
> values outside this range reliably break the intended experience.
> "当前设置" 是编写时的设计意图 — 实现值位于 `assets/data/`。调参范围是安全操作范围：此范围之外的值会可靠地破坏预期体验。

| Lever | Phase(s) | Effect | Current Setting | Tuning Range | Notes |
|-------|----------|--------|----------------|-------------|-------|
| [Enemy health multiplier] | [All] | [Higher = longer fights = more resource pressure and execution time] | [1.0x] | [0.7x - 1.5x] | [Below 0.7x, fights end before player can read enemy patterns. Above 1.5x, attrition replaces skill.] |
| [Enemy aggression timer] | [Mid game onward] | [Time between enemy attacks; lower = less time to react] | [e.g., 2.0s] | [1.2s - 3.0s] | [Below 1.2s, reaction window is sub-human. Above 3.0s, encounters feel passive.] |
| [Resource drop rate] | [Early game] | [Lower = more resource pressure = punishes inefficiency harder] | [e.g., 1.5x baseline] | [0.8x - 2.0x] | [Onboarding generosity; reduces in mid-game as player skill assumed.] |
| [New mechanic introduction density] | [First hour] | [How many new concepts per minute of play; too high = cognitive overload] | [e.g., 1 new mechanic per 8 min] | [1 per 5 min (max) to 1 per 15 min (slow)] | [Above 1 per 5 min in early game causes retention drop. Below 1 per 15 min causes boredom.] |
| [Failure cost] | [All] | [Time lost on failure; higher = more punishing = more tension] | [e.g., 2 min setback] | [30s - 8 min] | [Must scale with encounter frequency. Frequent failures need fast recovery.] |
| [Add lever] | [Phase] | [Effect] | [Setting] | [Range] | [Notes] |

| 调节杆 | 阶段 | 效果 | 当前设置 | 调参范围 | 备注 |
|-------|----------|--------|----------------|-------------|-------|
| [敌人生命倍率] | [全部] | [越高 = 战斗越长 = 更多资源压力与执行时间] | [1.0x] | [0.7x - 1.5x] | [低于 0.7x，战斗在玩家读懂敌人模式前结束。高于 1.5x，消耗取代技巧。] |
| [敌人攻击间隔计时] | [中期起] | [敌人攻击间隔；越低 = 反应时间越少] | [例如 2.0s] | [1.2s - 3.0s] | [低于 1.2s，反应窗口非人类。高于 3.0s，遭遇感觉被动。] |
| [资源掉落率] | [早期] | [越低 = 资源压力越大 = 更严厉惩罚低效] | [例如 1.5x 基线] | [0.8x - 2.0x] | [上手慷慨；中期假定玩家技能提升而降低。] |
| [新机制引入密度] | [第一小时] | [每分钟游玩引入多少新概念；过高 = 认知过载] | [例如 每 8 分钟 1 个新机制] | [每 5 分钟 1 个（最高）至每 15 分钟 1 个（慢）] | [早期高于每 5 分钟 1 个会致留存下降。低于每 15 分钟 1 个会致无聊。] |
| [失败代价] | [全部] | [失败损失时间；越高 = 越惩罚 = 越紧张] | [例如 2 分钟回退] | [30s - 8 分钟] | [须随遭遇频率缩放。频繁失败需快速恢复。] |
| [新增调节杆] | [阶段] | [效果] | [设置] | [范围] | [备注] |

---

## Player Skill Assumptions
## 玩家技能假设

> **Guidance**: Every game implicitly assumes players develop a set of skills
> over the course of play. Making these assumptions explicit allows the team to
> verify that each skill is actually taught before it is tested, and that the
> gap between "introduced" and "tested hard" is long enough for internalization.
> **指南**：每款游戏都隐含假设玩家在游玩过程中培养一系列技能。将这些假设显式化使团队能验证每项技能在被测试之前确实被教过，且 "引入" 与 "硬测试" 之间的间隔足以内化。
>
> A skill introduced and tested in the same encounter is a surprise difficulty
> spike. A skill assumed but never formally introduced is an undocumented knowledge
> wall. Both are fixable — but only if they are documented.
> 在同一次遭遇中引入并测试的技能是意外的难度峰值。被假设却从未正式引入的技能是无文档的知识墙。两者都可修复 — 但前提是被记录下来。
>
> "Taught by" refers to the mechanism: tutorial prompt, environmental design,
> safe practice opportunity, NPC instruction, or organic discovery.
> "教学方" 指机制：教程提示、环境设计、安全练习机会、NPC 指引或有机发现。
>
> "Tested by" refers to the first encounter that REQUIRES this skill to survive
> without taking significant damage or cost.
> "测试方" 指首个"要求"此技能才能在不承受重大伤害或代价下存活的遭遇。

| Skill | Introduced In | Expected Mastered By | Taught By | First Hard Test |
|-------|--------------|---------------------|-----------|-----------------|
| [Core movement / dodging] | [Tutorial area, 0-5 min] | [End of Area 1, ~1 hr] | [Safe practice zone with visible hazards] | [First Elite enemy, ~45 min] |
| [Resource management] | [First shop encounter, ~10 min] | [Mid game, ~4 hrs] | [Resource scarcity in Area 2 forces planning] | [Boss that requires consumables to survive efficiently] |
| [Build decision-making] | [First upgrade choice, ~20 min] | [End of mid game, ~10 hrs] | [Multiple playthroughs / community discussion / in-game build advisor] | [Endgame encounters that punish build incoherence] |
| [Enemy pattern reading] | [Area 1 basic enemies] | [Area 3, ~4 hrs] | [Enemy telegraphs visible and consistent from introduction] | [Elite enemy with 3+ distinct attack patterns] |
| [Add skill] | [When introduced] | [When mastered] | [Taught by] | [First hard test] |

| 技能 | 引入于 | 预期精通于 | 教学方 | 首次硬测试 |
|-------|--------------|---------------------|-----------|-----------------|
| [核心移动 / 闪避] | [教程区，0-5 分钟] | [区域 1 末，约 1 小时] | [带可见危险的安全练习区] | [首个精英敌人，约 45 分钟] |
| [资源管理] | [首次商店遭遇，约 10 分钟] | [中期，约 4 小时] | [区域 2 的资源稀缺迫使规划] | [需要消耗品才能高效存活的 Boss] |
| [流派决策] | [首次升级选择，约 20 分钟] | [中期末，约 10 小时] | [多周目 / 社群讨论 / 游戏内流派顾问] | [惩罚流派混乱的终局遭遇] |
| [敌人招式阅读] | [区域 1 基础敌人] | [区域 3，约 4 小时] | [敌人预警从引入起可见且一致] | [具有 3 种以上不同攻击模式的精英敌人] |
| [新增技能] | [引入时] | [精通时] | [教学方] | [首次硬测试] |

---

## Accessibility Considerations
## 无障碍考量

> **Guidance**: Accessibility in difficulty design is not about making the game
> easier — it is about ensuring players with different needs and skill profiles
> can reach the intended emotional experience. Be explicit about what CAN be
> adjusted and what CANNOT, and justify both.
> **指南**：难度设计中的无障碍不是让游戏变简单 — 而是确保有不同需求与技能画像的玩家能抵达预期的情感体验。请明确什么"可"调、什么"不可"调，并对两者给出理由。
>
> The principle from Self-Determination Theory: players need to feel competent.
> Accessibility options that help players feel competent without removing the
> feeling of agency are always worth including. Options that make competence
> meaningless undermine the core experience.
> 来自自我决定理论的原则：玩家需要感到胜任。在不消除能动性感受的前提下帮助玩家感到胜任的无障碍选项始终值得纳入。使胜任变得无意义的选项会损害核心体验。

### What Can Be Adjusted
### 可调项

| Adjustment | Method | Effect on Experience | Tradeoff |
|-----------|--------|---------------------|----------|
| [e.g., Enemy speed reduction] | [Difficulty setting / accessibility menu] | [Lowers execution difficulty without changing knowledge or decision requirements] | [Reduces the tension of combat timing; acceptable for narrative players] |
| [e.g., Extended input windows] | [Accessibility menu] | [Allows players with motor impairments to achieve the same skill outcomes with more time] | [Minimal — skill expression preserved, threshold relaxed] |
| [e.g., Hint frequency] | [Settings toggle] | [Surfaces contextual guidance more or less aggressively based on player preference] | [Higher hints reduce knowledge difficulty; players who want to discover organically may feel over-guided] |
| [Add option] | [Method] | [Effect] | [Tradeoff] |

| 调整项 | 方法 | 对体验的影响 | 权衡 |
|-----------|--------|---------------------|----------|
| [例如 敌人速度降低] | [难度设置 / 无障碍菜单] | [降低执行难度，不改变知识或决策要求] | [减少战斗时序的紧张感；对叙事玩家可接受] |
| [例如 延长输入窗口] | [无障碍菜单] | [让运动障碍玩家以更多时间达成相同技能结果] | [极小 — 技巧表达保留，阈值放宽] |
| [例如 提示频率] | [设置开关] | [根据玩家偏好或多或少激进地呈现情境指引] | [更多提示降低知识难度；想有机发现的玩家可能感到过度引导] |
| [新增选项] | [方法] | [影响] | [权衡] |

### What Cannot Be Adjusted (and Why)
### 不可调项（及原因）

| Fixed Element | Why It Cannot Change | Design Reasoning |
|--------------|---------------------|-----------------|
| [e.g., Permadeath in roguelike run] | [Removing it eliminates the resource pressure axis that all encounter balance is built around] | [The weight of each decision comes from permanence; without it, the core loop loses meaning] |
| [e.g., Core narrative pacing] | [Difficulty valleys are timed to story beats; adjustable pacing would decouple challenge from narrative intention] | [Story and difficulty are designed as one arc, not two independent tracks] |
| [Add fixed element] | [Why] | [Reasoning] |

| 固定元素 | 为何不可变 | 设计理由 |
|--------------|---------------------|-----------------|
| [例如 Roguelike 一局中的永久死亡] | [移除它会消除所有遭遇平衡所依托的资源压力维度] | [每个决策的重量来自永久性；无之，核心循环失去意义] |
| [例如 核心叙事节奏] | [难度谷底与故事节拍同步；可调节奏会使挑战与叙事意图脱钩] | [故事与难度被设计为一条弧线，而非两条独立轨道] |
| [新增固定元素] | [原因] | [理由] |

---

## Cross-System Difficulty Interactions
## 跨系统难度交互

> **Guidance**: When two systems operate simultaneously, their combined
> difficulty is often greater than the sum of their parts — or sometimes
> less. These interactions are frequently unintended and only surface during
> playtesting. Documenting anticipated interactions here creates a checklist
> for QA and playtest sessions.
> **指南**：当两个系统同时运作时，其组合难度往往大于各部分之和 — 有时则更小。这些交互常常是无意的，且只在试玩时浮现。在此记录预期交互为 QA 与试玩场次创建一份检查清单。
>
> "Is this intended?" Yes means the interaction is a designed feature.
> No means it should be mitigated. Partial means the interaction is
> acceptable in small doses but problematic if it becomes the dominant
> experience.
> "是否预期？" 是表示该交互是设计功能。
> 否表示应缓解。部分表示该交互在少量出现时可接受，但若成为主导体验则有问题。

| System A | System B | Combined Effect | Intended? |
|----------|----------|----------------|-----------|
| [Combat difficulty] | [Resource scarcity] | [Resource-poor players face combat encounters with fewer options, compounding difficulty for players already struggling. Can create a death spiral where failing creates worse conditions.] | [Partial — intended as stakes, not as a trap. Pity mechanics required to prevent unrecoverable states.] |
| [Build complexity] | [Time pressure] | [Players who are still learning their build take longer to make decisions under time pressure, increasing cognitive load beyond the intended challenge of either system alone.] | [No — reduce decision complexity demand in high time-pressure encounters.] |
| [New mechanic introduction] | [Resource pressure] | [Introducing a new system while the player is already under resource pressure forces them to learn and optimize simultaneously.] | [No — new mechanics should be introduced in low-resource-pressure environments.] |
| [Enemy density] | [Execution difficulty] | [High enemy counts with individually demanding enemies produce difficulty that scales exponentially, not linearly.] | [Partial — intended for optional challenge content only; not acceptable on the critical path.] |
| [Add System A] | [Add System B] | [Combined effect description] | [Yes / No / Partial] |

| 系统 A | 系统 B | 组合效果 | 是否预期？ |
|----------|----------|----------------|-----------|
| [战斗难度] | [资源稀缺] | [资源匮乏的玩家以更少选项面对战斗遭遇，使本已挣扎的玩家难度复合。可形成失败造成更糟条件的死亡螺旋。] | [部分 — 预期为赌注而非陷阱。需要保底机制防止不可恢复状态。] |
| [流派复杂度] | [时间压力] | [仍在学习流派的玩家在时间压力下决策更慢，认知负荷超出任一系统单独的预期挑战。] | [否 — 在高时间压力遭遇中降低决策复杂度要求。] |
| [新机制引入] | [资源压力] | [玩家已在资源压力下时引入新系统迫使其同时学习与优化。] | [否 — 新机制应在低资源压力环境中引入。] |
| [敌人密度] | [执行难度] | [高敌人数量与个体要求高的敌人产生指数级而非线性增长的难度。] | [部分 — 仅预期用于可选挑战内容；关键路径上不可接受。] |
| [新增系统 A] | [新增系统 B] | [组合效果描述] | [是 / 否 / 部分] |

---

## Validation Checklist
## 验证清单

> **Guidance**: These checkpoints structure playtesting sessions to verify
> the difficulty curve is achieving its intent. Each item should be checked
> with at least 3 playtester sessions before being marked complete. Note the
> playtester profile that revealed issues — difficulty problems are almost
> always player-profile-specific.
> **指南**：这些检查点结构化试玩场次以验证难度曲线是否达成意图。每项至少须经 3 场试玩检查后方可标记完成。注意揭示问题的试玩者画像 — 难度问题几乎总是与玩家画像相关。

### Onboarding (0-30 min)
### 上手（0-30 分钟）
- [ ] Players with no prior genre experience complete the tutorial area without external help
- [ ] 无先前类型经验的玩家在无外部帮助下完成教程区
- [ ] Zero players cite confusion about what they are supposed to be doing in the first 5 minutes
- [ ] 零玩家在前 5 分钟内表示对应当做什么感到困惑
- [ ] At least one playtester spontaneously says "I want to see what's next" within 15 minutes
- [ ] 至少一名试玩者在 15 分钟内自发说 "我想看接下来是什么"
- [ ] First failure moment produces a visible learning response (player verbalizes what went wrong)
- [ ] 首次失败时刻产生可见学习反应（玩家口头表达出错原因）

### Early Game (30 min - 2 hrs)
### 早期（30 分钟 - 2 小时）
- [ ] Average player reaches the first competence moment within 60 minutes
- [ ] 平均玩家在 60 分钟内达到首次胜任时刻
- [ ] First major encounter (boss or equivalent) is passed within 3-5 attempts on average
- [ ] 首次重大遭遇（Boss 或同等）平均 3-5 次尝试内通过
- [ ] No player cites a mechanic introduced "too suddenly without warning"
- [ ] 无玩家表示某机制 "毫无预警地突然引入"
- [ ] Players can describe their current goal without prompting
- [ ] 玩家能在无提示下描述当前目标

### Mid Game (2-10 hrs)
### 中期（2-10 小时）
- [ ] Players discover at least one depth mechanic through organic play (without guide)
- [ ] 玩家通过有机游玩发现至少一项深度机制（无攻略）
- [ ] Playtest sessions report "I want to try a different build / strategy next run"
- [ ] 试玩场次报告 "我想下次尝试不同流派 / 策略"
- [ ] No single difficulty axis dominates player complaints — frustration is distributed
- [ ] 无单一难度维度主导玩家抱怨 — 挫败感分散
- [ ] Players who fail a mid-game encounter correctly identify the cause without being told
- [ ] 中期遭遇失败的玩家能在不被告知下正确识别原因

### Late Game (10+ hrs)
### 后期（10+ 小时）
- [ ] Players report the final challenge feels like a culmination of everything they have learned
- [ ] 玩家报告最终挑战感觉是所学一切的集大成
- [ ] Failure at late-game content does not feel unfair (even if it is hard)
- [ ] 后期内容失败不感觉不公（即使困难）
- [ ] Players who complete the main content express a reason to continue playing
- [ ] 完成主内容的玩家表达继续游玩的理由

### Accessibility
### 无障碍
- [ ] All listed accessibility options function without breaking encounter intent
- [ ] 所有列出的无障碍选项运作而不破坏遭遇意图
- [ ] Players using accessibility settings report feeling competent, not patronized
- [ ] 使用无障碍设置的玩家报告感到胜任，而非被施舍
- [ ] Fixed difficulty elements are encountered and accepted without negative reception from accessibility playtesters
- [ ] 固定难度元素被无障碍试玩者遇到并接受，无负面反馈

---

## Open Questions
## 待解决问题

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| [Is the onboarding ramp correctly calibrated for players without prior genre experience?] | [game-designer] | [Date] | [Unresolved — schedule genre-naive playtester sessions] |
| [Does the first boss represent the correct difficulty spike or is it a wall?] | [game-designer, systems-designer] | [Date] | [Unresolved — requires 5+ playtester sessions to establish average attempt count] |
| [Do any cross-system interactions produce unrecoverable states?] | [systems-designer] | [Date] | [Unresolved — requires targeted playtest with resource-constrained starting conditions] |
| [Add question] | [Owner] | [Date] | [Resolution] |

| 问题 | 负责人 | 截止日期 | 决议 |
|----------|-------|----------|-----------|
| [上手斜坡对无先前类型经验玩家是否校准正确？] | [游戏设计师] | [日期] | [未解决 — 安排类型新手试玩场次] |
| [首个 Boss 是否代表正确的难度峰值，还是一堵墙？] | [游戏设计师、系统设计师] | [日期] | [未解决 — 需 5 场以上试玩以确立平均尝试次数] |
| [是否有跨系统交互产生不可恢复状态？] | [系统设计师] | [日期] | [未解决 — 需在资源受限起始条件下定向试玩] |
| [新增问题] | [负责人] | [日期] | [决议] |
