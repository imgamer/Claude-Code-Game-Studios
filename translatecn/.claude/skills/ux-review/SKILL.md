---
name: ux-review
description: "Validates a UX spec, HUD design, or interaction pattern library for completeness, accessibility compliance, GDD alignment, and implementation readiness. Produces APPROVED / NEEDS REVISION / MAJOR REVISION NEEDED verdict with specific gaps."
argument-hint: "[file-path or 'all' or 'hud' or 'patterns']"
user-invocable: true
allowed-tools: Read, Glob, Grep
model: sonnet
agent: ux-designer
---
---
名称：ux-review
描述："验证 UX 规格、HUD 设计或交互模式库的完整性、可访问性合规、GDD 对齐与实现就绪。产出 APPROVED / NEEDS REVISION / MAJOR REVISION NEEDED 结论及具体缺漏。"
argument-hint："[file-path or 'all' or 'hud' or 'patterns']"
user-invocable：true
allowed-tools：Read, Glob, Grep
model：sonnet
agent：ux-designer
---

## Overview
## 概述

Validates UX design documents before they enter the implementation pipeline.
Acts as the quality gate between UX Design and Visual Design/Implementation in
the `/team-ui` pipeline.
在 UX 设计文档进入实现流水线之前验证它们。在 `/team-ui` 流水线中充当 UX 设计与
视觉设计/实现之间的质量门禁。

**Run this skill:**
- After completing a UX spec with `/ux-design`
- Before handing off to `ui-programmer` or `art-director`
- Before the Pre-Production to Production gate check (which requires key screens
  to have reviewed UX specs)
- After major revisions to a UX spec
**运行此技能：**
- 用 `/ux-design` 完成 UX 规格之后
- 交给 `ui-programmer` 或 `art-director` 之前
- 在 Pre-Production 到 Production 门禁检查之前（要求关键屏幕有已评审的 UX 规格）
- 在 UX 规格重大修订之后

**Verdict levels:**
- **APPROVED** — spec is complete, consistent, and implementation-ready
- **NEEDS REVISION** — specific gaps found; fix before handoff but not a full redesign
- **MAJOR REVISION NEEDED** — fundamental issues with scope, player need, or
  completeness; needs significant rework
**结论级别：**
- **APPROVED** —— 规格完整、一致、可立即实现
- **NEEDS REVISION** —— 发现具体缺漏；交接前修复但非全面重设计
- **MAJOR REVISION NEEDED** —— 范围、玩家需求或完整性有根本问题；需要重大
  返工

---

## Phase 1: Parse Arguments
## 阶段 1：解析参数

- **Specific file path** (e.g., `/ux-review design/ux/inventory.md`): validate
  that one document
- **`all`**: find all files in `design/ux/` and validate each
- **`hud`**: validate `design/ux/hud.md` specifically
- **`patterns`**: validate `design/ux/interaction-patterns.md` specifically
- **No argument**: ask the user which spec to validate
- **具体文件路径**（如 `/ux-review design/ux/inventory.md`）：验证单个文档
- **`all`**：查找 `design/ux/` 中所有文件并验证每个
- **`hud`**：专门验证 `design/ux/hud.md`
- **`patterns`**：专门验证 `design/ux/interaction-patterns.md`
- **无参数**：询问用户验证哪个规格

For `all`, output a summary table first (file | verdict | primary issue) then
full detail for each.
对 `all`，先输出摘要表（file | verdict | primary issue）然后每个的完整细节。

---

## Phase 2: Load Cross-Reference Context
## 阶段 2：加载交叉引用上下文

Before validating any spec, load:
验证任何规格之前，加载：

1. **Input & Platform config**: Read `.claude/docs/technical-preferences.md` and
   extract `## Input & Platform`. This is the authoritative source for which input
   methods the game supports — use it to drive the Input Method Coverage checks in
   Phase 3A, not the spec's own header. If unconfigured, fall back to the spec header.
2. The accessibility tier committed to in `design/accessibility-requirements.md`
   (if it exists)
3. The interaction pattern library at `design/ux/interaction-patterns.md` (if
   it exists)
4. The GDDs referenced in the spec's header (read their UI Requirements sections)
5. The player journey map at `design/player-journey.md` (if it exists) for
   context-arrival validation
1. **Input & Platform 配置**：读取 `.claude/docs/technical-preferences.md` 并
   提取 `## Input & Platform`。这是游戏支持哪些输入方法的权威来源 —— 用它驱动
   阶段 3A 的 Input Method Coverage 检查，而非规格自身头。若未配置，回退到规格
   头。
2. `design/accessibility-requirements.md` 中承诺的可访问性层级（若存在）
3. `design/ux/interaction-patterns.md` 中的交互模式库（若存在）
4. 规格头中引用的 GDD（读取其 UI Requirements 段落）
5. `design/player-journey.md` 中的玩家旅程图（若存在），用于上下文到达验证

---

## Phase 3A: UX Spec Validation Checklist
## 阶段 3A：UX 规格验证检查清单

Run all checks against a `ux-spec.md`-based document.
对基于 `ux-spec.md` 的文档运行所有检查。

### Completeness (required sections)
### 完整性（必需段落）

- [ ] Document header present with Status, Author, Platform Target
- [ ] Purpose & Player Need — has a player-perspective need statement (not
  developer-perspective)
- [ ] Player Context on Arrival — describes player's state and prior activity
- [ ] Navigation Position — shows where screen sits in hierarchy
- [ ] Entry & Exit Points — all entry sources and exit destinations documented
- [ ] Layout Specification — zones defined, component inventory table present
- [ ] States & Variants — at minimum: loading, empty/populated, and error states
  documented
- [ ] Interaction Map — covers all target input methods (check platform target
  in header)
- [ ] Data Requirements — every displayed data element has a source system and owner
- [ ] Events Fired — every player action has a corresponding event or null
  explanation
- [ ] Transitions & Animations — at least enter/exit transitions specified
- [ ] Accessibility Requirements — screen-level requirements present
- [ ] Localization Considerations — max character counts for text elements
- [ ] Acceptance Criteria — at least 5 specific testable criteria
- [ ] 文档头存在含 Status、Author、Platform Target
- [ ] Purpose & Player Need —— 有玩家视角的需求陈述（非开发者视角）
- [ ] Player Context on Arrival —— 描述玩家状态与先前活动
- [ ] Navigation Position —— 展示屏幕在层级中的位置
- [ ] Entry & Exit Points —— 记录所有入口来源与出口目的地
- [ ] Layout Specification —— 定义区域、存在组件清单表
- [ ] States & Variants —— 至少：loading、empty/populated 与 error 状态已
  文档化
- [ ] Interaction Map —— 覆盖所有目标输入方法（检查头中的 platform target）
- [ ] Data Requirements —— 每个显示的数据元素有源系统与所有者
- [ ] Events Fired —— 每个玩家动作有对应事件或 null 解释
- [ ] Transitions & Animations —— 至少指定 enter/exit 过渡
- [ ] Accessibility Requirements —— 存在屏幕级要求
- [ ] Localization Considerations —— 文本元素的最大字符数
- [ ] Acceptance Criteria —— 至少 5 个具体可测试标准

### Quality Checks
### 质量检查

**Player Need Clarity**
- [ ] Purpose is written from player perspective, not system/developer perspective
- [ ] Player goal on arrival is unambiguous ("The player arrives wanting to ___")
- [ ] The player context on arrival is specific (not just "they opened the
  inventory")
**玩家需求清晰度**
- [ ] Purpose 从玩家视角写，非系统/开发者视角
- [ ] 到达时的玩家目标明确（"The player arrives wanting to ___"）
- [ ] 到达时的玩家上下文具体（不仅是 "they opened the inventory"）

**Completeness of States**
- [ ] Error state is documented (not just happy path)
- [ ] Empty state is documented (no data scenario)
- [ ] Loading state is documented if the screen fetches async data
- [ ] Any state with a timer or auto-dismiss is documented with duration
**状态完整性**
- [ ] 已文档化 error 状态（不仅是 happy path）
- [ ] 已文档化 empty 状态（无数据场景）
- [ ] 若屏幕获取异步数据则已文档化 loading 状态
- [ ] 任何带计时器或 auto-dismiss 的状态已文档化含持续时间

**Input Method Coverage**
- [ ] If platform includes PC: keyboard-only navigation is fully specified
- [ ] If platform includes console/gamepad: d-pad navigation and face button
  mapping documented
- [ ] No interaction requires mouse-like precision on gamepad
- [ ] Focus order is defined (Tab order for keyboard, d-pad order for gamepad)
**输入方法覆盖**
- [ ] 若平台含 PC：完整指定仅键盘导航
- [ ] 若平台含 console/gamepad：已文档化 d-pad 导航与面键映射
- [ ] 无交互需要 gamepad 上类似鼠标的精度
- [ ] 已定义焦点顺序（键盘 Tab 顺序，gamepad d-pad 顺序）

**Data Architecture**
- [ ] No data element has "UI" listed as the owner (UI must not own game state)
- [ ] Update frequency is specified for all real-time data (not just "realtime" —
  what triggers update?)
- [ ] Null handling is specified for all data elements (what shows when data is
  unavailable?)
**数据架构**
- [ ] 无数据元素的 owner 列为 "UI"（UI 不得拥有游戏状态）
- [ ] 所有实时数据指定更新频率（不仅是 "realtime" —— 什么触发更新？）
- [ ] 所有数据元素指定 null 处理（数据不可用时显示什么？）

**Accessibility**
- [ ] Accessibility tier from `accessibility-requirements.md` is matched or exceeded
- [ ] If Basic tier: no color-only information indicators
- [ ] If Standard tier+: focus order documented, text contrast ratios specified
- [ ] If Comprehensive tier+: screen reader announcements for key state changes
- [ ] Colorblind check: any color-coded elements have non-color alternatives
**可访问性**
- [ ] 匹配或超过 `accessibility-requirements.md` 中的可访问性层级
- [ ] 若 Basic 层级：无仅颜色的信息指示器
- [ ] 若 Standard 层级+：已文档化焦点顺序、指定文字对比度
- [ ] 若 Comprehensive 层级+：关键状态变化有屏幕阅读器公告
- [ ] 色盲检查：任何颜色编码元素有非颜色替代

**GDD Alignment**
- [ ] Every GDD UI Requirement referenced in the header is addressed in this spec
- [ ] No UI element displays or modifies game state without a corresponding GDD
  requirement
- [ ] No GDD UI Requirement is missing from this spec (cross-check the referenced
  GDD sections)
**GDD 对齐**
- [ ] 头中引用的每个 GDD UI Requirement 都在此规格中处理
- [ ] 无 UI 元素显示或修改游戏状态而无对应 GDD 需求
- [ ] 此规格无遗漏的 GDD UI Requirement（交叉检查引用的 GDD 段落）

**Pattern Library Consistency**
- [ ] All interactive components reference the pattern library (or note they are
  new patterns)
- [ ] No pattern behavior is re-specified from scratch if it already exists in
  the pattern library
- [ ] Any new patterns invented in this spec are flagged for addition to the
  pattern library
**模式库一致性**
- [ ] 所有交互组件引用模式库（或注明是新模式）
- [ ] 若模式库已存在则不从零重指定模式行为
- [ ] 此规格中发明的任何新模式标记以加入模式库

**Localization**
- [ ] Character limit warnings present for all text-heavy elements
- [ ] Any layout-critical text has been flagged for 40% expansion accommodation
**本地化**
- [ ] 所有文本密集元素存在字符限制警告
- [ ] 任何布局关键文本已标记需 40% 扩展容纳

**Acceptance Criteria Quality**
- [ ] Criteria are specific enough for a QA tester who hasn't seen the design docs
- [ ] Performance criterion present (screen opens within Xms)
- [ ] Resolution criterion present
- [ ] No criterion requires reading another document to evaluate
**验收标准质量**
- [ ] 标准足够具体以使未见设计文档的 QA 测试者能理解
- [ ] 存在性能标准（屏幕在 Xms 内打开）
- [ ] 存在分辨率标准
- [ ] 无标准需要阅读另一文档来评估

---

## Phase 3B: HUD Validation Checklist
## 阶段 3B：HUD 验证检查清单

Run all checks against a `hud-design.md`-based document.
对基于 `hud-design.md` 的文档运行所有检查。

### Completeness
### 完整性

- [ ] HUD Philosophy defined
- [ ] Information Architecture table covers ALL systems with UI Requirements in GDDs
- [ ] Layout Zones defined with safe zone margins for all target platforms
- [ ] Every HUD element has a full specification (zone, visibility trigger, data
  source, priority)
- [ ] HUD States by Gameplay Context covers at minimum: exploration, combat,
  dialogue/cutscene, paused
- [ ] Visual Budget defined (max simultaneous elements, max screen %)
- [ ] Platform Adaptation covers all target platforms
- [ ] Tuning Knobs present for player-adjustable elements
- [ ] 已定义 HUD Philosophy
- [ ] Information Architecture 表覆盖 GDD 中所有有 UI Requirements 的系统
- [ ] 已定义 Layout Zones 含所有目标平台的安全区边距
- [ ] 每个 HUD 元素有完整规格（区域、可见性触发、数据源、优先级）
- [ ] HUD States by Gameplay Context 至少覆盖：exploration、combat、
  dialogue/cutscene、paused
- [ ] 已定义 Visual Budget（最大同时元素数、最大屏幕百分比）
- [ ] Platform Adaptation 覆盖所有目标平台
- [ ] 玩家可调元素存在 Tuning Knobs

### Quality Checks
### 质量检查

- [ ] No HUD element covers the center play area without a visibility rule to
  hide it
- [ ] Every information item that exists in any GDD is either in the HUD or
  explicitly categorized as "hidden/demand"
- [ ] All color-coded HUD elements have colorblind variants
- [ ] HUD elements in the Feedback & Notification section have queue/priority
  behavior defined
- [ ] Visual Budget compliance: total simultaneous elements is within budget
- [ ] 无 HUD 元素在无隐藏可见性规则的情况下覆盖中心游戏区域
- [ ] 任何 GDD 中存在的每个信息项要么在 HUD 中，要么明确分类为 "hidden/demand"
- [ ] 所有颜色编码 HUD 元素有色盲变体
- [ ] Feedback & Notification 段落中的 HUD 元素已定义队列/优先级行为
- [ ] Visual Budget 合规：总同时元素数在预算内

### GDD Alignment
### GDD 对齐

- [ ] All systems in `design/gdd/systems-index.md` with UI category have
  representation in HUD (or justified absence)
- [ ] `design/gdd/systems-index.md` 中所有 UI 类别系统在 HUD 中有表示
  （或合理缺失）

---

## Phase 3C: Pattern Library Validation Checklist
## 阶段 3C：模式库验证检查清单

- [ ] Pattern catalog index is current (matches actual patterns in document)
- [ ] All standard control patterns are specified: button variants, toggle,
  slider, dropdown, list, grid, modal, dialog, toast, tooltip, progress bar,
  input field, tab bar, scroll
- [ ] All game-specific patterns needed by current UX specs are present
- [ ] Each pattern has: When to Use, When NOT to Use, full state specification,
  accessibility spec, implementation notes
- [ ] Animation Standards table present
- [ ] Sound Standards table present
- [ ] No conflicting behaviors between patterns (e.g., "Back" behavior consistent
  across all navigation patterns)
- [ ] 模式目录索引是最新的（匹配文档中的实际模式）
- [ ] 已指定所有标准控件模式：button 变体、toggle、slider、dropdown、list、
  grid、modal、dialog、toast、tooltip、progress bar、input field、tab bar、
  scroll
- [ ] 当前 UX 规格所需的所有游戏特定模式存在
- [ ] 每个模式有：When to Use、When NOT to Use、完整状态规格、可访问性规格、
  实现注
- [ ] 存在 Animation Standards 表
- [ ] 存在 Sound Standards 表
- [ ] 模式之间无冲突行为（如 "Back" 行为在所有导航模式中一致）

---

## Phase 4: Output the Verdict
## 阶段 4：输出结论

```markdown
## UX Review: [Document Name]
**Date**: [date]
**Reviewer**: ux-review skill
**Document**: [file path]
**Platform Target**: [from header]
**Accessibility Tier**: [from header or accessibility-requirements.md]

### Completeness: [X/Y sections present]
- [x] Purpose & Player Need
- [ ] States & Variants — MISSING: error state not documented

### Quality Issues: [N found]
1. **[Issue title]** [BLOCKING / ADVISORY]
   - What's wrong: [specific description]
   - Where: [section name]
   - Fix: [specific action to take]

### GDD Alignment: [ALIGNED / GAPS FOUND]
- GDD [name] UI Requirements — [X/Y requirements covered]
- Missing: [list any uncovered GDD requirements]

### Accessibility: [COMPLIANT / GAPS / NON-COMPLIANT]
- Target tier: [tier]
- [list specific accessibility findings]

### Pattern Library: [CONSISTENT / INCONSISTENCIES FOUND]
- [findings]

### Verdict: APPROVED / NEEDS REVISION / MAJOR REVISION NEEDED
**Blocking issues**: [N] — must be resolved before implementation
**Advisory issues**: [N] — recommended but not blocking

[For APPROVED]: This spec is ready for handoff to `/team-ui` Phase 2
(Visual Design).

[For NEEDS REVISION]: Address the [N] blocking issues above, then re-run
`/ux-review`.

[For MAJOR REVISION NEEDED]: The spec has fundamental gaps in [areas].
Recommend returning to `/ux-design` to rework [sections].
```
```markdown
## UX Review: [Document Name]
**Date**: [date]
**Reviewer**: ux-review skill
**Document**: [file path]
**Platform Target**: [from header]
**Accessibility Tier**: [from header or accessibility-requirements.md]

### Completeness: [X/Y sections present]
- [x] Purpose & Player Need
- [ ] States & Variants — MISSING: error state not documented

### Quality Issues: [N found]
1. **[Issue title]** [BLOCKING / ADVISORY]
   - What's wrong: [specific description]
   - Where: [section name]
   - Fix: [specific action to take]

### GDD Alignment: [ALIGNED / GAPS FOUND]
- GDD [name] UI Requirements — [X/Y requirements covered]
- Missing: [list any uncovered GDD requirements]

### Accessibility: [COMPLIANT / GAPS / NON-COMPLIANT]
- Target tier: [tier]
- [list specific accessibility findings]

### Pattern Library: [CONSISTENT / INCONSISTENCIES FOUND]
- [findings]

### Verdict: APPROVED / NEEDS REVISION / MAJOR REVISION NEEDED
**Blocking issues**: [N] — must be resolved before implementation
**Advisory issues**: [N] — recommended but not blocking

[For APPROVED]: This spec is ready for handoff to `/team-ui` Phase 2
(Visual Design).

[For NEEDS REVISION]: Address the [N] blocking issues above, then re-run
`/ux-review`.

[For MAJOR REVISION NEEDED]: The spec has fundamental gaps in [areas].
Recommend returning to `/ux-design` to rework [sections].
```

---

## Phase 5: Collaborative Protocol
## 阶段 5：协作协议

This skill is READ-ONLY — it never edits or writes files. It reports findings only.
本技能只读 —— 绝不编辑或写文件。仅报告发现。

After delivering the verdict:
交付结论后：
- For **APPROVED**: suggest running `/team-ui` to begin implementation coordination
- For **NEEDS REVISION**: offer to help fix specific gaps ("Would you like me to
  help draft the missing error state?") — but do not auto-fix; wait for user
  instruction
- For **MAJOR REVISION NEEDED**: suggest returning to `/ux-design` with the
  specific sections to rework
- 对 **APPROVED**：建议运行 `/team-ui` 开始实现协调
- 对 **NEEDS REVISION**：提供帮助修复具体缺漏（"Would you like me to
  help draft the missing error state?"）—— 但不自动修复；等待用户指示
- 对 **MAJOR REVISION NEEDED**：建议回到 `/ux-design` 重做具体段落

Never block the user from proceeding — the verdict is advisory. Document risks,
present findings, let the user decide whether to proceed despite concerns. A user
who chooses to proceed with a NEEDS REVISION spec takes on the documented risk.
绝不阻止用户继续 —— 结论是建议性的。记录风险、展示发现、让用户决定是否不顾
担忧继续。选择带 NEEDS REVISION 规格继续的用户承担已记录风险。
