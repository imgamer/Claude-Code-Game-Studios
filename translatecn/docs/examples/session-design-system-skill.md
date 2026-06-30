# Example Session: Using /design-system to Author a GDD
# 示例会话：使用 /design-system 编写 GDD

**Context:** Solo indie dev, week 3 of project. `/map-systems` already ran and produced `design/gdd/systems-index.md`. The movement system is listed as MVP tier, Foundation priority.
**上下文：** 独立开发者，项目第 3 周。`/map-systems` 已运行并生成了 `design/gdd/systems-index.md`。移动系统被列为 MVP 层级，基础优先级。

**Starting State:**
**起始状态：**
- `design/gdd/game-concept.md` exists (survival-crafting game, 2D, Godot 4.6)
- `design/gdd/game-concept.md` 存在（生存制作游戏，2D，Godot 4.6）
- `design/gdd/systems-index.md` exists (15 systems identified, movement is first in design order)
- `design/gdd/systems-index.md` 存在（识别了 15 个系统，移动是设计顺序中的第一个）
- No per-system GDDs written yet
- 尚未编写任何单系统 GDD
- No upstream dependencies for movement (it is the foundation)
- 移动没有上游依赖（它是基础）

**Session Duration:** ~60 minutes
**会话时长：** 约 60 分钟
**Turns:** 14
**回合数：** 14
**Outcome:** Complete `design/gdd/movement-system.md` with all 8 required sections written to file, ready for `/design-review`
**结果：** 完整的 `design/gdd/movement-system.md`，所有 8 个必需章节已写入文件，准备好进行 `/design-review`

---

## Turn 1: User Invokes the Skill
## 回合 1：用户调用技能

**User:**
**用户：**
> /design-system movement

## Turn 2: Agent Loads Context and Presents the Plan
## 回合 2：智能体加载上下文并呈现计划

**Agent (game-designer, via /design-system):**
**智能体（game-designer，通过 /design-system）：**
> I'm loading context before we start:
> 开始之前我正在加载上下文：
>
> - Read `design/gdd/game-concept.md` — survival-crafting, exploration-heavy, lone survivor stranded on alien planet
> - 阅读 `design/gdd/game-concept.md` —— 生存制作、重探索、孤独幸存者被困在外星行星
> - Read `design/gdd/systems-index.md` — movement is **Foundation / MVP**, no upstream deps, downstream: combat, exploration, stamina
> - 阅读 `design/gdd/systems-index.md` —— 移动是**基础 / MVP**，无上游依赖，下游：战斗、探索、耐力
> - No prior GDD found for movement — starting fresh
> - 未找到移动的先前 GDD —— 从头开始
>
> **Technical Feasibility Pre-Check:**
> **技术可行性预检查：**
>
> | Domain | Signal |
> | 领域 | 信号 |
> |--------|--------|
> | Engine | Godot 4.6 — CharacterBody2D with move_and_slide() is well-supported |
> | 引擎 | Godot 4.6 —— CharacterBody2D 与 move_and_slide() 受良好支持 |
> | Physics | Jolt is now the default in 4.6 — 2D movement unaffected, but note for future 3D work |
> | 物理 | Jolt 现在是 4.6 的默认值 —— 2D 移动不受影响，但注意未来的 3D 工作 |
> | Downstream risk | Stamina system listed as downstream dep — movement must expose a stamina callback hook |
> | 下游风险 | 耐力系统列为下游依赖 —— 移动必须暴露耐力回调钩子 |
>
> The GDD template has **8 required sections**. We'll go one at a time:
> GDD 模板有**8 个必需章节**。我们将一次一个地进行：
> `Overview → Player Fantasy → Detailed Rules → Formulas → Edge Cases → Dependencies → Tuning Knobs → Acceptance Criteria`
>
> I'll show you a draft of each section, you approve or revise, then I write it to the file before we move on.
> 我会向你展示每个章节的草稿，你批准或修改，然后我将其写入文件再继续。
>
> Creating the file skeleton now with empty section headers — may I write `design/gdd/movement-system.md` with placeholders?
> 现在创建带有空章节标题的文件骨架——我可以将 `design/gdd/movement-system.md` 写入占位符吗？

**User:**
**用户：**
> Yes
> 是

**Agent writes** `design/gdd/movement-system.md` with 8 empty section headers.
**智能体写入** 带有 8 个空章节标题的 `design/gdd/movement-system.md`。

---

## Turn 3: Section 1 — Overview
## 回合 3：章节 1 —— Overview

**Agent:**
**智能体：**
> **Section 1 draft — Overview:**
> **章节 1 草稿 —— Overview：**
>
> > The movement system is the player's primary interface with the world. It governs how the lone survivor walks, runs, rolls, and traverses alien terrain. Movement is physically grounded — the planet has heavier-than-Earth gravity, and the survivor is physically exhausted — but remains responsive enough to stay out of the player's way. The system feeds directly into exploration pacing, combat positioning, and the stamina system.
> > 移动系统是玩家与世界交互的主要界面。它控制孤独幸存者如何行走、奔跑、翻滚和穿越外星地形。移动有物理基础——行星的重力大于地球，幸存者身体疲惫——但仍保持足够响应以不阻碍玩家。该系统直接馈入探索节奏、战斗定位和耐力系统。
>
> Does this capture what you want?
> 这捕获了你想要的吗？

**User:**
**用户：**
> Good but add something about the terrain — surfaces slow you down or have friction modifiers.
> 不错，但添加一些关于地形的内容——表面会减慢你或具有摩擦修饰符。

**Agent:**
**智能体：**
> Revised:
> 修改：
>
> > The movement system is the player's primary interface with the world. It governs how the lone survivor walks, runs, rolls, and traverses alien terrain. Movement is physically grounded — the planet has heavier-than-Earth gravity, and the survivor is physically exhausted — but remains responsive enough to stay out of the player's way. Terrain surfaces apply friction and speed modifiers (slick rock, sand, shallow water) that make navigation a meaningful skill. The system feeds directly into exploration pacing, combat positioning, and the stamina system.
> > 移动系统是玩家与世界交互的主要界面。它控制孤独幸存者如何行走、奔跑、翻滚和穿越外星地形。移动有物理基础——行星的重力大于地球，幸存者身体疲惫——但仍保持足够响应以不阻碍玩家。地形表面应用摩擦和速度修饰符（湿滑岩石、沙子、浅水）使导航成为一项有意义的技能。该系统直接馈入探索节奏、战斗定位和耐力系统。
>
> May I write this to Section 1 in the file?
> 我可以将此写入文件中的章节 1 吗？

**User:**
**用户：**
> Yes
> 是

**Agent writes Section 1 to file.**
**智能体将章节 1 写入文件。**

---

## Turn 4: Section 2 — Player Fantasy
## 回合 4：章节 2 —— Player Fantasy

**Agent:**
**智能体：**
> **Section 2 draft — Player Fantasy:**
> **章节 2 草稿 —— Player Fantasy：**
>
> > The player should feel like a tired but capable survivor — not a superhero, not helpless. Early game movement is deliberate and taxing; the player earns fluency over time. When traversing alien terrain, the goal is careful navigation with moments of urgency (running from threats, leaping a gap). The fantasy is "I know this terrain" — every surface type becomes readable and manageable as the player internalizes the friction system.
> > 玩家应该感觉自己像一个疲惫但有能力的幸存者——不是超级英雄，也不是无助。游戏早期的移动是刻意且费力的；玩家随时间获得流畅度。穿越外星地形时，目标是谨慎导航并伴有紧迫时刻（逃离威胁、跃过裂缝）。幻想是"我熟悉这片地形"——每种表面类型随着玩家内化摩擦系统而变得可读和可管理。
>
> Approve, revise, or change direction?
> 批准、修改，还是改变方向？

**User:**
**用户：**
> Love it. Write it.
> 喜欢。写吧。

**Agent writes Section 2 to file.**
**智能体将章节 2 写入文件。**

---

## Turn 5: Section 3 — Detailed Rules
## 回合 5：章节 3 —— Detailed Rules

**Agent:**
**智能体：**
> **Section 3 draft — Detailed Rules:**
> **章节 3 草稿 —— Detailed Rules：**
>
> > **Base States:**
> > **基础状态：**
> > - **Walk** (default): Full directional control, moderate speed, low stamina drain
> > - **行走**（默认）：全方向控制，中等速度，低耐力消耗
> > - **Run** (hold Shift): Faster, medium stamina drain per second — cancels automatically when stamina hits 0
> > - **奔跑**（按住 Shift）：更快，每秒中等耐力消耗——耐力达到 0 时自动取消
> > - **Roll/Dodge** (tap Space): Quick burst in movement direction, brief invincibility frames, high stamina cost (flat)
> > - **翻滚/闪避**（点击空格）：在移动方向上的快速冲刺，短暂无敌帧，高耐力成本（固定）
> > - **Idle**: Zero movement input, stamina regenerates at full rate
> > - **静止**：零移动输入，耐力以全速率恢复
> >
> > **Terrain Modifiers:**
> > **地形修饰符：**
> > - Normal ground: 1.0x speed multiplier, standard friction
> > - 普通地面：1.0x 速度倍数，标准摩擦
> > - Sand/loose soil: 0.8x speed, 1.1x stamina drain (harder to move)
> > - 沙子/松散土壤：0.8x 速度，1.1x 耐力消耗（更难移动）
> > - Slick rock (wet): 1.1x speed, reduced stop friction (player overshoots)
> > - 湿滑岩石（湿）：1.1x 速度，降低停止摩擦（玩家过冲）
> > - Shallow water (ankle-deep): 0.7x speed, no friction modifier
> > - 浅水（齐踝深）：0.7x 速度，无摩擦修饰符
> > - Deep water: movement disabled, replaced by swimming (separate system, out of scope)
> > - 深水：移动禁用，由游泳替代（独立系统，超出范围）
> >
> > **Collision:**
> > **碰撞：**
> > - CharacterBody2D + move_and_slide(); wall slide enabled
> > - CharacterBody2D + move_and_slide()；墙壁滑行启用
> > - Slopes up to 45° walked normally; >45° blocked
> > - 高达 45° 的斜坡正常行走；>45° 阻挡
> > - One-way platforms supported (drop-through with Down + Jump)
> > - 支持单向平台（使用下 + 跳穿越）
>
> A lot here — want me to trim or expand anything?
> 这里很多——你想让我删减或扩展什么吗？

**User:**
**用户：**
> Add that running is disabled when carrying a heavy object (like a large resource chunk).
> 添加携带重物（如大型资源块）时奔跑被禁用。

**Agent:**
**智能体：**
> Added to Run rule:
> 添加到奔跑规则：
> > - **Run** (hold Shift): Faster, medium stamina drain per second — cancels automatically when stamina hits 0. **Disabled when carrying heavy objects** (signals broadcast by inventory system).
> > - **奔跑**（按住 Shift）：更快，每秒中等耐力消耗——耐力达到 0 时自动取消。**携带重物时禁用**（信号由库存系统广播）。
>
> Write to file?
> 写入文件？

**User:**
**用户：**
> Yes
> 是

**Agent writes Section 3 to file.**
**智能体将章节 3 写入文件。**

---

## Turn 6: Section 4 — Formulas
## 回合 6：章节 4 —— Formulas

**Agent:**
**智能体：**
> **Section 4 draft — Formulas:**
> **章节 4 草稿 —— Formulas：**
>
> Let me define variables first, then the formulas:
> 让我先定义变量，然后是公式：
>
> | Variable | Type | Description |
> | 变量 | 类型 | 描述 |
> |----------|------|-------------|
> | `base_walk_speed` | float | Base walk speed in px/s (tunable) |
> | `base_walk_speed` | float | 基础行走速度，单位 px/s（可调） |
> | `run_multiplier` | float | Run speed = walk × this |
> | `run_multiplier` | float | 奔跑速度 = 行走 × 此值 |
> | `terrain_speed_mod` | float | Per-terrain multiplier (0.7–1.1) |
> | `terrain_speed_mod` | float | 每地形倍数（0.7–1.1） |
> | `stamina_drain_walk` | float | Stamina units/sec while walking |
> | `stamina_drain_walk` | float | 行走时每秒耐力单位 |
> | `stamina_drain_run` | float | Stamina units/sec while running |
> | `stamina_drain_run` | float | 奔跑时每秒耐力单位 |
> | `stamina_cost_roll` | float | Flat stamina cost per roll |
> | `stamina_cost_roll` | float | 每次翻滚的固定耐力成本 |
> | `terrain_drain_mod` | float | Per-terrain stamina drain multiplier |
> | `terrain_drain_mod` | float | 每地形耐力消耗倍数 |
>
> **Effective Speed:**
> **有效速度：**
> ```
> effective_speed = base_walk_speed × run_multiplier (if running, else 1.0) × terrain_speed_mod
> ```
>
> **Stamina Drain (per second):**
> **耐力消耗（每秒）：**
> ```
> drain_per_sec = stamina_drain_walk (or stamina_drain_run if running) × terrain_drain_mod
> ```
>
> **Roll Stamina Cost (flat):**
> **翻滚耐力成本（固定）：**
> ```
> stamina_after_roll = current_stamina - stamina_cost_roll
> (clamped to 0 — roll still executes, but player lands with 0 stamina)
> （限制为 0 —— 翻滚仍然执行，但玩家以 0 耐力着陆）
> ```
>
> Approve or adjust variable names/formulas?
> 批准或调整变量名/公式？

**User:**
**用户：**
> Looks right. Write it.
> 看起来对。写吧。

**Agent writes Section 4 to file.**
**智能体将章节 4 写入文件。**

---

## Turn 7–9: Sections 5, 6, 7
## 回合 7–9：章节 5、6、7

*(These follow the same pattern — agent drafts, user approves or requests minor tweaks, agent writes each section to file before moving on. Abbreviated here.)*
*（这些遵循相同模式——智能体起草，用户批准或请求小幅调整，智能体在继续之前将每个章节写入文件。此处简略。）*

**Section 5 — Edge Cases** covered:
**章节 5 —— Edge Cases** 涵盖：
- Player rolls into a wall (animation completes, no movement bonus)
- 玩家翻滚进墙（动画完成，无移动奖励）
- Stamina hits 0 mid-run (transitions to walk, no lurch)
- 耐力在奔跑中途达到 0（过渡到行走，无颠簸）
- Two terrain types overlapping (lower multiplier wins)
- 两种地形类型重叠（较低倍数获胜）
- Roll on slick rock (still applies slick friction to stopping, not to the roll itself)
- 湿滑岩石上翻滚（仍对停止应用湿滑摩擦，而非翻滚本身）

**Section 6 — Dependencies** covered:
**章节 6 —— Dependencies** 涵盖：
- **Stamina system** (downstream): movement exposes `on_stamina_event(type: String, amount: float)` signal
- **耐力系统**（下游）：移动暴露 `on_stamina_event(type: String, amount: float)` 信号
- **Inventory system** (downstream): listens to `carrying_heavy_object_changed(is_heavy: bool)` signal
- **库存系统**（下游）：监听 `carrying_heavy_object_changed(is_heavy: bool)` 信号
- **Input system**: movement direction from `InputMap` — standard Godot action names (`ui_left`, `ui_right`, etc.)
- **输入系统**：来自 `InputMap` 的移动方向——标准 Godot 动作名（`ui_left`、`ui_right` 等）
- **Terrain layer**: tile metadata drives terrain modifier lookup
- **地形层**：瓦片元数据驱动地形修饰符查找

**Section 7 — Tuning Knobs** covered:
**章节 7 —— Tuning Knobs** 涵盖：
- `base_walk_speed` (default: 120 px/s)
- `base_walk_speed`（默认：120 px/s）
- `run_multiplier` (default: 1.7)
- `run_multiplier`（默认：1.7）
- Per-terrain speed and drain modifiers (table of 4 terrain types)
- 每地形速度和消耗修饰符（4 种地形类型的表）
- `stamina_drain_walk`, `stamina_drain_run`, `stamina_cost_roll`
- `stamina_drain_walk`、`stamina_drain_run`、`stamina_cost_roll`

---

## Turn 10: Section 8 — Acceptance Criteria
## 回合 10：章节 8 —— Acceptance Criteria

**Agent:**
**智能体：**
> **Section 8 draft — Acceptance Criteria:**
> **章节 8 草稿 —— Acceptance Criteria：**
>
> These are the testable conditions that prove this system is complete:
> 这些是证明此系统完整的可测试条件：
>
> - [ ] Player walks at `base_walk_speed` on normal ground (measured with debug overlay)
> - [ ] 玩家在普通地面上以 `base_walk_speed` 行走（通过调试覆盖层测量）
> - [ ] Player runs at `base_walk_speed × run_multiplier` on normal ground
> - [ ] 玩家在普通地面上以 `base_walk_speed × run_multiplier` 奔跑
> - [ ] Run automatically cancels when stamina reaches 0, no input required
> - [ ] 耐力达到 0 时奔跑自动取消，无需输入
> - [ ] Roll executes with correct invincibility frames (verify via hitbox debug mode)
> - [ ] 翻滚以正确的无敌帧执行（通过碰撞箱调试模式验证）
> - [ ] Each terrain type applies the correct speed modifier (QA table in sprint plan)
> - [ ] 每种地形类型应用正确的速度修饰符（冲刺计划中的 QA 表）
> - [ ] Carrying heavy object disables run (test with inventory integration)
> - [ ] 携带重物禁用奔跑（与库存集成测试）
> - [ ] Stamina system receives `on_stamina_event` signals during walk, run, and roll
> - [ ] 耐力系统在行走、奔跑和翻滚期间接收 `on_stamina_event` 信号
> - [ ] All movement values come from exported variables — no hardcoded numbers in code
> - [ ] 所有移动值来自导出变量——代码中没有硬编码数字
>
> Does this cover what "done" means to you?
> 这覆盖了"完成"对你的含义吗？

**User:**
**用户：**
> Add one for slopes — make sure >45 degree slopes block movement.
> 为斜坡添加一项——确保 >45 度斜坡阻挡移动。

**Agent:**
**智能体：**
> Added:
> 已添加：
> - [ ] Slopes ≤45° traversed normally; slopes >45° block movement (player slides back)
> - [ ] ≤45° 斜坡正常穿越；>45° 斜坡阻挡移动（玩家滑回）
>
> Write to file?
> 写入文件？

**User:**
**用户：**
> Yes. Write it.
> 是。写吧。

**Agent writes Section 8 to file.**
**智能体将章节 8 写入文件。**

---

## Turn 11: Completion Summary
## 回合 11：完成摘要

**Agent:**
**智能体：**
> All 8 sections written to `design/gdd/movement-system.md`.
> 所有 8 个章节已写入 `design/gdd/movement-system.md`。
>
> **GDD Status:**
> **GDD 状态：**
> - ✅ Overview
> - ✅ Player Fantasy
> - ✅ Detailed Rules
> - ✅ Formulas
> - ✅ Edge Cases
> - ✅ Dependencies (exposes 2 signals: stamina_event, carrying_heavy)
> - ✅ Dependencies（暴露 2 个信号：stamina_event、carrying_heavy）
> - ✅ Tuning Knobs
> - ✅ Acceptance Criteria
>
> **Key decisions made:**
> **已做出的关键决策：**
> - Terrain modifiers as tile metadata (not code constants)
> - 地形修饰符作为瓦片元数据（非代码常量）
> - Rolling has a flat stamina cost (not per-second)
> - 翻滚有固定耐力成本（非每秒）
> - Heavy carry disables run (signal-driven, not hardcoded check)
> - 携带重物禁用奔跑（信号驱动，非硬编码检查）
>
> **Recommended next step:** Run `/design-review design/gdd/movement-system.md` before moving to the next system in your queue (stamina is next in dependency order, per systems-index).
> **推荐的下一步：** 在移动到队列中的下一个系统之前运行 `/design-review design/gdd/movement-system.md`（根据 systems-index，耐力是依赖顺序中的下一个）。
>
> The review will validate completeness, internal consistency, and catch anything that needs revision before you build on this GDD.
> 评审将验证完整性、内部一致性，并捕获在此 GDD 之上构建之前需要修订的任何内容。

---

## What This Example Demonstrates
## 本示例展示了什么

- **Incremental file writing**: each section written to disk immediately after approval — a session crash loses at most one in-progress section
- **增量文件写入**：每个章节在批准后立即写入磁盘——会话崩溃最多丢失一个进行中的章节
- **Template population**: the GDD template's 8 sections become concrete content through dialogue, not bulk generation
- **模板填充**：GDD 模板的 8 个章节通过对话变为具体内容，而非批量生成
- **Dependency signaling**: agent proactively surfaces downstream signal contracts (stamina, inventory) during the Dependencies section
- **依赖信号**：智能体在 Dependencies 章节期间主动呈现下游信号契约（耐力、库存）
- **Technical feasibility pre-check**: agent notes Jolt physics change before design starts, not after implementation breaks
- **技术可行性预检查**：智能体在设计开始之前标记 Jolt 物理变更，而非在实现中断之后
- **Section-by-section cycle**: no full draft dumped at once — each section is a small, reviewable unit
- **逐章节循环**：一次性不倾倒完整草稿——每个章节是小的、可审查的单元
- **Recovery**: if the session crashes, the agent re-reads the file on restart and resumes from the first empty section
- **恢复**：如果会话崩溃，智能体在重启时重新读取文件并从第一个空章节恢复
