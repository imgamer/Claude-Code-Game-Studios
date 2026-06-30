---
name: create-stories
description: "Break a single epic into implementable story files. Reads the epic, its GDD, governing ADRs, and control manifest. Each story embeds its GDD requirement TR-ID, ADR guidance, acceptance criteria, story type, and test evidence path. Run after /create-epics for each epic."
argument-hint: "[epic-slug | epic-path] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
model: sonnet
agent: lead-programmer
---
---
名称：create-stories
描述："将单个史诗拆解为可实现的故事文件。读取史诗及其 GDD、治理 ADR 和控制清单。每个故事嵌入其 GDD 需求 TR-ID、ADR 指引、验收标准、故事类型和测试证据路径。在每个史诗上运行 /create-epics 之后运行。"
argument-hint："[epic-slug | epic-path] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Task, AskUserQuestion
model：sonnet
agent：lead-programmer
---

# Create Stories
# 创建故事

A story is a single implementable behaviour — small enough to complete in one
focused session, self-contained, and fully traceable to a GDD requirement and
an ADR decision. Stories are what developers pick up. Epics are what architects
define.
故事是单一可实现的行为 —— 足够小以便在一次专注会话内完成、自包含，且
完全可追溯到 GDD 需求与 ADR 决策。故事是开发者领取的对象。史诗是架构师
定义的对象。

**Run this skill per epic**, not per layer. Run it for Foundation epics first,
then Core, and so on — matching the dependency order.
**按每个史诗运行此技能**，而非按层运行。先为 Foundation 史诗运行，
再为 Core 运行，以此类推 —— 与依赖顺序匹配。

**Output:** `production/epics/[epic-slug]/story-NNN-[slug].md` files
**输出：** `production/epics/[epic-slug]/story-NNN-[slug].md` 文件

**Previous step:** `/create-epics [system]`
**Next step after stories exist:** `/story-readiness [story-path]` then `/dev-story [story-path]`
**上一步：** `/create-epics [system]`
**故事存在之后的下一步：** `/story-readiness [story-path]`，然后 `/dev-story [story-path]`

---

## 1. Parse Argument
## 1. 解析参数

Extract `--review [full|lean|solo]` if present and store as the review mode
override for this run. If not provided, read `production/review-mode.txt`
(default `lean` if missing). This resolved mode applies to all gate spawns
in this skill — apply the check pattern from `.claude/docs/director-gates.md`
before every gate invocation.
若存在则提取 `--review [full|lean|solo]` 并存为本次运行的评审模式
覆盖值。若未提供，则读取 `production/review-mode.txt`
（缺失时默认为 `lean`）。此解析后的模式适用于本技能中所有门禁
派生 —— 在每次门禁调用之前应用 `.claude/docs/director-gates.md`
中的检查模式。

- `/create-stories [epic-slug]` — e.g. `/create-stories combat`
- `/create-stories production/epics/combat/EPIC.md` — full path also accepted
- No argument — ask: "Which epic would you like to break into stories?"
  Glob `production/epics/*/EPIC.md` and list available epics with their status.
- `/create-stories [epic-slug]` —— 例如 `/create-stories combat`
- `/create-stories production/epics/combat/EPIC.md` —— 也接受完整路径
- 无参数 —— 询问："你想把哪个史诗拆解为故事？"
  Glob `production/epics/*/EPIC.md` 并列出可用史诗及其状态。

---

## 2. Load Everything for This Epic
## 2. 加载该史诗的全部内容

Read in full:
完整读取：

- `production/epics/[epic-slug]/EPIC.md` — epic overview, governing ADRs, GDD requirements table
- The epic's GDD (`design/gdd/[filename].md`) — read all 8 sections, especially Acceptance Criteria, Formulas, and Edge Cases
- All governing ADRs listed in the epic — read the Decision, Implementation Guidelines, Engine Compatibility, and Engine Notes sections
- `docs/architecture/control-manifest.md` — extract rules for this epic's layer; note the Manifest Version date from the header
- `docs/architecture/tr-registry.yaml` — load all TR-IDs for this system
- `production/epics/[epic-slug]/EPIC.md` —— 史诗概览、治理 ADR、GDD 需求表
- 该史诗的 GDD（`design/gdd/[filename].md`）—— 读取全部 8 节，尤其是验收标准、公式与边界情况
- 史诗中列出的所有治理 ADR —— 读取 Decision、Implementation Guidelines、Engine Compatibility 与 Engine Notes 各节
- `docs/architecture/control-manifest.md` —— 提取该史诗所在层的规则；记录头部的 Manifest Version 日期
- `docs/architecture/tr-registry.yaml` —— 加载该系统的全部 TR-ID

**ADR existence validation**: After reading the governing ADRs list from the epic, confirm each ADR file exists on disk. If any ADR file cannot be found, **stop immediately** before decomposing any story:
**ADR 存在性校验**：从史诗读取治理 ADR 列表后，确认每个 ADR 文件在磁盘上存在。若任何 ADR 文件无法找到，在拆解任何故事之前**立即停止**：

> "Epic references [ADR-NNNN: title] but `docs/architecture/[adr-file].md` was not found.
> Check the filename in the epic's Governing ADRs list, or run `/architecture-decision`
> to create it. Cannot create stories until all referenced ADR files are present."
> "史诗引用了 [ADR-NNNN: title]，但 `docs/architecture/[adr-file].md` 未找到。
> 请检查史诗治理 ADR 列表中的文件名，或运行 `/architecture-decision`
> 创建它。在所有被引用的 ADR 文件齐备之前无法创建故事。"

Do not proceed to Step 3 until all referenced ADR files are confirmed present.
在所有被引用的 ADR 文件确认存在之前，不要进入第 3 步。

Report: "Loaded epic [name], GDD [filename], [N] governing ADRs (all confirmed present), control manifest v[date]."
报告："已加载史诗 [name]、GDD [filename]、[N] 个治理 ADR（全部确认存在）、控制清单 v[date]。"

---

## 3. Classify Stories by Type
## 3. 按类型对故事分类

**Story Type Classification** — assign each story a type based on its acceptance criteria:
**故事类型分类** —— 根据每个故事的验收标准为其指派类型：

| Story Type | Assign when criteria reference... |
|---|---|
| **Logic** | Formulas, numerical thresholds, state transitions, AI decisions, calculations |
| **Integration** | Two or more systems interacting, signals crossing boundaries, save/load round-trips |
| **Visual/Feel** | Animation behaviour, VFX, "feels responsive", timing, screen shake, audio sync |
| **UI** | Menus, HUD elements, buttons, screens, dialogue boxes, tooltips |
| **Config/Data** | Balance tuning values, data file changes only — no new code logic |

| 故事类型 | 当标准引用……时指派 |
|---|---|
| **Logic** | 公式、数值阈值、状态转换、AI 决策、计算 |
| **Integration** | 两个或多个系统交互、信号跨越边界、存档/读档往返 |
| **Visual/Feel** | 动画行为、VFX、"手感响应"、时序、屏幕震动、音频同步 |
| **UI** | 菜单、HUD 元素、按钮、屏幕、对话框、工具提示 |
| **Config/Data** | 平衡调优值、仅数据文件改动 —— 无新代码逻辑 |

Mixed stories: assign the type that carries the highest implementation risk.
The type determines what test evidence is required before `/story-done` can close the story.
混合型故事：指派实现风险最高的类型。
该类型决定 `/story-done` 关闭故事之前所需的测试证据。

---

## 4. Decompose the GDD into Stories
## 4. 将 GDD 拆解为故事

For each GDD acceptance criterion:
对于每个 GDD 验收标准：

1. Group related criteria that require the same core implementation
2. Each group = one story
3. Order stories: foundational behaviour first, edge cases last, UI last
1. 将需要相同核心实现的相关标准分组
2. 每组 = 一个故事
3. 故事排序：基础行为在前、边界情况在后、UI 最后

**Story sizing rule:** one story = one focused session (~2-4 hours). If a
group of criteria would take longer, split into two stories.
**故事规模规则：** 一个故事 = 一次专注会话（约 2-4 小时）。若
一组标准耗时更长，则拆分为两个故事。

For each story, determine:
对每个故事，确定：
- **GDD requirement**: which acceptance criterion(ia) does this satisfy?
- **TR-ID**: look up in `tr-registry.yaml`. Use the stable ID. If no match, use `TR-[system]-???` and warn.
- **Governing ADR**: which ADR governs how to implement this?
  - `Status: Accepted` → embed normally
  - `Status: Proposed` → set story `Status: Blocked` with note: "BLOCKED: ADR-NNNN is Proposed — run `/architecture-decision` to advance it"
  - **Multiple ADRs apply**: List all governing ADRs in the story's `Governing ADRs:` field. Designate the one most directly controlling the implementation pattern as primary (first in the list). Others are listed as secondary references.
  - **No ADR applies at all**: Write `ADR: N/A — [brief reason, e.g. "pure data configuration, no architectural pattern required"]` in the story's ADR field. Do NOT leave the field blank — a blank ADR field means "not checked", not "not applicable".
- **Story Type**: from Step 3 classification
- **Engine risk**: from the ADR's Knowledge Risk field
- **GDD 需求**：该故事满足哪些验收标准？
- **TR-ID**：在 `tr-registry.yaml` 中查找。使用稳定 ID。若无匹配，使用 `TR-[system]-???` 并警告。
- **治理 ADR**：哪个 ADR 治理其实现方式？
  - `Status: Accepted` → 正常嵌入
  - `Status: Proposed` → 设定故事 `Status: Blocked`，附注："BLOCKED: ADR-NNNN is Proposed —— 运行 `/architecture-decision` 推进它"
  - **多个 ADR 适用**：在故事的 `Governing ADRs:` 字段列出所有治理 ADR。将最直接控制实现模式的 ADR 指定为主（列表第一个）。其余作为次要参考列出。
  - **无 ADR 适用**：在故事的 ADR 字段写入 `ADR: N/A —— [简短原因，例如 "纯数据配置，无需架构模式"]`。不要留空 —— 空的 ADR 字段意为"未检查"，而非"不适用"。
- **故事类型**：来自第 3 步分类
- **引擎风险**：来自 ADR 的 Knowledge Risk 字段

---

## 4b. QA Lead Story Readiness Gate
## 4b. QA 主管故事就绪门禁

**Review mode check** — apply before spawning QL-STORY-READY:
- `solo` → skip. Note: "QL-STORY-READY skipped — Solo mode." Proceed to Step 5 (present stories for review).
- `lean` → skip (not a PHASE-GATE). Note: "QL-STORY-READY skipped — Lean mode." Proceed to Step 5 (present stories for review).
- `full` → spawn as normal.
**评审模式检查** —— 在派生 QL-STORY-READY 之前应用：
- `solo` → 跳过。附注："QL-STORY-READY skipped —— Solo 模式。"进入第 5 步（呈现故事供评审）。
- `lean` → 跳过（非 PHASE-GATE）。附注："QL-STORY-READY skipped —— Lean 模式。"进入第 5 步（呈现故事供评审）。
- `full` → 正常派生。

After decomposing all stories (Step 4 complete) but before presenting them for write approval, spawn `qa-lead` via Task using gate **QL-STORY-READY** (`.claude/docs/director-gates.md`).
在拆解全部故事之后（第 4 步完成）但呈现它们以获得写入批准之前，通过 Task 派生 `qa-lead` 并使用门禁 **QL-STORY-READY**（`.claude/docs/director-gates.md`）。

Pass: the full story list with acceptance criteria, story types, and TR-IDs; the epic's GDD acceptance criteria for reference.
传递：包含验收标准、故事类型与 TR-ID 的完整故事列表；史诗的 GDD 验收标准作为参考。

Present the QA lead's assessment. For each story flagged as GAPS or INADEQUATE, revise the acceptance criteria before proceeding — stories with untestable criteria cannot be implemented correctly. Once all stories reach ADEQUATE, proceed.
呈现 QA 主管的评估。对于每个被标记为 GAPS 或 INADEQUATE 的故事，在继续之前修订其验收标准 —— 不可测试的标准无法被正确实现。当所有故事达到 ADEQUATE 时，继续。

**Before generating test specs**: Glob `production/qa/qa-plan-*.md` for the most recently modified file. If found, read it and check whether it contains test case specifications for the stories in this epic (look for story titles or slugs in the plan's Automated Tests Required section). If matching specs exist:
**在生成测试规格之前**：Glob `production/qa/qa-plan-*.md` 查找最近修改的文件。若找到，读取它并检查其是否包含本史诗中故事的测试用例规格（在计划的 Automated Tests Required 节查找故事标题或 slug）。若存在匹配规格：
- Use `AskUserQuestion`:
  - Prompt: "A QA plan exists at [path] with test specs for some of these stories. How do you want to proceed?"
  - Options:
    - `Use existing specs from the QA plan — embed them into the story files (Recommended)`
    - `Ask qa-lead to generate fresh specs — override the QA plan`
    - `Skip test spec generation — I'll fill in ## QA Test Cases manually`
- 使用 `AskUserQuestion`：
  - 提示："在 [path] 存在 QA 计划，其中含有部分故事的测试规格。你希望如何进行？"
  - 选项：
    - `使用 QA 计划中的既有规格 —— 将其嵌入故事文件（推荐）`
    - `要求 qa-lead 生成新规格 —— 覆盖 QA 计划`
    - `跳过测试规格生成 —— 我将手动填写 ## QA Test Cases`
- If "Use existing specs": extract the test case specs from the qa-plan for each matching story and embed them directly into the `## QA Test Cases` section. No qa-lead spawn needed for those stories. Only spawn qa-lead for stories with no coverage in the qa-plan.
- If "Generate fresh": proceed with the qa-lead spawn below as normal.
- If "Skip": leave `## QA Test Cases` with a placeholder: `*Test cases not yet defined — run /qa-plan to generate them.*`
- 若选择"使用既有规格"：从 qa-plan 为每个匹配故事提取测试用例规格，并直接嵌入 `## QA Test Cases` 节。这些故事无需派生 qa-lead。仅对在 qa-plan 中无覆盖的故事派生 qa-lead。
- 若选择"生成新规格"：按下方流程正常派生 qa-lead。
- 若选择"跳过"：在 `## QA Test Cases` 留下占位符：`*测试用例尚未定义 —— 运行 /qa-plan 生成。*`

**After ADEQUATE** (or after qa-plan import): for every Logic and Integration story, ask the qa-lead to produce concrete test case specifications — one per acceptance criterion — in this format:
**ADEQUATE 之后**（或 qa-plan 导入之后）：对每个 Logic 与 Integration 故事，要求 qa-lead 为每个验收标准产出具体测试用例规格 —— 格式如下：

```
Test: [criterion text]
  Given: [precondition]
  When: [action]
  Then: [expected result / assertion]
  Edge cases: [boundary values or failure states to test]
```

For Visual/Feel and UI stories, produce manual verification steps instead:
对于 Visual/Feel 与 UI 故事，改为产出手动验证步骤：
```
Manual check: [criterion text]
  Setup: [how to reach the state]
  Verify: [what to look for]
  Pass condition: [unambiguous pass description]
```

These test case specs are embedded directly into each story's `## QA Test Cases` section. The developer implements against these cases. The programmer does not write tests from scratch — QA has already defined what "done" looks like.
这些测试用例规格直接嵌入每个故事的 `## QA Test Cases` 节。开发者依据这些用例实现。程序员无需从零编写测试 —— QA 已定义了"完成"的样子。

---

## 5. Present Stories for Review
## 5. 呈现故事供评审

Before writing any files, present the full story list:
在写入任何文件之前，呈现完整故事列表：

```
## Stories for Epic: [name]

Story 001: [title] — Logic — ADR-NNNN
  Covers: TR-[system]-001 ([1-line summary of requirement])
  Test required: tests/unit/[system]/[slug]_test.[ext]

Story 002: [title] — Integration — ADR-MMMM
  Covers: TR-[system]-002, TR-[system]-003
  Test required: tests/integration/[system]/[slug]_test.[ext]

Story 003: [title] — Visual/Feel — ADR-NNNN
  Covers: TR-[system]-004
  Evidence required: production/qa/evidence/[slug]-evidence.md

[N stories total: N Logic, N Integration, N Visual/Feel, N UI, N Config/Data]
```

Use `AskUserQuestion`:
- Prompt: "May I write these [N] stories to `production/epics/[epic-slug]/`?"
- Options: `[A] Yes — write all [N] stories` / `[B] Not yet — I want to review or adjust first`
使用 `AskUserQuestion`：
- 提示："我可以将这 [N] 个故事写入 `production/epics/[epic-slug]/` 吗？"
- 选项：`[A] 是 —— 写入全部 [N] 个故事` / `[B] 暂不 —— 我想先评审或调整`

---

## 6. Write Story Files
## 6. 写入故事文件

For each story, write `production/epics/[epic-slug]/story-[NNN]-[slug].md`:
对每个故事，写入 `production/epics/[epic-slug]/story-[NNN]-[slug].md`：

```markdown
# Story [NNN]: [title]

> **Epic**: [epic name]
> **Status**: Ready
> **Layer**: [Foundation / Core / Feature / Presentation]
> **Type**: [Logic | Integration | Visual/Feel | UI | Config/Data]
> **Estimate**: [hours or t-shirt size — fill before sprint planning]
> **Manifest Version**: [date from control-manifest.md header]
> **Last Updated**: [set by /dev-story when implementation begins]

## Context

**GDD**: `design/gdd/[filename].md`
**Requirement**: `TR-[system]-NNN`
*(Requirement text lives in `docs/architecture/tr-registry.yaml` — read fresh at review time)*

**ADR Governing Implementation**: [ADR-NNNN: title]
**ADR Decision Summary**: [1-2 sentence summary of what the ADR decided]

**Engine**: [name + version] | **Risk**: [LOW / MEDIUM / HIGH]
**Engine Notes**: [from ADR Engine Compatibility section — post-cutoff APIs, verification required]

**Control Manifest Rules (this layer)**:
- Required: [relevant required pattern]
- Forbidden: [relevant forbidden pattern]
- Guardrail: [relevant performance guardrail]

---

## Acceptance Criteria

*From GDD `design/gdd/[filename].md`, scoped to this story:*

- [ ] [criterion 1 — directly from GDD]
- [ ] [criterion 2]
- [ ] [performance criterion if applicable]

---

## Implementation Notes

*Derived from ADR-NNNN Implementation Guidelines:*

[Specific, actionable guidance from the ADR. Do not paraphrase in ways that
change meaning. This is what the programmer reads instead of the ADR.]

---

## Out of Scope

*Handled by neighbouring stories — do not implement here:*

- [Story NNN+1]: [what it handles]

---

## QA Test Cases

*Written by qa-lead at story creation. The developer implements against these — do not invent new test cases during implementation.*

**[For Logic / Integration stories — automated test specs]:**

- **AC-1**: [criterion text]
  - Given: [precondition]
  - When: [action]
  - Then: [assertion]
  - Edge cases: [boundary values / failure states]

**[For Visual/Feel / UI stories — manual verification steps]:**

- **AC-1**: [criterion text]
  - Setup: [how to reach the state]
  - Verify: [what to look for]
  - Pass condition: [unambiguous pass description]

---

## Test Evidence

**Story Type**: [type]
**Required evidence**:
- Logic: `tests/unit/[system]/[story-slug]_test.[ext]` — must exist and pass
- Integration: `tests/integration/[system]/[story-slug]_test.[ext]` OR playtest doc
- Visual/Feel: `production/qa/evidence/[story-slug]-evidence.md` + sign-off
- UI: `production/qa/evidence/[story-slug]-evidence.md` or interaction test
- Config/Data: smoke check pass (`production/qa/smoke-*.md`)

**Status**: [ ] Not yet created

---

## Dependencies

- Depends on: [Story NNN-1 must be DONE, or "None"]
- Unlocks: [Story NNN+1, or "None"]
```

### Also update `production/epics/[epic-slug]/EPIC.md`
### 同时更新 `production/epics/[epic-slug]/EPIC.md`

Replace the "Stories: Not yet created" line with a populated table:
将 "Stories: Not yet created" 行替换为已填充的表格：

```markdown
## Stories

| # | Story | Type | Status | ADR |
|---|-------|------|--------|-----|
| 001 | [title] | Logic | Ready | ADR-NNNN |
| 002 | [title] | Integration | Ready | ADR-MMMM |
```

### Also update `production/epics/index.md`
### 同时更新 `production/epics/index.md`

Find the row in the index table matching this epic (by epic name or slug). Update its `Stories` column from `Not yet created` to `[N] stories` (where N is the count just written). If the index file does not exist, skip silently.
在索引表中找到与该史诗匹配的行（按史诗名或 slug）。将其 `Stories` 列从 `Not yet created` 更新为 `[N] stories`（其中 N 为刚写入的数量）。若索引文件不存在，则静默跳过。

---

## 7. After Writing
## 7. 写入之后

Use `AskUserQuestion` to close with context-aware next steps:
使用 `AskUserQuestion` 以感知上下文的下一步收尾：

Check:
- Are there other epics in `production/epics/` without stories yet? List them.
- Is this the last epic? If so, include `/sprint-plan` as an option.
检查：
- `production/epics/` 中是否还有尚未生成故事的史诗？列出它们。
- 这是最后一个史诗吗？若是，将 `/sprint-plan` 作为选项之一。

Widget:
- Prompt: "[N] stories written to `production/epics/[epic-slug]/`. What next?"
- Options (include all that apply):
  - `[A] Start implementing — run /story-readiness [first-story-path]` (Recommended)
  - `[B] Create stories for [next-epic-slug] — run /create-stories [slug]` (only if other epics have no stories yet)
  - `[C] Plan the sprint — run /sprint-plan new` (only if all epics have stories)
  - `[D] Stop here for this session`
小组件：
- 提示："[N] 个故事已写入 `production/epics/[epic-slug]/`。下一步？"
- 选项（含所有适用项）：
  - `[A] 开始实现 —— 运行 /story-readiness [first-story-path]`（推荐）
  - `[B] 为 [next-epic-slug] 创建故事 —— 运行 /create-stories [slug]`（仅当其他史诗尚无故事时）
  - `[C] 规划冲刺 —— 运行 /sprint-plan new`（仅当所有史诗都有故事时）
  - `[D] 本次会话到此为止`

Note in output: "Work through stories in order — each story's `Depends on:` field tells you what must be DONE before you can start it."
在输出中注明："按顺序处理故事 —— 每个故事的 `Depends on:` 字段告诉你启动前必须完成什么。"

---

## Collaborative Protocol
## 协作协议

1. **Read before presenting** — load all inputs silently before showing the story list
2. **Ask once** — present all stories for the epic in one summary, not one at a time
3. **Warn on blocked stories** — flag any story with a Proposed ADR before writing
4. **Ask before writing** — get approval for the full story set before writing files
5. **No invention** — acceptance criteria come from GDDs, implementation notes from ADRs, rules from the manifest
6. **Never start implementation** — this skill stops at the story file level
1. **先读取后呈现** —— 在呈现故事列表之前静默加载全部输入
2. **一次性询问** —— 在一份摘要中呈现该史诗的全部故事，而非逐个呈现
3. **对受阻故事发出警告** —— 写入前标记任何带有 Proposed ADR 的故事
4. **写入前征询** —— 写入文件前获取整套故事的批准
5. **禁止杜撰** —— 验收标准来自 GDD、实现说明来自 ADR、规则来自清单
6. **绝不开始实现** —— 本技能止于故事文件层

After writing (or declining):
写入（或拒绝）之后：

- **Verdict: COMPLETE** — [N] stories written to `production/epics/[epic-slug]/`. Run `/story-readiness` → `/dev-story` to begin implementation.
- **Verdict: BLOCKED** — user declined. No story files written.
- **结论：COMPLETE** —— [N] 个故事已写入 `production/epics/[epic-slug]/`。运行 `/story-readiness` → `/dev-story` 开始实现。
- **结论：BLOCKED** —— 用户拒绝。未写入任何故事文件。
