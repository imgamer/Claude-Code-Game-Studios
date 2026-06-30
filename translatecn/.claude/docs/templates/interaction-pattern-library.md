# Interaction Pattern Library: [Game Title]

# 交互模式库：[游戏名称]

> **Status**: Draft | Stable | Under Revision
> **Author**: [ux-designer]
> **Last Updated**: [Date]
> **Version**: [1.0]
> **Engine**: [Godot 4.6 / Unity 6 / Unreal Engine 5]
> **UI Framework**: [Godot Control nodes / Unity UI Toolkit / Unreal UMG]
> **Related Documents**:
> - `design/art/art-bible.md` — visual standards (colors, typography, iconography)
> - `docs/accessibility-requirements.md` — accessibility commitments per feature
> - `docs/ux/ux-spec-[screen].md` — individual screen specs that reference patterns

> **状态**：草稿 | 稳定 | 修订中
> **作者**：[ux-designer]
> **最后更新**：[日期]
> **版本**：[1.0]
> **引擎**：[Godot 4.6 / Unity 6 / Unreal Engine 5]
> **UI 框架**：[Godot Control 节点 / Unity UI Toolkit / Unreal UMG]
> **相关文档**：
> - `design/art/art-bible.md` — 视觉标准（颜色、字体、图标体系）
> - `docs/accessibility-requirements.md` — 每项功能的无障碍承诺
> - `docs/ux/ux-spec-[screen].md` — 引用模式的具体屏幕规格

> **Why this document exists**: Every UI screen spec should be able to say
> "uses Button (Primary) pattern" rather than re-specifying hover states,
> press animations, focus behavior, keyboard handling, and screen reader
> announcements from scratch. This library is the single source of truth for
> reusable interaction behaviors. When a screen spec references a pattern name,
> the programmer looks it up here. When the behavior changes, it changes here
> and applies everywhere.

> **本文件存在的理由**：每个 UI 屏幕规格都应该能够声明"使用 Button (Primary)
> 模式"，而不是从头重新规定悬停状态、按下动画、焦点行为、键盘处理和屏幕阅读器
> 朗读内容。本库是可复用交互行为的单一事实来源。当屏幕规格引用某个模式名称时，
> 程序员在此查阅。当行为发生变化时，仅在此处修改并应用到所有使用处。

> This is a living document. Patterns are added as new screens are designed —
> do not design a new interaction without checking here first. If a new pattern
> is needed, add it here (or propose it to the ux-designer) before writing the
> first screen spec that uses it.

> 这是一份持续演进的文件。模式随新屏幕的设计而增加——在设计新交互之前请先在此
> 查阅。若需要新模式，请在编写首个使用该模式的屏幕规格之前，将其添加到此处
> （或向 ux-designer 提议）。

> **Status definitions**:
> - **Draft**: Interaction specified but not yet implemented or validated
> - **Stable**: Implemented, tested, and validated in at least one shipped screen
> - **Deprecated**: Being phased out — existing uses will be migrated, do not use in new screens

> **状态定义**：
> - **草稿 (Draft)**：已规定交互但尚未实现或验证
> - **稳定 (Stable)**：已在至少一个交付屏幕中实现、测试并验证
> - **已弃用 (Deprecated)**：正在逐步淘汰——现有用法将被迁移，新屏幕不要使用

---

## How to Use This Library

## 如何使用本库

**If you are designing a screen**: Browse the Pattern Catalog Index below before
inventing new interactions. When a standard pattern fits, reference it by name
in the screen spec (e.g., "The confirm button uses Button (Primary) pattern").
When no existing pattern fits, propose a new one — document it here alongside
or before the screen spec that introduces it.

**如果你正在设计屏幕**：在发明新交互之前，请先浏览下方的"模式目录索引"。
当标准模式适用时，在屏幕规格中按名称引用（例如，"确认按钮使用 Button (Primary)
模式"）。当没有现有模式适用时，提出新模式——在引入该模式的屏幕规格之前或同时，
在此处记录它。

**If you are implementing a screen**: When a screen spec says "use [PatternName]
pattern," find it in this document for the complete specification. The
implementation notes section contains engine-specific guidance. The accessibility
section contains the requirements that are non-negotiable.

**如果你正在实现屏幕**：当屏幕规格说"使用 [模式名称] 模式"时，在本文件中查找完整
规格。实现说明部分包含引擎特定指导。无障碍部分包含不可协商的强制要求。

**If you are reviewing a screen spec**: Verify that all interactive elements
reference a pattern from this library or include their own full interaction
specification. "Standard button" or "the usual way" is not a valid reference.

**如果你正在评审屏幕规格**：验证所有可交互元素都引用了本库中的模式，或包含其自身
完整的交互规格。"标准按钮"或"通常方式"不是有效的引用。

**If you are updating a pattern**: Changing a Stable pattern affects every screen
that uses it. Before changing, audit all usages (search screen specs for the
pattern name), determine the impact, get approval from the ux-designer, and
update this document before or simultaneously with any implementation change.

**如果你正在更新模式**：修改稳定模式会影响使用它的所有屏幕。在修改之前，审查所有
用法（在屏幕规格中搜索该模式名称），评估影响，获得 ux-designer 批准，并在任何实现
修改之前或同时更新本文件。

---

## Pattern Catalog Index

## 模式目录索引

> Add a row here every time a new pattern is added to this document.
> The "Used In" column is the usages audit trail — update it when new screens
> adopt the pattern.

> 每次向本文件添加新模式时，在此添加一行。
> "用于 (Used In)"列是用法审计追溯——当新屏幕采用该模式时更新它。

| Pattern Name | Category | Description | Used In (Screens) | Status |
|-------------|----------|-------------|------------------|--------|
| Button (Primary) | Input | Main call-to-action. High visual weight. One per screen. | [Main Menu, Pause Menu, Settings] | Draft |
| Button (Secondary) | Input | Alternative action or cancel. Lower visual weight than Primary. | [All modal dialogs, settings screens] | Draft |
| Button (Destructive) | Input | Irreversible action. Requires confirmation before execution. | [Delete Save, Reset Settings] | Draft |
| Toggle | Input | Binary on/off state selection. | [Accessibility settings, audio settings] | Draft |
| Slider | Input | Continuous value selection. | [Volume controls, brightness, text size] | Draft |
| Dropdown / Select | Input | Selection from a discrete list of options. | [Resolution, language, key binding] | Draft |
| List Item | Layout / Input | Selectable row in a vertical scrollable list. | [Achievements, quest log, settings list] | Draft |
| Grid Item | Layout / Input | Selectable cell in a two-dimensional grid. | [Inventory, ability select, item shop] | Draft |
| Modal Dialog | Feedback / Layout | Blocking overlay requiring explicit player decision. | [Confirmation dialogs, error prompts] | Draft |
| Confirmation Dialog | Feedback / Layout | Specific modal for destructive action confirmation. | [Delete Save, Leave Match, Reset] | Draft |
| Toast / Notification | Feedback | Non-blocking temporary message in a screen corner. | [Achievement unlock, autosave notification] | Draft |
| Tooltip | Feedback | Contextual information on hover or focus. | [Inventory items, ability descriptions, settings] | Draft |
| Progress Bar | Feedback / Layout | Linear progress indicator. | [Loading screen, XP bar, quest progress] | Draft |
| Input Field | Input | Text entry control. | [Player name, search, key binding entry] | Draft |
| Tab Bar | Navigation | Tabbed section navigation within a single screen. | [Character sheet, settings, crafting] | Draft |
| Scroll Container | Layout | Scrollable content region with visible scroll indicator. | [Inventory, lore entries, credits] | Draft |
| Inventory Slot | Game-Specific | Item container in inventory grid (empty, filled, equipped, locked). | [Inventory screen, equipment screen] | Draft |
| Ability / Skill Icon | Game-Specific | Ability button with cooldown, charges, and locked states. | [HUD ability bar, skill tree] | Draft |
| Health / Resource Bar | Game-Specific | Value bar with threshold states and damage flash. | [HUD] | Draft |
| Minimap | Game-Specific | Overview map with player marker and points of interest. | [HUD] | Draft |
| Quest / Objective Tracker | Game-Specific | Active objective display with proximity and completion states. | [HUD] | Draft |
| Dialogue Box | Game-Specific | NPC conversation UI with speaker identification. | [All dialogue sequences] | Draft |
| Context Action Prompt | Game-Specific | Contextual "Press X to [action]" prompt near interactable objects. | [World interaction] | Draft |
| Damage Number | Game-Specific | Floating combat feedback number. | [Combat HUD] | Draft |
| Status Effect Icon | Game-Specific | Buff/debuff indicator with duration. | [HUD status bar, enemy health display] | Draft |
| Notification Banner | Game-Specific | Achievement, level up, item acquired notifications. | [Global overlay] | Draft |
| Screen Push | Navigation | Forward navigation with directional animation. | [All menu navigation] | Draft |
| Screen Pop (Back) | Navigation | Back navigation with reversed animation. | [All menu navigation] | Draft |
| Screen Replace | Navigation | Replace current screen without stacking history. | [Main Menu to Loading Screen] | Draft |
| Modal Open / Close | Navigation | Overlay that dims background screen. | [All modal dialogs] | Draft |
| Tab Switch | Navigation | Same-screen content switch between tabs. | [All tabbed screens] | Draft |
| Focus Management | Navigation | Rules for where focus goes when screens open, close, or change. | [All screens] | Draft |
| Escape / Cancel | Navigation | Universal back behavior across platforms and input methods. | [All screens] | Draft |
| Loading State | Feedback | How screens and components indicate loading in progress. | [All loading states] | Draft |
| Empty State | Feedback | How empty lists and grids are presented. | [Empty inventory, no quests, no saves] | Draft |
| Error State | Feedback | How errors are communicated. | [Save failed, network error, invalid input] | Draft |
| Success Confirmation | Feedback | How completed actions are confirmed. | [Settings saved, item crafted, quest turned in] | Draft |
| Optimistic UI | Feedback | Showing assumed success before system confirmation. | [If online features are present] | Draft |

| 模式名称 | 类别 | 描述 | 用于（屏幕） | 状态 |
|-------------|----------|-------------|------------------|--------|
| Button (Primary) | 输入 | 主要行动召唤。视觉权重高。每屏一个。 | [主菜单、暂停菜单、设置] | 草稿 |
| Button (Secondary) | 输入 | 替代或取消操作。视觉权重低于 Primary。 | [所有模态对话框、设置屏幕] | 草稿 |
| Button (Destructive) | 输入 | 不可逆操作。执行前需要确认。 | [删除存档、重置设置] | 草稿 |
| Toggle | 输入 | 二元开/关状态选择。 | [无障碍设置、音频设置] | 草稿 |
| Slider | 输入 | 连续值选择。 | [音量控制、亮度、文字大小] | 草稿 |
| Dropdown / Select | 输入 | 从离散选项列表中选择。 | [分辨率、语言、按键绑定] | 草稿 |
| List Item | 布局 / 输入 | 垂直可滚动列表中的可选行。 | [成就、任务日志、设置列表] | 草稿 |
| Grid Item | 布局 / 输入 | 二维网格中的可选单元格。 | [背包、技能选择、物品商店] | 草稿 |
| Modal Dialog | 反馈 / 布局 | 阻塞式覆盖层，要求玩家明确决策。 | [确认对话框、错误提示] | 草稿 |
| Confirmation Dialog | 反馈 / 布局 | 用于破坏性操作确认的特定模态。 | [删除存档、离开对局、重置] | 草稿 |
| Toast / Notification | 反馈 | 屏幕角落的非阻塞临时消息。 | [成就解锁、自动保存通知] | 草稿 |
| Tooltip | 反馈 | 悬停或聚焦时的上下文信息。 | [背包物品、技能描述、设置] | 草稿 |
| Progress Bar | 反馈 / 布局 | 线性进度指示器。 | [加载屏幕、经验条、任务进度] | 草稿 |
| Input Field | 输入 | 文本输入控件。 | [玩家名称、搜索、按键绑定录入] | 草稿 |
| Tab Bar | 导航 | 单屏内的分页区域导航。 | [角色面板、设置、制作] | 草稿 |
| Scroll Container | 布局 | 带可见滚动指示器的可滚动内容区。 | [背包、传说条目、制作人员名单] | 草稿 |
| Inventory Slot | 游戏特定 | 背包网格中的物品容器（空、已填、已装备、已锁定）。 | [背包屏幕、装备屏幕] | 草稿 |
| Ability / Skill Icon | 游戏特定 | 带冷却、充能和锁定状态的技能按钮。 | [HUD 技能栏、技能树] | 草稿 |
| Health / Resource Bar | 游戏特定 | 带阈值状态和受击闪烁的数值条。 | [HUD] | 草稿 |
| Minimap | 游戏特定 | 带玩家标记和兴趣点的概览地图。 | [HUD] | 草稿 |
| Quest / Objective Tracker | 游戏特定 | 带接近度和完成状态的当前目标显示。 | [HUD] | 草稿 |
| Dialogue Box | 游戏特定 | 带说话者识别的 NPC 对话 UI。 | [所有对话序列] | 草稿 |
| Context Action Prompt | 游戏特定 | 在可交互对象附近显示的上下文"按 X 进行 [操作]"提示。 | [世界交互] | 草稿 |
| Damage Number | 游戏特定 | 漂浮的战斗反馈数字。 | [战斗 HUD] | 草稿 |
| Status Effect Icon | 游戏特定 | 带持续时间的增益/减益指示器。 | [HUD 状态栏、敌人生命显示] | 草稿 |
| Notification Banner | 游戏特定 | 成就、升级、获得物品的通知。 | [全局覆盖层] | 草稿 |
| Screen Push | 导航 | 带方向性动画的向前导航。 | [所有菜单导航] | 草稿 |
| Screen Pop (Back) | 导航 | 反向动画的返回导航。 | [所有菜单导航] | 草稿 |
| Screen Replace | 导航 | 不堆叠历史地替换当前屏幕。 | [主菜单到加载屏幕] | 草稿 |
| Modal Open / Close | 导航 | 使背景屏幕变暗的覆盖层。 | [所有模态对话框] | 草稿 |
| Tab Switch | 导航 | 标签之间的同屏内容切换。 | [所有分页屏幕] | 草稿 |
| Focus Management | 导航 | 屏幕打开、关闭或切换时焦点的去向规则。 | [所有屏幕] | 草稿 |
| Escape / Cancel | 导航 | 跨平台和输入方法的通用返回行为。 | [所有屏幕] | 草稿 |
| Loading State | 反馈 | 屏幕和组件如何指示加载中状态。 | [所有加载状态] | 草稿 |
| Empty State | 反馈 | 空列表和空网格的呈现方式。 | [空背包、无任务、无存档] | 草稿 |
| Error State | 反馈 | 错误如何传达。 | [保存失败、网络错误、无效输入] | 草稿 |
| Success Confirmation | 反馈 | 完成操作如何确认。 | [设置已保存、物品已制作、任务已交付] | 草稿 |
| Optimistic UI | 反馈 | 在系统确认前显示假设的成功。 | [如果存在在线功能] | 草稿 |

---

## Standard Control Patterns

## 标准控件模式

---

#### Button (Primary)

#### Button (Primary)

**Category**: Input
**Status**: Draft
**When to Use**: The single most important action on a screen. "Start Game,"
"Confirm," "Accept," "Buy." There should be at most one Primary button visible
at a time. It is the answer to "what does the player most likely want to do here?"
**When NOT to Use**: Alternative or secondary actions; destructive actions that
require confirmation before the consequence is irreversible; any action that is
not the primary intent of the screen.

**类别**：输入
**状态**：草稿
**何时使用**：屏幕上最重要的单一操作。"开始游戏"、"确认"、"接受"、"购买"。
同时最多可见一个 Primary 按钮。它是"玩家在此最可能想做什么？"这一问题的答案。
**何时不要使用**：替代或次要操作；在后果不可逆前需要确认的破坏性操作；任何
不属于屏幕主要意图的操作。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Default | Full-opacity fill, primary color from art-bible. Label centered. | — | — | — | — |
| Hovered (mouse) | Brightness +15%, subtle scale 1.03x, cursor changes to pointer | Mouse over element | Transition from Default | 80ms ease-out | [UI hover sound — see Sound Standards] |
| Focused (keyboard/gamepad) | Focus ring visible (2px, offset 3px, high contrast color). Same brightness as Hovered. | Tab / D-pad navigation | Transition from Default | 80ms ease-out | [UI focus sound — same as hover] |
| Pressed | Scale 0.97x, brightness -10% | Click / Enter / A (Xbox) / Cross (PS) | Action fires on press-up, not press-down. Scale on press-down. | 60ms ease-in for press; 80ms ease-out on release | [UI confirm sound] |
| Disabled | 40% opacity, no pointer cursor, no hover state | — | No response | — | — |
| Loading (post-press) | Replace label with spinner. Button remains at pressed scale, disabled state. | — | Prevents double-submission | Duration of async operation | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 默认 | 不透明填充，使用美术指南中的主色。标签居中。 | — | — | — | — |
| 悬停（鼠标） | 亮度 +15%，轻微缩放 1.03x，光标变为手型 | 鼠标悬停于元素 | 从默认过渡 | 80ms ease-out | [UI 悬停音——见声音标准] |
| 聚焦（键盘/手柄） | 焦点环可见（2px，偏移 3px，高对比色）。亮度同悬停。 | Tab / 方向键导航 | 从默认过渡 | 80ms ease-out | [UI 聚焦音——同悬停] |
| 按下 | 缩放 0.97x，亮度 -10% | 点击 / Enter / A (Xbox) / Cross (PS) | 操作在按键释放时触发，不在按下时触发。按下时缩放。 | 按下 60ms ease-in；释放 80ms ease-out | [UI 确认音] |
| 禁用 | 40% 不透明度，无手型光标，无悬停状态 | — | 无响应 | — | — |
| 加载中（按下后） | 用转圈替换标签。按钮保持按下缩放，禁用状态。 | — | 防止重复提交 | 异步操作持续时间 | — |

**Accessibility**:
- Keyboard: Tab to focus, Enter or Space to activate. Must be reachable from any other interactive element on screen via Tab sequence.
- Gamepad: D-pad or left stick to navigate focus to button. A (Xbox) / Cross (PS) to activate. Focus must be placed on Primary button by default when screen opens.
- Screen reader: Button must expose accessible name matching visible label. Role: "button." State: "dimmed" when disabled. Activation announcement: "[Label] button — [result of action, if known]."
- Colorblind: Do not rely on color alone to distinguish Primary from Secondary. Primary uses higher visual weight (fill vs. outline, or larger size) in addition to color differentiation.
- Minimum touch target: 44x44pt (iOS HIG) / 48x48dp (Android). Apply even on PC if touch support is possible.

**无障碍**：
- 键盘：Tab 聚焦，Enter 或 Space 激活。必须能通过 Tab 序列从屏幕上任何其他可交互元素到达。
- 手柄：方向键或左摇杆将焦点导航到按钮。A (Xbox) / Cross (PS) 激活。屏幕打开时默认焦点必须放在 Primary 按钮上。
- 屏幕阅读器：按钮必须公开与可见标签匹配的可访问名称。角色："button"。禁用时状态："dimmed"。激活朗读："[标签] 按钮——[操作结果（如已知）]。"
- 色盲：不要仅依靠颜色区分 Primary 与 Secondary。Primary 在颜色之外还使用更高的视觉权重（填充与描边对比，或更大尺寸）。
- 最小触摸目标：44x44pt (iOS HIG) / 48x48dp (Android)。即使 PC 上可能支持触摸也应应用。

**Implementation Notes**:
[Godot: Extend `Button` control. Override `_draw()` for custom states rather than
modifying themes mid-state. Use `focus_mode = FOCUS_ALL` to ensure keyboard
focusability. Set `mouse_default_cursor_shape = CURSOR_POINTING_HAND`. For the
scale animation, use a Tween on the `scale` property of the button's parent
Control — scaling the Button itself can clip children.]

**实现说明**：
[Godot：扩展 `Button` 控件。重写 `_draw()` 处理自定义状态，而非在状态中修改主题。
使用 `focus_mode = FOCUS_ALL` 确保键盘可聚焦。设置
`mouse_default_cursor_shape = CURSOR_POINTING_HAND`。对于缩放动画，在按钮父
Control 的 `scale` 属性上使用 Tween——直接缩放 Button 本身可能会裁剪子节点。]

---

#### Button (Secondary)

#### Button (Secondary)

**Category**: Input
**Status**: Draft
**When to Use**: Alternative or cancel action. "Back," "Cancel," "Skip," "Maybe
Later." Lower visual weight than Primary — it should recede visually, not compete.
**When NOT to Use**: Destructive actions (use Button (Destructive)). The most
important action on the screen (use Button (Primary)).

**类别**：输入
**状态**：草稿
**何时使用**：替代或取消操作。"返回"、"取消"、"跳过"、"以后再说"。视觉权重低于
Primary——它应该视觉退后，而非竞争。
**何时不要使用**：破坏性操作（使用 Button (Destructive)）。屏幕上最重要的操作
（使用 Button (Primary)）。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Default | Outlined style (border only, transparent fill), secondary color. Slightly smaller or lower weight than Primary. | — | — | — | — |
| Hovered | Background fill appears at 15% opacity. Border brightens. Scale 1.02x. | Mouse over | Transition from Default | 80ms ease-out | [UI hover sound — softer variant than Primary] |
| Focused | Focus ring, same specification as Primary. | Tab / D-pad | Transition from Default | 80ms ease-out | [UI focus sound] |
| Pressed | Scale 0.97x, fill opacity increases to 30% | Click / Enter / B (Xbox) / Circle (PS) on focused state | Action fires on press-up | 60ms ease-in | [UI cancel/back sound] |
| Disabled | 40% opacity | — | No response | — | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 默认 | 描边样式（仅边框，透明填充），次色。比 Primary 略小或权重略低。 | — | — | — | — |
| 悬停 | 背景填充以 15% 不透明度出现。边框变亮。缩放 1.02x。 | 鼠标悬停 | 从默认过渡 | 80ms ease-out | [UI 悬停音——比 Primary 更柔和的变体] |
| 聚焦 | 焦点环，规格同 Primary。 | Tab / 方向键 | 从默认过渡 | 80ms ease-out | [UI 聚焦音] |
| 按下 | 缩放 0.97x，填充不透明度增加到 30% | 点击 / Enter / B (Xbox) / Circle (PS) 在聚焦状态 | 操作在按键释放时触发 | 60ms ease-in | [UI 取消/返回音] |
| 禁用 | 40% 不透明度 | — | 无响应 | — | — |

**Accessibility**: Same requirements as Button (Primary). Accessible name must
match visible label. In a dialog with Primary and Secondary buttons, the Secondary
button typically maps to the platform "cancel" input (B / Circle / Escape) as well
as direct focus activation.

**无障碍**：与 Button (Primary) 要求相同。可访问名称必须与可见标签匹配。在含
Primary 与 Secondary 按钮的对话框中，Secondary 按钮通常映射到平台"取消"输入
（B / Circle / Escape），以及直接聚焦激活。

**Implementation Notes**: [Same as Button (Primary). Where a Primary and Secondary
appear together, ensure Secondary is always positioned consistently — right/bottom
of Primary on horizontal layouts, or below Primary on vertical layouts. Consistency
across screens is more important than per-screen aesthetic preference.]

**实现说明**：[同 Button (Primary)。Primary 与 Secondary 共同出现时，确保 Secondary
位置一致——水平布局中位于 Primary 右/下方，垂直布局中位于 Primary 下方。跨屏幕
的一致性比单屏美学偏好更重要。]

---

#### Button (Destructive)

#### Button (Destructive)

**Category**: Input
**Status**: Draft
**When to Use**: Any action that is irreversible and causes loss of player data or
significant progress: "Delete Save File," "Reset All Settings," "Leave Match,"
"Discard Changes." The visual treatment signals danger before the player presses.
**When NOT to Use**: Actions that can be undone, or actions that are merely
consequential but reversible.

**类别**：输入
**状态**：草稿
**何时使用**：任何不可逆且造成玩家数据或显著进度损失的操作："删除存档文件"、
"重置所有设置"、"离开对局"、"放弃更改"。视觉处理在玩家按下之前就警示危险。
**何时不要使用**：可撤销的操作，或仅是有后果但可逆的操作。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Default | Outlined or filled with destructive color (typically a desaturated red — confirm colorblind compatibility in accessibility-requirements). Label may include a warning icon. | — | — | — | — |
| Hovered / Focused | Same behavior as Button (Primary) hover/focus but with destructive color | — | — | 80ms | [UI hover sound] |
| Pressed (first press) | Does NOT execute the action. Instead, opens Confirmation Dialog pattern (see below). The button itself shows a brief pulse animation. | Click / Enter | Trigger Confirmation Dialog | 100ms pulse | [UI warning sound — distinct from standard confirm] |
| — | Confirmation Dialog handles the actual execution | — | — | — | — |
| Disabled | 40% opacity | — | No response | — | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 默认 | 描边或填充破坏性色（通常是去饱和红色——在 accessibility-requirements 中确认色盲兼容性）。标签可能包含警告图标。 | — | — | — | — |
| 悬停 / 聚焦 | 同 Button (Primary) 悬停/聚焦行为，但使用破坏性色 | — | — | 80ms | [UI 悬停音] |
| 按下（首次按下） | 不执行操作。改为打开 Confirmation Dialog 模式（见下）。按钮本身显示短暂的脉动动画。 | 点击 / Enter | 触发 Confirmation Dialog | 100ms 脉动 | [UI 警告音——与标准确认音区分] |
| — | Confirmation Dialog 处理实际执行 | — | — | — | — |
| 禁用 | 40% 不透明度 | — | 无响应 | — | — |

> **Critical rule**: A Button (Destructive) NEVER executes its action directly.
> It always triggers a Confirmation Dialog. There are no exceptions. A player
> who presses it by accident must always have one more opportunity to back out.
> Games that skip confirmation on destructive actions generate the most visible
> negative community sentiment of any UX failure type. See: every "accidentally
> deleted save file" complaint on any game forum.

> **关键规则**：Button (Destructive) 永不直接执行操作。
> 它总是触发 Confirmation Dialog。无例外。意外按下它的玩家必须始终有再一次退出
> 的机会。跳过破坏性操作确认的游戏会引发所有 UX 失败类型中最显性的负面社区情绪。
> 参见：任何游戏论坛上每一例"误删存档文件"投诉。

**Accessibility**: Screen reader must announce the destructive nature: "[Label] button — this action cannot be undone." In addition to accessible name, use the `description` property if available to add the warning text.

**无障碍**：屏幕阅读器必须朗读破坏性："[标签] 按钮——此操作无法撤销。"除可访问
名称外，若可用，使用 `description` 属性添加警告文本。

**Implementation Notes**: [Destructive button triggers a separate Confirmation Dialog scene. Pass the action callback to the dialog — the button itself does not hold the execution logic. This separation prevents accidental execution if the confirmation dialog has a bug.]

**实现说明**：[破坏性按钮触发独立的 Confirmation Dialog 场景。将操作回调传递给对话
框——按钮本身不持有执行逻辑。这种分离防止确认对话框存在 bug 时意外执行。]

---

#### Toggle

#### Toggle

**Category**: Input
**Status**: Draft
**When to Use**: Binary on/off settings where both states are equally valid and
the current state must be visible at a glance. "Subtitles: On/Off," "Aim Assist:
On/Off," "Notifications: On/Off."
**When NOT to Use**: Selections from more than two options (use Dropdown). Actions
that happen once rather than representing a persistent state (use Button). Cases
where the consequence of toggling is complex enough to need explanation (show
a description field alongside).

**类别**：输入
**状态**：草稿
**何时使用**：二元开/关设置，其中两种状态都同样有效，且当前状态必须一眼可见。
"字幕：开/关"、"瞄准辅助：开/关"、"通知：开/关"。
**何时不要使用**：从两个以上选项中选择（使用 Dropdown）。发生一次而非表示持久状态
的操作（使用 Button）。切换后果复杂到需要解释的情况（在旁边显示描述字段）。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Off / Default | Track: muted fill. Thumb: leftmost position. Label: "Off" or state label. | — | — | — | — |
| Hovered | Track brightens 10%. Cursor: pointer. | Mouse over | Transition | 60ms | [UI hover sound] |
| Focused | Focus ring around entire toggle element (track + thumb). | Tab / D-pad | — | 60ms | [UI focus sound] |
| Pressed / Activated | Thumb slides to right side. Track fill changes to active color. Label changes to "On" or active state label. State persists. | Click / Enter / A / Cross | Toggle state change. Fire onChange event. Persist value. | 150ms ease-in-out for slide | [Toggle ON sound] |
| Pressed / Deactivated | Thumb slides to left. Track reverts to muted fill. | Same inputs | Toggle state change | 150ms ease-in-out | [Toggle OFF sound — subtly different from ON] |
| Disabled | 40% opacity. No interaction. Current state still visible. | — | No response | — | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 关 / 默认 | 轨道：柔和填充。滑块：最左位置。标签："关"或状态标签。 | — | — | — | — |
| 悬停 | 轨道变亮 10%。光标：手型。 | 鼠标悬停 | 过渡 | 60ms | [UI 悬停音] |
| 聚焦 | 整个 toggle 元素（轨道 + 滑块）周围的焦点环。 | Tab / 方向键 | — | 60ms | [UI 聚焦音] |
| 按下 / 激活 | 滑块滑到右侧。轨道填充变为激活色。标签变为"开"或激活状态标签。状态持久。 | 点击 / Enter / A / Cross | 切换状态。触发 onChange 事件。持久化值。 | 滑动 150ms ease-in-out | [Toggle 开启音] |
| 按下 / 取消激活 | 滑块滑到左侧。轨道恢复柔和填充。 | 同上输入 | 切换状态 | 150ms ease-in-out | [Toggle 关闭音——与开启音略有不同] |
| 禁用 | 40% 不透明度。无交互。当前状态仍可见。 | — | 无响应 | — | — |

**Accessibility**:
- Keyboard/Gamepad: Space or Enter to toggle. Avoid requiring directional inputs (left/right) to toggle — some users cannot predict that behavior.
- Screen reader: Role: "switch." State: "on" or "off" — the accessible name should NOT include the state (the screen reader announces state separately). Correct: accessible name "Subtitles," state "on." Incorrect: accessible name "Subtitles On."
- The toggle label (not just the visual thumb position) must change to show current state for players who cannot reliably distinguish left from right positions.

**无障碍**：
- 键盘/手柄：Space 或 Enter 切换。避免要求方向输入（左/右）来切换——某些用户无法
预测该行为。
- 屏幕阅读器：角色："switch"。状态："on"或"off"——可访问名称不应包含状态（屏幕
阅读器单独朗读状态）。正确：可访问名称"字幕"，状态"on"。错误：可访问名称"字幕开"。
- toggle 标签（不仅是视觉滑块位置）必须改变以显示当前状态，供无法可靠区分左右位置
的玩家使用。

**Implementation Notes**: [Godot: Use a custom Control or a CheckButton. The
built-in CheckButton provides accessibility role but uses a checkbox-style visual;
a custom slide-toggle animation may be needed for the target art style. Ensure
the slide animation is skipped when motion reduction mode is active — in that
case, snap to final state instantly.]

**实现说明**：[Godot：使用自定义 Control 或 CheckButton。内建 CheckButton 提供无障碍
角色但使用复选框式视觉；目标美术风格可能需要自定义滑动 toggle 动画。确保在
减少动效模式激活时跳过滑动动画——此时瞬间跳到最终状态。]

---

#### Slider

#### Slider

**Category**: Input
**Status**: Draft
**When to Use**: Selecting a value from a continuous range where approximate values
are acceptable and the range and relative position matter. Volume (0–100%), brightness,
text size. The visual representation of position is itself useful information.
**When NOT to Use**: Precise value entry (use Input Field). Selection from a short
discrete list (use Dropdown). Binary state (use Toggle).

**类别**：输入
**状态**：草稿
**何时使用**：从连续范围中选择值，近似值可接受，且范围和相对位置重要。音量
（0–100%）、亮度、文字大小。位置的视觉表示本身就是有用的信息。
**何时不要使用**：精确值输入（使用 Input Field）。从短的离散列表中选择（使用
Dropdown）。二元状态（使用 Toggle）。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Default | Track (full width). Fill (left of thumb, shows current value). Thumb (draggable handle). Current value label (right of track or above thumb). | — | — | — | — |
| Hovered | Thumb enlarges slightly (1.2x). Track brightens. | Mouse over | — | 60ms | — |
| Focused | Focus ring on thumb. Track brightens. | Tab / D-pad | — | 60ms | [UI focus sound] |
| Dragging (mouse) | Thumb follows cursor. Fill updates in real time. Value label updates in real time. | Click + drag on thumb | Continuous value update. Fire onChange continuously. | Real time | [Slider adjust sound — subtle, loops while dragging] |
| Keyboard / D-pad adjust | Thumb moves one step (5% of range per press, or 1 discrete unit). | Left/Right arrows or Left/Right D-pad while focused | Step value change. Fire onChange per step. | Instant | [Slider step sound — one click per step] |
| Keyboard fast adjust | Larger step (25% of range). | Page Up / Page Down while focused | Large step value change | Instant | [Same step sound] |
| Released | Value locks. onChange fires final value. | Mouse release | — | — | — |
| Disabled | 40% opacity. No interaction. Value visible. | — | No response | — | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 默认 | 轨道（全宽）。填充（滑块左侧，显示当前值）。滑块（可拖动把手）。当前值标签（轨道右侧或滑块上方）。 | — | — | — | — |
| 悬停 | 滑块略微放大（1.2x）。轨道变亮。 | 鼠标悬停 | — | 60ms | — |
| 聚焦 | 滑块上的焦点环。轨道变亮。 | Tab / 方向键 | — | 60ms | [UI 聚焦音] |
| 拖动（鼠标） | 滑块跟随光标。填充实时更新。值标签实时更新。 | 在滑块上点击 + 拖动 | 连续值更新。持续触发 onChange。 | 实时 | [Slider 调节音——细微，拖动时循环] |
| 键盘 / 方向键调节 | 滑块移动一步（每次按下移动范围的 5%，或 1 个离散单位）。 | 聚焦时左/右箭头或左/右方向键 | 步进值变化。每步触发 onChange。 | 即时 | [Slider 步进音——每步一声] |
| 键盘快速调节 | 更大步进（范围的 25%）。 | 聚焦时 Page Up / Page Down | 大步进值变化 | 即时 | [相同步进音] |
| 释放 | 值锁定。onChange 触发最终值。 | 鼠标释放 | — | — | — |
| 禁用 | 40% 不透明度。无交互。值可见。 | — | 无响应 | — | — |

**Accessibility**:
- Keyboard: Left/Right arrows to adjust by small step. Page Up/Page Down for large step. Home/End to jump to min/max.
- Screen reader: Role: "slider." Accessible name: the label (e.g., "Music Volume"). Current value announced on every change: "Music Volume, 80 percent." Min/max values announced on first focus.
- All sliders must show a numeric value alongside the visual position. Relying only on track fill position excludes players who cannot perceive relative position.

**无障碍**：
- 键盘：左/右箭头小步调节。Page Up/Page Down 大步调节。Home/End 跳到最小/最大。
- 屏幕阅读器：角色："slider"。可访问名称：标签（如"音乐音量"）。每次变化时朗读当前值：
"音乐音量，80%"。首次聚焦时朗读最小/最大值。
- 所有 slider 必须在视觉位置旁显示数值。仅依靠轨道填充位置会排除无法感知相对位置的
玩家。

**Implementation Notes**: [Godot `HSlider`: set `step` to appropriate increment.
Override keyboard input to add Page Up/Down support via `_input()`. Bind the
`value_changed` signal to update the displayed numeric label. When motion reduction
mode is enabled, ensure value label updates are the sole feedback — do not suppress
them. Rumble feedback on gamepad slider adjustment is a nice enhancement for
accessibility.]

**实现说明**：[Godot `HSlider`：将 `step` 设置为合适的增量。通过 `_input()` 重写
键盘输入以支持 Page Up/Down。绑定 `value_changed` 信号以更新显示的数值标签。当
减少动效模式启用时，确保值标签更新是唯一反馈——不要抑制它们。手柄 slider 调节时
的震动反馈是无障碍的不错增强。]

---

#### Dropdown / Select

#### Dropdown / Select

**Category**: Input
**Status**: Draft
**When to Use**: Selection from a discrete list of 3-15 options where only the
selected value needs to be visible at rest. Display resolution, language, window
mode, input preset. The closed state shows only the current selection.
**When NOT to Use**: Binary choices (use Toggle). More than ~15 options (use a
full List pattern or a scrollable Select). When comparing options matters as much
as selecting one (show options visibly, e.g., as a horizontal selector or list).

**类别**：输入
**状态**：草稿
**何时使用**：从 3-15 个选项的离散列表中选择，且只有选中值需要在静止时可见。显示
分辨率、语言、窗口模式、输入预设。关闭状态只显示当前选择。
**何时不要使用**：二元选择（使用 Toggle）。超过约 15 个选项（使用完整 List 模式或
可滚动 Select）。当比较选项与选择本身同等重要时（可见地显示选项，例如作为水平
选择器或列表）。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Closed / Default | Label (left). Current value (right). Chevron-down icon (far right). | — | — | — | — |
| Hovered | Row background fills at 10% opacity | Mouse over | — | 60ms | — |
| Focused (closed) | Focus ring on entire row. | Tab / D-pad | — | 60ms | [UI focus sound] |
| Opening | Dropdown list appears below (or above if near screen bottom). List items visible. Previously selected item highlighted. Focus moves to selected item inside list. | Click / Enter / A / Cross | Open list | 100ms ease-out (expand) | [UI expand sound] |
| List item hovered/focused | List item highlights | Mouse / D-pad | — | 60ms | [UI hover sound] |
| List item selected | List closes. Closed state shows new value. onChange event fires. | Click / Enter / A / Cross on item | Select value, close list | 80ms ease-in (collapse) | [UI confirm sound] |
| Dismissed without selecting | List closes. Value unchanged. | Escape / B / Circle / click outside | Dismiss | 80ms | [UI cancel sound] |
| Disabled | 40% opacity. No interaction. | — | — | — | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 关闭 / 默认 | 标签（左）。当前值（右）。下箭头图标（最右）。 | — | — | — | — |
| 悬停 | 行背景以 10% 不透明度填充 | 鼠标悬停 | — | 60ms | — |
| 聚焦（关闭） | 整行的焦点环。 | Tab / 方向键 | — | 60ms | [UI 聚焦音] |
| 打开 | 下拉列表出现在下方（或在接近屏幕底部时出现在上方）。列表项可见。先前选中项高亮。焦点移到列表内选中项。 | 点击 / Enter / A / Cross | 打开列表 | 100ms ease-out（展开） | [UI 展开音] |
| 列表项悬停/聚焦 | 列表项高亮 | 鼠标 / 方向键 | — | 60ms | [UI 悬停音] |
| 列表项选中 | 列表关闭。关闭状态显示新值。触发 onChange 事件。 | 在项上点击 / Enter / A / Cross | 选择值，关闭列表 | 80ms ease-in（折叠） | [UI 确认音] |
| 未选中而关闭 | 列表关闭。值不变。 | Escape / B / Circle / 点击外部 | 关闭 | 80ms | [UI 取消音] |
| 禁用 | 40% 不透明度。无交互。 | — | — | — | — |

**Accessibility**:
- Keyboard: Up/Down arrows navigate list items while open. Enter selects. Escape dismisses. First letter of an option jumps focus to first matching item.
- Screen reader: Role: "combobox." Accessible name: the field label. Expanded/collapsed state announced. Current value announced when focused. Each list item announces its value and position: "English, 1 of 12."
- The dropdown list must never obscure the current item or the control that opened it — this is a common failure on small screens.

**无障碍**：
- 键盘：打开时上/下箭头在列表项间导航。Enter 选择。Escape 关闭。选项首字母将焦点
跳到第一个匹配项。
- 屏幕阅读器：角色："combobox"。可访问名称：字段标签。朗读展开/折叠状态。聚焦时
朗读当前值。每个列表项朗读其值和位置："英语，第 1 项，共 12 项"。
- 下拉列表绝不能遮挡当前项或打开它的控件——这是小屏幕上的常见失败。

**Implementation Notes**: [Godot: Custom implementation using a `Button` (the
closed state) and a `PopupMenu` or a `VBoxContainer` revealed by animation. Native
`OptionButton` provides accessibility but limited visual customization. Ensure
the popup positions itself above the control if it would be clipped by the screen
bottom. Close the popup on `_input` detecting click outside its rect.]

**实现说明**：[Godot：使用 `Button`（关闭状态）和 `PopupMenu` 或由动画显示的
`VBoxContainer` 自定义实现。原生 `OptionButton` 提供无障碍但视觉自定义有限。确保
弹窗在被屏幕底部裁剪时定位到控件上方。在 `_input` 中检测其矩形外点击以关闭弹窗。]

---

#### List Item

#### List Item

**Category**: Layout / Input
**Status**: Draft
**When to Use**: A single selectable row in a vertically scrollable list. Achievements,
quest log entries, settings categories, save file slots. The list is the container;
this is the row within it.
**When NOT to Use**: Grid layouts where items exist in two dimensions (use Grid Item).
Non-selectable content rows (remove hover/focus states and the pressed state).

**类别**：布局 / 输入
**状态**：草稿
**何时使用**：垂直可滚动列表中的单个可选行。成就、任务日志条目、设置类别、存档槽。
列表是容器；这是其中的行。
**何时不要使用**：物品存在于二维的网格布局（使用 Grid Item）。不可选的内容行（移除
悬停/聚焦状态和按下状态）。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Default | Full-width row. Icon (optional, left). Primary label. Secondary label / metadata (right or below primary). Chevron (right, if navigates deeper). | — | — | — | — |
| Hovered | Row background at 12% opacity highlight. | Mouse over | — | 60ms | — |
| Focused | Focus ring on row OR row background at 20% opacity (consistent with platform convention). | D-pad / Tab | — | 60ms | [UI focus sound] |
| Selected (persistent) | Row background at 25% opacity. May show a selection indicator (left border, checkmark). Distinct from focused state — a row can be selected but not focused. | — | Rendered state | — | — |
| Pressed / Activated | Brief brightness flash, then navigates or performs action | Click / Enter / A / Cross | Navigation or action | 80ms flash | [UI confirm sound] |
| Disabled | 40% opacity. No interaction. | — | — | — | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 默认 | 全宽行。图标（可选，左）。主标签。次标签/元数据（右或主标签下方）。箭头（右，若向更深层导航）。 | — | — | — | — |
| 悬停 | 行背景以 12% 不透明度高亮。 | 鼠标悬停 | — | 60ms | — |
| 聚焦 | 行上的焦点环或行背景以 20% 不透明度（与平台约定一致）。 | 方向键 / Tab | — | 60ms | [UI 聚焦音] |
| 选中（持久） | 行背景以 25% 不透明度。可能显示选择指示器（左边框、对勾）。与聚焦状态不同——一行可能被选中但未聚焦。 | — | 渲染状态 | — | — |
| 按下 / 激活 | 短暂亮度闪烁，然后导航或执行操作 | 点击 / Enter / A / Cross | 导航或操作 | 80ms 闪烁 | [UI 确认音] |
| 禁用 | 40% 不透明度。无交互。 | — | — | — | — |

**Accessibility**:
- Keyboard/Gamepad: Up/Down arrows or D-pad to move between list items. The list must handle focus cycling — reaching the bottom should stop (not wrap) unless wrapping is explicitly designed.
- Screen reader: Role: "listitem." Parent list role: "list." Accessible name: primary label content. Metadata (secondary label) is optionally included in the description. Position announced: "Quest Log, 3 of 12."
- Minimum row height: 44pt / 48dp for touch. For controller-primary platforms, 56px rows are more comfortable.

**无障碍**：
- 键盘/手柄：上/下箭头或方向键在列表项间移动。列表必须处理焦点循环——到达底部应
停止（而非环绕），除非显式设计了环绕。
- 屏幕阅读器：角色："listitem"。父列表角色："list"。可访问名称：主标签内容。
元数据（次标签）可选包含在描述中。朗读位置："任务日志，第 3 项，共 12 项"。
- 最小行高：触摸用 44pt / 48dp。手柄优先平台上，56px 行更舒适。

**Implementation Notes**: [Godot: Use a `VBoxContainer` inside a `ScrollContainer`.
Each row is a custom `Control` or `PanelContainer` with a `_gui_input` override.
For keyboard navigation inside the scroll container, implement custom focus
traversal — Godot's default Tab navigation does not scroll the container to keep
focused items in view. Use `ensure_control_visible()` on the scroll container.]

**实现说明**：[Godot：在 `ScrollContainer` 内使用 `VBoxContainer`。每行是带
`_gui_input` 重写的自定义 `Control` 或 `PanelContainer`。对于滚动容器内的键盘
导航，实现自定义焦点遍历——Godot 默认 Tab 导航不会滚动容器以保持聚焦项可见。在
滚动容器上使用 `ensure_control_visible()`。]

---

#### Grid Item

#### Grid Item

**Category**: Layout / Input
**Status**: Draft
**When to Use**: A selectable cell in a two-dimensional grid. Inventory slots,
ability select, crafting ingredient selection, character portrait selection. The
grid is the container; this is the cell.
**When NOT to Use**: Single-column content (use List Item). Non-selectable display
cells (remove interactive states).

**类别**：布局 / 输入
**状态**：草稿
**何时使用**：二维网格中的可选单元格。背包槽、技能选择、制作材料选择、角色头像
选择。网格是容器；这是单元格。
**何时不要使用**：单列内容（使用 List Item）。不可选的显示单元格（移除交互状态）。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Empty | Empty slot visual (subtle border or dashed outline). Different from disabled. | — | — | — | — |
| Populated | Item icon fills cell. Stack count (bottom right, if applicable). Quality indicator (border color or icon overlay). | — | — | — | — |
| Hovered | Brightness +15%. Tooltip appears after 400ms delay. | Mouse over | — | 60ms | — |
| Focused | Focus ring (2px, offset 2px). Same brightness as hovered. Tooltip appears after 400ms delay or immediately on gamepad. | D-pad navigation | — | 60ms | [UI focus sound] |
| Selected (persistent) | Distinct border (thicker, contrasting color). May show selection checkmark. | Click / Enter / A / Cross | Select item. Can coexist with focused state on a different cell. | Instant | [UI select sound] |
| Pressed | Brief scale 0.95x, then executes action | Double-click / Enter / A / Cross | Action (equip, use, inspect — defined by context) | 80ms | [UI confirm sound] |
| Locked | Padlock overlay icon on populated content. No hover/focus states. | — | No interaction | — | — |
| Drag source | Cell dims (50% opacity), drag preview appears at cursor. | Click + drag (mouse only) | Begin drag operation | Instant | [UI grab sound] |
| Drop target (valid) | Cell brightens, accepting color indicator | Item dragged over | — | 60ms | — |
| Drop target (invalid) | Red tint or shake animation | Item dragged over invalid slot | — | 60ms | [UI error sound] |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 空 | 空槽视觉（细微边框或虚线轮廓）。与禁用不同。 | — | — | — | — |
| 已填 | 物品图标填满单元格。堆叠计数（右下，若适用）。品质指示器（边框色或图标覆盖）。 | — | — | — | — |
| 悬停 | 亮度 +15%。延迟 400ms 后出现 Tooltip。 | 鼠标悬停 | — | 60ms | — |
| 聚焦 | 焦点环（2px，偏移 2px）。亮度同悬停。延迟 400ms 后或手柄上立即出现 Tooltip。 | 方向键导航 | — | 60ms | [UI 聚焦音] |
| 选中（持久） | 独特边框（更粗，对比色）。可能显示选中对勾。 | 点击 / Enter / A / Cross | 选择物品。可与不同单元格的聚焦状态共存。 | 即时 | [UI 选择音] |
| 按下 | 短暂缩放 0.95x，然后执行操作 | 双击 / Enter / A / Cross | 操作（装备、使用、检视——由上下文定义） | 80ms | [UI 确认音] |
| 锁定 | 已填内容上的挂锁覆盖图标。无悬停/聚焦状态。 | — | 无交互 | — | — |
| 拖动源 | 单元格变暗（50% 不透明度），拖动预览出现在光标处。 | 点击 + 拖动（仅鼠标） | 开始拖动操作 | 即时 | [UI 抓取音] |
| 放置目标（有效） | 单元格变亮，接受色指示器 | 物品拖过 | — | 60ms | — |
| 放置目标（无效） | 红色调或摇晃动画 | 物品拖过无效槽 | — | 60ms | [UI 错误音] |

**Accessibility**:
- Keyboard/Gamepad: D-pad or arrow keys navigate cells. The grid must communicate its dimensions to screen readers. Row/column position announced.
- Screen reader: Role: "gridcell." Parent role: "grid." Accessible name: item name (or "empty slot" for empty cells). State: "selected" when selected, "dimmed" when locked. Position: "row 2, column 3."
- Tooltips must be reachable by keyboard — they must appear when the cell is focused, not only when hovered.

**无障碍**：
- 键盘/手柄：方向键或箭头键导航单元格。网格必须将其维度传达给屏幕阅读器。朗读
行/列位置。
- 屏幕阅读器：角色："gridcell"。父角色："grid"。可访问名称：物品名（或空单元格的
"空槽"）。状态：选中时"selected"，锁定时"dimmed"。位置："行 2，列 3"。
- Tooltip 必须可通过键盘访问——它们必须在单元格聚焦时出现，而非仅在悬停时。

**Implementation Notes**: [Godot: `GridContainer` with fixed column count. Each
cell is a custom `Control`. Implement custom D-pad navigation by overriding
`_gui_input` and calculating the cell to the left/right/above/below based on
index and column count. `GridContainer` does not provide this natively.]

**实现说明**：[Godot：`GridContainer` 带固定列数。每个单元格是自定义 `Control`。
通过重写 `_gui_input` 并基于索引和列数计算左/右/上/下单元格，实现自定义方向键
导航。`GridContainer` 原生不提供此功能。]

---

#### Modal Dialog

#### Modal Dialog

**Category**: Feedback / Layout
**Status**: Draft
**When to Use**: A decision or acknowledgment that must be resolved before the
player can continue. The dialog is blocking — background content is dimmed and
non-interactive. "Are you sure?", "Your progress will be saved.", error states.
**When NOT to Use**: Non-blocking notifications (use Toast / Notification). Information
that can wait until the player is ready (add it to a persistent help system instead).
Dialogs that should allow the player to continue playing behind them.

**类别**：反馈 / 布局
**状态**：草稿
**何时使用**：必须在玩家继续前解决的决策或确认。对话框是阻塞的——背景内容变暗且
不可交互。"你确定吗？"、"你的进度将被保存。"、错误状态。
**何时不要使用**：非阻塞通知（使用 Toast / Notification）。可以等到玩家准备好的
信息（改为添加到持久帮助系统）。应允许玩家在后面继续游戏的对话框。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Opening | Background overlay animates from 0 to 60% opacity. Dialog panel scales from 0.9 to 1.0. Dialog enters from center (not from an edge). | Triggered by code | Focus moves to first interactive element in dialog (or the Primary button) | 200ms ease-out | [UI modal open sound] |
| Active | Background non-interactive. Dialog has all input focus. Player cannot interact with background. | Keyboard / gamepad navigates within dialog only | — | — | — |
| Dismissing (confirmed) | Dialog panel scales to 1.1 then fades. Overlay fades to 0%. | Primary button pressed | Execute action, return focus to trigger element | 180ms | [UI confirm sound] |
| Dismissing (cancelled) | Dialog panel scales to 0.9 then fades. Overlay fades to 0%. | Secondary button / Escape / B / Circle | No action, return focus to trigger element | 150ms | [UI cancel sound] |
| Cannot dismiss | If the dialog represents a blocking error, do not provide a cancel path. Provide only resolution options. | — | — | — | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 打开 | 背景覆盖层从 0 动画到 60% 不透明度。对话框面板从 0.9 缩放到 1.0。对话框从中心进入（非从边缘）。 | 由代码触发 | 焦点移到对话框中第一个可交互元素（或 Primary 按钮） | 200ms ease-out | [UI 模态打开音] |
| 活动 | 背景不可交互。对话框持有所有输入焦点。玩家无法与背景交互。 | 键盘 / 手柄仅在对话框内导航 | — | — | — |
| 关闭（确认） | 对话框面板缩放到 1.1 然后淡出。覆盖层淡到 0%。 | 按下 Primary 按钮 | 执行操作，焦点返回触发元素 | 180ms | [UI 确认音] |
| 关闭（取消） | 对话框面板缩放到 0.9 然后淡出。覆盖层淡到 0%。 | Secondary 按钮 / Escape / B / Circle | 无操作，焦点返回触发元素 | 150ms | [UI 取消音] |
| 无法关闭 | 若对话框代表阻塞错误，不提供取消路径。仅提供解决选项。 | — | — | — | — |

> **Focus trap rule**: While a modal dialog is open, Tab and D-pad navigation
> must cycle within the dialog's interactive elements only. It must not be possible
> to navigate focus outside the dialog to the background content. This is both
> an accessibility requirement (WCAG 2.1 SC 2.1.2) and a UX integrity requirement.
> When the dialog closes, focus must return to the element that triggered it,
> not to the top of the page.

> **焦点陷阱规则**：模态对话框打开时，Tab 和方向键导航必须仅在对话框的可交互
> 元素内循环。必须不能将焦点导航到对话框外的背景内容。这既是无障碍要求（WCAG 2.1
> SC 2.1.2），也是 UX 完整性要求。对话框关闭时，焦点必须返回触发它的元素，而非
> 页面顶部。

**Accessibility**:
- Screen reader: Dialog container role: "dialog." Accessible name: dialog title (required — every dialog must have a title, even if visually hidden). On open, screen reader announces dialog title and first focusable element. Focus trap active.
- Keyboard: Escape key always maps to the cancel/dismiss action (same as Secondary button or close button). Enter always maps to the primary/confirm action.
- Motion reduction: Scale animation replaced with instant appear/disappear. Overlay fade retained at 100ms (faster).

**无障碍**：
- 屏幕阅读器：对话框容器角色："dialog"。可访问名称：对话框标题（必需——每个对话框
必须有标题，即使视觉隐藏）。打开时，屏幕阅读器朗读对话框标题和首个可聚焦元素。
焦点陷阱激活。
- 键盘：Escape 键始终映射到取消/关闭操作（同 Secondary 按钮或关闭按钮）。Enter 始终
映射到主要/确认操作。
- 减少动效：缩放动画替换为即时出现/消失。覆盖层淡入淡出保留 100ms（更快）。

**Implementation Notes**: [Godot: Implement as a `CanvasLayer` with a high layer
value (100+) to ensure it renders above all game content. The background overlay
is a full-screen `ColorRect` at 60% black opacity. Use `grab_focus()` on the
dialog's primary button after the open animation completes. Override `_input()` to
implement the focus trap — intercept Tab navigation and reroute to the dialog's
focusable elements.]

**实现说明**：[Godot：实现为带高 layer 值（100+）的 `CanvasLayer`，确保渲染在所有
游戏内容之上。背景覆盖层是 60% 黑色不透明度的全屏 `ColorRect`。在打开动画完成后
对对话框的主要按钮调用 `grab_focus()`。重写 `_input()` 实现焦点陷阱——拦截 Tab
导航并重新路由到对话框的可聚焦元素。]

---

#### Confirmation Dialog

#### Confirmation Dialog

**Category**: Feedback / Layout
**Status**: Draft
**When to Use**: The specific case of confirming a destructive action. Always
triggered by Button (Destructive). Always has exactly two options: confirm (labeled
with the specific action, not "OK") and cancel.
**When NOT to Use**: Non-destructive confirmations. Errors or notifications that
do not require a decision. Any dialog with more than two actions.

**类别**：反馈 / 布局
**状态**：草稿
**何时使用**：确认破坏性操作的特定情况。始终由 Button (Destructive) 触发。始终
恰好有两个选项：确认（以具体操作命名，而非"OK"）和取消。
**何时不要使用**：非破坏性确认。不需要决策的错误或通知。超过两个操作的任何
对话框。

> **Label rule**: The confirm button must be labeled with the specific action,
> not a generic "OK" or "Yes." "Delete Save File" not "OK." "Leave Match" not
> "Yes." This reduces mistakes for players who have difficulty reading the dialog
> content quickly. The pattern comes from Apple HIG and is validated by decades
> of usability research.

> **标签规则**：确认按钮必须以具体操作命名，而非通用"OK"或"是"。"删除存档文件"
> 而非"OK"。"离开对局"而非"是"。这减少了难以快速阅读对话框内容的玩家的失误。该
> 模式源自 Apple HIG，经数十年可用性研究验证。

**Structure**:
- Title: Brief, action-describing. "Delete save file?" not "Are you sure?"
- Body: One sentence stating the consequence. "This cannot be undone."
- Confirm button: Button (Primary) — labeled with the specific action. "Delete Save File."
- Cancel button: Button (Secondary) — "Cancel."
- Default focus: Cancel (safer default — reduces accidental destructive actions).

**结构**：
- 标题：简短、描述操作。"删除存档文件？"而非"你确定吗？"
- 正文：一句话陈述后果。"此操作无法撤销。"
- 确认按钮：Button (Primary)——以具体操作命名。"删除存档文件。"
- 取消按钮：Button (Secondary)——"取消。"
- 默认焦点：取消（更安全的默认值——减少意外的破坏性操作）。

**Accessibility**: Inherits all Modal Dialog accessibility. Additionally: screen
reader announces "Alert dialog, [title]" to signal destructive context. Default
focus on Cancel is a requirement, not a preference.

**无障碍**：继承所有 Modal Dialog 无障碍。此外：屏幕阅读器朗读"警示对话框，
[标题]"以标志破坏性上下文。默认聚焦取消是要求，而非偏好。

**Implementation Notes**: [Confirmation Dialog is a specific instance of Modal
Dialog — implement it as a subclass or as a parameterized scene. The default
focus on Cancel is critical: set `grab_focus()` on the Cancel button, not the
Confirm button, after open animation completes.]

**实现说明**：[Confirmation Dialog 是 Modal Dialog 的特定实例——实现为子类或参数化
场景。默认聚焦取消至关重要：打开动画完成后对取消按钮（而非确认按钮）调用
`grab_focus()`。]

---

#### Toast / Notification

#### Toast / Notification

**Category**: Feedback
**Status**: Draft
**When to Use**: Brief, non-blocking information that does not require a player
decision. "Game saved." "Achievement unlocked." "Your inventory is full." The player
can continue playing; the notification disappears on its own.
**When NOT to Use**: Information that requires a decision (use Modal Dialog).
Errors that require the player to take action. Critical information that the player
must not miss.

**类别**：反馈
**状态**：草稿
**何时使用**：不需要玩家决策的简短非阻塞信息。"游戏已保存。"、"成就已解锁。"
、"你的背包已满。"玩家可继续游戏；通知自行消失。
**何时不要使用**：需要决策的信息（使用 Modal Dialog）。需要玩家采取行动的错误。
玩家绝不能错过的关键信息。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Entering | Slides in from screen edge (typically bottom-right, away from primary action areas). Fades from 0 to 100% opacity. | Triggered by code | — | 200ms ease-out | [Sound matching notification type — see Sound Standards] |
| Displayed | Full opacity. Optional: icon (left), title, body text (optional), dismiss button (X, optional). | Pointer hover pauses auto-dismiss timer | Pause auto-dismiss | — | — |
| Auto-dismiss | Fades from 100 to 0% opacity, slides out | Timer expires (5 seconds default for one-line; 8 seconds for two-line) | Remove from queue | 200ms ease-in | — |
| Manual dismiss | Fades and slides out immediately | Click/tap X button or swipe on touch | Remove | 150ms | [UI cancel sound, quiet] |
| Queue overflow | New notification pushes oldest out early | New notification triggered while previous is displayed | FIFO queue, max 3 simultaneous | — | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 进入 | 从屏幕边缘滑入（通常从右下，远离主要操作区）。从 0 淡入到 100% 不透明度。 | 由代码触发 | — | 200ms ease-out | [匹配通知类型的声音——见声音标准] |
| 显示 | 完全不透明。可选：图标（左）、标题、正文（可选）、关闭按钮（X，可选）。 | 指针悬停暂停自动消失计时器 | 暂停自动消失 | — | — |
| 自动消失 | 从 100% 淡到 0% 不透明度，滑出 | 计时器到期（单行默认 5 秒；双行 8 秒） | 从队列移除 | 200ms ease-in | — |
| 手动关闭 | 立即淡出并滑出 | 点击/触摸 X 按钮或触摸上滑动 | 移除 | 150ms | [UI 取消音，轻柔] |
| 队列溢出 | 新通知将最旧的提前推出 | 上一通知显示时触发新通知 | FIFO 队列，最多同时 3 个 | — | — |

**Accessibility**:
- Screen reader: Toasts must be read aloud without requiring focus. In HTML, this uses `role="status"` or `role="alert"`. In game UI, this requires the engine's accessibility notification system. Verify engine support in engine-reference docs.
- Motion reduction: Slide animation replaced with fade only.
- Toasts must never be the sole communication channel for information the player needs to act on. If the information requires action, use a persistent UI element in addition to the toast.
- Auto-dismiss timer: 5 seconds is the minimum. Players with cognitive processing differences may need more time. Consider a setting to extend to 10 or 15 seconds.

**无障碍**：
- 屏幕阅读器：Toast 必须无需聚焦即可朗读。在 HTML 中使用 `role="status"` 或
`role="alert"`。在游戏 UI 中，这需要引擎的无障碍通知系统。在 engine-reference 文档
中验证引擎支持。
- 减少动效：滑动动画替换为仅淡入淡出。
- Toast 绝不能是玩家需要采取行动的信息的唯一通信渠道。若信息需要行动，除 toast 外
还使用持久 UI 元素。
- 自动消失计时器：5 秒为最小值。认知处理差异的玩家可能需要更长时间。考虑设置扩展到
10 或 15 秒。

**Implementation Notes**: [Godot: Manage a queue of `PanelContainer` scenes in a
`VBoxContainer` anchored to a screen corner. Each toast is instantiated, added to
the container, then auto-removed after a timer. The container should be on a high
`CanvasLayer` (50+) but below modal dialogs (100+). Animate using a `Tween` on
`modulate.a` and `position.x`. When motion reduction is active, skip the position
animation.]

**实现说明**：[Godot：在锚定到屏幕角落的 `VBoxContainer` 中管理 `PanelContainer`
场景队列。每个 toast 实例化后加入容器，然后计时器后自动移除。容器应在高
`CanvasLayer`（50+）上但低于模态对话框（100+）。使用 `Tween` 在 `modulate.a` 和
`position.x` 上动画。减少动效激活时，跳过位置动画。]

---

#### Tooltip

#### Tooltip

**Category**: Feedback
**Status**: Draft
**When to Use**: Contextual information that supplements a visible label. Item
descriptions in inventory. Stat explanations on a character sheet. Setting
descriptions in accessibility options. The player must be able to access this
information or proceed without it.
**When NOT to Use**: Information the player MUST read to complete an action — put
that in the label or body text, not a tooltip. Tooltips are not discoverable
on mobile touch without a hover state. On touch-only platforms, use an info button
that opens a description modal instead.

**类别**：反馈
**状态**：草稿
**何时使用**：补充可见标签的上下文信息。背包中的物品描述。角色面板上的属性解释。
无障碍选项中的设置描述。玩家必须能够访问此信息或在没有它的情况下继续。
**何时不要使用**：玩家必须阅读才能完成操作的信息——将其放在标签或正文中，而非
tooltip。tooltip 在无悬停状态的移动触摸上不可发现。在仅触摸平台上，使用打开描述
模态的信息按钮。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Hidden | — | — | — | — | — |
| Hover trigger | — | Mouse enters element | Begin 400ms delay timer | — | — |
| Gamepad/keyboard trigger | — | Element receives focus | Begin 300ms delay timer (shorter because navigation is intentional) | — | — |
| Appearing | Tooltip panel fades in and scales from 0.95 to 1.0. Positioned near element (prefer above, adjust if near screen edge). | Timer expires | Show tooltip | 120ms ease-out | — |
| Displayed | Tooltip visible. Title (optional). Body text. Max width: 300px. Multiple lines allowed. | — | — | — | — |
| Hiding | Tooltip fades out | Mouse leaves element / focus moves away | Hide tooltip | 80ms ease-in | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 隐藏 | — | — | — | — | — |
| 悬停触发 | — | 鼠标进入元素 | 启动 400ms 延迟计时器 | — | — |
| 手柄/键盘触发 | — | 元素获得焦点 | 启动 300ms 延迟计时器（更短因为导航是有意的） | — | — |
| 出现 | Tooltip 面板淡入并从 0.95 缩放到 1.0。定位在元素附近（优先上方，接近屏幕边缘时调整）。 | 计时器到期 | 显示 tooltip | 120ms ease-out | — |
| 显示 | Tooltip 可见。标题（可选）。正文。最大宽度：300px。允许多行。 | — | — | — | — |
| 隐藏 | Tooltip 淡出 | 鼠标离开元素 / 焦点移开 | 隐藏 tooltip | 80ms ease-in | — |

**Accessibility**:
- Screen reader: Tooltip content must be accessible without hover. The accessible name of the parent element should include the most critical tooltip information. The full tooltip text is optionally in the `description` property. Screen reader reads tooltip content when element is focused.
- The delay (300-400ms) prevents accidental tooltip display and is required — instant tooltips are disruptive in gamepad navigation.
- Tooltip text must meet the same contrast requirements as body text (4.5:1 minimum).

**无障碍**：
- 屏幕阅读器：Tooltip 内容必须可在无悬停时访问。父元素的可访问名称应包含最关键的
tooltip 信息。完整 tooltip 文本可选放在 `description` 属性。元素聚焦时屏幕阅读器
朗读 tooltip 内容。
- 延迟（300-400ms）防止意外 tooltip 显示且为必需——即时 tooltip 在手柄导航中是
干扰性的。
- Tooltip 文本必须满足与正文相同的对比度要求（最低 4.5:1）。

**Implementation Notes**: [Godot: Attach a custom `TooltipControl` scene as a
child of the trigger element. Show/hide with a `Timer` node. Position the tooltip
using a `CanvasLayer` to ensure it appears above all other UI. For screen edges,
detect if the tooltip rect extends beyond `get_viewport_rect()` and flip the
position to the opposite side.]

**实现说明**：[Godot：附加自定义 `TooltipControl` 场景作为触发元素的子节点。使用
`Timer` 节点显示/隐藏。使用 `CanvasLayer` 定位 tooltip 以确保它显示在所有其他 UI
之上。对于屏幕边缘，检测 tooltip 矩形是否超出 `get_viewport_rect()` 并将位置翻转到
对侧。]

---

#### Progress Bar

#### Progress Bar

**Category**: Feedback / Layout
**Status**: Draft
**When to Use**: Linear progress toward a defined endpoint. Loading screens (time
to completion), XP fill toward next level, quest objectives with countable progress
("3 of 10 enemies defeated"), download progress.
**When NOT to Use**: Circular or radial progress (use a separate Radial Progress
pattern if needed). Values that fluctuate up and down rapidly (use Health/Resource
Bar pattern). Values with no defined endpoint.

**类别**：反馈 / 布局
**状态**：草稿
**何时使用**：朝向定义终点的线性进度。加载屏幕（完成时间）、朝向下级的经验填充、
带可数进度的任务目标（"3/10 敌人已击败"）、下载进度。
**何时不要使用**：圆形或径向进度（如需要使用单独的 Radial Progress 模式）。快速
上下波动的值（使用 Health/Resource Bar 模式）。无定义终点的值。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Default | Track (full width, background color). Fill (left to right, value color). Value label (percentage or N/M, outside or inside fill). | — | — | — | — |
| Value increasing | Fill width animates to new value | Value changes | Smooth fill animation | 300ms ease-out | [Context-dependent — XP gain has a sound; loading has none] |
| Value at maximum | Fill reaches full width. Optional: completion animation (pulse, glow). | Value reaches 100% | Completion event fires | 200ms | [Completion sound if appropriate] |
| Value at zero | Fill hidden (zero width). Track still visible. | — | — | — | — |
| Indeterminate (unknown duration) | Animated loop (fill segment moves left-to-right, repeat). Used for loading of unknown duration. | — | — | Infinite loop | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 默认 | 轨道（全宽，背景色）。填充（左到右，值色）。值标签（百分比或 N/M，填充外或内）。 | — | — | — | — |
| 值增加 | 填充宽度动画到新值 | 值变化 | 平滑填充动画 | 300ms ease-out | [上下文相关——经验获取有声音；加载无] |
| 值达最大 | 填充达全宽。可选：完成动画（脉动、发光）。 | 值达 100% | 触发完成事件 | 200ms | [合适时播放完成音] |
| 值为零 | 填充隐藏（零宽）。轨道仍可见。 | — | — | — | — |
| 不确定（未知持续时间） | 动画循环（填充段从左到右移动，重复）。用于未知持续时间的加载。 | — | — | 无限循环 | — |

**Accessibility**:
- Screen reader: Role: "progressbar." Accessible name: what is progressing (e.g., "Experience Points," "Loading"). Value: current numeric value AND percentage AND maximum. "Experience Points, 450 of 1000, 45 percent." Update on significant changes (not every pixel).
- Do not rely only on fill color to communicate value. Include a numeric label.
- Indeterminate progress bars: announce "Loading, in progress" — do not announce changes since the value is unknown.
- Motion reduction: Indeterminate animation is replaced with a static "loading" indicator. Smooth fill animation is replaced with instant jump to new value.

**无障碍**：
- 屏幕阅读器：角色："progressbar"。可访问名称：正在进展的内容（如"经验值"、
"加载中"）。值：当前数值、百分比和最大值。"经验值，450/1000，45%"。在显著变化时
更新（非每像素）。
- 不要仅依靠填充色传达值。包含数值标签。
- 不确定进度条：朗读"加载中，进行中"——不要朗读变化因为值未知。
- 减少动效：不确定动画替换为静态"加载中"指示器。平滑填充动画替换为即时跳到新值。

**Implementation Notes**: [Godot: `ProgressBar` built-in with custom theming.
For indeterminate mode, `ProgressBar` does not have a native indeterminate state
in Godot 4.x — implement using a looping `Tween` on a fill element's position.
Ensure the Tween is paused when motion reduction mode is active and a static
indicator is shown instead.]

**实现说明**：[Godot：内建 `ProgressBar` 带自定义主题。对于不确定模式，
`ProgressBar` 在 Godot 4.x 中没有原生不确定状态——使用填充元素位置上的循环
`Tween` 实现。确保减少动效模式激活时 Tween 暂停并显示静态指示器。]

---

#### Input Field

#### Input Field

**Category**: Input
**Status**: Draft
**When to Use**: Text entry. Player name on a new save, search within a list,
remapping a key binding (special case — shows the key press, not typed text),
entering a numeric value precisely.
**When NOT to Use**: Selecting from known options (use Dropdown or List). On
console-primary platforms, minimize text entry — it requires a virtual keyboard,
which is high friction.

**类别**：输入
**状态**：草稿
**何时使用**：文本输入。新存档上的玩家名、列表内搜索、重映射按键绑定（特殊情况——
显示按键按下而非输入文本）、精确输入数值。
**何时不要使用**：从已知选项中选择（使用 Dropdown 或 List）。在主机优先平台上，
尽量减少文本输入——它需要虚拟键盘，摩擦高。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Default | Field border, placeholder text (label-style, muted color), empty input area. | — | — | — | — |
| Hovered | Border brightens slightly | Mouse over | — | 60ms | — |
| Focused | Border brightens fully. Cursor (blinking, 530ms on/530ms off). Placeholder text hidden. | Tab / click | Open virtual keyboard on console/mobile | Instant | [UI focus sound] |
| Typing | Characters appear. Cursor advances. | Keyboard input | Update field value | Immediate | [Subtle keystroke sound, optional] |
| Value present | Field shows typed value. Placeholder hidden. Clear button appears (X, right of field) if value is non-empty. | — | — | — | — |
| Character limit reached | No further input accepted. Optional: brief shake animation and limit indicator changes color. | Input at limit | Reject further characters | 200ms shake | [UI error sound, subtle] |
| Clear | Field empties. Cursor returns. Clear button disappears. | Click X / gamepad clear input | Clear value | Instant | [UI cancel sound, subtle] |
| Validation error | Border turns error color (red — ensure colorblind safe). Error message appears below field. | On submit or on blur | Show error | Instant | [UI error sound] |
| Validated / correct | Border turns success color (green — ensure colorblind safe). Success icon optional. | On validation pass | — | Instant | — |
| Disabled | 40% opacity, no interaction. Value still visible. | — | — | — | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 默认 | 字段边框、占位文本（标签式，柔和色）、空输入区。 | — | — | — | — |
| 悬停 | 边框略微变亮 | 鼠标悬停 | — | 60ms | — |
| 聚焦 | 边框完全变亮。光标（闪烁，530ms 开/530ms 关）。占位文本隐藏。 | Tab / 点击 | 主机/移动上打开虚拟键盘 | 即时 | [UI 聚焦音] |
| 输入中 | 字符出现。光标前进。 | 键盘输入 | 更新字段值 | 即时 | [细微击键音，可选] |
| 值存在 | 字段显示输入值。占位隐藏。值非空时出现清除按钮（X，字段右）。 | — | — | — | — |
| 字符限制达到 | 不接受进一步输入。可选：短暂摇晃动画，限制指示器变色。 | 限制处输入 | 拒绝更多字符 | 200ms 摇晃 | [UI 错误音，细微] |
| 清除 | 字段清空。光标返回。清除按钮消失。 | 点击 X / 手柄清除输入 | 清除值 | 即时 | [UI 取消音，细微] |
| 验证错误 | 边框变错误色（红——确保色盲安全）。字段下方出现错误消息。 | 提交或失焦时 | 显示错误 | 即时 | [UI 错误音] |
| 验证/正确 | 边框变成功色（绿——确保色盲安全）。可选成功图标。 | 验证通过时 | — | 即时 | — |
| 禁用 | 40% 不透明度，无交互。值仍可见。 | — | — | — | — |

**Accessibility**:
- Keyboard: All standard text editing shortcuts (Home, End, Ctrl+A, Ctrl+C, Ctrl+V, Ctrl+Z).
- Screen reader: Role: "textbox." Accessible name: field label (not placeholder text). Current value announced. Character limit announced when reached. Validation errors announced immediately on occurrence.
- Placeholder text must not be used as the only label — a visible label above or beside the field is required. Placeholder text disappears when the player types, causing confusion for players with cognitive or memory impairments.

**无障碍**：
- 键盘：所有标准文本编辑快捷键（Home、End、Ctrl+A、Ctrl+C、Ctrl+V、Ctrl+Z）。
- 屏幕阅读器：角色："textbox"。可访问名称：字段标签（非占位文本）。朗读当前值。
达到字符限制时朗读。验证错误在发生时立即朗读。
- 占位文本不得用作唯一标签——字段上方或旁边需要可见标签。占位文本在玩家输入时
消失，对认知或记忆障碍的玩家造成困惑。

**Implementation Notes**: [Godot `LineEdit`: set `placeholder_text` for the hint
but always include a visible `Label` node as the field's accessible name. Bind
`text_changed` signal for real-time validation. Bind `text_submitted` for form
submission on Enter. On console, `LineEdit.call("_popup_keyboard")` or use the OS
virtual keyboard API — verify against engine-reference/godot/ for Godot 4.6
console keyboard API specifics.]

**实现说明**：[Godot `LineEdit`：设置 `placeholder_text` 作为提示但始终包含
可见 `Label` 节点作为字段的可访问名称。绑定 `text_changed` 信号用于实时验证。
绑定 `text_submitted` 用于 Enter 时表单提交。主机上，`LineEdit.call("_popup_keyboard")`
或使用 OS 虚拟键盘 API——在 engine-reference/godot/ 中验证 Godot 4.6 主机键盘
API 细节。]

---

#### Tab Bar

#### Tab Bar

**Category**: Navigation
**Status**: Draft
**When to Use**: Dividing a single screen's content into discrete sections where
only one section is visible at a time. Character sheet tabs (Stats / Equipment /
Skills), settings tabs (Gameplay / Graphics / Audio / Accessibility). Maximum
5-6 tabs before the pattern breaks down and a sidebar navigation should be
considered instead.
**When NOT to Use**: More than 6 tabs. Content that benefits from simultaneous
visibility (use a layout pattern instead). Navigation between different screens
(use Screen Push).

**类别**：导航
**状态**：草稿
**何时使用**：将单屏内容划分为离散区域，一次只可见一个区域。角色面板标签
（属性 / 装备 / 技能）、设置标签（玩法 / 图形 / 音频 / 无障碍）。最多 5-6 个
标签后该模式失效，应考虑侧边栏导航。
**何时不要使用**：超过 6 个标签。受益于同时可见的内容（改用布局模式）。不同屏幕
间导航（使用 Screen Push）。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Default (inactive tab) | Tab label. No active indicator. | — | — | — | — |
| Active tab | Tab label. Active indicator (underline, fill, or contrasting background). Content area shows this tab's content. | — | — | — | — |
| Hovered (inactive) | Tab background fills slightly | Mouse over | — | 60ms | — |
| Focused (keyboard/gamepad) | Focus ring on tab label. | Tab key (within tab bar) or D-pad left/right on tab row | — | 60ms | [UI focus sound] |
| Activated | Active indicator transitions to this tab. Content area transitions (fade or slide). | Click / Enter / A / Cross | Switch active tab. Content update. | 150ms ease | [UI tab switch sound] |
| Gamepad shoulder button | — | L1/R1 (PS) or LB/RB (Xbox) | Switch to previous/next tab (standard platform convention) | 150ms | [UI tab switch sound] |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 默认（非活动标签） | 标签标签。无活动指示器。 | — | — | — | — |
| 活动标签 | 标签标签。活动指示器（下划线、填充或对比背景）。内容区显示此标签的内容。 | — | — | — | — |
| 悬停（非活动） | 标签背景略微填充 | 鼠标悬停 | — | 60ms | — |
| 聚焦（键盘/手柄） | 标签标签上的焦点环。 | Tab 键（标签栏内）或方向键左/右在标签行上 | — | 60ms | [UI 聚焦音] |
| 激活 | 活动指示器过渡到此标签。内容区过渡（淡入淡出或滑动）。 | 点击 / Enter / A / Cross | 切换活动标签。内容更新。 | 150ms ease | [UI 标签切换音] |
| 手柄肩键 | — | L1/R1 (PS) 或 LB/RB (Xbox) | 切换到上/下一个标签（标准平台约定） | 150ms | [UI 标签切换音] |

**Accessibility**:
- Keyboard: Arrow keys navigate between tabs within the tab bar (left/right). Tab key moves focus into the content area below. This follows the ARIA tab panel pattern.
- Screen reader: Role: "tab" for individual tabs. Role: "tablist" for the container. Role: "tabpanel" for the content area. Active tab state: "selected." Accessible name: tab label. Tabpanel is labeled by its corresponding tab.
- The active tab must be visually distinguishable by more than color alone (underline, fill pattern, or weight change in addition to color).

**无障碍**：
- 键盘：箭头键在标签栏内标签间导航（左/右）。Tab 键将焦点移到下方内容区。这遵循
ARIA 标签面板模式。
- 屏幕阅读器：单个标签角色："tab"。容器角色："tablist"。内容区角色："tabpanel"。
活动标签状态："selected"。可访问名称：标签标签。Tabpanel 由其对应标签命名。
- 活动标签必须能通过颜色之外的更多方式区分（下划线、填充图案或颜色之外的权重变化）。

**Implementation Notes**: [Godot: `TabContainer` built-in. For custom visual
styling, implement manually with a `HBoxContainer` of tab buttons and a
`MarginContainer` for content. The shoulder button shortcut (LB/RB) must be
implemented in the screen's `_input()` override — it is not built into Godot's
tab system. Check platform conventions: Xbox uses LB/RB; PlayStation uses L1/R1;
both are the same physical button, so a single binding works.]

**实现说明**：[Godot：内建 `TabContainer`。对于自定义视觉样式，使用 `HBoxContainer`
标签按钮和 `MarginContainer` 内容手动实现。肩键快捷键（LB/RB）必须在屏幕的
`_input()` 重写中实现——它内建于 Godot 标签系统。检查平台约定：Xbox 使用 LB/RB；
PlayStation 使用 L1/R1；两者是相同物理按钮，单一绑定即可。]

---

#### Scroll Container

#### Scroll Container

**Category**: Layout
**Status**: Draft
**When to Use**: Content that exceeds the visible area of its container. Inventory
lists, lore entry text, credits, long settings lists. The scroll indicator shows
the player that more content exists.
**When NOT to Use**: Content that can be paginated instead (pagination may be
clearer for dense list navigation). Infinite scroll (always provide a loading
state and an end state).

**类别**：布局
**状态**：草稿
**何时使用**：超过其容器可见区的内容。背包列表、传说条目文本、制作人员名单、长设置
列表。滚动指示器向玩家显示存在更多内容。
**何时不要使用**：可改为分页的内容（分页对密集列表导航可能更清晰）。无限滚动
（始终提供加载状态和结束状态）。

**Interaction Specification**:

**交互规格**：

| State | Visual | Input | Response | Duration | Audio |
|-------|--------|-------|----------|----------|-------|
| Content fits | No scrollbar visible (or always-visible scrollbar at full height, depending on art direction). | — | — | — | — |
| Scrollable | Scrollbar appears (right edge). Scrollbar thumb size represents viewport vs. content ratio. | — | — | — | — |
| Scrolling (mouse) | Content moves. Scrollbar thumb moves proportionally. | Mouse wheel | Scroll by 3 lines per wheel tick (configurable in OS) | Smooth | — |
| Scrollbar drag | Content moves. Thumb follows pointer. | Click + drag scrollbar thumb | Scroll proportionally | Real time | — |
| Keyboard scroll | Content moves one item height per keypress. | Up/Down arrows when container is focused and no child is focused | Scroll by one unit | Immediate | — |
| Gamepad scroll | Content moves to keep focused item in view. | D-pad navigation to items beyond visible area | Auto-scroll to keep focused item visible | Smooth 150ms | — |
| Scroll top / bottom | Content stops. Scrollbar thumb at end. | Content boundary reached | Stop scrolling | — | — |
| Focus follows scroll | When a child element receives focus, scroll container ensures it is fully visible. | Any child receives focus | Scroll to reveal focused element | 200ms ease | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 | 音频 |
|-------|--------|-------|----------|----------|-------|
| 内容适配 | 无滚动条可见（或根据美术方向始终可见全高滚动条）。 | — | — | — | — |
| 可滚动 | 滚动条出现（右边缘）。滚动条滑块大小代表视口与内容比例。 | — | — | — | — |
| 滚动（鼠标） | 内容移动。滚动条滑块按比例移动。 | 鼠标滚轮 | 每滚轮刻度滚动 3 行（OS 中可配置） | 平滑 | — |
| 滚动条拖动 | 内容移动。滑块跟随指针。 | 点击 + 拖动滚动条滑块 | 按比例滚动 | 实时 | — |
| 键盘滚动 | 每次按键内容移动一个项目高度。 | 容器聚焦且无子项聚焦时上/下箭头 | 滚动一个单位 | 即时 | — |
| 手柄滚动 | 内容移动以保持聚焦项可见。 | 方向键导航到可见区外项目 | 自动滚动保持聚焦项可见 | 平滑 150ms | — |
| 滚动顶部 / 底部 | 内容停止。滚动条滑块在末端。 | 到达内容边界 | 停止滚动 | — | — |
| 焦点跟随滚动 | 子元素获得焦点时，滚动容器确保其完全可见。 | 任何子项获得焦点 | 滚动显示聚焦元素 | 200ms ease | — |

**Accessibility**:
- Keyboard/Gamepad: The scroll container itself should not require explicit scrollbar interaction — navigating list items inside it should auto-scroll to keep focused items in view.
- Screen reader: The scroll container announces "scrollable" and the scroll position ("showing items 5 through 15 of 30"). This requires engine accessibility support — verify in engine-reference/godot/.
- Fade edges (content fading at scroll boundaries to indicate more content exists) are a helpful visual affordance but must not be the only indicator that content exists beyond the visible area. Include a scrollbar.

**无障碍**：
- 键盘/手柄：滚动容器本身不应需要显式滚动条交互——导航其中的列表项应自动滚动以
保持聚焦项可见。
- 屏幕阅读器：滚动容器朗读"可滚动"和滚动位置（"显示 30 项中的第 5 到 15 项"）。
这需要引擎无障碍支持——在 engine-reference/godot/ 中验证。
- 边缘淡入淡出（内容在滚动边界淡入淡出以指示存在更多内容）是有用的视觉暗示但不得
是可见区外存在内容的唯一指示器。包含滚动条。

**Implementation Notes**: [Godot `ScrollContainer`: call `ensure_control_visible()`
on the focused child whenever `gui_focus_changed` fires inside the container.
Bind this via a recursive `connect` on the container's `gui_focus_changed` signal.
For smooth scroll animation, use a `Tween` on `scroll_vertical` rather than
setting it directly.]

**实现说明**：[Godot `ScrollContainer`：每当容器内 `gui_focus_changed` 触发时对
聚焦子项调用 `ensure_control_visible()`。通过容器 `gui_focus_changed` 信号上的
递归 `connect` 绑定。对于平滑滚动动画，使用 `scroll_vertical` 上的 `Tween` 而非
直接设置。]

---

## Game-Specific UI Patterns

## 游戏特定 UI 模式

---

#### Inventory Slot

#### Inventory Slot

**Category**: Game-Specific
**Status**: Draft
**When to Use**: Every item container in the inventory grid. Empty slots, populated
slots, equipped slots, locked slots. The slot is the frame; the item icon is the
content.

**类别**：游戏特定
**状态**：草稿
**何时使用**：背包网格中的每个物品容器。空槽、已填槽、已装备槽、已锁定槽。槽是
框架；物品图标是内容。

**States**:

**状态**：

| State | Visual | Notes |
|-------|--------|-------|
| Empty | Subtle slot border, no content. Not the same as disabled. Empty slots are interactable (receive items). | Avoid fully invisible empty slots — players lose track of grid dimensions |
| Populated | Item icon fills 80% of slot area. Stack count bottom-right (if applicable). Quality border (colorblind-safe — icon + color). Equipped badge (top-right, if equipped). | |
| Focused | Focus ring. Tooltip appears after 300ms. | |
| Selected | Thicker or contrasting border. Used when multi-select is supported. | |
| Drag source | Slot dims, drag ghost follows pointer. | See Grid Item for full drag spec |
| Locked | Padlock icon overlay. No interaction. May show item at 50% opacity behind lock. | Used for locked loadout slots, DLC content, etc. |
| Highlighted | Animated border glow (pulsing). Used for quest-relevant items or newly acquired items. | Respect motion reduction — replace pulse with a static badge |
| Cooldown overlay | Radial fill overlay from 12 o'clock, clockwise, depleting as cooldown expires. | Only applicable if slots represent active items with cooldowns |

| 状态 | 视觉 | 注释 |
|-------|--------|-------|
| 空 | 细微槽边框，无内容。与禁用不同。空槽可交互（接收物品）。 | 避免完全不可见的空槽——玩家会失去对网格维度的追踪 |
| 已填 | 物品图标填充 80% 槽区。堆叠计数右下（若适用）。品质边框（色盲安全——图标 + 颜色）。已装备徽章（右上，若已装备）。 | |
| 聚焦 | 焦点环。延迟 300ms 后出现 Tooltip。 | |
| 选中 | 更粗或对比边框。用于支持多选时。 | |
| 拖动源 | 槽变暗，拖动幽灵跟随指针。 | 见 Grid Item 完整拖动规格 |
| 锁定 | 挂锁图标覆盖。无交互。可能显示锁后 50% 不透明度的物品。 | 用于锁定的装备槽、DLC 内容等 |
| 高亮 | 动画边框发光（脉动）。用于任务相关物品或新获得物品。 | 尊重减少动效——用静态徽章替换脉动 |
| 冷却覆盖 | 从 12 点钟方向顺时针、随冷却到期耗尽的径向填充覆盖。 | 仅当槽代表带冷却的活动物品时适用 |

**Accessibility**: Stack counts and quality tiers must have text or icon alternatives to color coding. Tooltip is the primary accessibility mechanism — ensure it is reachable by keyboard and screen reader. Locked slots must announce "locked" to screen readers.

**无障碍**：堆叠计数和品质级别必须有颜色编码之外的文本或图标替代。Tooltip 是主要
无障碍机制——确保它可通过键盘和屏幕阅读器访问。锁定的槽必须向屏幕阅读器朗读
"已锁定"。

**Implementation Notes**: [Godot: Custom `Control` node. Quality border implemented as a `StyleBoxFlat` swapped based on rarity — avoid using `modulate` color for quality, as it affects the icon color. Drag and drop implemented via `get_drag_data()` and `can_drop_data()` / `drop_data()` override methods.]

**实现说明**：[Godot：自定义 `Control` 节点。品质边框实现为基于稀有度交换的
`StyleBoxFlat`——避免用 `modulate` 颜色表示品质，因为它影响图标颜色。拖放通过
`get_drag_data()` 和 `can_drop_data()` / `drop_data()` 重写方法实现。]

---

#### Ability / Skill Icon

#### Ability / Skill Icon

**Category**: Game-Specific
**Status**: Draft
**When to Use**: Ability buttons in the HUD ability bar, skill tree nodes, and
any context where an ability must show availability state.

**类别**：游戏特定
**状态**：草稿
**何时使用**：HUD 技能栏中的技能按钮、技能树节点，以及任何需要显示可用性状态的
技能上下文。

**States**:

**状态**：

| State | Visual | Notes |
|-------|--------|-------|
| Available | Full opacity icon. Keybinding label below. | |
| On cooldown | Radial overlay depleting clockwise from 12 o'clock. Remaining time shown as a number in the center when > 2 seconds remain. | |
| Charges remaining | Charge pip indicators below icon (e.g., 3 filled circles = 3 charges). Number alternative for screen readers. | |
| Out of resource | Icon desaturates to ~20%. Border dims. Keybinding label dims. Distinct from cooldown — resource-gated, not time-gated. | |
| Locked / not unlocked | Icon silhouette only (no full art visible). Padlock badge. May show unlock condition in tooltip. | |
| Active / channeling | Pulsing border. Radial fill shows channel duration remaining. | |
| Just activated | Brief scale 0.9x then spring to 1.0x (overshoot to 1.05x). | Example: Guild Wars 2 and Path of Exile both use press-depress animations on ability use to confirm activation. Respect motion reduction. |

| 状态 | 视觉 | 注释 |
|-------|--------|-------|
| 可用 | 完全不透明图标。下方按键绑定标签。 | |
| 冷却中 | 从 12 点钟方向顺时针耗尽的径向覆盖。剩余 > 2 秒时中心显示剩余时间数字。 | |
| 充能剩余 | 图标下方的充能点指示器（如 3 个填充圆 = 3 充能）。屏幕阅读器使用数字替代。 | |
| 资源不足 | 图标去饱和到约 20%。边框变暗。按键绑定标签变暗。与冷却不同——资源门控，非时间门控。 | |
| 锁定 / 未解锁 | 仅图标剪影（无完整美术可见）。挂锁徽章。可能 tooltip 中显示解锁条件。 | |
| 活动 / 引导中 | 脉动边框。径向填充显示剩余引导持续时间。 | |
| 刚激活 | 短暂缩放 0.9x 然后弹到 1.0x（过冲到 1.05x）。 | 示例：Guild Wars 2 和 Path of Exile 都在技能使用时使用按下-释放动画以确认激活。尊重减少动效。 |

**Accessibility**: All cooldown/charge information must have a numeric value (screen reader cannot parse radial overlays). The cooldown timer number satisfies this. Ability names and descriptions must be exposed to screen readers via tooltip.

**无障碍**：所有冷却/充能信息必须有数值（屏幕阅读器无法解析径向覆盖）。冷却计时器
数字满足此要求。技能名称和描述必须通过 tooltip 暴露给屏幕阅读器。

**Implementation Notes**: [Godot: Custom `TextureButton` subclass with overlay
`Control` nodes for cooldown radial and charge pips. The cooldown radial uses a
custom shader on a `ColorRect` rotating a mask — or implement with a
`ProgressBar` styled as circular if engine supports it. Verify against
engine-reference/godot/ for Godot 4.6 shader support for this pattern.]

**实现说明**：[Godot：自定义 `TextureButton` 子类，带覆盖 `Control` 节点用于冷却
径向和充能点。冷却径向使用 `ColorRect` 上旋转遮罩的自定义着色器——或在引擎支持时
用样式化为圆形的 `ProgressBar` 实现。在 engine-reference/godot/ 中验证 Godot 4.6
对此模式的着色器支持。]

---

#### Health / Resource Bar

#### Health / Resource Bar

**Category**: Game-Specific
**Status**: Draft
**When to Use**: Any continuously varying value in the HUD that represents a
critical player resource. Health, mana, stamina, shield, fuel.

**类别**：游戏特定
**状态**：草稿
**何时使用**：HUD 中代表关键玩家资源的任何连续变化值。生命、法力、耐力、护盾、
燃料。

**States and behaviors**:

**状态和行为**：

| Event | Visual | Audio | Duration |
|-------|--------|-------|---------|
| Value decrease (damage) | Fill shrinks. Brief "damage flash" on the fill (white or red flash). Ghost bar lingers at previous value and drains to new value over 0.5s ("damage indicator"). | [Damage taken sound — varies by amount] | Instant decrease, 500ms ghost bar drain |
| Value increase (heal) | Fill grows. Brief heal color flash (green — ensure colorblind safe with icon/glow backup). | [Heal sound] | 300ms ease-in |
| Below 25% threshold | Fill changes color to warning state. Border pulses (or static badge in motion reduction mode). Optional: heartbeat audio cue (paired with visual if audio is sole signal). | [Low health sound — loops until above threshold] | Continuous |
| At zero | Bar empty. Optional: bar shakes briefly. Death/depletion event fires. | [Death/depletion sound] | 200ms shake |
| Maximum | Fill at 100%, brief glow. | — | 200ms |
| Overflow (shield) | A separate bar segment appears beyond the natural fill area, in shield color. | [Shield gain sound] | 200ms |

| 事件 | 视觉 | 音频 | 持续时间 |
|-------|--------|-------|---------|
| 值减少（伤害） | 填充收缩。填充上短暂"受击闪烁"（白色或红色闪烁）。幽灵条停留在先前值并在 0.5 秒内排到新值（"伤害指示器"）。 | [受击音——按量变化] | 即时减少，500ms 幽灵条排空 |
| 值增加（治疗） | 填充增长。短暂治疗色闪烁（绿——确保色盲安全带图标/发光备份）。 | [治疗音] | 300ms ease-in |
| 低于 25% 阈值 | 填充变色为警告状态。边框脉动（或减少动效模式下静态徽章）。可选：心跳音频提示（若音频是唯一信号则配对视觉）。 | [低生命音——循环直到高于阈值] | 持续 |
| 为零 | 条空。可选：条短暂摇晃。触发死亡/耗尽事件。 | [死亡/耗尽音] | 200ms 摇晃 |
| 最大 | 填充 100%，短暂发光。 | — | 200ms |
| 溢出（护盾） | 自然填充区外出现单独的条段，护盾色。 | [护盾获得音] | 200ms |

**Accessibility**: The current value must be accessible as a number (tooltip or persistent display, or both). Color-coded threshold states must have non-color backups (icon, flashing, or audio visual warning). Warning state at 25% must have a visual signal independent of the color change.

**无障碍**：当前值必须可作为数字访问（tooltip 或持久显示，或两者）。颜色编码的阈值
状态必须有非颜色备份（图标、闪烁或音频视觉警告）。25% 的警告状态必须有独立于颜色
变化的视觉信号。

**Implementation Notes**: [Godot: Two overlapping `ProgressBar` nodes for ghost
bar effect — back bar holds previous value (drains via Tween), front bar holds
current value (updates instantly). Threshold states trigger `StyleBoxFlat` swaps
on the front bar. Ghost bar Tween duration is tunable as a designer parameter.]

**实现说明**：[Godot：两个重叠 `ProgressBar` 节点用于幽灵条效果——后条持有先前值
（通过 Tween 排空），前条持有当前值（即时更新）。阈值状态在前条上触发 `StyleBoxFlat`
交换。幽灵条 Tween 持续时间可作为设计师参数调谐。]

---

#### Dialogue Box

#### Dialogue Box

**Category**: Game-Specific
**Status**: Draft
**When to Use**: NPC conversation, voiced narrative dialogue, tutorial text
delivered through a character. All dialogue that has a speaker.

**类别**：游戏特定
**状态**：草稿
**何时使用**：NPC 对话、配音叙事对话、通过角色交付的教程文本。所有有说话者的对话。

**Structure**: Speaker portrait or name tag (top of box or left side). Dialogue text body. Continue/advance prompt (bottom right). Optional: skip-all button, voice acting indicator, subtitle indicator.

**结构**：说话者头像或名牌（框顶或左侧）。对话正文。继续/前进提示（右下）。可选：
全部跳过按钮、配音指示器、字幕指示器。

**States and behaviors**:

**状态和行为**：

| State | Visual | Input | Response | Duration |
|-------|--------|-------|----------|---------|
| Line entering | Text reveals character-by-character (typewriter effect). Or: text fades in at full speed if accessibility option set. | — | — | Speed: configurable in accessibility settings |
| Revealing | Text animating in. Continue prompt hidden or pulsing at slow opacity. | [Any advance input] | Skip to end of current line instantly (show full line, stop typewriter) | Immediate |
| Line complete | Full line shown. Continue prompt visible and animated. | — | — | — |
| Advancing to next line | Continue prompt hides. Text fades out or wipes. New line begins. | [Any advance input] — Enter / A / Cross / Space / mouse click | Advance | 100ms transition |
| Choices appearing | Choice buttons appear below dialogue text. Continue prompt hidden. Navigation focus moves to first choice. | D-pad / keyboard to select, Enter / A / Cross to confirm | Select choice | 150ms enter animation |
| Closing | Box fades out | Final line advanced | Return control to player | 200ms |
| Skipping all (if supported) | Brief confirmation prompt: "Skip dialogue?" | Dedicated skip button | Skip to post-dialogue state | — |

| 状态 | 视觉 | 输入 | 响应 | 持续时间 |
|-------|--------|-------|----------|---------|
| 行进入 | 文本逐字符显示（打字机效果）。或：若设置了无障碍选项则文本全速淡入。 | — | — | 速度：无障碍设置中可配置 |
| 显示中 | 文本动画进入。继续提示隐藏或以低不透明度脉动。 | [任何前进输入] | 即时跳到当前行末（显示完整行，停止打字机） | 即时 |
| 行完成 | 完整行显示。继续提示可见并动画。 | — | — | — |
| 前进到下一行 | 继续提示隐藏。文本淡出或擦除。新行开始。 | [任何前进输入]——Enter / A / Cross / Space / 鼠标点击 | 前进 | 100ms 过渡 |
| 选项出现 | 选项按钮出现在对话文本下方。继续提示隐藏。导航焦点移到首个选项。 | 方向键 / 键盘选择，Enter / A / Cross 确认 | 选择选项 | 150ms 进入动画 |
| 关闭 | 框淡出 | 最后一行前进 | 将控制返回玩家 | 200ms |
| 全部跳过（若支持） | 简短确认提示："跳过对话？" | 专用跳过按钮 | 跳到对话后状态 | — |

**Accessibility**: Subtitles are always enabled by default for all voiced dialogue. Typewriter animation speed is a user setting (see accessibility-requirements.md). The dialogue box must not auto-advance — players must control pacing. Speaker name is always shown. All choice buttons must be navigable by keyboard and gamepad. Choices must be accessible to screen readers with position announced.

**无障碍**：所有配音对话默认始终启用字幕。打字机动画速度是用户设置（见
accessibility-requirements.md）。对话框不得自动前进——玩家必须控制节奏。说话者
名称始终显示。所有选项按钮必须可由键盘和手柄导航。选项必须可由屏幕阅读器访问并
朗读位置。

**Implementation Notes**: [Godot: `RichTextLabel` with `bbcode_enabled` for
formatting. Typewriter effect via `visible_characters` property animated by a
`Timer`. Bind the advance input to a function that either skips typewriter
(sets `visible_characters = -1`) or advances the dialogue state. Speaker name
displayed in a separate `Label` above or beside the box. Dialogue data loaded from
JSON or a dedicated dialogue format (e.g., Dialogic, Yarn Spinner for Godot).]

**实现说明**：[Godot：`RichTextLabel` 带 `bbcode_enabled` 用于格式化。打字机效果
通过由 `Timer` 动画的 `visible_characters` 属性。将前进输入绑定到要么跳过打字机
（设置 `visible_characters = -1`）要么推进对话状态的函数。说话者名称显示在框上
方或旁边的单独 `Label` 中。对话数据从 JSON 或专用对话格式加载（如 Godot 的
Dialogic、Yarn Spinner）。]

---

#### Context Action Prompt

#### Context Action Prompt

**Category**: Game-Specific
**Status**: Draft
**When to Use**: A prompt that appears near an interactable game object indicating
what the player can do. "Press [A] to open chest." "Hold [E] to pick up." Appears
when the player enters the interaction zone, disappears when they leave.

**类别**：游戏特定
**状态**：草稿
**何时使用**：在可交互游戏对象附近出现的提示，指示玩家能做什么。"按 [A] 打开
宝箱。" "长按 [E] 拾取。"玩家进入交互区时出现，离开时消失。

**States**:

**状态**：

| State | Visual | Notes |
|-------|--------|-------|
| Appearing | Fades in and rises 8px from object anchor point. | Respect motion reduction — fade only, no rise |
| Idle | Platform-correct button icon + action label. Icon matches current input method (updates if player switches). | Always show platform-correct icon — do not hardcode "Press A" for all platforms |
| Holding (for hold inputs) | Radial fill on the button icon shows hold progress. Label changes to active verb ("Opening..."). | |
| Cannot interact (blocked) | Icon dims. Label shows reason if known ("Too heavy", "Need key"). | Optional — only show blocked state if the reason is meaningful to the player |
| Disappearing | Fades out. | Triggered when player exits interaction zone |

| 状态 | 视觉 | 注释 |
|-------|--------|-------|
| 出现 | 从对象锚点淡入并上升 8px。 | 尊重减少动效——仅淡入，无上升 |
| 空闲 | 平台正确的按钮图标 + 操作标签。图标匹配当前输入方法（玩家切换时更新）。 | 始终显示平台正确图标——不要对所有平台硬编码"按 A" |
| 长按中（对于长按输入） | 按钮图标上的径向填充显示长按进度。标签变为活动动词（"打开中..."）。 | |
| 无法交互（阻塞） | 图标变暗。标签显示已知原因（"太重"、"需要钥匙"）。 | 可选——仅当原因对玩家有意义时显示阻塞状态 |
| 消失 | 淡出。 | 玩家退出交互区时触发 |

**Accessibility**: The button icon must be accompanied by a text label — do not rely on icon alone (some players use custom button labels or adaptive controllers with non-standard icons). The prompt must be positioned to not overlap character health or critical HUD information.

**无障碍**：按钮图标必须伴随文本标签——不要仅依赖图标（某些玩家使用自定义按钮标签
或带非标准图标的自适应手柄）。提示定位不得遮挡角色生命或关键 HUD 信息。

**Implementation Notes**: [Godot: Attach as a `Node3D` child (or `Node2D` child in 2D) of the interactable object. Use a `BillboardMesh` or a `SubViewport` with a UI scene for 3D games — this keeps the prompt facing the camera without code. Update the button icon texture based on `Input.get_joy_name()` or keyboard detection via `InputEventKey` vs `InputEventJoypadButton`. Hold progress implemented as an `AnimationPlayer` or `Tween` on a radial mask shader.]

**实现说明**：[Godot：作为可交互对象的 `Node3D` 子节点（或 2D 中的 `Node2D`
子节点）附加。3D 游戏中使用 `BillboardMesh` 或带 UI 场景的 `SubViewport`——这使
提示朝向相机而无需代码。基于 `Input.get_joy_name()` 或通过 `InputEventKey` 与
`InputEventJoypadButton` 检测键盘，更新按钮图标纹理。长按进度实现为径向遮罩着色器
上的 `AnimationPlayer` 或 `Tween`。]

---

#### Damage Number

#### Damage Number

**Category**: Game-Specific
**Status**: Draft
**When to Use**: Floating feedback numbers above combat participants. Normal
damage, critical damage, healing, miss.

**类别**：游戏特定
**状态**：草稿
**何时使用**：战斗参与者上方的浮动反馈数字。普通伤害、暴击伤害、治疗、未命中。

**Variants**:

**变体**：

| Variant | Visual | Notes |
|---------|--------|-------|
| Normal damage | White number, normal weight, medium size. | |
| Critical hit | Larger size (1.5x), bold weight, orange or yellow — verify colorblind safe. Brief scale impact (1.3x → 1.0x on appear). | Example: Path of Exile and Diablo IV both use scale-pop for crits to make them immediately recognizable by size alone, independent of color. |
| Healing | Green (verify colorblind safe — use + prefix and upward trajectory as non-color backups). | |
| Miss / Evade | "MISS" text, grey, italic. Floats at smaller size. | |
| Status damage (DoT) | Smaller size, distinct color matching the status effect. | |

| 变体 | 视觉 | 注释 |
|---------|--------|-------|
| 普通伤害 | 白色数字，常规字重，中等尺寸。 | |
| 暴击 | 更大尺寸（1.5x），粗字重，橙色或黄色——验证色盲安全。出现时短暂缩放冲击（1.3x → 1.0x）。 | 示例：Path of Exile 和 Diablo IV 都对暴击使用缩放弹出，使其仅凭尺寸即可立即识别，独立于颜色。 |
| 治疗 | 绿色（验证色盲安全——使用 + 前缀和向上轨迹作为非颜色备份）。 | |
| 未命中 / 闪避 | "MISS"文本，灰色，斜体。以更小尺寸浮动。 | |
| 状态伤害 (DoT) | 更小尺寸，匹配状态效果的独特颜色。 | |

**Behavior**: Numbers float upward from the hit location over 1.0 second. Numbers fade from 100% to 0% during the last 0.4 seconds. Multiple numbers from rapid hits stagger horizontally to avoid overlap. Maximum simultaneous damage numbers on screen: [define per game — typically 8-12 per character].

**行为**：数字从受击位置向上浮动 1.0 秒。最后 0.4 秒内数字从 100% 淡到 0%。快速
命中的多个数字水平交错以避免重叠。屏幕上同时显示的最大伤害数字：[按游戏定义——
通常每角色 8-12 个]。

**Accessibility**: Damage numbers are purely supplementary feedback — they must never be the only way to understand combat state. Health bars are the authoritative source. Provide an option to disable damage numbers entirely (some players find them visually overwhelming). When disabled, the game must remain fully playable.

**无障碍**：伤害数字纯粹是补充反馈——绝不能是理解战斗状态的唯一方式。生命条是权威
来源。提供完全禁用伤害数字的选项（某些玩家觉得它们视觉过载）。禁用时，游戏必须保持
完全可玩。

**Implementation Notes**: [Godot: Pool of `Label3D` (3D games) or `Label` (2D games)
instances recycled via an object pool. Each instance is given a random small
horizontal offset on spawn (±20px) to reduce overlap. Float animation via
`Tween` on `position.y` and `modulate.a`. Critical hit scale-pop via Tween
with `EASE_OUT` on scale followed by linear settle.]

**实现说明**：[Godot：通过对象池回收的 `Label3D`（3D 游戏）或 `Label`（2D 游戏）
实例池。每个实例生成时给予随机小幅水平偏移（±20px）以减少重叠。浮动动画通过
`position.y` 和 `modulate.a` 上的 `Tween`。暴击缩放弹出通过 `EASE_OUT` 在缩放上
的 Tween 后跟线性沉淀。]

---

## Navigation Patterns

## 导航模式

---

#### Screen Push / Pop / Replace

#### Screen Push / Pop / Replace

**Category**: Navigation
**Status**: Draft

These three patterns define how screens enter and exit the navigation stack.

**类别**：导航
**状态**：草稿

这三个模式定义屏幕如何进入和退出导航栈。

| Pattern | Trigger | Animation | Stack Behavior | Focus Behavior |
|---------|---------|-----------|---------------|----------------|
| Push | Navigate deeper (open submenu, open detail view) | New screen slides in from right. Previous screen slides left and dims. | Previous screen remains on stack | Focus moves to first interactive element on new screen |
| Pop (Back) | Back button / Escape / B / Circle | Current screen slides right and exits. Previous screen slides in from left and brightens. | Current screen removed from stack | Focus returns to the element that triggered the Push |
| Replace | Navigate to a peer screen (not child, not parent). Loading screen. | Fade out current, fade in new. No directional bias. | Current screen removed. New screen added. | Focus moves to first interactive element on new screen |

| 模式 | 触发 | 动画 | 栈行为 | 焦点行为 |
|---------|---------|-----------|---------------|----------------|
| Push | 向更深层导航（打开子菜单、打开详情视图） | 新屏幕从右滑入。前屏幕向左滑并变暗。 | 前屏幕保留在栈上 | 焦点移到新屏幕首个可交互元素 |
| Pop（返回） | 返回按钮 / Escape / B / Circle | 当前屏幕向右滑出。前屏幕从左滑入并变亮。 | 当前屏幕从栈移除 | 焦点返回触发 Push 的元素 |
| Replace | 导航到同级屏幕（非子，非父）。加载屏幕。 | 当前淡出，新淡入。无方向偏向。 | 当前屏幕移除。新屏幕添加。 | 焦点移到新屏幕首个可交互元素 |

**Animation durations**: Push/Pop: 250ms ease-in-out. Replace: 200ms fade out + 200ms fade in.

**动画持续时间**：Push/Pop：250ms ease-in-out。Replace：200ms 淡出 + 200ms 淡入。

**Motion reduction**: All slide animations become fades. Duration reduces to 100ms.

**减少动效**：所有滑动动画变为淡入淡出。持续时间减到 100ms。

**Implementation Notes**: [Godot: Implement as a `ScreenManager` singleton managing
a stack of `Control` scenes. `push(screen_scene)` instantiates and animates in.
`pop()` animates out and frees. `replace(screen_scene)` calls pop then push without
the intermediate stack state. Use `CanvasLayer` per screen to isolate input handling.
Store the "return focus" element reference before pushing so it can be restored on pop.]

**实现说明**：[Godot：实现为管理 `Control` 场景栈的 `ScreenManager` 单例。
`push(screen_scene)` 实例化并动画进入。`pop()` 动画退出并释放。
`replace(screen_scene)` 调用 pop 然后 push 而无中间栈状态。每个屏幕使用
`CanvasLayer` 隔离输入处理。Push 前存储"返回焦点"元素引用以便 pop 时恢复。]

---

#### Focus Management

#### Focus Management

**Category**: Navigation
**Status**: Draft

> Focus management is the most common keyboard and gamepad accessibility failure
> in game UIs. These rules must be implemented consistently. A player should
> never be in a state where they cannot see which element is focused, or where
> Tab/D-pad produces no visible result.

**类别**：导航
**状态**：草稿

> 焦点管理是游戏 UI 中最常见的键盘和手柄无障碍失败。这些规则必须一致实现。玩家
> 绝不应处于无法看到哪个元素聚焦、或 Tab/方向键不产生可见结果的状态。

| Rule | Description |
|------|-------------|
| Screen open | Focus is placed on the most logical interactive element — typically the Primary button, the first list item, or the last-focused element if the screen was previously visited. Never on a non-interactive element. |
| Screen close / pop | Focus returns to the element that triggered the navigation (the button that opened the screen, the list item that was selected). If that element no longer exists, focus goes to the nearest preceding interactive element. |
| Modal open | Focus is trapped inside the modal. See Modal Dialog pattern. |
| Modal close | Focus returns to the element that triggered the modal. |
| Element disabled | If the focused element becomes disabled, focus moves to the next available interactive element in the tab order. |
| Element destroyed | If the focused element is removed from the scene, focus moves to the nearest preceding element in the tab order. |
| Screen without interactive elements | Focus management is a no-op. Ensure back/cancel input still works. |
| Tab key (keyboard) | Moves focus forward through interactive elements in document order (left to right, top to bottom). Shift+Tab moves backward. |
| D-pad (gamepad) | Moves focus in the spatial direction pressed. Spatial navigation is preferred over strict tab order for gamepad. Never wrap focus between unrelated regions (e.g., Tab bar and content area should be separate navigation regions). |
| Focus is always visible | Focus ring or equivalent focus indicator must ALWAYS be visible when an element is focused via keyboard or gamepad. Never suppress focus indicators. |

| 规则 | 描述 |
|------|-------------|
| 屏幕打开 | 焦点放在最合理的可交互元素上——通常是 Primary 按钮、首个列表项，或若屏幕先前被访问过则为最后聚焦元素。绝不在非交互元素上。 |
| 屏幕关闭 / pop | 焦点返回触发导航的元素（打开屏幕的按钮、被选中的列表项）。若该元素不再存在，焦点移到 tab 顺序中最近的先前可交互元素。 |
| 模态打开 | 焦点陷阱在模态内。见 Modal Dialog 模式。 |
| 模态关闭 | 焦点返回触发模态的元素。 |
| 元素禁用 | 若聚焦元素变为禁用，焦点移到 tab 顺序中下一个可用可交互元素。 |
| 元素销毁 | 若聚焦元素从场景移除，焦点移到 tab 顺序中最近的先前元素。 |
| 无可交互元素的屏幕 | 焦点管理为无操作。确保返回/取消输入仍工作。 |
| Tab 键（键盘） | 按文档顺序（左到右、上到下）向前移动焦点穿越可交互元素。Shift+Tab 向后移动。 |
| 方向键（手柄） | 沿按下的空间方向移动焦点。手柄优先使用空间导航而非严格 tab 顺序。绝不在无关区域间环绕焦点（如标签栏和内容区应是独立导航区域）。 |
| 焦点始终可见 | 通过键盘或手柄聚焦元素时，焦点环或等效焦点指示器必须始终可见。绝不抑制焦点指示器。 |

---

#### Escape / Cancel

#### Escape / Cancel

**Category**: Navigation
**Status**: Draft

> The "go back" action is the most-used navigation input in all menu systems.
> It must be consistent across every screen with no exceptions.

**类别**：导航
**状态**：草稿

> "返回"操作是所有菜单系统中使用最频繁的导航输入。它必须在每个屏幕上保持一致，
> 无例外。

| Platform | Input | Behavior |
|----------|-------|---------|
| PC (keyboard) | Escape | Close top-most modal / go back one screen in stack / if at root screen (main menu), open "quit?" confirmation |
| PC (gamepad) | B (Xbox layout) / Circle (PS layout) | Same as Escape |
| Xbox | B button | Same as Escape |
| PlayStation | Circle button | Same as Escape |
| Nintendo Switch | B button | Same as Escape (NOTE: Nintendo uses B for confirm in some first-party titles — verify platform convention for this release and document the decision) |

| 平台 | 输入 | 行为 |
|----------|-------|---------|
| PC（键盘） | Escape | 关闭最顶层模态 / 在栈中返回一屏 / 若在根屏幕（主菜单），打开"退出？"确认 |
| PC（手柄） | B (Xbox 布局) / Circle (PS 布局) | 同 Escape |
| Xbox | B 按钮 | 同 Escape |
| PlayStation | Circle 按钮 | 同 Escape |
| Nintendo Switch | B 按钮 | 同 Escape（注意：某些任天堂第一方游戏中 B 用于确认——为本次发布验证平台约定并记录决策） |

**Rules**: This input must never be overridden to do something other than "go back / cancel." If a screen has no back action (e.g., the game is paused and the player must make a choice), Escape does nothing or shows a "you must choose" message — it does not navigate away. Every screen must define its Escape behavior explicitly in its UX spec.

**规则**：此输入绝不可被重写为"返回 / 取消"之外的任何操作。若屏幕无返回操作
（如游戏暂停且玩家必须做出选择），Escape 不做任何事或显示"你必须选择"消息——
它不导航离开。每个屏幕必须在其 UX 规格中显式定义其 Escape 行为。

---

## Feedback and Loading Patterns

## 反馈和加载模式

---

#### Loading State

#### Loading State

**Category**: Feedback
**Status**: Draft

**类别**：反馈
**状态**：草稿

| Scope | Pattern | Notes |
|-------|---------|-------|
| Full screen (initial load) | Full-screen loading screen with game art, progress bar (determinate if possible), tip text (optional). | Never use an empty black screen. Give the player something to read or look at. |
| Full screen (level transition) | Fade to black, loading screen, fade from black to new scene. | The fade removes the pop of the previous scene disappearing. |
| Component / inline | Spinner or skeleton placeholder replaces the loading component. Component does not shift layout when content loads. | Skeleton placeholder (grey boxes approximating content shape) is preferable to spinner for layout-heavy content — it prevents layout shift on load. |
| Background / async | No visual indication unless operation exceeds 2 seconds. After 2 seconds, show a small spinner or toast. | Do not show loading indicators for operations that complete in under 2 seconds — the flash of an indicator is more disruptive than waiting. |

| 范围 | 模式 | 注释 |
|-------|---------|-------|
| 全屏（初始加载） | 带游戏美术、进度条（如可能则确定）、提示文本（可选）的全屏加载屏幕。 | 绝不使用空白黑屏。给玩家可读或可看的内容。 |
| 全屏（关卡切换） | 淡到黑、加载屏幕、从黑淡到新场景。 | 淡入淡出消除了前一场景消失的突现。 |
| 组件 / 内联 | 转圈或骨架占位符替换加载组件。组件加载内容时不移动布局。 | 骨架占位符（近似内容形状的灰盒）比转圈更适合布局密集的内容——它防止加载时布局移动。 |
| 后台 / 异步 | 除非操作超过 2 秒否则无视觉指示。2 秒后显示小转圈或 toast。 | 不要为 2 秒内完成的操作显示加载指示器——指示器的闪烁比等待更具干扰性。 |

**Accessibility**: Loading states must announce to screen readers: "[Context] loading, please wait." Completion must announce "[Context] loaded." For full-screen loading, ensure the loading screen itself is navigable to screen readers — the tips text and any UI elements must be exposed.

**无障碍**：加载状态必须向屏幕阅读器朗读："[上下文] 加载中，请稍候。"完成必须
朗读"[上下文] 已加载。"对于全屏加载，确保加载屏幕本身可被屏幕阅读器导航——
提示文本和任何 UI 元素必须暴露。

---

#### Empty State

#### Empty State

**Category**: Feedback
**Status**: Draft

> Empty states are consistently the least-designed parts of game UIs. They are
> the difference between a player feeling "this is where I'll store my items"
> and "why is nothing here? did something break?" Every empty list and grid must
> have a designed empty state. The empty state is not an error — it is a starting
> point.

**类别**：反馈
**状态**：草稿

> 空状态始终是游戏 UI 中最少被设计的部分。它们是玩家感到"这是我存放物品的地方"
> 与"为什么这里什么都没有？是不是出问题了？"之间的区别。每个空列表和空网格必须
> 有设计的空状态。空状态不是错误——它是起点。

| Location | Empty State Content | Notes |
|----------|--------------------|----|
| Inventory (no items) | Icon (subtle, large, centered). Message: "Your inventory is empty." Sub-message: "Items you find on your journey will appear here." | Do not say "No items found" — "found" implies a failed search. |
| Quest Log (no active quests) | Icon. Message: "No active quests." Sub-message: "Talk to characters marked with [quest marker icon] to start a quest." | Give the player a clear action. |
| Achievements (none earned) | Icon. Message: "No achievements yet." List of hint achievements: "Try [Action] to earn your first achievement." | Gamified motivation, not just emptiness. |
| Search results (no matches) | Icon. Message: "No results for '[search term]'." Sub-message: "Try a different search or [browse all]." | Mirror the search term back at them. Give an alternative action. |

| 位置 | 空状态内容 | 注释 |
|----------|--------------------|----|
| 背包（无物品） | 图标（细微、大、居中）。消息："你的背包是空的。"子消息："你在旅途中找到的物品会出现在这里。" | 不要说"未找到物品"——"找到"暗示失败的搜索。 |
| 任务日志（无活动任务） | 图标。消息："无活动任务。"子消息："与标记为[任务标记图标]的角色交谈以开始任务。" | 给玩家清晰的操作。 |
| 成就（未获得） | 图标。消息："尚无成就。"提示成就列表："尝试[操作]来获得你的第一个成就。" | 游戏化激励，而非仅是空。 |
| 搜索结果（无匹配） | 图标。消息："'[搜索词]'无结果。"子消息："尝试不同搜索或[浏览全部]。" | 将搜索词反射回去。给替代操作。 |

**Rule**: Every empty state must include an icon, a message, and either a sub-message or an action button. A blank container with no explanation is never acceptable.

**规则**：每个空状态必须包含图标、消息，以及子消息或操作按钮。无解释的空白容器绝不
可接受。

---

#### Error State

#### Error State

**Category**: Feedback
**Status**: Draft

**类别**：反馈
**状态**：草稿

| Error Type | Pattern | Tone |
|-----------|---------|------|
| Input validation (form field) | Inline error message below the field. Error icon left of message. Red border on field (colorblind-safe with icon). | Neutral and specific — "Username must be 3-20 characters." Not "Invalid input." |
| Operation failed (save error, network error) | Toast notification for non-critical failures. Modal Dialog for critical failures (save file cannot be written). | Calm and actionable — "Save failed. Check storage space." Not "FATAL ERROR." |
| System error (crash, data corruption) | Full-screen error screen with error code, recovery options ("Restart Game," "Load last save"), and support contact. | Reassuring — acknowledge the problem, give the player agency. Never blame the player. |
| Soft error (action cannot be performed) | Toast or inline message. | Explanatory — "Not enough gold" not "Action unavailable." |

| 错误类型 | 模式 | 语气 |
|-----------|---------|------|
| 输入验证（表单字段） | 字段下方内联错误消息。消息左侧错误图标。字段红色边框（色盲安全带图标）。 | 中性且具体——"用户名必须为 3-20 个字符。"而非"无效输入。" |
| 操作失败（保存错误、网络错误） | 非关键失败用 Toast 通知。关键失败用 Modal Dialog（存档文件无法写入）。 | 平静且可操作——"保存失败。检查存储空间。"而非"致命错误。" |
| 系统错误（崩溃、数据损坏） | 带错误代码、恢复选项（"重启游戏"、"加载上次存档"）和支持联系方式的全屏错误屏幕。 | 令人安心——承认问题，给玩家主动权。绝不责备玩家。 |
| 软错误（操作无法执行） | Toast 或内联消息。 | 解释性——"金币不足"而非"操作不可用。" |

**Principle**: Error messages are never the player's fault. They are the game telling the player what happened and what to do next. Remove the word "invalid" from all error messages — replace with specific explanations.

**原则**：错误消息绝不是玩家的错。它们是游戏告诉玩家发生了什么以及下一步该做什么。
从所有错误消息中移除"无效"一词——替换为具体解释。

---

## Animation Standards

## 动画标准

> These timing values apply to ALL patterns in this library. When a pattern says
> "150ms ease-out," the easing function is defined here. Consistency in timing
> makes the UI feel like a single designed system rather than a collection of
> individual decisions.

> 这些时间值适用于本库中的所有模式。当模式说"150ms ease-out"时，缓动函数在此定义。
> 时间的一致性使 UI 感觉像单一设计的系统，而非个别决策的集合。

| Animation Type | Duration (ms) | Easing Function | Notes |
|---------------|--------------|----------------|-------|
| Button hover / focus enter | 80 | ease-out | Fast — snappy, not sluggish |
| Button hover / focus exit | 60 | ease-in | Slightly faster exit than entry |
| Button press scale down | 60 | ease-in | Immediate feedback |
| Button press scale up (release) | 80 | ease-out | Slightly bouncy feel |
| Screen push (enter) | 250 | ease-in-out | Screen slides in from right |
| Screen pop (exit) | 250 | ease-in-out | Screen slides out to right |
| Modal open | 200 | ease-out | Expands from center |
| Modal close | 150 | ease-in | Collapses faster than it opens |
| Toast enter | 200 | ease-out | Slides in from screen edge |
| Toast exit | 200 | ease-in | |
| Tab switch | 150 | ease-in-out | Content cross-fades or slides |
| Tooltip appear | 120 | ease-out | After 300-400ms delay |
| Tooltip disappear | 80 | ease-in | |
| Progress bar fill | 300 | ease-out | Value changes animate smoothly |
| Value flash (damage, gain) | 100ms on + 100ms off | linear | Brief, attention-catching |
| Dialogue text reveal (per character) | 30ms per character | linear | Configurable in accessibility settings |
| HUD damage flash | 80 | linear | White or red overlay, immediate |

| 动画类型 | 持续时间 (ms) | 缓动函数 | 注释 |
|---------------|--------------|----------------|-------|
| 按钮悬停 / 聚焦进入 | 80 | ease-out | 快——干脆，不迟缓 |
| 按钮悬停 / 聚焦退出 | 60 | ease-in | 退出略快于进入 |
| 按钮按下缩放下降 | 60 | ease-in | 即时反馈 |
| 按钮按下缩放上升（释放） | 80 | ease-out | 略有弹跳感 |
| 屏幕 Push（进入） | 250 | ease-in-out | 屏幕从右滑入 |
| 屏幕 Pop（退出） | 250 | ease-in-out | 屏幕向右滑出 |
| 模态打开 | 200 | ease-out | 从中心展开 |
| 模态关闭 | 150 | ease-in | 折叠快于打开 |
| Toast 进入 | 200 | ease-out | 从屏幕边缘滑入 |
| Toast 退出 | 200 | ease-in | |
| 标签切换 | 150 | ease-in-out | 内容交叉淡入淡出或滑动 |
| Tooltip 出现 | 120 | ease-out | 延迟 300-400ms 后 |
| Tooltip 消失 | 80 | ease-in | |
| 进度条填充 | 300 | ease-out | 值变化平滑动画 |
| 值闪烁（伤害、获得） | 100ms 开 + 100ms 关 | linear | 短暂、引人注意 |
| 对话文本显示（每字符） | 每字符 30ms | linear | 无障碍设置中可配置 |
| HUD 伤害闪烁 | 80 | linear | 白色或红色覆盖，即时 |

**Motion reduction overrides**: When motion reduction mode is enabled (see accessibility-requirements.md), all slide and scale animations are replaced with fades. Fade durations are reduced by 50%. Looping animations (indeterminate spinners, pulsing indicators) are replaced with static equivalents.

**减少动效覆盖**：减少动效模式启用时（见 accessibility-requirements.md），所有滑动
和缩放动画替换为淡入淡出。淡入淡出持续时间减少 50%。循环动画（不确定转圈、脉动
指示器）替换为静态等效物。

---

## Sound Standards

## 声音标准

> Every interactive event should have audio feedback. Sound is a primary feedback
> channel, not a decoration. The sounds defined here are event categories — the
> specific audio assets are defined in `docs/sound-bible.md`. This table maps
> interaction events to sound categories so the sound designer and UI programmer
> use the same vocabulary.

> 每个可交互事件都应有音频反馈。声音是主要反馈渠道，而非装饰。此处定义的声音是
> 事件类别——具体音频资源定义在 `docs/sound-bible.md`。此表将交互事件映射到声音
> 类别，以便声音设计师和 UI 程序员使用相同词汇。

| Interaction Event | Sound Category | Notes |
|------------------|---------------|-------|
| Button hover / focus | UI Hover | Subtle, short (< 80ms), non-fatiguing on rapid navigation. Hades uses a very quiet, high-frequency click that disappears into background on rapid nav. |
| Button (Primary) confirm | UI Confirm — Primary | Slightly more prominent than secondary confirm. The "yes, let's go" sound. |
| Button (Secondary) cancel / back | UI Cancel | Subtly downward in pitch. The "going back" sound. Mass Effect uses a clean, distinct swoosh for back navigation. |
| Button (Destructive) — opening confirmation | UI Warning | Distinct from standard confirm. Brief attention-catching sound. |
| Confirmation dialog — confirm destructive | UI Confirm — Destructive | Final, slightly weighted. The action is being taken. |
| Toggle ON | UI Toggle On | Brief, snappy, slightly bright. Celeste's accessibility toggles have a satisfying click-on sound. |
| Toggle OFF | UI Toggle Off | Same click family, slightly flatter. |
| Slider adjust | UI Slider | Subtle continuous sound while dragging. A single click per D-pad step. Never fatiguing. |
| Dropdown open | UI Expand | Brief, directional (opening feel). |
| Dropdown close / select | UI Select | Confirmation feel. |
| Tab switch | UI Tab | Horizontal movement feel. Distinct from vertical navigation. |
| Modal open | UI Modal Open | More prominent than standard navigation — draws attention. |
| Modal close (cancel) | UI Modal Close | Returns to previous context. |
| Toast — informational | UI Notification | Background-level, non-intrusive. |
| Toast — achievement | UI Achievement | Celebratory but not overlong. The player should feel rewarded, not interrupted. |
| Toast — warning | UI Warning — Toast | Distinct from error. Alert, not alarming. |
| Error state | UI Error | Friendly but clear. Not a harsh buzzer. Dark Souls uses a subtle dull thud for failed actions — communicates "no" without being harsh. |
| Success confirmation | UI Success | Clean and satisfying. |
| Ability activate | Gameplay — Ability Activate | In-world feel, distinct from pure UI. Part of game feel, not menu feel. |
| Damage received | Gameplay — Damage | See sound-bible.md for full specification. |
| Item pickup | Gameplay — Item Acquire | Brief, rewarding. |
| Level up / rank up | Gameplay — Progression | Celebratory, appropriately prominent. |
| Dialogue advance | UI Dialogue | Subtle, matches typewriter rhythm if typewriter is active. |

| 交互事件 | 声音类别 | 注释 |
|------------------|---------------|-------|
| 按钮悬停 / 聚焦 | UI 悬停 | 细微、短（< 80ms），快速导航时不疲劳。Hades 使用非常轻的高频点击，快速导航时融入背景。 |
| Button (Primary) 确认 | UI 确认——主要 | 比次要确认略显著。"对，开始吧"的声音。 |
| Button (Secondary) 取消 / 返回 | UI 取消 | 音高略微向下。"返回"的声音。Mass Effect 使用干净、独特的嗖嗖声用于返回导航。 |
| Button (Destructive)——打开确认 | UI 警告 | 与标准确认不同。短暂引人注意的声音。 |
| 确认对话框——确认破坏性操作 | UI 确认——破坏性 | 最终的、略带分量。操作正在执行。 |
| Toggle 开 | UI Toggle 开 | 简短、干脆、略明亮。Celeste 的无障碍 toggle 有令人满意的开启点击声。 |
| Toggle 关 | UI Toggle 关 | 同点击家族，略平。 |
| Slider 调节 | UI Slider | 拖动时的细微连续声音。每方向键步进一声点击。绝不疲劳。 |
| Dropdown 打开 | UI 展开 | 简短、方向性（展开感）。 |
| Dropdown 关闭 / 选择 | UI 选择 | 确认感。 |
| 标签切换 | UI 标签 | 水平移动感。与垂直导航不同。 |
| 模态打开 | UI 模态打开 | 比标准导航更显著——吸引注意。 |
| 模态关闭（取消） | UI 模态关闭 | 返回先前上下文。 |
| Toast——信息 | UI 通知 | 背景级，不侵入。 |
| Toast——成就 | UI 成就 | 庆祝性但不过长。玩家应感到被奖励，而非被打断。 |
| Toast——警告 | UI 警告——Toast | 与错误不同。警示，而非惊吓。 |
| 错误状态 | UI 错误 | 友好但清晰。非刺耳蜂鸣。Dark Souls 对失败操作使用细微的闷响——传达"不行"而不刺耳。 |
| 成功确认 | UI 成功 | 干净且令人满意。 |
| 技能激活 | 玩法——技能激活 | 世界内感，与纯 UI 不同。游戏感的一部分，非菜单感。 |
| 受到伤害 | 玩法——伤害 | 见 sound-bible.md 完整规格。 |
| 物品拾取 | 玩法——物品获得 | 简短、有奖励感。 |
| 升级 / 段位提升 | 玩法——进度 | 庆祝性、恰当地显著。 |
| 对话前进 | UI 对话 | 细微，若打字机激活则匹配打字机节奏。 |

---

## Open Questions

## 待解决问题

| Question | Owner | Deadline | Resolution |
|----------|-------|----------|-----------|
| [Does the engine's accessibility node system support screen reader announcements for toast notifications without requiring focus? Verify against engine-reference/godot/ for Godot 4.6.] | [ux-designer] | [Before first menu implementation] | [Unresolved] |
| [What is the platform-correct confirm/cancel button mapping for Nintendo Switch release? Nintendo first-party convention differs from Xbox/PlayStation.] | [producer] | [Before platform certification submission] | [Unresolved] |
| [Should damage numbers be pooled as Label3D nodes or rendered in a SubViewport? Verify performance budget in coordination with technical-director.] | [lead-programmer, ux-designer] | [Before combat HUD implementation] | [Unresolved] |
| [What is the maximum number of simultaneous toast notifications before the queue becomes visually overwhelming? Needs playtesting.] | [ux-designer] | [First playtesting session] | [Unresolved] |
| [Add question] | [Owner] | [Deadline] | [Resolution] |

| 问题 | 负责人 | 截止日期 | 解决状态 |
|----------|-------|----------|-----------|
| [引擎的无障碍节点系统是否支持在无需聚焦时为 toast 通知朗读屏幕阅读器公告？在 engine-reference/godot/ 中验证 Godot 4.6。] | [ux-designer] | [首个菜单实现前] | [未解决] |
| [Nintendo Switch 发行的平台正确确认/取消按钮映射是什么？任天堂第一方约定与 Xbox/PlayStation 不同。] | [producer] | [平台认证提交前] | [未解决] |
| [伤害数字应作为 Label3D 节点池化还是在 SubViewport 中渲染？与 technical-director 协调验证性能预算。] | [lead-programmer, ux-designer] | [战斗 HUD 实现前] | [未解决] |
| [队列在视觉上变得压倒性之前的最大同时 toast 通知数是多少？需要试玩测试。] | [ux-designer] | [首次试玩测试会话] | [未解决] |
| [添加问题] | [负责人] | [截止日期] | [解决状态] |