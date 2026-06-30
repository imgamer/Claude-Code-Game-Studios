# UX Specification: [Screen / Flow Name]
# UX 规格：[屏幕 / 流程名称]

> **Status**: Draft | In Review | Approved | Implemented
> **Author**: [Name or agent — e.g., ui-designer]
> **Last Updated**: [Date]
> **Screen / Flow Name**: [Short identifier used in code and tickets — e.g., `InventoryScreen`, `NewGameFlow`]
> **Platform Target**: [PC | Console | Mobile | All — list all that this spec covers]
> **Related GDDs**: [Links to the GDD sections that generated this UI requirement — e.g., `design/gdd/inventory.md § UI Requirements`]
> **Related ADRs**: [Any architectural decisions that constrain this screen — e.g., `ADR-0012: UI Framework Selection`]
> **Related UX Specs**: [Sibling and parent screens — e.g., `ux-spec-pause-menu.md`, `ux-spec-settings.md`]
> **Accessibility Tier**: Basic | Standard | Comprehensive | Exemplary
> **状态**：草稿 | 审核中 | 已批准 | 已实现
> **作者**：[姓名或智能体 — 例如，ui-designer]
> **最后更新**：[Date]
> **屏幕 / 流程名称**：[代码与工单中使用的短标识符 — 例如，`InventoryScreen`、`NewGameFlow`]
> **平台目标**：[PC | 主机 | 移动 | 全部 — 列出此规格覆盖的所有]
> **相关 GDD**：[生成此 UI 要求的 GDD 章节链接 — 例如，`design/gdd/inventory.md § UI Requirements`]
> **相关 ADR**：[约束此屏幕的任何架构决策 — 例如，`ADR-0012: UI Framework Selection`]
> **相关 UX 规格**：[兄弟与父屏幕 — 例如，`ux-spec-pause-menu.md`、`ux-spec-settings.md`]
> **无障碍层级**：基础 | 标准 | 全面 | 示范

> **Note — Scope boundary**: This template covers discrete screens and flows (menus,
> dialogs, inventory, settings, cutscene UI, etc.). For persistent in-game overlays
> that exist during active gameplay, use `hud-design.md` instead. If a screen is a
> hybrid (e.g., a pause menu that overlays the game world), treat it as a screen spec
> and note the overlay relationship in Navigation Position.
> **注 — 范围边界**：此模板覆盖离散屏幕与流程（菜单、对话框、库存、设置、过场 UI 等）。
> 对于活跃游戏过程中存在的持久游戏内覆盖，请改用 `hud-design.md`。如屏幕为混合
> （例如，覆盖游戏世界的暂停菜单），将其视为屏幕规格并在导航位置中记录覆盖关系。

---

## 1. Purpose & Player Need
## 1. 目的与玩家需求

> **Why this section exists**: Every screen must justify its existence from the
> player's perspective. Screens that are designed from a developer perspective ("display
> the save data") produce cluttered, confusing interfaces. Screens designed from the
> player's perspective ("let the player feel confident their progress is safe before they
> put the controller down") produce purposeful, calm interfaces. Write this section before
> touching any layout decisions — it is the filter through which every subsequent choice
> is evaluated.
> **为何存在此节**：每个屏幕必须从玩家角度证明其存在。从开发者角度设计的屏幕（"显示存档数据"）
> 产生杂乱、令人困惑的界面。从玩家角度设计的屏幕（"让玩家在放下控制器前确信其进度安全"）
> 产生有目的、平静的界面。在触及任何布局决策之前写此节 — 它是评估每个后续选择的过滤器。

**What player need does this screen serve?**
**此屏幕服务什么玩家需求？**

[One paragraph. Name the real human need, not the system function. Consider: what would
a player say they want when they open this screen? What would frustrate them if it did
not work? That frustration describes the need.
[一段话。命名真实人类需求，而非系统功能。考虑：玩家打开此屏幕时会说什么他们想要？
如果它不工作会让他们沮丧什么？那沮丧描述了需求。]

Example — bad: "Displays the player's current items and equipment."
Example — good: "Lets the player understand what they're carrying and quickly decide what
to take into the next encounter, without breaking their mental model of the game world.
The inventory is the player's planning tool between moments of action."]
示例 — 坏："显示玩家当前物品与装备。"
示例 — 好："让玩家理解他们携带什么并快速决定带入下次遭遇什么，
而不破坏他们对游戏世界的心智模型。库存是玩家在动作时刻之间的规划工具。"]

**The player goal** (what the player wants to accomplish):
**玩家目标**（玩家想完成什么）：

[One sentence. Specific enough that you could write an acceptance criterion for it.
Example: "Find the item they are looking for within three button presses and equip it
without navigating to a separate screen."]
[一句。足够具体以便你能为其写验收标准。
示例："在三次按键内找到他们寻找的物品并装备它，而无需导航到单独屏幕。"]

**The game goal** (what the game needs to communicate or capture):
**游戏目标**（游戏需要传达或捕获什么）：

[One sentence. This is what the system needs from this interaction. Example: "Record the
player's equipment choices and relay them to the combat system before the next encounter
loads." This section prevents UI that looks good but fails to serve the system it is
part of.]
[一句。这是系统从此交互需要什么。示例："记录玩家的装备选择并在下次遭遇加载前
转达给战斗系统。"此节防止看起来好但未能服务其所属系统的 UI。]

---

## 2. Player Context on Arrival
## 2. 玩家到达时的上下文

> **Why this section exists**: Screens do not exist in isolation. A player opening the
> inventory mid-combat is in a completely different cognitive and emotional state than
> a player opening it after clearing a dungeon. The same information architecture can
> feel oppressively complex in one context and trivially simple in another. Document the
> context so that design decisions — what to show first, what to hide, what to animate,
> what to simplify — are calibrated to the actual player arriving at this screen, not
> an abstract user.
> **为何存在此节**：屏幕非孤立存在。战斗中打开库存的玩家与清完地下城后打开它的玩家
> 处于完全不同的认知与情感状态。相同信息架构在一种上下文中可感觉压迫性复杂，
> 在另一种中可感觉微不足道简单。记录上下文以便设计决策 — 先显示什么、隐藏什么、
> 动画什么、简化什么 — 校准到到达此屏幕的真实玩家，而非抽象用户。

| Question | Answer |
|----------|--------|
| What was the player just doing? | [e.g., Completed a combat encounter / Pressed Esc from exploration / Triggered a story cutscene] |
| What is their emotional state? | [e.g., High tension — just narrowly survived / Calm — exploring between objectives] |
| What cognitive load are they carrying? | [e.g., High — actively tracking enemy positions / Low — no active threats] |
| What information do they already have? | [e.g., They know they just picked up an item but haven't seen its stats yet] |
| What are they most likely trying to do? | [e.g., Check if the new item is better than their current weapon — primary use case] |
| What are they likely afraid of? | [e.g., Missing something, making an irreversible mistake, losing track of where they were] |
| 问题 | 答案 |
|----------|--------|
| 玩家刚才在做什么？ | [例如，完成战斗遭遇 / 探索时按 Esc / 触发故事过场] |
| 他们的情感状态是什么？ | [例如，高紧张 — 险些丧生 / 平静 — 在目标间探索] |
| 他们承受什么认知负载？ | [例如，高 — 主动追踪敌人位置 / 低 — 无活跃威胁] |
| 他们已有什么信息？ | [例如，他们知道刚拾起物品但还没看其属性] |
| 他们最可能在尝试做什么？ | [例如，检查新物品是否比当前武器更好 — 主要用例] |
| 他们可能害怕什么？ | [例如，错过什么、做出不可逆错误、忘记自己在何处] |

**Emotional design target for this screen**:
**此屏幕的情感设计目标**：

[One sentence describing the feeling the player should have while using this screen.
Example: "Confident and in control — the player should feel like they have complete
information and complete authority over their choices, with no ambiguity about outcomes."]
[一句描述玩家使用此屏幕时应有的感觉。
示例："自信且掌控 — 玩家应感觉有完整信息与对其选择的完整权威，对结果无歧义。"]

---

## 3. Navigation Position
## 3. 导航位置

> **Why this section exists**: A screen that does not know where it sits in the
> navigation hierarchy cannot define its entry/exit transitions, its back-button
> behavior, or its relationship to the game's pause state. Navigation position also
> reveals architectural problems early — if this screen is reachable from eight
> different places, that is a complexity flag that should be resolved in design, not
> implementation.
> **为何存在此节**：不知道自己在导航层级中位置的屏幕无法定义其进入/退出过渡、
> 后退按钮行为或其与游戏暂停状态的关系。导航位置还及早揭示架构问题 —
> 如此屏幕可从八处不同地方到达，那是应在设计中解决的复杂度标志，而非实现。

**Screen hierarchy** (use indentation to show parent-child relationships):
**屏幕层级**（用缩进显示父子关系）：

```
[Root — e.g., Main Menu]
  └── [Parent Screen — e.g., Settings]
        └── [This Screen — e.g., Audio Settings]
              ├── [Child Screen — e.g., Advanced Audio Options]
              └── [Child Screen — e.g., Speaker Test Dialog]
```

```
[Root — e.g., Main Menu]
  └── [Parent Screen — e.g., Settings]
        └── [This Screen — e.g., Audio Settings]
              ├── [Child Screen — e.g., Advanced Audio Options]
              └── [Child Screen — e.g., Speaker Test Dialog]
```

```
[根 — 例如，主菜单]
  └── [父屏幕 — 例如，设置]
        └── [此屏幕 — 例如，音频设置]
              ├── [子屏幕 — 例如，高级音频选项]
              └── [子屏幕 — 例如，扬声器测试对话框]
```

**Modal behavior**: [Modal (blocks everything behind it, requires explicit dismiss) | Non-modal (game continues behind it) | Overlay (renders over game world, game paused) | Overlay-live (renders over game world, game continues)]
**模态行为**：[模态（阻挡其后一切，需显式消除） | 非模态（游戏在其后继续） | 覆盖（渲染在游戏世界之上，游戏暂停） | 实时覆盖（渲染在游戏世界之上，游戏继续）]

> If this screen is modal: document the dismiss behavior. Can it be dismissed by pressing
> Back/B? By pressing Escape? By clicking outside it? Can it be dismissed at all, or
> must the player complete it? Undismissable modals are high-friction — justify them.
> 如此屏幕为模态：记录消除行为。可通过按 Back/B 消除吗？通过按 Escape？通过在外部点击？
> 可消除吗，或玩家必须完成它？不可消除的模态是高摩擦 — 证明其合理。

**Reachability — all entry points**:
**可达性 — 所有入口**：

| Entry Point | Triggered By | Notes |
|-------------|-------------|-------|
| [e.g., Main Menu → Play] | [Player selects "New Game"] | [Primary entry point] |
| [e.g., Pause Menu → Resume] | [Player presses Start from any gameplay state] | [Secondary entry] |
| [e.g., Game event] | [Tutorial system forces open first time only] | [Systemic entry — must not break if player dismisses] |
| 入口 | 触发者 | 备注 |
|-------------|-------------|-------|
| [例如，主菜单 → 玩] | [玩家选"新游戏"] | [主要入口] |
| [例如，暂停菜单 → 继续] | [玩家从任何游戏状态按 Start] | [次要入口] |
| [例如，游戏事件] | [教程系统仅首次强制打开] | [系统入口 — 玩家消除时不得中断] |

---

## 4. Entry & Exit Points
## 4. 进入与退出点

> **Why this section exists**: Entry and exit define the screen's contract with the
> rest of the navigation system. Every entry point must have a corresponding exit point.
> Transitions that are undefined become bugs — the player finds themselves stuck, or the
> game state becomes inconsistent. Fill this table completely before implementation
> begins. Empty cells are a sign that design work is unfinished.
> **为何存在此节**：进入与退出定义屏幕与其余导航系统的契约。每个入口必须有对应出口。
> 未定义的过渡变成错误 — 玩家发现自己卡住，或游戏状态变得不一致。
> 实现开始前完整填写此表。空单元格是设计工作未完成的标志。

**Entry table**:
**入口表**：

| Trigger | Source Screen / State | Transition Type | Data Passed In | Notes |
|---------|----------------------|-----------------|----------------|-------|
| [e.g., Player presses Inventory button] | [Gameplay / Exploration state] | [Overlay push — game pauses] | [Current player loadout, inventory contents] | [Works from any non-combat state] |
| [e.g., Item pickup prompt accepted] | [Gameplay / Item Pickup dialog] | [Replace dialog with full inventory] | [Newly acquired item pre-highlighted] | [The new item should be visually distinguished on open] |
| [e.g., Quest system directs player to inventory] | [Gameplay / Quest Update notification] | [Overlay push] | [Quest-relevant item ID for highlight] | [Screen should deep-link to the relevant item] |
| 触发 | 源屏幕 / 状态 | 过渡类型 | 传入数据 | 备注 |
|---------|----------------------|-----------------|----------------|-------|
| [例如，玩家按库存按钮] | [游戏 / 探索状态] | [覆盖推入 — 游戏暂停] | [当前玩家装备、库存内容] | [从任何非战斗状态可用] |
| [例如，物品拾取提示接受] | [游戏 / 物品拾取对话框] | [用完整库存替换对话框] | [新获得的物品预先高亮] | [打开时新物品应视觉上区分] |
| [例如，任务系统引导玩家到库存] | [游戏 / 任务更新通知] | [覆盖推入] | [任务相关物品 ID 用于高亮] | [屏幕应深链接到相关物品] |

**Exit table**:
**出口表**：

| Exit Action | Destination | Transition Type | Data Returned / Saved | Notes |
|-------------|------------|-----------------|----------------------|-------|
| [e.g., Player closes inventory (Back/B/Esc)] | [Previous state — Exploration] | [Overlay pop — game resumes] | [Updated equipment loadout committed] | [Changes must be committed before transition begins] |
| [e.g., Player selects "Equip" on item] | [Same screen, updated state] | [In-place state change] | [Loadout change event fired] | [No navigation, just a state refresh] |
| [e.g., Player navigates to Map from inventory shortcut] | [Map Screen] | [Replace] | [No data] | [Inventory state is preserved if player returns] |
| 退出动作 | 目的地 | 过渡类型 | 返回 / 保存数据 | 备注 |
|-------------|------------|-----------------|----------------------|-------|
| [例如，玩家关闭库存（Back/B/Esc）] | [前一状态 — 探索] | [覆盖弹出 — 游戏恢复] | [已提交更新装备] | [过渡开始前必须提交更改] |
| [例如，玩家选"装备"物品] | [同屏幕，更新状态] | [原地状态改变] | [触发装备变更事件] | [无导航，仅状态刷新] |
| [例如，玩家从库存快捷方式导航到地图] | [地图屏幕] | [替换] | [无数据] | [如玩家返回则保留库存状态] |

---

## 5. Layout Specification
## 5. 布局规格

> **Why this section exists**: The layout specification is the handoff artifact between
> UX design and UI programming. It does not need to be pixel-perfect — it needs to
> communicate hierarchy (what is important), proximity (what belongs together), and
> proportion (what is big vs. small). ASCII wireframes achieve this without requiring
> design software. A programmer who reads this section should be able to build the
> correct structure without guessing. An artist who reads it should know where
> visual weight should be concentrated.
>
> Draw the layout at one standard resolution (e.g., 1920x1080). Note adaptations
> for other resolutions separately.
> **为何存在此节**：布局规格是 UX 设计与 UI 编程之间的交接工件。它不需要像素完美 —
> 它需要传达层级（什么重要）、邻近（什么属于一起）和比例（什么大什么小）。
> ASCII 线框无需设计软件即可实现此。读此节的程序员应能在不猜测的情况下构建正确结构。
> 读它的美术应知道视觉重量应集中何处。
>
> 在一个标准分辨率（例如，1920x1080）绘制布局。单独注意其他分辨率的适配。

### 5.1 Wireframe
### 5.1 线框

```
[Draw the screen layout using ASCII art. Suggested characters:
 ┌ ┐ └ ┘ │ ─    for borders
 ╔ ╗ ╚ ╝ ║ ═    for emphasized/modal borders
 [ ]              for interactive elements (buttons, inputs)
 { }              for content areas (lists, grids, images)
 ...              for scrollable content
 ●                for the focused element on open

Example:
┌──────────────────────────────────────────────┐
│  [← Back]        INVENTORY         [Options] │  ← HEADER ZONE
├──────────────────────────────────────────────┤
│ ┌──────────────┐  ┌─────────────────────────┐│
│ │ CATEGORY NAV │  │  ITEM DETAIL PANEL      ││  ← CONTENT ZONE
│ │  ● Weapons   │  │  Item Name              ││
│ │    Armor     │  │  {item icon}            ││
│ │    Consumable│  │  Stats comparison       ││
│ │    Key Items │  │  Description text...    ││
│ ├──────────────┤  └─────────────────────────┘│
│ │ ITEM GRID    │                             │
│ │ {□}{□}{□}{□} │                             │
│ │ {□}{□}{□}{□} │                             │
│ │ ...          │                             │
│ └──────────────┘                             │
├──────────────────────────────────────────────┤
│   [Equip]     [Drop]     [Compare]  [Close]  │  ← ACTION BAR
└──────────────────────────────────────────────┘
]
```

```
[Draw the screen layout using ASCII art. Suggested characters:
 ┌ ┐ └ ┘ │ ─    for borders
 ╔ ╗ ╚ ╝ ║ ═    for emphasized/modal borders
 [ ]              for interactive elements (buttons, inputs)
 { }              for content areas (lists, grids, images)
 ...              for scrollable content
 ●                for the focused element on open

Example:
┌──────────────────────────────────────────────┐
│  [← Back]        INVENTORY         [Options] │  ← HEADER ZONE
├──────────────────────────────────────────────┤
│ ┌──────────────┐  ┌─────────────────────────┐│
│ │ CATEGORY NAV │  │  ITEM DETAIL PANEL      ││  ← CONTENT ZONE
│ │  ● Weapons   │  │  Item Name              ││
│ │    Armor     │  │  {item icon}            ││
│ │    Consumable│  │  Stats comparison       ││
│ │    Key Items │  │  Description text...    ││
│ ├──────────────┤  └─────────────────────────┘│
│ │ ITEM GRID    │                             │
│ │ {□}{□}{□}{□} │                             │
│ │ {□}{□}{□}{□} │                             │
│ │ ...          │                             │
│ └──────────────┘                             │
├──────────────────────────────────────────────┤
│   [Equip]     [Drop]     [Compare]  [Close]  │  ← ACTION BAR
└──────────────────────────────────────────────┘
]
```

```
[用 ASCII 艺术绘制屏幕布局。建议字符：
 ┌ ┐ └ ┘ │ ─    用于边框
 ╔ ╗ ╚ ╝ ║ ═    用于强调/模态边框
 [ ]              用于交互元素（按钮、输入）
 { }              用于内容区（列表、网格、图像）
 ...              用于可滚动内容
 ●                用于打开时聚焦元素

示例：
┌──────────────────────────────────────────────┐
│  [← Back]        INVENTORY         [Options] │  ← 标题区
├──────────────────────────────────────────────┤
│ ┌──────────────┐  ┌─────────────────────────┐│
│ │ CATEGORY NAV │  │  ITEM DETAIL PANEL      ││  ← 内容区
│ │  ● Weapons   │  │  Item Name              ││
│ │    Armor     │  │  {item icon}            ││
│ │    Consumable│  │  Stats comparison       ││
│ │    Key Items │  │  Description text...    ││
│ ├──────────────┤  └─────────────────────────┘│
│ │ ITEM GRID    │                             │
│ │ {□}{□}{□}{□} │                             │
│ │ {□}{□}{□}{□} │                             │
│ │ ...          │                             │
│ └──────────────┘                             │
├──────────────────────────────────────────────┤
│   [Equip]     [Drop]     [Compare]  [Close]  │  ← 动作栏
└──────────────────────────────────────────────┘
]
```

### 5.2 Zone Definitions
### 5.2 区域定义

| Zone Name | Description | Approximate Size | Scrollable? | Overflow Behavior |
|-----------|-------------|-----------------|-------------|-------------------|
| [e.g., Header Zone] | [Top bar: navigation, screen title, global actions] | [Full width, ~10% height] | [No] | [Truncate long screen names with ellipsis] |
| [e.g., Category Nav] | [Left panel: item category tabs] | [~25% width, ~75% height] | [Yes — vertical if categories exceed panel] | [Scroll indicator appears at bottom of list] |
| [e.g., Item Grid] | [Center: grid of item icons for selected category] | [~45% width, ~75% height] | [Yes — vertical] | [Page-based: 4x4 grid, next page on overflow] |
| [e.g., Detail Panel] | [Right: stats and description for selected item] | [~30% width, ~75% height] | [Yes — vertical for long descriptions] | [Fade at bottom, scroll to reveal] |
| [e.g., Action Bar] | [Bottom: context-sensitive actions for selected item] | [Full width, ~15% height] | [No] | [Actions collapse to icon-only below 4] |
| 区域名称 | 描述 | 近似尺寸 | 可滚动？ | 溢出行为 |
|-----------|-------------|-----------------|-------------|-------------------|
| [例如，标题区] | [顶部栏：导航、屏幕标题、全局动作] | [全宽，~10% 高度] | [否] | [长屏幕名用省略号截断] |
| [例如，类别导航] | [左面板：物品类别标签] | [~25% 宽，~75% 高] | [是 — 类别超出面板时垂直] | [列表底部出现滚动指示器] |
| [例如，物品网格] | [中心：所选类别的物品图标网格] | [~45% 宽，~75% 高] | [是 — 垂直] | [基于页：4x4 网格，溢出时下一页] |
| [例如，详情面板] | [右：所选物品的属性与描述] | [~30% 宽，~75% 高] | [是 — 长描述垂直] | [底部淡出，滚动揭示] |
| [例如，动作栏] | [底部：所选物品的上下文敏感动作] | [全宽，~15% 高] | [否] | [动作低于 4 时折叠为仅图标] |

### 5.3 Component Inventory
### 5.3 组件清单

> List every discrete UI component on this screen. This table drives the implementation
> task list — each row becomes a component to build or reuse.
> 列出此屏幕上每个离散 UI 组件。此表驱动实施任务列表 — 每行成为要构建或复用的组件。

| Component Name | Type | Zone | Purpose | Required? | Reuses Existing Component? |
|----------------|------|------|---------|-----------|---------------------------|
| [e.g., Back Button] | [Button] | [Header] | [Returns to previous screen] | [Yes] | [Yes — standard NavButton component] |
| [e.g., Screen Title Label] | [Text] | [Header] | [Displays "INVENTORY" or context name] | [Yes] | [Yes — ScreenTitle component] |
| [e.g., Category Tab] | [Toggle Button] | [Category Nav] | [Filters item grid by category] | [Yes] | [No — new component needed] |
| [e.g., Item Slot] | [Icon + Frame] | [Item Grid] | [Represents one inventory slot, empty or filled] | [Yes] | [No — new component] |
| [e.g., Item Name Label] | [Text] | [Detail Panel] | [Shows selected item's name] | [Yes] | [Yes — BodyText component] |
| [e.g., Stat Comparison Row] | [Compound — label + value + delta] | [Detail Panel] | [Shows stat value vs. currently equipped] | [Yes] | [No — new component] |
| [e.g., Equip Button] | [Primary Button] | [Action Bar] | [Equips selected item in appropriate slot] | [Yes] | [Yes — PrimaryAction component] |
| [e.g., Empty State Message] | [Text + Icon] | [Item Grid] | [Shown when category has no items] | [Yes] | [Yes — EmptyState component] |
| 组件名称 | 类型 | 区域 | 用途 | 必需？ | 复用现有组件？ |
|----------------|------|------|---------|-----------|---------------------------|
| [例如，返回按钮] | [按钮] | [标题] | [返回前一屏幕] | [是] | [是 — 标准 NavButton 组件] |
| [例如，屏幕标题标签] | [文本] | [标题] | [显示 "INVENTORY" 或上下文名] | [是] | [是 — ScreenTitle 组件] |
| [例如，类别标签] | [切换按钮] | [类别导航] | [按类别过滤物品网格] | [是] | [否 — 需新组件] |
| [例如，物品槽] | [图标 + 框] | [物品网格] | [代表一个库存槽，空或满] | [是] | [否 — 新组件] |
| [例如，物品名标签] | [文本] | [详情面板] | [显示所选物品名] | [是] | [是 — BodyText 组件] |
| [例如，属性比较行] | [复合 — 标签 + 值 + 增量] | [详情面板] | [显示属性值 vs. 当前装备] | [是] | [否 — 新组件] |
| [例如，装备按钮] | [主按钮] | [动作栏] | [在适当槽装备所选物品] | [是] | [是 — PrimaryAction 组件] |
| [例如，空状态消息] | [文本 + 图标] | [物品网格] | [类别无物品时显示] | [是] | [是 — EmptyState 组件] |

**Primary focus element on open**: [e.g., The first item in the Item Grid — or, if deep-linked, the highlighted item. If the grid is empty, focus lands on the first Category Tab.]
**打开时主要聚焦元素**：[例如，物品网格中第一个物品 — 或如深链接，高亮物品。如网格为空，聚焦落在第一个类别标签。]

---

## 6. States & Variants
## 6. 状态与变体

> **Why this section exists**: A screen is not a single picture — it is a set of
> states, each of which must look correct and behave correctly. Screens that are
> designed only in their "happy path" state ship with broken empty states, invisible
> loading indicators, and crashes when data is missing. Document every state before
> implementation. The states table is also the test matrix for QA.
> **为何存在此节**：屏幕非单一画面 — 它是一组状态，每个都必须看起来正确且行为正确。
> 仅在其"快乐路径"状态设计的屏幕发布时有破损空状态、不可见加载指示器和数据缺失时崩溃。
> 实现前记录每个状态。状态表也是 QA 的测试矩阵。

| State Name | Trigger | What Changes Visually | What Changes Behaviorally | Notes |
|------------|---------|----------------------|--------------------------|-------|
| [Loading] | [Screen is opening, data not yet available] | [Item Grid shows skeleton/shimmer placeholders; Action Bar buttons disabled] | [No interactions possible except Close] | [Should not be visible >500ms under normal conditions; if it is, investigate data fetch performance] |
| [Empty — no items in category] | [Player switches to a category with zero items] | [Item Grid replaced by EmptyState component: icon + "Nothing here yet."] | [Action Bar shows no item actions; Drop/Equip/Compare all hidden] | [Do not show disabled buttons — remove them. Disabled buttons with no tooltip are confusing.] |
| [Populated — items present] | [Category has at least one item] | [Item Grid fills with item slots; first slot is auto-focused] | [All item actions available for selected item] | [Default and most common state] |
| [Item Selected] | [Player navigates to an item slot] | [Detail Panel populates; selected slot has focus ring; Action Bar updates to item's valid actions] | [Equip/Drop/Compare enabled based on item type] | [Equip is disabled if item is already equipped — show a "Equipped" badge instead] |
| [Confirmation Pending — Drop] | [Player selects Drop action] | [Confirmation dialog overlays the screen] | [All background interactions suspended until dialog resolves] | [Use a modal confirmation, not an inline toggle. Items cannot be recovered after dropping.] |
| [Error — data load failed] | [Inventory data could not be retrieved] | [Item Grid shows error state: icon + "Couldn't load items." + Retry button] | [Only Retry and Close are available] | [Log the error; do not expose technical details to player] |
| [Item Newly Acquired] | [Screen opened from item pickup deep-link] | [Newly acquired item has a visual "New" badge; Detail Panel pre-populated with that item] | [Same as Item Selected but with badge until player navigates away] | [Badge persists until the player manually navigates off that slot once] |
| 状态名称 | 触发 | 视觉变化 | 行为变化 | 备注 |
|------------|---------|----------------------|--------------------------|-------|
| [加载] | [屏幕打开中，数据尚不可用] | [物品网格显示骨架/闪光占位；动作栏按钮禁用] | [除关闭外无交互] | [正常条件下不应可见 >500ms；如是，调查数据获取性能] |
| [空 — 类别无物品] | [玩家切换到零物品类别] | [物品网格由 EmptyState 组件替换：图标 + "此处尚无内容。"] | [动作栏不显示物品动作；丢弃/装备/比较全隐藏] | [不显示禁用按钮 — 移除它们。无工具提示的禁用按钮令人困惑。] |
| [有内容 — 物品存在] | [类别至少有一物品] | [物品网格填满物品槽；第一个槽自动聚焦] | [所选物品的所有物品动作可用] | [默认且最常见状态] |
| [物品已选] | [玩家导航到物品槽] | [详情面板填充；所选槽有聚焦环；动作栏更新为物品有效动作] | [装备/丢弃/比较基于物品类型启用] | [如物品已装备则装备禁用 — 改显示"已装备"徽章] |
| [确认待定 — 丢弃] | [玩家选择丢弃动作] | [确认对话框覆盖屏幕] | [所有后台交互暂停直到对话框解决] | [用模态确认，非内联切换。丢弃后物品不可恢复。] |
| [错误 — 数据加载失败] | [无法检索库存数据] | [物品网格显示错误状态：图标 + "无法加载物品。" + 重试按钮] | [仅重试与关闭可用] | [记录错误；不向玩家暴露技术细节] |
| [新获得物品] | [屏幕从物品拾取深链接打开] | [新获得物品有视觉"新"徽章；详情面板预填充该物品] | [同物品已选但带徽章直到玩家导航离开] | [徽章持续直到玩家手动从该槽导航离开一次] |

---

## 7. Interaction Map
## 7. 交互映射

> **Why this section exists**: This section is the source of truth for what every
> input does on this screen. It forces the designer to think through every input
> method (mouse, keyboard, gamepad, touch) and every interactive state (hover, focus,
> pressed, disabled). Gaps in this table are bugs waiting to happen. The
> interaction map is also the input for the accessibility audit — if an action is
> only reachable by mouse, it will fail the keyboard and gamepad columns.
> **为何存在此节**：此节是此屏幕上每个输入做什么的真相来源。它强制设计师思考每个输入方法
> （鼠标、键盘、手柄、触控）和每个交互状态（悬停、聚焦、按下、禁用）。此表中的缺口是等待
> 发生的错误。交互映射也是无障碍审计的输入 — 如动作仅鼠标可达，它将失败键盘与手柄列。

### 7.1 Navigation Inputs
### 7.1 导航输入

| Input | Platform | Action | Visual Response | Audio Cue | Notes |
|-------|----------|--------|-----------------|-----------|-------|
| [Arrow keys / D-Pad] | [All] | [Move focus within active zone] | [Focus ring moves to adjacent element] | [Soft navigation tick] | [Wrap at edges within zone; do not cross zones with arrows alone] |
| [Tab / R1] | [KB / Gamepad] | [Move focus to next zone (Category → Grid → Detail → Action Bar)] | [Focus ring jumps to first element in next zone] | [Distinct zone-change tone] | [Shift+Tab / L1 goes backward] |
| [Mouse hover] | [PC] | [Show hover state on interactive elements] | [Highlight / underline / color shift] | [None] | [Hover does NOT move focus — only click does] |
| [Mouse click] | [PC] | [Select and focus the clicked element] | [Pressed state flash, then selected/focused] | [Soft click] | [Right-click opens context menu if applicable; otherwise no-op] |
| [Touch tap] | [Mobile] | [Select and activate in one gesture] | [Press ripple] | [Soft click] | [Treat tap as click + confirm for low-risk actions; require explicit confirm for destructive actions] |
| 输入 | 平台 | 动作 | 视觉响应 | 音频提示 | 备注 |
|-------|----------|--------|-----------------|-----------|-------|
| [方向键 / D-Pad] | [全部] | [在活跃区内移动聚焦] | [聚焦环移到相邻元素] | [柔和导航滴答] | [区内边缘环绕；仅用方向键不跨区] |
| [Tab / R1] | [键盘 / 手柄] | [聚焦移到下一区（类别 → 网格 → 详情 → 动作栏）] | [聚焦环跳到下一区首元素] | [独特区改变音调] | [Shift+Tab / L1 向后] |
| [鼠标悬停] | [PC] | [在交互元素显示悬停状态] | [高亮 / 下划线 / 颜色变化] | [无] | [悬停不移动聚焦 — 仅点击移动] |
| [鼠标点击] | [PC] | [选择并聚焦所点击元素] | [按下状态闪烁，然后选中/聚焦] | [柔和点击] | [右键打开上下文菜单（如适用）；否则无操作] |
| [触控点按] | [移动] | [一手势选择并激活] | [按下涟漪] | [柔和点击] | [低风险动作将点按视为点击 + 确认；破坏性动作需显式确认] |

### 7.2 Action Inputs
### 7.2 动作输入

| Input | Platform | Context (What must be focused) | Action | Response | Animation | Audio Cue | Notes |
|-------|----------|-------------------------------|--------|----------|-----------|-----------|-------|
| [Enter / A button / Left click] | [All] | [Item slot focused] | [Select item → populate Detail Panel] | [Detail panel slides in or updates in place] | [Panel fade/slide in, 120ms] | [Soft select tone] | [If item already selected: no-op] |
| [Enter / A button] | [All] | [Equip button focused] | [Equip selected item] | [Button animates press; item badge updates to "Equipped"; previously equipped item loses badge] | [Badge swap, 80ms] | [Equip success sound] | [Fires EquipItem event to Inventory system] |
| [Triangle / Y button / Right-click] | [All] | [Item slot focused] | [Open item context menu] | [Context menu appears adjacent to item slot] | [Popover, 80ms] | [Menu open sound] | [Context menu contains: Equip, Drop, Inspect, Compare] |
| [Square / X button] | [Gamepad] | [Item slot focused] | [Quick-equip without opening detail] | [Equip animation plays inline on slot] | [Slot flash, 80ms] | [Equip success sound] | [Convenience shortcut; does not change screen state] |
| [Esc / B button / Back] | [All] | [Any, screen level] | [Close screen and return to previous state] | [Screen exit transition plays] | [Slide out, 200ms] | [Back/close tone] | [Commits all changes before closing. No discard — inventory is not a draft.] |
| [F / L2] | [KB / Gamepad] | [Any] | [Toggle filter panel] | [Sort/filter overlay opens] | [Slide in from right, 200ms] | [Panel open tone] | [If no items in category, filter is disabled] |
| 输入 | 平台 | 上下文（必须聚焦什么） | 动作 | 响应 | 动画 | 音频提示 | 备注 |
|-------|----------|-------------------------------|--------|----------|-----------|-----------|-------|
| [Enter / A 键 / 左键] | [全部] | [物品槽聚焦] | [选择物品 → 填充详情面板] | [详情面板滑入或原地更新] | [面板淡入/滑入，120ms] | [柔和选择音] | [如物品已选：无操作] |
| [Enter / A 键] | [全部] | [装备按钮聚焦] | [装备所选物品] | [按钮动画按下；物品徽章更新为"已装备"；前装备物品失去徽章] | [徽章交换，80ms] | [装备成功声] | [向库存系统触发 EquipItem 事件] |
| [Triangle / Y 键 / 右键] | [全部] | [物品槽聚焦] | [打开物品上下文菜单] | [上下文菜单出现在物品槽附近] | [弹出，80ms] | [菜单打开声] | [上下文菜单包含：装备、丢弃、检查、比较] |
| [Square / X 键] | [手柄] | [物品槽聚焦] | [不打开详情快速装备] | [装备动画在槽内联播放] | [槽闪烁，80ms] | [装备成功声] | [便捷快捷方式；不改变屏幕状态] |
| [Esc / B 键 / Back] | [全部] | [任何，屏幕级] | [关闭屏幕并返回前一状态] | [屏幕退出过渡播放] | [滑出，200ms] | [返回/关闭音] | [关闭前提交所有更改。无丢弃 — 库存非草稿。] |
| [F / L2] | [键盘 / 手柄] | [任何] | [切换过滤面板] | [排序/过滤覆盖打开] | [从右滑入，200ms] | [面板打开音] | [如类别无物品，过滤禁用] |

### 7.3 State-Specific Behaviors
### 7.3 状态特定行为

| State | Input Restriction | Reason |
|-------|------------------|--------|
| [Loading] | [All item and action inputs disabled] | [No data to act on; prevent race conditions] |
| [Confirmation dialog open] | [Only Confirm and Cancel inputs active] | [Modal — background is locked] |
| [Error state] | [Only Retry and Close active] | [No data available to navigate] |
| 状态 | 输入限制 | 原因 |
|-------|------------------|--------|
| [加载] | [所有物品与动作输入禁用] | [无数据可操作；防止竞态条件] |
| [确认对话框打开] | [仅确认与取消输入活跃] | [模态 — 后台锁定] |
| [错误状态] | [仅重试与关闭活跃] | [无数据可导航] |

---

## 8. Data Requirements
## 8. 数据要求

> **Why this section exists**: The separation between UI and game state is the most
> important architectural boundary in a game's UI system. UI reads data; it does not
> own it. UI fires events; it does not write state directly. This section defines
> exactly what data this screen needs to display, where it comes from, and how
> frequently it updates. Filling this table before implementation prevents two
> common failure modes: (1) UI developers reaching into systems they should not touch,
> and (2) systems not knowing they need to expose data until a UI is half-built.
> **为何存在此节**：UI 与游戏状态的分离是游戏 UI 系统中最重要的架构边界。UI 读数据；
> 它不拥有数据。UI 触发事件；它不直接写状态。此节准确定义此屏幕需显示什么数据、
> 来自何处以及更新频率。实现前填此表防止两种常见失败模式：
> (1) UI 开发者触及他们不应触及的系统，(2) 系统不知需暴露数据直到 UI 已半建。

| Data Element | Source System | Update Frequency | Who Owns It | Format | Null / Missing Handling |
|--------------|--------------|-----------------|-------------|--------|------------------------|
| [e.g., Item list] | [Inventory System] | [On screen open; on InventoryChanged event] | [InventorySystem] | [Array of ItemData structs: id, name, icon_path, category, stats, is_equipped] | [Empty array → show Empty State. Never null — system must return array.] |
| [e.g., Equipped loadout] | [Equipment System] | [On screen open; on EquipmentChanged event] | [EquipmentSystem] | [Dict mapping slot_id → item_id] | [Unequipped slot has null value — UI shows empty slot icon] |
| [e.g., Item stat comparisons] | [Stats System] | [On item selection change] | [StatsSystem] | [Dict mapping stat_name → {current, new, delta}] | [If no item selected, detail panel shows placeholder. Stats system must handle this gracefully.] |
| [e.g., Player currency] | [Economy System] | [On screen open only — inventory does not show live currency] | [EconomySystem] | [Int — gold pieces] | [If currency system not active for this game mode, hide the currency row entirely] |
| [e.g., Newly acquired item flag] | [Inventory System] | [On screen open] | [InventorySystem] | [Array of item_ids flagged as new] | [If empty array, no badges shown] |
| 数据元素 | 源系统 | 更新频率 | 谁拥有 | 格式 | Null / 缺失处理 |
|--------------|--------------|-----------------|-------------|--------|------------------------|
| [例如，物品列表] | [库存系统] | [屏幕打开时；InventoryChanged 事件时] | [InventorySystem] | [ItemData 结构数组：id、name、icon_path、category、stats、is_equipped] | [空数组 → 显示空状态。永不 null — 系统必须返回数组。] |
| [例如，已装备装备] | [装备系统] | [屏幕打开时；EquipmentChanged 事件时] | [EquipmentSystem] | [映射 slot_id → item_id 的字典] | [未装备槽有 null 值 — UI 显示空槽图标] |
| [例如，物品属性比较] | [属性系统] | [物品选择改变时] | [StatsSystem] | [映射 stat_name → {current, new, delta} 的字典] | [如无物品选择，详情面板显示占位。属性系统必须优雅处理。] |
| [例如，玩家货币] | [经济系统] | [仅屏幕打开时 — 库存不显示实时货币] | [EconomySystem] | [整数 — 金币] | [如此游戏模式货币系统不活跃，完全隐藏货币行] |
| [例如，新获得物品标记] | [库存系统] | [屏幕打开时] | [InventorySystem] | [标记为新的 item_id 数组] | [如空数组，不显示徽章] |

> **Rule**: This screen must never write directly to any system listed above. All
> player actions fire events (see Section 9). Systems update their own data and
> notify the UI.
> **规则**：此屏幕绝不得直接写上面列出的任何系统。所有玩家动作触发事件（见第 9 节）。
> 系统更新自己的数据并通知 UI。

---

## 9. Events Fired
## 9. 触发的事件

> **Why this section exists**: This is the other half of the UI/system boundary.
> Where Section 8 defines what the UI reads, this section defines what the UI
> communicates back to the game. Specifying events at design time prevents UI
> programmers from writing game logic, and prevents game programmers from being
> surprised by what the UI does. Every destructive or state-changing player action
> must appear in this table.
> **为何存在此节**：这是 UI/系统边界的另一半。第 8 节定义 UI 读什么，此节定义 UI
> 向游戏传达什么。设计时规定事件防止 UI 程序员写游戏逻辑，并防止游戏程序员对 UI
> 做什么感到惊讶。每个破坏性或状态改变的玩家动作必须出现在此表。

| Player Action | Event Fired | Payload | Receiver System | Notes |
|---------------|-------------|---------|-----------------|-------|
| [Player equips an item] | [EquipItemRequested] | [{item_id: string, target_slot: string}] | [Equipment System] | [Equipment System validates the action and fires EquipmentChanged if successful; UI listens for EquipmentChanged to update its display] |
| [Player drops an item] | [DropItemRequested] | [{item_id: string, quantity: int}] | [Inventory System] | [Fires only after player confirms the drop dialog. Inventory System removes the item and fires InventoryChanged.] |
| [Player opens item compare] | [ItemCompareOpened] | [{item_a_id: string, item_b_id: string}] | [Analytics System] | [No game-state change — analytics event only. Compare view is purely local UI state.] |
| [Player closes screen] | [InventoryScreenClosed] | [{session_duration_ms: int}] | [Analytics System] | [Fires on every close regardless of reason. Used for engagement metrics.] |
| [Player navigates between categories] | [InventoryCategoryChanged] | [{category: string}] | [Analytics System] | [Analytics only. No game state change.] |
| 玩家动作 | 触发事件 | 负载 | 接收系统 | 备注 |
|---------------|-------------|---------|-----------------|-------|
| [玩家装备物品] | [EquipItemRequested] | [{item_id: string, target_slot: string}] | [装备系统] | [装备系统验证动作并如成功触发 EquipmentChanged；UI 监听 EquipmentChanged 以更新显示] |
| [玩家丢弃物品] | [DropItemRequested] | [{item_id: string, quantity: int}] | [库存系统] | [仅在玩家确认丢弃对话框后触发。库存系统移除物品并触发 InventoryChanged。] |
| [玩家打开物品比较] | [ItemCompareOpened] | [{item_a_id: string, item_b_id: string}] | [分析系统] | [无游戏状态改变 — 仅分析事件。比较视图纯本地 UI 状态。] |
| [玩家关闭屏幕] | [InventoryScreenClosed] | [{session_duration_ms: int}] | [分析系统] | [无论原因每次关闭触发。用于参与度指标。] |
| [玩家在类别间导航] | [InventoryCategoryChanged] | [{category: string}] | [分析系统] | [仅分析。无游戏状态改变。] |

---

## 10. Transition & Animation
## 10. 过渡与动画

> **Why this section exists**: Transitions are not decoration — they communicate
> hierarchy and causality. A screen that slides in from the right implies the
> player has moved forward. A screen that fades implies a context break. Inconsistent
> transitions make navigation feel broken even when it is technically correct.
> This section ensures transitions are specified intentionally, not left to the
> developer's discretion, and that accessibility settings (reduced motion) are
> planned for from the start.
> **为何存在此节**：过渡非装饰 — 它们传达层级与因果。从右滑入的屏幕暗示玩家向前移动。
> 淡出的屏幕暗示上下文中断。不一致的过渡使导航感觉破损，即使技术上正确。
> 此节确保过渡有意规定，而非留给开发者自行决定，且无障碍设置（减少动效）从一开始就规划。

| Transition | Trigger | Direction / Type | Duration (ms) | Easing | Interruptible? | Skipped by Reduced Motion? |
|------------|---------|-----------------|--------------|--------|----------------|---------------------------|
| [Screen enter] | [Screen pushed onto stack] | [Slide in from right] | [250] | [Ease out cubic] | [No — must complete before interaction is enabled] | [Yes — instant appear at 0ms] |
| [Screen exit — Back] | [Player presses Back] | [Slide out to right] | [200] | [Ease in cubic] | [No] | [Yes — instant disappear] |
| [Screen exit — Forward] | [Player navigates to child screen] | [Slide out to left] | [200] | [Ease in cubic] | [No] | [Yes — instant] |
| [Detail panel update] | [Player selects a new item] | [Cross-fade content] | [120] | [Linear] | [Yes — if player navigates quickly, previous animation cancels] | [Yes — instant swap] |
| [Loading → Populated] | [Data arrives after load] | [Skeleton shimmer fades out, content fades in] | [180] | [Ease out] | [No] | [Yes — instant reveal] |
| [Action Bar button press] | [Player activates a button] | [Scale down 95% on press, return on release] | [60 down / 60 up] | [Ease out / ease in] | [Yes — if released early, returns to normal] | [No — this is tactile feedback, not decorative motion] |
| [Confirmation dialog open] | [Player initiates destructive action] | [Background dims 60% opacity; dialog scales up from 95%] | [150] | [Ease out] | [No] | [Yes — instant appear, no scale] |
| [New item badge appear] | [Screen opens with newly acquired item] | [Badge pops from 0% to 110% to 100% scale] | [200 total] | [Ease out back] | [No] | [Yes — instant appear at 100% scale] |
| 过渡 | 触发 | 方向 / 类型 | 持续时间（ms） | 缓动 | 可中断？ | 减少动效是否跳过？ |
|------------|---------|-----------------|--------------|--------|----------------|---------------------------|
| [屏幕进入] | [屏幕推入栈] | [从右滑入] | [250] | [Ease out cubic] | [否 — 必须完成才能启用交互] | [是 — 0ms 即时出现] |
| [屏幕退出 — 后退] | [玩家按 Back] | [向右滑出] | [200] | [Ease in cubic] | [否] | [是 — 即时消失] |
| [屏幕退出 — 前进] | [玩家导航到子屏幕] | [向左滑出] | [200] | [Ease in cubic] | [否] | [是 — 即时] |
| [详情面板更新] | [玩家选择新物品] | [交叉淡入内容] | [120] | [线性] | [是 — 如玩家快速导航，前动画取消] | [是 — 即时交换] |
| [加载 → 有内容] | [加载后数据到达] | [骨架闪光淡出，内容淡入] | [180] | [Ease out] | [否] | [是 — 即时揭示] |
| [动作栏按钮按下] | [玩家激活按钮] | [按下缩放 95%，释放返回] | [60 下 / 60 上] | [Ease out / ease in] | [是 — 如提前释放，返回正常] | [否 — 这是触觉反馈，非装饰动效] |
| [确认对话框打开] | [玩家启动破坏性动作] | [背景变暗至 60% 不透明度；对话框从 95% 放大] | [150] | [Ease out] | [否] | [是 — 即时出现，无缩放] |
| [新物品徽章出现] | [屏幕带新获得物品打开] | [徽章从 0% 弹至 110% 再到 100% 缩放] | [共 200] | [Ease out back] | [否] | [是 — 在 100% 缩放即时出现] |

---

## 11. Input Method Completeness Checklist
## 11. 输入方法完整性检查表

> **Why this section exists**: Input completeness is not optional — it is a
> certification requirement for console platforms and a legal risk area for
> accessibility laws in multiple markets. Fill this checklist before marking
> the spec as Approved. Any unchecked item blocks implementation start.
> **为何存在此节**：输入完整性非可选 — 它是主机平台的认证要求，也是多个市场无障碍法律的
> 法律风险领域。在标记规格为已批准前填此检查表。任何未勾选项阻碍实现开始。

**Keyboard**
**键盘**
- [ ] All interactive elements are reachable using Tab and arrow keys alone
- [ ] Tab order follows visual reading order (left-to-right, top-to-bottom within each zone)
- [ ] Every action achievable by mouse is also achievable by keyboard
- [ ] Focus is visible at all times (no element where focus ring disappears)
- [ ] Focus does not escape the screen while it is open (focus trap for modals)
- [ ] Esc key closes or cancels (and does not quit the game from within a screen)
- [ ] 所有交互元素仅用 Tab 与方向键可达
- [ ] Tab 顺序遵循视觉阅读顺序（每区内从左到右、从上到下）
- [ ] 鼠标可实现的每个动作键盘也可实现
- [ ] 聚焦始终可见（无聚焦环消失的元素）
- [ ] 打开时聚焦不逃出屏幕（模态的聚焦陷阱）
- [ ] Esc 键关闭或取消（且不从屏幕内退出游戏）

**Gamepad**
**手柄**
- [ ] All interactive elements reachable with D-Pad and left stick
- [ ] Face button mapping documented and consistent with platform conventions (see Section 7.2)
- [ ] No action requires analog stick precision that cannot be replicated with D-Pad
- [ ] Trigger and bumper shortcuts documented if used
- [ ] Controller disconnection while screen is open is handled gracefully
- [ ] 所有交互元素用 D-Pad 与左摇杆可达
- [ ] 面键映射已记录且与平台约定一致（见第 7.2 节）
- [ ] 无动作需要 D-Pad 无法复制的摇杆精度
- [ ] 如使用，扳机与肩键快捷方式已记录
- [ ] 屏幕打开时控制器断开优雅处理

**Mouse**
**鼠标**
- [ ] Hover states defined for all interactive elements
- [ ] Clickable hit targets are at minimum 32x32px (44x44px preferred)
- [ ] Right-click behavior defined (context menu or no-op — not undefined)
- [ ] Scroll wheel behavior defined in all scrollable zones
- [ ] 所有交互元素已定义悬停状态
- [ ] 可点击命中目标最小 32x32px（首选 44x44px）
- [ ] 右键行为已定义（上下文菜单或无操作 — 非未定义）
- [ ] 所有可滚动区已定义滚轮行为

**Touch (if applicable)**
**触控（如适用）**
- [ ] All touch targets are minimum 44x44px
- [ ] Swipe gestures do not conflict with system-level swipe navigation
- [ ] All actions achievable with one hand in portrait orientation
- [ ] Long-press behavior defined if used
- [ ] 所有触控目标最小 44x44px
- [ ] 滑动手势不与系统级滑动导航冲突
- [ ] 竖屏方向所有动作可单手实现
- [ ] 如使用，长按行为已定义

---

## 12. Screen-Level Accessibility Requirements
## 12. 屏幕级无障碍要求

> **Why this section exists**: Accessibility requirements must be specified at design
> time because retrofitting them is expensive and often architecturally impractical.
> This section documents requirements specific to this screen. Project-wide standards
> live in `docs/accessibility-requirements.md` — consult it before filling this
> section so you do not duplicate or contradict project-level commitments.
>
> Accessibility Tiers in this project:
> - Basic: WCAG 2.1 AA text contrast, keyboard navigable, no motion-only information
> - Standard: Basic + screen reader support, colorblind-safe, focus management
> - Comprehensive: Standard + reduced motion support, text scaling, high contrast mode
> - Exemplary: Comprehensive + cognitive load management, AAA equivalent, certified
> **为何存在此节**：无障碍要求必须在设计时规定，因为改造它们昂贵且常常架构上不可行。
> 此节记录此屏幕专属要求。项目范围标准位于 `docs/accessibility-requirements.md` —
> 填此节前查阅它以便不重复或矛盾项目级承诺。
>
> 此项目的无障碍层级：
> - 基础：WCAG 2.1 AA 文本对比、键盘可导航、无仅动效信息
> - 标准：基础 + 屏幕阅读器支持、色盲安全、聚焦管理
> - 全面：标准 + 减少动效支持、文本缩放、高对比度模式
> - 示范：全面 + 认知负载管理、AAA 等价、已认证

**Text contrast requirements for this screen**:
**此屏幕的文本对比要求**：

| Text Element | Background Context | Required Ratio | Current Ratio | Pass? |
|--------------|-------------------|---------------|---------------|-------|
| [e.g., Item name in Detail Panel] | [Dark panel background ~#1a1a1a] | [4.5:1 (WCAG AA normal text)] | [TBD — verify in implementation] | [ ] |
| [e.g., Category tab label — inactive] | [Mid-grey tab background] | [4.5:1] | [TBD] | [ ] |
| [e.g., Category tab label — active] | [Accent color background] | [4.5:1] | [TBD] | [ ] |
| [e.g., Action button label] | [Button color (varies by state)] | [4.5:1] | [TBD] | [ ] |
| [e.g., Stat comparison delta (positive)] | [Detail panel] | [4.5:1 — do NOT rely on green color alone] | [TBD] | [ ] |
| 文本元素 | 背景上下文 | 要求比率 | 当前比率 | 通过？ |
|--------------|-------------------|---------------|---------------|-------|
| [例如，详情面板物品名] | [深面板背景 ~#1a1a1a] | [4.5:1（WCAG AA 正常文本）] | [待定 — 实现时验证] | [ ] |
| [例如，类别标签 — 未激活] | [中灰标签背景] | [4.5:1] | [待定] | [ ] |
| [例如，类别标签 — 激活] | [强调色背景] | [4.5:1] | [待定] | [ ] |
| [例如，动作按钮标签] | [按钮颜色（按状态变化）] | [4.5:1] | [待定] | [ ] |
| [例如，属性比较增量（正）] | [详情面板] | [4.5:1 — 不依赖绿色] | [待定] | [ ] |

**Colorblind-unsafe elements and mitigations**:
**色盲不安全元素与缓解**：

| Element | Colorblind Risk | Mitigation |
|---------|----------------|------------|
| [e.g., Stat delta indicators (red/green for worse/better)] | [Red-green colorblindness (Deuteranopia) — most common form] | [Add arrow icons (↑ / ↓) and +/- prefix in addition to color. Color is a redundant, not sole, indicator.] |
| [e.g., Item rarity color coding (grey/green/blue/purple/orange)] | [Multiple types — rarity color is a common industry failure] | [Add rarity name text label below icon. Color is supplemental only.] |
| 元素 | 色盲风险 | 缓解 |
|---------|----------------|------------|
| [例如，属性增量指示器（红/绿表示差/好）] | [红绿色盲（绿色盲）— 最常见类型] | [除颜色外加箭头图标（↑ / ↓）与 +/- 前缀。颜色是冗余，非唯一，指示器。] |
| [例如，物品稀有度颜色编码（灰/绿/蓝/紫/橙）] | [多类型 — 稀有度颜色是常见行业失败] | [图标下方加稀有度名文本标签。颜色仅补充。] |

**Focus order** (Tab key sequence, numbered):
**聚焦顺序**（Tab 键序列，编号）：

[e.g.,
1. Back button (Header)
2. Options button (Header)
3. Category Tab 1 — Weapons
4. Category Tab 2 — Armor
5. Category Tab 3 — Consumables
6. Category Tab 4 — Key Items
7. Item Slot [0,0]
8. Item Slot [0,1] ... (grid traverses left-to-right, top-to-bottom)
9. Last item slot
10. Equip button (Action Bar)
11. Drop button (Action Bar)
12. Compare button (Action Bar)
13. Close button (Action Bar)
→ Cycles back to Back button

Focus does not enter the Detail Panel — it is a display panel driven by item focus, not independently navigable.]
[例如，
1. 返回按钮（标题）
2. 选项按钮（标题）
3. 类别标签 1 — 武器
4. 类别标签 2 — 护甲
5. 类别标签 3 — 消耗品
6. 类别标签 4 — 关键物品
7. 物品槽 [0,0]
8. 物品槽 [0,1] ...（网格从左到右、从上到下遍历）
9. 最后一个物品槽
10. 装备按钮（动作栏）
11. 丢弃按钮（动作栏）
12. 比较按钮（动作栏）
13. 关闭按钮（动作栏）
→ 循环回到返回按钮

聚焦不进入详情面板 — 它是由物品聚焦驱动的显示面板，非独立可导航。]

**Screen reader announcements for key state changes**:
**关键状态改变的屏幕阅读器公告**：

| State Change | Announcement Text | Announcement Timing |
|--------------|------------------|---------------------|
| [Screen opens] | ["Inventory screen. [N] items. [Active category] selected."] | [On screen focus settle] |
| [Player focuses an item slot] | ["[Item name]. [Category]. [Rarity]. [Key stats summary]. [Equipped / Not equipped]."] | [On focus arrival] |
| [Player equips an item] | ["[Item name] equipped to [slot name]."] | [After EquipmentChanged event confirmed] |
| [Player drops an item] | ["[Item name] dropped."] | [After InventoryChanged event confirmed] |
| [Category changes] | ["[Category name]. [N] items."] | [On category tab focus] |
| [Empty state shown] | ["No items in [category name]."] | [When empty state renders] |
| 状态改变 | 公告文本 | 公告时机 |
|--------------|------------------|---------------------|
| [屏幕打开] | ["库存屏幕。[N] 件物品。已选 [激活类别]。"] | [屏幕聚焦稳定时] |
| [玩家聚焦物品槽] | ["[物品名]。[类别]。[稀有度]。[关键属性摘要]。[已装备 / 未装备]。"] | [聚焦到达时] |
| [玩家装备物品] | ["[物品名] 装备到 [槽名]。"] | [EquipmentChanged 事件确认后] |
| [玩家丢弃物品] | ["[物品名] 已丢弃。"] | [InventoryChanged 事件确认后] |
| [类别改变] | ["[类别名]。[N] 件物品。"] | [类别标签聚焦时] |
| [显示空状态] | ["[类别名] 中无物品。"] | [空状态渲染时] |

**Cognitive load assessment**:
**认知负载评估**：

[Estimate the number of information streams the player is simultaneously tracking while
using this screen. For this screen: (1) item grid position, (2) item detail stats,
(3) current equipment loadout for comparison, (4) available actions, (5) item category.
That is 5 concurrent streams — within the standard 7±2 limit, but at the higher end.
Mitigation: detail panel auto-updates on navigation so the player never needs to
manually retrieve item info. Reduce active decisions by surfacing stat comparison
automatically.]
[估算玩家使用此屏幕时同时追踪的信息流数量。对于此屏幕：
(1) 物品网格位置，(2) 物品详情属性，(3) 当前装备装备用于比较，
(4) 可用动作，(5) 物品类别。这是 5 个并发流 — 在标准 7±2 限制内，但偏高端。
缓解：详情面板在导航时自动更新以便玩家永不需手动检索物品信息。
通过自动显示属性比较减少活跃决策。]

---

## 13. Localization Considerations
## 13. 本地化考量

> **Why this section exists**: UI built without localization in mind breaks on first
> translation. German text is typically 30–40% longer than English. Arabic and Hebrew
> require right-to-left layout mirroring. Japanese and Chinese text may be significantly
> shorter than English, creating awkward whitespace. These issues are cheap to plan for
> and expensive to fix after a layout is built and shipped. Every text element should
> have an explicit max-character count and a plan for overflow.
> **为何存在此节**：未考虑本地化构建的 UI 在首次翻译时破裂。德语文本通常比英文长 30–40%。
> 阿拉伯语和希伯来语需要从右到左布局镜像。日文和中文文本可能比英文显著短，
> 造成尴尬空白。这些问题规划成本低，但布局构建并发布后修复成本高。
> 每个文本元素应有显式最大字符数与溢出计划。

**General rules for this screen**:
**此屏幕的通用规则**：
- All text elements must tolerate a minimum of 40% expansion from English baseline
- RTL layout (Arabic, Hebrew): mirrored layout required — document which elements mirror and which do not
- CJK languages (Japanese, Korean, Chinese): text may be 20-30% shorter — verify layouts do not look broken with less text
- Do not use text in images — all text must be from localization strings
- 所有文本元素必须容忍英文基线至少 40% 扩展
- RTL 布局（阿拉伯语、希伯来语）：需镜像布局 — 记录哪些元素镜像、哪些不镜像
- CJK 语言（日文、韩文、中文）：文本可能短 20-30% — 验证布局在文本少时不显破损
- 不在图像中使用文本 — 所有文本必须来自本地化字符串

| Text Element | English Baseline Length | Max Characters | Expansion Budget | RTL Behavior | Overflow Behavior | Risk |
|--------------|------------------------|----------------|-----------------|--------------|-------------------|------|
| [e.g., Screen title "INVENTORY"] | [9 chars] | [16 chars] | [78%] | [Mirror to right, or center — acceptable] | [Truncate with ellipsis — title is not critical content] | [Low] |
| [e.g., Item name] | [~15 chars avg, max ~35 "Enchanted Dragon Scale Gauntlets"] | [50 chars] | [43%] | [Right-align in RTL layouts] | [Truncate with tooltip showing full name on hover/focus] | [Medium — long fantasy item names are common] |
| [e.g., Item description] | [~80–120 chars] | [200 chars] | [67%] | [Right-align, wrap normally] | [Scroll within Detail Panel — no truncation] | [Low — panel is scrollable] |
| [e.g., Action button "Equip"] | [5 chars] | [14 chars] | [180%] | [Button layout mirrors; text right-aligns] | [Shrink font to 90% minimum, then truncate] | [Medium — "Ausrüsten" in German is 9 chars] |
| [e.g., Category tab "Consumables"] | [11 chars] | [18 chars] | [64%] | [Mirror tab position] | [Abbreviate: "Consum." — define abbreviations per language in loc file] | [High — long localized tab labels are a known problem] |
| 文本元素 | 英文基线长度 | 最大字符 | 扩展预算 | RTL 行为 | 溢出行为 | 风险 |
|--------------|------------------------|----------------|-----------------|--------------|-------------------|------|
| [例如，屏幕标题 "INVENTORY"] | [9 字符] | [16 字符] | [78%] | [镜像到右，或居中 — 可接受] | [用省略号截断 — 标题非关键内容] | [低] |
| [例如，物品名] | [~15 字符均，最大 ~35 "Enchanted Dragon Scale Gauntlets"] | [50 字符] | [43%] | [RTL 布局右对齐] | [截断并在悬停/聚焦时显示工具提示显示全名] | [中 — 长奇幻物品名常见] |
| [例如，物品描述] | [~80–120 字符] | [200 字符] | [67%] | [右对齐，正常换行] | [详情面板内滚动 — 无截断] | [低 — 面板可滚动] |
| [例如，动作按钮 "Equip"] | [5 字符] | [14 字符] | [180%] | [按钮布局镜像；文本右对齐] | [字体缩小到 90% 最低，然后截断] | [中 — 德语 "Ausrüsten" 是 9 字符] |
| [例如，类别标签 "Consumables"] | [11 字符] | [18 字符] | [64%] | [镜像标签位置] | [缩写："Consum." — 在 loc 文件中按语言定义缩写] | [高 — 长本地化标签是已知问题] |

---

## 14. Acceptance Criteria
## 14. 验收标准

> **Why this section exists**: Acceptance criteria are the contractual definition of
> "done." Without them, implementation is complete when the developer says it is.
> With them, implementation is complete when a QA tester can verify every item on
> this list. Write criteria that a tester can verify independently, without asking the
> designer what they meant. Every criterion should be binary — pass or fail, not
> subjective.
> **为何存在此节**：验收标准是"完成"的契约定义。无它们，实现完成于开发者说完成时。
> 有它们，实现完成于 QA 测试者可验证此列表每项时。写测试者可独立验证的标准，
> 无需问设计师他们什么意思。每个标准应为二元 — 通过或失败，非主观。

**Performance**
**性能**
- [ ] Screen opens (first frame visible) within 200ms of trigger on minimum-spec hardware
- [ ] Screen is fully interactive (all data loaded) within 500ms of trigger on minimum-spec hardware
- [ ] Navigation between items produces no perceptible frame drop (maintain target framerate ±5fps)
- [ ] 屏幕在最低规格硬件触发后 200ms 内打开（首帧可见）
- [ ] 屏幕在最低规格硬件触发后 500ms 内完全可交互（所有数据加载）
- [ ] 物品间导航不产生可感知帧率下降（维持目标帧率 ±5fps）

**Layout & Rendering**
**布局与渲染**
- [ ] Screen displays correctly (no overlap, no cutoff, no overflow) at minimum supported resolution [specify]
- [ ] Screen displays correctly at maximum supported resolution [specify]
- [ ] Screen displays correctly at 4:3, 16:9, 16:10, and 21:9 aspect ratios if targeting PC
- [ ] No text overflow or truncation in English within defined max-character bounds
- [ ] No text overflow or truncation in the longest-translation language [specify — typically German]
- [ ] All states (Loading, Empty, Populated, Error, Confirmation) render correctly
- [ ] Item grid scrolls smoothly without frame drops when all item slots are populated
- [ ] 屏幕在最低支持分辨率正确显示（无重叠、无截断、无溢出）[指定]
- [ ] 屏幕在最高支持分辨率正确显示 [指定]
- [ ] 如目标 PC，屏幕在 4:3、16:9、16:10 与 21:9 宽高比正确显示
- [ ] 英文在定义最大字符边界内无文本溢出或截断
- [ ] 最长翻译语言无文本溢出或截断 [指定 — 通常德文]
- [ ] 所有状态（加载、空、有内容、错误、确认）正确渲染
- [ ] 所有物品槽有内容时物品网格无帧率下降平滑滚动

**Input**
**输入**
- [ ] All interactive elements reachable by keyboard using Tab and arrow keys only
- [ ] All interactive elements reachable by gamepad using D-Pad and face buttons only
- [ ] All interactive elements reachable by mouse without keyboard
- [ ] No action requires simultaneous input that is not documented in Section 7
- [ ] Focus is visible at all times on keyboard and gamepad navigation
- [ ] Focus does not escape the screen while it is open
- [ ] 所有交互元素仅用键盘 Tab 与方向键可达
- [ ] 所有交互元素仅用手柄 D-Pad 与面键可达
- [ ] 所有交互元素用鼠标无键盘可达
- [ ] 无动作需第 7 节未记录的同时输入
- [ ] 键盘与手柄导航时聚焦始终可见
- [ ] 打开时聚焦不逃出屏幕

**Events & Data**
**事件与数据**
- [ ] All events in Section 9 fire with correct payloads on all exit paths (verify with debug logging)
- [ ] Screen does not write directly to any game system (verify: no direct state mutation calls)
- [ ] Inventory changes persist correctly after screen is closed and reopened
- [ ] Screen handles InventoryChanged events fired by other systems while it is open without crashing
- [ ] 第 9 节所有事件在所有退出路径以正确负载触发（用调试日志验证）
- [ ] 屏幕不直接写任何游戏系统（验证：无直接状态变更调用）
- [ ] 库存更改在屏幕关闭并重开后正确持久
- [ ] 屏幕在打开时处理其他系统触发的 InventoryChanged 事件而不崩溃

**Accessibility**
**无障碍**
- [ ] All text passes minimum contrast ratios specified in Section 12
- [ ] Stat comparison does not rely on color alone as the sole differentiator
- [ ] Screen reader announces item name and key stats on focus (verify with platform screen reader)
- [ ] Reduced motion setting results in instant transitions (no animated transitions)
- [ ] High contrast mode (if applicable to Accessibility Tier) renders without visual breakage
- [ ] 所有文本通过第 12 节规定的最低对比比率
- [ ] 属性比较不依赖颜色作为唯一区分器
- [ ] 屏幕阅读器在聚焦时公告物品名与关键属性（用平台屏幕阅读器验证）
- [ ] 减少动效设置导致即时过渡（无动画过渡）
- [ ] 高对比度模式（如适用于无障碍层级）渲染无视觉破损

**Localization**
**本地化**
- [ ] No text element overflows its container in any supported language
- [ ] RTL layout renders correctly (if RTL is a target language)
- [ ] All text elements are driven by localization strings — no hardcoded display text
- [ ] 任何支持语言中无文本元素溢出其容器
- [ ] RTL 布局正确渲染（如 RTL 是目标语言）
- [ ] 所有文本元素由本地化字符串驱动 — 无硬编码显示文本

---

## 15. Open Questions
## 15. 待解决问题

> Track unresolved design questions here. Each question should have a clear owner
> and a deadline. An Approved spec must have zero open questions — move to a decision
> or explicitly document the deferral rationale.
> 在此追踪未解决的设计问题。每个问题应有清晰负责人与截止日期。
> 已批准规格必须有零个待解决问题 — 移到决策或显式记录延后理由。

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| [e.g., Should item comparison be automatic (always showing equipped stats) or player-triggered (press Compare)?] | [ui-designer] | [Sprint 4, Day 3] | [Pending] |
| [e.g., Do we support controller cursor (free aim) in the item grid, or d-pad-only grid navigation?] | [lead-programmer + ui-designer] | [Sprint 4, Day 3] | [Pending — depends on ADR-0015 input model decision] |
| [e.g., What is the game's item drop policy — permanent loss or drop-to-world?] | [systems-designer] | [Requires GDD update] | [Blocked on inventory GDD Edge Cases section] |
| [e.g., Maximum inventory size — does the grid have a hard cap or is it infinite-scroll?] | [economy-designer] | [Sprint 3, Day 5] | [Pending] |
| 问题 | 负责人 | 截止日期 | 解决 |
|----------|-------|----------|-----------|
| [例如，物品比较应自动（始终显示已装备属性）还是玩家触发（按 Compare）？] | [ui-designer] | [Sprint 4, Day 3] | [待定] |
| [例如，我们支持手柄光标（自由瞄准）在物品网格中，还是仅 D-pad 网格导航？] | [lead-programmer + ui-designer] | [Sprint 4, Day 3] | [待定 — 取决于 ADR-0015 输入模型决策] |
| [例如，游戏物品丢弃策略 — 永久丢失还是丢到世界？] | [systems-designer] | [需 GDD 更新] | [受阻于库存 GDD 边界情况节] |
| [例如，最大库存尺寸 — 网格有硬上限还是无限滚动？] | [economy-designer] | [Sprint 3, Day 5] | [待定] |
