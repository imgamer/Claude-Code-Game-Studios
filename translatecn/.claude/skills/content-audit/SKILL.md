---
name: content-audit
description: "Audit GDD-specified content counts against implemented content. Identifies what's planned vs built."
argument-hint: "[system-name | --summary | (no arg = full audit)]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
agent: producer
---
---
名称：content-audit
描述："审计 GDD 指定的内容数量与已实现内容。识别计划 vs 已构建。"
argument-hint："[system-name | --summary | (no arg = full audit)]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write
model：sonnet
agent：producer
---

When this skill is invoked:
当此技能被调用时：

Parse the argument:
- No argument → full audit across all systems
- `[system-name]` → audit that single system only
- `--summary` → summary table only, no file write
解析参数：
- 无参数 → 跨所有系统完整审计
- `[system-name]` → 仅审计单个系统
- `--summary` → 仅摘要表，不写文件

---

## Phase 1 — Context Gathering
## 阶段 1 —— 上下文收集

1. **Read `design/gdd/systems-index.md`** for the full list of systems, their
   categories, and MVP/priority tier.
1. **读取 `design/gdd/systems-index.md`** 获取完整系统列表、其类别与
   MVP/优先级层级。

2. **L0 pre-scan**: Before full-reading any GDDs, Grep all GDD files for
   `## Summary` sections plus common content-count keywords:
   ```
   Grep pattern="(## Summary|N enemies|N levels|N items|N abilities|enemy types|item types)" glob="design/gdd/*.md" output_mode="files_with_matches"
   ```
   For a single-system audit: skip this step and go straight to full-read.
   For a full audit: full-read only the GDDs that matched content-count keywords.
   GDDs with no content-count language (pure mechanics GDDs) are noted as
   "No auditable content counts" without a full read.
2. **L0 预扫描**：完整读取任何 GDD 之前，Grep 所有 GDD 文件的 `## Summary`
   段落加常见内容数量关键词：
   ```
   Grep pattern="(## Summary|N enemies|N levels|N items|N abilities|enemy types|item types)" glob="design/gdd/*.md" output_mode="files_with_matches"
   ```
   单系统审计：跳过此步骤直接全读。
   完整审计：仅全读匹配内容数量关键词的 GDD。
   无内容数量语言（纯机制 GDD）的 GDD 注为 "No auditable content counts"
   而不全读。

3. **Full-read in-scope GDD files** (or the single system GDD if a system
   name was given).
3. **完整读取范围内 GDD 文件**（或给定系统名时的单个系统 GDD）。

4. **For each GDD, extract explicit content counts or lists.** Look for patterns
   like:
   - "N enemies" / "enemy types:" / list of named enemies
   - "N levels" / "N areas" / "N maps" / "N stages"
   - "N items" / "N weapons" / "N equipment pieces"
   - "N abilities" / "N skills" / "N spells"
   - "N dialogue scenes" / "N conversations" / "N cutscenes"
   - "N quests" / "N missions" / "N objectives"
   - Any explicit enumerated list (bullet list of named content pieces)
4. **对每个 GDD，提取明确的内容数量或列表。** 寻找以下模式：
   - "N enemies" / "enemy types:" / 命名敌人列表
   - "N levels" / "N areas" / "N maps" / "N stages"
   - "N items" / "N weapons" / "N equipment pieces"
   - "N abilities" / "N skills" / "N spells"
   - "N dialogue scenes" / "N conversations" / "N cutscenes"
   - "N quests" / "N missions" / "N objectives"
   - 任何显式枚举列表（命名内容的项目符号列表）

4. **Build a content inventory table** from the extracted data:
4. **从提取数据构建内容清单表**：

   | System | Content Type | Specified Count/List | Source GDD |
   |--------|-------------|---------------------|------------|
   | 系统 | 内容类型 | 指定数量/列表 | 源 GDD |
   |--------|-------------|---------------------|------------|

   Note: If a GDD describes content qualitatively but gives no count, record
   "Unspecified" and flag it — unspecified counts are a design gap worth noting.
   注意：若 GDD 定性描述内容但未给数量，记录 "Unspecified" 并标记 —— 未指定
   数量是值得注意的设计缺漏。

---

## Phase 2 — Implementation Scan
## 阶段 2 —— 实现扫描

For each content type found in Phase 1, scan the relevant directories to count
what has been implemented. Use Glob and Grep to locate files.
对阶段 1 中发现的每个内容类型，扫描相关目录统计已实现内容。使用 Glob 与 Grep
定位文件。

**Levels / Areas / Maps:**
- Glob `assets/**/*.tscn`, `assets/**/*.unity`, `assets/**/*.umap`
- Glob `src/**/*.tscn`, `src/**/*.unity`
- Look for scene files in subdirectories named `levels/`, `areas/`, `maps/`,
  `worlds/`, `stages/`
- Count unique files that appear to be level/scene definitions (not UI scenes)
**关卡 / 区域 / 地图：**
- Glob `assets/**/*.tscn`, `assets/**/*.unity`, `assets/**/*.umap`
- Glob `src/**/*.tscn`, `src/**/*.unity`
- 在名为 `levels/`、`areas/`、`maps/`、`worlds/`、`stages/` 的子目录中查找场景
  文件
- 统计看起来是关卡/场景定义（非 UI 场景）的唯一文件

**Enemies / Characters / NPCs:**
- Glob `assets/data/**/enemies/**`, `assets/data/**/characters/**`
- Glob `src/**/enemies/**`, `src/**/characters/**`
- Look for `.json`, `.tres`, `.asset`, `.yaml` data files defining entity stats
- Look for scene/prefab files in character subdirectories
**敌人 / 角色 / NPC：**
- Glob `assets/data/**/enemies/**`, `assets/data/**/characters/**`
- Glob `src/**/enemies/**`, `src/**/characters/**`
- 查找定义实体属性的 `.json`、`.tres`、`.asset`、`.yaml` 数据文件
- 查找角色子目录中的场景/预制体文件

**Items / Equipment / Loot:**
- Glob `assets/data/**/items/**`, `assets/data/**/equipment/**`,
  `assets/data/**/loot/**`
- Look for `.json`, `.tres`, `.asset` data files
**物品 / 装备 / 掉落：**
- Glob `assets/data/**/items/**`, `assets/data/**/equipment/**`,
  `assets/data/**/loot/**`
- 查找 `.json`、`.tres`、`.asset` 数据文件

**Abilities / Skills / Spells:**
- Glob `assets/data/**/abilities/**`, `assets/data/**/skills/**`,
  `assets/data/**/spells/**`
- Look for `.json`, `.tres`, `.asset` data files
**能力 / 技能 / 法术：**
- Glob `assets/data/**/abilities/**`, `assets/data/**/skills/**`,
  `assets/data/**/spells/**`
- 查找 `.json`、`.tres`、`.asset` 数据文件

**Dialogue / Conversations / Cutscenes:**
- Glob `assets/**/*.dialogue`, `assets/**/*.csv`, `assets/**/*.ink`
- Grep for dialogue data files in `assets/data/`
**对话 / 交谈 / 过场动画：**
- Glob `assets/**/*.dialogue`, `assets/**/*.csv`, `assets/**/*.ink`
- 在 `assets/data/` 中 Grep 对话数据文件

**Quests / Missions:**
- Glob `assets/data/**/quests/**`, `assets/data/**/missions/**`
- Look for `.json`, `.yaml` definition files
**任务 / 使者：**
- Glob `assets/data/**/quests/**`, `assets/data/**/missions/**`
- 查找 `.json`、`.yaml` 定义文件

**Engine-specific notes (acknowledge in the report):**
- Counts are approximations — the skill cannot perfectly parse every engine
  format or distinguish editor-only files from shipped content
- Scene files may include both gameplay content and system/UI scenes; the scan
  counts all matches and notes this caveat
**引擎特定说明（在报告中承认）：**
- 计数是近似值 —— 本技能无法完美解析每种引擎格式或区分编辑器专用文件与已发布
  内容
- 场景文件可能同时包含玩法内容与系统/UI 场景；扫描统计所有匹配并注明此注意

---

## Phase 3 — Gap Report
## 阶段 3 —— 缺口报告

Produce the gap table:
产出缺口表：

```
| System | Content Type | Specified | Found | Gap | Status |
|--------|-------------|-----------|-------|-----|--------|
```
```
| System | Content Type | Specified | Found | Gap | Status |
|--------|-------------|-----------|-------|-----|--------|
```

**Status categories:**
- `COMPLETE` — Found ≥ Specified (100%+)
- `IN PROGRESS` — Found is 50–99% of Specified
- `EARLY` — Found is 1–49% of Specified
- `NOT STARTED` — Found is 0
**状态分类：**
- `COMPLETE` —— 找到 ≥ 指定（100%+）
- `IN PROGRESS` —— 找到为指定的 50-99%
- `EARLY` —— 找到为指定的 1-49%
- `NOT STARTED` —— 找到为 0

**Priority flags:**
Flag a system as `HIGH PRIORITY` in the report if:
- Status is `NOT STARTED` or `EARLY`, AND
- The system is tagged MVP or Vertical Slice in the systems index, OR
- The systems index shows the system is blocking downstream systems
**优先级标志：**
若满足以下条件，在报告中将系统标记为 `HIGH PRIORITY`：
- 状态为 `NOT STARTED` 或 `EARLY`，且
- 系统在 systems index 中标记为 MVP 或 Vertical Slice，或
- systems index 显示该系统阻塞下游系统

**Summary line:**
- Total content items specified (sum of all Specified column values)
- Total content items found (sum of all Found column values)
- Overall gap percentage: `(Specified - Found) / Specified * 100`
**摘要行：**
- 指定内容项总数（所有 Specified 列值之和）
- 找到内容项总数（所有 Found 列值之和）
- 总体缺口百分比：`(Specified - Found) / Specified * 100`

---

## Phase 4 — Output
## 阶段 4 —— 输出

### Full audit and single-system modes
### 完整审计与单系统模式

Present the gap table and summary to the user. Ask: "May I write the full report to `docs/content-audit-[YYYY-MM-DD].md`?"
向用户展示缺口表与摘要。询问："May I write the full report to `docs/content-audit-[YYYY-MM-DD].md`?"

If yes, write the file:
若是，写文件：

```markdown
# Content Audit — [Date]

## Summary
- **Total specified**: [N] content items across [M] systems
- **Total found**: [N]
- **Gap**: [N] items ([X%] unimplemented)
- **Scope**: [Full audit | System: name]

> Note: Counts are approximations based on file scanning.
> The audit cannot distinguish shipped content from editor/test assets.
> Manual verification is recommended for any HIGH PRIORITY gaps.

## Gap Table

| System | Content Type | Specified | Found | Gap | Status |
|--------|-------------|-----------|-------|-----|--------|

## HIGH PRIORITY Gaps

[List systems flagged HIGH PRIORITY with rationale]

## Per-System Breakdown

### [System Name]
- **GDD**: `design/gdd/[file].md`
- **Content types audited**: [list]
- **Notes**: [any caveats about scan accuracy for this system]

## Recommendation

Focus implementation effort on:
1. [Highest-gap HIGH PRIORITY system]
2. [Second system]
3. [Third system]

## Unspecified Content Counts

The following GDDs describe content without giving explicit counts.
Consider adding counts to improve auditability:
[List of GDDs and content types with "Unspecified"]
```
```markdown
# Content Audit — [Date]

## Summary
- **Total specified**: [N] content items across [M] systems
- **Total found**: [N]
- **Gap**: [N] items ([X%] unimplemented)
- **Scope**: [Full audit | System: name]

> Note: Counts are approximations based on file scanning.
> The audit cannot distinguish shipped content from editor/test assets.
> Manual verification is recommended for any HIGH PRIORITY gaps.

## Gap Table

| System | Content Type | Specified | Found | Gap | Status |
|--------|-------------|-----------|-------|-----|--------|

## HIGH PRIORITY Gaps

[List systems flagged HIGH PRIORITY with rationale]

## Per-System Breakdown

### [System Name]
- **GDD**: `design/gdd/[file].md`
- **Content types audited**: [list]
- **Notes**: [any caveats about scan accuracy for this system]

## Recommendation

Focus implementation effort on:
1. [Highest-gap HIGH PRIORITY system]
2. [Second system]
3. [Third system]

## Unspecified Content Counts

The following GDDs describe content without giving explicit counts.
Consider adding counts to improve auditability:
[List of GDDs and content types with "Unspecified"]
```

After writing the report, ask:
写报告后，询问：

> "Would you like to create backlog stories for any of the content gaps?"
> "Would you like to create backlog stories for any of the content gaps?"

If yes: for each system the user selects, suggest a story title and point them
to `/create-stories [epic-slug]` or `/quick-design` depending on the size of the gap.
若是：对用户选的每个系统，建议故事标题并根据缺口大小指向
`/create-stories [epic-slug]` 或 `/quick-design`。

### --summary mode
### --summary 模式

Print the Gap Table and Summary directly to conversation. Do not write a file.
End with: "Run `/content-audit` without `--summary` to write the full report."
直接将 Gap Table 与 Summary 打印到对话。不写文件。
结束于："Run `/content-audit` without `--summary` to write the full report."

---

## Phase 5 — Next Steps
## 阶段 5 —— 下一步

After the audit, recommend the highest-value follow-up actions:
审计后，推荐最高价值的后续行动：

- If any system is `NOT STARTED` and MVP-tagged → "Run `/design-system [name]` to
  add missing content counts to the GDD before implementation begins."
- If total gap is >50% → "Run `/sprint-plan` to allocate content work across upcoming sprints."
- If backlog stories are needed → "Run `/create-stories [epic-slug]` for each HIGH PRIORITY gap."
- If `--summary` was used → "Run `/content-audit` (no flag) to write the full report to `docs/`."
- 若任何系统为 `NOT STARTED` 且标记 MVP → "Run `/design-system [name]` to
  add missing content counts to the GDD before implementation begins."
- 若总缺口 >50% → "Run `/sprint-plan` to allocate content work across upcoming sprints."
- 若需要待办故事 → "Run `/create-stories [epic-slug]` for each HIGH PRIORITY gap."
- 若用了 `--summary` → "Run `/content-audit` (no flag) to write the full report to `docs/`."

Verdict: **COMPLETE** — content audit finished.
结论：**COMPLETE** —— 内容审计完成。
