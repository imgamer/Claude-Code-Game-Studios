---
name: story-readiness
description: "Validate that a story file is implementation-ready. Checks for embedded GDD requirements, ADR references, engine notes, clear acceptance criteria, and no open design questions. Produces READY / NEEDS WORK / BLOCKED verdict with specific gaps. Use when user says 'is this story ready', 'can I start on this story', 'is story X ready to implement'."
argument-hint: "[story-file-path or 'all' or 'sprint']"
user-invocable: true
allowed-tools: Read, Glob, Grep, AskUserQuestion, Task
model: sonnet
---
---
名称：story-readiness
描述："验证故事文件是否准备好实施。检查嵌入的 GDD 要求、ADR 引用、引擎说明、明确的验收标准以及没有未解决的设计问题。生成 READY / NEEDS WORK / BLOCKED 结论，并附具体差距。当用户说'这个故事准备好了吗'、'我可以开始这个故事吗'、'故事 X 准备好实施了吗'时使用。"
argument-hint："[story-file-path 或 'all' 或 'sprint']"
user-invocable：true
allowed-tools：Read, Glob, Grep, AskUserQuestion, Task
model：sonnet
---

# Story Readiness
# 故事就绪性

This skill validates that a story file contains everything a developer needs
to begin implementation — no mid-sprint design interruptions, no guessing,
no ambiguous acceptance criteria. Run it before assigning a story.
此技能验证故事文件包含开发者开始实施所需的一切 —— 没有冲刺中途的设计中断、没有猜测、没有模糊的验收标准。在分配故事前运行。

**This skill is read-only.** It never edits story files. It reports findings
and asks whether the user wants help filling gaps.
**此技能是只读的。** 它从不编辑故事文件。它报告发现并询问用户是否需要帮助填补差距。

**Output:** Verdict per story (READY / NEEDS WORK / BLOCKED) with a specific
gap list for each non-ready story.
**输出：** 每个故事的结论（READY / NEEDS WORK / BLOCKED），并为每个未就绪的故事附具体差距列表。

---

## Phase 0: Resolve Review Mode
## 阶段 0：解析评审模式

Resolve the review mode once at startup (store for all gate spawns this run):
在启动时解析一次评审模式（存储用于本次运行的所有关卡生成）：

1. If skill was called with `--review [full|lean|solo]` → use that value
1. 如果技能以 `--review [full|lean|solo]` 调用 → 使用该值
2. Else read `production/review-mode.txt` → use that value
2. 否则读取 `production/review-mode.txt` → 使用该值
3. Else → default to `lean`
3. 否则 → 默认为 `lean`

See `.claude/docs/director-gates.md` for the full check pattern and mode definitions.
有关完整检查模式和模式定义，请参阅 `.claude/docs/director-gates.md`。

---

## 1. Parse Arguments
## 1. 解析参数

**Scope:** `$ARGUMENTS[0]` (blank = ask user via AskUserQuestion)
**范围：** `$ARGUMENTS[0]`（空白 = 通过 AskUserQuestion 询问用户）

- **Specific path** (e.g., `/story-readiness production/epics/combat/story-001-basic-attack.md`):
  validate that single story file.
- **特定路径**（例如，`/story-readiness production/epics/combat/story-001-basic-attack.md`）：
  验证该单个故事文件。
- **`sprint`**: read the current sprint plan from `production/sprints/` (most
  recent file), extract every story path it references, validate each one.
- **`sprint`**：从 `production/sprints/` 读取当前冲刺计划（最新文件），提取其引用的每个故事路径，验证每一个。
- **`all`**: glob `production/epics/**/*.md`, exclude `EPIC.md` index files,
  validate every story file found.
- **`all`**：通配 `production/epics/**/*.md`，排除 `EPIC.md` 索引文件，验证找到的每个故事文件。
- **No argument**: ask the user which scope to validate.
- **无参数**：询问用户要验证哪个范围。

If no argument is given, use `AskUserQuestion`:
如果没有给出参数，使用 `AskUserQuestion`：
- "What would you like to validate?"
  - Options: "A specific story file", "All stories in the current sprint",
    "All stories in production/epics/", "Stories for a specific epic"
- "您想验证什么？"
  - 选项："一个特定故事文件"、"当前冲刺中的所有故事"、
    "production/epics/ 中的所有故事"、"特定 Epic 的故事"

Report the scope before proceeding: "Validating [N] story files."
在继续之前报告范围："正在验证 [N] 个故事文件。"

---

## 2. Load Supporting Context
## 2. 加载支持上下文

Before checking any stories, load reference documents once (not per-story):
在检查任何故事之前，加载参考文档一次（不是每个故事）：

- `design/gdd/systems-index.md` — to know which systems have approved GDDs
- `design/gdd/systems-index.md` —— 了解哪些系统有已批准的 GDD
- `docs/architecture/control-manifest.md` — to know which manifest rules exist
  (if the file does not exist, note it as missing once; do not re-flag per story)
  Also extract the `Manifest Version:` date from the header block if the file exists.
- `docs/architecture/control-manifest.md` —— 了解存在哪些清单规则
  （如果文件不存在，注明一次；不要对每个故事重复标记）
  如果文件存在，还要从标题块中提取 `Manifest Version:` 日期。
- `docs/architecture/tr-registry.yaml` — index all entries by `id`. Used to
  validate TR-IDs in stories. If the file does not exist, note it once; TR-ID
  checks will auto-pass for all stories (registry predates stories, so missing
  registry means stories are from before TR tracking was introduced).
- `docs/architecture/tr-registry.yaml` —— 按 `id` 索引所有条目。用于验证
  故事中的 TR-ID。如果文件不存在，注明一次；TR-ID 检查将对所有故事自动通过
  （注册表早于故事存在，因此缺失的注册表意味着故事来自 TR 跟踪引入之前）。
- All ADR status fields — for each unique ADR referenced across the stories being
  checked, read the ADR file and note its `Status:` field. Cache these so you
  don't re-read the same ADR for every story.
- 所有 ADR 状态字段 —— 对于被检查故事中引用的每个唯一 ADR，
  读取 ADR 文件并记录其 `Status:` 字段。缓存这些以便不为每个故事重新读取同一个 ADR。
- The current sprint file (if scope is `sprint`) — to identify Must Have /
  Should Have priority for escalation decisions
- 当前冲刺文件（如果范围是 `sprint`）—— 用于识别 Must Have /
  Should Have 优先级以进行升级决策

---

## 3. Story Readiness Checklist
## 3. 故事就绪性检查清单

For each story file, evaluate every item below. A story is READY only if all
items pass or are explicitly marked N/A with a stated reason.
对于每个故事文件，评估以下每一项。只有当所有项通过或明确标记为 N/A 并附理由时，故事才 READY。

### Design Completeness
### 设计完整性

- [ ] **GDD requirement referenced**: The story includes a `design/gdd/` path
  and quotes or links a specific requirement, acceptance criterion, or rule from
  that GDD — not just the GDD filename. A link to the document without tracing
  to a specific requirement does not pass.
- [ ] **引用了 GDD 要求**：故事包含 `design/gdd/` 路径
  并引用或链接了该 GDD 中的特定要求、验收标准或规则 ——
  不仅仅是 GDD 文件名。仅链接到文档而未追踪到特定要求的不通过。
- [ ] **Requirement is self-contained**: The acceptance criteria in the story
  are understandable without opening the GDD. A developer should not need to
  read a separate document to understand what DONE means.
- [ ] **要求自包含**：故事中的验收标准无需打开 GDD 即可理解。
  开发者不应需要阅读单独的文档来理解 DONE 的含义。
- [ ] **Acceptance criteria are testable**: Each criterion is a specific,
  observable condition — not "implement X" or "the system works correctly".
  Bad example: "Implement the jump mechanic." Good example: "Jump reaches
  max height of 5 units within 0.3 seconds when jump is held."
- [ ] **验收标准可测试**：每个标准是一个特定的、
  可观察的条件 —— 而不是"实现 X"或"系统正常工作"。
  反例："实现跳跃机制。"正例："按住跳跃时在 0.3 秒内达到 5 单位的最大高度。"
- [ ] **No acceptance criteria require judgment calls** *(auto-pass for `Type: Visual/Feel`)*: Criteria like
  "feels responsive" or "looks good" are not testable without a defined
  benchmark. For Logic, Integration, UI, and Config/Data stories, these must be
  replaced with specific observable conditions. For Visual/Feel stories, subjective
  criteria are expected and this check auto-passes — instead verify that each
  subjective criterion has a paired playtest protocol or evidence requirement
  (e.g., "evidence doc required at `production/qa/evidence/[slug]-evidence.md`").
  PASS if the acceptance criterion ends with or is accompanied by an explicit reference to a file path such as `production/qa/evidence/[slug]-evidence.md`. NEEDS WORK if the criterion is purely subjective with no evidence file path specified.
- [ ] **没有需要主观判断的验收标准** *（对 `Type: Visual/Feel` 自动通过）*：诸如
  "感觉响应灵敏"或"看起来不错"的标准在没有定义
  基准的情况下不可测试。对于 Logic、Integration、UI 和 Config/Data 故事，这些必须
  替换为特定的可观察条件。对于 Visual/Feel 故事，主观
  标准是预期的，此检查自动通过 —— 而是验证每个
  主观标准都有配对的游戏测试协议或证据要求
  （例如，"需要证据文档，位于 `production/qa/evidence/[slug]-evidence.md`"）。
  如果验收标准以文件路径（如 `production/qa/evidence/[slug]-evidence.md`）的显式引用结尾或伴随，则通过。如果标准纯粹主观且未指定证据文件路径，则 NEEDS WORK。

### Architecture Completeness
### 架构完整性

- [ ] **ADR referenced or N/A stated**: The story references at least one ADR,
  OR explicitly states "No ADR applies" with a brief reason.
  A story with no ADR reference and no explicit N/A note fails this check.
- [ ] **引用了 ADR 或声明了 N/A**：故事引用至少一个 ADR，
  或明确声明"不适用 ADR"并附简短理由。
  没有 ADR 引用且没有明确 N/A 注释的故事未通过此检查。
- [ ] **ADR is Accepted (not Proposed)**: For each referenced ADR, check its
  `Status:` field using the cached ADR statuses loaded in Section 2.
  - If `Status: Accepted` → pass.
  - If `Status: Proposed` → **BLOCKED**: the ADR may change before it is accepted,
    and the story's implementation guidance could be wrong.
    Fix: `BLOCKED: ADR-NNNN is Proposed — wait for acceptance before implementing.`
  - If the ADR file does not exist → **BLOCKED**: referenced ADR is missing.
  - Auto-pass if story has an explicit "No ADR applies" N/A note.
- [ ] **ADR 已接受（非 Proposed）**：对于每个引用的 ADR，使用第 2 节加载的缓存 ADR 状态检查其
  `Status:` 字段。
  - 如果 `Status: Accepted` → 通过。
  - 如果 `Status: Proposed` → **BLOCKED**：ADR 在接受前可能更改，
    故事的实施指导可能错误。
    修复：`BLOCKED: ADR-NNNN is Proposed — 等待接受后再实施。`
  - 如果 ADR 文件不存在 → **BLOCKED**：引用的 ADR 缺失。
  - 如果故事有明确的"不适用 ADR" N/A 注释则自动通过。
- [ ] **TR-ID is valid and active**: If the story contains a `TR-[system]-NNN`
  reference, look it up in the TR registry loaded in Section 2.
  - If the ID exists and `status: active` → pass.
  - If the ID exists and `status: deprecated` or `status: superseded-by: ...` →
    NEEDS WORK: the requirement was removed or replaced.
    Fix: update the story to reference the current requirement ID or remove if no longer applicable.
  - If the ID does not exist in the registry → NEEDS WORK: ID was not registered
    (story may predate registry, or registry needs an `/architecture-review` run).
  - Auto-pass if the story has no TR-ID reference OR if the registry does not exist.
- [ ] **TR-ID 有效且活跃**：如果故事包含 `TR-[system]-NNN`
  引用，在第 2 节加载的 TR 注册表中查找它。
  - 如果 ID 存在且 `status: active` → 通过。
  - 如果 ID 存在且 `status: deprecated` 或 `status: superseded-by: ...` →
    NEEDS WORK：要求已被移除或替换。
    修复：更新故事以引用当前要求 ID，或如果不再适用则移除。
  - 如果 ID 在注册表中不存在 → NEEDS WORK：ID 未注册
    （故事可能早于注册表，或注册表需要运行 `/architecture-review`）。
  - 如果故事没有 TR-ID 引用或注册表不存在则自动通过。
- [ ] **Manifest version is current**: If the story has a `Manifest Version:` date
  in its header AND `docs/architecture/control-manifest.md` exists:
  - If story version matches current manifest `Manifest Version:` → pass.
  - If story version is older than current manifest → NEEDS WORK: new rules may
    apply. Fix: review changed manifest rules, update story if any forbidden/required
    entries changed, then update the story's `Manifest Version:` to current.
  - Auto-pass if either the story has no `Manifest Version:` field OR the manifest
    does not exist.
- [ ] **清单版本是当前的**：如果故事标题中有 `Manifest Version:` 日期
  且 `docs/architecture/control-manifest.md` 存在：
  - 如果故事版本与当前清单 `Manifest Version:` 匹配 → 通过。
  - 如果故事版本早于当前清单 → NEEDS WORK：可能
    适用新规则。修复：审查更改的清单规则，如果任何禁止/必填
    条目已更改则更新故事，然后将故事的 `Manifest Version:` 更新为当前。
  - 如果故事没有 `Manifest Version:` 字段或清单不存在则自动通过。
- [ ] **Engine notes present**: For any post-cutoff engine API this story
  is likely to touch, implementation notes or a verification requirement are
  included. If the story clearly does not touch engine APIs (e.g., it is a
  pure data/config change), "N/A — no engine API involved" is acceptable.
- [ ] **存在引擎说明**：对于此故事
  可能触及的任何截止日期后的引擎 API，应包含实施说明或验证要求。
  如果故事明显不触及引擎 API（例如，它是
  纯数据/配置变更），"N/A — 不涉及引擎 API"是可接受的。
- [ ] **Control manifest rules noted**: Relevant layer rules from the control
  manifest are referenced, OR "N/A — manifest not yet created" is stated.
  This item auto-passes if `docs/architecture/control-manifest.md` does not
  exist yet (do not penalize stories written before the manifest was created).
- [ ] **注明了控制清单规则**：引用了控制
  清单中相关的层规则，或声明"N/A — 清单尚未创建"。
  如果 `docs/architecture/control-manifest.md` 尚不存在则此项自动通过（不要惩罚在清单创建前编写的故事）。

### Scope Clarity
### 范围清晰性

- [ ] **Estimate present**: The story includes a size estimate (hours,
  points, or a t-shirt size). A story with no estimate cannot be planned.
- [ ] **存在估算**：故事包含大小估算（小时、
  点数或 t 恤尺码）。没有估算的故事无法计划。
- [ ] **In-scope / Out-of-scope boundary stated**: The story states what
  it does NOT include, either in an explicit Out of Scope section or in
  language that makes the boundary unambiguous. Without this, scope creep
  during implementation is likely.
- [ ] **声明了范围内/范围外边界**：故事说明
  它不包括什么，要么在明确的范围外章节中，要么在
  使边界明确的语言中。没有这个，实施期间
  范围蔓延很可能会发生。
- [ ] **Story dependencies listed**: If this story depends on other stories
  being DONE first, those story IDs are listed. If there are no dependencies,
  "None" is explicitly stated (not just omitted).
- [ ] **列出故事依赖**：如果此故事依赖于其他故事
  先 DONE，则列出这些故事 ID。如果没有依赖，
  则明确声明"None"（而不仅仅是省略）。

### Open Questions
### 未决问题

- [ ] **No unresolved design questions**: The story does not contain text
  flagged as "UNRESOLVED", "TBD", "TODO", "?", or equivalent markers in
  any acceptance criterion, implementation note, or rule statement.
- [ ] **没有未解决的设计问题**：故事不包含在任何验收标准、实施说明或规则声明中标记为
  "UNRESOLVED"、"TBD"、"TODO"、"?"或等效标记的文本。
- [ ] **Dependency stories are not in DRAFT**: For each story listed as a
  dependency, check if the file exists and does not have a DRAFT status. A
  story that depends on a DRAFT or missing story is BLOCKED, not just
  NEEDS WORK.
- [ ] **依赖故事不是 DRAFT 状态**：对于列为
  依赖的每个故事，检查文件是否存在且没有 DRAFT 状态。一个
  依赖于 DRAFT 或缺失故事的故事是 BLOCKED，而不仅仅是
  NEEDS WORK。

### Asset References Check
### 资源引用检查

- [ ] **Referenced assets exist**: Scan the story text for asset path patterns
  (paths containing `assets/`, or file extensions `.png`, `.jpg`, `.svg`,
  `.wav`, `.ogg`, `.mp3`, `.glb`, `.gltf`, `.tres`, `.tscn`, `.res`).
  - For each asset path found: use Glob to check whether the file exists.
  - If any referenced asset does not exist: **NEEDS WORK** — note the missing
    path(s). (The story references assets that have not been created yet.
    Either remove the reference, create a placeholder, or mark it as an
    explicit dependency on an asset creation story.)
  - If all referenced assets exist: note "Referenced assets verified:
    [count] found."
  - If no asset paths are referenced in the story: note "No asset references
    found in story — skipping asset check." This item auto-passes.
  - This is an existence-only check. Do not validate file format or content.
- [ ] **引用的资源存在**：扫描故事文本以查找资源路径模式
  （包含 `assets/` 的路径，或文件扩展名 `.png`、`.jpg`、`.svg`、
  `.wav`、`.ogg`、`.mp3`、`.glb`、`.gltf`、`.tres`、`.tscn`、`.res`）。
  - 对于找到的每个资源路径：使用 Glob 检查文件是否存在。
  - 如果任何引用的资源不存在：**NEEDS WORK** —— 注明缺失的
    路径。（故事引用了尚未创建的资源。
    要么移除引用，创建占位符，要么将其标记为对资源创建故事的
    显式依赖。）
  - 如果所有引用的资源都存在：注明"已验证引用的资源：
    找到 [count] 个。"
  - 如果故事中没有引用资源路径：注明"故事中未找到资源
    引用 —— 跳过资源检查。"此项自动通过。
  - 这是仅存在性检查。不要验证文件格式或内容。

### Definition of Done
### 完成定义

- [ ] **Minimum testable acceptance criteria by story type**:
  - Logic / Integration stories: at least 3
  - Visual/Feel and UI stories: at least 2
  - Config/Data stories: at least 1
  Apply the threshold matching the story's `Type:` field. If the story has fewer than the minimum, mark as NEEDS WORK.
- [ ] **按故事类型的最小可测试验收标准**：
  - Logic / Integration 故事：至少 3 个
  - Visual/Feel 和 UI 故事：至少 2 个
  - Config/Data 故事：至少 1 个
  应用与故事的 `Type:` 字段匹配的阈值。如果故事少于最小值，则标记为 NEEDS WORK。
- [ ] **Performance budget noted if applicable**: If this story touches any
  part of the gameplay loop, rendering, or physics, a performance budget or
  a "no performance impact expected — [reason]" note is present.
- [ ] **适用时注明性能预算**：如果此故事触及
  游戏循环、渲染或物理的任何部分，则存在性能预算或
  "预期无性能影响 —— [理由]"注释。
- [ ] **Story Type declared**: The story includes a `Type:` field in its header
  identifying the test category (Logic / Integration / Visual/Feel / UI / Config/Data).
  Without this, test evidence requirements cannot be enforced at story close.
  Fix: Add `Type: [Logic|Integration|Visual/Feel|UI|Config/Data]` to the story header.
- [ ] **声明了故事类型**：故事标题中包含 `Type:` 字段，
  标识测试类别（Logic / Integration / Visual/Feel / UI / Config/Data）。
  没有这个，在故事关闭时无法强制执行测试证据要求。
  修复：将 `Type: [Logic|Integration|Visual/Feel|UI|Config/Data]` 添加到故事标题。
- [ ] **Test evidence requirement is clear**: If the Story Type is set, the story
  includes a `## Test Evidence` section stating where evidence will be stored
  (test file path for Logic/Integration, or evidence doc path for Visual/Feel/UI).
  Fix: Add `## Test Evidence` with the expected evidence location for the story's type.
- [ ] **测试证据要求明确**：如果设置了故事类型，故事
  包含 `## Test Evidence` 章节，说明证据将存储在
  哪里（Logic/Integration 的测试文件路径，或 Visual/Feel/UI 的证据文档路径）。
  修复：为故事类型添加带有预期证据位置的 `## Test Evidence`。

---

## 4. Verdict Assignment
## 4. 结论分配

Assign one of three verdicts per story:
为每个故事分配三种结论之一：

**READY** — All checklist items pass or have explicit N/A justifications.
The story can be assigned immediately.
**READY（就绪）** —— 所有检查清单项通过或有明确的 N/A 理由。
故事可以立即分配。

**NEEDS WORK** — One or more checklist items fail, but all dependency stories
exist and are not DRAFT. The story can be fixed before assignment.
**NEEDS WORK（需要工作）** —— 一个或多个检查清单项失败，但所有依赖故事
存在且不是 DRAFT。故事可以在分配前修复。

**BLOCKED** — One or more dependency stories are missing or in DRAFT state,
OR a critical design question (flagged UNRESOLVED in a criterion or rule) has
no owner. The story cannot be assigned until the blocker is resolved. Note:
a story that is BLOCKED may also have NEEDS WORK items — list both.
**BLOCKED（阻塞）** —— 一个或多个依赖故事缺失或处于 DRAFT 状态，
或关键设计问题（在标准或规则中标记为 UNRESOLVED）没有
所有者。故事在阻塞解决前无法分配。注意：
BLOCKED 的故事也可能有 NEEDS WORK 项 —— 两者都列出。

---

## 5. Output Format
## 5. 输出格式

### Single story output
### 单故事输出

```
## Story Readiness: [story title]
File: [path]
Verdict: [READY / NEEDS WORK / BLOCKED]

### Passing Checks (N/[total])
[list passing items briefly]

### Gaps
- [Checklist item]: [exact description of what is missing or wrong]
  Fix: [specific text needed to resolve this gap]

### Blockers (if BLOCKED)
- [What is blocking]: [story ID or design question that must resolve first]
```

### Multiple story aggregate output
### 多故事汇总输出

```
## Story Readiness Summary — [scope] — [date]

Ready:      [N] stories
Needs Work: [N] stories
Blocked:    [N] stories

### Ready Stories
- [story title] ([path])

### Needs Work
- [story title]: [primary gap — one line]
- [story title]: [primary gap — one line]

### Blocked Stories
- [story title]: Blocked by [story ID / design question]

---
[Full detail for each non-ready story follows, using the single-story format]
```

### Sprint escalation
### 冲刺升级

If the scope is `sprint` and any Must Have stories are NEEDS WORK or BLOCKED,
add a prominent warning at the top of the output:
如果范围是 `sprint` 且任何 Must Have 故事是 NEEDS WORK 或 BLOCKED，
在输出顶部添加显眼警告：

```
WARNING: [N] Must Have stories are not implementation-ready.
[List them with their primary gap or blocker.]
Resolve these before the sprint begins or replan with `/sprint-plan update`.
```

---

## 6. Collaborative Protocol
## 6. 协作协议

This skill is read-only. It never proposes edits or asks to write files.
此技能是只读的。它从不提议编辑或要求写入文件。

After reporting findings, offer:
报告发现后，提供：

"Would you like help filling in the gaps for any of these stories? I can
draft the missing sections for your approval."
"您是否需要帮助填补任何这些故事的差距？我可以
起草缺失的章节供您批准。"

If the user says yes for a specific story, draft only the missing sections
in conversation. Do not use Write or Edit tools — the user (or
`/create-stories`) handles writing.
如果用户对特定故事说是，仅在对话中起草缺失的章节。
不要使用 Write 或 Edit 工具 —— 用户（或
`/create-stories`）处理写入。

**Redirect rules:**
- If a story file does not exist at all: "This story file is missing entirely.
  Run `/create-epics [layer]` then `/create-stories [epic-slug]` to generate stories from the GDD and ADR."
- If a story has no GDD reference and the work appears small: "This story has
  no GDD reference. If the change is small (under ~4 hours), run
  `/quick-design [description]` to create a Quick Design Spec, then reference
  that spec in the story."
- If a story's scope has grown beyond its original sizing:
**重定向规则：**
- 如果故事文件根本不存在："此故事文件完全缺失。
  运行 `/create-epics [layer]` 然后 `/create-stories [epic-slug]` 从 GDD 和 ADR 生成故事。"
- 如果故事没有 GDD 引用且工作看起来很小："此故事没有
  GDD 引用。如果变更很小（约 4 小时以内），运行
  `/quick-design [description]` 创建快速设计规格，然后在故事中引用
  该规格。"
- 如果故事的范围已超出其原始估算：
  "This story appears to have expanded in scope. Consider splitting it or escalating to the producer
  before implementation begins."
  "此故事似乎在范围上有所扩展。考虑拆分它或在实施开始前升级给制作人。"

---

## 7. Next-Story Handoff
## 7. 下一个故事交接

After completing a single-story readiness check (not `all` or `sprint` scope):
完成单故事就绪性检查后（非 `all` 或 `sprint` 范围）：

1. Read the current sprint file from `production/sprints/` (most recent).
1. 从 `production/sprints/` 读取当前冲刺文件（最新）。
2. Find stories that are:
2. 查找以下故事：
   - Status: READY or NOT STARTED
   - 状态：READY 或 NOT STARTED
   - Not the story just checked
   - 不是刚检查的故事
   - Not blocked by incomplete dependencies
   - 未被不完整依赖阻塞
   - In the Must Have or Should Have tier
   - 在 Must Have 或 Should Have 层级

If any are found, surface up to 3:
如果找到任何，显示最多 3 个：

```
### Other Ready Stories in This Sprint

1. [Story name] — [1-line description] — Est: [X hrs]
2. [Story name] — [1-line description] — Est: [X hrs]

Run `/story-readiness [path]` to validate before starting.
```

If no sprint file exists or no other ready stories are found, skip this section silently.
如果没有冲刺文件或未找到其他就绪故事，则静默跳过此章节。

---

## Phase 8: Director Gate — Story Readiness Review
## 阶段 8：主管关卡 —— 故事就绪性评审

Apply the review mode resolved in Phase 0 before spawning QL-STORY-READY:
在生成 QL-STORY-READY 之前，应用阶段 0 中解析的评审模式：

- `solo` → skip. Note: "QL-STORY-READY skipped — Solo mode." Proceed to close.
- `solo` → 跳过。注明："QL-STORY-READY 已跳过 —— Solo 模式。"继续关闭。
- `lean` → skip. Note: "QL-STORY-READY skipped — Lean mode." Proceed to close.
- `lean` → 跳过。注明："QL-STORY-READY 已跳过 —— Lean 模式。"继续关闭。
- `full` → spawn as normal.
- `full` → 正常生成。

Spawn `qa-lead` via Task using gate **QL-STORY-READY** (`.claude/docs/director-gates.md`).
使用关卡 **QL-STORY-READY**（`.claude/docs/director-gates.md`）通过 Task 生成 `qa-lead`。

Pass the following context:
传递以下上下文：
- Story title
- 故事标题
- Acceptance criteria list (all items from the story's acceptance criteria section)
- 验收标准列表（来自故事验收标准章节的所有项）
- Dependency status (all dependencies listed and their current state: exist / DRAFT / missing)
- 依赖状态（列出的所有依赖及其当前状态：存在 / DRAFT / 缺失）
- Overall verdict (READY / NEEDS WORK / BLOCKED) from Phase 4
- 来自阶段 4 的总体结论（READY / NEEDS WORK / BLOCKED）

Handle the verdict per standard rules in `director-gates.md`:
按 `director-gates.md` 中的标准规则处理结论：
- **ADEQUATE** → story is cleared. Proceed to close.
- **ADEQUATE（充分）** → 故事已清除。继续关闭。
- **GAPS [list]** → surface the specific gaps to the user via `AskUserQuestion`:
  options: `Update story with suggested gaps` / `Accept and proceed anyway` / `Discuss further`.
- **GAPS [list]（差距 [列表]）** → 通过 `AskUserQuestion` 向用户显示具体差距：
  选项：`用建议的差距更新故事` / `接受并继续` / `进一步讨论`。
- **INADEQUATE** → surface the specific gaps; ask user whether to update the story or proceed anyway.
- **INADEQUATE（不充分）** → 显示具体差距；询问用户是更新故事还是继续。

---

## Recommended Next Steps
## 推荐的下一步

- Run `/dev-story [story-path]` to begin implementation once the story is READY
- 一旦故事 READY，运行 `/dev-story [story-path]` 开始实施
- Run `/story-readiness sprint` to check all stories in the current sprint at once
- 运行 `/story-readiness sprint` 一次检查当前冲刺中的所有故事
- Run `/create-stories [epic-slug]` if a story file is missing entirely
- 如果故事文件完全缺失，运行 `/create-stories [epic-slug]`
