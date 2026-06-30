---
name: help
description: "Analyzes what is done and the users query and offers advice on what to do next. Use if user says what should I do next or what do I do now or I'm stuck or I don't know what to do"
argument-hint: "[optional: what you just finished, e.g. 'finished design-review' or 'stuck on ADRs']"
user-invocable: true
allowed-tools: Read, Glob, Grep
context: |
  !echo "=== Live Project State ===" && echo "Stage: $(cat production/stage.txt 2>/dev/null | tr -d '[:space:]' || echo 'not set')" && echo "Latest sprint: $(ls -t production/sprints/*.md 2>/dev/null | head -1 || echo 'none')" && echo "Session state: $(head -5 production/session-state/active.md 2>/dev/null || echo 'none')"
model: haiku
---
---
name: help
description: "分析已完成的内容和用户查询，并就下一步该做什么提供建议。当用户说 what should I do next 或 what do I do now 或 I'm stuck 或 I don't know what to do 时使用"
argument-hint: "[optional: what you just finished, e.g. 'finished design-review' or 'stuck on ADRs']"
user-invocable: true
allowed-tools: Read, Glob, Grep
context: |
  !echo "=== Live Project State ===" && echo "Stage: $(cat production/stage.txt 2>/dev/null | tr -d '[:space:]' || echo 'not set')" && echo "Latest sprint: $(ls -t production/sprints/*.md 2>/dev/null | head -1 || echo 'none')" && echo "Session state: $(head -5 production/session-state/active.md 2>/dev/null || echo 'none')"
model: haiku
---

# Studio Help — What Do I Do Next?
# 工作室帮助 — 我下一步该做什么？

This skill is read-only — it reports findings but writes no files.
此技能是只读的 — 它报告发现但不写入任何文件。

This skill figures out exactly where you are in the game development pipeline and
tells you what comes next. It is **lightweight** — not a full audit. For a full
gap analysis, use `/project-stage-detect`.
此技能准确判断您在游戏开发管线中的位置，并
告诉您下一步该做什么。它是**轻量级的** — 不是完整审计。要进行完整的
缺口分析，使用 `/project-stage-detect`。

---

## Step 1: Read the Catalog
## 步骤 1：读取目录

Read `.claude/docs/workflow-catalog.yaml`. This is the authoritative list of all
phases, their steps (in order), whether each step is required or optional, and
the artifact globs that indicate completion.
读取 `.claude/docs/workflow-catalog.yaml`。这是所有
阶段及其步骤（按顺序）、每个步骤是必需还是可选、以及
指示完成情况的工件 glob 的权威列表。

---

## Step 1b: Find Skills Not in the Catalog
## 步骤 1b：查找不在目录中的技能

After reading the catalog, Glob `.claude/skills/*/SKILL.md` to get the full list
of installed skills. For each file, extract the `name:` field from its frontmatter.
读取目录后，Glob `.claude/skills/*/SKILL.md` 获取已安装技能的完整
列表。对于每个文件，从其 frontmatter 提取 `name:` 字段。

Compare against the `command:` values in the catalog. Any skill whose name does
not appear as a catalog command is an **uncataloged skill** — still usable but not
part of the phase-gated workflow.
与目录中的 `command:` 值进行比较。任何名称未作为目录命令出现的技能都是
**未编目技能** — 仍可使用但不属于
阶段门控工作流。

Collect these for the output in Step 7 — show them as a footer block:
为步骤 7 的输出收集这些内容 — 将它们显示为页脚块：

```
### Also installed (not in workflow)
- `/skill-name` — [description from SKILL.md frontmatter]
- `/skill-name` — [description]
```
```
### Also installed (not in workflow)
- `/skill-name` — [description from SKILL.md frontmatter]
- `/skill-name` — [description]
```

Only show this block if at least one uncataloged skill exists. Limit to the 10
most relevant based on the user's current phase (QA skills in production, team
skills in production/polish, etc.).
仅在至少存在一个未编目技能时显示此块。根据用户当前阶段将其限制为最相关的 10 个（生产中的 QA 技能、生产/打磨中的团队
技能等）。

---

## Step 2: Determine Current Phase
## 步骤 2：确定当前阶段

Check in this order:
按此顺序检查：

1. **Read `production/stage.txt`** — if it exists and has content, this is the
   authoritative phase name. Map it to a catalog phase key:
   - "Concept" → `concept`
   - "Systems Design" → `systems-design`
   - "Technical Setup" → `technical-setup`
   - "Pre-Production" → `pre-production`
   - "Production" → `production`
   - "Polish" → `polish`
   - "Release" → `release`
1. **读取 `production/stage.txt`** — 如果它存在且有内容，这是
   权威阶段名。将其映射到目录阶段键：
   - "Concept" → `concept`
   - "Systems Design" → `systems-design`
   - "Technical Setup" → `technical-setup`
   - "Pre-Production" → `pre-production`
   - "Production" → `production`
   - "Polish" → `polish`
   - "Release" → `release`

2. **If stage.txt is missing**, infer phase from artifacts (most-advanced match wins):
   - `src/` has 10+ source files → `production`
   - `production/stories/*.md` exists → `pre-production`
   - `docs/architecture/adr-*.md` exists → `technical-setup`
   - `design/gdd/systems-index.md` exists → `systems-design`
   - `design/gdd/game-concept.md` exists → `concept`
   - Nothing → `concept` (fresh project)
2. **如果 stage.txt 缺失**，从工件推断阶段（最先进的匹配优先）：
   - `src/` 有 10+ 源文件 → `production`
   - `production/stories/*.md` 存在 → `pre-production`
   - `docs/architecture/adr-*.md` 存在 → `technical-setup`
   - `design/gdd/systems-index.md` 存在 → `systems-design`
   - `design/gdd/game-concept.md` 存在 → `concept`
   - 无 → `concept`（新项目）

---

## Step 3: Read Session Context
## 步骤 3：读取会话上下文

Read `production/session-state/active.md` if it exists. Extract:
- What was most recently worked on
- Any in-progress tasks or open questions
- Current epic/feature/task from STATUS block (if present)
如果存在，读取 `production/session-state/active.md`。提取：
- 最近在处理什么
- 任何进行中的任务或待决问题
- STATUS 块中的当前史诗/功能/任务（如果存在）

This tells you what the user just finished or is stuck on — use it to personalize
the output.
这告诉您用户刚刚完成什么或卡在哪里 — 用它来个性化
输出。

---

## Step 4: Check Step Completion for the Current Phase
## 步骤 4：检查当前阶段的步骤完成情况

For each step in the current phase (from the catalog):
对于当前阶段中的每个步骤（来自目录）：

### Artifact-based checks
### 基于工件的检查

If the step has `artifact.glob`:
- Use Glob to check if files matching the pattern exist
- If `min_count` is specified, verify at least that many files match
- If `artifact.pattern` is specified, use Grep to verify the pattern exists in the matched file
- **Complete** = artifact condition is met
- **Incomplete** = artifact is missing or pattern not found
如果步骤有 `artifact.glob`：
- 使用 Glob 检查是否存在匹配该模式的文件
- 如果指定了 `min_count`，验证至少有那么多文件匹配
- 如果指定了 `artifact.pattern`，使用 Grep 验证匹配文件中是否存在该模式
- **完成** = 工件条件满足
- **未完成** = 工件缺失或未找到模式

If the step has `artifact.note` (no glob):
- Mark as **MANUAL** — cannot auto-detect, will ask user
如果步骤有 `artifact.note`（无 glob）：
- 标记为 **MANUAL** — 无法自动检测，将询问用户

If the step has no `artifact` field:
- Mark as **UNKNOWN** — completion not trackable (e.g. repeatable implementation work)
如果步骤没有 `artifact` 字段：
- 标记为 **UNKNOWN** — 完成情况无法跟踪（例如可重复的实现工作）

### Special case: production phase — read `sprint-status.yaml`
### 特殊情况：生产阶段 — 读取 `sprint-status.yaml`

When the current phase is `production`, check for `production/sprint-status.yaml`
before doing any glob-based story checks. If it exists, read it directly:
当当前阶段为 `production` 时，在进行任何基于 glob 的故事检查之前检查 `production/sprint-status.yaml`。如果存在，直接读取它：

- Stories with `status: in-progress` → surface as "currently active"
- Stories with `status: ready-for-dev` → surface as "next up"
- Stories with `status: done` → count as complete
- Stories with `status: blocked` → surface as blocker with the `blocker` field
- 带有 `status: in-progress` 的故事 → 显示为 "currently active"
- 带有 `status: ready-for-dev` 的故事 → 显示为 "next up"
- 带有 `status: done` 的故事 → 计为完成
- 带有 `status: blocked` 的故事 → 以 `blocker` 字段显示为阻塞

This gives precise per-story status without markdown scanning. Skip the glob
artifact check for the `implement` and `story-done` steps — the YAML is authoritative.
这无需 markdown 扫描即可提供精确的每个故事状态。跳过 `implement` 和 `story-done` 步骤的 glob
工件检查 — YAML 是权威的。

### Special case: `repeatable: true` (non-production)
### 特殊情况：`repeatable: true`（非生产）

For repeatable steps outside production (e.g. "System GDDs"), the artifact
check tells you whether *any* work has been done, not whether it's finished.
Label these differently — show what's been detected, then note it may be ongoing.
对于生产之外的重复步骤（例如 "System GDDs"），工件
检查告诉您是否已做过*任何*工作，而非是否完成。
以不同方式标记这些 — 显示已检测到的内容，然后注明它可能是进行中的。

---

## Step 5: Find Position and Identify Next Steps
## 步骤 5：查找位置并识别后续步骤

From the completion data, determine:
从完成数据中确定：

1. **Last confirmed complete step** — the furthest completed required step
2. **Current blocker** — the first incomplete *required* step (this is what the
   user must do next)
3. **Optional opportunities** — incomplete *optional* steps that can be done
   before or alongside the blocker
4. **Upcoming required steps** — required steps after the current blocker
   (show as "coming up" so user can plan ahead)
1. **最后确认完成的步骤** — 最远的已完成必需步骤
2. **当前阻塞项** — 第一个未完成的*必需*步骤（这是
   用户下一步必须做的）
3. **可选机会** — 可以在阻塞项之前或同时完成的未完成
   *可选*步骤
4. **即将到来的必需步骤** — 当前阻塞项之后的必需步骤
   （显示为 "coming up" 以便用户提前规划）

If the user provided an argument (e.g. "just finished design-review"), use that
to advance past the step they named even if the artifact check is ambiguous.
如果用户提供了参数（例如 "just finished design-review"），使用它
推进过他们命名的步骤，即使工件检查模棱两可。

---

## Step 6: Check for In-Progress Work
## 步骤 6：检查进行中的工作

If `active.md` shows an active task or epic:
- Surface it prominently at the top: "It looks like you were working on [X]"
- Suggest continuing it or confirm if it's done
如果 `active.md` 显示活动任务或史诗：
- 在顶部突出显示："看起来您正在处理 [X]"
- 建议继续它或确认是否完成

---

## Step 7: Present Output
## 步骤 7：呈现输出

Keep it **short and direct**. This is a quick orientation, not a report.
保持**简短直接**。这是一个快速导向，而非报告。

```
## Where You Are: [Phase Label]

**In progress:** [from active.md, if any]

### ✓ Done
- [completed step name]
- [completed step name]

### → Next up (REQUIRED)
**[Step name]** — [description]
Command: `[/command]`

### ~ Also available (OPTIONAL)
- **[Step name]** — [description] → `/command`
- **[Step name]** — [description] → `/command`

### Coming up after that
- [Next required step name] (`/command`)
- [Next required step name] (`/command`)

---
Approaching **[next phase]** gate → run `/gate-check` when ready.
```
```
## Where You Are: [Phase Label]

**In progress:** [from active.md, if any]

### ✓ Done
- [completed step name]
- [completed step name]

### → Next up (REQUIRED)
**[Step name]** — [description]
Command: `[/command]`

### ~ Also available (OPTIONAL)
- **[Step name]** — [description] → `/command`
- **[Step name]** — [description] → `/command`

### Coming up after that
- [Next required step name] (`/command`)
- [Next required step name] (`/command`)

---
Approaching **[next phase]** gate → run `/gate-check` when ready.
```

**Formatting rules:**
- `✓` for confirmed complete
- `→` for the current required next step (only one — the first blocker)
- `~` for optional steps available now
- Show commands inline as backtick code
- If a step has no command (e.g. "Implement Stories"), explain what to do instead of showing a slash command
- For MANUAL steps, ask the user: "I can't tell if [step] is done — has it been completed?"
**格式规则：**
- `✓` 表示已确认完成
- `→` 表示当前必需的下一步（仅一个 — 第一个阻塞项）
- `~` 表示现在可用的可选步骤
- 将命令以内联反引号代码形式显示
- 如果步骤没有命令（例如 "Implement Stories"），解释该做什么而不是显示斜杠命令
- 对于 MANUAL 步骤，询问用户："我无法判断 [step] 是否完成 — 它是否已完成？"

Verdict: **COMPLETE** — next steps identified.
结论：**已完成** — 后续步骤已识别。

---

## Step 8: Gate Warning (if close)
## 步骤 8：门禁警告（如果接近）

After the current phase's steps, check if the user is likely approaching a gate:
- If all required steps in the current phase are complete (or nearly complete),
  add: "You're close to the **[Current] → [Next]** gate. Run `/gate-check` when ready."
- If multiple required steps remain, skip the gate warning — it's not relevant yet.
在当前阶段的步骤之后，检查用户是否可能接近门禁：
- 如果当前阶段的所有必需步骤都完成（或接近完成），
  添加："You're close to the **[Current] → [Next]** gate. Run `/gate-check` when ready."
- 如果剩余多个必需步骤，跳过门禁警告 — 它还不相关。

---

## Step 9: Escalation Paths
## 步骤 9：升级路径

After the recommendations, if the user seems stuck or confused, add:
在建议之后，如果用户似乎卡住或困惑，添加：

```
---
Need more detail?
- `/project-stage-detect` — full gap analysis with all missing artifacts listed
- `/gate-check` — formal readiness check for your next phase
- `/start` — re-orient from scratch
```
```
---
Need more detail?
- `/project-stage-detect` — full gap analysis with all missing artifacts listed
- `/gate-check` — formal readiness check for your next phase
- `/start` — re-orient from scratch
```

Only show this if the user's input suggested confusion (e.g. "I don't know", "stuck",
"lost", "not sure"). Don't show it for simple "what's next?" queries.
仅在用户输入暗示困惑时显示（例如 "I don't know"、"stuck"、
"lost"、"not sure"）。对于简单的 "what's next?" 查询不显示。

---

## Collaborative Protocol
## 协作协议

- **Never auto-run the next skill.** Recommend it, let the user invoke it.
- **Ask about MANUAL steps** rather than assuming complete or incomplete.
- **Match the user's tone** — if they sound stressed ("I'm totally lost"), be
  reassuring and give one action, not a list of six.
- **One primary recommendation** — the user should leave knowing exactly one thing
  to do next. Optional steps and "coming up" are secondary context.
- **切勿自动运行下一个技能。** 推荐它，让用户调用它。
- **询问 MANUAL 步骤**，而非假设完成或未完成。
- **匹配用户的语气** — 如果他们听起来紧张（"I'm totally lost"），给予
  安慰并提供一个操作，而非六个的列表。
- **一个主要建议** — 用户离开时应该确切知道下一步该做的一件事。
  可选步骤和 "coming up" 是次要上下文。
