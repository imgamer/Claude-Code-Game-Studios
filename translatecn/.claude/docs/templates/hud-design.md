# HUD Design: [Game Name]
# HUD 设计：[游戏名称]

> **Status**: Draft | In Review | Approved | Implemented
> **Author**: [Name or agent — e.g., ui-designer]
> **Last Updated**: [Date]
> **Game**: [Game name — this is a single document per game, not per element]
> **Platform Targets**: [All platforms this HUD must work on — e.g., PC, PS5, Xbox Series X, Steam Deck]
> **Related GDDs**: [Every system that exposes information through the HUD — e.g., `design/gdd/combat.md`, `design/gdd/progression.md`, `design/gdd/quests.md`]
> **Accessibility Tier**: Basic | Standard | Comprehensive | Exemplary
> **Style Reference**: [Link to art bible HUD section if it exists — e.g., `design/art/art-bible.md § HUD Visual Language`]
> **状态**：草稿 | 审核中 | 已批准 | 已实现
> **作者**：[姓名或智能体 — 例如，ui-designer]
> **最后更新**：[Date]
> **游戏**：[游戏名称 — 每款游戏一份文档，而非每个元素一份]
> **平台目标**：[此 HUD 必须运行的所有平台 — 例如，PC, PS5, Xbox Series X, Steam Deck]
> **相关 GDD**：[通过 HUD 暴露信息的每个系统 — 例如，`design/gdd/combat.md`、`design/gdd/progression.md`、`design/gdd/quests.md`]
> **无障碍层级**：基础 | 标准 | 全面 | 示范
> **风格参考**：[如存在，链接到美术指南 HUD 章节 — 例如，`design/art/art-bible.md § HUD Visual Language`]

> **Note — Scope boundary**: This document specifies all elements that overlay the
> game world during active gameplay — health bars, ammo counters, minimaps, quest
> trackers, subtitles, damage numbers, and notification toasts. For menu screens,
> pause menus, inventory, and dialogs that the player navigates explicitly, use
> `ux-spec.md` instead. The test: if it appears while the player is directly
> controlling their character, it belongs here.
> **注 — 范围边界**：本文档规定在活跃游戏过程中覆盖游戏世界的所有元素 — 血条、弹药计数器、小地图、
> 任务追踪器、字幕、伤害数字和通知 toast。对于玩家显式浏览的菜单屏幕、暂停菜单、库存与对话框，
> 请改用 `ux-spec.md`。测试：如果它出现在玩家直接控制角色时，则属于此文档。

---

## 1. HUD Philosophy
## 1. HUD 哲学

> **Why this section exists**: The HUD design philosophy is not decoration — it is a
> design constraint that every subsequent decision is measured against. Without a
> philosophy, individual elements get added on request ("the quest tracker wants a
> bigger icon") without any principled way to push back. With a philosophy, there is
> a shared, explicit standard. More importantly, the philosophy prevents the HUD from
> slowly growing to cover the game world while each individual addition seemed
> reasonable in isolation. Write this before specifying any elements.
> **为何存在此节**：HUD 设计哲学不是装饰 — 它是衡量每个后续决策的设计约束。
> 没有哲学，单个元素会按请求添加（"任务追踪器想要更大的图标"）而没有任何有原则的反驳方式。
> 有了哲学，就有共享的、显式的标准。更重要的是，哲学防止 HUD 缓慢增长以覆盖游戏世界，
> 而每个单独添加在孤立看时似乎合理。在规定任何元素之前先写此节。

**What is this game's relationship with on-screen information?**
**此游戏与屏幕信息的关系是什么？**

[One paragraph. This is a design statement, not a description of features. Consider
the game's genre, pacing, and player fantasy. A stealth game's HUD philosophy might
be: "The world is the interface. If the player has to look away from the environment
to survive, the HUD has failed." A tactics game might say: "Complete situational
awareness is the game. The HUD is not an overlay — it is the battlefield."
[一段话。这是设计声明，而非功能描述。考虑游戏类型、节奏与玩家幻想。
潜行游戏的 HUD 哲学可能是："世界即是界面。如果玩家必须从环境移开视线才能生存，
HUD 就失败了。"战术游戏可能说："完全的态势感知就是游戏。HUD 不是覆盖层 — 它是战场。"

Reference comparable games if helpful, but describe your specific stance:
Example — diegetic-first action RPG: "We treat screen information as a concession,
not a feature. Every HUD element must earn its pixel space by answering the question:
would the player make demonstrably worse decisions without this information visible?
If the answer is 'they'd adapt,' we put it in the environment instead."]
如有帮助可参考可比游戏，但描述你的具体立场：
示例 — 叙事优先的动作 RPG："我们将屏幕信息视为让步，而非功能。每个 HUD 元素必须通过
回答以下问题来赢得其像素空间：如果没有此信息可见，玩家会做出明显更糟的决策吗？
如果答案是'他们会适应'，我们就把它放进环境。"]

**Visibility principle** — when in doubt, show or hide?
**可见性原则** — 拿不准时，显示还是隐藏？

[State the default resolution for ambiguous cases. Options:
- Default to HIDE: information is available on demand (e.g., Dark Souls — no quest tracker, no minimap, stats are in a menu)
- Default to SHOW: players prefer to be informed; cluttered is better than uncertain
- Default to CONTEXTUAL: information appears when it becomes relevant and fades when it does not
Most games benefit from contextual defaults. State your game's default clearly so every element decision is consistent.]
[说明模糊情况的默认解决方案。选项：
- 默认隐藏：信息按需提供（例如，Dark Souls — 无任务追踪器、无小地图、属性在菜单中）
- 默认显示：玩家偏好被告知；杂乱胜过不确定
- 默认上下文化：信息在相关时出现，不相关时淡出
多数游戏受益于上下文化默认。清晰说明你的游戏的默认设置，使每个元素决策一致。]

**The Rule of Necessity for this game**:
**此游戏的必要性规则**：

[Complete this sentence: "A HUD element earns its place when ______________."
[完成此句："一个 HUD 元素当 ______________ 时赢得其位置。"

Example: "...the player would have to stop playing to find the same information
elsewhere, or would make meaningfully worse decisions without it."
示例："……玩家必须停止游玩才能在其他地方找到相同信息，或没有它会做出明显更糟的决策。"

Example: "...removing it in playtesting causes measurable frustration or confusion
in more than 25% of testers within the first hour of play."
示例："……在试玩中移除它会在游玩首小时内导致超过 25% 的测试者出现可测量的挫败或困惑。"

This rule is the veto power over feature requests to add HUD elements. Document it
so it can be cited in design reviews.]
此规则是对添加 HUD 元素的功能请求的否决权。记录它以便在设计评审中引用。]

---

## 2. Information Architecture
## 2. 信息架构

> **Why this section exists**: Before specifying any HUD element's visual design,
> position, or behavior, you must answer a more fundamental question: should this
> information be on the HUD at all? This section is a forcing function — it requires
> you to categorize EVERY piece of information the game world generates and make an
> explicit, intentional decision about how each is presented. "We'll figure that out
> later" is how games end up with 18 elements competing for the player's peripheral
> vision. This table is the master inventory of game information, not just HUD information.
> **为何存在此节**：在规定任何 HUD 元素的视觉设计、位置或行为之前，你必须回答一个更根本的问题：
> 此信息应否在 HUD 上？此节是强制函数 — 它要求你将游戏世界生成的每一条信息分类，
> 并对每条如何呈现做出显式、有意的决策。"以后再解决"是游戏最终有 18 个元素争夺玩家周边视觉的原因。
> 此表是游戏信息的主清单，不仅是 HUD 信息。

| Information Type | Always Show | Contextual (show when relevant) | On Demand (menu/button) | Hidden (environmental / diegetic) | Reasoning |
|-----------------|-------------|--------------------------------|------------------------|----------------------------------|-----------|
| [Health / Vitality] | [X if action game — player needs constant awareness] | [X if exploration game — show only when injured] | [ ] | [ ] | [Example: always visible because health decisions (retreat, heal) must be instant in combat] |
| [Primary resource (mana / stamina / ammo)] | [ ] | [X — show when resource is being consumed or is critically low] | [ ] | [ ] | [Example: contextual because stable resource levels are not decision-relevant] |
| [Secondary resource (currency / materials)] | [ ] | [ ] | [X — check in inventory] | [ ] | [Example: on-demand because resource totals don't affect immediate gameplay decisions] |
| [Minimap / Compass] | [X] | [ ] | [ ] | [ ] | [Example: always visible because navigation decisions are constant during exploration] |
| [Quest objective] | [ ] | [X — show when objective changes or player is near it] | [ ] | [ ] | [Example: contextual — player knows their objective; only remind at key moments] |
| [Enemy health bar] | [ ] | [X — show only during combat encounters] | [ ] | [ ] | [Example: contextual because enemy health is irrelevant outside combat] |
| [Status effects (buffs/debuffs)] | [ ] | [X — show when active] | [ ] | [ ] | [Example: contextual because status effects only affect decisions when present] |
| [Dialogue subtitles] | [X when dialogue is playing] | [ ] | [ ] | [ ] | [Example: always show while dialogue is active — accessibility requirement] |
| [Combo / streak counter] | [ ] | [X — show while combo is active, hide on reset] | [ ] | [ ] | [Example: contextual because it communicates active performance, not baseline state] |
| [Timer] | [ ] | [X — show only in timed sequences] | [ ] | [ ] | [Example: contextual because timers only exist in specific encounter types] |
| [Tutorial prompts] | [ ] | [X — show for first-time situations only] | [ ] | [ ] | [Example: contextual and one-time; never repeat to experienced players] |
| [Score / points] | [ ] | [X — show in score-relevant modes only] | [ ] | [ ] | [Example: contextual by game mode; hidden in modes where score is irrelevant] |
| [XP / level progress] | [ ] | [ ] | [X — available via character screen] | [ ] | [Example: on-demand because progression does not affect in-moment gameplay decisions] |
| [Waypoint / objective marker] | [ ] | [X — show when player is navigating to objective] | [ ] | [ ] | [Example: contextual — suppress during cutscenes, cinematic moments, and free exploration] |
| 信息类型 | 始终显示 | 上下文化（相关时显示） | 按需（菜单/按钮） | 隐藏（环境/叙事） | 理由 |
|-----------------|-------------|--------------------------------|------------------------|----------------------------------|-----------|
| [生命/活力] | [X 若为动作游戏 — 玩家需要持续感知] | [X 若为探索游戏 — 仅受伤时显示] | [ ] | [ ] | [示例：始终可见，因为生命决策（撤退、治疗）在战斗中必须即时] |
| [主要资源（法力/耐力/弹药）] | [ ] | [X — 资源被消耗或临界低时显示] | [ ] | [ ] | [示例：上下文化，因为稳定资源水平与决策无关] |
| [次要资源（货币/材料）] | [ ] | [ ] | [X — 在库存中查看] | [ ] | [示例：按需，因为资源总量不影响即时游戏决策] |
| [小地图/罗盘] | [X] | [ ] | [ ] | [ ] | [示例：始终可见，因为探索期间导航决策是持续的] |
| [任务目标] | [ ] | [X — 目标改变或玩家接近时显示] | [ ] | [ ] | [示例：上下文化 — 玩家知道其目标；仅在关键时刻提醒] |
| [敌人血条] | [ ] | [X — 仅战斗遭遇时显示] | [ ] | [ ] | [示例：上下文化，因为战斗外敌人生命无关] |
| [状态效果（增益/减益）] | [ ] | [X — 激活时显示] | [ ] | [ ] | [示例：上下文化，因为状态效果仅在存在时影响决策] |
| [对话字幕] | [X 对话播放时] | [ ] | [ ] | [ ] | [示例：对话激活时始终显示 — 无障碍要求] |
| [连击/连续计数器] | [ ] | [X — 连击激活时显示，重置时隐藏] | [ ] | [ ] | [示例：上下文化，因为它传达活跃表现，而非基线状态] |
| [计时器] | [ ] | [X — 仅计时序列显示] | [ ] | [ ] | [示例：上下文化，因为计时器仅存在于特定遭遇类型] |
| [教程提示] | [ ] | [X — 仅首次情况显示] | [ ] | [ ] | [示例：上下文化且一次性；永不向经验丰富的玩家重复] |
| [分数/点数] | [ ] | [X — 仅在分数相关模式显示] | [ ] | [ ] | [示例：按游戏模式上下文化；在分数无关模式中隐藏] |
| [经验/等级进度] | [ ] | [ ] | [X — 通过角色界面可用] | [ ] | [示例：按需，因为进阶不影响当下游戏决策] |
| [路径点/目标标记] | [ ] | [X — 玩家导航至目标时显示] | [ ] | [ ] | [示例：上下文化 — 在过场、电影时刻与自由探索时抑制] |

---

## 3. Layout Zones
## 3. 布局区域

> **Why this section exists**: The game world is the primary content — the HUD is a
> frame around it. Before placing any element, divide the screen into named zones
> with explicit positions and safe zone margins. This section prevents two failure
> modes: (1) elements placed ad-hoc until the screen is cluttered, and (2) elements
> that overlap platform-required safe zones and get rejected in certification.
> Every element in Section 4 must be assigned to a zone defined here.
> **为何存在此节**：游戏世界是主要内容 — HUD 是其周围的画框。在放置任何元素之前，
> 将屏幕划分为有显式位置与安全区边距的命名区域。此节防止两种失败模式：
> (1) 临时放置元素直到屏幕杂乱，(2) 元素与平台所需安全区重叠并在认证中被拒。
> 第 4 节的每个元素必须分配到此节定义的区域。

### 3.1 Zone Diagram
### 3.1 区域图

```
[Draw your HUD layout zones. Customize this to match your game's actual layout.
 Axes represent approximate screen percentage. Adjust zone names and sizes.]
[绘制你的 HUD 布局区域。自定义以匹配你游戏实际布局。
 坐标轴代表近似屏幕百分比。调整区域名称与尺寸。]

 0%                                             100%
 ┌──────────────────────────────────────────────────┐  0%
 │  [SAFE MARGIN — 10% from edge on all sides]      │
 │  ┌────────────────────────────────────────────┐  │
 │  │ [TOP-LEFT]              [TOP-CENTER]  [TOP-RIGHT] │  ~15%
 │  │  Health, resource       Quest name    Ammo, magazine │
 │  │                                              │  │
 │  │                                              │  │
 │  │               [CENTER-SCREEN]               │  │  ~50%
 │  │                Crosshair / reticle           │  │
 │  │               (minimize HUD here)            │  │
 │  │                                              │  │
 │  │                                              │  │
 │  │ [BOTTOM-LEFT]     [BOTTOM-CENTER]   [BOTTOM-RIGHT] │  ~85%
 │  │  Minimap          Subtitles          Notifications │
 │  │  Ability icons    Tutorial prompts             │  │
 │  └────────────────────────────────────────────┘  │
 │                                                  │
 └──────────────────────────────────────────────────┘  100%
```

```
[Draw your HUD layout zones. Customize this to match your game's actual layout.
 Axes represent approximate screen percentage. Adjust zone names and sizes.]
[绘制你的 HUD 布局区域。自定义以匹配你游戏实际布局。
 坐标轴代表近似屏幕百分比。调整区域名称与尺寸。]

 0%                                             100%
 ┌──────────────────────────────────────────────────┐  0%
 │  [SAFE MARGIN — 10% from edge on all sides]      │
 │  ┌────────────────────────────────────────────┐  │
 │  │ [TOP-LEFT]              [TOP-CENTER]  [TOP-RIGHT] │  ~15%
 │  │  Health, resource       Quest name    Ammo, magazine │
 │  │                                              │  │
 │  │                                              │  │
 │  │               [CENTER-SCREEN]               │  │  ~50%
 │  │                Crosshair / reticle           │  │
 │  │               (minimize HUD here)            │  │
 │  │                                              │  │
 │  │                                              │  │
 │  │ [BOTTOM-LEFT]     [BOTTOM-CENTER]   [BOTTOM-RIGHT] │  ~85%
 │  │  Minimap          Subtitles          Notifications │
 │  │  Ability icons    Tutorial prompts             │  │
 │  └────────────────────────────────────────────┘  │
 │                                                  │
 └──────────────────────────────────────────────────┘  100%
```

> Rule for zone placement: the center 40% of the screen (both horizontally and
> vertically) is the player's primary focus area. Keep this zone as clear as
> possible at all times. HUD elements that appear in the center zone — crosshairs,
> interaction prompts, hit markers — must be minimal, high-contrast, and brief.
> 区域放置规则：屏幕中心 40%（水平和垂直）是玩家的主要关注区。
> 始终保持此区域尽可能清晰。出现在中心区的 HUD 元素 — 准星、交互提示、
> 命中标记 — 必须最小、高对比度且短暂。

### 3.2 Zone Specification Table
### 3.2 区域规格表

| Zone Name | Screen Position | Safe Zone Compliant | Primary Elements | Max Simultaneous Elements | Notes |
|-----------|----------------|---------------------|-----------------|--------------------------|-------|
| [Top Left] | [Top-left corner, within safe margin] | [Yes — 10% from top, 10% from left] | [Health bar, stamina bar, shield bar] | [3] | [Vital status — player's own resources. Priority zone for player state.] |
| [Top Center] | [Top edge, centered horizontally] | [Yes — 10% from top] | [Quest objective, area name (on enter)] | [1 — only one message at a time] | [Use for narrative context, not mechanical information. Keep text minimal.] |
| [Top Right] | [Top-right corner, within safe margin] | [Yes — 10% from top, 10% from right] | [Ammo count, ability cooldowns] | [2] | [Weapon/ability state. Most relevant during active combat.] |
| [Center] | [Screen center ±15%] | [N/A — not a margin zone] | [Crosshair, interaction prompt, hit marker] | [1 active at a time] | [CRITICAL: Nothing persistent here. Only momentary indicators.] |
| [Bottom Left] | [Bottom-left corner, within safe margin] | [Yes — 10% from bottom, 10% from left] | [Minimap, ability icons] | [2] | [Navigation and ability readout. Small, non-intrusive.] |
| [Bottom Center] | [Bottom edge, centered horizontally] | [Yes — 10% from bottom] | [Subtitles, tutorial prompts] | [2 — subtitle + tutorial may coexist] | [Highest-priority accessibility zone. Never place other elements here.] |
| [Bottom Right] | [Bottom-right corner, within safe margin] | [Yes — 10% from bottom, 10% from right] | [Notification toasts, pick-up feedback] | [3 stacked] | [Transient notifications. Stack vertically. Oldest disappears first.] |
| 区域名称 | 屏幕位置 | 安全区合规 | 主要元素 | 最大同时元素数 | 备注 |
|-----------|----------------|---------------------|-----------------|--------------------------|-------|
| [左上] | [左上角，安全区内] | [是 — 距顶部 10%，距左侧 10%] | [血条、耐力条、护盾条] | [3] | [关键状态 — 玩家自身资源。玩家状态的优先区。] |
| [顶部居中] | [上边缘，水平居中] | [是 — 距顶部 10%] | [任务目标、区域名（进入时）] | [1 — 一次仅一条消息] | [用于叙事上下文，非机械信息。保持文字最小。] |
| [右上] | [右上角，安全区内] | [是 — 距顶部 10%，距右侧 10%] | [弹药计数、能力冷却] | [2] | [武器/能力状态。活跃战斗中最相关。] |
| [中心] | [屏幕中心 ±15%] | [N/A — 非边距区] | [准星、交互提示、命中标记] | [一次仅 1 个活跃] | [关键：此处无持久内容。仅瞬时指示器。] |
| [左下] | [左下角，安全区内] | [是 — 距底部 10%，距左侧 10%] | [小地图、能力图标] | [2] | [导航与能力读数。小、不干扰。] |
| [底部居中] | [下边缘，水平居中] | [是 — 距底部 10%] | [字幕、教程提示] | [2 — 字幕 + 教程可共存] | [最高优先级无障碍区。永不在此放置其他元素。] |
| [右下] | [右下角，安全区内] | [是 — 距底部 10%，距右侧 10%] | [通知 toast、拾取反馈] | [3 个堆叠] | [瞬态通知。垂直堆叠。最旧的先消失。] |

**Safe zone margins by platform**:
**按平台的安全区边距**：

| Platform | Top | Bottom | Left | Right | Notes |
|----------|-----|--------|------|-------|-------|
| [PC — windowed] | [0% — no safe zone required] | [0%] | [0%] | [0%] | [But respect minimum resolution — elements must not crowd at 1280x720] |
| [PC — fullscreen] | [3%] | [3%] | [3%] | [3%] | [Slight margin for 4K TV-connected PCs] |
| [Console — TV] | [10%] | [10%] | [10%] | [10%] | [Action-safe zone for broadcast-spec TVs. Some TVs overscan beyond this.] |
| [Steam Deck] | [5%] | [5%] | [5%] | [5%] | [Small screen; safe zone is smaller but crowding risk is higher] |
| [Mobile — portrait] | [15% top] | [10% bottom] | [5%] | [5%] | [15% top avoids notch/camera cutout on most devices] |
| [Mobile — landscape] | [5%] | [5%] | [15% left] | [15% right] | [Thumb placement on landscape — side zones are obscured by hands] |
| 平台 | 顶部 | 底部 | 左侧 | 右侧 | 备注 |
|----------|-----|--------|------|-------|-------|
| [PC — 窗口] | [0% — 无需安全区] | [0%] | [0%] | [0%] | [但尊重最低分辨率 — 元素在 1280x720 不得拥挤] |
| [PC — 全屏] | [3%] | [3%] | [3%] | [3%] | [为连接 4K 电视的 PC 留少量边距] |
| [主机 — 电视] | [10%] | [10%] | [10%] | [10%] | [广播规格电视的动作安全区。部分电视过扫超过此值。] |
| [Steam Deck] | [5%] | [5%] | [5%] | [5%] | [小屏；安全区更小但拥挤风险更高] |
| [移动 — 竖屏] | [顶部 15%] | [底部 10%] | [5%] | [5%] | [顶部 15% 避开多数设备的刘海/摄像头开孔] |
| [移动 — 横屏] | [5%] | [5%] | [左 15%] | [右 15%] | [横屏时拇指位置 — 侧区被手遮挡] |

---

## 4. HUD Element Specifications
## 4. HUD 元素规格

> **Why this section exists**: Each HUD element needs its own specification to be
> built correctly. Ad-hoc implementation of HUD elements produces inconsistent
> sizing, mismatched update frequencies, missing urgency states, and accessibility
> failures. This section is the implementation brief for every element — fill it
> completely before any element moves into development.
> **为何存在此节**：每个 HUD 元素需要自己的规格才能正确构建。临时实现 HUD 元素
> 会产生不一致尺寸、不匹配的更新频率、缺失的紧迫性状态和无障碍失败。
> 此节是每个元素的实施简报 — 在任何元素进入开发之前完整填写。

### 4.1 Element Overview Table
### 4.1 元素概览表

> One row per HUD element. This is the master inventory for implementation planning.
> 每个 HUD 元素一行。这是实施规划的主清单。

| Element Name | Zone | Always Visible | Visibility Trigger | Data Source | Update Frequency | Max Size (% screen W) | Min Readable Size | Overlap Priority | Accessibility Alt |
|-------------|------|---------------|-------------------|-------------|-----------------|----------------------|------------------|-----------------|------------------|
| [Health Bar] | [Top Left] | [Yes] | [N/A] | [PlayerStats] | [On value change] | [20%] | [120px wide] | [1 — highest] | [Numerical text label showing current/max: "80/100"] |
| [Stamina Bar] | [Top Left] | [No — context] | [Show when consuming stamina; hide 3s after full] | [PlayerStats] | [Realtime during use] | [15%] | [80px wide] | [2] | [Numerical label, or hide if full (accessible assumption)] |
| [Shield Indicator] | [Top Left] | [No — context] | [Show when shield is active or recently hit] | [PlayerStats] | [On value change] | [20%] | [120px wide] | [3] | [Numerical label. Must not use color alone — add shield icon.] |
| [Ammo Counter] | [Top Right] | [No — context] | [Show when weapon is equipped; hide when unarmed] | [WeaponSystem] | [On fire / on reload] | [10%] | ["88/888" readable at game's min resolution] | [4] | [Text-only fallback: "32 / 120"] |
| [Minimap] | [Bottom Left] | [Yes] | [N/A — but suppressed in cinematic mode] | [NavigationSystem] | [Realtime] | [18%] | [150x150px] | [5] | [Cardinal direction compass strip as fallback; must be toggleable] |
| [Quest Objective] | [Top Center] | [No — context] | [Show on objective change; show when near objective location; hide after 5s] | [QuestSystem] | [On event] | [30%] | [Legible at body text size] | [6] | [Read aloud on objective change via screen reader] |
| [Crosshair] | [Center] | [No — context] | [Show when ranged weapon equipped; hide in melee or unarmed] | [WeaponSystem / AimSystem] | [Realtime] | [3%] | [12px diameter minimum] | [1 — center zone priority] | [Reduce motion: static crosshair only. Option to enlarge.] |
| [Interaction Prompt] | [Center] | [No — context] | [Show when player is within interaction range of an interactive object] | [InteractionSystem] | [On enter/exit interaction range] | [15%] | [24px icon + readable text] | [2 — center zone] | [Text description of interaction always present, not icon-only] |
| [Subtitles] | [Bottom Center] | [No — always on when dialogue plays, if setting enabled] | [Show during any voiced line or ambient dialogue] | [DialogueSystem] | [Per dialogue line] | [60%] | [Minimum 24px font] | [1 — highest in zone] | [This IS the accessibility feature — see Section 8 for subtitle spec] |
| [Damage Numbers] | [World-space / anchored to entity] | [No — context] | [Show on any damage event; duration 800ms] | [CombatSystem] | [On event] | [5% per number] | [18px minimum] | [3] | [Option to disable; numbers can overwhelm for photosensitive players] |
| [Status Effect Icons] | [Top Left — below health bar] | [No — context] | [Show when any status effect is active on player] | [StatusSystem] | [On effect add/remove] | [3% per icon] | [24px per icon] | [3] | [Icon + text label on hover/focus. Never icon-only.] |
| [Notification Toast] | [Bottom Right] | [No — event-driven] | [On loot, XP gain, achievement, quest update] | [Multiple — see Section 6] | [On event] | [25%] | [Legible at body text size] | [7 — lowest] | [Queued; never overlapping. Read by screen reader if subtitle mode on.] |
| 元素名称 | 区域 | 始终可见 | 可见性触发 | 数据源 | 更新频率 | 最大尺寸（% 屏幕宽） | 最小可读尺寸 | 重叠优先级 | 无障碍替代 |
|-------------|------|---------------|-------------------|-------------|-----------------|----------------------|------------------|-----------------|------------------|
| [血条] | [左上] | [是] | [N/A] | [PlayerStats] | [值改变时] | [20%] | [120px 宽] | [1 — 最高] | [显示当前/最大数值文本标签："80/100"] |
| [耐力条] | [左上] | [否 — 上下文] | [消耗耐力时显示；满后 3 秒隐藏] | [PlayerStats] | [使用时实时] | [15%] | [80px 宽] | [2] | [数值标签，或满时隐藏（无障碍假设）] |
| [护盾指示] | [左上] | [否 — 上下文] | [护盾激活或最近受击时显示] | [PlayerStats] | [值改变时] | [20%] | [120px 宽] | [3] | [数值标签。不得仅用颜色 — 加护盾图标。] |
| [弹药计数器] | [右上] | [否 — 上下文] | [装备武器时显示；徒手时隐藏] | [WeaponSystem] | [开火/装填时] | [10%] | ["88/888" 在游戏最低分辨率可读] | [4] | [仅文本后备："32 / 120"] |
| [小地图] | [左下] | [是] | [N/A — 但电影模式中抑制] | [NavigationSystem] | [实时] | [18%] | [150x150px] | [5] | [基本方位罗盘条作为后备；必须可切换] |
| [任务目标] | [顶部居中] | [否 — 上下文] | [目标改变时显示；接近目标位置时显示；5 秒后隐藏] | [QuestSystem] | [事件时] | [30%] | [正文字号可读] | [6] | [目标改变时通过屏幕阅读器朗读] |
| [准星] | [中心] | [否 — 上下文] | [装备远程武器时显示；近战或徒手时隐藏] | [WeaponSystem / AimSystem] | [实时] | [3%] | [最小直径 12px] | [1 — 中心区优先] | [减少动效：仅静态准星。可放大选项。] |
| [交互提示] | [中心] | [否 — 上下文] | [玩家在交互对象交互范围内时显示] | [InteractionSystem] | [进入/退出交互范围时] | [15%] | [24px 图标 + 可读文本] | [2 — 中心区] | [交互的文本描述始终存在，非仅图标] |
| [字幕] | [底部居中] | [否 — 对话播放时始终开启（如设置启用）] | [任何配音台词或环境对话时显示] | [DialogueSystem] | [每对话行] | [60%] | [最小 24px 字体] | [1 — 区域内最高] | [此即无障碍功能 — 字幕规格见第 8 节] |
| [伤害数字] | [世界空间/锚定于实体] | [否 — 上下文] | [任何伤害事件时显示；持续 800ms] | [CombatSystem] | [事件时] | [每个数字 5%] | [最小 18px] | [3] | [可禁用选项；数字可能让光敏玩家不堪重负] |
| [状态效果图标] | [左上 — 血条下方] | [否 — 上下文] | [玩家有任何激活状态效果时显示] | [StatusSystem] | [效果添加/移除时] | [每个图标 3%] | [每个图标 24px] | [3] | [悬停/聚焦时图标 + 文本标签。绝不仅图标。] |
| [通知 Toast] | [右下] | [否 — 事件驱动] | [拾取、获得经验、成就、任务更新时] | [多个 — 见第 6 节] | [事件时] | [25%] | [正文字号可读] | [7 — 最低] | [排队；从不重叠。字幕模式开启时由屏幕阅读器朗读。] |

### 4.2 Element Detail Blocks
### 4.2 元素详情块

> For each element in the table above, write a detail block. Copy and complete
> one block per element.
> 对上表中的每个元素，写一个详情块。每个元素复制并完成一个块。

---

**Health Bar**
**血条**

- Visual description: [Horizontal fill bar. Left-to-right fill direction. Segmented at 25/50/75% to aid reading at a glance. Background: dark semi-transparent (40% opacity). Fill color: context-dependent — see Urgency States.]
- Data displayed: [Current HP as fill percentage. Numerical value displayed as text below bar at all times: "80 / 100".]
- Update behavior: [Bar fill decreases or increases smoothly using a lerp over 150ms per change. Large damage (>25% single hit) triggers a brief flash (1 frame white, then drain).]
- Urgency states:
  - Normal (>50% HP): [Green fill, no special behavior]
  - Caution (25–50% HP): [Yellow fill, low warning pulse every 4 seconds]
  - Critical (<25% HP): [Red fill, persistent slow pulse (1 Hz), vignette appears at screen edges]
  - Zero (0% HP): [Bar empties and turns grey; death state begins]
- Interaction: [Display only. Not interactive. Player cannot click, hover, or focus this element as an action target.]
- Player customization: [Opacity adjustable (see Section 7 Tuning Knobs). Can be repositioned to any corner by player in accessibility settings.]
- 视觉描述：[水平填充条。从左到右填充方向。在 25/50/75% 分段以便一眼读取。背景：深色半透明（40% 不透明度）。填充色：取决于上下文 — 见紧迫性状态。]
- 显示数据：[当前 HP 作为填充百分比。数值始终以条下方文本显示："80 / 100"。]
- 更新行为：[条填充使用每变化 150ms 的 lerp 平滑减少或增加。大伤害（>25% 单次命中）触发短暂闪烁（1 帧白色，然后排空）。]
- 紧迫性状态：
  - 正常（>50% HP）：[绿色填充，无特殊行为]
  - 警告（25–50% HP）：[黄色填充，每 4 秒低警告脉冲]
  - 危急（<25% HP）：[红色填充，持续慢脉冲（1 Hz），屏幕边缘出现暗角]
  - 零（0% HP）：[条排空并变灰；死亡状态开始]
- 交互：[仅显示。不可交互。玩家不能点击、悬停或聚焦此元素作为动作目标。]
- 玩家自定义：[不透明度可调（见第 7 节调优旋钮）。可在无障碍设置中由玩家重新定位到任何角落。]

---

**Minimap**
**小地图**

- Visual description: [Circular mask, radius = 75px at reference resolution 1920x1080. Player icon at center. North always up unless player has unlocked "Rotate minimap" setting. Range = configurable, default 80 world units radius.]
- Data displayed: [Player position, nearby enemies (if detection perk unlocked), quest markers within range, points of interest icons, traversal obstacles (walls, drops).]
- Update behavior: [Realtime. Updates every frame. Enemy icons fade in/out as they enter/leave detection range over 300ms.]
- Urgency states: [None for the map itself. Enemy icons turn red when they are in combat-alert state.]
- Interaction: [Not interactive in-game. Press dedicated Map button to open the full map screen (separate UX spec).]
- Player customization: [Size: S/M/L (70/90/110px radius). Opacity: 30–100%. Rotation: locked-north or player-relative. Can be disabled entirely (compass strip shows as fallback).]
- 视觉描述：[圆形遮罩，参考分辨率 1920x1080 下半径 = 75px。玩家图标居中。除非玩家解锁"旋转小地图"设置，否则北始终朝上。范围 = 可配置，默认 80 世界单位半径。]
- 显示数据：[玩家位置、附近敌人（如解锁侦测特长）、范围内任务标记、兴趣点图标、穿越障碍物（墙、跌落）。]
- 更新行为：[实时。每帧更新。敌人图标在进入/离开侦测范围时在 300ms 内淡入/淡出。]
- 紧迫性状态：[地图本身无。敌人在战斗警戒状态时图标变红。]
- 交互：[游戏中不可交互。按专用地图按钮打开全屏地图（单独 UX 规格）。]
- 玩家自定义：[尺寸：S/M/L（70/90/110px 半径）。不透明度：30–100%。旋转：锁定北方或玩家相对。可完全禁用（罗盘条作为后备显示）。]

---

**[Repeat this block for every element in Section 4.1]**
**[对第 4.1 节中的每个元素重复此块]**

---

## 5. HUD States by Gameplay Context
## 5. 按游戏上下文的 HUD 状态

> **Why this section exists**: The HUD is not a static overlay — it is a dynamic
> system that must adapt to what the player is doing. A HUD designed only for
> standard gameplay will look wrong in cutscenes, feel cluttered in exploration,
> and occlude critical information in boss fights. This section defines the
> transformations the HUD undergoes in each gameplay context. It is also the spec
> for the system that manages HUD visibility — the HUD state machine.
> **为何存在此节**：HUD 不是静态覆盖 — 它是必须适应玩家正在做什么的动态系统。
> 仅为标准游戏设计的 HUD 在过场中看起来不对，在探索中感觉杂乱，
> 并在 Boss 战中遮挡关键信息。此节定义 HUD 在每个游戏上下文中经历的变换。
> 它也是管理 HUD 可见性的系统 — HUD 状态机 — 的规格。

| Context | Elements Shown | Elements Hidden | Elements Modified | Transition Into This State |
|---------|---------------|-----------------|------------------|---------------------------|
| [Exploration — no threats] | [Minimap, Quest Objective (faded, 60%), Subtitles (if active)] | [Ammo Counter, Crosshair, Damage Numbers, Status Effects (if none active)] | [Health Bar fades to 40% opacity — visible but not dominant] | [Fade transition, 500ms, when no enemies detected for 10s] |
| [Combat — active threat] | [Health Bar (full opacity), Stamina Bar (when used), Ammo Counter, Crosshair, Damage Numbers, Status Effects, Enemy Health Bars] | [Quest Objective (temporarily hidden), Notification Toasts (paused queue)] | [Minimap scales down 15% and raises opacity to 100%] | [Immediate snap in on first enemy detection — no fade. Combat readiness requires instant info.] |
| [Dialogue / Cutscene] | [Subtitles, Dialogue speaker name] | [All gameplay HUD elements: health, ammo, minimap, crosshair, damage numbers] | [N/A] | [All gameplay elements fade out over 300ms when cutscene flag is set] |
| [Cinematic (scripted camera sequence)] | [Subtitles only] | [Everything else including speaker name] | [Letterbox bars appear (if applicable to this game's style)] | [Immediate on cinematic flag; letterbox slides in from top/bottom over 400ms] |
| [Inventory / Menu open] | [None — inventory renders full-screen or as overlay] | [All HUD elements] | [Game world visible but paused behind inventory screen] | [All HUD elements hide over 150ms as menu opens] |
| [Death / Respawn pending] | [Death screen overlay — separate spec] | [All gameplay HUD elements] | [Screen desaturates and darkens over 800ms] | [Death state begins when HP reaches 0 — HUD elements fade over 600ms] |
| [Loading / Transition] | [Loading indicator, tip text] | [All gameplay HUD elements] | [N/A] | [Instant on level transition trigger] |
| [Tutorial — new mechanic] | [Standard context HUD + Tutorial Prompt overlay] | [Nothing additional hidden] | [Tutorial prompt dims background subtly to draw attention to prompt] | [Tutorial system fires ShowTutorial event; prompt fades in over 200ms] |
| [Boss Encounter] | [Boss health bar appears (large, bottom of screen or top center), all combat elements] | [Quest Objective] | [Boss bar renders in a distinct visual style — must not be confused with player health] | [Boss health bar slides in on boss encounter trigger over 400ms] |
| 上下文 | 显示元素 | 隐藏元素 | 修改的元素 | 进入此状态的过渡 |
|---------|---------------|-----------------|------------------|---------------------------|
| [探索 — 无威胁] | [小地图、任务目标（淡出，60%）、字幕（如激活）] | [弹药计数器、准星、伤害数字、状态效果（如无激活）] | [血条淡出至 40% 不透明度 — 可见但不突出] | [淡出过渡，500ms，10 秒内未侦测到敌人时] |
| [战斗 — 活跃威胁] | [血条（完全不透明）、耐力条（使用时）、弹药计数器、准星、伤害数字、状态效果、敌人血条] | [任务目标（暂时隐藏）、通知 Toast（暂停队列）] | [小地图缩小 15% 并将不透明度提高至 100%] | [首次侦测敌人时立即切换 — 无淡出。战斗准备需要即时信息。] |
| [对话 / 过场] | [字幕、对话说话者名] | [所有游戏 HUD 元素：生命、弹药、小地图、准星、伤害数字] | [N/A] | [设置过场标志时所有游戏元素在 300ms 内淡出] |
| [电影（脚本相机序列）] | [仅字幕] | [其他一切，包括说话者名] | [出现黑边（如适用于此游戏风格）] | [电影标志时立即；黑边在 400ms 内从顶部/底部滑入] |
| [库存 / 菜单打开] | [无 — 库存全屏渲染或作为覆盖] | [所有 HUD 元素] | [游戏世界可见但在库存界面后暂停] | [菜单打开时所有 HUD 元素在 150ms 内隐藏] |
| [死亡 / 等待重生] | [死亡界面覆盖 — 单独规格] | [所有游戏 HUD 元素] | [屏幕在 800ms 内去饱和并变暗] | [HP 到 0 时死亡状态开始 — HUD 元素在 600ms 内淡出] |
| [加载 / 过渡] | [加载指示器、提示文本] | [所有游戏 HUD 元素] | [N/A] | [关卡过渡触发时即时] |
| [教程 — 新机制] | [标准上下文 HUD + 教程提示覆盖] | [无额外隐藏] | [教程提示使背景微妙变暗以吸引注意] | [教程系统触发 ShowTutorial 事件；提示在 200ms 内淡入] |
| [Boss 遭遇] | [Boss 血条出现（大，屏幕底部或顶部居中）、所有战斗元素] | [任务目标] | [Boss 条以独特视觉风格渲染 — 不得与玩家生命混淆] | [Boss 遭遇触发时 Boss 血条在 400ms 内滑入] |

---

## 6. Information Hierarchy
## 6. 信息层级

> **Why this section exists**: Not all HUD information is equally important. When
> screen space is limited, when the player is under high stress, or when elements
> compete for the same zone, there must be a principled priority order that governs
> which elements survive and which get suppressed. This section formalizes that
> hierarchy so it can be enforced systematically and not just "feels obvious" decisions
> made at implementation time.
> **为何存在此节**：并非所有 HUD 信息同等重要。当屏幕空间有限、玩家处于高压下
> 或元素争夺同一区域时，必须有有原则的优先级顺序来决定哪些元素保留、哪些被抑制。
> 此节使该层级形式化，以便系统地执行，而非仅在实施时做"感觉明显"的决策。

| Element | Priority Tier | Reasoning | What Replaces It If Hidden |
|---------|--------------|-----------|---------------------------|
| [Subtitles] | [MUST KEEP — never hide during dialogue] | [Accessibility requirement. Legal requirement in some markets. Story clarity.] | [N/A — nothing replaces subtitles] |
| [Health Bar] | [MUST KEEP — during any state where the player can be damaged] | [Without health visibility, survival decisions become impossible] | [Auditory cues (heartbeat, breathing) supplement but do not replace] |
| [Crosshair] | [MUST KEEP — while aiming with a ranged weapon] | [Targeting without a crosshair is a precision failure, not a difficulty feature] | [Alternative: dot-only mode for minimalists; never fully hidden while aiming] |
| [Interaction Prompt] | [MUST KEEP — when player is in interaction range] | [Without it, interactive objects are invisible to the player] | [Environmental visual cues can supplement but interaction affordance must be explicit] |
| [Ammo Counter] | [SHOULD KEEP] | [Low ammo decisions (switch weapon, reload) require awareness; can be contextual] | [Auditory "click" on empty chamber is acceptable fallback for experienced players] |
| [Minimap] | [SHOULD KEEP] | [Navigation requires spatial awareness; loss forces repeated map opens] | [Compass strip (simplified directional indicator) is acceptable fallback] |
| [Status Effects] | [SHOULD KEEP — while active] | [Active debuffs change what actions are viable; invisible debuffs feel unfair] | [Character animation states can partially communicate status effects (limping, sparks)] |
| [Quest Objective] | [CAN HIDE] | [Player can hold objective in memory for extended periods; contextual is correct default] | [Player remembers objective from context] |
| [Damage Numbers] | [CAN HIDE] | [Feedback element, not decision-critical. Many players turn these off.] | [Hit sounds and enemy reactions communicate hit registration] |
| [Notification Toasts] | [CAN HIDE in high-intensity moments] | [Mid-combat "You gained 50 XP" is noise, not signal. Queue and show after combat.] | [Queue held and released when combat ends] |
| [Combo Counter] | [ALWAYS HIDE when combo resets or player is not attacking] | [Stale combo information is actively misleading] | [N/A — simply hidden] |
| 元素 | 优先级层级 | 理由 | 如隐藏由什么替代 |
|---------|--------------|-----------|---------------------------|
| [字幕] | [必须保留 — 对话期间永不隐藏] | [无障碍要求。某些市场的法律要求。故事清晰度。] | [N/A — 无物替代字幕] |
| [血条] | [必须保留 — 玩家可能受伤的任何状态] | [无生命可见性，生存决策变得不可能] | [听觉线索（心跳、呼吸）补充但不替代] |
| [准星] | [必须保留 — 用远程武器瞄准时] | [无准星瞄准是精度失败，非难度功能] | [替代：极简主义者的仅点模式；瞄准时永不全隐藏] |
| [交互提示] | [必须保留 — 玩家在交互范围内时] | [无它，交互对象对玩家不可见] | [环境视觉线索可补充但交互可见性必须显式] |
| [弹药计数器] | [应保留] | [低弹药决策（切换武器、装填）需要感知；可上下文化] | [空弹膛的听觉"咔哒"是有经验玩家的可接受后备] |
| [小地图] | [应保留] | [导航需要空间感知；失去迫使反复打开地图] | [罗盘条（简化方向指示器）是可接受后备] |
| [状态效果] | [应保留 — 激活时] | [激活的减益改变哪些动作可行；不可见减益感觉不公平] | [角色动画状态可部分传达状态效果（跛行、火花）] |
| [任务目标] | [可隐藏] | [玩家可将目标保持记忆中较长时间；上下文化是正确默认] | [玩家从上下文记住目标] |
| [伤害数字] | [可隐藏] | [反馈元素，非决策关键。许多玩家关闭它们。] | [命中声和敌人反应传达命中注册] |
| [通知 Toast] | [高强度时刻可隐藏] | [战斗中"你获得 50 经验"是噪声，非信号。排队并在战斗后显示。] | [队列保持并在战斗结束时释放] |
| [连击计数器] | [连击重置或玩家未攻击时始终隐藏] | [过时连击信息是主动误导] | [N/A — 仅隐藏] |

---

## 7. Visual Budget
## 7. 视觉预算

> **Why this section exists**: Without explicit budget constraints, HUD elements
> accumulate until the game world is nearly invisible. These numbers are hard limits,
> not guidelines. Every element addition that would breach a limit requires explicit
> approval and must displace or reduce an existing element.
> **为何存在此节**：无显式预算约束，HUD 元素会累积直到游戏世界几乎不可见。
> 这些数字是硬限制，非指导原则。每个会突破限制的元素添加需要显式批准
> 且必须替代或减少现有元素。

| Budget Constraint | Limit | Measurement Method | Current Estimate | Status |
|------------------|-------|--------------------|-----------------|--------|
| Maximum simultaneous active HUD elements | [8] | [Count all visible, non-faded elements at any one frame] | [TBD — verify at implementation] | [To verify] |
| Maximum % of screen occupied by HUD (exploration mode) | [12%] | [Pixel area of all HUD elements / total screen pixels] | [TBD] | [To verify] |
| Maximum % of screen occupied by HUD (combat mode) | [22%] | [Same method — combat adds ammo, crosshair, enemy bars] | [TBD] | [To verify] |
| Maximum % of center screen zone (40% of screen W/H) occupied | [5%] | [Only crosshair and interaction prompt allowed here] | [TBD] | [To verify] |
| Minimum contrast ratio — HUD text on any background | [4.5:1 (WCAG AA)] | [Measured against the darkest and lightest game world areas the element will appear over] | [TBD] | [To verify] |
| Maximum opacity for HUD background panels | [65%] | [Opacity of any panel behind HUD text — must preserve world visibility through panel] | [TBD] | [To verify] |
| Minimum HUD element size at minimum supported resolution | [40px for icons, 18px for text] | [Measure at lowest target resolution] | [TBD] | [To verify] |
| 预算约束 | 限制 | 测量方法 | 当前估算 | 状态 |
|------------------|-------|--------------------|-----------------|--------|
| 最大同时活跃 HUD 元素 | [8] | [任一帧中所有可见、未淡出元素的计数] | [待定 — 实施时验证] | [待验证] |
| HUD 占屏幕最大百分比（探索模式） | [12%] | [所有 HUD 元素像素面积 / 屏幕总像素] | [待定] | [待验证] |
| HUD 占屏幕最大百分比（战斗模式） | [22%] | [相同方法 — 战斗加弹药、准星、敌人条] | [待定] | [待验证] |
| 中心屏幕区（屏幕宽/高的 40%）最大占用百分比 | [5%] | [仅准星和交互提示允许在此] | [待定] | [待验证] |
| 最低对比度 — 任何背景上的 HUD 文本 | [4.5:1 (WCAG AA)] | [在元素将出现的最暗和最亮游戏世界区域上测量] | [待定] | [待验证] |
| HUD 背景面板最大不透明度 | [65%] | [HUD 文本后任何面板的不透明度 — 必须通过面板保留世界可见性] | [待定] | [待验证] |
| 最低支持分辨率下最小 HUD 元素尺寸 | [图标 40px，文本 18px] | [在最低目标分辨率下测量] | [待定] | [待验证] |

> **How to apply these budgets**: For every new HUD element proposed during
> production, require the proposer to state (1) which budget line it affects,
> (2) what the new total will be, and (3) what existing element will be reduced or
> made contextual to stay within budget. "It's a small icon" is not an analysis.
> **如何应用这些预算**：对于制作期间提议的每个新 HUD 元素，要求提议者说明
> (1) 它影响哪条预算线，(2) 新总数将是多少，(3) 哪个现有元素将被减少或上下文化以保持在预算内。
> "只是个小图标"不是分析。

---

## 8. Feedback & Notification Systems
## 8. 反馈与通知系统

> **Why this section exists**: Notifications are the most frequently-added and
> worst-controlled part of most HUDs. Every system wants to tell the player
> something. Without explicit rules about notification priority, stacking limits,
> and queue behavior, the notification zone becomes a firehose of overlapping
> toasts that players learn to ignore entirely. This section establishes the
> notification contract for all systems.
> **为何存在此节**：通知是大多数 HUD 中最常添加且控制最差的部分。
> 每个系统都想告诉玩家某事。无关于通知优先级、堆叠限制和队列行为的显式规则，
> 通知区会成为重叠 toast 的消防水带，玩家学会完全忽略。此节为所有系统建立通知契约。

| Notification Type | Trigger System | Screen Position | Duration (ms) | Animation In / Out | Max Simultaneous | Priority | Queue Behavior | Dismissible? |
|------------------|---------------|-----------------|--------------|-------------------|-----------------|----------|---------------|-------------|
| [Item Pickup] | [InventorySystem] | [Bottom Right — toast] | [2000] | [Slide in from right 200ms / fade out 300ms] | [3 stacked] | [Low] | [FIFO queue; older toasts pushed up as new ones enter] | [No — auto-dismiss] |
| [XP Gain] | [ProgressionSystem] | [Bottom Right — toast, below item toasts] | [1500] | [Fade in 150ms / fade out 300ms] | [1 — XP messages merge: "XP +150"] | [Very Low — suppress during combat, queue for post-combat] | [Combat-aware queue] | [No] |
| [Level Up] | [ProgressionSystem] | [Center screen — persistent until dismissed] | [Persistent — requires input to dismiss] | [Scale up from 80% + fade in 400ms] | [1] | [High — interrupts normal toasts] | [Pauses all other notifications until dismissed] | [Yes — any input] |
| [Quest Update] | [QuestSystem] | [Top Center] | [4000] | [Slide down from top 250ms / fade out 400ms] | [1 — top center is single-message zone] | [Medium] | [If quest update arrives while previous is visible, extend duration by 2000ms; do not stack] | [No] |
| [Objective Complete] | [QuestSystem] | [Top Center] | [3000] | [Same as Quest Update but with additional completion sound] | [1] | [Medium-High — preempts Quest Update] | [Preempts any queued top-center message] | [No] |
| [Critical Warning (low health, hazard)] | [CombatSystem / EnvironmentSystem] | [Screen edge vignette + text at center-bottom] | [Persistent while condition active] | [Fade in 200ms; fades out 500ms when condition clears] | [1 per warning type] | [Critical — never suppressed] | [Renders immediately, bypasses all queues] | [No] |
| [Achievement Unlocked] | [AchievementSystem] | [Bottom Right — distinct from item toasts] | [4000] | [Slide in from right with icon expansion 300ms / fade out 400ms] | [1] | [Low] | [Queues behind item toasts; never more than one achievement toast at a time] | [No] |
| [Hint / Tutorial] | [TutorialSystem] | [Bottom Center] | [Persistent — until player performs the action or dismisses] | [Fade in 300ms] | [1] | [Medium] | [Only one tutorial hint at a time; queue others] | [Yes — B button / Esc] |
| 通知类型 | 触发系统 | 屏幕位置 | 持续时间（ms） | 动画入/出 | 最大同时 | 优先级 | 队列行为 | 可消除？ |
|------------------|---------------|-----------------|--------------|-------------------|-----------------|----------|---------------|-------------|
| [物品拾取] | [InventorySystem] | [右下 — toast] | [2000] | [从右滑入 200ms / 淡出 300ms] | [3 个堆叠] | [低] | [FIFO 队列；新 toast 进入时旧的被推上] | [否 — 自动消除] |
| [获得经验] | [ProgressionSystem] | [右下 — toast，物品 toast 下方] | [1500] | [淡入 150ms / 淡出 300ms] | [1 — 经验消息合并："XP +150"] | [极低 — 战斗中抑制，战斗后排队] | [战斗感知队列] | [否] |
| [升级] | [ProgressionSystem] | [屏幕中心 — 持续直到消除] | [持久 — 需输入消除] | [从 80% 放大 + 淡入 400ms] | [1] | [高 — 中断普通 toast] | [暂停所有其他通知直到消除] | [是 — 任何输入] |
| [任务更新] | [QuestSystem] | [顶部居中] | [4000] | [从顶部滑下 250ms / 淡出 400ms] | [1 — 顶部居中是单消息区] | [中] | [若任务更新到达时前一个仍可见，延长持续时间 2000ms；不堆叠] | [否] |
| [目标完成] | [QuestSystem] | [顶部居中] | [3000] | [同任务更新但加完成音效] | [1] | [中高 — 抢占任务更新] | [抢占任何排队的顶部居中消息] | [否] |
| [关键警告（低生命、危险）] | [CombatSystem / EnvironmentSystem] | [屏幕边缘暗角 + 中下方文本] | [条件激活时持续] | [淡入 200ms；条件清除时淡出 500ms] | [每警告类型 1 个] | [关键 — 永不抑制] | [立即渲染，绕过所有队列] | [否] |
| [成就解锁] | [AchievementSystem] | [右下 — 与物品 toast 区分] | [4000] | [从右滑入并图标展开 300ms / 淡出 400ms] | [1] | [低] | [排在物品 toast 后；一次最多一个成就 toast] | [否] |
| [提示 / 教程] | [TutorialSystem] | [底部居中] | [持久 — 直到玩家执行动作或消除] | [淡入 300ms] | [1] | [中] | [一次仅一个教程提示；其他排队] | [是 — B 键 / Esc] |

**Notification queue rules**:
**通知队列规则**：
1. Combat-aware queue: notifications tagged as Low priority are queued, not displayed, when the player is in combat state. The queue is flushed in a batch when the player exits combat, with a max of 3 items displayed in sequence.
1. 战斗感知队列：玩家处于战斗状态时，标记为低优先级的通知被排队而非显示。玩家退出战斗时队列批量刷新，最多 3 项依次显示。
2. Merge rule: identical notification types that fire within 500ms of each other are merged into a single notification with a combined value (e.g., "Item Pickup x3" rather than three separate toasts).
2. 合并规则：在 500ms 内相继触发的相同通知类型合并为带合并值的单个通知（例如，"物品拾取 x3" 而非三个单独 toast）。
3. Critical notifications (health warning, environmental hazard) are never queued, never merged, and always displayed immediately regardless of combat state or existing notifications.
3. 关键通知（生命警告、环境危险）永不排队、永不合并，且无论战斗状态或现有通知都立即显示。

---

## 9. Platform Adaptation
## 9. 平台适配

> **Why this section exists**: A HUD designed at 1920x1080 on a monitor may be
> illegible on a 55-inch TV at 4K, broken at 1280x720 on Steam Deck, or hidden
> behind a notch on mobile. Platform adaptation is not optional post-ship work —
> it is a design requirement that must be specified before implementation so the
> architecture can support it from the start. Every platform listed here requires
> explicit layout testing before certification.
> **为何存在此节**：在显示器上以 1920x1080 设计的 HUD 可能在 55 寸 4K 电视上不可读，
> 在 Steam Deck 的 1280x720 上破损，或在移动设备上被刘海遮挡。平台适配不是可选的
> 发布后工作 — 它是必须在实施前规定的设计要求，以便架构从一开始就支持。
> 此处列出的每个平台在认证前都需要显式布局测试。

| Platform | Safe Zone | Resolution Range | Input Method | HUD-Specific Notes |
|----------|-----------|-----------------|-------------|-------------------|
| [PC — Windows, 1920x1080 reference] | [3% margin] | [1280x720 min to 3840x2160 max] | [Mouse + keyboard, controller optional] | [HUD must scale correctly at all resolutions. Test at 1280x720 — minimum before cert. Consider ultrawide (21:9) — minimap must not stretch.] |
| [PC — Steam Deck, 1280x800] | [5% margin] | [Fixed 1280x800] | [Controller + touchscreen] | [Smaller screen means minimum text sizes are critical. Test ALL elements at this resolution. Touch targets irrelevant (controller-only by default).] |
| [PlayStation 5 / Xbox Series X] | [10% margin] | [1080p to 4K] | [Controller] | [Console certification requires TV safe zone compliance. Action-safe is 90% of screen area. Test on a real TV, not a monitor — overscan behavior differs.] |
| [Mobile — iOS / Android] | [15% top, 10% other sides] | [360x640 min to 414x896 common] | [Touch] | [Notch/camera cutout avoidance at top. Bottom home indicator zone avoidance. Portrait and landscape layouts may differ significantly — specify both.] |
| 平台 | 安全区 | 分辨率范围 | 输入方法 | HUD 专属备注 |
|----------|-----------|-----------------|-------------|-------------------|
| [PC — Windows，1920x1080 参考] | [3% 边距] | [1280x720 最低至 3840x2160 最高] | [鼠标 + 键盘，控制器可选] | [HUD 必须在所有分辨率下正确缩放。在 1280x720 测试 — 认证前最低。考虑超宽屏（21:9）— 小地图不得拉伸。] |
| [PC — Steam Deck，1280x800] | [5% 边距] | [固定 1280x800] | [控制器 + 触屏] | [较小屏幕意味着最小文本尺寸至关重要。在此分辨率下测试所有元素。触控目标无关（默认仅控制器）。] |
| [PlayStation 5 / Xbox Series X] | [10% 边距] | [1080p 至 4K] | [控制器] | [主机认证要求电视安全区合规。动作安全区为屏幕区域的 90%。在真实电视上测试，非显示器 — 过扫行为不同。] |
| [移动 — iOS / Android] | [顶部 15%，其他边 10%] | [360x640 最低至 414x896 常见] | [触控] | [顶部避免刘海/摄像头开孔。底部避免主页指示区。竖屏与横屏布局可能显著不同 — 两者都规定。] |

**HUD repositionability requirement**: Players must be able to reposition at minimum the following elements using an in-game HUD layout editor (required for accessibility compliance on console):
**HUD 可重新定位要求**：玩家必须能够使用游戏内 HUD 布局编辑器重新定位至少以下元素（主机无障碍合规所需）：
- Health bar
- Minimap
- Ability bar (if present)
- 血条
- 小地图
- 能力栏（如存在）

Repositioning saves to player profile, not to a single slot. Applies across play sessions.
重新定位保存到玩家资料，而非单个槽位。跨游玩会话适用。

---

## 10. Accessibility — HUD Specific
## 10. 无障碍 — HUD 专属

> **Why this section exists**: HUD accessibility failures are the most visible
> accessibility failures in games — players encounter the HUD in every session,
> in every gameplay moment. Color-blind failures, illegible text at minimum scale,
> and inability to disable distracting animations are among the top accessibility
> complaints in game reviews. This section defines HUD-specific requirements; refer
> to the project's `docs/accessibility-requirements.md` for the full project standard.
> **为何存在此节**：HUD 无障碍失败是游戏中最可见的无障碍失败 — 玩家在每次会话、
> 每个游戏时刻都遇到 HUD。色盲失败、最小尺寸下不可读文本和无法禁用分散注意力的动画
> 是游戏评论中最常见的无障碍投诉。此节定义 HUD 专属要求；
> 项目完整标准请参考 `docs/accessibility-requirements.md`。

### 10.1 Colorblind Modes
### 10.1 色盲模式

| Element | Color-Only Information Risk | Colorblind Mode Fix |
|---------|----------------------------|---------------------|
| [Health bar fill] | [Red = low health uses red/green distinction] | [Add icon pulse + vignette as non-color indicators. Red fill is supplemental, not sole indicator.] |
| [Damage numbers] | [Red = taken, green = healed] | [Add minus (-) prefix for damage, plus (+) for healing. Symbols, not color.] |
| [Enemy health bars] | [If colored by faction or threat level] | [Add text label or icon badge for faction/threat level. Never color-only.] |
| [Status effect icons] | [If icon tint communicates status type] | [All status icons must have distinct shapes, not just distinct colors. Shape encodes meaning; color is secondary.] |
| [Minimap icons] | [If player vs. enemy vs. objective distinguished by color] | [Distinct icon shapes: circle = player, triangle = enemy, star = objective. Color supplements shape.] |
| 元素 | 仅颜色信息风险 | 色盲模式修复 |
|---------|----------------------------|---------------------|
| [血条填充] | [红 = 低生命使用红/绿区分] | [添加图标脉冲 + 暗角作为非颜色指示器。红色填充是补充，非唯一指示器。] |
| [伤害数字] | [红 = 受伤，绿 = 治疗] | [伤害加减号（-）前缀，治疗加加号（+）。符号，非颜色。] |
| [敌人血条] | [如按阵营或威胁等级着色] | [添加阵营/威胁等级的文本标签或图标徽章。绝不仅颜色。] |
| [状态效果图标] | [如图标色调传达状态类型] | [所有状态图标必须有不同形状，不仅是不同颜色。形状编码意义；颜色是次要。] |
| [小地图图标] | [如玩家 vs. 敌人 vs. 目标以颜色区分] | [不同图标形状：圆 = 玩家，三角 = 敌人，星 = 目标。颜色补充形状。] |

### 10.2 Text Scaling
### 10.2 文本缩放

[Describe what happens when the player sets the UI text scale to 150% (the maximum required for your Accessibility Tier). Which elements reflow? Which elements clip? Which elements are architecturally blocked from scaling (e.g., fixed-size canvases)?
[描述玩家将 UI 文本缩放设为 150%（你的无障碍层级所需最大值）时发生什么。哪些元素回流？哪些元素裁剪？哪些元素在架构上被阻止缩放（例如，固定尺寸画布）？]

Example: "Health bar numerical label grows with text scale — bar expands slightly to accommodate. Quest objective text wraps at 150% scale — verify Top Center zone can accommodate two-line objectives. Damage numbers do not scale (they are world-space, not screen-space) — this is an accepted limitation documented here."]
示例："血条数字标签随文本缩放增长 — 条略微扩展以容纳。任务目标文本在 150% 缩放时换行 — 验证顶部居中区可容纳两行目标。伤害数字不缩放（它们是世界空间，非屏幕空间）— 这是此处记录的已接受限制。"]

**Text scaling test matrix**:
**文本缩放测试矩阵**：

| Element | 100% (baseline) | 125% | 150% | Overflow behavior |
|---------|----------------|------|------|-------------------|
| [Health bar label] | [Pass] | [Pass] | [TBD] | [Bar expands; does not overlap stamina bar] |
| [Quest objective text] | [Pass] | [TBD] | [TBD] | [Wraps to second line; zone height expands] |
| [Notification toast text] | [Pass] | [TBD] | [TBD] | [Toast width expands to max 35% screen width, then wraps] |
| [Subtitle text] | [Pass] | [TBD] | [TBD] | [Dedicated subtitle zone — must accommodate scale] |
| 元素 | 100%（基线） | 125% | 150% | 溢出行为 |
|---------|----------------|------|------|-------------------|
| [血条标签] | [通过] | [通过] | [待定] | [条扩展；不与耐力条重叠] |
| [任务目标文本] | [通过] | [待定] | [待定] | [换行到第二行；区域高度扩展] |
| [通知 toast 文本] | [通过] | [待定] | [待定] | [Toast 宽度扩展到最大 35% 屏幕宽，然后换行] |
| [字幕文本] | [通过] | [待定] | [待定] | [专用字幕区 — 必须容纳缩放] |

### 10.3 Motion Sensitivity
### 10.3 动效敏感

| Animation / Motion Element | Severity | Disabled by Reduced Motion Setting? | Replacement Behavior |
|---------------------------|----------|-------------------------------------|---------------------|
| [Health bar low-HP pulse] | [Mild] | [Yes] | [Solid fill, no pulse. Vignette remains as it is less likely to trigger sensitivity.] |
| [Screen edge vignette] | [Moderate] | [Optional — separate toggle] | [Replace with static darkened corners at 30% opacity] |
| [Damage numbers float upward] | [Mild] | [Yes] | [Instant appear/disappear in place, no float] |
| [Notification toast slide-in] | [Mild] | [Yes] | [Instant appear at final position] |
| [Level up center animation] | [High] | [Yes — required] | [Static level up card, no scale animation, no particle effects] |
| [Combo counter scale pulse] | [Mild] | [Yes] | [Number increments without scale animation] |
| 动画 / 动效元素 | 严重度 | 减少动效设置是否禁用？ | 替代行为 |
|---------------------------|----------|-------------------------------------|---------------------|
| [血条低生命脉冲] | [轻] | [是] | [实心填充，无脉冲。暗角保留，因为它较少可能触发敏感。] |
| [屏幕边缘暗角] | [中] | [可选 — 单独切换] | [替换为 30% 不透明度的静态暗角] |
| [伤害数字上浮] | [轻] | [是] | [原位即时出现/消失，无上浮] |
| [通知 toast 滑入] | [轻] | [是] | [在最终位置即时出现] |
| [升级中心动画] | [高] | [是 — 必须] | [静态升级卡片，无缩放动画，无粒子效果] |
| [连击计数器缩放脉冲] | [轻] | [是] | [数字递增无缩放动画] |

### 10.4 Subtitles Specification
### 10.4 字幕规格

> Subtitles are the highest-impact accessibility feature in the HUD. Specify them
> with the same rigor as the rest of the HUD. Do not leave subtitle behavior to
> implementation discretion.
> 字幕是 HUD 中影响最大的无障碍功能。以与其余 HUD 相同的严谨度规定它们。
> 不要将字幕行为留给实施自行决定。

- **Default setting**: [ON or OFF — document your game's default and the rationale. Industry standard is ON by default.]
- **Position**: Bottom Center zone, centered horizontally, above the bottom safe zone margin
- **Max characters per line**: [42 characters — the readable limit for subtitle lines at minimum text size on TV viewing distance]
- **Max simultaneous lines**: [2 lines before scrolling — do not display more than 2 lines at once]
- **Speaker identification**: [Speaker name displayed in color or above subtitle text — never rely on color alone; add colon prefix: "ARIA: The door is locked."]
- **Background**: [Semi-transparent black panel, 70% opacity, behind all subtitle text — ensures contrast against any game world background]
- **Font size minimum**: [24px at 1080p reference — scales with text scale setting]
- **Line break behavior**: [Break at natural language pause points — before conjunctions, after commas, never mid-word]
- **Subtitle persistence**: [Each subtitle line holds for the duration of the spoken line plus 300ms after it ends — never disappear while audio is still playing]
- **Non-dialogue captions**: [Document whether ambient sounds, music descriptions, and sound effects are captioned — e.g., "[tense music]", "[explosion in the distance]" — and where these appear if different from dialogue subtitles]
- **默认设置**：[开或关 — 记录你的游戏默认及理由。行业标准为默认开。]
- **位置**：底部居中区，水平居中，底部安全区边距之上
- **每行最大字符数**：[42 字符 — 电视观看距离下最小文本尺寸字幕行的可读上限]
- **最大同时行数**：[滚动前 2 行 — 一次不超过 2 行]
- **说话者识别**：[说话者名以颜色显示或在字幕文本上方 — 永不仅靠颜色；加冒号前缀："ARIA：门锁了。"]
- **背景**：[半透明黑色面板，70% 不透明度，所有字幕文本之后 — 确保对任何游戏世界背景的对比度]
- **最小字体尺寸**：[1080p 参考下 24px — 随文本缩放设置缩放]
- **换行行为**：[在自然语言停顿点换行 — 连词之前，逗号之后，永不词中间]
- **字幕持久性**：[每行字幕保持口语行的持续时间加上结束后 300ms — 音频仍在播放时永不消失]
- **非对话字幕**：[记录环境声、音乐描述和音效是否被字幕化 — 例如，"[紧张的音乐]"，"[远处的爆炸]" — 以及如与对话字幕不同时这些出现在何处]

### 10.5 HUD Opacity and Visibility Controls
### 10.5 HUD 不透明度与可见性控制

The following player-adjustable settings must be available from the Accessibility menu:
以下玩家可调设置必须可从无障碍菜单获得：

| Setting | Range | Default | Effect |
|---------|-------|---------|--------|
| [HUD Opacity — Global] | [0% (HUD hidden) to 100%] | [100%] | [Scales all HUD element opacities simultaneously] |
| [HUD Text Scale] | [75% to 150%] | [100%] | [Scales all HUD text elements; layout adapts] |
| [Damage Number Visibility] | [On / Off] | [On] | [Enables or disables all floating damage numbers] |
| [Minimap Visibility] | [On / Off / Compass Only] | [On] | [Compass strip shown as fallback when minimap off] |
| [Notification Verbosity] | [All / Important Only / Off] | [All] | [All = all toasts; Important Only = quest + level up; Off = no toasts] |
| [Motion Reduction] | [On / Off] | [Off] | [When On, replaces all animated HUD transitions with instant state changes] |
| [High Contrast Mode] | [On / Off] | [Off] | [Applies high contrast visual theme to all HUD elements — see art bible for HC variants] |
| 设置 | 范围 | 默认 | 效果 |
|---------|-------|---------|--------|
| [HUD 不透明度 — 全局] | [0%（HUD 隐藏）至 100%] | [100%] | [同时缩放所有 HUD 元素不透明度] |
| [HUD 文本缩放] | [75% 至 150%] | [100%] | [缩放所有 HUD 文本元素；布局适应] |
| [伤害数字可见性] | [开 / 关] | [开] | [启用或禁用所有浮动伤害数字] |
| [小地图可见性] | [开 / 关 / 仅罗盘] | [开] | [小地图关时罗盘条作为后备显示] |
| [通知详细度] | [全部 / 仅重要 / 关] | [全部] | [全部 = 所有 toast；仅重要 = 任务 + 升级；关 = 无 toast] |
| [减少动效] | [开 / 关] | [关] | [开启时将所有动画 HUD 过渡替换为即时状态改变] |
| [高对比度模式] | [开 / 关] | [关] | [对所有 HUD 元素应用高对比度视觉主题 — HC 变体见美术指南] |

---

## 11. Tuning Knobs
## 11. 调优旋钮

> **Why this section exists**: HUD behavior should be data-driven to the same degree
> as gameplay systems. Values that are hardcoded are values that require an engineer
> to change. Values that are in config can be tuned by a designer or adjusted for
> player preferences. Document all tunable parameters before implementation so the
> programmer knows which values to externalize.
> **为何存在此节**：HUD 行为应在与游戏系统相同程度上数据驱动。
> 硬编码的值是需工程师更改的值。配置中的值可由设计师调优或为玩家偏好调整。
> 在实施前记录所有可调参数以便程序员知道哪些值要外部化。

| Parameter | Current Value | Range | Effect of Increase | Effect of Decrease | Player Adjustable? | Notes |
|-----------|-------------|-------|-------------------|-------------------|-------------------|-------|
| [Notification display duration (default)] | [2000ms] | [500ms – 5000ms] | [Toasts persist longer — less likely to be missed, more screen clutter] | [Toasts disappear faster — cleaner, higher miss risk] | [No — but player can adjust verbosity level] | [Per-type overrides in Section 8 take precedence] |
| [Notification queue max size] | [8] | [3 – 15] | [More messages preserved but queue takes longer to clear] | [Older messages dropped earlier] | [No] | [Expand if playtesting reveals important messages being lost] |
| [Health bar low-HP pulse frequency] | [1 Hz] | [0.5 – 2 Hz] | [More urgent feeling — can become fatiguing] | [Calmer — may fail to communicate urgency] | [No — but Reduced Motion disables it] | [Linked to accessibility setting] |
| [Combat HUD reveal duration] | [0ms (instant)] | [0 – 300ms] | [Softer reveal — feels less jarring] | [Instant — highest responsiveness] | [No] | [Keep at 0ms — combat information must be instant] |
| [Exploration HUD fade-out delay] | [10000ms (10s after last threat)] | [3000 – 30000ms] | [HUD fades sooner — cleaner exploration] | [HUD stays longer — more reassurance] | [No] | [Tune based on playtest; 10s is a starting estimate] |
| [Minimap range (world units visible)] | [80] | [40 – 200] | [More map context visible] | [Tighter local view] | [Yes — Small/Medium/Large preset] | [Exposed as S/M/L, not raw unit value] |
| [Minimap size (px radius at 1080p)] | [75] | [50 – 120] | [Larger map, more screen space consumed] | [Smaller, less intrusive] | [Yes — S/M/L preset] | [Three sizes exposed to player] |
| [Damage number duration (ms)] | [800] | [400 – 1500] | [Numbers linger longer — easier to read, more cluttered] | [Numbers clear faster — cleaner, harder to parse] | [No] | [Tune based on visual noise in dense combat] |
| [Global HUD opacity] | [100%] | [0 – 100%] | [Fully visible] | [Fully hidden] | [Yes — opacity slider in Accessibility settings] | [0% = full HUD off; some players prefer this] |
| 参数 | 当前值 | 范围 | 增加效果 | 减少效果 | 玩家可调？ | 备注 |
|-----------|-------------|-------|-------------------|-------------------|-------------------|-------|
| [通知显示持续时间（默认）] | [2000ms] | [500ms – 5000ms] | [Toast 持续更久 — 不易错过，更多屏幕杂乱] | [Toast 消失更快 — 更干净，更高错过风险] | [否 — 但玩家可调详细度级别] | [第 8 节的类型专属覆盖优先] |
| [通知队列最大尺寸] | [8] | [3 – 15] | [保留更多消息但队列清理更久] | [更早丢弃旧消息] | [否] | [若试玩显示重要消息丢失则扩展] |
| [血条低生命脉冲频率] | [1 Hz] | [0.5 – 2 Hz] | [更紧迫感 — 可能令人疲劳] | [更平静 — 可能无法传达紧迫] | [否 — 但减少动效会禁用它] | [链接到无障碍设置] |
| [战斗 HUD 揭示持续时间] | [0ms（即时）] | [0 – 300ms] | [更柔和揭示 — 感觉不那么突兀] | [即时 — 最高响应] | [否] | [保持 0ms — 战斗信息必须即时] |
| [探索 HUD 淡出延迟] | [10000ms（最后威胁后 10 秒）] | [3000 – 30000ms] | [HUD 更早淡出 — 更干净探索] | [HUD 持续更久 — 更多保证] | [否] | [基于试玩调优；10 秒是起始估算] |
| [小地图范围（可见世界单位）] | [80] | [40 – 200] | [更多地图上下文可见] | [更紧凑局部视图] | [是 — 小/中/大预设] | [以 S/M/L 暴露，非原始单位值] |
| [小地图尺寸（1080p 下 px 半径）] | [75] | [50 – 120] | [更大地图，更多屏幕空间消耗] | [更小，更不干扰] | [是 — S/M/L 预设] | [三种尺寸暴露给玩家] |
| [伤害数字持续时间（ms）] | [800] | [400 – 1500] | [数字持续更久 — 更易读，更杂乱] | [数字清理更快 — 更干净，更难解析] | [否] | [基于密集战斗中视觉噪声调优] |
| [全局 HUD 不透明度] | [100%] | [0 – 100%] | [完全可见] | [完全隐藏] | [是 — 无障碍设置中不透明度滑块] | [0% = 完全关闭 HUD；部分玩家偏好此] |

---

## 12. Acceptance Criteria
## 12. 验收标准

> **Why this section exists**: These criteria are the certification checklist for the
> HUD. Every item must pass before the HUD can be marked Approved. QA must be able
> to verify each item independently.
> **为何存在此节**：这些标准是 HUD 的认证检查表。每项必须在 HUD 可标记为已批准之前通过。
> QA 必须能独立验证每项。

**Layout & Visibility**
**布局与可见性**
- [ ] All HUD elements are within platform safe zone margins on all target platforms
- [ ] No two HUD elements overlap in any documented gameplay context
- [ ] HUD occupies less than [12]% of screen area in exploration context (measure at reference resolution)
- [ ] HUD occupies less than [22]% of screen area in combat context
- [ ] No HUD element occupies the center [40]% of screen during exploration (crosshair excepted during combat)
- [ ] All HUD elements are visible and legible at minimum supported resolution on all platforms
- [ ] 所有 HUD 元素在所有目标平台上都在平台安全区边距内
- [ ] 在任何记录的游戏上下文中无两个 HUD 元素重叠
- [ ] 探索上下文中 HUD 占屏幕区域少于 [12]%（在参考分辨率下测量）
- [ ] 战斗上下文中 HUD 占屏幕区域少于 [22]%
- [ ] 探索期间无 HUD 元素占据屏幕中心 [40]%（战斗时准星除外）
- [ ] 所有 HUD 元素在所有平台最低支持分辨率下可见且可读

**Per-Context Correctness**
**按上下文正确性**
- [ ] HUD correctly shows only specified elements in every context defined in Section 5
- [ ] Context transitions (combat enter/exit, dialogue, cinematic) show correct elements within transition timing spec
- [ ] Boss health bar appears correctly on boss encounter trigger and disappears after boss defeat
- [ ] Death state correctly hides all gameplay HUD elements
- [ ] HUD 在第 5 节定义的每个上下文中正确仅显示指定元素
- [ ] 上下文过渡（战斗进入/退出、对话、电影）在过渡时序规格内显示正确元素
- [ ] Boss 血条在 Boss 遭遇触发时正确出现并在 Boss 失败后消失
- [ ] 死亡状态正确隐藏所有游戏 HUD 元素

**Accessibility**
**无障碍**
- [ ] All HUD text elements meet 4.5:1 contrast ratio against all backgrounds they appear over (test light AND dark scenes)
- [ ] No HUD element uses color as the ONLY differentiator (verify: remove color from each element and confirm information is still communicated)
- [ ] Subtitles appear for all voiced lines and ambient dialogue when subtitle setting is enabled
- [ ] Subtitle text never disappears while audio is still playing
- [ ] Reduced Motion setting disables all HUD animations listed in Section 10.3
- [ ] Text Scale 150% does not cause any HUD text to overflow its container or overlap another element
- [ ] All player-adjustable HUD settings in Section 10.5 are functional and persist between sessions
- [ ] 所有 HUD 文本元素对它们出现的所有背景满足 4.5:1 对比度（测试浅色与深色场景）
- [ ] 无 HUD 元素仅用颜色作为区分器（验证：从每个元素移除颜色并确认信息仍被传达）
- [ ] 字幕设置启用时所有配音行和环境对话出现字幕
- [ ] 音频仍在播放时字幕文本永不消失
- [ ] 减少动效设置禁用第 10.3 节列出的所有 HUD 动画
- [ ] 文本缩放 150% 不导致任何 HUD 文本溢出其容器或与另一元素重叠
- [ ] 第 10.5 节所有玩家可调 HUD 设置功能正常并在会话间持久

**Notifications**
**通知**
- [ ] Notifications of the same type that fire within 500ms merge into a single notification
- [ ] Low-priority notifications are queued (not displayed) during combat and released post-combat
- [ ] Critical warnings (low health, hazard) appear immediately regardless of queue state or combat state
- [ ] No more than [3] notification toasts are visible simultaneously
- [ ] Notification queue is cleared correctly on level transition (no stale notifications from previous area)
- [ ] 500ms 内相继触发的相同类型通知合并为单个通知
- [ ] 低优先级通知在战斗期间排队（不显示）并在战斗后释放
- [ ] 关键警告（低生命、危险）无论队列状态或战斗状态都立即出现
- [ ] 同时可见的通知 toast 不超过 [3] 个
- [ ] 关卡过渡时通知队列正确清除（前一区域无过时通知）

**Platform**
**平台**
- [ ] All elements respect 10% safe zone margins on console (test on physical TV — not monitor)
- [ ] HUD displays correctly at 1280x720 (Steam Deck) with no element clipping or overlap
- [ ] HUD elements are repositionable (Health, Minimap, Ability Bar) and reposition settings persist
- [ ] Controller disconnection during play does not cause HUD state corruption
- [ ] 主机上所有元素尊重 10% 安全区边距（在物理电视上测试 — 非显示器）
- [ ] HUD 在 1280x720（Steam Deck）下正确显示，无元素裁剪或重叠
- [ ] HUD 元素可重新定位（生命、小地图、能力栏）且重新定位设置持久
- [ ] 游玩期间控制器断开不导致 HUD 状态损坏

---

## 13. Open Questions
## 13. 待解决问题

> Track unresolved design questions here. All questions must be resolved before
> the HUD design document can be marked Approved.
> 在此追踪未解决的设计问题。所有问题必须在 HUD 设计文档可标记为已批准之前解决。

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| [e.g., Should the minimap show enemy positions by default, or only after a detection skill is unlocked?] | [systems-designer + ui-designer] | [Sprint 5, Day 2] | [Pending — depends on progression GDD decision] |
| [e.g., Does the game have a boss health bar, or do bosses use the standard enemy health bar? Bosses need a visually distinct treatment if they are significantly more important than normal enemies.] | [game-designer] | [Sprint 5, Day 1] | [Pending] |
| [e.g., Damage numbers: diegetic (floating in world space, occluded by geometry) or screen space (always readable, overlaid on HUD layer)?] | [ui-designer + lead-programmer] | [Sprint 4, Day 5] | [Pending — architecture decision affects rendering layer choice] |
| [e.g., Mobile portrait vs. landscape: does the game support both orientations? If yes, each requires its own zone layout.] | [producer] | [Sprint 3, Day 3] | [Pending — platform scope decision required first] |
| 问题 | 负责人 | 截止日期 | 解决 |
|----------|-------|----------|-----------|
| [例如，小地图应默认显示敌人位置，还是仅在侦测技能解锁后？] | [systems-designer + ui-designer] | [Sprint 5, Day 2] | [待定 — 取决于进阶 GDD 决策] |
| [例如，游戏是否有 Boss 血条，还是 Boss 使用标准敌人血条？如 Boss 比普通敌人重要得多，则需要视觉上不同的处理。] | [game-designer] | [Sprint 5, Day 1] | [待定] |
| [例如，伤害数字：叙事性（在世界空间浮动、被几何遮挡）还是屏幕空间（始终可读、覆盖在 HUD 层上）？] | [ui-designer + lead-programmer] | [Sprint 4, Day 5] | [待定 — 架构决策影响渲染层选择] |
| [例如，移动竖屏 vs. 横屏：游戏是否支持两种方向？如是，每种需要其自己的区域布局。] | [producer] | [Sprint 3, Day 3] | [待定 — 需先做平台范围决策] |
