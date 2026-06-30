# Accessibility Requirements: [Game Title]
# 无障碍需求：[游戏名称]

> **Status**: Draft | Committed | Audited | Certified
> **状态**：草稿 | 已承诺 | 已审计 | 已认证
> **Author**: [ux-designer / producer]
> **作者**：[UX 设计师 / 制作人]
> **Last Updated**: [Date]
> **最后更新**：[日期]
> **Accessibility Tier Target**: [Basic / Standard / Comprehensive / Exemplary]
> **无障碍层级目标**：[基础 / 标准 / 全面 / 典范]
> **Platform(s)**: [PC / Xbox / PlayStation 5 / Nintendo Switch / iOS / Android]
> **平台**：[PC / Xbox / PlayStation 5 / Nintendo Switch / iOS / Android]
> **External Standards Targeted**:
> **目标外部标准**：
> - WCAG 2.1 Level [A / AA / AAA]
> - WCAG 2.1 等级 [A / AA / AAA]
> - AbleGamers CVAA Guidelines
> - AbleGamers CVAA 指南
> - Xbox Accessibility Guidelines (XAG) [Yes / No / Partial]
> - Xbox 无障碍指南 (XAG) [是 / 否 / 部分]
> - PlayStation Accessibility (Sony Guidelines) [Yes / No / Partial]
> - PlayStation 无障碍（索尼指南）[是 / 否 / 部分]
> - Apple / Google Accessibility Guidelines [Yes / No / N/A — mobile only]
> - Apple / Google 无障碍指南 [是 / 否 / 不适用 — 仅移动端]
> **Accessibility Consultant**: [Name and organization, or "None engaged"]
> **无障碍顾问**：[姓名与机构，或 "未聘请"]
> **Linked Documents**: `design/gdd/systems-index.md`, `docs/ux/interaction-pattern-library.md`
> **关联文档**：`design/gdd/systems-index.md`、`docs/ux/interaction-pattern-library.md`

> **Why this document exists**: Per-screen accessibility annotations belong in
> UX specs. This document captures the project-wide accessibility commitments,
> the feature matrix across all systems, the test plan, and the audit history.
> It is created once during Technical Setup by the UX designer and producer,
> then updated as features are added and audits are completed. If a feature
> conflicts with a commitment made here, this document wins — change the feature,
> not the commitment, unless the producer approves a formal revision.
> **本文档存在的原因**：逐屏的无障碍标注应放在 UX 规格中。本文档记录项目级的无障碍承诺、跨所有系统的功能矩阵、测试计划与审计历史。它由 UX 设计师与制作人在"技术设置"阶段创建一次，然后随功能增加与审计完成而更新。若某功能与本文档中的承诺冲突，本文档优先 — 修改功能而非承诺，除非制作人批准正式修订。
>
> **When to update**: After each `/gate-check` pass, after any accessibility
> audit, and whenever a new game system is added to `systems-index.md`.
> **何时更新**：每次 `/gate-check` 通过后、任何无障碍审计后，以及每当 `systems-index.md` 中新增游戏系统时。

---

## Accessibility Tier Definition
## 无障碍层级定义

> **Why define tiers**: Accessibility is not binary. Defining four tiers gives
> the team a shared vocabulary, forces an explicit commitment at the start of
> production, and prevents scope creep in both directions ("we'll add it later"
> and "we have to support everything"). The tiers below are this project's
> definitions — the industry uses similar but not identical language. Commit to
> a tier with specific feature targets, not just the tier name.
> **为何定义层级**：无障碍并非二元。定义四个层级为团队提供共享词汇，在生产开始时强制做出明确承诺，并防止双向范围蔓延（"以后再加" 与 "我们必须支持一切"）。以下层级是本项目的定义 — 业界使用相似但不完全相同的措辞。请以具体功能目标承诺某层级，而非仅层级名称。

### Tier Definitions
### 层级定义

| Tier | Core Commitment | Typical Effort |
|------|----------------|----------------|
| **Basic** | Critical player-facing text is readable at standard resolution. No feature requires color discrimination alone. Volume controls exist for music, SFX, and voice independently. The game is completable without photosensitivity risk. | Low — primarily design constraints |
| **Standard** | All of Basic, plus: full input remapping on all platforms, subtitle support with speaker identification, adjustable text size, at least one colorblind mode, and no timed input that cannot be extended or toggled. | Medium — requires dedicated implementation work |
| **Comprehensive** | All of Standard, plus: screen reader support for menus, mono audio option, difficulty assist modes, HUD element repositioning, reduced motion mode, and visual indicators for all gameplay-critical audio. | High — requires platform API integration and significant UI architecture |
| **Exemplary** | All of Comprehensive, plus: full subtitle customization (font, size, color, background, position), high contrast mode, cognitive load assist tools, tactile/haptic alternatives for all audio-only cues, and external third-party accessibility audit. | Very High — requires dedicated accessibility budget and specialist consultation |

| 层级 | 核心承诺 | 典型工作量 |
|------|----------------|----------------|
| **基础** | 关键的玩家可见文字在标准分辨率下可读。无功能仅靠颜色辨别。音乐、音效与配音有独立音量控制。游戏可完成且无光敏性风险。 | 低 — 主要是设计约束 |
| **标准** | 基础全部，加：所有平台全输入重映射、带说话人识别的字幕支持、可调文字大小、至少一种色盲模式、无可延长或切换的限时输入。 | 中 — 需要专门实现工作 |
| **全面** | 标准全部，加：菜单屏幕阅读器支持、单声道音频选项、难度辅助模式、HUD 元素重定位、减少动效模式、所有玩法关键音频的视觉指示。 | 高 — 需要平台 API 集成与显著 UI 架构工作 |
| **典范** | 全面全部，加：完整字幕自定义（字体、大小、颜色、背景、位置）、高对比度模式、认知负荷辅助工具、所有纯音频提示的触觉/震动替代、外部第三方无障碍审计。 | 极高 — 需要专门无障碍预算与专家咨询 |

### This Project's Commitment
### 本项目的承诺

**Target Tier**: [Standard]
**目标层级**：[标准]

**Rationale**: [Write 3-5 sentences justifying the tier choice. Do not simply
state the tier — explain the reasoning. Consider: What is the game's genre and
how does it map to common accessibility barriers (e.g., fast-twitch games have
motor barriers; reading-heavy games have visual barriers)? Who is the target
player and what does the research say about disability prevalence in that group?
What are the platform requirements (Xbox requires XAG compliance for ID@Xbox)?
What is the team's capacity? What would dropping one tier cost the player base,
in concrete terms?]
**理由**：[写 3-5 句话论证层级选择。不要仅陈述层级 — 解释推理。考虑：游戏类型是什么，它如何映射到常见无障碍障碍（例如快节奏游戏有运动障碍；阅读重的游戏有视觉障碍）？目标玩家是谁，研究对该群体中的残疾流行率怎么说？平台要求是什么（Xbox 的 ID@Xbox 要求 XAG 合规）？团队产能如何？降低一个层级会以具体数字损失多少玩家基础？]

Example: "This is a narrative RPG with turn-based combat targeted at players
25-45. The turn-based structure eliminates the most severe motor barriers common
in action games, but the reading-heavy design creates significant visual and
cognitive barriers. Standard tier addresses all of these. Exemplary tier is not
achievable without a dedicated accessibility engineer. Xbox ID@Xbox program
requires XAG compliance for Game Pass consideration, which Standard meets.
Dropping to Basic would exclude players who rely on colorblind modes or input
remapping, estimated at 8-12% of the target audience based on AbleGamers data."
示例："这是一款面向 25-45 岁玩家的回合制叙事 RPG。回合制结构消除了动作游戏中常见的最严重运动障碍，但重阅读设计带来显著的视觉与认知障碍。标准层级涵盖所有这些。典范层级无专门无障碍工程师不可达成。Xbox ID@Xbox 项目为考虑 Game Pass 要求 XAG 合规，标准层级满足此要求。降至基础层级会排除依赖色盲模式或输入重映射的玩家，按 AbleGamers 数据估计约占目标受众的 8-12%。"

**Features explicitly in scope (beyond tier baseline)**:
**明确在范围内的功能（超出层级基线）**：
- [e.g., "Full subtitle customization — elevated from Comprehensive because our
  game is dialogue-heavy and subtitles are a primary channel"]
- [例如 "完整字幕自定义 — 从全面层级提升，因为我们的游戏对话多且字幕是主要通道"]
- [e.g., "One-hand mode for controller — we have hold inputs critical to combat"]
- [例如 "控制器单手模式 — 我们有战斗关键的按住输入"]

**Features explicitly out of scope**:
**明确不在范围内的功能**：
- [e.g., "Screen reader for in-game world (not menus) — requires engine work
  beyond current capacity. Documented in Known Intentional Limitations."]
- [例如 "游戏世界（非菜单）的屏幕阅读器 — 需要超出当前产能的引擎工作。记录于已知有意局限。"]

---

## Visual Accessibility
## 视觉无障碍

> **Why this section comes first**: Visual impairments affect the largest
> proportion of players who use accessibility features. Color vision deficiency
> alone affects approximately 8% of men and 0.5% of women. Text legibility at
> TV viewing distance is frequently the single largest accessibility failure
> in shipped games. Document every visual feature before implementation begins,
> because retrofitting minimum text sizes or color decisions after assets are
> locked is expensive.
> **为何本节排在首位**：视觉障碍影响使用无障碍功能玩家的最大比例。仅色觉缺陷就影响约 8% 的男性和 0.5% 的女性。电视观看距离下的文字可读性常常是已发售游戏中最大的单一无障碍失败。在实现开始前记录每个视觉功能，因为在资产锁定后再补加最小文字尺寸或颜色决策代价高昂。

| Feature | Target Tier | Scope | Status | Implementation Notes |
|---------|-------------|-------|--------|---------------------|
| Minimum text size — menu UI | Standard | All menu screens | Not Started | 24px minimum at 1080p. At 4K, scale proportionally. Reference: WCAG 2.1 SC 1.4.4 requires text resizable to 200% without loss of content. |
| Minimum text size — subtitles | Standard | All voiced/captioned content | Not Started | 32px minimum at 1080p. Players viewing on TV at 3m are the constraint. |
| Minimum text size — HUD | Standard | In-game HUD | Not Started | 20px minimum for critical information (health, ammo, objective). Non-critical HUD elements may be smaller. |
| Text contrast — UI text on backgrounds | Standard | All UI text | Not Started | Minimum 4.5:1 ratio for body text (WCAG AA). 3:1 for large text (18px+ or 14px bold). Test with automated contrast checker on final color values. |
| Text contrast — subtitles | Standard | Subtitle display | Not Started | Minimum 7:1 ratio (WCAG AAA) for subtitles — players read them quickly and cannot control background. Use drop shadow or opaque background box by default. |
| Colorblind mode — Protanopia | Standard | All color-coded gameplay | Not Started | Red-green — affects ~6% of men. Primary concern: health bars, enemy indicators, map markers. Shift red signals to orange/yellow; shift green signals to teal. |
| Colorblind mode — Deuteranopia | Standard | All color-coded gameplay | Not Started | Green-red — affects ~1% of men. Similar to Protanopia in practical impact. Often the same palette adjustment covers both. Verify with Coblis or Colour Blindness Simulator. |
| Colorblind mode — Tritanopia | Standard | All color-coded gameplay | Not Started | Blue-yellow — rarer (~0.001%). Shift blue UI elements to purple; shift yellow to orange. |
| Color-as-only-indicator audit | Basic | All UI and gameplay | Not Started | List every place color is the SOLE differentiator in the table below. Each must have a non-color backup (icon, shape, pattern, text label) before shipping. |
| UI scaling | Standard | All UI elements | Not Started | Range: 75% to 150%. Default: 100%. Scaling must not break layout — test all screens at min and max. HUD scaling should be independent from menu scaling. |
| High contrast mode | Comprehensive | Menus (minimum); HUD (preferred) | Not Started | Replace all semi-transparent backgrounds with fully opaque. Replace mid-tone UI colors with black/white/system-high-contrast colors. All interactive elements outlined. |
| Brightness/gamma controls | Basic | Global | Not Started | Exposed in graphics settings. Include a reference calibration image (a gradient or symbol barely visible at correct calibration). Range: -50% to +50% from default. |
| Screen flash / strobe warning | Basic | All cutscenes, VFX | Not Started | (1) Pre-launch warning screen with photosensitivity seizure notice. (2) Audit all flash-heavy VFX against Harding FPA standard (no more than 3 flashes per second above luminance threshold). (3) Optional: flash reduction mode that lowers flash amplitude by 80%. |
| Motion/animation reduction mode | Standard | All UI transitions, camera shake, VFX | Not Started | Reduce or eliminate: screen shake, camera bob, motion blur, parallax scrolling in menus, looping background animations. Cannot fully eliminate: player movement animation (would break readability). Toggle in accessibility settings. |
| Subtitles — on/off | Basic | All voiced content | Not Started | Default: OFF (industry standard — many players prefer immersion). Prominently offered at first launch. |
| Subtitles — speaker identification | Standard | All voiced content | Not Started | Speaker name displayed before dialogue line. Color-coded by speaker IF colors differ by more than hue alone (test for colorblind compatibility). |
| Subtitles — style customization | Comprehensive | Subtitle display | Not Started | Font size (4 sizes minimum), background opacity (0–100%), text color (white / yellow / custom), position (bottom / top / player-relative). |
| Subtitles — sound effect captions | Comprehensive | Gameplay-critical SFX | Not Started | See Auditory Accessibility section for which SFX qualify. Format: [SOUND DESCRIPTION] in brackets, distinct from dialogue. |

| 功能 | 目标层级 | 范围 | 状态 | 实现备注 |
|---------|-------------|-------|--------|---------------------|
| 最小文字尺寸 — 菜单 UI | 标准 | 所有菜单屏 | 未开始 | 1080p 下最小 24px。4K 下按比例缩放。参考：WCAG 2.1 SC 1.4.4 要求文字可缩放至 200% 且无内容损失。 |
| 最小文字尺寸 — 字幕 | 标准 | 所有配音/字幕内容 | 未开始 | 1080p 下最小 32px。3 米外看电视的玩家是约束。 |
| 最小文字尺寸 — HUD | 标准 | 游戏内 HUD | 未开始 | 关键信息（生命、弹药、目标）最小 20px。非关键 HUD 元素可更小。 |
| 文字对比度 — 背景上的 UI 文字 | 标准 | 所有 UI 文字 | 未开始 | 正文字最小对比比 4.5:1（WCAG AA）。大字（18px+ 或 14px 粗体）3:1。用自动对比检查器在最终颜色值上测试。 |
| 文字对比度 — 字幕 | 标准 | 字幕显示 | 未开始 | 字幕最小对比比 7:1（WCAG AAA）— 玩家快速阅读且无法控制背景。默认使用投影或不透明背景框。 |
| 色盲模式 — 红色盲 | 标准 | 所有颜色编码玩法 | 未开始 | 红绿 — 影响约 6% 男性。主要关注：血条、敌人指示器、地图标记。将红色信号转为橙/黄；将绿色信号转为青。 |
| 色盲模式 — 绿色盲 | 标准 | 所有颜色编码玩法 | 未开始 | 绿红 — 影响约 1% 男性。实际影响与红色盲相似。常同一调色板调整可覆盖两者。用 Coblis 或 Colour Blindness Simulator 验证。 |
| 色盲模式 — 蓝色盲 | 标准 | 所有颜色编码玩法 | 未开始 | 蓝黄 — 更罕见（约 0.001%）。将蓝色 UI 元素转为紫；将黄转为橙。 |
| 颜色作为唯一指示器审计 | 基础 | 所有 UI 与玩法 | 未开始 | 在下表列出颜色是唯一区分项的每处。每处在发售前必须有非颜色备份（图标、形状、图案、文字标签）。 |
| UI 缩放 | 标准 | 所有 UI 元素 | 未开始 | 范围：75% 至 150%。默认：100%。缩放不得破坏布局 — 在最小与最大下测试所有屏。HUD 缩放应独立于菜单缩放。 |
| 高对比度模式 | 全面 | 菜单（最小）；HUD（首选） | 未开始 | 将所有半透明背景替换为完全不透明。将中调 UI 颜色替换为黑/白/系统高对比色。所有可交互元素加轮廓。 |
| 亮度/伽马控制 | 基础 | 全局 | 未开始 | 在图形设置中暴露。包含参考校准图（在正确校准下勉强可见的渐变或符号）。范围：相对默认 -50% 至 +50%。 |
| 屏幕闪烁/频闪警告 | 基础 | 所有过场、VFX | 未开始 | (1) 启动前光敏性癫痫警告屏。(2) 按 Harding FPA 标准审计所有高频闪 VFX（高于亮度阈值时每秒不超过 3 次闪烁）。(3) 可选：闪烁降低模式将闪烁幅度降低 80%。 |
| 动效/动画减少模式 | 标准 | 所有 UI 转换、镜头震动、VFX | 未开始 | 减少或消除：屏幕震动、镜头摆动、动态模糊、菜单视差滚动、循环背景动画。无法完全消除：玩家移动动画（会破坏可读性）。在无障碍设置中切换。 |
| 字幕 — 开/关 | 基础 | 所有配音内容 | 未开始 | 默认：关（业界标准 — 许多玩家偏好沉浸感）。首次启动时显著提供。 |
| 字幕 — 说话人识别 | 标准 | 所有配音内容 | 未开始 | 在对话行前显示说话人名。若颜色不仅靠色相区分（测试色盲兼容性），则按说话人颜色编码。 |
| 字幕 — 样式自定义 | 全面 | 字幕显示 | 未开始 | 字体大小（最少 4 档）、背景不透明度（0–100%）、文字颜色（白/黄/自定义）、位置（底部/顶部/相对玩家）。 |
| 字幕 — 音效字幕 | 全面 | 玩法关键音效 | 未开始 | 关于哪些音效符合条件，见听觉无障碍章节。格式：方括号内 [音效描述]，与对话区分。 |

### Color-as-Only-Indicator Audit
### 颜色作为唯一指示器审计

> Fill in every gameplay or UI element where color is currently the sole
> differentiator. Resolve each before shipping. A resolved entry has a non-color
> backup that works in all three colorblind modes above.
> 填入当前颜色是唯一区分项的每个玩法或 UI 元素。发售前逐一解决。已解决的条目需有在上述三种色盲模式下均可工作的非颜色备份。

| Location | Color Signal | What It Communicates | Non-Color Backup | Status |
|----------|-------------|---------------------|-----------------|--------|
| [Health bar] | [Red = low health] | [Player is near death] | [Bar also shows numeric value and flashes] | [Not Started] |
| [Minimap markers] | [Red = enemy, green = ally] | [Unit allegiance] | [Enemy markers are triangles; ally markers are circles] | [Not Started] |
| [Inventory item rarity] | [Color-coded border (grey/blue/purple/gold)] | [Item quality tier] | [Rarity name shown on hover/focus; icon star count] | [Not Started] |
| [Add row for each color-coded element] | | | | |

| 位置 | 颜色信号 | 它传达什么 | 非颜色备份 | 状态 |
|----------|-------------|---------------------|-----------------|--------|
| [血条] | [红 = 低血量] | [玩家濒死] | [条同时显示数值并闪烁] | [未开始] |
| [小地图标记] | [红 = 敌人，绿 = 友军] | [单位归属] | [敌人标记为三角形；友军标记为圆形] | [未开始] |
| [库存物品稀有度] | [颜色编码边框（灰/蓝/紫/金）] | [物品品质等级] | [悬停/聚焦时显示稀有度名；图标星数] | [未开始] |
| [为每个颜色编码元素添加行] | | | | |

---

## Motor Accessibility
## 运动无障碍

> **Why motor accessibility matters for games**: Games are more motor-demanding
> than most software. A web form requires precise clicks; a game may require
> rapid simultaneous button combinations held for specific durations. Motor
> impairments span a wide range — from tremor (affecting precision) to
> hemiplegia (one functional hand) to RSI (affecting hold duration). The AbleGamers
> Able Assistance program estimates 35 million gamers in the US have a disability
> affecting their ability to play. Many of the features below cost very little
> to implement if planned from the start, and are extremely expensive to add post-launch.
> **为何运动无障碍对游戏重要**：游戏比大多数软件对运动能力要求更高。网页表单要求精确点击；游戏可能要求快速同时按下特定时长的按钮组合。运动障碍范围广泛 — 从震颤（影响精度）到偏瘫（一只可用手）到 RSI（影响按住时长）。AbleGamers 的 Able Assistance 项目估计美国有 3500 万玩家患有影响其游玩能力的残疾。以下许多功能若从开始就规划则成本极低，而发售后再添加代价极高。

| Feature | Target Tier | Scope | Status | Implementation Notes |
|---------|-------------|-------|--------|---------------------|
| Full input remapping | Standard | All gameplay inputs, all platforms | Not Started | Every input bound by default must be rebindable. Remapping applies to keyboard, mouse, controller, and any supported peripheral independently. No two actions may be bound to the same input simultaneously (warn on conflict). Persist remapping to player profile. |
| Input method switching | Standard | PC | Not Started | Player must be able to switch between keyboard/mouse and gamepad at any moment without restarting. UI must update prompts dynamically (show correct button icons for active input method). |
| One-hand mode | [Tier] | [Identify which features require two simultaneous hands] | Not Started | Audit every multi-input action. For each: can it be executed with a single hand? If not, provide a toggle alternative or hold-to-toggle version. Specify here which features have a one-hand path and which do not. |
| Hold-to-press alternatives | Standard | All hold inputs | Not Started | Every "hold [button] to [action]" must offer a toggle alternative. Toggle mode: first press activates, second press deactivates. Example: "Hold to sprint" becomes optional "toggle sprint" mode. List all hold inputs in the game here. |
| Rapid input alternatives | Standard | Any button mashing / rapid input sequences | Not Started | Any input requiring more than 3 presses per second sustained must offer a single-press toggle alternative. Example: Hades' "Hold to dash repeatedly" solves this elegantly. |
| Input timing adjustments | Standard | QTEs, timed button presses, rhythm inputs | Not Started | Provide a timing window multiplier in accessibility settings. Minimum range: 0.5x to 3.0x. Default: 1.0x. At 3.0x, a 500ms window becomes 1500ms. Document every timed input in this game and test at all multiplier values. |
| Aim assist | Standard | All ranged combat / targeting | Not Started | Not just on/off — provide granularity: Assist Strength (0–100%), Assist Radius, Aim Magnetism (snap-to-target), and Aim Slowdown (near-target deceleration) as separate sliders. Default values should be tuned to feel helpful, not intrusive. |
| Auto-sprint / movement assists | Standard | Movement systems | Not Started | "Hold to sprint" toggle (covered above). Additionally: auto-run option (hold direction, player continues without input). Specify any movement input that is held continuously in normal gameplay. |
| Platforming / traversal assists | [Tier] | [If game has platforming] | Not Started | Evaluate whether auto-grab (generous ledge detection), coyote time extension, and jump height adjustment are appropriate for this game's design. If platforming is not a game system, mark N/A. |
| HUD element repositioning | Comprehensive | All HUD elements | Not Started | Allow players to move health bars, minimaps, and quest trackers to their preferred screen position. Particularly important for players using head-tracking or eye-gaze hardware who may have reduced peripheral vision coverage. |

| 功能 | 目标层级 | 范围 | 状态 | 实现备注 |
|---------|-------------|-------|--------|---------------------|
| 完整输入重映射 | 标准 | 所有玩法输入、所有平台 | 未开始 | 默认绑定的每个输入必须可重绑。重映射独立应用于键盘、鼠标、手柄及任何支持的外设。两个动作不得同时绑定到同一输入（冲突时警告）。重映射持久化至玩家档案。 |
| 输入方式切换 | 标准 | PC | 未开始 | 玩家必须能任意时刻在键鼠与手柄之间切换而无需重启。UI 必须动态更新提示（显示当前输入方式的正确按钮图标）。 |
| 单手模式 | [层级] | [识别哪些功能需要双手同时操作] | 未开始 | 审计每个多输入动作。对每个：能否单手执行？若不能，提供切换替代或按住转切换版本。在此指明哪些功能有单手路径、哪些没有。 |
| 按住转替代 | 标准 | 所有按住输入 | 未开始 | 每个 "按住 [按钮] 以 [动作]" 必须提供切换替代。切换模式：首次按下激活、第二次取消。例如 "按住冲刺" 变为可选的 "切换冲刺" 模式。在此列出游戏中所有按住输入。 |
| 快速输入替代 | 标准 | 任何连按/快速输入序列 | 未开始 | 任何要求每秒超过 3 次持续按下的输入必须提供单按切换替代。例如 Hades 的 "按住连续闪避" 优雅地解决此问题。 |
| 输入时序调整 | 标准 | QTE、定时按钮、节奏输入 | 未开始 | 在无障碍设置中提供时序窗口倍率。最小范围：0.5x 至 3.0x。默认：1.0x。在 3.0x 时，500ms 窗口变为 1500ms。记录本游戏中每个定时输入并在所有倍率值下测试。 |
| 瞄准辅助 | 标准 | 所有远程战斗/目标 | 未开始 | 不仅是开/关 — 提供细粒度：辅助强度（0–100%）、辅助半径、瞄准磁吸（吸附目标）、瞄准减速（近目标减速）作为独立滑杆。默认值应调至感觉有帮助而非干扰。 |
| 自动冲刺/移动辅助 | 标准 | 移动系统 | 未开始 | "按住冲刺" 切换（已在上方涵盖）。此外：自动奔跑选项（按住方向，玩家无需输入继续）。指明正常玩法中任何持续按住的移动输入。 |
| 平台跳跃/穿行辅助 | [层级] | [若游戏有平台跳跃] | 未开始 | 评估自动抓取（宽大边缘检测）、郊狼时间延长、跳跃高度调整是否适合本游戏设计。若平台跳跃不是游戏系统，标记为不适用。 |
| HUD 元素重定位 | 全面 | 所有 HUD 元素 | 未开始 | 允许玩家将血条、小地图、任务追踪器移至其偏好的屏幕位置。对使用头部追踪或眼动硬件、可能外周视野覆盖减少的玩家尤为重要。 |

---

## Cognitive Accessibility
## 认知无障碍

> **Why cognitive accessibility is often under-specced**: Cognitive accessibility
> affects players with ADHD, dyslexia, autism spectrum conditions, acquired brain
> injuries, and anxiety disorders — a larger combined population than many studios
> realize. It also benefits all players in high-stress moments. The most common
> failures are: no pause anywhere, tutorial information that can only be seen once,
> and systems that require tracking too many simultaneous states. Games like
> Hades and Celeste have demonstrated that cognitive assist options (god mode,
> persistent reminders, extended text display) do not harm the experience for
> players who don't use them.
> **为何认知无障碍常被低估**：认知无障碍影响患 ADHD、阅读障碍、自闭症谱系、获得性脑损伤与焦虑症的玩家 — 总人数比许多工作室意识到的更多。它也惠及高压时刻的所有玩家。最常见的失败：无处暂停、教程信息只能看一次、需要同时跟踪过多状态的系统。Hades 与 Celeste 等游戏已证明认知辅助选项（神模式、持久提醒、扩展文字显示）不会损害不使用它们的玩家体验。

| Feature | Target Tier | Scope | Status | Implementation Notes |
|---------|-------------|-------|--------|---------------------|
| Difficulty options | Standard | All gameplay difficulty parameters | Not Started | Separate granular sliders where possible (damage dealt, damage received, enemy aggression, enemy speed) rather than a single Easy/Normal/Hard label. Document which parameters are adjustable and which are fixed. Fixed parameters require a design justification. |
| Pause anywhere | Basic | All gameplay states | Not Started | Players must be able to pause during any gameplay state, including cutscenes, dialogue, and tutorial sequences. Document any state where pausing is currently prevented and the design justification for that restriction. Any restriction is a risk. |
| Tutorial persistence | Standard | All tutorials and help text | Not Started | After dismissing a tutorial prompt, the player must be able to retrieve it from a Help section in the menu. Do not rely on players absorbing tutorials on first encounter — AbleGamers research shows many players dismiss prompts on reflex. |
| Quest / objective clarity | Standard | Quest and objective systems | Not Started | The current active objective must be accessible within 2 button presses at all times during gameplay. Display the full objective text on demand, not just a truncated marker. Avoid objectives that require inference ("investigate the northern region" — where exactly?). |
| Visual indicators for audio-only information | Standard | All SFX that carry gameplay information | Not Started | Audit every sound effect that communicates gameplay-critical state. For each: is there a visual equivalent? Directional audio (off-screen enemy) needs a screen-edge indicator. Critical warnings (boss phase transition, trap trigger) need visual cues. See Auditory Accessibility for full list. |
| Reading time for UI | Standard | All auto-dismissing dialogs | Not Started | No dialog, notification, or tooltip that contains actionable information may auto-dismiss in less than 5 seconds. Preferred: do not auto-dismiss at all — require player confirmation. Document every auto-dismissing element here and its current duration. |
| Cognitive load documentation | Comprehensive | Per game system | Not Started | For each system in systems-index.md, document the maximum number of things it asks the player to simultaneously track. Flag any system where the number exceeds 4. This is not a hard rule but a review trigger — high cognitive load systems need compensating UI clarity. See Per-Feature Accessibility Matrix below. |
| Navigation assists | Standard | World navigation | Not Started | Fast travel (to previously visited locations), waypoint system for current objective, optional objective indicator always visible. Document which of these apply to this game's design and which are intentionally omitted. |

| 功能 | 目标层级 | 范围 | 状态 | 实现备注 |
|---------|-------------|-------|--------|---------------------|
| 难度选项 | 标准 | 所有玩法难度参数 | 未开始 | 尽可能分离细粒度滑杆（造成伤害、承受伤害、敌人攻击性、敌人速度），而非单一易/普通/难标签。记录哪些参数可调、哪些固定。固定参数需要设计理由。 |
| 处处可暂停 | 基础 | 所有玩法状态 | 未开始 | 玩家必须能在任何玩法状态下暂停，包括过场、对话与教程序列。记录当前阻止暂停的任何状态及其设计理由。任何限制都是风险。 |
| 教程持久化 | 标准 | 所有教程与帮助文字 | 未开始 | 关闭教程提示后，玩家必须能从菜单帮助部分检索它。不要依赖玩家首次遇到时吸收教程 — AbleGamers 研究表明许多玩家反射性关闭提示。 |
| 任务/目标清晰度 | 标准 | 任务与目标系统 | 未开始 | 当前活动目标必须能在玩法中两次按键内访问。按需显示完整目标文字，而非仅截断标记。避免需要推断的目标（"调查北部区域" — 具体哪里？）。 |
| 纯音频信息的视觉指示器 | 标准 | 所有携带玩法信息的音效 | 未开始 | 审计每个传达玩法关键状态的音效。对每个：是否有视觉等价物？定向音频（屏外敌人）需要屏幕边缘指示器。关键警告（Boss 阶段转换、陷阱触发）需要视觉提示。完整清单见听觉无障碍。 |
| UI 阅读时间 | 标准 | 所有自动消失对话框 | 未开始 | 任何含可操作信息的对话框、通知或提示不得在少于 5 秒内自动消失。推荐：完全不自动消失 — 要求玩家确认。在此记录每个自动消失元素及其当前时长。 |
| 认知负荷文档 | 全面 | 每个游戏系统 | 未开始 | 对 systems-index.md 中每个系统，记录它要求玩家同时跟踪的最大事物数。标记任何超过 4 的系统。这不是硬规则而是评审触发 — 高认知负荷系统需要补偿性 UI 清晰度。见下方逐功能无障碍矩阵。 |
| 导航辅助 | 标准 | 世界导航 | 未开始 | 快速旅行（至已访问地点）、当前目标路径点系统、可选目标指示器始终可见。记录哪些适用于本游戏设计、哪些有意省略。 |

---

## Auditory Accessibility
## 听觉无障碍

> **Why auditory accessibility matters even for players without hearing loss**:
> 7% of players are deaf or hard of hearing. Additionally, a large portion of
> players regularly play in environments where audio is reduced or absent (commute,
> shared household, infant sleeping). Any gameplay-critical information delivered
> only through audio is a design failure even before accessibility is considered.
> The guiding principle: every sound that changes what the player should DO next
> must have a visual equivalent.
> **为何听觉无障碍对无听力损失的玩家也重要**：7% 玩家失聪或重听。此外，大量玩家常在音频降低或缺失的环境中游玩（通勤、共用家庭、婴儿睡眠）。任何仅通过音频传递的玩法关键信息即使在无障碍之前也是设计失败。指导原则：每个改变玩家下一步"应做什么"的声音必须有视觉等价物。

| Feature | Target Tier | Scope | Status | Implementation Notes |
|---------|-------------|-------|--------|---------------------|
| Subtitles for all spoken dialogue | Basic | All voiced content | Not Started | 100% coverage — no exceptions. Include narration, in-engine dialogue, radio/environmental dialogue heard from a distance. Test subtitle sync against voice acting timing. |
| Closed captions for gameplay-critical SFX | Comprehensive | Identified SFX list (below) | Not Started | Not all SFX need captions — only those that communicate state the player cannot infer visually. See the SFX audit table below. |
| Mono audio option | Comprehensive | Global audio output | Not Started | Folds stereo/spatial audio to mono. Preserves volume balance between channels rather than summing to full volume on both sides. Essential for players with single-sided deafness. |
| Independent volume controls | Basic | Music / SFX / Voice / UI audio buses | Not Started | Four independent sliders minimum. Persist to player profile. Range: 0–100%, default 80%. Expose in both main settings and the pause menu. |
| Visual representations for directional audio | Comprehensive | All off-screen threats and audio events | Not Started | Screen-edge indicator pointing toward the audio source. Opacity scales with audio volume (closer = more opaque). Two variants: threat indicators (red) and information indicators (neutral). Example: The Last of Us Part II uses screen-edge indicators for off-screen enemy positions. |
| Hearing aid compatibility mode | Standard | High-frequency audio cues | Not Started | Audit all audio cues for frequency range. Any cue that communicates critical information only through high-frequency sound (above 4kHz) must have a low-frequency or visual equivalent. Hearing aids often filter high frequencies. |

| 功能 | 目标层级 | 范围 | 状态 | 实现备注 |
|---------|-------------|-------|--------|---------------------|
| 所有口语对话字幕 | 基础 | 所有配音内容 | 未开始 | 100% 覆盖 — 无例外。包括旁白、引擎内对话、远处听到的广播/环境对话。针对配音时序测试字幕同步。 |
| 玩法关键音效闭合字幕 | 全面 | 已识别音效清单（下） | 未开始 | 并非所有音效都需字幕 — 仅那些传达玩家无法视觉推断的状态。见下方音效审计表。 |
| 单声道音频选项 | 全面 | 全局音频输出 | 未开始 | 将立体声/空间音频折叠为单声道。保留通道间音量平衡，而非在两侧全音量相加。对单侧失聪玩家至关重要。 |
| 独立音量控制 | 基础 | 音乐/音效/配音/UI 音频总线 | 未开始 | 至少 4 个独立滑杆。持久化至玩家档案。范围：0–100%，默认 80%。在主设置与暂停菜单中均暴露。 |
| 定向音频的视觉表示 | 全面 | 所有屏外威胁与音频事件 | 未开始 | 指向音频源的屏幕边缘指示器。不透明度随音量缩放（越近越不透明）。两种变体：威胁指示器（红）与信息指示器（中性）。示例：The Last of Us Part II 用屏幕边缘指示器表示屏外敌人位置。 |
| 助听器兼容模式 | 标准 | 高频音频提示 | 未开始 | 审计所有音频提示的频率范围。任何仅通过高频声音（4kHz 以上）传达关键信息的提示必须有低频或视觉等价物。助听器常过滤高频。 |

### Gameplay-Critical SFX Audit
### 玩法关键音效审计

> Identify every sound effect that communicates state the player needs to act on.
> Each entry in this table requires either a confirmed visual backup or a caption.
> 识别每个传达玩家需采取行动的状态的音效。本表每条目需要确认的视觉备份或字幕。

| Sound Effect | What It Communicates | Visual Backup | Caption Required | Status |
|-------------|---------------------|--------------|-----------------|--------|
| [Enemy attack windup sound] | [Incoming damage — player should dodge] | [Enemy animation telegraph visible from all camera angles] | [No — visual is sufficient] | [Not Started] |
| [Trap trigger click] | [Trap is about to fire] | [Not always visible depending on camera angle] | [Yes — "[CLICK]" caption with directional indicator] | [Not Started] |
| [Low health heartbeat] | [Player health critical] | [Health bar also shows critical state visually] | [No — visual is sufficient] | [Not Started] |
| [Quest completion chime] | [Objective completed] | [Quest tracker updates visually] | [No — visual is sufficient] | [Not Started] |
| [Add each SFX that changes what the player should do] | | | | |

| 音效 | 它传达什么 | 视觉备份 | 需字幕？ | 状态 |
|-------------|---------------------|--------------|-----------------|--------|
| [敌人攻击蓄力声] | [即将受到伤害 — 玩家应闪避] | [敌人动画预警在所有镜头角度可见] | [否 — 视觉足够] | [未开始] |
| [陷阱触发咔哒] | [陷阱即将发射] | [视镜头角度不一定可见] | [是 — "[咔哒]" 字幕带方向指示器] | [未开始] |
| [低生命心跳] | [玩家生命危急] | [血条也视觉显示危急状态] | [否 — 视觉足够] | [未开始] |
| [任务完成钟声] | [目标完成] | [任务追踪器视觉更新] | [否 — 视觉足够] | [未开始] |
| [添加每个改变玩家应做行动的音效] | | | | |

---

## Platform Accessibility API Integration
## 平台无障碍 API 集成

> **Why this section exists**: Each platform provides native accessibility APIs
> that, when used, allow OS-level features (system screen readers, display
> accommodations, motor accessibility services) to work with your game. Ignoring
> these APIs does not break the game, but it means players who rely on OS-level
> accessibility tools get no benefit from them inside your game. Xbox in particular
> requires XAG compliance for certification. Verify platform requirements before
> committing to a tier — platform requirements set a floor, not a ceiling.
> **为何本节存在**：每个平台提供原生无障碍 API，使用时允许系统级功能（系统屏幕阅读器、显示适配、运动无障碍服务）与你的游戏协作。忽略这些 API 不会破坏游戏，但意味着依赖系统级无障碍工具的玩家在游戏内无法受益。特别是 Xbox 认证要求 XAG 合规。承诺层级前验证平台要求 — 平台要求设定下限而非上限。

| Platform | API / Standard | Features Planned | Status | Notes |
|----------|---------------|-----------------|--------|-------|
| Xbox (GDK) | Xbox Game Core Accessibility / XAG | [Input remapping via Xbox Ease of Access, high contrast support, narrator integration for menus] | Not Started | XAG compliance is required for ID@Xbox Game Pass consideration. Review XAG checklist at https://docs.microsoft.com/gaming/accessibility/guidelines |
| PlayStation 5 | Sony Accessibility Guidelines / AccessibilityNode API | [Screen reader passthrough for menus, mono audio, high contrast] | Not Started | PS5 natively supports system-level audio description and mono audio if the game exposes AccessibilityNode data on UI elements. |
| Steam (PC) | Steam Accessibility Features / SDL | [Controller input remapping via Steam Input, subtitle support] | Not Started | Steam Input allows system-level remapping independent of in-game remapping. In-game remapping still required for keyboard/mouse. |
| iOS | UIAccessibility / VoiceOver | [VoiceOver support for menus if mobile port planned] | N/A | Only required if mobile release is in scope. |
| Android | AccessibilityService / TalkBack | [TalkBack support for menus if mobile port planned] | N/A | Only required if mobile release is in scope. |
| PC (Screen Reader) | JAWS / NVDA / Windows Narrator | [Menu navigation announcements] | Not Started | Requires UI elements to expose accessible names and roles via platform UI layer. Godot 4.5+ AccessKit integration covers this for supported control types. Verify against engine-reference/godot/ docs. |

| 平台 | API / 标准 | 计划功能 | 状态 | 备注 |
|----------|---------------|-----------------|--------|-------|
| Xbox (GDK) | Xbox Game Core Accessibility / XAG | [通过 Xbox 轻松访问的输入重映射、高对比度支持、菜单讲述者集成] | 未开始 | ID@Xbox Game Pass 考虑要求 XAG 合规。在 https://docs.microsoft.com/gaming/accessibility/guidelines 审阅 XAG 清单 |
| PlayStation 5 | 索尼无障碍指南 / AccessibilityNode API | [菜单屏幕阅读器透传、单声道音频、高对比度] | 未开始 | 若游戏在 UI 元素上暴露 AccessibilityNode 数据，PS5 原生支持系统级音频描述与单声道音频。 |
| Steam (PC) | Steam 无障碍功能 / SDL | [通过 Steam Input 的手柄输入重映射、字幕支持] | 未开始 | Steam Input 允许独立于游戏内重映射的系统级重映射。键鼠仍需游戏内重映射。 |
| iOS | UIAccessibility / VoiceOver | [若计划移动移植，菜单 VoiceOver 支持] | 不适用 | 仅在移动发行在范围内时需要。 |
| Android | AccessibilityService / TalkBack | [若计划移动移植，菜单 TalkBack 支持] | 不适用 | 仅在移动发行在范围内时需要。 |
| PC（屏幕阅读器） | JAWS / NVDA / Windows Narrator | [菜单导航播报] | 未开始 | 要求 UI 元素通过平台 UI 层暴露无障碍名与角色。Godot 4.5+ AccessKit 集成对支持的控制类型覆盖此项。对照 engine-reference/godot/ 文档验证。 |

---

## Per-Feature Accessibility Matrix
## 逐功能无障碍矩阵

> **Why this matrix exists**: Accessibility is not a list of settings — it is a
> property of every game system. This matrix creates the "accessibility impact"
> view of the game: which systems have which barriers, and whether those barriers
> are addressed. When a new system is added to systems-index.md, a row must be
> added here. If a system has an unaddressed accessibility concern, it cannot be
> marked Approved in the systems index.
> **为何本矩阵存在**：无障碍不是设置清单 — 它是每个游戏系统的属性。本矩阵创建游戏的 "无障碍影响" 视图：哪些系统有哪些障碍、这些障碍是否被解决。当 systems-index.md 中新增系统时，必须在此添加一行。若某系统有未解决的无障碍关切，则它在系统索引中不得标记为已批准。

| System | Visual Concerns | Motor Concerns | Cognitive Concerns | Auditory Concerns | Addressed | Notes |
|--------|----------------|---------------|-------------------|------------------|-----------|-------|
| [Combat System] | [Enemy health bars are color-coded; attack animations may cause motion sickness] | [Rapid input required for combos; hold inputs for guard] | [Track enemy patterns + cooldowns + player resources simultaneously] | [Audio cues for off-screen attacks; critical damage warning sounds] | [Partial] | [Colorblind palette applied; hold-to-block toggle needed] |
| [Inventory / Equipment] | [Item rarity conveyed by border color] | [No motor concerns — turn-based] | [Item stats comparison requires reading multiple values] | [None — no critical audio in this system] | [Partial] | [Non-color rarity indicators in progress] |
| [Dialogue System] | [Subtitle display depends on contrast settings] | [No motor concerns] | [Long dialogue trees with time pressure on dialogue choices] | [All dialogue must be subtitled] | [Not Started] | [Timed dialogue choices must support extended timer option] |
| [Navigation / World Map] | [Map marker colors] | [No motor concerns] | [Quest objective clarity; waypoint visibility] | [Audio pings for objectives have no visual equivalent] | [Not Started] | |
| [Add system from systems-index.md] | | | | | | |

| 系统 | 视觉关切 | 运动关切 | 认知关切 | 听觉关切 | 已解决？ | 备注 |
|--------|----------------|---------------|-------------------|------------------|-----------|-------|
| [战斗系统] | [敌人血条颜色编码；攻击动画可能引起动晕] | [连招需要快速输入；防御需要按住输入] | [同时跟踪敌人模式 + 冷却 + 玩家资源] | [屏外攻击音频提示；关键伤害警告声] | [部分] | [已应用色盲调色板；需按住转格挡切换] |
| [库存/装备] | [物品稀有度由边框颜色传达] | [无运动关切 — 回合制] | [物品属性比较需阅读多值] | [无 — 此系统无关键音频] | [部分] | [非颜色稀有度指示器进行中] |
| [对话系统] | [字幕显示依赖对比度设置] | [无运动关切] | [长对话树且对话选择有时间压力] | [所有对话必须有字幕] | [未开始] | [定时对话选择必须支持延长计时器选项] |
| [导航/世界地图] | [地图标记颜色] | [无运动关切] | [任务目标清晰度；路径点可见性] | [目标音频提示无视觉等价物] | [未开始] | |
| [添加 systems-index.md 中的系统] | | | | | | |

---

## Accessibility Test Plan
## 无障碍测试计划

> **Why testing accessibility separately from QA**: Standard QA tests whether
> features work. Accessibility testing tests whether features work for players
> who use them. These are different tests. A subtitle system can pass QA (it
> displays text) and fail accessibility testing (the text is unreadable at TV
> distance by a player with low vision). Plan for three test types: automated
> (contrast ratios, text sizes), manual internal (team members simulating
> impairments using accessibility simulators), and user testing (players who
> actually use these features).
> **为何无障碍测试独立于 QA**：标准 QA 测试功能是否工作。无障碍测试测试功能对使用它们的玩家是否工作。这是不同的测试。字幕系统可能通过 QA（它显示文字）但未通过无障碍测试（文字在电视距离下对低视力玩家不可读）。计划三种测试类型：自动（对比比、文字大小）、内部人工（团队成员用无障碍模拟器模拟障碍）、用户测试（实际使用这些功能的玩家）。

| Feature | Test Method | Test Cases | Pass Criteria | Responsible | Status |
|---------|------------|------------|--------------|-------------|--------|
| Text contrast ratios | Automated — contrast analyzer tool on all UI screenshots | All text/background combinations at all game states | All body text ≥ 4.5:1; all large text ≥ 3:1; subtitle backgrounds ≥ 7:1 | ux-designer | Not Started |
| Colorblind modes | Manual — Coblis simulator on all game screenshots with modes enabled | Gameplay screenshots in exploration, combat, inventory in each mode | No essential information is lost in any mode; player can complete all objectives without color discrimination | ux-designer | Not Started |
| Input remapping | Manual — remap all inputs to non-default bindings, complete tutorial and first level | All default inputs rebound; gameplay functions correctly; no binding conflict possible | All actions accessible after remapping; conflict prevention works; bindings persist across restart | qa-tester | Not Started |
| Subtitle accuracy | Manual — verify against voice script, check all lines | All voiced content; subtitle timing; speaker identification | 100% of voiced lines subtitled; speaker identified for all multi-character scenes; no subtitle display for more than 3 seconds after line ends | qa-tester | Not Started |
| Hold input toggles | Manual — enable all toggle alternatives, complete all combat and traversal sequences | All hold inputs in toggle mode | All hold actions completable in toggle mode; no gameplay state requires sustained hold when toggle is enabled | qa-tester | Not Started |
| Reduced motion mode | Manual — enable mode, navigate all menus and complete first hour of gameplay | All menu transitions; all HUD animations; all camera shake events | No looping animations in menus; no camera shake above threshold; all screen transitions are cross-fade or cut | ux-designer | Not Started |
| Platform screen reader (menu) | Manual — enable OS screen reader, navigate all menus | Main menu, settings, pause menu, inventory, map | All interactive menu elements have screen reader announcements; navigation order is logical; no element unreachable by keyboard/D-pad | ux-designer | Not Started |
| User testing — colorblind | User testing with colorblind participants | Full game session with each colorblind mode | Participants complete all content without requesting color clarification; no session-stopping confusion | producer | Not Started |
| User testing — motor impairment | User testing with participants using one hand or adaptive controllers | Full game session with toggle and extended timing modes enabled | Participants complete all MVP content within tolerance of able-bodied completion time | producer | Not Started |

| 功能 | 测试方法 | 测试用例 | 通过标准 | 负责人 | 状态 |
|---------|------------|------------|--------------|-------------|--------|
| 文字对比比 | 自动 — 对所有 UI 截图用对比分析工具 | 所有游戏状态下所有文字/背景组合 | 所有正文字 ≥ 4.5:1；所有大字 ≥ 3:1；字幕背景 ≥ 7:1 | UX 设计师 | 未开始 |
| 色盲模式 | 人工 — 对所有游戏截图启用模式后用 Coblis 模拟器 | 每种模式下探索、战斗、库存中的玩法截图 | 任何模式下无重要信息丢失；玩家无需颜色辨别即可完成所有目标 | UX 设计师 | 未开始 |
| 输入重映射 | 人工 — 将所有输入重映射为非默认绑定，完成教程与第一关 | 所有默认输入已重绑；玩法正常；不可能绑定冲突 | 重映射后所有动作可访问；冲突预防工作；重启后绑定持久 | QA 测试员 | 未开始 |
| 字幕准确性 | 人工 — 对照配音脚本验证、检查所有行 | 所有配音内容；字幕时序；说话人识别 | 100% 配音行有字幕；多角色场景识别说话人；行结束后字幕显示不超过 3 秒 | QA 测试员 | 未开始 |
| 按住输入切换 | 人工 — 启用所有切换替代，完成所有战斗与穿行序列 | 切换模式下所有按住输入 | 切换模式下所有按住动作可完成；启用切换时无玩法状态要求持续按住 | QA 测试员 | 未开始 |
| 减少动效模式 | 人工 — 启用模式，导航所有菜单并完成第一小时玩法 | 所有菜单转换；所有 HUD 动画；所有镜头震动事件 | 菜单无循环动画；无超过阈值的镜头震动；所有屏幕转换为交叉淡入或剪辑 | UX 设计师 | 未开始 |
| 平台屏幕阅读器（菜单） | 人工 — 启用系统屏幕阅读器，导航所有菜单 | 主菜单、设置、暂停菜单、库存、地图 | 所有可交互菜单元素有屏幕阅读器播报；导航顺序逻辑；无元素键盘/方向键不可达 | UX 设计师 | 未开始 |
| 用户测试 — 色盲 | 与色盲参与者用户测试 | 每种色盲模式下的完整游戏会话 | 参与者无需颜色澄清即可完成所有内容；无停止会话的困惑 | 制作人 | 未开始 |
| 用户测试 — 运动障碍 | 与单手或自适应控制器参与者用户测试 | 启用切换与延长时间模式的完整游戏会话 | 参与者在体能健全完成时间的容差内完成所有 MVP 内容 | 制作人 | 未开始 |

---

## Known Intentional Limitations
## 已知有意局限

> **Why document what is NOT included**: Omissions left undocumented become
> surprises at certification or in community feedback. Documenting a limitation
> with a rationale demonstrates that it was a deliberate choice, not an oversight.
> It also identifies which players are not served and what the mitigation is.
> Every entry here is a risk — assess it honestly.
> **为何记录"未包含"什么**：未记录的省略在认证或社群反馈时成为意外。带理由记录局限表明这是刻意选择而非疏忽。它也识别哪些玩家未被服务以及缓解措施是什么。此处每条都是风险 — 诚实评估。

| Feature | Tier Required | Why Not Included | Risk / Impact | Mitigation |
|---------|--------------|-----------------|--------------|------------|
| [Screen reader support for in-game world (NPCs, objects, environmental text)] | Exemplary | Engine (Godot 4.6) AccessKit integration covers menus only; extending to the game world requires a custom spatial audio description system beyond current scope | Affects blind and low-vision players who can navigate menus but cannot independently explore the game world | Ensure all critical world information is duplicated in accessible menu systems (quest log, map); evaluate for post-launch DLC |
| [Full subtitle customization (font/color/background)] | Comprehensive | Scope reduction — targeting Standard tier. Custom font rendering in Godot requires additional asset pipeline work | Affects deaf and hard-of-hearing players with specific legibility needs; particularly affects players with dyslexia who use custom fonts | Provide two preset subtitle styles (default and high-readability) as a partial mitigation; log for post-launch update |
| [Tactile/haptic alternatives for all audio cues] | Exemplary | Platform rumble API integration for non-Xbox platforms is out of scope for v1.0 | Affects deaf players relying on haptic feedback; PC players with non-Xbox controllers get no haptic response | Xbox controller haptic integration is in scope; evaluate PlayStation DualSense haptic API for a post-launch patch |
| [Add any other intentionally excluded accessibility feature] | | | | |

| 功能 | 所需层级 | 为何未包含 | 风险/影响 | 缓解 |
|---------|--------------|-----------------|--------------|------------|
| [游戏世界（NPC、物体、环境文字）的屏幕阅读器] | 典范 | 引擎（Godot 4.6）AccessKit 集成仅覆盖菜单；扩展至游戏世界需要超出当前范围的自定义空间音频描述系统 | 影响可导航菜单但无法独立探索游戏世界的盲与低视力玩家 | 确保所有关键世界信息在无障碍菜单系统（任务日志、地图）中复制；为上线后 DLC 评估 |
| [完整字幕自定义（字体/颜色/背景）] | 全面 | 范围削减 — 目标标准层级。Godot 中自定义字体渲染需要额外资产管线工作 | 影响有特定可读性需求的聋与重听玩家；尤其影响使用自定义字体的阅读障碍玩家 | 提供两种预设字幕样式（默认与高可读性）作为部分缓解；记录为上线后更新 |
| [所有音频提示的触觉/震动替代] | 典范 | 非 Xbox 平台的震动 API 集成超出 v1.0 范围 | 影响依赖触觉反馈的聋玩家；使用非 Xbox 控制器的 PC 玩家无触觉响应 | Xbox 控制器震动集成在范围内；为上线后补丁评估 PlayStation DualSense 震动 API |
| [添加任何其他有意排除的无障碍功能] | | | | |

---

## Audit History
## 审计历史

> **Why track audit history**: Accessibility is not certified once and done.
> Platform requirements change. New features may introduce new barriers. Legal
> standards evolve. An audit history demonstrates due diligence and helps identify
> regressions between audits.
> **为何跟踪审计历史**：无障碍非一次认证即完成。平台要求变化。新功能可能引入新障碍。法律标准演进。审计历史展示尽职调查并帮助识别审计间的回归。

| Date | Auditor | Type | Scope | Findings Summary | Status |
|------|---------|------|-------|-----------------|--------|
| [Date] | [Internal — ux-designer] | Internal review | [Pre-submission checklist against committed tier] | [e.g., "12 items verified, 3 open issues: subtitle contrast below target in 2 scenes, color-only indicator on minimap not resolved"] | [In Progress] |
| [Date] | [External — AbleGamers Player Panel] | User testing | [Motor accessibility — one-hand mode and timing adjustments] | [e.g., "Toggle modes functional. Timed QTE window at 3x still failed for one participant — recommend 5x option."] | [Findings addressed] |
| [Add row for each audit] | | | | | |

| 日期 | 审计人 | 类型 | 范围 | 发现摘要 | 状态 |
|------|---------|------|-------|-----------------|--------|
| [日期] | [内部 — UX 设计师] | 内部评审 | [对照承诺层级的提交前清单] | [例如 "已验证 12 项，3 项未决：2 个场景字幕对比度低于目标，小地图颜色唯一指示器未解决"] | [进行中] |
| [日期] | [外部 — AbleGamers 玩家小组] | 用户测试 | [运动无障碍 — 单手模式与时序调整] | [例如 "切换模式功能正常。3x 定时 QTE 窗口对一名参与者仍失败 — 推荐 5x 选项。"] | [发现已处理] |
| [为每次审计添加行] | | | | | |

---

## External Resources
## 外部资源

| Resource | URL | Relevance |
|----------|-----|-----------|
| WCAG 2.1 (Web Content Accessibility Guidelines) | https://www.w3.org/TR/WCAG21/ | Foundational accessibility standard — contrast ratios, text sizing, input requirements |
| Game Accessibility Guidelines | https://gameaccessibilityguidelines.com | Comprehensive game-specific checklist organized by category and cost |
| AbleGamers Player Panel | https://ablegamers.org/player-panel/ | User testing service and consulting with disabled gamers |
| Xbox Accessibility Guidelines (XAG) | https://docs.microsoft.com/gaming/accessibility/guidelines | Required reading for Xbox certification; well-structured feature checklist |
| PlayStation Accessibility Guidelines | https://www.playstation.com/en-us/accessibility/ | Sony platform requirements; also contains well-written design guidance |
| Colour Blindness Simulator (Coblis) | https://www.color-blindness.com/coblis-color-blindness-simulator/ | Free tool for simulating colorblind modes on screenshots |
| Accessible Games Database | https://accessible.games | Research and examples of accessible game design decisions |
| CVAA (21st Century Communications and Video Accessibility Act) | https://www.fcc.gov/consumers/guides/21st-century-communications-and-video-accessibility-act-cvaa | US legal requirement for games with communication features (voice chat, messaging) |

| 资源 | URL | 相关性 |
|----------|-----|-----------|
| WCAG 2.1（网页内容无障碍指南） | https://www.w3.org/TR/WCAG21/ | 基础无障碍标准 — 对比比、文字大小、输入要求 |
| Game Accessibility Guidelines | https://gameaccessibilityguidelines.com | 按类别与成本组织的全面游戏专属清单 |
| AbleGamers 玩家小组 | https://ablegamers.org/player-panel/ | 与残疾玩家用户测试服务与咨询 |
| Xbox 无障碍指南 (XAG) | https://docs.microsoft.com/gaming/accessibility/guidelines | Xbox 认证必读；结构良好的功能清单 |
| PlayStation 无障碍指南 | https://www.playstation.com/en-us/accessibility/ | 索尼平台要求；也含良好编写的设计指南 |
| 色盲模拟器 (Coblis) | https://www.color-blindness.com/coblis-color-blindness-simulator/ | 在截图上模拟色盲模式的免费工具 |
| Accessible Games Database | https://accessible.games | 无障碍游戏设计决策的研究与示例 |
| CVAA（21 世纪通信与视频无障碍法案） | https://www.fcc.gov/consumers/guides/21st-century-communications-and-video-accessibility-act-cvaa | 对含通信功能（语音聊天、消息）游戏的美国法律要求 |

---

## Open Questions
## 待解决问题

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| [Does Godot 4.6 AccessKit support dynamic accessibility node updates for HUD elements, or only static menus?] | [ux-designer] | [Before Technical Setup gate] | [Unresolved — check engine-reference/godot/ docs] |
| [What is the Xbox ID@Xbox minimum XAG compliance requirement for our release window?] | [producer] | [Before Pre-Production gate] | [Unresolved] |
| [Will the dialogue system support timed choice extensions without a full architecture change?] | [lead-programmer] | [During Technical Design] | [Unresolved] |
| [Add question] | [Owner] | [Deadline] | [Resolution] |

| 问题 | 负责人 | 截止日期 | 决议 |
|----------|-------|----------|-----------|
| [Godot 4.6 AccessKit 是否支持 HUD 元素的动态无障碍节点更新，还是仅静态菜单？] | [UX 设计师] | [技术设置门前] | [未解决 — 检查 engine-reference/godot/ 文档] |
| [我们发行窗口的 Xbox ID@Xbox 最低 XAG 合规要求是什么？] | [制作人] | [Pre-Production 门前] | [未解决] |
| [对话系统是否支持定时选择延长而无需全面架构变更？] | [首席程序员] | [技术设计期间] | [未解决] |
| [添加问题] | [负责人] | [截止日期] | [决议] |
