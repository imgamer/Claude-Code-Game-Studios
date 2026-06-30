# Example Session: UX Pipeline — /ux-design → /ux-review → /team-ui
# 示例会话：UX 流水线 —— /ux-design → /ux-review → /team-ui

**Context:** Solo indie dev, Pre-Production phase. The HUD and inventory screen are the first UI to design. The game has a player journey doc and GDDs for inventory and combat.
**上下文：** 独立开发者，生产前阶段。HUD 和库存界面是要设计的第一个 UI。游戏有玩家旅程文档以及库存和战斗的 GDD。

**Starting State:**
**起始状态：**
- `design/gdd/inventory-system.md` — complete
- `design/gdd/inventory-system.md` —— 完整
- `design/gdd/combat-system.md` — complete
- `design/gdd/combat-system.md` —— 完整
- `design/player-journey.md` — complete (6-phase arc defined)
- `design/player-journey.md` —— 完整（已定义 6 阶段弧线）
- No UX specs yet
- 尚无 UX 规格
- `design/ux/` directory does not exist
- `design/ux/` 目录不存在

**Session Duration:** ~90 minutes across two sessions (HUD design, then inventory screen)
**会话时长：** 跨两次会话约 90 分钟（HUD 设计，然后库存界面）
**Turns:** 16
**回合数：** 16
**Outcome:** `design/ux/hud.md` and `design/ux/screens/inventory.md` created and reviewed; team-ui handed off for visual implementation
**结果：** 已创建并评审 `design/ux/hud.md` 和 `design/ux/screens/inventory.md`；team-ui 已交接进行视觉实现

---

## Part 1: HUD Design with /ux-design
## 第 1 部分：使用 /ux-design 进行 HUD 设计

### Turn 1: User Starts HUD Design
### 回合 1：用户开始 HUD 设计

**User:**
**用户：**
> /ux-design hud

### Turn 2: Agent Loads Context and Presents the Approach
### 回合 2：智能体加载上下文并呈现方法

**Agent (ux-designer, via /ux-design):**
**智能体（ux-designer，通过 /ux-design）：**
> Starting HUD design. Loading context...
> 开始 HUD 设计。加载上下文...
>
> - `design/player-journey.md` read — key states: Calm Exploration, Tension (threat detected), Crisis (low HP/stamina), Combat
> - 阅读 `design/player-journey.md` —— 关键状态：平静探索、紧张（检测到威胁）、危机（低 HP/耐力）、战斗
> - `design/gdd/combat-system.md` — HUD needs: HP bar, stamina bar, active ability cooldowns (up to 3), damage numbers
> - `design/gdd/combat-system.md` —— HUD 需要：HP 条、耐力条、激活技能冷却（最多 3 个）、伤害数字
> - `design/gdd/inventory-system.md` — HUD needs: quick slots (4 slots), active item indicator
> - `design/gdd/inventory-system.md` —— HUD 需要：快捷栏（4 个槽位）、激活物品指示器
>
> **HUD Philosophy question:** What is the player's primary attention focus during play?
> **HUD 理念问题：** 游玩期间玩家的主要注意力焦点是什么？
>
> This determines how aggressive the HUD should be:
> 这决定 HUD 应该有多显眼：
> - **Option A: Diegetic (low presence)** — HP shown only when damaged, stamina shown only when draining. World-first, HUD recedes unless urgent.
> - **选项 A：叙事性（低存在感）** —— HP 仅在受伤时显示，耐力仅在消耗时显示。世界优先，HUD 在非紧急时退居。
> - **Option B: Persistent minimal** — HP and stamina always visible, small and unobtrusive. Quick reference without mode-switching.
> - **选项 B：持续性最小** —— HP 和耐力始终可见，小而不显眼。快速参考而无需切换模式。
> - **Option C: Full tactical** — All info always visible (HP, stamina, cooldowns, map, inventory slots). Player always has full awareness.
> - **选项 C：全战术** —— 所有信息始终可见（HP、耐力、冷却、地图、库存槽位）。玩家始终有完整感知。
>
> Given your survival game and "lone survivor" fantasy, A or B is most consistent with the genre.
> 鉴于你的生存游戏和"孤独幸存者"幻想，A 或 B 与该类型最一致。

**User:**
**用户：**
> B — I want it always there but minimal. The player is always managing resources.
> B —— 我希望它始终在那里但最小化。玩家始终在管理资源。

### Turn 3: Agent Drafts HUD Philosophy Section
### 回合 3：智能体起草 HUD 理念章节

**Agent:**
**智能体：**
> **HUD Philosophy draft:**
> **HUD 理念草稿：**
>
> > **Approach: Persistent Minimal**
> > **方法：持续性最小**
> > The HUD is always visible but occupies minimal screen real estate. The player is a resource-managing survivor — denying HP/stamina visibility creates frustration, not tension. All critical stats are readable at a glance without interrupting exploration focus. Elements scale in visual weight when they become urgent (HP below 30% pulses, stamina at 0 flashes), then return to minimal state.
> > HUD 始终可见但占用最小屏幕空间。玩家是管理资源的幸存者——拒绝 HP/耐力可见性会造成挫败，而非紧张。所有关键状态可一眼读取而不中断探索焦点。元素在变紧急时增加视觉权重（HP 低于 30% 脉动，耐力为 0 闪烁），然后返回最小状态。
>
> May I write this to `design/ux/hud.md` as the Philosophy section?
> 我可以将此作为 Philosophy 章节写入 `design/ux/hud.md` 吗？

**User:**
**用户：**
> Yes
> 是

**Agent creates `design/ux/hud.md` and writes Philosophy section.**
**智能体创建 `design/ux/hud.md` 并写入 Philosophy 章节。**

*(Turns 4–7 continue the section-by-section HUD authoring: Info Architecture, Zones, Element Specs, State Machine, Visual Budget, Platform Adaptation — each drafted, approved, and written to file.)*
*（回合 4–7 继续逐章节的 HUD 编写：信息架构、区域、元素规格、状态机、视觉预算、平台适配——每个章节起草、批准并写入文件。）*

---

**Final HUD structure after all sections:**
**所有章节完成后的最终 HUD 结构：**

```
design/ux/hud.md
  - Philosophy: Persistent Minimal
  - Info Architecture: HP (top-left), Stamina (top-left below HP), Quick Slots (bottom-center),
    Ability Cooldowns (bottom-right), Damage Numbers (world-space)
  - Zones: 4 defined (top-left vitals, bottom-center interaction, bottom-right abilities, world-space)
  - Element Specs: 6 elements with dimensions, font sizes, opacity rules
  - State Machine: Calm / Tension / Crisis / Combat (4 states, transition triggers)
  - Visual Budget: Max 8% screen coverage, 3 simultaneous animations
  - Platform Adaptation: Controller (aim reticle added), Mobile (out of scope v1)
```

---

## Part 2: Inventory Screen with /ux-design
## 第 2 部分：使用 /ux-design 设计库存界面

### Turn 8: User Starts Inventory Screen
### 回合 8：用户开始库存界面

**User:**
**用户：**
> /ux-design screen inventory

### Turn 9: Agent Scans GDD and Player Journey
### 回合 9：智能体扫描 GDD 和玩家旅程

**Agent:**
**智能体：**
> Loading context for inventory screen...
> 为库存界面加载上下文...
>
> From `design/gdd/inventory-system.md`:
> 来自 `design/gdd/inventory-system.md`：
> - Grid inventory, 6×8 slots
> - 网格库存，6×8 槽位
> - Items have weight (affects movement) and stack size
> - 物品有重量（影响移动）和堆叠大小
> - Player can equip, drop, inspect, split stacks
> - 玩家可以装备、丢弃、检查、拆分堆叠
>
> From `design/player-journey.md`:
> 来自 `design/player-journey.md`：
> - Inventory opens frequently during Calm Exploration
> - 库存在平静探索期间频繁打开
> - Players should feel "organized and in control" when opening inventory
> - 玩家打开库存时应感觉"有条不紊且受控"
> - Anxiety moment: "I need to decide what to drop" — should be clear, not panicked
> - 焦虑时刻："我需要决定丢弃什么"——应该清晰，不慌张
>
> **Player Need (from journey):** *I need to quickly assess what I'm carrying, decide what to keep or drop, and get back into the world.*
> **玩家需求（来自旅程）：** *我需要快速评估携带的物品，决定保留或丢弃什么，然后回到世界中。*
>
> **Key design question:** How do players navigate to inventory actions?
> **关键设计问题：** 玩家如何导航到库存操作？
> - **Option A: Right-click context menu** — hover item → right-click → menu appears (Escape/common)
> - **选项 A：右键上下文菜单** —— 悬停物品 → 右键 → 菜单出现（Escape/常见）
> - **Option B: Drag-based** — drag to drop zone to drop; drag to equipment slot to equip (Stardew Valley)
> - **选项 B：基于拖拽** —— 拖到丢弃区以丢弃；拖到装备槽以装备（Stardew Valley）
> - **Option C: Inspect panel** — click item → details panel appears on right with action buttons
> - **选项 C：检查面板** —— 点击物品 → 右侧出现详情面板，带操作按钮

**User:**
**用户：**
> B — drag-based. I want it tactile.
> B —— 基于拖拽。我希望它有触感。

*(Turns 10–12 continue section-by-section for the inventory screen spec: Player Need, Layout Zones, States, Interaction Map, Data Requirements, Events Fired, Accessibility, Localization.)*
*（回合 10–12 继续库存界面规格的逐章节：玩家需求、布局区域、状态、交互图、数据需求、触发事件、可访问性、本地化。）*

---

## Part 3: /ux-review
## 第 3 部分：/ux-review

### Turn 13: User Runs Review
### 回合 13：用户运行评审

**User:**
**用户：**
> /ux-review design/ux/

### Turn 14: Agent Reviews Both Documents
### 回合 14：智能体评审两份文档

**Agent (via /ux-review):**
**智能体（通过 /ux-review）：**
> Reviewing all UX specs in `design/ux/`...
> 评审 `design/ux/` 中的所有 UX 规格...
>
> ---
> **HUD Review (`hud.md`):**
> **HUD 评审（`hud.md`）：**
>
> | Check | Status | Notes |
> | 检查 | 状态 | 备注 |
> |-------|--------|-------|
> | GDD alignment — all required elements present | ✅ | HP, stamina, quick slots, cooldowns all specced |
> | GDD 对齐 —— 所有必需元素存在 | ✅ | HP、耐力、快捷栏、冷却都已规格化 |
> | State machine coverage | ✅ | All 4 gameplay states covered |
> | 状态机覆盖 | ✅ | 覆盖所有 4 种游戏状态 |
> | Accessibility tier | ⚠️ | No colorblind mode specified for HP/stamina bars (red/yellow convention) |
> | 可访问性层级 | ⚠️ | 未为 HP/耐力条指定色盲模式（红/黄约定） |
> | Visual budget | ✅ | 8% screen coverage, within budget |
> | 视觉预算 | ✅ | 8% 屏幕覆盖率，在预算内 |
> | Platform notes | ✅ | Controller variant documented |
> | 平台备注 | ✅ | 控制器变体已记录 |
>
> **Inventory Screen Review (`screens/inventory.md`):**
> **库存界面评审（`screens/inventory.md`）：**
>
> | Check | Status | Notes |
> | 检查 | 状态 | 备注 |
> |-------|--------|-------|
> | Player Need stated | ✅ | |
> | 已声明玩家需求 | ✅ | |
> | All GDD actions covered | ✅ | Equip, drop, inspect, split all in interaction map |
> | 覆盖所有 GDD 操作 | ✅ | 装备、丢弃、检查、拆分都在交互图中 |
> | Drag-drop interaction fully specified | ⚠️ | Drop target behavior when inventory is full not defined — what happens? |
> | 拖拽交互完全指定 | ⚠️ | 库存已满时的丢弃目标行为未定义——会发生什么？ |
> | Accessibility: keyboard alternative for drag | ❌ | MISSING — drag-only with no keyboard path fails motor accessibility |
> | 可访问性：拖拽的键盘替代 | ❌ | 缺失 —— 仅拖拽无键盘路径违反运动可访问性 |
> | Localization: all strings tagged | ✅ | |
> | 本地化：所有字符串已标记 | ✅ | |
> | Data events fired | ✅ | `item_equipped`, `item_dropped`, `stack_split` all mapped |
> | 触发的数据事件 | ✅ | `item_equipped`、`item_dropped`、`stack_split` 全部映射 |
>
> ---
> **Verdict: NEEDS REVISION**
> **裁决：需要修订**
>
> Two issues must be resolved before handoff to /team-ui:
> 两个问题必须在交接给 /team-ui 之前解决：
>
> 1. **BLOCKING (Accessibility):** Inventory screen has no keyboard alternative to drag-drop. Motor accessibility requires a non-drag interaction path. Add a "press X to pick up / press X again to place" toggle, or a details panel with keyboard-accessible action buttons.
> 1. **阻塞（可访问性）：** 库存界面没有拖拽的键盘替代。运动可访问性需要非拖拽交互路径。添加"按 X 拾取 / 再按 X 放置"切换，或带键盘可访问操作按钮的详情面板。
>
> 2. **ADVISORY (HUD):** HP/stamina bars use color alone to convey state. Add a secondary indicator (shape, icon, or pattern) for colorblind users. Can be resolved in visual pass.
> 2. **建议（HUD）：** HP/耐力条仅使用颜色传达状态。为色盲用户添加次级指示器（形状、图标或图案）。可在视觉阶段解决。
> 3. **ADVISORY (Inventory):** Full-inventory drop behavior is unspecified. Resolve in GDD before implementation, or document as out-of-scope.
> 3. **建议（库存）：** 库存满时的丢弃行为未指定。在实现之前在 GDD 中解决，或记录为超出范围。

### Turn 15: User Resolves Blocking Issue
### 回合 15：用户解决阻塞问题

**User:**
**用户：**
> For keyboard: press F to pick up item at cursor, press F again on target slot to place it. Add this to the inventory spec.
> 对于键盘：按 F 拾取光标处的物品，再按 F 在目标槽位放置。将此添加到库存规格。

**Agent:**
**智能体：**
> Adding to Interaction Map under "Keyboard / Controller Path":
> 在"键盘 / 控制器路径"下添加到交互图：
>
> > Press F (or South button) to pick up item at cursor. Item enters "held" state with visual indicator. Press F again on target slot to place. Press Escape to cancel and return item.
> > 按 F（或 South 按钮）拾取光标处的物品。物品进入"持有"状态并带视觉指示器。再按 F 在目标槽位放置。按 Escape 取消并返回物品。
>
> May I write this to `design/ux/screens/inventory.md` and re-run the accessibility check?
> 我可以将此写入 `design/ux/screens/inventory.md` 并重新运行可访问性检查吗？

**User:**
**用户：**
> Yes
> 是

**Agent writes update. Re-checks accessibility — now passes.**
**智能体写入更新。重新检查可访问性 —— 现在通过。**

---

## Part 4: Handoff to /team-ui
## 第 4 部分：交接给 /team-ui

### Turn 16: User Kicks Off Team UI
### 回合 16：用户启动 Team UI

**User:**
**用户：**
> /team-ui

**Agent (via /team-ui):**
**智能体（通过 /team-ui）：**
> Running UX pipeline context check...
> 运行 UX 流水线上下文检查...
>
> - `design/ux/hud.md` — reviewed ✅
> - `design/ux/hud.md` —— 已评审 ✅
> - `design/ux/screens/inventory.md` — reviewed ✅
> - `design/ux/screens/inventory.md` —— 已评审 ✅
> - `/ux-review` verdict: NEEDS REVISION was resolved — current state: APPROVED ✅
> - `/ux-review` 裁决：需要修订已解决 —— 当前状态：已批准 ✅
>
> Proceeding to **Phase 2: Visual Design**
> 进入**阶段 2：视觉设计**
>
> Handing off to `art-director` for visual spec (color palette, typography, iconography aligned to art bible)...
> 交接给 `art-director` 进行视觉规格（调色板、排版、图标与艺术圣经对齐）...
>
> *(team-ui continues through visual design → layout implementation → accessibility audit → final review)*
> *（team-ui 继续通过视觉设计 → 布局实现 → 可访问性审计 → 最终评审）*

---

## What This Example Demonstrates
## 本示例展示了什么

- **Context-driven design**: agent reads player-journey.md to ground HUD decisions in player emotional state, not just feature lists
- **上下文驱动设计**：智能体阅读 player-journey.md 以将 HUD 决策建立在玩家情绪状态之上，而不只是功能列表
- **UX review is a hard gate**: `/team-ui` checks for a passing `/ux-review` before proceeding to visual design
- **UX 评审是硬性关卡**：`/team-ui` 在进入视觉设计之前检查是否有通过的 `/ux-review`
- **Accessibility caught early**: the missing keyboard alternative was flagged by review, not discovered by a QA tester in the final week
- **可访问性早期捕获**：缺失的键盘替代由评审标记，而非在最后一周由 QA 测试人员发现
- **Blocking vs. advisory**: the missing keyboard path was BLOCKING (stops handoff); the colorblind colors were ADVISORY (can be fixed in visual pass)
- **阻塞 vs. 建议**：缺失的键盘路径是阻塞（停止交接）；色盲颜色是建议（可在视觉阶段修复）
- **Section-by-section UX authoring**: same incremental pattern as `/design-system` — each section written to file before moving on
- **逐章节 UX 编写**：与 `/design-system` 相同的增量模式 —— 每个章节在继续之前写入文件
- **Separate HUD and screen files**: `design/ux/hud.md` is the whole-game HUD; per-screen specs live in `design/ux/screens/`
- **分离的 HUD 和界面文件**：`design/ux/hud.md` 是全游戏 HUD；单屏规格位于 `design/ux/screens/`
- **Pattern library enforced by /team-ui**: after this session, inventory drag-drop becomes a documented pattern in `design/ux/interaction-patterns.md` for all future screens to reference
- **由 /team-ui 强制的模式库**：此会话之后，库存拖放成为 `design/ux/interaction-patterns.md` 中记录的模式，供所有未来界面引用
