# Example Session: /adopt — Brownfield Project Onboarding
# 示例会话：/adopt —— 棕地项目接入

**Context:** Developer has been building a game for 3 months before discovering this agent system. They have code, some design docs, and rough notes — but nothing in the right format for the skills to use.
**上下文：** 开发者在发现这个智能体系统之前已经构建游戏 3 个月了。他们有代码、一些设计文档和粗略笔记——但没有任何内容符合技能使用的正确格式。

**Starting State:**
**起始状态：**
- `src/gameplay/` — ~4000 lines of Godot 4.6 GDScript
- `src/gameplay/` —— 约 4000 行 Godot 4.6 GDScript
- `design/` — 3 markdown files with rough design notes (not in GDD template format)
- `design/` —— 3 个带有粗略设计笔记的 markdown 文件（不是 GDD 模板格式）
- No `design/gdd/` directory
- 没有 `design/gdd/` 目录
- No `docs/architecture/` directory
- 没有 `docs/architecture/` 目录
- No `production/` directory
- 没有 `production/` 目录
- No `design/gdd/systems-index.md`
- 没有 `design/gdd/systems-index.md`
- CLAUDE.md exists (engine configured: Godot 4.6)
- CLAUDE.md 存在（已配置引擎：Godot 4.6）

**Session Duration:** ~30 minutes
**会话时长：** 约 30 分钟
**Turns:** 8
**回合数：** 8
**Outcome:** Adoption plan written, most urgent gap (missing system index) fixed immediately
**结果：** 接入计划已编写，最紧急的差距（缺失的系统索引）已立即修复

---

## Turn 1: User Invokes /adopt
## 回合 1：用户调用 /adopt

**User:**
**用户：**
> /adopt

## Turn 2: Agent Detects Project State
## 回合 2：智能体检测项目状态

**Agent (technical-director, via /adopt, context: fork):**
**智能体（technical-director，通过 /adopt，上下文：fork）：**
> Running brownfield audit...
> 运行棕地审计...
>
> **Phase 1: Project State Detection**
> **阶段 1：项目状态检测**
>
> | Artifact Category | Found | Notes |
> | 产物类别 | 找到 | 备注 |
> |------------------|-------|-------|
> | Engine configuration | ✅ | Godot 4.6 in CLAUDE.md |
> | 引擎配置 | ✅ | CLAUDE.md 中的 Godot 4.6 |
> | Source code | ✅ | ~4000 lines in `src/gameplay/` |
> | 源代码 | ✅ | `src/gameplay/` 中约 4000 行 |
> | Design docs | ⚠️ | 3 files in `design/`, not in GDD format |
> | 设计文档 | ⚠️ | `design/` 中有 3 个文件，不是 GDD 格式 |
> | Architecture docs | ❌ | `docs/architecture/` missing |
> | 架构文档 | ❌ | `docs/architecture/` 缺失 |
> | Systems index | ❌ | `design/gdd/systems-index.md` missing |
> | 系统索引 | ❌ | `design/gdd/systems-index.md` 缺失 |
> | Production tracking | ❌ | `production/` directory missing |
> | 生产跟踪 | ❌ | `production/` 目录缺失 |
> | Sprint/story files | ❌ | None |
> | 冲刺/故事文件 | ❌ | 无 |
>
> **Phase 2: Format Audit (existing files)**
> **阶段 2：格式审计（现有文件）**
>
> Checking internal structure of the 3 design files...
> 检查 3 个设计文件的内部结构...
>
> *`design/combat-notes.md`:*
> *`design/combat-notes.md`：*
> - Has informal notes, no GDD template sections
> - 包含非正式笔记，没有 GDD 模板章节
> - Contains: some rule descriptions, a damage formula, rough balance numbers
> - 包含：一些规则描述、一个伤害公式、粗略的平衡数值
> - Missing from GDD template: Overview, Player Fantasy, Edge Cases, Dependencies, Tuning Knobs, Acceptance Criteria (6 of 8 sections)
> - GDD 模板缺失：Overview、Player Fantasy、Edge Cases、Dependencies、Tuning Knobs、Acceptance Criteria（8 个章节中的 6 个）
>
> *`design/crafting-ideas.md`:*
> *`design/crafting-ideas.md`：*
> - Brainstorm-style notes, no structure
> - 头脑风暴式笔记，没有结构
> - No formulas, no acceptance criteria
> - 没有公式，没有验收标准
> - Represents early concept thinking — useful as input for a GDD, not yet a GDD
> - 代表早期概念思考——可作为 GDD 的输入，但还不是 GDD
>
> *`design/inventory.md`:*
> *`design/inventory.md`：*
> - Closest to a GDD — has Overview and Rules sections
> - 最接近 GDD——有 Overview 和 Rules 章节
> - Missing: Player Fantasy, Formulas, Edge Cases, Tuning Knobs, Acceptance Criteria (5 of 8 sections)
> - 缺失：Player Fantasy、Formulas、Edge Cases、Tuning Knobs、Acceptance Criteria（8 个章节中的 5 个）
>
> **Phase 3: Gap Classification**
> **阶段 3：差距分类**
>
> | Gap | Severity | Reason |
> | 差距 | 严重性 | 原因 |
> |-----|----------|--------|
> | No systems index | BLOCKING | `/design-system`, `/create-stories`, `/gate-check` all require it |
> | 无系统索引 | BLOCKING | `/design-system`、`/create-stories`、`/gate-check` 都需要它 |
> | GDD files not in template format | HIGH | Skills cannot reference TR-IDs from malformed GDDs |
> | GDD 文件不是模板格式 | HIGH | 技能无法从格式错误的 GDD 引用 TR-ID |
> | No architecture docs | HIGH | Cannot run `/architecture-review` or `/create-control-manifest` |
> | 无架构文档 | HIGH | 无法运行 `/architecture-review` 或 `/create-control-manifest` |
> | No production tracking | MEDIUM | Sprint and story skills won't work, but not needed until production phase |
> | 无生产跟踪 | MEDIUM | 冲刺和故事技能无法工作，但在生产阶段之前不需要 |
> | `design/crafting-ideas.md` — pre-GDD content | LOW | Useful as input; needs full GDD authoring before it blocks anything |
> | `design/crafting-ideas.md` —— GDD 前内容 | LOW | 可作为输入；在阻塞任何内容之前需要完整的 GDD 编写 |

## Turn 3: Agent Presents the Migration Plan
## 回合 3：智能体呈现迁移计划

**Agent:**
**智能体：**
> **Phase 4: Migration Plan**
> **阶段 4：迁移计划**
>
> 7 ordered steps to bring your project into full alignment. NEVER overwrites your existing content — each step fills gaps only.
> 7 个有序步骤使你的项目完全对齐。绝不覆盖你的现有内容——每一步仅填补差距。
>
> | # | Step | Skill | Priority | Notes |
> | # | 步骤 | 技能 | 优先级 | 备注 |
> |---|------|-------|----------|-------|
> | 1 | Create `design/gdd/systems-index.md` | `/map-systems` or `/adopt` inline | BLOCKING | Unblocks all downstream skills |
> | 1 | 创建 `design/gdd/systems-index.md` | `/map-systems` 或 `/adopt` 内联 | BLOCKING | 解除所有下游技能阻塞 |
> | 2 | Retrofit `design/inventory.md` → GDD format | `/design-system retrofit design/inventory.md` | HIGH | Adds 5 missing sections, keeps your existing content |
> | 2 | 改造 `design/inventory.md` → GDD 格式 | `/design-system retrofit design/inventory.md` | HIGH | 添加 5 个缺失章节，保留你的现有内容 |
> | 3 | Retrofit `design/combat-notes.md` → GDD format | `/design-system retrofit design/combat-notes.md` | HIGH | Adds 6 missing sections |
> | 3 | 改造 `design/combat-notes.md` → GDD 格式 | `/design-system retrofit design/combat-notes.md` | HIGH | 添加 6 个缺失章节 |
> | 4 | Convert `design/crafting-ideas.md` → full GDD | `/design-system crafting` (new authoring, uses ideas as input) | MEDIUM | Not a retrofit — too informal to retrofit |
> | 4 | 将 `design/crafting-ideas.md` 转换为完整 GDD | `/design-system crafting`（全新编写，使用想法作为输入） | MEDIUM | 不是改造——太不正式而无法改造 |
> | 5 | Create architecture ADRs from existing code | `/reverse-document` + `/architecture-decision` | HIGH | Captures decisions already made in code |
> | 5 | 从现有代码创建架构 ADR | `/reverse-document` + `/architecture-decision` | HIGH | 捕获代码中已做出的决策 |
> | 6 | Create master architecture document | `/create-architecture` | HIGH | After ADRs |
> | 6 | 创建主架构文档 | `/create-architecture` | HIGH | 在 ADR 之后 |
> | 7 | Set up production tracking | `/sprint-plan new` | MEDIUM | After architecture phase |
> | 7 | 设置生产跟踪 | `/sprint-plan new` | MEDIUM | 在架构阶段之后 |
>
> **Important:** Step 1 must happen before any other step. Everything else can be done in parallel or reordered based on your priorities.
> **重要：** 第 1 步必须在任何其他步骤之前发生。其他所有内容可以并行完成或根据你的优先级重新排序。
>
> May I write this plan to `docs/adoption-plan-2026-03-12.md`?
> 我可以将此计划写入 `docs/adoption-plan-2026-03-12.md` 吗？

**User:**
**用户：**
> Yes. Write it.
> 是的，写吧。

**Agent writes adoption plan.**
**智能体编写接入计划。**

## Turn 4: Agent Offers to Fix the Most Urgent Gap
## 回合 4：智能体提议修复最紧急的差距

**Agent:**
**智能体：**
> The BLOCKING gap is the missing `design/gdd/systems-index.md`.
> BLOCKING 差距是缺失的 `design/gdd/systems-index.md`。
>
> I can create one now by reading your existing code and design docs to infer what systems you've built. This is faster than running `/map-systems` from scratch — I'll populate it based on what already exists, then you confirm or adjust.
> 我现在可以通过阅读你现有的代码和设计文档来推断你构建了哪些系统，从而创建一个。这比从头运行 `/map-systems` 更快——我会根据已存在的内容填充它，然后你确认或调整。
>
> Would you like me to do that now?
> 你希望我现在这样做吗？

**User:**
**用户：**
> Yes, do it.
> 是的，做吧。

## Turn 5: Agent Reads Codebase and Drafts Systems Index
## 回合 5：智能体阅读代码库并起草系统索引

**Agent:**
**智能体：**
> Reading `src/gameplay/` to infer systems...
> 阅读 `src/gameplay/` 以推断系统...
>
> Found these system boundaries in the code:
> 在代码中找到这些系统边界：
>
> - `src/gameplay/combat/` — damage calculation, hit-detection, status effects
> - `src/gameplay/combat/` —— 伤害计算、命中检测、状态效果
> - `src/gameplay/inventory/` — item management, weight, stacks
> - `src/gameplay/inventory/` —— 物品管理、重量、堆叠
> - `src/gameplay/crafting/` — recipe lookup, result generation (references inventory)
> - `src/gameplay/crafting/` —— 配方查找、结果生成（引用库存）
> - `src/gameplay/movement/` — CharacterBody2D, terrain modifiers (referenced by combat for positioning)
> - `src/gameplay/movement/` —— CharacterBody2D、地形修饰符（被战斗引用以确定位置）
> - `src/gameplay/ui/` — HUD, inventory screen, dialogue box
> - `src/gameplay/ui/` —— HUD、库存界面、对话框
> - `src/gameplay/world/` — tile map, terrain types, chunk loading
> - `src/gameplay/world/` —— 瓦片地图、地形类型、区块加载
>
> **Draft systems index:**
> **草拟系统索引：**
>
> | System | Category | Priority | Dependencies | GDD Status |
> | 系统 | 类别 | 优先级 | 依赖 | GDD 状态 |
> |--------|----------|----------|--------------|------------|
> | Movement | Foundation | MVP | None | ❌ Missing |
> | 移动 | 基础 | MVP | 无 | ❌ 缺失 |
> | World/Terrain | Foundation | MVP | Movement | ❌ Missing |
> | 世界/地形 | 基础 | MVP | 移动 | ❌ 缺失 |
> | Combat | Core | MVP | Movement | ⚠️ Needs retrofit |
> | 战斗 | 核心 | MVP | 移动 | ⚠️ 需要改造 |
> | Inventory | Core | MVP | None | ⚠️ Needs retrofit |
> | 库存 | 核心 | MVP | 无 | ⚠️ 需要改造 |
> | Crafting | Feature | MVP | Inventory | ❌ Pre-GDD only |
> | 制作 | 功能 | MVP | 库存 | ❌ 仅有 GDD 前内容 |
> | UI / HUD | Presentation | MVP | Combat, Inventory, Movement | ❌ Missing |
> | UI / HUD | 呈现 | MVP | 战斗、库存、移动 | ❌ 缺失 |
>
> Does this look right? Anything I missed or miscategorized?
> 这看起来对吗？我遗漏或错误分类了什么吗？

**User:**
**用户：**
> Looks right. Add a Stamina system — it's in combat/ but should be its own system.
> 看起来对。添加一个耐力系统——它在 combat/ 中但应该是独立的系统。

**Agent:**
**智能体：**
> Added Stamina as a Core / MVP system:
> 添加了耐力作为 Core / MVP 系统：
>
> | Stamina | Core | MVP | Movement, Combat | ❌ Missing |
> | 耐力 | 核心 | MVP | 移动、战斗 | ❌ 缺失 |
>
> Updated dependency: Movement depends on Stamina (movement drains it).
> 更新依赖：移动依赖于耐力（移动消耗它）。
>
> May I write this to `design/gdd/systems-index.md`?
> 我可以将此写入 `design/gdd/systems-index.md` 吗？

## Turn 6: User Approves
## 回合 6：用户批准

**User:**
**用户：**
> Yes
> 是

**Agent writes `design/gdd/systems-index.md`.**
**智能体写入 `design/gdd/systems-index.md`。**

## Turn 7: Agent Shows What's Now Unlocked
## 回合 7：智能体展示现已解锁的内容

**Agent:**
**智能体：**
> `design/gdd/systems-index.md` created.
> `design/gdd/systems-index.md` 已创建。
>
> **BLOCKING gap resolved.** The following skills can now run:
> **BLOCKING 差距已解决。** 以下技能现在可以运行：
>
> - ✅ `/design-system [name]` — guided GDD authoring for any system
> - ✅ `/design-system [name]` —— 任何系统的引导式 GDD 编写
> - ✅ `/design-system retrofit [path]` — fill missing sections in existing docs
> - ✅ `/design-system retrofit [path]` —— 填补现有文档中缺失的章节
> - ✅ `/gate-check` — now has a systems index to validate
> - ✅ `/gate-check` —— 现在有一个系统索引可验证
> - ✅ `/help` — can now show correct phase workflow
> - ✅ `/help` —— 现在可以显示正确的阶段工作流
>
> **Recommended next steps (from adoption plan):**
> **推荐的下一步（来自接入计划）：**
>
> 1. Run `/design-system retrofit design/inventory.md` — your most complete existing doc, easiest win
> 1. 运行 `/design-system retrofit design/inventory.md` —— 你最完整的现有文档，最容易的胜利
> 2. Run `/design-system retrofit design/combat-notes.md`
> 2. 运行 `/design-system retrofit design/combat-notes.md`
> 3. Run `/design-system crafting` — author crafting GDD using your ideas file as input
> 3. 运行 `/design-system crafting` —— 使用你的想法文件作为输入编写制作 GDD
>
> After those 3, you'll have 5 of 7 systems with complete GDDs. Then run `/review-all-gdds` to catch cross-system consistency issues before architecture work.
> 完成这 3 项后，你将拥有 7 个系统中 5 个的完整 GDD。然后运行 `/review-all-gdds` 在架构工作之前捕获跨系统一致性问题。

## Turn 8: User Confirms Plan
## 回合 8：用户确认计划

**User:**
**用户：**
> Perfect. I'll start with the inventory retrofit.
> 完美。我将从库存改造开始。

**Agent:**
**智能体：**
> Run:
> 运行：
> ```
> /design-system retrofit design/inventory.md
> ```
>
> The skill will detect your existing Overview and Rules sections, show you which of the 8 sections are missing, and run the section cycle only for the gaps. Your existing content won't be touched.
> 技能将检测你现有的 Overview 和 Rules 章节，向你展示 8 个章节中哪些缺失，并仅对差距运行章节循环。你现有的内容不会被触碰。

---

## What This Example Demonstrates
## 本示例展示了什么

- **FORMAT audit, not existence audit**: `/adopt` doesn't just check whether files exist — it checks whether their internal structure matches what skills expect. A file named `gdd.md` with no template sections is flagged as a gap.
- **格式审计，而非存在性审计**：`/adopt` 不只是检查文件是否存在——它检查它们的内部结构是否匹配技能的预期。一个名为 `gdd.md` 但没有模板章节的文件会被标记为差距。
- **Migration, not replacement**: existing content is never overwritten. The plan fills gaps only.
- **迁移，而非替换**：现有内容从不被覆盖。该计划仅填补差距。
- **BLOCKING gaps surface first**: the missing systems index is identified as BLOCKING because it prevents the most skills from functioning.
- **BLOCKING 差距优先呈现**：缺失的系统索引被识别为 BLOCKING，因为它阻止了最多技能的运行。
- **Inline creation of the urgent gap**: rather than just reporting the gap, the agent offers to fix it immediately in the same session.
- **紧急差距的内联创建**：智能体不只是报告差距，它提议在同一会话中立即修复。
- **Inferred from code**: the systems index is bootstrapped from the codebase structure, not written from scratch — brownfield code already contains the answer.
- **从代码推断**：系统索引从代码库结构引导生成，而非从头编写——棕地代码已经包含答案。
- **Retrofit vs. new authoring**: `/design-system retrofit` handles existing-but-incomplete docs; full `/design-system` handles net-new docs. The adoption plan distinguishes which applies to each file.
- **改造 vs. 全新编写**：`/design-system retrofit` 处理现有但不完整的文档；完整 `/design-system` 处理全新文档。接入计划区分每个文件适用哪种方式。
- **Context: fork**: `/adopt` runs in a forked context to avoid polluting the main session with large file reads across the whole codebase.
- **上下文：fork**：`/adopt` 在分叉的上下文中运行，以避免用整个代码库的大文件读取污染主会话。
