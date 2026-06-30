---
name: consistency-check
description: "Scan all GDDs against the entity registry to detect cross-document inconsistencies: same entity with different stats, same item with different values, same formula with different variables. Grep-first approach — reads registry then targets only conflicting GDD sections rather than full document reads."
argument-hint: "[full | since-last-review | entity:<name> | item:<name>]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, AskUserQuestion
model: sonnet
---
---
名称：consistency-check
描述："对照实体注册表扫描所有 GDD，以检测跨文档不一致：同一实体有不同属性、同一物品有不同数值、同一公式有不同变量。采用 grep 优先方法 — 读取注册表后仅针对冲突的 GDD 部分而非全文阅读。"
argument-hint："[full | since-last-review | entity:<名称> | item:<名称>]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash, AskUserQuestion
model：sonnet
---

# Consistency Check
# 一致性检查

Detects cross-document inconsistencies by comparing all GDDs against the
entity registry (`design/registry/entities.yaml`). Uses a grep-first approach:
reads the registry once, then targets only the GDD sections that mention
registered names — no full document reads unless a conflict needs investigation.
通过将所有 GDD 与实体注册表（`design/registry/entities.yaml`）进行对比，
检测跨文档不一致。采用 grep 优先方法：
读取一次注册表，然后仅针对提及已注册名称的 GDD 部分 —
除非需要调查冲突，否则不进行全文阅读。

**This skill is the write-time safety net.** It catches what `/design-system`'s
per-section checks may have missed and what `/review-all-gdds`'s holistic review
catches too late.
**此技能是写入时的安全网。**它捕获 `/design-system` 的
逐节检查可能遗漏的内容，以及 `/review-all-gdds` 的整体评审
发现过晚的内容。

**When to run:**
- After writing each new GDD (before moving to the next system)
- Before `/review-all-gdds` (so that skill starts with a clean baseline)
- Before `/create-architecture` (inconsistencies poison downstream ADRs)
- On demand: `/consistency-check entity:[name]` to check one entity specifically
**何时运行：**
- 编写每个新 GDD 之后（在移至下一个系统之前）
- 运行 `/review-all-gdds` 之前（以便该技能从干净的基线开始）
- 运行 `/create-architecture` 之前（不一致会污染下游 ADR）
- 按需：`/consistency-check entity:[名称]` 专门检查一个实体

**Output:** Conflict report + optional registry corrections
**输出：** 冲突报告 + 可选的注册表修正

---

## Phase 1: Parse Arguments and Load Registry
## 阶段 1：解析参数并加载注册表

**Modes:**
- No argument / `full` — check all registered entries against all GDDs
- `since-last-review` — check only GDDs modified since the last review report
- `entity:<name>` — check one specific entity across all GDDs
- `item:<name>` — check one specific item across all GDDs
**模式：**
- 无参数 / `full` — 对照所有 GDD 检查所有已注册条目
- `since-last-review` — 仅检查自上次评审报告以来修改的 GDD
- `entity:<名称>` — 在所有 GDD 中检查一个特定实体
- `item:<名称>` — 在所有 GDD 中检查一个特定物品

**Load the registry:**
**加载注册表：**

```
Read path="design/registry/entities.yaml"
```

If the file does not exist or has no entries:
如果文件不存在或没有条目：

> "Entity registry is empty. Run `/design-system` to write GDDs — the registry
> is populated automatically after each GDD is completed. Nothing to check yet."
> "实体注册表为空。运行 `/design-system` 来编写 GDD — 注册表
> 在每个 GDD 完成后自动填充。目前无需检查。"

Stop and exit.
停止并退出。

Build four lookup tables from the registry:
从注册表构建四个查找表：

- **entity_map**: `{ name → { source, attributes, referenced_by } }`
- **item_map**: `{ name → { source, value_gold, weight, ... } }`
- **formula_map**: `{ name → { source, variables, output_range } }`
- **constant_map**: `{ name → { source, value, unit } }`

Count total registered entries. Report:
统计已注册条目总数。报告：

```
Registry loaded: [N] entities, [N] items, [N] formulas, [N] constants
Scope: [full | since-last-review | entity:name]
```

---

## Phase 2: Locate In-Scope GDDs
## 阶段 2：定位范围内的 GDD

```
Glob pattern="design/gdd/*.md"
```

Exclude: `game-concept.md`, `systems-index.md`, `game-pillars.md` — these are
not system GDDs.
排除：`game-concept.md`、`systems-index.md`、`game-pillars.md` — 这些
不是系统 GDD。

For `since-last-review` mode:
对于 `since-last-review` 模式：

```bash
git log --name-only --pretty=format: -- design/gdd/ | grep "\.md$" | sort -u
```

Limit to GDDs modified since the most recent `design/gdd/gdd-cross-review-*.md`
file's creation date.
限制为自最近一次 `design/gdd/gdd-cross-review-*.md`
文件创建日期以来修改的 GDD。

Report the in-scope GDD list before scanning.
在扫描前报告范围内的 GDD 列表。

---

## Phase 3: Grep-First Conflict Scan
## 阶段 3：Grep 优先冲突扫描

For each registered entry, grep every in-scope GDD for the entry's name.
Do NOT do full reads — extract only the matching lines and their immediate
context (-C 3 lines).
对于每个已注册条目，在每个范围内的 GDD 中 grep 该条目的名称。
不要进行全文阅读 — 仅提取匹配行及其紧邻上下文（-C 3 行）。

This is the core optimization: instead of reading 10 GDDs × 400 lines each
(4,000 lines), you grep 50 entity names × 10 GDDs (50 targeted searches,
each returning ~10 lines on a hit).
这是核心优化：不是阅读 10 个 GDD × 每个 400 行
（4,000 行），而是 grep 50 个实体名 × 10 个 GDD（50 次定向搜索，
每次命中返回约 10 行）。

### 3a: Entity Scan
### 3a：实体扫描

For each entity in entity_map:
对于 entity_map 中的每个实体：

```
Grep pattern="[entity_name]" glob="design/gdd/*.md" output_mode="content" -C 3
```

For each GDD hit, extract the values mentioned near the entity name:
- any numeric attributes (counts, costs, durations, ranges, rates)
- any categorical attributes (types, tiers, categories)
- any derived values (totals, outputs, results)
- any other attributes registered in entity_map
对于每个 GDD 命中，提取实体名附近提及的值：
- 任何数值属性（计数、成本、持续时间、范围、比率）
- 任何类别属性（类型、层级、分类）
- 任何衍生值（总计、输出、结果）
- entity_map 中注册的任何其他属性

Compare extracted values against the registry entry.
将提取的值与注册表条目进行对比。

**Conflict detection:**
- Registry says `[entity_name].[attribute] = [value_A]`. GDD says `[entity_name] has [value_B]`. → **CONFLICT**
- Registry says `[item_name].[attribute] = [value_A]`. GDD says `[item_name] is [value_B]`. → **CONFLICT**
- GDD mentions `[entity_name]` but doesn't specify the attribute. → **NOTE** (no conflict, just unverifiable)
**冲突检测：**
- 注册表显示 `[entity_name].[attribute] = [value_A]`。GDD 显示 `[entity_name] has [value_B]`。→ **CONFLICT**
- 注册表显示 `[item_name].[attribute] = [value_A]`。GDD 显示 `[item_name] is [value_B]`。→ **CONFLICT**
- GDD 提及 `[entity_name]` 但未指定属性。→ **NOTE**（无冲突，仅无法验证）

### 3b: Item Scan
### 3b：物品扫描

For each item in item_map, grep all GDDs for the item name. Extract:
- sell price / value / gold value
- weight
- stack rules (stackable / non-stackable)
- category
对于 item_map 中的每个物品，在所有 GDD 中 grep 物品名。提取：
- 售价 / 价值 / 金币价值
- 重量
- 堆叠规则（可堆叠 / 不可堆叠）
- 分类

Compare against registry entry values.
与注册表条目值进行对比。

### 3c: Formula Scan
### 3c：公式扫描

For each formula in formula_map, grep all GDDs for the formula name. Extract:
- variable names mentioned near the formula
- output range or cap values mentioned
对于 formula_map 中的每个公式，在所有 GDD 中 grep 公式名。提取：
- 公式附近提及的变量名
- 提及的输出范围或上限值

Compare against registry entry:
- Different variable names → **CONFLICT**
- Output range stated differently → **CONFLICT**
与注册表条目对比：
- 不同变量名 → **CONFLICT**
- 输出范围表述不同 → **CONFLICT**

### 3d: Constant Scan
### 3d：常量扫描

For each constant in constant_map, grep all GDDs for the constant name. Extract:
- Any numeric value mentioned near the constant name
对于 constant_map 中的每个常量，在所有 GDD 中 grep 常量名。提取：
- 常量名附近提及的任何数值

Compare against registry value:
- Different number → **CONFLICT**
与注册表值对比：
- 不同数字 → **CONFLICT**

---

## Phase 4: Deep Investigation (Conflicts Only)
## 阶段 4：深入调查（仅限冲突）

For each conflict found in Phase 3, do a targeted full-section read of the
conflicting GDD to get precise context:
对于阶段 3 中发现的每个冲突，对冲突 GDD 进行定向的整节阅读以获取精确上下文：

```
Read path="design/gdd/[conflicting_gdd].md"
```

(Or use Grep with wider context if the file is large)
（或如果文件较大，使用带更宽上下文的 Grep）

Confirm the conflict with full context. Determine:
1. **Which GDD is correct?** Check the `source:` field in the registry — the
   source GDD is the authoritative owner. Any other GDD that contradicts it
   is the one that needs updating.
2. **Is the registry itself out of date?** If the source GDD was updated after
   the registry entry was written (check git log), the registry may be stale.
3. **Is this a genuine design change?** If the conflict represents an intentional
   design decision, the resolution is: update the source GDD, update the registry,
   then fix all other GDDs.
用完整上下文确认冲突。确定：
1. **哪个 GDD 是正确的？** 检查注册表中的 `source:` 字段 —
   源 GDD 是权威所有者。任何与之矛盾的 GDD
   都是需要更新的那个。
2. **注册表本身是否过时？** 如果源 GDD 在
   注册表条目写入后被更新（检查 git 日志），注册表可能已过期。
3. **这是真正的设计变更吗？** 如果冲突代表有意的
   设计决策，解决方案是：更新源 GDD、更新注册表，
   然后修复所有其他 GDD。

For each conflict, classify:
- **🔴 CONFLICT** — same named entity/item/formula/constant with different values
  in different GDDs. Must resolve before architecture begins.
- **⚠️ STALE REGISTRY** — source GDD value changed but registry not updated.
  Registry needs updating; other GDDs may be correct already.
- **ℹ️ UNVERIFIABLE** — entity mentioned but no comparable attribute stated.
  Not a conflict; just noting the reference.
对每个冲突进行分类：
- **🔴 CONFLICT** — 同一命名的实体/物品/公式/常量在不同 GDD 中有不同值。
  必须在架构开始前解决。
- **⚠️ STALE REGISTRY** — 源 GDD 值已变更但注册表未更新。
  注册表需要更新；其他 GDD 可能已经正确。
- **ℹ️ UNVERIFIABLE** — 实体被提及但无可比较的属性。
  非冲突；仅记录引用。

---

## Phase 5: Output Report
## 阶段 5：输出报告

```
## Consistency Check Report
Date: [date]
Registry entries checked: [N entities, N items, N formulas, N constants]
GDDs scanned: [N] ([list names])

---

### Conflicts Found (must resolve before architecture)

🔴 [Entity/Item/Formula/Constant Name]
   Registry (source: [gdd]): [attribute] = [value]
   Conflict in [other_gdd].md: [attribute] = [different_value]
   → Resolution needed: [which doc to change and to what]

---

### Stale Registry Entries (registry behind the GDD)

⚠️ [Entry Name]
   Registry says: [value] (written [date])
   Source GDD now says: [new value]
   → Update registry entry to match source GDD, then check referenced_by docs.

---

### Unverifiable References (no conflict, informational)

ℹ️ [gdd].md mentions [entity_name] but states no comparable attributes.
   No conflict detected. No action required.

---

### Clean Entries (no issues found)

✅ [N] registry entries verified across all GDDs with no conflicts.

---

Verdict: PASS | CONFLICTS FOUND
```

**Verdict:**
- **PASS** — no conflicts. Registry and GDDs agree on all checked values.
- **CONFLICTS FOUND** — one or more conflicts detected. List resolution steps.
**裁决：**
- **PASS** — 无冲突。注册表和 GDD 在所有检查的值上一致。
- **CONFLICTS FOUND** — 检测到一个或多个冲突。列出解决步骤。

---

## Phase 6: Registry Corrections
## 阶段 6：注册表修正

If stale registry entries were found, ask:
如果发现过期的注册表条目，询问：

> "May I update `design/registry/entities.yaml` to fix the [N] stale entries?"
> "我可以更新 `design/registry/entities.yaml` 以修复 [N] 个过期条目吗？"

For each stale entry:
- Update the `value` / attribute field
- Set `revised:` to today's date
- Add a YAML comment with the old value: `# was: [old_value] before [date]`
对于每个过期条目：
- 更新 `value` / 属性字段
- 将 `revised:` 设为今天日期
- 添加带旧值的 YAML 注释：`# was: [old_value] before [date]`

If new entries were found in GDDs that are not in the registry, ask:
如果在 GDD 中发现注册表中没有的新条目，询问：

> "Found [N] entities/items mentioned in GDDs that aren't in the registry yet.
> May I add them to `design/registry/entities.yaml`?"
> "在 GDD 中发现 [N] 个尚未在注册表中的实体/物品。
> 我可以将它们添加到 `design/registry/entities.yaml` 吗？"

Only add entries that appear in more than one GDD (true cross-system facts).
仅添加出现在多个 GDD 中的条目（真正的跨系统事实）。

**Never delete registry entries.** Set `status: deprecated` if an entry is removed
from all GDDs.
**永远不要删除注册表条目。** 如果条目从所有 GDD 中移除，则设置 `status: deprecated`。

After writing: Verdict: **COMPLETE** — consistency check finished.
If conflicts remain unresolved: Verdict: **BLOCKED** — [N] conflicts need manual resolution before architecture begins.
写入后：裁决：**COMPLETE** — 一致性检查完成。
如果冲突仍未解决：裁决：**BLOCKED** — [N] 个冲突需要在架构开始前手动解决。

### 6b: Append to Reflexion Log
### 6b：追加到反思日志

If any 🔴 CONFLICT entries were found (regardless of whether they were resolved),
append an entry to `docs/consistency-failures.md` for each conflict:
如果发现任何 🔴 CONFLICT 条目（无论是否已解决），
为每个冲突向 `docs/consistency-failures.md` 追加一条记录：

```markdown
### [YYYY-MM-DD] — /consistency-check — 🔴 CONFLICT
**Domain**: [system domain(s) involved]
**Documents involved**: [source GDD] vs [conflicting GDD]
**What happened**: [specific conflict — entity name, attribute, differing values]
**Resolution**: [how it was fixed, or "Unresolved — manual action needed"]
**Pattern**: [generalised lesson, e.g. "Item values defined in combat GDD were not
referenced in economy GDD before authoring — always check entities.yaml first"]
```

If `docs/consistency-failures.md` does not exist, create it with this header before appending:
如果 `docs/consistency-failures.md` 不存在，在追加前使用此头部创建：

```markdown
# Consistency Failure Log

<!-- Auto-maintained by /consistency-check. Do not edit manually. -->
<!-- One entry per detected conflict, in chronological order. -->

| Date | GDD A | GDD B | Conflict Type | Status |
|------|-------|-------|---------------|--------|
```

Then append the new conflict entries. Never skip logging — a missing file is not a reason to lose conflict history.
然后追加新的冲突条目。永远不要跳过日志记录 — 文件缺失不是丢失冲突历史的理由。

---

## Phase 7: Session State and Closing
## 阶段 7：会话状态与收尾

Silently append to `production/session-state/active.md` (create the file if it does not exist):
静默追加到 `production/session-state/active.md`（如果文件不存在则创建）：

```
<!-- CONSISTENCY-CHECK: [date] | GDDs checked: [N] | Conflicts found: [N] | Report: docs/consistency-report-[date].md -->
```

Then close with an `AskUserQuestion` widget:
然后用 `AskUserQuestion` 小部件收尾：

- **Prompt**: "Consistency check complete — [N] conflicts found. What next?"
- **Options**:
  - `[A] Fix the highest-priority conflict now`
  - `[B] Save full report and stop`
  - `[C] Run /design-review on the most conflicted GDD`
  - `[D] Stop here`
- **提示**："一致性检查完成 — 发现 [N] 个冲突。下一步？"
- **选项**：
  - `[A] 现在修复最高优先级的冲突`
  - `[B] 保存完整报告并停止`
  - `[C] 对冲突最多的 GDD 运行 /design-review`
  - `[D] 在此停止`

Never end the skill with plain text. Always close with this widget.
永远不要以纯文本结束技能。始终用此小部件收尾。

---

## Recovery / Reference
## 恢复 / 参考

- **If PASS**: Run `/review-all-gdds` for holistic design-theory review, or
  `/create-architecture` if all MVP GDDs are complete.
- **If CONFLICTS FOUND**: Fix the flagged GDDs, then re-run
  `/consistency-check` to confirm resolution.
- **If STALE REGISTRY**: Update the registry (Phase 6), then re-run to verify.
- Run `/consistency-check` after writing each new GDD to catch issues early,
  not at architecture time.
- **如果 PASS**：运行 `/review-all-gdds` 进行整体设计理论评审，或
  如果所有 MVP GDD 都已完成，运行 `/create-architecture`。
- **如果 CONFLICTS FOUND**：修复被标记的 GDD，然后重新运行
  `/consistency-check` 以确认已解决。
- **如果 STALE REGISTRY**：更新注册表（阶段 6），然后重新运行以验证。
- 在编写每个新 GDD 后运行 `/consistency-check` 以尽早发现问题，
  而不是在架构阶段。
