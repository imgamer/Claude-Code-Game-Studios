---
name: asset-spec
description: "Generate per-asset visual specifications and AI generation prompts from GDDs, level docs, or character profiles. Produces structured spec files and updates the master asset manifest. Run after art bible and GDD/level design are approved, before production begins."
argument-hint: "[system:<name> | level:<name> | character:<name>] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---
---
name: asset-spec
description: "从 GDD、关卡文档或角色档案生成按资产的视觉规格和 AI 生成提示。生成结构化规格文件并更新主资产清单。在艺术圣经和 GDD/关卡设计批准之后、生产开始之前运行。"
argument-hint: "[system:<name> | level:<name> | character:<name>] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---

If no argument is provided, check whether `design/assets/entity-inventory.md` exists:
- If it exists: read it, find the first entity or screen with status "Needed" but no spec file yet, and use `AskUserQuestion`:
  - Prompt: "The next unspecced item is **[name]**. Generate specs for it?"
  - Options: `[A] Yes — spec [name]` / `[B] Pick a different item` / `[C] Stop here`
- If no entity inventory: check `design/assets/asset-manifest.md`. If manifest exists, same flow above but reading from manifest.
- If neither exists: **start the Entity & Screen Inventory flow** (Phase 0b below) rather than failing.
如果未提供参数，检查 `design/assets/entity-inventory.md` 是否存在：
- 如果存在：读取它，找到第一个状态为 "Needed" 但尚无规格文件的实体或屏幕，并使用 `AskUserQuestion`：
  - 提示："The next unspecced item is **[name]**. Generate specs for it?"
  - 选项：`[A] Yes — spec [name]` / `[B] Pick a different item` / `[C] Stop here`
- 如果没有实体清单：检查 `design/assets/asset-manifest.md`。如果清单存在，使用上述相同流程但从清单读取。
- 如果都不存在：**启动 Entity & Screen Inventory 流程**（下面的阶段 0b），而不是失败。

---

## Phase 0b: Entity & Screen Inventory (runs when no arguments and no existing inventory)
## 阶段 0b：Entity & Screen Inventory（在无参数且无现有清单时运行）

This flow produces `design/assets/entity-inventory.md` — the master list of everything
the game needs visually. Run once before asset spec work begins.
此流程生成 `design/assets/entity-inventory.md` — 游戏视觉上需要的
所有内容的主列表。在资产规格工作开始之前运行一次。

### Step 1 — Gather from docs
### 步骤 1 — 从文档收集

Read all available source material in parallel:
- `design/gdd/systems-index.md` — extract every system listed
- All GDDs in `design/gdd/` — extract: Visual/Audio Requirements sections, UI elements mentioned, VFX events, any named entities (characters, enemies, buildings, items)
- `design/art/art-bible.md` — extract: any named visual categories, asset type expectations
- `design/narrative/` — scan for any character or world entity documents if they exist (optional — not required)
并行读取所有可用的源材料：
- `design/gdd/systems-index.md` — 提取列出的每个系统
- `design/gdd/` 中的所有 GDD — 提取：Visual/Audio Requirements 章节、提及的 UI 元素、VFX 事件、任何命名的实体（角色、敌人、建筑、物品）
- `design/art/art-bible.md` — 提取：任何命名的视觉类别、资产类型期望
- `design/narrative/` — 扫描任何角色或世界实体文档（如果存在）（可选 — 非必需）

### Step 2 — Build proposed inventory
### 步骤 2 — 构建建议清单

Organize everything found into categories:
将找到的所有内容组织为类别：

```
Characters / Protagonists
Enemies / Creatures
Buildings / Structures
Environment / Terrain
Items / Props
VFX / Particles
UI Screens (list each screen by name)
HUD Elements
Audio (SFX, music — descriptions only, no generation prompts)
Other
```
```
Characters / Protagonists
Enemies / Creatures
Buildings / Structures
Environment / Terrain
Items / Props
VFX / Particles
UI Screens (list each screen by name)
HUD Elements
Audio (SFX, music — descriptions only, no generation prompts)
Other
```

For each item, note the source doc it was found in.
对每个项，注明它被发现所在的源文档。

### Step 3 — Present and collaborate
### 步骤 3 — 呈现并协作

Present the full proposed inventory to the user in conversation. Then use `AskUserQuestion`:
- Prompt: "I found **[N] visual entities and [N] UI screens** across your GDDs and art bible. Review the list — what's missing, what's not needed?"
- Options:
  - `[A] Looks good — save this inventory`
  - `[B] Add items I'll describe`
  - `[C] Remove items that don't apply`
  - `[D] Both add and remove — let me edit`
在对话中向用户呈现完整的建议清单。然后使用 `AskUserQuestion`：
- 提示："I found **[N] visual entities and [N] UI screens** across your GDDs and art bible. Review the list — what's missing, what's not needed?"
- 选项：
  - `[A] Looks good — save this inventory`
  - `[B] Add items I'll describe`
  - `[C] Remove items that don't apply`
  - `[D] Both add and remove — let me edit`

If [B] or [D]: ask the user to describe additional items. Accept brief descriptions ("a medieval keep, used as a level background") or detailed ones — either works. Work through them collaboratively until the user is satisfied.
如果 [B] 或 [D]：要求用户描述附加项。接受简短描述（"a medieval keep, used as a level background"）或详细描述 — 两者都可以。协作地处理它们直到用户满意。

If [C] or [D]: ask which items to remove and why. Remove them from the list.
如果 [C] 或 [D]：询问要移除哪些项以及为什么。将它们从列表中移除。

### Step 4 — Write inventory
### 步骤 4 — 写入清单

After user approval, ask: "May I write the entity inventory to `design/assets/entity-inventory.md`?"
用户批准后，询问："May I write the entity inventory to `design/assets/entity-inventory.md`?"

Write the file:
写入文件：

```markdown
# Visual Entity & Screen Inventory

> Generated: [date]
> Sources: [list of source docs read]

## Entities

| # | Name | Type | Description | Source | Status |
|---|------|------|-------------|--------|--------|
| 1 | [name] | Character / Enemy / Building / Environment / Item / Other | [brief description] | [source doc] | Needed |

## UI Screens

| # | Screen Name | Description | Source | Status |
|---|-------------|-------------|--------|--------|
| 1 | Main Menu | [description] | [source] | Needed |

## HUD Elements

| # | Element | Description | Source | Status |
|---|---------|-------------|--------|--------|

## Audio

| # | Name | Type (SFX / Music / Ambient) | Description | Source | Status |
|---|------|------------------------------|-------------|--------|--------|
```
```markdown
# Visual Entity & Screen Inventory

> Generated: [date]
> Sources: [list of source docs read]

## Entities

| # | Name | Type | Description | Source | Status |
|---|------|------|-------------|--------|--------|
| 1 | [name] | Character / Enemy / Building / Environment / Item / Other | [brief description] | [source doc] | Needed |

## UI Screens

| # | Screen Name | Description | Source | Status |
|---|-------------|-------------|--------|--------|
| 1 | Main Menu | [description] | [source] | Needed |

## HUD Elements

| # | Element | Description | Source | Status |
|---|---------|-------------|--------|--------|

## Audio

| # | Name | Type (SFX / Music / Ambient) | Description | Source | Status |
|---|------|------------------------------|-------------|--------|--------|
```

After writing, tell the user:
> "Entity inventory saved. Next steps:
> - Run `/ux-design [screen name]` for each UI screen in the inventory
> - Run `/asset-spec entity:[name]` to spec each visual entity
> - Or run `/asset-spec` again to work through the inventory one item at a time"
写入后，告诉用户：
> "Entity inventory saved. Next steps:
> - Run `/ux-design [screen name]` for each UI screen in the inventory
> - Run `/asset-spec entity:[name]` to spec each visual entity
> - Or run `/asset-spec` again to work through the inventory one item at a time"

---

## Phase 0: Parse Arguments
## 阶段 0：解析参数

Extract:
- **Target type**: `system`, `level`, or `character`
- **Target name**: the name after the colon (normalize to kebab-case)
- **Review mode**: `--review [full|lean|solo]` if present
提取：
- **Target type**：`system`、`level` 或 `character`
- **Target name**：冒号之后的名称（规范化为 kebab-case）
- **Review mode**：`--review [full|lean|solo]`（如果存在）

**Mode behavior:**
- `full` (default): spawn both `art-director` and `technical-artist` in parallel
- `lean`: spawn `art-director` only — faster, skips technical constraint pass
- `solo`: no agent spawning — main session writes specs from art bible rules alone. Use for simple asset categories or when speed matters more than depth.
**模式行为：**
- `full`（默认）：并行生成 `art-director` 和 `technical-artist`
- `lean`：仅生成 `art-director` — 更快，跳过技术约束传递
- `solo`：不生成智能体 — 主会话仅根据艺术圣经规则编写规格。用于简单资产类别或当速度比深度更重要时。

---

## Phase 1: Gather Context
## 阶段 1：收集上下文

Read all source material **before** asking the user anything.
在向用户询问任何内容 **之前** 读取所有源材料。

### Required reads:
- **Art bible**: Read `design/art/art-bible.md` — fail if missing:
  > "No art bible found. Run `/art-bible` first — asset specs are anchored to the art bible's visual rules and asset standards."
  Extract: Visual Identity Statement, Color System (semantic colors), Shape Language, Asset Standards (Section 8 — dimensions, formats, polycount budgets, texture resolution tiers).
### 必需读取：
- **Art bible**：读取 `design/art/art-bible.md` — 如果缺失则失败：
  > "No art bible found. Run `/art-bible` first — asset specs are anchored to the art bible's visual rules and asset standards."
  提取：Visual Identity Statement、Color System（语义颜色）、Shape Language、Asset Standards（第 8 章节 — 尺寸、格式、多边形预算、纹理分辨率层级）。

- **Technical preferences**: Read `.claude/docs/technical-preferences.md` — extract performance budgets and naming conventions.
- **Technical preferences**：读取 `.claude/docs/technical-preferences.md` — 提取性能预算和命名约定。

### Source doc reads (by target type):
- **system**: Read `design/gdd/[target-name].md`. Extract the **Visual/Audio Requirements** section. If it doesn't exist or reads `[To be designed]`:
  > "The Visual/Audio section of `design/gdd/[target-name].md` is empty. Either run `/design-system [target-name]` to complete the GDD, or describe the visual needs manually."
  Use `AskUserQuestion`: `[A] Describe needs manually` / `[B] Stop — complete the GDD first`
- **level**: Read `design/levels/[target-name].md`. Extract art requirements, asset list, VFX needs, and the art-director's production concept specs from Step 4.
- **character** or **entity**: Read `design/narrative/characters/[target-name].md` or search `design/narrative/` and `design/assets/entity-inventory.md` for a matching entry. Extract visual description, role, and any specified distinguishing features.
  - **If no source doc exists**: do not fail. Instead, use `AskUserQuestion`:
    - Prompt: "No profile found for **[name]**. Describe it briefly — a sentence or two is enough."
    - Options: `[A] Describe it now` / `[B] Skip this entity` / `[C] Stop here`
    - If [A]: the user's description becomes the source. Brief answers produce concise specs; detailed answers produce detailed specs. Accept whatever level of detail the user provides and work from it.
### 源文档读取（按目标类型）：
- **system**：读取 `design/gdd/[target-name].md`。提取 **Visual/Audio Requirements** 章节。如果不存在或读取为 `[To be designed]`：
  > "The Visual/Audio section of `design/gdd/[target-name].md` is empty. Either run `/design-system [target-name]` to complete the GDD, or describe the visual needs manually."
  使用 `AskUserQuestion`：`[A] Describe needs manually` / `[B] Stop — complete the GDD first`
- **level**：读取 `design/levels/[target-name].md`。提取艺术需求、资产列表、VFX 需求和来自步骤 4 的 art-director 生产概念规格。
- **character** 或 **entity**：读取 `design/narrative/characters/[target-name].md` 或搜索 `design/narrative/` 和 `design/assets/entity-inventory.md` 查找匹配条目。提取视觉描述、角色和任何指定的区分特征。
  - **如果不存在源文档**：不要失败。相反，使用 `AskUserQuestion`：
    - 提示："No profile found for **[name]**. Describe it briefly — a sentence or two is enough."
    - 选项：`[A] Describe it now` / `[B] Skip this entity` / `[C] Stop here`
    - 如果 [A]：用户的描述成为源。简短回答产生简洁规格；详细回答产生详细规格。接受用户提供的任何详细程度并据此工作。

### Optional reads:
- **Existing manifest**: Read `design/assets/asset-manifest.md` if it exists — extract already-specced assets for this target to avoid duplicates.
- **Related specs**: Glob `design/assets/specs/*.md` — scan for assets that could be shared (e.g., a common UI element specced for one system might apply here too).
### 可选读取：
- **Existing manifest**：读取 `design/assets/asset-manifest.md`（如果存在）— 提取此目标已规格化的资产以避免重复。
- **Related specs**：Glob `design/assets/specs/*.md` — 扫描可能共享的资产（例如，为一个系统规格化的通用 UI 元素可能也适用于此处）。

### Present context summary:
> **Asset Spec: [Target Type] — [Target Name]**
> - Source doc: [path] — [N] asset types identified
> - Art bible: found — Asset Standards at Section 8
> - Existing specs for this target: [N already specced / none]
> - Shared assets found in other specs: [list or "none"]
### 呈现上下文摘要：
> **Asset Spec: [Target Type] — [Target Name]**
> - Source doc: [path] — [N] asset types identified
> - Art bible: found — Asset Standards at Section 8
> - Existing specs for this target: [N already specced / none]
> - Shared assets found in other specs: [list or "none"]

---

## Phase 2: Asset Identification
## 阶段 2：资产识别

From the source doc, extract every asset type mentioned — explicit and implied.
从源文档中，提取提及的每个资产类型 — 显式和隐式的。

**For systems**: look for VFX events, sprite references, UI elements, audio triggers, particle effects, icon needs, and any "visual feedback" language.
**For systems**：查找 VFX 事件、精灵引用、UI 元素、音频触发器、粒子效果、图标需求，以及任何 "visual feedback" 语言。

**For levels**: look for unique environment props, atmospheric VFX, lighting setups, ambient audio, skybox/background, and any area-specific materials.
**For levels**：查找独特的环境道具、氛围 VFX、光照设置、环境音频、天空盒/背景，以及任何区域特定材质。

**For characters**: look for sprite sheets (idle, walk, attack, death), portrait/avatar, VFX attached to abilities, UI representation (icon, health bar skin).
**For characters**：查找精灵表（待机、行走、攻击、死亡）、头像/角色、附加到能力的 VFX、UI 表示（图标、生命条皮肤）。

Group assets into categories:
- **Sprite / 2D Art** — character sprites, UI icons, tile sheets
- **VFX / Particles** — hit effects, ambient particles, screen effects
- **Environment** — props, tiles, backgrounds, skyboxes
- **UI** — HUD elements, menu art, fonts (if custom)
- **Audio** — SFX, music tracks, ambient loops *(note: audio specs are descriptions only — no generation prompts)*
- **3D Assets** — meshes, materials (if applicable per engine)
将资产分组为类别：
- **Sprite / 2D Art** — 角色精灵、UI 图标、图块表
- **VFX / Particles** — 命中效果、环境粒子、屏幕效果
- **Environment** — 道具、图块、背景、天空盒
- **UI** — HUD 元素、菜单艺术、字体（如果是自定义的）
- **Audio** — SFX、音乐曲目、环境循环 *（注意：音频规格仅是描述 — 无生成提示）*
- **3D Assets** — 网格、材质（如果按引擎适用）

Present the full identified list to the user. Use `AskUserQuestion`:
- Prompt: "I identified [N] assets across [N] categories for **[target]**. Review before speccing:"
- Show the grouped list in conversation text first
- Options: `[A] Proceed — spec all of these` / `[B] Remove some assets` / `[C] Add assets I didn't catch` / `[D] Adjust categories`
向用户呈现完整的已识别列表。使用 `AskUserQuestion`：
- 提示："I identified [N] assets across [N] categories for **[target]**. Review before speccing:"
- 首先在对话文本中显示分组列表
- 选项：`[A] Proceed — spec all of these` / `[B] Remove some assets` / `[C] Add assets I didn't catch` / `[D] Adjust categories`

Do NOT proceed to Phase 3 without user confirmation of the asset list.
在未经用户确认资产列表之前，不要继续阶段 3。

---

## Phase 3: Spec Generation
## 阶段 3：规格生成

Spawn specialist agents based on review mode. **Issue all Task calls simultaneously — do not wait for one before starting the next.**
根据评审模式生成专家智能体。**同时发出所有 Task 调用 — 不要在开始下一个之前等待一个。**

### Full mode — spawn in parallel:
### Full 模式 — 并行生成：

**`art-director`** via Task:
- Provide: full asset list from Phase 2, art bible Visual Identity Statement, Color System, Shape Language, the source doc's visual requirements, and any reference games/art mentioned in the art bible Section 9
- Ask: "For each asset in this list, produce: (1) a 2–3 sentence visual description anchored to the art bible's shape language and color system — be specific enough that two different artists would produce consistent results; (2) a generation prompt ready for use with AI image tools (Midjourney/Stable Diffusion style — include style keywords, composition, color palette anchors, negative prompts); (3) which art bible rules directly govern this asset (cite by section). For audio assets, describe the sonic character instead of a generation prompt."
**`art-director`** 通过 Task：
- 提供：来自阶段 2 的完整资产列表、艺术圣经 Visual Identity Statement、Color System、Shape Language、源文档的视觉需求，以及艺术圣经第 9 章节中提及的任何参考游戏/艺术
- 询问："For each asset in this list, produce: (1) a 2–3 sentence visual description anchored to the art bible's shape language and color system — be specific enough that two different artists would produce consistent results; (2) a generation prompt ready for use with AI image tools (Midjourney/Stable Diffusion style — include style keywords, composition, color palette anchors, negative prompts); (3) which art bible rules directly govern this asset (cite by section). For audio assets, describe the sonic character instead of a generation prompt."

**`technical-artist`** via Task:
- Provide: full asset list, art bible Asset Standards (Section 8), technical-preferences.md performance budgets, engine name and version
- Ask: "For each asset in this list, specify: (1) exact dimensions or polycount (match the art bible Asset Standards tiers — do not invent new sizes); (2) file format and export settings; (3) naming convention (from technical-preferences.md); (4) any engine-specific constraints this asset type must respect; (5) LOD requirements if applicable. Flag any asset type where the art bible's preferred standard conflicts with the engine's constraints."
**`technical-artist`** 通过 Task：
- 提供：完整资产列表、艺术圣经 Asset Standards（第 8 章节）、technical-preferences.md 性能预算、引擎名称和版本
- 询问："For each asset in this list, specify: (1) exact dimensions or polycount (match the art bible Asset Standards tiers — do not invent new sizes); (2) file format and export settings; (3) naming convention (from technical-preferences.md); (4) any engine-specific constraints this asset type must respect; (5) LOD requirements if applicable. Flag any asset type where the art bible's preferred standard conflicts with the engine's constraints."

### Lean mode — spawn art-director only (skip technical-artist).
### Lean 模式 — 仅生成 art-director（跳过 technical-artist）。

### Solo mode — skip both. Derive specs from art bible rules alone, noting that technical constraints were not validated.
### Solo 模式 — 两者都跳过。仅从艺术圣经规则推导规格，注明技术约束未经验证。

**Collect both responses before Phase 4.** If any conflict exists between art-director and technical-artist (e.g., art-director specifies 4K textures but technical-artist flags the engine budget requires 512px), surface it explicitly — do NOT silently resolve.
**在阶段 4 之前收集两个响应。** 如果 art-director 和 technical-artist 之间存在任何冲突（例如，art-director 指定 4K 纹理但 technical-artist 标记引擎预算需要 512px），明确呈现 — 不要静默解决。

---

## Phase 4: Compile and Review
## 阶段 4：编译和评审

Combine the agent outputs into a draft spec per asset. Present all specs in conversation text using this format:
将智能体输出合并为每个资产的草稿规格。使用此格式在对话文本中呈现所有规格：

```
## ASSET-[NNN] — [Asset Name]

| Field | Value |
|-------|-------|
| Category | [Sprite / VFX / Environment / UI / Audio / 3D] |
| Dimensions | [e.g. 256×256px, 4-frame sprite sheet] |
| Format | [PNG / SVG / WAV / etc.] |
| Naming | [e.g. vfx_frost_hit_01.png] |
| Polycount | [if 3D — e.g. <800 tris] |
| Texture Res | [e.g. 512px — matches Art Bible §8 Tier 2] |

**Visual Description:**
[2–3 sentences. Specific enough for two artists to produce consistent results.]

**Art Bible Anchors:**
- §3 Shape Language: [relevant rule applied]
- §4 Color System: [color role — e.g. "uses Threat Blue per semantic color rules"]

**Generation Prompt:**
[Ready-to-use prompt. Include: style keywords, composition notes, color palette anchors, lighting direction, negative prompts.]

**Status:** Needed
```
```
## ASSET-[NNN] — [Asset Name]

| Field | Value |
|-------|-------|
| Category | [Sprite / VFX / Environment / UI / Audio / 3D] |
| Dimensions | [e.g. 256×256px, 4-frame sprite sheet] |
| Format | [PNG / SVG / WAV / etc.] |
| Naming | [e.g. vfx_frost_hit_01.png] |
| Polycount | [if 3D — e.g. <800 tris] |
| Texture Res | [e.g. 512px — matches Art Bible §8 Tier 2] |

**Visual Description:**
[2–3 sentences. Specific enough for two artists to produce consistent results.]

**Art Bible Anchors:**
- §3 Shape Language: [relevant rule applied]
- §4 Color System: [color role — e.g. "uses Threat Blue per semantic color rules"]

**Generation Prompt:**
[Ready-to-use prompt. Include: style keywords, composition notes, color palette anchors, lighting direction, negative prompts.]

**Status:** Needed
```

After presenting all specs, use `AskUserQuestion`:
- Prompt: "Asset specs for **[target]** — [N] assets. Review complete?"
- Options: `[A] Approve all — write to file` / `[B] Revise a specific asset` / `[C] Regenerate with different direction`
呈现所有规格后，使用 `AskUserQuestion`：
- 提示："Asset specs for **[target]** — [N] assets. Review complete?"
- 选项：`[A] Approve all — write to file` / `[B] Revise a specific asset` / `[C] Regenerate with different direction`

If [B]: ask which asset and what to change. Revise inline and re-present. Do NOT re-spawn agents for minor text revisions — only re-spawn if the visual direction itself needs to change.
如果 [B]：询问哪个资产以及要更改什么。内联修订并重新呈现。对于次要文本修订，不要重新生成智能体 — 仅当视觉方向本身需要更改时才重新生成。

If [C]: ask what direction to change. Re-spawn the relevant agent with the updated brief.
如果 [C]：询问要更改什么方向。使用更新的简报重新生成相关智能体。

---

## Phase 5: Write Spec File
## 阶段 5：写入规格文件

After approval, ask: "May I write the spec to `design/assets/specs/[target-name]-assets.md`?"
批准后，询问："May I write the spec to `design/assets/specs/[target-name]-assets.md`?"

Write the file with:
写入文件：

```markdown
# Asset Specs — [Target Type]: [Target Name]

> **Source**: [path to source GDD/level/character doc]
> **Art Bible**: design/art/art-bible.md
> **Generated**: [date]
> **Status**: [N] assets specced / [N] approved / [N] in production / [N] done

[all asset specs in ASSET-NNN format]
```
```markdown
# Asset Specs — [Target Type]: [Target Name]

> **Source**: [path to source GDD/level/character doc]
> **Art Bible**: design/art/art-bible.md
> **Generated**: [date]
> **Status**: [N] assets specced / [N] approved / [N] in production / [N] done

[all asset specs in ASSET-NNN format]
```

Then update `design/assets/asset-manifest.md`. If it doesn't exist, create it:
然后更新 `design/assets/asset-manifest.md`。如果不存在，创建它：

```markdown
# Asset Manifest

> Last updated: [date]

## Progress Summary

| Total | Needed | In Progress | Done | Approved |
|-------|--------|-------------|------|----------|
| [N] | [N] | [N] | [N] | [N] |

## Assets by Context

### [Target Type]: [Target Name]
| Asset ID | Name | Category | Status | Spec File |
|----------|------|----------|--------|-----------|
| ASSET-001 | [name] | [category] | Needed | design/assets/specs/[target]-assets.md |
```
```markdown
# Asset Manifest

> Last updated: [date]

## Progress Summary

| Total | Needed | In Progress | Done | Approved |
|-------|--------|-------------|------|----------|
| [N] | [N] | [N] | [N] | [N] |

## Assets by Context

### [Target Type]: [Target Name]
| Asset ID | Name | Category | Status | Spec File |
|----------|------|----------|--------|-----------|
| ASSET-001 | [name] | [category] | Needed | design/assets/specs/[target]-assets.md |
```

If the manifest already exists, append the new context block and update the Progress Summary counts.
如果清单已存在，追加新的上下文块并更新 Progress Summary 计数。

Ask: "May I update `design/assets/asset-manifest.md`?"
询问："May I update `design/assets/asset-manifest.md`?"

---

## Phase 6: Close
## 阶段 6：关闭

Use `AskUserQuestion`:
- Prompt: "Asset specs complete for **[target]**. What's next?"
- Options:
  - `[A] Spec another system — /asset-spec system:[next-system]`
  - `[B] Spec a level — /asset-spec level:[level-name]`
  - `[C] Spec a character — /asset-spec character:[character-name]`
  - `[D] Run /asset-audit — validate delivered assets against specs`
  - `[E] Stop here`
使用 `AskUserQuestion`：
- 提示："Asset specs complete for **[target]**. What's next?"
- 选项：
  - `[A] Spec another system — /asset-spec system:[next-system]`
  - `[B] Spec a level — /asset-spec level:[level-name]`
  - `[C] Spec a character — /asset-spec character:[character-name]`
  - `[D] Run /asset-audit — validate delivered assets against specs`
  - `[E] Stop here`

---

## Asset ID Assignment
## Asset ID 分配

Asset IDs are assigned sequentially across the entire project — not per-context. Read the manifest before assigning IDs to find the current highest number:
Asset ID 在整个项目中按顺序分配 — 而非按上下文。在分配 ID 之前读取清单以查找当前最高编号：

```
Grep pattern="ASSET-" path="design/assets/asset-manifest.md"
```
```
Grep pattern="ASSET-" path="design/assets/asset-manifest.md"
```

Start new assets from `ASSET-[highest + 1]`. This ensures IDs are stable and unique across the whole project.
从 `ASSET-[highest + 1]` 开始新资产。这确保 ID 在整个项目中稳定且唯一。

If no manifest exists yet, start from `ASSET-001`.
如果尚无清单，从 `ASSET-001` 开始。

---

## Shared Asset Protocol
## 共享资产协议

Before speccing an asset, check if an equivalent already exists in another context's spec:
在规格化资产之前，检查另一个上下文的规格中是否已存在等效项：

- Common UI elements (health bars, score displays) are often shared across systems
- Generic environment props may appear in multiple levels
- Character VFX (hit sparks, death effects) may reuse a base spec with color variants
- 通用 UI 元素（生命条、分数显示）通常跨系统共享
- 通用环境道具可能出现在多个关卡中
- 角色 VFX（命中火花、死亡效果）可能复用基础规格并附颜色变体

If a match is found: reference the existing ASSET-ID rather than creating a duplicate. Note the shared usage in the manifest's referenced-by column.
如果找到匹配：引用现有的 ASSET-ID 而非创建重复。在清单的 referenced-by 列中注明共享使用。

> "ASSET-012 (Generic Hit Spark) already specced for Combat system. Reusing for Tower Defense — adding tower-defense to referenced-by."
> "ASSET-012 (Generic Hit Spark) already specced for Combat system. Reusing for Tower Defense — adding tower-defense to referenced-by."

---

## Error Recovery Protocol
## 错误恢复协议

If any spawned agent returns BLOCKED or cannot complete:
如果任何生成的智能体返回 BLOCKED 或无法完成：

1. Surface immediately: "[AgentName]: BLOCKED — [reason]"
2. In `lean` mode or if `technical-artist` blocks: proceed with art-director output only — note that technical constraints were not validated
3. In `solo` mode or if `art-director` blocks: derive descriptions from art bible rules — flag as "Art director not consulted — verify against art bible before production"
4. Always produce a partial spec — never discard work because one agent blocked
1. 立即呈现："[AgentName]: BLOCKED — [reason]"
2. 在 `lean` 模式中或如果 `technical-artist` 阻塞：仅用 art-director 输出继续 — 注明技术约束未经验证
3. 在 `solo` 模式中或如果 `art-director` 阻塞：从艺术圣经规则推导描述 — 标记为 "Art director not consulted — verify against art bible before production"
4. 始终生成部分规格 — 切勿因为一个智能体阻塞而丢弃工作

---

## Collaborative Protocol
## 协作协议

Every phase follows: **Identify → Confirm → Generate → Review → Approve → Write**
每个阶段遵循：**Identify → Confirm → Generate → Review → Approve → Write**

- Never spec assets without first confirming the asset list with the user
- Always anchor specs to the art bible — a spec that contradicts the art bible is wrong
- Surface all agent disagreements — do not silently pick one
- Write the spec file only after explicit approval
- Update the manifest immediately after writing the spec
- 切勿在未先与用户确认资产列表的情况下规格化资产
- 始终将规格锚定到艺术圣经 — 与艺术圣经矛盾的规格是错误的
- 呈现所有智能体分歧 — 不要静默选择一个
- 仅在明确批准后写入规格文件
- 写入规格后立即更新清单

---

## Recommended Next Steps
## 推荐的后续步骤

- Run `/asset-spec [next-context]` to continue speccing remaining systems, levels, or characters
- Run `/asset-audit` to validate delivered assets against the written specs and identify gaps or mismatches
- 运行 `/asset-spec [next-context]` 继续规格化剩余系统、关卡或角色
- 运行 `/asset-audit` 针对写入的规格验证已交付的资产并识别缺口或不匹配
