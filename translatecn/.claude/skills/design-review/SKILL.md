---
name: design-review
description: "Reviews a game design document for completeness, internal consistency, implementability, and adherence to project design standards. Run this before handing a design document to programmers."
argument-hint: "[path-to-design-doc] [--depth full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model: sonnet
---
---
名称：design-review
描述："评审游戏设计文档的完整性、内部一致性、可实现性与对项目设计标准的遵循。在将设计文档交给程序员之前运行此技能。"
argument-hint："[path-to-design-doc] [--depth full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Task, AskUserQuestion
model：sonnet
---

## Phase 0: Parse Arguments
## 阶段 0：解析参数

Extract `--depth [full|lean|solo]` if present. Default is `full` when no flag is given.
提取 `--depth [full|lean|solo]`（若存在）。无标志时默认为 `full`。

**Note**: `--depth` controls the *analysis depth* of this skill (how many specialist agents are spawned). It is independent of the global review mode in `production/review-mode.txt`, which controls director gate spawning. These are two different concepts — `--depth` is about how thoroughly *this* skill analyses the document.
**注意**：`--depth` 控制*本技能的分析深度*（启动多少专家智能体）。它独立于
`production/review-mode.txt` 中的全局评审模式（后者控制总监门禁启动）。这是
两个不同概念 —— `--depth` 关于*本技能*分析文档的彻底程度。

- **`full`**: Complete review — all phases + specialist agent delegation (Phase 3b)
- **`lean`**: All phases, no specialist agents — faster, single-session analysis
- **`solo`**: Phases 1-4 only, no delegation, no Phase 5 next-step prompt — use when called from within another skill
- **`full`**：完整评审 —— 所有阶段 + 专家智能体委派（阶段 3b）
- **`lean`**：所有阶段，无专家智能体 —— 更快、单会话分析
- **`solo`**：仅阶段 1-4，无委派，无阶段 5 下一步提示 —— 在从其他技能内调用时
  使用

---

## Phase 1: Load Documents
## 阶段 1：加载文档

Read the target design document in full. Read CLAUDE.md to understand project context and standards. Read related design documents referenced or implied by the target doc (check `design/gdd/` for related systems).
完整读取目标设计文档。读取 CLAUDE.md 了解项目上下文与标准。读取目标文档引用或
暗示的相关设计文档（在 `design/gdd/` 检查相关系统）。

**Dependency graph validation:** For every system listed in the Dependencies section, use Glob to check whether its GDD file exists in `design/gdd/`. Flag any that don't exist yet — these are broken references that downstream authors will hit.
**依赖图验证**：对 Dependencies 段落中列出的每个系统，使用 Glob 检查其 GDD 文件
是否存在于 `design/gdd/`。标记任何尚不存在的 —— 这些是下游作者会撞上的破损
引用。

**Lore/narrative alignment:** If `design/gdd/game-concept.md` or any file in `design/narrative/` exists, read it. Note any mechanical choices in this GDD that contradict established world rules, tone, or design pillars. Pass this context to `game-designer` in Phase 3b.
**传说/叙事对齐**：若 `design/gdd/game-concept.md` 或 `design/narrative/` 中任何
文件存在，读取它。记录本 GDD 中与已确立世界规则、基调或设计支柱矛盾的任何机制
选择。在阶段 3b 将此上下文传给 `game-designer`。

**Prior review check:** Check whether `design/gdd/reviews/[doc-name]-review-log.md` exists. If it does, read the most recent entry — note what verdict was given and what blocking items were listed. This session is a re-review; track whether prior items were addressed.
**先前评审检查**：检查 `design/gdd/reviews/[doc-name]-review-log.md` 是否存在。
若存在，读取最近条目 —— 记录给定结论与所列阻塞项。本会话是再评审；追踪先前
项是否已处理。

---

## Phase 2: Completeness Check
## 阶段 2：完整性检查

Evaluate against the Design Document Standard checklist:
对照设计文档标准检查清单评估：

- [ ] Has Overview section (one-paragraph summary)
- [ ] Has Player Fantasy section (intended feeling)
- [ ] Has Detailed Rules section (unambiguous mechanics)
- [ ] Has Formulas section (all math defined with variables)
- [ ] Has Edge Cases section (unusual situations handled)
- [ ] Has Dependencies section (other systems listed)
- [ ] Has Tuning Knobs section (configurable values identified)
- [ ] Has Acceptance Criteria section (testable success conditions)
- [ ] 有 Overview 段落（一段摘要）
- [ ] 有 Player Fantasy 段落（预期感受）
- [ ] 有 Detailed Rules 段落（明确机制）
- [ ] 有 Formulas 段落（所有数学用变量定义）
- [ ] 有 Edge Cases 段落（异常情况已处理）
- [ ] 有 Dependencies 段落（列出其他系统）
- [ ] 有 Tuning Knobs 段落（识别可配置值）
- [ ] 有 Acceptance Criteria 段落（可测试成功条件）

---

## Phase 3: Consistency and Implementability
## 阶段 3：一致性与可实现性

**Internal consistency:**
- Do the formulas produce values that match the described behavior?
- Do edge cases contradict the main rules?
- Are dependencies bidirectional (does the other system know about this one)?
**内部一致性：**
- 公式产生的值是否与描述行为匹配？
- 边界情况是否与主规则矛盾？
- 依赖是否双向（另一系统是否知道本系统）？

**Implementability:**
- Are the rules precise enough for a programmer to implement without guessing?
- Are there any "hand-wave" sections where details are missing?
- Are performance implications considered?
**可实现性：**
- 规则是否足够精确使程序员可无需猜测地实现？
- 是否有任何 "挥手带过" 的段落缺失细节？
- 是否考虑了性能影响？

**Cross-system consistency:**
- Does this conflict with any existing mechanic?
- Does this create unintended interactions with other systems?
- Is this consistent with the game's established tone and pillars?
**跨系统一致性：**
- 是否与任何现有机制冲突？
- 是否与其他系统产生未预期交互？
- 是否与游戏已确立基调与支柱一致？

---

## Phase 3b: Adversarial Specialist Review (full mode only)
## 阶段 3b：对抗式专家评审（仅 full 模式）

**Skip this phase in `lean` or `solo` mode.**
**在 `lean` 或 `solo` 模式跳过此阶段。**

**This phase is MANDATORY in full mode.** Do not skip it.
**此阶段在 full 模式是强制的。** 不要跳过。

**Before spawning any agents**, print this notice:
> "Full review: spawning specialist agents in parallel. This typically takes 8–15 minutes. Use `--review lean` for faster single-session analysis."
**在启动任何智能体之前**，打印此通知：
> "Full review: spawning specialist agents in parallel. This typically takes 8–15 minutes. Use `--review lean` for faster single-session analysis."

### Step 1 — Identify all domains the GDD touches
### 步骤 1 —— 识别 GDD 触及的所有领域

Read the GDD and identify every domain present. A GDD can touch multiple domains simultaneously — be thorough. Common signals:
读取 GDD 并识别存在的每个领域。一个 GDD 可同时触及多个领域 —— 要彻底。常见信号：

| If the GDD contains... | Spawn these agents |
|------------------------|-------------------|
| Costs, prices, drops, rewards, economy | `economy-designer` |
| Combat stats, damage, health, DPS | `game-designer`, `systems-designer` |
| AI behaviour, pathfinding, targeting | `ai-programmer` |
| Level layout, spawning, wave structure | `level-designer` |
| Player progression, XP, unlocks | `economy-designer`, `game-designer` |
| UI, HUD, menus, player-facing displays | `ux-designer`, `ui-programmer` |
| Dialogue, quests, story, lore | `narrative-director` |
| Animation, feel, timing, juice | `gameplay-programmer` |
| Multiplayer, sync, replication | `network-programmer` |
| Audio cues, music triggers | `audio-director` |
| Performance, draw calls, memory | `performance-analyst` |
| Engine-specific patterns or APIs | Primary engine specialist (from `.claude/docs/technical-preferences.md`) |
| Acceptance criteria, test coverage | `qa-lead` |
| Data schema, resource structure | `systems-designer` |
| Any gameplay system | `game-designer` (always) |
| 若 GDD 包含... | 启动这些智能体 |
|------------------------|-------------------|
| 成本、价格、掉落、奖励、经济 | `economy-designer` |
| 战斗属性、伤害、生命、DPS | `game-designer`, `systems-designer` |
| AI 行为、寻路、目标选择 | `ai-programmer` |
| 关卡布局、刷怪、波次结构 | `level-designer` |
| 玩家进度、XP、解锁 | `economy-designer`, `game-designer` |
| UI、HUD、菜单、面向玩家的显示 | `ux-designer`, `ui-programmer` |
| 对话、任务、故事、传说 | `narrative-director` |
| 动画、手感、时机、juice | `gameplay-programmer` |
| 多人、同步、复制 | `network-programmer` |
| 音频提示、音乐触发 | `audio-director` |
| 性能、绘制调用、内存 | `performance-analyst` |
| 引擎特定模式或 API | 主引擎专家（来自 `.claude/docs/technical-preferences.md`） |
| 验收标准、测试覆盖 | `qa-lead` |
| 数据架构、资源结构 | `systems-designer` |
| 任何玩法系统 | `game-designer`（始终） |

Spawn `game-designer` for all GDDs that describe gameplay mechanics or player-facing rules.
Spawn `systems-designer` for all GDDs that contain formulas or system interaction rules.
These are the most common baselines — but not required for pure UI specs, audio specs, or lore documents. Use the domain table above to determine which specialists are truly relevant.
对所有描述玩法机制或面向玩家规则的 GDD 启动 `game-designer`。
对所有含公式或系统交互规则的 GDD 启动 `systems-designer`。
这些是最常见基线 —— 但纯 UI 规格、音频规格或传说文档不需要。用上面领域表
决定哪些专家真正相关。

### Step 2 — Spawn all relevant specialists in parallel
### 步骤 2 —— 并行启动所有相关专家

**CRITICAL: Task in this skill spawns a SUBAGENT — a separate independent Claude session
with its own context window. It is NOT task tracking. Do NOT simulate specialist
perspectives internally. Do NOT reason through domain views yourself. You MUST issue
actual Task calls. A simulated review is not a specialist review.**
**关键：本技能中的 Task 启动一个子智能体 —— 一个独立的 Claude 会话，有自己
的上下文窗口。它不是任务跟踪。不要在内部模拟专家视角。不要自己推理领域观点。
你必须发出实际 Task 调用。模拟评审不是专家评审。**

Issue all Task calls simultaneously. Do NOT spawn one at a time.
同时发出所有 Task 调用。不要逐个启动。

**Prompt each specialist adversarially:**
> "Here is the GDD for [system] and the main review's structural findings so far.
> Your job is NOT to validate this design — your job is to find problems.
> Challenge the design choices from your domain expertise. What is wrong,
> underspecified, likely to cause problems, or missing entirely?
> Be specific and critical. Disagreement with the main review is welcome."
**对抗式地提示每个专家：**
> "Here is the GDD for [system] and the main review's structural findings so far.
> Your job is NOT to validate this design — your job is to find problems.
> Challenge the design choices from your domain expertise. What is wrong,
> underspecified, likely to cause problems, or missing entirely?
> Be specific and critical. Disagreement with the main review is welcome."

**Additional instructions per agent type:**
**每个智能体类型的额外指令：**

- **`game-designer`**: Anchor your review to the Player Fantasy stated in Section B of this GDD. Does this design actually deliver that fantasy? Would a player feel the intended experience? Flag any rules that serve implementability but undermine the stated feeling.
- **`game-designer`**：将评审锚定到本 GDD 段落 B 中陈述的 Player Fantasy。此设计
  是否真正交付了该幻想？玩家会感受到预期体验吗？标记任何服务于可实现性但
  削弱既定感受的规则。

- **`systems-designer`**: For every formula in the GDD, plug in boundary values (minimum and maximum plausible inputs). Report whether any outputs go degenerate — negative values, division by zero, infinity, or nonsensical results at the extremes.
- **`systems-designer`**：对 GDD 中每个公式，代入边界值（最小与最大合理输入）。
  报告是否有任何输出退化 —— 负值、除以零、无穷或极端处的无意义结果。

- **`qa-lead`**: Review every acceptance criterion. Flag any that are not independently testable — phrases like "feels balanced", "works correctly", "performs well" are not ACs. Suggest concrete rewrites for any that fail this test.
- **`qa-lead`**：审查每个验收标准。标记任何不可独立测试的 —— "feels balanced"、
  "works correctly"、"performs well" 等短语不是验收标准。为未通过的提出具体
  重写建议。

### Step 3 — Senior lead review
### 步骤 3 —— 高级主管评审

After all specialists respond, spawn `creative-director` as the **senior reviewer**:
- Provide: the GDD, all specialist findings, any disagreements between them
- Ask: "Synthesise these findings. What are the most important issues? Do you agree with the specialists? What is your overall verdict on this design?"
- The creative-director's synthesis becomes the **final verdict** in Phase 4.
所有专家回复后，启动 `creative-director` 作为 **senior reviewer**：
- 提供：GDD、所有专家发现、它们之间的任何分歧
- 询问："Synthesise these findings. What are the most important issues? Do you agree with the specialists? What is your overall verdict on this design?"
- creative-director 的综合成为阶段 4 的**最终结论**。

### Step 4 — Surface disagreements
### 步骤 4 —— 暴露分歧

If specialists disagree with each other or with the creative-director, do NOT silently pick one view. Present the disagreement explicitly in Phase 4 so the user can adjudicate.
若专家之间或与 creative-director 不一致，不要默默选一方。在阶段 4 显式展示
分歧以便用户裁决。

Mark every finding with its source: `[game-designer]`, `[economy-designer]`, `[creative-director]` etc.
每条发现标记来源：`[game-designer]`、`[economy-designer]`、`[creative-director]` 等。

---

## Phase 4: Output Review
## 阶段 4：输出评审

```
## Design Review: [Document Title]
Specialists consulted: [list agents spawned]
Re-review: [Yes — prior verdict was X on YYYY-MM-DD / No — first review]

### Completeness: [X/8 sections present]
[List missing sections]

### Dependency Graph
[List each declared dependency and whether its GDD file exists on disk]
- ✓ enemy-definition-data.md — exists
- ✗ loot-system.md — NOT FOUND (file does not exist yet)

### Required Before Implementation
[Numbered list — blocking issues only. Each item tagged with source agent.]

### Recommended Revisions
[Numbered list — important but not blocking. Source-tagged.]

### Specialist Disagreements
[Any cases where agents disagreed with each other or with the main review.
Present both sides — do not silently resolve.]

### Nice-to-Have
[Minor improvements, low priority.]

### Senior Verdict [creative-director]
[Creative director's synthesis and overall assessment.]

### Scope Signal
Estimate implementation scope based on: dependency count, formula count,
systems touched, and whether new ADRs are required.
- **S** — single system, no formulas, no new ADRs, <3 dependencies
- **M** — moderate complexity, 1-2 formulas, 3-6 dependencies
- **L** — multi-system integration, 3+ formulas, may require new ADR
- **XL** — cross-cutting concern, 5+ dependencies, multiple new ADRs likely
Label clearly: "Rough scope signal: M (producer should verify before sprint planning)"

### Verdict: [APPROVED / NEEDS REVISION / MAJOR REVISION NEEDED]
```

This skill is read-only — no files are written during Phase 4.
本技能只读 —— 阶段 4 中不写任何文件。

---

## Phase 5: Next Steps
## 阶段 5：下一步

Use `AskUserQuestion` for ALL closing interactions. Never plain text.
所有结束交互使用 `AskUserQuestion`。绝不用纯文本。

**First widget — what to do next:**
**第一个组件 —— 下一步做什么：**

If APPROVED (first-pass, no revision needed), proceed directly to the systems-index widget, review-log widget, then the final closing widget. Do not show a separate "what to do" widget — the final closing widget covers next steps.
若 APPROVED（首次通过，无需修订），直接进入 systems-index 组件、review-log
组件，然后最终结束组件。不显示单独的 "what to do" 组件 —— 最终结束组件覆盖
下一步。

If NEEDS REVISION or MAJOR REVISION NEEDED, options:
- `[A] Revise the GDD now — address blocking items together`
- `[B] Stop here — revise in a separate session`
- `[C] Accept as-is and move on (only if all items are advisory)`
若 NEEDS REVISION 或 MAJOR REVISION NEEDED，选项：
- `[A] Revise the GDD now — address blocking items together`
- `[B] Stop here — revise in a separate session`
- `[C] Accept as-is and move on (only if all items are advisory)`

**If user selects [A] — Revise now:**
**若用户选 [A] —— 立即修订：**

Work through all blocking items, asking for design decisions only where you cannot resolve the issue from the GDD and existing docs alone. Group all design-decision questions into a single multi-tab `AskUserQuestion` before making any edits — do not interrupt mid-revision for each blocker individually.
处理所有阻塞项，仅在无法仅从 GDD 与现有文档解决时询问设计决策。在进行任何编辑
之前，将所有设计决策问题组合到单个多 tab `AskUserQuestion` —— 不要在修订中途
为每个阻塞项单独打断。

After all revisions are complete, show a summary table (blocker → fix applied) and use `AskUserQuestion` for a **post-revision closing widget**:
所有修订完成后，展示摘要表（阻塞项 → 已应用修复）并使用 `AskUserQuestion`
做 **post-revision closing widget**：

- Prompt: "Revisions complete — [N] blockers resolved. What next?"
- Note current context usage: if context is above ~50%, add: "(Recommended: /clear before re-review — this session has used X% context. A full re-review runs 5 agents and needs clean context.)"
- Options:
  - `[A] Re-review in a new session — run /design-review [doc-path] after /clear`
  - `[B] Accept revisions and mark Approved — update systems index, skip re-review`
  - `[C] Move to next system — /design-system [next-system] (#N in design order)`
  - `[D] Stop here`
- 提示："Revisions complete — [N] blockers resolved. What next?"
- 注明当前上下文使用：若上下文高于约 50%，添加："(Recommended: /clear before re-review — this session has used X% context. A full re-review runs 5 agents and needs clean context.)"
- 选项：
  - `[A] Re-review in a new session — run /design-review [doc-path] after /clear`
  - `[B] Accept revisions and mark Approved — update systems index, skip re-review`
  - `[C] Move to next system — /design-system [next-system] (#N in design order)`
  - `[D] Stop here`

Never end the revision flow with plain text. Always close with this widget.
修订流程绝不要以纯文本结束。始终以此组件收尾。

**Second widget — tracking records (combined, for APPROVED path):**
**第二个组件 —— 跟踪记录（合并，针对 APPROVED 路径）：**

When the verdict is APPROVED, use a single `AskUserQuestion` with `multiSelect: true` to batch the two tracking updates:
- Prompt: "Verdict: APPROVED. I can update the tracking records now. Select any you'd like me to complete:"
- Options:
  - `Update systems-index.md status to 'Approved' for [system]`
  - `Append approval entry to design/gdd/reviews/[doc-name]-review-log.md`
当结论为 APPROVED 时，使用单个 `multiSelect: true` 的 `AskUserQuestion`
批量处理两个跟踪更新：
- 提示："Verdict: APPROVED. I can update the tracking records now. Select any you'd like me to complete:"
- 选项：
  - `Update systems-index.md status to 'Approved' for [system]`
  - `Append approval entry to design/gdd/reviews/[doc-name]-review-log.md`

If the review-log option is selected, append the same format as below. Execute both selected actions before showing the final closing widget.
若选中 review-log 选项，追加以下相同格式。在展示最终结束组件之前执行所有选中
操作。

When the verdict is NEEDS REVISION or MAJOR REVISION NEEDED, use separate widgets as before:
当结论为 NEEDS REVISION 或 MAJOR REVISION NEEDED 时，使用单独组件如前：

Use a second `AskUserQuestion`:
- Prompt: "May I update `design/gdd/systems-index.md` to mark [system] as [In Review / Approved]?"
- Options: `[A] Yes — update it` / `[B] No — leave it as-is`
使用第二个 `AskUserQuestion`：
- 提示："May I update `design/gdd/systems-index.md` to mark [system] as [In Review / Approved]?"
- 选项：`[A] Yes — update it` / `[B] No — leave it as-is`

Use a third `AskUserQuestion`:
- Prompt: "May I append this review summary to `design/gdd/reviews/[doc-name]-review-log.md`? This creates a revision history so future re-reviews can track what changed."
- Options: `[A] Yes — append to review log` / `[B] No — skip`
使用第三个 `AskUserQuestion`：
- 提示："May I append this review summary to `design/gdd/reviews/[doc-name]-review-log.md`? This creates a revision history so future re-reviews can track what changed."
- 选项：`[A] Yes — append to review log` / `[B] No — skip`

If yes, append an entry in this format:
若是，按此格式追加条目：
```
## Review — [YYYY-MM-DD] — Verdict: [APPROVED / NEEDS REVISION / MAJOR REVISION NEEDED]
Scope signal: [S/M/L/XL]
Specialists: [list]
Blocking items: [count] | Recommended: [count]
Summary: [2-3 sentence summary of key findings from creative-director verdict]
Prior verdict resolved: [Yes / No / First review]
```

---

**Final closing widget — always show after all file writes complete:**
**最终结束组件 —— 所有文件写入完成后始终展示：**

Once the systems-index and review-log widgets are answered, check project state and show one final `AskUserQuestion`:
systems-index 与 review-log 组件被回答后，检查项目状态并展示最后一个
`AskUserQuestion`：

Before building options, read:
- `design/gdd/systems-index.md` — find any system with Status: In Review or NEEDS REVISION (other than the one just reviewed)
- Count `.md` files in `design/gdd/` (excluding game-concept.md, systems-index.md) to determine if `/review-all-gdds` is worth offering (≥2 GDDs)
- Find the next system with Status: Not Started in design order
构建选项之前，读取：
- `design/gdd/systems-index.md` —— 找出任何 Status 为 In Review 或 NEEDS
  REVISION 的系统（除刚评审的）
- 统计 `design/gdd/` 中的 `.md` 文件（除 game-concept.md、systems-index.md）
  以决定是否值得提供 `/review-all-gdds`（≥2 GDD）
- 在设计顺序中找出下一个 Status: Not Started 的系统

Build the option list dynamically — only include options that are genuinely next:
动态构建选项列表 —— 仅包含真正下一步的选项：
- `[_] Run /design-review [other-gdd-path] — [system name] is still [In Review / NEEDS REVISION]` (include if another GDD needs review)
- `[_] Run /consistency-check — verify this GDD's values don't conflict with existing GDDs` (always include if ≥1 other GDD exists)
- `[_] Run /review-all-gdds — holistic design-theory review across all designed systems` (include if ≥2 GDDs exist)
- `[_] Run /design-system [next-system] — next in design order` (always include, name the actual system)
- `[_] Stop here`
- `[_] Run /design-review [other-gdd-path] — [system name] is still [In Review / NEEDS REVISION]`（若另一 GDD 需评审则包含）
- `[_] Run /consistency-check — verify this GDD's values don't conflict with existing GDDs`（若存在 ≥1 其他 GDD 则始终包含）
- `[_] Run /review-all-gdds — holistic design-theory review across all designed systems`（若存在 ≥2 GDD 则包含）
- `[_] Run /design-system [next-system] — next in design order`（始终包含，并指明实际系统）
- `[_] Stop here`

Assign letters A, B, C… only to included options. Mark the most pipeline-advancing option as `(recommended)`.
仅向所包含选项分配字母 A、B、C…。将最推进流水线的选项标记为
`(recommended)`。

Never end the skill with plain text after file writes. Always close with this widget.
文件写入后绝不以纯文本结束技能。始终以此组件收尾。
