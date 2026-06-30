---
name: create-control-manifest
description: "After architecture is complete, produces a flat actionable rules sheet for programmers — what you must do, what you must never do, per system and per layer. Extracted from all Accepted ADRs, technical preferences, and engine reference docs. More immediately actionable than ADRs (which explain why)."
argument-hint: "[update — regenerate from current ADRs]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task
model: sonnet
agent: technical-director
---
---
名称：create-control-manifest
描述："在架构完成之后，为程序员产出一份扁平、可执行的规则表 —— 该做什么、绝不该做什么，按系统与按层划分。从全部 Accepted ADR、技术偏好与引擎参考文档中提取。比 ADR（解释为什么）更具即时可执行性。"
argument-hint："[update — regenerate from current ADRs]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Task
model：sonnet
agent：technical-director
---

# Create Control Manifest
# 创建控制清单

The Control Manifest is a flat, actionable rules sheet for programmers. It
answers "what do I do?" and "what must I never do?" — organized by architectural
layer, extracted from all Accepted ADRs, technical preferences, and engine
reference docs. Where ADRs explain *why*, the manifest tells you *what*.
控制清单是面向程序员的一份扁平、可执行的规则表。它回答"我该做什么？"和"我
绝不能做什么？" —— 按架构层组织，从全部 Accepted ADR、技术偏好与
引擎参考文档中提取。ADR 解释*为什么*，而清单告诉你*做什么*。

**Output:** `docs/architecture/control-manifest.md`
**输出：** `docs/architecture/control-manifest.md`

**When to run:** After `/architecture-review` passes and ADRs are in Accepted
status. Re-run whenever new ADRs are accepted or existing ADRs are revised.
**运行时机：** 在 `/architecture-review` 通过且 ADR 处于 Accepted
状态之后。每当新 ADR 被接受或既有 ADR 被修订时重新运行。

---

## 1. Load All Inputs
## 1. 加载全部输入

### ADRs
- Glob `docs/architecture/adr-*.md` and read every file
- Filter to only Accepted ADRs (Status: Accepted) — skip Proposed, Deprecated,
  Superseded
- Note the ADR number and title for every rule sourced
### ADR
- Glob `docs/architecture/adr-*.md` 并读取每个文件
- 仅筛选 Accepted ADR（Status: Accepted）—— 跳过 Proposed、Deprecated、
  Superseded
- 为每条来源规则记录 ADR 编号与标题

### Technical Preferences
- Read `.claude/docs/technical-preferences.md`
- Extract: naming conventions, performance budgets, approved libraries/addons,
  forbidden patterns
### 技术偏好
- 读取 `.claude/docs/technical-preferences.md`
- 提取：命名约定、性能预算、获批的库/插件、
  禁用模式

### Engine Reference
- Read `docs/engine-reference/[engine]/VERSION.md` for engine + version
- Read `docs/engine-reference/[engine]/deprecated-apis.md` — these become
  forbidden API entries
- Read `docs/engine-reference/[engine]/current-best-practices.md` if it exists
### 引擎参考
- 读取 `docs/engine-reference/[engine]/VERSION.md` 获取引擎与版本
- 读取 `docs/engine-reference/[engine]/deprecated-apis.md` —— 这些将转为
  禁用 API 条目
- 若存在则读取 `docs/engine-reference/[engine]/current-best-practices.md`

Report: "Loaded [N] Accepted ADRs, engine: [name + version]."
报告："已加载 [N] 个 Accepted ADR，引擎：[name + version]。"

---

## 2. Extract Rules from Each ADR
## 2. 从每个 ADR 提取规则

For each Accepted ADR, extract:
对每个 Accepted ADR，提取：

### Required Patterns (from "Implementation Guidelines" section)
- Every "must", "should", "required to", "always" statement
- Every specific pattern or approach mandated
### 必需模式（来自 "Implementation Guidelines" 节）
- 每条 "must"、"should"、"required to"、"always" 语句
- 每个被强制要求的特定模式或方法

### Forbidden Approaches (from "Alternatives Considered" sections)
- Every alternative that was explicitly rejected — *why* it was rejected becomes
  the rule ("never use X because Y")
- Any anti-patterns explicitly called out
### 禁用方法（来自 "Alternatives Considered" 各节）
- 每个被明确否决的备选方案 —— 其被否决的*原因*即成为
  规则（"绝不使用 X，因为 Y"）
- 任何被明确指出的反模式

### Performance Guardrails (from "Performance Implications" section)
- Budget constraints: "max N ms per frame for this system"
- Memory limits: "this system must not exceed N MB"
### 性能护栏（来自 "Performance Implications" 节）
- 预算约束："该系统每帧最多 N ms"
- 内存上限："该系统不得超过 N MB"

### Engine API Constraints (from "Engine Compatibility" section)
- Post-cutoff APIs that require verification
- Verified behaviours that differ from default LLM assumptions
- API fields or methods that behave differently in the pinned engine version
### 引擎 API 约束（来自 "Engine Compatibility" 节）
- 截止日期之后需要核验的 API
- 与默认 LLM 假设不同的已核验行为
- 在所钉定的引擎版本中行为不同的 API 字段或方法

### Layer Classification
Classify each rule by the architectural layer of the system it governs:
- **Foundation**: Scene management, event architecture, save/load, engine init
- **Core**: Core gameplay loops, main player systems, physics/collision
- **Feature**: Secondary systems, secondary mechanics, AI
- **Presentation**: Rendering, audio, UI, VFX, shaders
### 层分类
按规则所治理系统的架构层对每条规则分类：
- **Foundation**：场景管理、事件架构、存档/读档、引擎初始化
- **Core**：核心玩法循环、主要玩家系统、物理/碰撞
- **Feature**：次要系统、次要机制、AI
- **Presentation**：渲染、音频、UI、VFX、着色器

If an ADR spans multiple layers, duplicate the rule into each relevant layer.
若一个 ADR 跨越多层，则将规则复制到每个相关层。

---

## 3. Add Global Rules
## 3. 添加全局规则

Combine rules that apply to all layers:
合并适用于所有层的规则：

### From technical-preferences.md:
- Naming conventions (classes, variables, signals/events, files, constants)
- Performance budgets (target framerate, frame budget, draw call limits, memory ceiling)
### 来自 technical-preferences.md：
- 命名约定（类、变量、信号/事件、文件、常量）
- 性能预算（目标帧率、帧预算、绘制调用上限、内存上限）

### From deprecated-apis.md:
- All deprecated APIs → Forbidden API entries
### 来自 deprecated-apis.md：
- 所有弃用 API → 禁用 API 条目

### From current-best-practices.md (if available):
- Engine-recommended patterns → Required entries
### 来自 current-best-practices.md（若可用）：
- 引擎推荐模式 → 必需条目

### From technical-preferences.md forbidden patterns:
- Copy any "Forbidden Patterns" entries directly
### 来自 technical-preferences.md 的禁用模式：
- 直接复制任何 "Forbidden Patterns" 条目

---

## 4. Present Rules Summary Before Writing
## 4. 写入之前呈现规则摘要

Before writing the manifest, present a summary to the user:
在写入清单之前，向用户呈现摘要：

```
## Control Manifest Preview
Engine: [name + version]
ADRs covered: [list ADR numbers]
Total rules extracted:
  - Foundation layer: [N] required, [M] forbidden, [P] guardrails
  - Core layer: [N] required, [M] forbidden, [P] guardrails
  - Feature layer: ...
  - Presentation layer: ...
  - Global: [N] naming conventions, [M] forbidden APIs, [P] approved libraries
```

Use `AskUserQuestion`:
- Prompt: "Does this rule summary look complete?"
- Options:
  - `[A] Yes — looks good, run the director review and write the manifest`
  - `[B] Add rules — I have additional rules to include before writing`
  - `[C] Remove rules — some extracted rules should be dropped`
  - `[D] Stop here — I need to review the ADRs first`
使用 `AskUserQuestion`：
- 提示："这份规则摘要看起来完整吗？"
- 选项：
  - `[A] 是 —— 看起来不错，运行主管评审并写入清单`
  - `[B] 添加规则 —— 写入前我还有额外的规则要纳入`
  - `[C] 移除规则 —— 某些提取的规则应被剔除`
  - `[D] 到此为止 —— 我需要先评审 ADR`

---

## 4b. Director Gate — Technical Review
## 4b. 主管门禁 —— 技术评审

**Review mode check** — apply before spawning TD-MANIFEST:
- `solo` → skip. Note: "TD-MANIFEST skipped — Solo mode." Proceed to Phase 5.
- `lean` → skip. Note: "TD-MANIFEST skipped — Lean mode." Proceed to Phase 5.
- `full` → spawn as normal.
**评审模式检查** —— 在派生 TD-MANIFEST 之前应用：
- `solo` → 跳过。附注："TD-MANIFEST skipped —— Solo 模式。"进入第 5 阶段。
- `lean` → 跳过。附注："TD-MANIFEST skipped —— Lean 模式。"进入第 5 阶段。
- `full` → 正常派生。

Spawn `technical-director` via Task using gate **TD-MANIFEST** (`.claude/docs/director-gates.md`).
通过 Task 派生 `technical-director`，使用门禁 **TD-MANIFEST**（`.claude/docs/director-gates.md`）。

Pass: the Control Manifest Preview from Phase 4 (rule counts per layer, full extracted rule list), the list of ADRs covered, engine version, and any rules sourced from technical-preferences.md or engine reference docs.
传递：第 4 阶段的控制清单预览（每层规则计数、完整提取的规则列表）、所覆盖的 ADR 列表、引擎版本，以及任何源自 technical-preferences.md 或引擎参考文档的规则。

The technical-director reviews whether:
- All mandatory ADR patterns are captured and accurately stated
- Forbidden approaches are complete and correctly attributed
- No rules were added that lack a source ADR or preference document
- Performance guardrails are consistent with the ADR constraints
technical-director 评审：
- 所有强制性 ADR 模式是否被捕获并准确表述
- 禁用方法是否完整且归因正确
- 是否存在缺少来源 ADR 或偏好文档的规则
- 性能护栏是否与 ADR 约束一致

Apply the verdict:
- **APPROVE** → proceed to Phase 5
- **CONCERNS** → surface via `AskUserQuestion` with options: `Revise flagged rules` / `Accept and proceed` / `Discuss further`
- **REJECT** → do not write the manifest; fix the flagged rules and re-present the summary
应用结论：
- **APPROVE** → 进入第 5 阶段
- **CONCERNS** → 通过 `AskUserQuestion` 呈现，选项：`修订被标记的规则` / `接受并继续` / `进一步讨论`
- **REJECT** → 不写入清单；修复被标记的规则并重新呈现摘要

---

## 5. Write the Control Manifest
## 5. 写入控制清单

Use `AskUserQuestion`:
- Prompt: "May I write the Control Manifest?"
- Options:
  - `[A] Yes — write to docs/architecture/control-manifest.md`
  - `[B] Show me the full draft first, then ask again`
  - `[C] Not yet — I want to make more changes`
使用 `AskUserQuestion`：
- 提示："我可以写入控制清单吗？"
- 选项：
  - `[A] 是 —— 写入 docs/architecture/control-manifest.md`
  - `[B] 先给我看完整草稿，然后再问我`
  - `[C] 暂不 —— 我想再做更多修改`

Format:
格式：

```markdown
# Control Manifest

> **Engine**: [name + version]
> **Last Updated**: [date]
> **Manifest Version**: [date]
> **ADRs Covered**: [ADR-NNNN, ADR-MMMM, ...]
> **Status**: [Active — regenerate with `/create-control-manifest update` when ADRs change]

`Manifest Version` is the date this manifest was generated. Story files embed
this date when created. `/story-readiness` compares a story's embedded version
to this field to detect stories written against stale rules. Always matches
`Last Updated` — they are the same date, serving different consumers.

This manifest is a programmer's quick-reference extracted from all Accepted ADRs,
technical preferences, and engine reference docs. For the reasoning behind each
rule, see the referenced ADR.

---

## Foundation Layer Rules

*Applies to: scene management, event architecture, save/load, engine initialisation*

### Required Patterns
- **[rule]** — source: [ADR-NNNN]
- **[rule]** — source: [ADR-NNNN]

### Forbidden Approaches
- **Never [anti-pattern]** — [brief reason] — source: [ADR-NNNN]

### Performance Guardrails
- **[system]**: max [N]ms/frame — source: [ADR-NNNN]

---

## Core Layer Rules

*Applies to: core gameplay loop, main player systems, physics, collision*

### Required Patterns
...

### Forbidden Approaches
...

### Performance Guardrails
...

---

## Feature Layer Rules

*Applies to: secondary mechanics, AI systems, secondary features*

### Required Patterns
...

### Forbidden Approaches
...

---

## Presentation Layer Rules

*Applies to: rendering, audio, UI, VFX, shaders, animations*

### Required Patterns
...

### Forbidden Approaches
...

---

## Global Rules (All Layers)

### Naming Conventions
| Element | Convention | Example |
|---------|-----------|---------|
| Classes | [from technical-preferences] | [example] |
| Variables | [from technical-preferences] | [example] |
| Signals/Events | [from technical-preferences] | [example] |
| Files | [from technical-preferences] | [example] |
| Constants | [from technical-preferences] | [example] |

### Performance Budgets
| Target | Value |
|--------|-------|
| Framerate | [from technical-preferences] |
| Frame budget | [from technical-preferences] |
| Draw calls | [from technical-preferences] |
| Memory ceiling | [from technical-preferences] |

### Approved Libraries / Addons
- [library] — approved for [purpose]

### Forbidden APIs ([engine version])
These APIs are deprecated or unverified for [engine + version]:
- `[api name]` — deprecated since [version] / unverified post-cutoff
- Source: `docs/engine-reference/[engine]/deprecated-apis.md`

### Cross-Cutting Constraints
- [constraint that applies everywhere, regardless of layer]
```

---

## 6. Suggest Next Steps
## 6. 建议后续步骤

After writing the manifest:
写入清单之后：

- If epics/stories don't exist yet: "Run `/create-epics layer: foundation` then `/create-stories [epic-slug]` — programmers
  can now use this manifest when writing story implementation notes."
- If this is a regeneration (manifest already existed): "Updated. Recommend
  notifying the team of changed rules — especially any new Forbidden entries."
- 若史诗/故事尚不存在："运行 `/create-epics layer: foundation` 然后 `/create-stories [epic-slug]` ——
  程序员现在编写故事实现说明时即可使用本清单。"
- 若本次为重新生成（清单已存在）："已更新。建议
  通知团队规则变更 —— 尤其是任何新增的禁用条目。"

---

## Collaborative Protocol
## 协作协议

1. **Load silently** — read all inputs before presenting anything
2. **Show the summary first** — let the user see the scope before writing
3. **Ask before writing** — always confirm before creating or overwriting the manifest. On write: Verdict: **COMPLETE** — control manifest written. On decline: Verdict: **BLOCKED** — user declined write.
4. **Source every rule** — never add a rule that doesn't trace to an ADR, a
   technical preference, or an engine reference doc
5. **No interpretation** — extract rules as stated in ADRs; do not paraphrase
   in ways that change meaning
1. **静默加载** —— 呈现任何内容之前读取全部输入
2. **先呈现摘要** —— 写入前让用户看到范围
3. **写入前征询** —— 创建或覆盖清单前总是确认。写入时：结论：**COMPLETE** —— 控制清单已写入。拒绝时：结论：**BLOCKED** —— 用户拒绝写入。
4. **为每条规则溯源** —— 绝不添加无法追溯到 ADR、
   技术偏好或引擎参考文档的规则
5. **不做解释** —— 按 ADR 中所述提取规则；不要以改变
   含义的方式改写
