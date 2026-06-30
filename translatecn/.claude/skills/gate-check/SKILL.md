---
name: gate-check
description: "Validate readiness to advance between development phases. Produces a PASS/CONCERNS/FAIL verdict with specific blockers and required artifacts. Use when user says 'are we ready to move to X', 'can we advance to production', 'check if we can start the next phase', 'pass the gate'."
argument-hint: "[target-phase: systems-design | technical-setup | pre-production | production | polish | release] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, Task, AskUserQuestion
model: opus
---
---
name: gate-check
description: "验证是否具备进入下一开发阶段的就绪状态。生成包含具体阻塞项和必需产出物的 PASS/CONCERNS/FAIL（通过/顾虑/失败）判定。当用户说'我们准备好进入 X 了吗'、'我们可以推进到生产阶段了吗'、'检查我们能否开始下一阶段'、'通过门禁'时使用。"
argument-hint: "[target-phase: systems-design | technical-setup | pre-production | production | polish | release] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, Task, AskUserQuestion
model: opus
---

# Phase Gate Validation

This skill validates whether the project is ready to advance to the next development
phase. It checks for required artifacts, quality standards, and blockers.

**Distinct from `/project-stage-detect`**: That skill is diagnostic ("where are we?").
This skill is prescriptive ("are we ready to advance?" with a formal verdict).

# 阶段门禁验证

本技能验证项目是否已准备好进入下一开发阶段。它会检查必需的产出物、质量标准和阻塞项。

**与 `/project-stage-detect` 的区别**：该技能是诊断性的（"我们在哪里？"）。
本技能是规范性的（"我们准备好推进了吗？"并给出正式判定）。

## Production Stages (7)

The project progresses through these stages:

1. **Concept** — Brainstorming, game concept document
2. **Systems Design** — Mapping systems, writing GDDs
3. **Technical Setup** — Engine config, architecture decisions
4. **Pre-Production** — Prototyping, vertical slice validation
5. **Production** — Feature development (Epic/Feature/Task tracking active)
6. **Polish** — Performance, playtesting, bug fixing
7. **Release** — Launch prep, certification

**When a gate passes**, write the new stage name to `production/stage.txt`
(single line, e.g. `Production`). This updates the status line immediately.

## 生产阶段（7 个）

项目按以下阶段推进：

1. **概念（Concept）** — 头脑风暴，游戏概念文档
2. **系统设计（Systems Design）** — 映射系统，撰写 GDD
3. **技术设置（Technical Setup）** — 引擎配置，架构决策
4. **预生产（Pre-Production）** — 原型制作，垂直切片验证
5. **生产（Production）** — 功能开发（史诗/功能/任务跟踪已激活）
6. **打磨（Polish）** — 性能、试玩、修 Bug
7. **发布（Release）** — 上线准备，认证

**门禁通过时**，将新的阶段名称写入 `production/stage.txt`
（单行，例如 `Production`）。这会立即更新状态行。

---

## 1. Parse Arguments

**Target phase:** `$ARGUMENTS[0]` (blank = auto-detect current stage, then validate next transition)

Also resolve the review mode (once, store for all gate spawns this run):
1. If `--review [full|lean|solo]` was passed → use that
2. Else read `production/review-mode.txt` → use that value
3. Else → default to `lean`

Note: in `solo` mode, director spawns (CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE) are skipped — gate-check becomes artifact-existence checks only. In `lean` mode, all four directors still run (phase gates are the purpose of lean mode).

- **With argument**: `/gate-check production` — validate readiness for that specific phase
- **No argument**: Auto-detect current stage using the same heuristics as
  `/project-stage-detect`, then **confirm with the user before running**:

  Use `AskUserQuestion`:
  - Prompt: "Detected stage: **[current stage]**. Running gate for [Current] → [Next] transition. Is this correct?"
  - Options:
    - `[A] Yes — run this gate`
    - `[B] No — pick a different gate` (if selected, show a second widget listing all gate options: Concept → Systems Design, Systems Design → Technical Setup, Technical Setup → Pre-Production, Pre-Production → Production, Production → Polish, Polish → Release)
  
  Do not skip this confirmation step when no argument is provided.

## 1. 解析参数

**目标阶段：** `$ARGUMENTS[0]`（为空 = 自动检测当前阶段，然后验证下一次过渡）

同时解析评审模式（仅一次，保存供本次运行的所有门禁派生使用）：
1. 如果传入了 `--review [full|lean|solo]` → 使用该值
2. 否则读取 `production/review-mode.txt` → 使用该值
3. 否则 → 默认为 `lean`

注意：在 `solo` 模式下，总监派生（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE）会被跳过 —— gate-check 仅进行产出物存在性检查。在 `lean` 模式下，四位总监仍会运行（阶段门禁正是 lean 模式的目的）。

- **带参数**：`/gate-check production` — 验证针对该特定阶段的就绪状态
- **无参数**：使用与 `/project-stage-detect` 相同的启发式方法自动检测当前阶段，
  然后**在运行前向用户确认**：

  使用 `AskUserQuestion`：
  - 提示："检测到阶段：**[当前阶段]**。为 [当前] → [下一] 过渡运行门禁。是否正确？"
  - 选项：
    - `[A] 是 —— 运行此门禁`
    - `[B] 否 —— 选择不同门禁`（如选中，显示第二个组件列出所有门禁选项：概念 → 系统设计，系统设计 → 技术设置，技术设置 → 预生产，预生产 → 生产，生产 → 打磨，打磨 → 发布）
  
  当未提供参数时，不要跳过此确认步骤。

---

## 2. Phase Gate Definitions

### Gate: Concept → Systems Design

**Required Artifacts:**
- [ ] `design/gdd/game-concept.md` exists and has content
- [ ] Game pillars defined (in concept doc or `design/gdd/game-pillars.md`)
- [ ] Visual Identity Anchor section exists in `design/gdd/game-concept.md` (from brainstorm Phase 4 art-director output)

**Recommended (not blocking):**
- [ ] Concept prototype exists in `prototypes/` with a REPORT.md showing PROCEED verdict
      (`/prototype [core-mechanic]`) — skipping this means GDDs may be written for an
      idea that hasn't been played. Acceptable if the concept is proven by other means.

**Quality Checks:**
- [ ] Game concept has been reviewed (`/design-review` verdict not MAJOR REVISION NEEDED)
- [ ] Core loop is described and understood
- [ ] Target audience is identified
- [ ] Visual Identity Anchor contains a one-line visual rule and at least 2 supporting visual principles

## 2. 阶段门禁定义

### 门禁：概念 → 系统设计

**必需产出物：**
- [ ] `design/gdd/game-concept.md` 存在且有内容
- [ ] 已定义游戏支柱（在概念文档或 `design/gdd/game-pillars.md` 中）
- [ ] `design/gdd/game-concept.md` 中存在视觉标识锚点章节（来自头脑风暴第 4 阶段艺术总监输出）

**推荐（非阻塞）：**
- [ ] `prototypes/` 中存在概念原型，且 REPORT.md 显示 PROCEED 判定
      （`/prototype [核心机制]`）—— 跳过此步骤意味着可能为尚未试玩过的
      想法撰写 GDD。如通过其他方式已证明概念则可接受。

**质量检查：**
- [ ] 游戏概念已完成评审（`/design-review` 判定不是 MAJOR REVISION NEEDED）
- [ ] 核心循环已被描述并理解
- [ ] 已识别目标受众
- [ ] 视觉标识锚点包含一句视觉规则及至少 2 条支持性视觉原则

---

### Gate: Systems Design → Technical Setup

**Required Artifacts:**
- [ ] Systems index exists at `design/gdd/systems-index.md` with at least MVP systems enumerated
- [ ] All MVP-tier GDDs exist in `design/gdd/` and individually pass `/design-review`
- [ ] A cross-GDD review report exists in `design/gdd/` (from `/review-all-gdds`)

**Quality Checks:**
- [ ] All MVP GDDs pass individual design review (8 required sections, no MAJOR REVISION NEEDED verdict)
- [ ] `/review-all-gdds` verdict is not FAIL (cross-GDD consistency and design theory checks pass)
- [ ] All cross-GDD consistency issues flagged by `/review-all-gdds` are resolved or explicitly accepted
- [ ] System dependencies are mapped in the systems index and are bidirectionally consistent
- [ ] MVP priority tier is defined
- [ ] No stale GDD references flagged (older GDDs updated to reflect decisions made in later GDDs)

### 门禁：系统设计 → 技术设置

**必需产出物：**
- [ ] `design/gdd/systems-index.md` 中存在系统索引，且至少枚举了 MVP 系统
- [ ] 所有 MVP 级 GDD 存在于 `design/gdd/` 并各自通过 `/design-review`
- [ ] `design/gdd/` 中存在跨 GDD 评审报告（来自 `/review-all-gdds`）

**质量检查：**
- [ ] 所有 MVP GDD 通过单独的设计评审（8 个必需章节，无 MAJOR REVISION NEEDED 判定）
- [ ] `/review-all-gdds` 判定不是 FAIL（跨 GDD 一致性和设计理论检查通过）
- [ ] `/review-all-gdds` 标记的所有跨 GDD 一致性问题已解决或被明确接受
- [ ] 系统依赖在系统索引中已映射，且双向一致
- [ ] 已定义 MVP 优先级层级
- [ ] 未标记陈旧的 GDD 引用（较旧的 GDD 已更新以反映后续 GDD 中所做的决策）

---

### Gate: Technical Setup → Pre-Production

**Required Artifacts:**
- [ ] Engine chosen (CLAUDE.md Technology Stack is not `[CHOOSE]`)
- [ ] Technical preferences configured (`.claude/docs/technical-preferences.md` populated)
- [ ] Art bible exists at `design/art/art-bible.md` with at least Sections 1–4 (Visual Identity Foundation)
- [ ] At least 3 Architecture Decision Records in `docs/architecture/` covering
      Foundation-layer systems (scene management, event architecture, save/load)
- [ ] Engine reference docs exist in `docs/engine-reference/[engine]/`
- [ ] Test framework initialized: `tests/unit/` and `tests/integration/` directories exist
- [ ] CI/CD test workflow exists at `.github/workflows/tests.yml` (or equivalent)
- [ ] At least one example test file exists to confirm the framework is functional
- [ ] Master architecture document exists at `docs/architecture/architecture.md`
- [ ] Architecture traceability index exists at `docs/architecture/requirements-traceability.md`
- [ ] `/architecture-review` has been run (a review report file exists in `docs/architecture/`)
- [ ] `design/accessibility-requirements.md` exists with accessibility tier committed
- [ ] `design/ux/interaction-patterns.md` exists (pattern library initialized, even if minimal)

**Quality Checks:**
- [ ] Architecture decisions cover core systems (rendering, input, state management)
- [ ] Technical preferences have naming conventions and performance budgets set
- [ ] Accessibility tier is defined and documented (even "Basic" is acceptable — undefined is not)
- [ ] At least one screen's UX spec started (often the main menu or core HUD is designed during Technical Setup)
- [ ] All ADRs have an **Engine Compatibility section** with engine version stamped
- [ ] All ADRs have a **GDD Requirements Addressed section** with explicit GDD linkage
- [ ] No ADR references APIs listed in `docs/engine-reference/[engine]/deprecated-apis.md`
- [ ] All HIGH RISK engine domains (per VERSION.md) have been explicitly addressed
      in the architecture document or flagged as open questions
- [ ] Architecture traceability matrix has **zero Foundation layer gaps**
      (all Foundation requirements must have ADR coverage before Pre-Production)

**ADR Circular Dependency Check**: For all ADRs in `docs/architecture/`, read each ADR's
"ADR Dependencies" / "Depends On" section. Build a dependency graph (ADR-A → ADR-B means
A depends on B). If any cycle is detected (e.g. A→B→A, or A→B→C→A):
- Flag as **FAIL**: "Circular ADR dependency: [ADR-X] → [ADR-Y] → [ADR-X].
  Neither can reach Accepted while the cycle exists. Remove one 'Depends On' edge to
  break the cycle."

**Engine Validation** (read `docs/engine-reference/[engine]/VERSION.md` first):
- [ ] ADRs that touch post-cutoff engine APIs are flagged with Knowledge Risk: HIGH/MEDIUM
- [ ] `/architecture-review` engine audit shows no deprecated API usage
- [ ] All ADRs agree on the same engine version (no stale version references)

### 门禁：技术设置 → 预生产

**必需产出物：**
- [ ] 已选定引擎（CLAUDE.md 的 Technology Stack 不是 `[CHOOSE]`）
- [ ] 已配置技术偏好（`.claude/docs/technical-preferences.md` 已填充）
- [ ] `design/art/art-bible.md` 存在美术风格指南，至少包含第 1–4 章（视觉标识基础）
- [ ] `docs/architecture/` 中至少有 3 份架构决策记录（ADR），覆盖
      基础层系统（场景管理、事件架构、存档/读档）
- [ ] `docs/engine-reference/[engine]/` 中存在引擎参考文档
- [ ] 已初始化测试框架：存在 `tests/unit/` 和 `tests/integration/` 目录
- [ ] `.github/workflows/tests.yml`（或同等文件）中存在 CI/CD 测试工作流
- [ ] 至少存在一个示例测试文件以确认框架可用
- [ ] `docs/architecture/architecture.md` 中存在主架构文档
- [ ] `docs/architecture/requirements-traceability.md` 中存在架构可追溯性索引
- [ ] 已运行过 `/architecture-review`（`docs/architecture/` 中存在一份评审报告文件）
- [ ] `design/accessibility-requirements.md` 存在并已承诺可访问性等级
- [ ] `design/ux/interaction-patterns.md` 存在（已初始化模式库，即便内容很少）

**质量检查：**
- [ ] 架构决策覆盖核心系统（渲染、输入、状态管理）
- [ ] 技术偏好已设置命名约定和性能预算
- [ ] 可访问性等级已定义并文档化（即使是 "Basic" 也可接受 —— 未定义则不可接受）
- [ ] 至少一个屏幕的 UX 规格已开始（通常主菜单或核心 HUD 在技术设置期间设计）
- [ ] 所有 ADR 都包含 **Engine Compatibility 章节** 并标注引擎版本
- [ ] 所有 ADR 都包含 **GDD Requirements Addressed 章节** 并显式关联 GDD
- [ ] 没有 ADR 引用 `docs/engine-reference/[engine]/deprecated-apis.md` 中列出的 API
- [ ] 所有 HIGH RISK（高风险）引擎领域（按 VERSION.md）已在
      架构文档中被显式处理或标记为开放问题
- [ ] 架构可追溯性矩阵 **Foundation 层零缺口**
      （所有 Foundation 需求在进入预生产前必须有 ADR 覆盖）

**ADR 循环依赖检查**：对 `docs/architecture/` 中的所有 ADR，读取每个 ADR 的
"ADR Dependencies" / "Depends On" 章节。构建依赖图（ADR-A → ADR-B 表示
A 依赖 B）。如检测到任何循环（例如 A→B→A，或 A→B→C→A）：
- 标记为 **FAIL**："循环 ADR 依赖：[ADR-X] → [ADR-Y] → [ADR-X]。
  循环存在时二者都无法达到 Accepted。移除一条 'Depends On' 边以
  打破循环。"

**引擎验证**（先读取 `docs/engine-reference/[engine]/VERSION.md`）：
- [ ] 涉及截止线后引擎 API 的 ADR 被标记为 Knowledge Risk: HIGH/MEDIUM
- [ ] `/architecture-review` 引擎审计显示无弃用 API 用法
- [ ] 所有 ADR 就同一引擎版本达成一致（无陈旧版本引用）

---

### Gate: Pre-Production → Production

**Required Artifacts:**
- [ ] Vertical slice exists in `prototypes/` with a REPORT.md (run `/vertical-slice`) — **recommended, not blocking**; if absent, surface as CONCERNS
- [ ] First sprint plan exists in `production/sprints/`
- [ ] Art bible is complete (all 9 sections) and AD-ART-BIBLE sign-off verdict is recorded in `design/art/art-bible.md`
- [ ] Entity inventory exists at `design/assets/entity-inventory.md` (recommended — run `/asset-spec` with no arguments to generate collaboratively from GDDs + art bible)
- [ ] All MVP-tier GDDs from systems index are complete
- [ ] Master architecture document exists at `docs/architecture/architecture.md`
- [ ] At least 3 ADRs covering Foundation-layer decisions exist in `docs/architecture/`
- [ ] All Foundation and Core layer ADRs have status `Accepted` (not `Proposed`) — stories cannot be unblocked until their governing ADR is accepted
- [ ] Control manifest exists at `docs/architecture/control-manifest.md`
      (generated by `/create-control-manifest` from Accepted ADRs)
- [ ] Epics defined in `production/epics/` with at least Foundation and Core
      layer epics present (use `/create-epics layer: foundation` and
      `/create-epics layer: core` to create them, then `/create-stories [epic-slug]`
      for each epic)
- [ ] Vertical Slice build exists and is playable (not just scope-defined) — **recommended, not blocking**; if absent, surface as CONCERNS
- [ ] Vertical Slice has been playtested with at least 1 documented session — **recommended, not blocking**; if absent, surface as CONCERNS
- [ ] Vertical Slice playtest report exists at `production/playtests/` or equivalent — **recommended, not blocking**; if absent, surface as CONCERNS
- [ ] UX specs exist for key screens: main menu, core gameplay HUD (at `design/ux/`), pause menu
- [ ] HUD design document exists at `design/ux/hud.md` (if game has in-game HUD)
- [ ] All key screen UX specs have passed `/ux-review` (verdict APPROVED or NEEDS REVISION accepted)

**Quality Checks:**
- [ ] **Core loop fun is validated** — playtest data confirms the central mechanic is enjoyable, not just functional. Explicitly check the Vertical Slice playtest report.
- [ ] UX specs cover all UI Requirements sections from MVP-tier GDDs
- [ ] Interaction pattern library documents patterns used in key screens
- [ ] Accessibility tier from `design/accessibility-requirements.md` is addressed in all key screen UX specs
- [ ] Sprint plan references real story file paths from `production/epics/`
      (not just GDDs — stories must embed GDD req ID + ADR reference)
- [ ] **Vertical Slice is COMPLETE**, not just scoped — the build demonstrates the full core loop end-to-end. At least one complete [start → challenge → resolution] cycle works.
- [ ] Architecture document has no unresolved open questions in Foundation or Core layers
- [ ] All ADRs have Engine Compatibility sections stamped with the engine version
- [ ] All ADRs have ADR Dependencies sections (even if all fields are "None")
- [ ] Manual validation confirms GDDs + architecture + epics are coherent
      (run `/review-all-gdds` and `/architecture-review` if not done recently)
- [ ] **Core fantasy is delivered** — at least one playtester independently described an experience that matches the Player Fantasy section of the core system GDDs (without being prompted).

**Vertical Slice Validation** (only run these checks if a Vertical Slice was built):
- [ ] A human has played through the core loop without developer guidance
- [ ] The game communicates what to do within the first 2 minutes of play
- [ ] No critical "fun blocker" bugs exist in the Vertical Slice build
- [ ] The core mechanic feels good to interact with (this is a subjective check — ask the user)

> **Verdict rules for Vertical Slice:**
> - **Slice was built AND any validation item is NO** → verdict is automatically FAIL. A broken
>   or unfun vertical slice should not advance to Production.
> - **Slice was not built (skipped)** → downgrade to CONCERNS only, not FAIL. Surface the risk
>   clearly: "Advancing without a validated Vertical Slice increases the risk of late-stage design
>   pivots. Recommended before committing full production scope." The user decides.
> - Skipping is a valid solo dev or time-constrained call. Shipping a broken one is not.

### 门禁：预生产 → 生产

**必需产出物：**
- [ ] `prototypes/` 中存在垂直切片并附带 REPORT.md（运行 `/vertical-slice`）—— **推荐，非阻塞**；如缺失则提示为 CONCERNS
- [ ] `production/sprints/` 中存在首份冲刺计划
- [ ] 美术风格指南完整（全部 9 章），且 AD-ART-BIBLE 签署判定已记录在 `design/art/art-bible.md` 中
- [ ] `design/assets/entity-inventory.md` 中存在实体清单（推荐 —— 不带参数运行 `/asset-spec` 以从 GDD + 美术风格指南协作生成）
- [ ] 系统索引中所有 MVP 级 GDD 均已完成
- [ ] `docs/architecture/architecture.md` 中存在主架构文档
- [ ] `docs/architecture/` 中至少有 3 份覆盖 Foundation 层决策的 ADR
- [ ] 所有 Foundation 和 Core 层 ADR 的状态均为 `Accepted`（非 `Proposed`）—— 在其所属 ADR 被接受前，故事无法解锁
- [ ] `docs/architecture/control-manifest.md` 中存在控制清单
      （由 `/create-control-manifest` 从 Accepted ADR 生成）
- [ ] `production/epics/` 中已定义史诗，且至少包含 Foundation 和 Core
      层史诗（使用 `/create-epics layer: foundation` 和
      `/create-epics layer: core` 创建它们，再为每个史诗运行
      `/create-stories [epic-slug]`）
- [ ] 垂直切片构建存在且可玩（不仅是范围已定义）—— **推荐，非阻塞**；如缺失则提示为 CONCERNS
- [ ] 垂直切片已完成至少 1 次记录在案的试玩 —— **推荐，非阻塞**；如缺失则提示为 CONCERNS
- [ ] `production/playtests/` 或同等位置存在垂直切片试玩报告 —— **推荐，非阻塞**；如缺失则提示为 CONCERNS
- [ ] 关键屏幕存在 UX 规格：主菜单、核心游戏 HUD（位于 `design/ux/`）、暂停菜单
- [ ] `design/ux/hud.md` 中存在 HUD 设计文档（如游戏有游戏内 HUD）
- [ ] 所有关键屏幕 UX 规格已通过 `/ux-review`（判定为 APPROVED 或 NEEDS REVISION 被接受）

**质量检查：**
- [ ] **核心循环乐趣已验证** —— 试玩数据确认核心机制令人愉悦，而不仅是功能可用。显式检查垂直切片试玩报告。
- [ ] UX 规格覆盖 MVP 级 GDD 中所有 UI Requirements 章节
- [ ] 交互模式库记录了关键屏幕中使用的模式
- [ ] `design/accessibility-requirements.md` 中的可访问性等级在所有关键屏幕 UX 规格中被处理
- [ ] 冲刺计划引用 `production/epics/` 中真实的故事文件路径
     （不只是 GDD —— 故事必须嵌入 GDD 需求 ID + ADR 引用）
- [ ] **垂直切片已完成**，不仅是范围已定 —— 构建演示完整核心循环端到端。至少一个完整的 [开始 → 挑战 → 解决] 周期可运行。
- [ ] 架构文档在 Foundation 或 Core 层无未解决的开放问题
- [ ] 所有 ADR 都有标注引擎版本的 Engine Compatibility 章节
- [ ] 所有 ADR 都有 ADR Dependencies 章节（即使所有字段为 "None"）
- [ ] 人工验证确认 GDD + 架构 + 史诗保持一致
      （如近期未做，运行 `/review-all-gdds` 和 `/architecture-review`）
- [ ] **核心幻想已传达** —— 至少一名试玩者在未被提示的情况下，独立描述了与核心系统 GDD 中 Player Fantasy 章节相符的体验。

**垂直切片验证**（仅在已构建垂直切片时运行这些检查）：
- [ ] 有人在无开发人员指导下玩通了核心循环
- [ ] 游戏在开始游玩的前 2 分钟内传达了要做什么
- [ ] 垂直切片构建中不存在关键的"乐趣阻断"Bug
- [ ] 核心机制交互感觉良好（这是主观检查 —— 询问用户）

> **垂直切片的判定规则：**
> - **切片已构建 AND 任一验证项为 NO** → 判定自动为 FAIL。一个损坏
>   或无趣的垂直切片不应推进到生产阶段。
> - **切片未构建（跳过）** → 仅降级为 CONCERNS，不是 FAIL。清晰提示
>   风险："未经垂直切片验证就推进会增加后期设计转向的风险。
>   在投入完整生产范围前建议先做。"由用户决定。
> - 跳过对独立开发者或时间受限的情况是合理选择。交付一个损坏的切片则不是。

---

### Gate: Production → Polish

**Required Artifacts:**
- [ ] `src/` has active code organized into subsystems
- [ ] All core mechanics from GDD are implemented (cross-reference `design/gdd/` with `src/`)
- [ ] Main gameplay path is playable end-to-end
- [ ] Test files exist in `tests/unit/` and `tests/integration/` covering Logic and Integration stories
- [ ] All Logic stories from this sprint have corresponding unit test files in `tests/unit/`
- [ ] Smoke check has been run with a PASS or PASS WITH WARNINGS verdict — report exists in `production/qa/`
- [ ] QA plan exists in `production/qa/` (generated by `/qa-plan`) covering this sprint or final production sprint
- [ ] At least one QA plan exists in `production/qa/` covering this production phase — run `/qa-plan` if missing (CONCERNS — advisory, not blocking)
- [ ] QA sign-off report exists in `production/qa/` (generated by `/team-qa`) with verdict APPROVED or APPROVED WITH CONDITIONS
- [ ] At least 3 distinct playtest sessions documented in `production/playtests/`
- [ ] Playtest reports cover: new player experience, mid-game systems, and difficulty curve
- [ ] Fun hypothesis from Game Concept has been explicitly validated or revised

**Quality Checks:**
- [ ] Tests are passing (run test suite via Bash)
- [ ] No critical/blocker bugs in any bug tracker or known issues
- [ ] Core loop plays as designed (compare to GDD acceptance criteria)
- [ ] Performance is within budget (check technical-preferences.md targets)
- [ ] Playtest findings have been reviewed and critical fun issues addressed (not just documented)
- [ ] No "confusion loops" identified — no point in the game where >50% of playtesters got stuck without knowing why
- [ ] Difficulty curve matches the Difficulty Curve design doc (if one exists at `design/difficulty-curve.md`)
- [ ] All implemented screens have corresponding UX specs (no "designed in-code" screens)
- [ ] Interaction pattern library is up-to-date with all patterns used in implementation
- [ ] Accessibility compliance verified against committed tier in `design/accessibility-requirements.md`

### 门禁：生产 → 打磨

**必需产出物：**
- [ ] `src/` 中有按子系统组织的活跃代码
- [ ] GDD 中所有核心机制均已实现（对照 `design/gdd/` 与 `src/`）
- [ ] 主游戏路径可端到端游玩
- [ ] `tests/unit/` 和 `tests/integration/` 中存在覆盖 Logic 和 Integration 故事的测试文件
- [ ] 本冲刺所有 Logic 故事在 `tests/unit/` 中有对应的单元测试文件
- [ ] 已运行 smoke check 并得到 PASS 或 PASS WITH WARNINGS 判定 —— 报告存在于 `production/qa/`
- [ ] `production/qa/` 中存在覆盖本冲刺或最终生产冲刺的 QA 计划（由 `/qa-plan` 生成）
- [ ] `production/qa/` 中至少存在一份覆盖本生产阶段的 QA 计划 —— 如缺失则运行 `/qa-plan`（CONCERNS —— 建议，非阻塞）
- [ ] `production/qa/` 中存在 QA 签署报告（由 `/team-qa` 生成），判定为 APPROVED 或 APPROVED WITH CONDITIONS
- [ ] `production/playtests/` 中记录了至少 3 次独立的试玩会话
- [ ] 试玩报告覆盖：新玩家体验、中后期系统、难度曲线
- [ ] 游戏概念中的乐趣假设已被显式验证或修订

**质量检查：**
- [ ] 测试通过（通过 Bash 运行测试套件）
- [ ] 任何 Bug 跟踪器或已知问题中无关键/阻塞 Bug
- [ ] 核心循环按设计运行（与 GDD 验收标准对比）
- [ ] 性能在预算内（检查 technical-preferences.md 目标）
- [ ] 试玩发现已被评审，关键乐趣问题已处理（不仅是记录）
- [ ] 未发现"困惑循环" —— 游戏中没有任何位置让 >50% 的试玩者不知为何卡住
- [ ] 难度曲线与难度曲线设计文档一致（如 `design/difficulty-curve.md` 存在）
- [ ] 所有已实现屏幕都有对应的 UX 规格（无"在代码中即兴设计"的屏幕）
- [ ] 交互模式库已更新至实现中使用的所有模式
- [ ] 可访问性合规性已对照 `design/accessibility-requirements.md` 中承诺的等级验证

---

### Gate: Polish → Release

**Required Artifacts:**
- [ ] All features from milestone plan are implemented
- [ ] Content is complete (all levels, assets, dialogue referenced in design docs exist)
- [ ] Localization strings are externalized (no hardcoded player-facing text in `src/`)
- [ ] QA test plan exists (`/qa-plan` output in `production/qa/`)
- [ ] QA sign-off report exists (`/team-qa` output — APPROVED or APPROVED WITH CONDITIONS)
- [ ] All Must Have story test evidence is present (Logic/Integration: test files pass; Visual/Feel/UI: sign-off docs in `production/qa/evidence/`)
- [ ] Smoke check passes cleanly (PASS verdict) on the release candidate build
- [ ] No test regressions from previous sprint (test suite passes fully)
- [ ] Balance data has been reviewed (`/balance-check` run)
- [ ] Release checklist completed (`/release-checklist` or `/launch-checklist` run)
- [ ] Store metadata prepared (if applicable)
- [ ] Changelog / patch notes drafted

**Quality Checks:**
- [ ] Full QA pass signed off by `qa-lead`
- [ ] All tests passing
- [ ] Performance targets met across all target platforms
- [ ] No known critical, high, or medium-severity bugs
- [ ] Accessibility basics covered (remapping, text scaling if applicable)
- [ ] Localization verified for all target languages
- [ ] Legal requirements met (EULA, privacy policy, age ratings if applicable)
- [ ] Build compiles and packages cleanly

### 门禁：打磨 → 发布

**必需产出物：**
- [ ] 里程碑计划中所有功能均已实现
- [ ] 内容完整（设计文档中引用的所有关卡、资产、对话均已存在）
- [ ] 本地化字符串已外置（`src/` 中无硬编码的面向玩家文本）
- [ ] 存在 QA 测试计划（`/qa-plan` 输出于 `production/qa/`）
- [ ] 存在 QA 签署报告（`/team-qa` 输出 —— APPROVED 或 APPROVED WITH CONDITIONS）
- [ ] 所有 Must Have 故事的测试证据齐全（Logic/Integration：测试文件通过；Visual/Feel/UI：`production/qa/evidence/` 中的签署文档）
- [ ] 发布候选构建上 smoke check 干净通过（PASS 判定）
- [ ] 无上一冲刺的测试回归（测试套件全部通过）
- [ ] 平衡数据已评审（已运行 `/balance-check`）
- [ ] 发布清单已完成（已运行 `/release-checklist` 或 `/launch-checklist`）
- [ ] 商店元数据已准备（如适用）
- [ ] 变更日志 / 补丁说明已起草

**质量检查：**
- [ ] `qa-lead` 签署完整 QA 通过
- [ ] 所有测试通过
- [ ] 所有目标平台均达到性能目标
- [ ] 无已知的关键、高或中等严重程度 Bug
- [ ] 可访问性基础已覆盖（重映射、文本缩放如适用）
- [ ] 所有目标语言的本地化已验证
- [ ] 法律要求已满足（EULA、隐私政策、年龄评级如适用）
- [ ] 构建编译与打包干净

---

## 3. Run the Gate Check

**Before running artifact checks**, read `docs/consistency-failures.md` if it exists.
Extract entries whose Domain matches the target phase (e.g., if checking
Systems Design → Technical Setup, pull entries in Economy, Combat, or any GDD domain;
if checking Technical Setup → Pre-Production, pull entries in Architecture, Engine).
Carry these as context — recurring conflict patterns in the target domain warrant
increased scrutiny on those specific checks.

For each item in the target gate:

### Artifact Checks
- Use `Glob` and `Read` to verify files exist and have meaningful content
- Don't just check existence — verify the file has real content (not just a template header)
- For code checks, verify directory structure and file counts

**Systems Design → Technical Setup gate — cross-GDD review check**:
Use `Glob('design/gdd/gdd-cross-review-*.md')` to find the `/review-all-gdds` report.
If no file matches, mark the "cross-GDD review report exists" artifact as **FAIL** and
surface it prominently: "No `/review-all-gdds` report found in `design/gdd/`. Run
`/review-all-gdds` before advancing to Technical Setup."
If a file is found, read it and check the verdict line: a FAIL verdict means the
cross-GDD consistency check failed and must be resolved before advancing.

### Quality Checks
- For test checks: Run the test suite via `Bash` if a test runner is configured
- For design review checks: `Read` the GDD and check for the 8 required sections
- For performance checks: `Read` technical-preferences.md and compare against any
  profiling data in `tests/performance/` or recent `/perf-profile` output
- For localization checks: `Grep` for hardcoded strings in `src/`

### Cross-Reference Checks
- Compare `design/gdd/` documents against `src/` implementations
- Check that every system referenced in architecture docs has corresponding code
- Verify sprint plans reference real work items

## 3. 运行门禁检查

**在运行产出物检查之前**，如 `docs/consistency-failures.md` 存在则读取它。
提取 Domain 与目标阶段匹配的条目（例如，若检查
系统设计 → 技术设置，提取 Economy、Combat 或任何 GDD 领域的条目；
若检查技术设置 → 预生产，提取 Architecture、Engine 领域的条目）。
将这些作为上下文携带 —— 目标领域中反复出现的冲突模式值得
对那些特定检查加强审视。

对于目标门禁中的每一项：

### 产出物检查
- 使用 `Glob` 和 `Read` 验证文件存在且有实质内容
- 不要只检查存在性 —— 验证文件有真实内容（不仅是模板头部）
- 对于代码检查，验证目录结构和文件计数

**系统设计 → 技术设置门禁 —— 跨 GDD 评审检查**：
使用 `Glob('design/gdd/gdd-cross-review-*.md')` 查找 `/review-all-gdds` 报告。
如无匹配文件，将"跨 GDD 评审报告存在"产出物标记为 **FAIL** 并
显著提示："在 `design/gdd/` 中未找到 `/review-all-gdds` 报告。请在
推进至技术设置前运行 `/review-all-gdds`。"
如找到文件，读取它并检查判定行：FAIL 判定意味着
跨 GDD 一致性检查失败，必须在推进前解决。

### 质量检查
- 对于测试检查：如已配置测试运行器，通过 `Bash` 运行测试套件
- 对于设计评审检查：`Read` GDD 并检查 8 个必需章节
- 对于性能检查：`Read` technical-preferences.md 并与
  `tests/performance/` 中任何性能分析数据或近期 `/perf-profile` 输出对比
- 对于本地化检查：在 `src/` 中 `Grep` 硬编码字符串

### 交叉引用检查
- 将 `design/gdd/` 文档与 `src/` 实现对比
- 检查架构文档中引用的每个系统是否有对应代码
- 验证冲刺计划引用真实的工作项

---

## 4. Collaborative Assessment

For items that can't be automatically verified, **ask the user**:

- "I can't automatically verify that the core loop plays well. Has it been playtested?"
- "No playtest report found. Has informal testing been done?"
- "Performance profiling data isn't available. Would you like to run `/perf-profile`?"

**Never assume PASS for unverifiable items.** Mark them as MANUAL CHECK NEEDED.

## 4. 协作评估

对于无法自动验证的项目，**询问用户**：

- "我无法自动验证核心循环玩起来是否流畅。是否已试玩过？"
- "未找到试玩报告。是否进行过非正式测试？"
- "暂无性能分析数据。是否要运行 `/perf-profile`？"

**永远不要为不可验证的项目假设 PASS。** 将其标记为 MANUAL CHECK NEEDED。

---

## 4b. Director Panel Assessment

**Apply review mode before spawning any director:**
- `solo` → skip all four directors. Note in output: "Director Panel skipped — Solo mode. Gate verdict based on artifact and quality checks only." Proceed to Phase 5.
- `lean` → spawn all four directors (phase gates always run in lean mode — this is their purpose).
- `full` → spawn all four directors as normal.

(Review mode was resolved in Phase 1. Use that stored value here.)

Before generating the final verdict, spawn all four directors as **parallel subagents** via Task using the parallel gate protocol from `.claude/docs/director-gates.md`. Issue all four Task calls simultaneously — do not wait for one before starting the next.

**Spawn in parallel:**

1. **`creative-director`** — gate **CD-PHASE-GATE** (`.claude/docs/director-gates.md`)
2. **`technical-director`** — gate **TD-PHASE-GATE** (`.claude/docs/director-gates.md`)
3. **`producer`** — gate **PR-PHASE-GATE** (`.claude/docs/director-gates.md`)
4. **`art-director`** — gate **AD-PHASE-GATE** (`.claude/docs/director-gates.md`)

Pass to each: target phase name, list of artifacts present, and the context fields listed in that gate's definition.

**Collect all four responses, then present the Director Panel summary:**

```
## Director Panel Assessment

Creative Director:  [READY / CONCERNS / NOT READY]
  [feedback]

Technical Director: [READY / CONCERNS / NOT READY]
  [feedback]

Producer:           [READY / CONCERNS / NOT READY]
  [feedback]

Art Director:       [READY / CONCERNS / NOT READY]
  [feedback]
```

**Apply to the verdict:**
- Any director returns NOT READY → verdict is minimum FAIL (user may override with explicit acknowledgement)
- Any director returns CONCERNS → verdict is minimum CONCERNS
- All four READY → eligible for PASS (still subject to artifact and quality checks from Section 3)

## 4b. 总监小组评估

**在派生任何总监前应用评审模式：**
- `solo` → 跳过全部四位总监。在输出中注明："Director Panel skipped — Solo mode. Gate verdict based on artifact and quality checks only."（总监小组已跳过 —— Solo 模式。门禁判定仅基于产出物和质量检查。）进入第 5 阶段。
- `lean` → 派生全部四位总监（阶段门禁在 lean 模式下始终运行 —— 这正是其目的）。
- `full` → 正常派生全部四位总监。

（评审模式已在第 1 阶段解析。此处使用该存储值。）

在生成最终判定之前，通过 Task 使用 `.claude/docs/director-gates.md` 中的并行门禁协议，将四位总监作为**并行子智能体**派生。同时发出全部四个 Task 调用 —— 不要等待一个完成再开始下一个。

**并行派生：**

1. **`creative-director`** —— 门禁 **CD-PHASE-GATE**（`.claude/docs/director-gates.md`）
2. **`technical-director`** —— 门禁 **TD-PHASE-GATE**（`.claude/docs/director-gates.md`）
3. **`producer`** —— 门禁 **PR-PHASE-GATE**（`.claude/docs/director-gates.md`）
4. **`art-director`** —— 门禁 **AD-PHASE-GATE**（`.claude/docs/director-gates.md`）

向每位传递：目标阶段名称、当前产出物清单，以及该门禁定义中列出的上下文字段。

**收集全部四个响应，然后呈现总监小组总结：**

```
## Director Panel Assessment

Creative Director:  [READY / CONCERNS / NOT READY]
  [feedback]

Technical Director: [READY / CONCERNS / NOT READY]
  [feedback]

Producer:           [READY / CONCERNS / NOT READY]
  [feedback]

Art Director:       [READY / CONCERNS / NOT READY]
  [feedback]
```

**应用于判定：**
- 任一总监返回 NOT READY → 判定最低为 FAIL（用户可显式确认后覆盖）
- 任一总监返回 CONCERNS → 判定最低为 CONCERNS
- 四位全部 READY → 有资格 PASS（仍受第 3 节产出物和质量检查约束）

---

## 5. Output the Verdict

```
## Gate Check: [Current Phase] → [Target Phase]

**Date**: [date]
**Checked by**: gate-check skill

### Required Artifacts: [X/Y present]
- [x] design/gdd/game-concept.md — exists, 2.4KB
- [ ] docs/architecture/ — MISSING (no ADRs found)
- [x] production/sprints/ — exists, 1 sprint plan

### Quality Checks: [X/Y passing]
- [x] GDD has 8/8 required sections
- [ ] Tests — FAILED (3 failures in tests/unit/)
- [?] Core loop playtested — MANUAL CHECK NEEDED

### Blockers
1. **No Architecture Decision Records** — Run `/architecture-decision` to create one
   covering core system architecture before entering production.
2. **3 test failures** — Fix failing tests in tests/unit/ before advancing.

### Recommendations
- [Priority actions to resolve blockers]
- [Optional improvements that aren't blocking]

### Verdict: [PASS / CONCERNS / FAIL]
- **PASS**: All required artifacts present, all quality checks passing
- **CONCERNS**: Minor gaps exist but can be addressed during the next phase
- **FAIL**: Critical blockers must be resolved before advancing
```

## 5. 输出判定

```
## Gate Check: [Current Phase] → [Target Phase]

**Date**: [date]
**Checked by**: gate-check skill

### Required Artifacts: [X/Y present]
- [x] design/gdd/game-concept.md — exists, 2.4KB
- [ ] docs/architecture/ — MISSING (no ADRs found)
- [x] production/sprints/ — exists, 1 sprint plan

### Quality Checks: [X/Y passing]
- [x] GDD has 8/8 required sections
- [ ] Tests — FAILED (3 failures in tests/unit/)
- [?] Core loop playtested — MANUAL CHECK NEEDED

### Blockers
1. **No Architecture Decision Records** — Run `/architecture-decision` to create one
   covering core system architecture before entering production.
2. **3 test failures** — Fix failing tests in tests/unit/ before advancing.

### Recommendations
- [Priority actions to resolve blockers]
- [Optional improvements that aren't blocking]

### Verdict: [PASS / CONCERNS / FAIL]
- **PASS**: All required artifacts present, all quality checks passing
- **CONCERNS**: Minor gaps exist but can be addressed during the next phase
- **FAIL**: Critical blockers must be resolved before advancing
```

---

## 5a. Chain-of-Verification

After drafting the verdict in Phase 5, challenge it before finalising.

**Step 1 — Generate 5 challenge questions** designed to disprove the verdict:

> **Tool-action requirement**: At least 2 of the 5 challenge questions below must be answered by re-reading a specific file (Read tool) or re-running a specific check (Grep tool) — not by reflection alone. Mark these with [TOOL ACTION] to indicate a tool was used.

For a **PASS** draft:
- "Which quality checks did I verify by actually reading a file, vs. inferring they passed?"
- "Are there MANUAL CHECK NEEDED items I marked PASS without user confirmation? [TOOL ACTION] Re-scan the checklist for any [?] or MANUAL CHECK items."
- "Did I confirm all listed artifacts have real content, not just empty headers? [TOOL ACTION] Re-read the file and check it has non-placeholder content."
- "Could any blocker I dismissed as minor actually prevent the phase from succeeding?"
- "Which single check am I least confident in, and why?"

For a **CONCERNS** draft:
- "Could any listed CONCERN be elevated to a blocker given the project's current state?"
- "Is the concern resolvable within the next phase, or does it compound over time?"
- "Did I soften any FAIL condition into a CONCERN to avoid a harder verdict?"
- "Are there artifacts I didn't check that could reveal additional blockers?"
- "Do all the CONCERNS together create a blocking problem even if each is minor alone?"

For a **FAIL** draft:
- "Have I accurately separated hard blockers from strong recommendations?"
- "Are there any PASS items I was too lenient about?"
- "Am I missing any additional blockers the user should know about?"
- "Can I provide a minimal path to PASS — the specific 3 things that must change?"
- "Is the fail condition resolvable, or does it indicate a deeper design problem?"

**Step 2 — Answer each question** independently.
Do NOT reference the draft verdict text — re-check specific files or ask the user.

**Step 3 — Revise if needed:**
- If any answer reveals a missed blocker → upgrade verdict (PASS→CONCERNS or CONCERNS→FAIL)
- If any answer reveals an over-stated blocker → downgrade only if citing specific evidence
- If answers are consistent → confirm verdict unchanged

**Step 4 — Note the verification** in the final report output:
`Chain-of-Verification: [N] questions checked — verdict [unchanged | revised from X to Y]`

## 5a. 验证链

在第 5 阶段起草判定后，在最终定稿前对其进行挑战。

**步骤 1 —— 生成 5 个挑战问题**，旨在推翻判定：

> **工具动作要求**：以下 5 个挑战问题中至少 2 个必须通过重新读取特定文件（Read 工具）或重新运行特定检查（Grep 工具）来回答 —— 而非仅凭反思。用 [TOOL ACTION] 标记以表示使用了工具。

针对 **PASS** 草稿：
- "哪些质量检查是我通过实际读取文件验证的，哪些是推断通过的？"
- "是否有 MANUAL CHECK NEEDED 项目我在未经用户确认的情况下标记为 PASS？[TOOL ACTION] 重新扫描清单中所有 [?] 或 MANUAL CHECK 项。"
- "我是否确认所有列出的产出物都有真实内容，而不只是空标题？[TOOL ACTION] 重新读取文件并检查其含非占位符内容。"
- "我驳回为次要的任何阻塞项，是否实际上会阻碍该阶段成功？"
- "我最不放心的是哪一项检查，为什么？"

针对 **CONCERNS** 草稿：
- "鉴于项目当前状态，列出的任一 CONCERN 是否可升级为阻塞项？"
- "此顾虑在下一阶段可解决，还是会随时间累积？"
- "我是否将任何 FAIL 条件软化为 CONCERN 以避免更严厉的判定？"
- "是否有我未检查的产出物可能揭示额外阻塞项？"
- "所有 CONCERN 加在一起是否构成阻塞问题，即使单独看都是次要的？"

针对 **FAIL** 草稿：
- "我是否准确区分了硬阻塞项和强建议？"
- "是否有 PASS 项我过于宽松？"
- "我是否遗漏了用户应知晓的额外阻塞项？"
- "我能否提供通往 PASS 的最小路径 —— 必须改变的具体 3 件事？"
- "失败条件是否可解决，还是暗示更深层次的设计问题？"

**步骤 2 —— 独立回答每个问题**。
不要引用草稿判定文本 —— 重新检查具体文件或询问用户。

**步骤 3 —— 如需要则修订：**
- 若任一答案揭示遗漏的阻塞项 → 升级判定（PASS→CONCERNS 或 CONCERNS→FAIL）
- 若任一答案揭示夸大的阻塞项 → 仅在引用具体证据时降级
- 若答案一致 → 确认判定不变

**步骤 4 —— 在最终报告输出中记录验证**：
`Chain-of-Verification: [N] questions checked — verdict [unchanged | revised from X to Y]`

---

## 6. Update Stage on PASS

When the verdict is **PASS** and the user confirms they want to advance:

1. Write the new stage name to `production/stage.txt` (single line, no trailing newline)
2. This immediately updates the status line for all future sessions

Example: if passing the "Pre-Production → Production" gate:
```bash
echo -n "Production" > production/stage.txt
```

**Always ask before writing**: "Gate passed. May I update `production/stage.txt` to 'Production'?"

## 6. PASS 时更新阶段

当判定为 **PASS** 且用户确认要推进时：

1. 将新阶段名称写入 `production/stage.txt`（单行，无尾随换行）
2. 这会立即为所有未来会话更新状态行

例如：通过"预生产 → 生产"门禁时：
```bash
echo -n "Production" > production/stage.txt
```

**写入前始终询问**："门禁通过。可以将 `production/stage.txt` 更新为 'Production' 吗？"

---

## 7. Closing Next-Step Widget

After the verdict is presented and any stage.txt update is complete, close with a structured next-step prompt using `AskUserQuestion`.

**Tailor the options to the gate that just ran:**

For **systems-design PASS**:
```
Gate passed. What would you like to do next?
[A] Run /create-architecture — produce your master architecture blueprint and ADR work plan (recommended next step)
[B] Design more GDDs first — return here when all MVP systems are complete
[C] Stop here for this session
```

> **Note for systems-design PASS**: `/create-architecture` is the required next step before writing any ADRs. It produces the master architecture document and a prioritized list of ADRs to write. Running `/architecture-decision` without this step means writing ADRs without a blueprint — skip it at your own risk.

For **technical-setup PASS**:
```
Gate passed. What would you like to do next?
[A] Run /create-control-manifest — generate the layer rules manifest from your Accepted ADRs (do this first)
[B] Run /vertical-slice — build the Vertical Slice (do this before writing epics — validate fun first)
[C] Write more ADRs first — run /architecture-decision [next-system]
[D] Stop here for this session
```

> **Note for technical-setup PASS**: The Pre-Production sequence is deliberately ordered
> to validate fun before committing to detailed planning:
>
> 1. `/create-control-manifest` — extract technical rules from Accepted ADRs (required before epics)
> 2. `/vertical-slice` — build the Vertical Slice **FIRST**, before writing epics or stories
> 3. Playtest → `/playtest-report` — at least 1 session required to pass the Pre-Production gate; 3+ recommended before committing the full team
> 4. `/ux-design [screen]` — UX specs for main menu, core HUD, pause menu (if not done)
> 5. `/create-epics layer:foundation` then `/create-epics layer:core` — plan after fun is validated
> 6. `/create-stories [epic-slug]` for each epic
> 7. `/sprint-plan new`
>
> **Why prototype before epics?** If the prototype reveals the core loop needs to change,
> epics written before that discovery will be partially wrong. Validate fun cheaply first,
> then plan in detail. This is the #1 lesson from GDC postmortem data.

For all other gates, offer the two most logical next steps for that phase plus "Stop here".

## 7. 收尾的下一步组件

呈现判定并完成任何 stage.txt 更新后，使用 `AskUserQuestion` 以结构化的下一步提示收尾。

**根据刚运行的门禁定制选项：**

针对 **systems-design PASS**：
```
Gate passed. What would you like to do next?
[A] Run /create-architecture — produce your master architecture blueprint and ADR work plan (recommended next step)
[B] Design more GDDs first — return here when all MVP systems are complete
[C] Stop here for this session
```

> **systems-design PASS 的说明**：`/create-architecture` 是撰写任何 ADR 之前的必需下一步。它会生成主架构文档和待写 ADR 的优先级清单。跳过此步直接运行 `/architecture-decision` 等于在没有蓝图的情况下写 ADR —— 风险自负。

针对 **technical-setup PASS**：
```
Gate passed. What would you like to do next?
[A] Run /create-control-manifest — generate the layer rules manifest from your Accepted ADRs (do this first)
[B] Run /vertical-slice — build the Vertical Slice (do this before writing epics — validate fun first)
[C] Write more ADRs first — run /architecture-decision [next-system]
[D] Stop here for this session
```

> **technical-setup PASS 的说明**：预生产顺序有意安排为
> 在投入详细规划前先验证乐趣：
>
> 1. `/create-control-manifest` —— 从 Accepted ADR 提取技术规则（史诗之前必需）
> 2. `/vertical-slice` —— **首先**构建垂直切片，在撰写史诗或故事之前
> 3. 试玩 → `/playtest-report` —— 通过预生产门禁至少需要 1 次会话；投入完整团队前建议 3 次以上
> 4. `/ux-design [screen]` —— 主菜单、核心 HUD、暂停菜单的 UX 规格（如未做）
> 5. `/create-epics layer:foundation` 然后 `/create-epics layer:core` —— 在乐趣验证后再规划
> 6. 对每个史诗运行 `/create-stories [epic-slug]`
> 7. `/sprint-plan new`
>
> **为何先原型再史诗？** 若原型揭示核心循环需要更改，
> 在此发现之前写的史诗将部分有误。先以低成本验证乐趣，
> 再详细规划。这是 GDC 复盘数据的头号教训。

对于所有其他门禁，提供该阶段最合逻辑的两个下一步加上"在此停止"。

---

## 8. Follow-Up Actions

Based on the verdict, suggest specific next steps:

- **No art bible?** → `/art-bible` to create the visual identity specification
- **Art bible exists but no asset specs?** → `/asset-spec system:[name]` to generate per-asset visual specs and generation prompts from approved GDDs
- **No game concept?** → `/brainstorm` to create one
- **No systems index?** → `/map-systems` to decompose the concept into systems
- **Missing design docs?** → `/reverse-document` or delegate to `game-designer`
- **Small design change needed?** → `/quick-design` for changes under ~4 hours (bypasses full GDD pipeline)
- **No UX specs?** → `/ux-design [screen name]` to author specs, or `/team-ui [feature]` for full pipeline
- **UX specs not reviewed?** → `/ux-review [file]` or `/ux-review all` to validate
- **No accessibility requirements doc?** → run `/ux-design` which creates both `design/accessibility-requirements.md` and `design/ux/interaction-patterns.md` in one step
- **No interaction pattern library?** → `/ux-design patterns` to initialize it
- **GDDs not cross-reviewed?** → `/review-all-gdds` (run after all MVP GDDs are individually approved)
- **Cross-GDD consistency issues?** → fix flagged GDDs, then re-run `/review-all-gdds`
- **No test framework?** → `/test-setup` to scaffold the framework for your engine
- **No QA plan for current sprint?** → `/qa-plan sprint` to generate one before implementation begins
- **Missing ADRs?** → `/architecture-decision` for individual decisions
- **No master architecture doc?** → `/create-architecture` for the full blueprint
- **ADRs missing engine compatibility sections?** → Re-run `/architecture-decision`
  or manually add Engine Compatibility sections to existing ADRs
- **Missing control manifest?** → `/create-control-manifest` (requires Accepted ADRs)
- **Missing epics?** → `/create-epics layer: foundation` then `/create-epics layer: core` (requires control manifest)
- **Missing stories for an epic?** → `/create-stories [epic-slug]` (run after each epic is created)
- **Stories not implementation-ready?** → `/story-readiness` to validate stories before developers pick them up
- **Tests failing?** → delegate to `lead-programmer` or `qa-tester`
- **No playtest data?** → `/playtest-report`
- **No playtest sessions beyond the minimum?** → Additional sessions give more reliable signal. 3+ total is recommended before committing the full team. Use `/playtest-report` to structure findings.
- **No Difficulty Curve doc?** → Create `design/difficulty-curve.md` from the template at `.claude/docs/templates/difficulty-curve.md` — or use `/quick-design "difficulty curve"` for a guided session.
- **No player journey map?** → Create `design/player-journey.md` from the template at `.claude/docs/templates/player-journey.md` — or author it collaboratively using `/ux-design` Phase 2b.
- **Need a quick sprint check?** → `/sprint-status` for current sprint progress snapshot
- **Performance unknown?** → `/perf-profile`
- **Not localized?** → `/localize`
- **Ready for release?** → `/launch-checklist`

## 8. 后续行动

根据判定，建议具体的下一步：

- **没有美术风格指南？** → `/art-bible` 创建视觉标识规格
- **美术风格指南存在但无资产规格？** → `/asset-spec system:[name]` 从已批准的 GDD 生成每个资产的视觉规格和生成提示
- **没有游戏概念？** → `/brainstorm` 创建一个
- **没有系统索引？** → `/map-systems` 将概念分解为系统
- **缺少设计文档？** → `/reverse-document` 或委派给 `game-designer`
- **需要小型设计变更？** → `/quick-design` 处理约 4 小时以内的变更（绕过完整 GDD 流程）
- **没有 UX 规格？** → `/ux-design [screen name]` 撰写规格，或 `/team-ui [feature]` 走完整流程
- **UX 规格未评审？** → `/ux-review [file]` 或 `/ux-review all` 验证
- **没有可访问性需求文档？** → 运行 `/ux-design`，它会一步创建 `design/accessibility-requirements.md` 和 `design/ux/interaction-patterns.md`
- **没有交互模式库？** → `/ux-design patterns` 初始化它
- **GDD 未做交叉评审？** → `/review-all-gdds`（在所有 MVP GDD 单独批准后运行）
- **跨 GDD 一致性问题？** → 修复被标记的 GDD，然后重新运行 `/review-all-gdds`
- **没有测试框架？** → `/test-setup` 为你的引擎搭建框架
- **当前冲刺没有 QA 计划？** → `/qa-plan sprint` 在实现开始前生成一份
- **缺少 ADR？** → `/architecture-decision` 处理单个决策
- **没有主架构文档？** → `/create-architecture` 获取完整蓝图
- **ADR 缺少引擎兼容性章节？** → 重新运行 `/architecture-decision`
  或手动为现有 ADR 添加 Engine Compatibility 章节
- **缺少控制清单？** → `/create-control-manifest`（需要 Accepted ADR）
- **缺少史诗？** → `/create-epics layer: foundation` 然后 `/create-epics layer: core`（需要控制清单）
- **某个史诗缺少故事？** → `/create-stories [epic-slug]`（在每个史诗创建后运行）
- **故事未达到可实现状态？** → `/story-readiness` 在开发人员领取前验证故事
- **测试失败？** → 委派给 `lead-programmer` 或 `qa-tester`
- **没有试玩数据？** → `/playtest-report`
- **试玩会话未超过最低数量？** → 额外会话提供更可靠信号。投入完整团队前建议总共 3 次以上。使用 `/playtest-report` 结构化发现。
- **没有难度曲线文档？** → 从 `.claude/docs/templates/difficulty-curve.md` 模板创建 `design/difficulty-curve.md` —— 或使用 `/quick-design "difficulty curve"` 进行引导式会话。
- **没有玩家旅程图？** → 从 `.claude/docs/templates/player-journey.md` 模板创建 `design/player-journey.md` —— 或使用 `/ux-design` 第 2b 阶段协作撰写。
- **需要快速冲刺检查？** → `/sprint-status` 获取当前冲刺进度快照
- **性能未知？** → `/perf-profile`
- **未本地化？** → `/localize`
- **准备发布？** → `/launch-checklist`

---

## Collaborative Protocol

This skill follows the collaborative design principle:

1. **Scan first**: Check all artifacts and quality gates
2. **Ask about unknowns**: Don't assume PASS for things you can't verify
3. **Present findings**: Show the full checklist with status
4. **User decides**: The verdict is a recommendation — the user makes the final call
5. **Get approval**: "May I write this gate check report to production/gate-checks/?"
6. **Never auto-fix**: If required artifacts are missing, report the FAIL verdict and
   name the skill to run (e.g. "run `/test-setup`"). Do NOT create missing files or
   re-run the gate automatically. Creating files to manufacture a PASS defeats the
   gate's purpose.

**Never** block a user from advancing — the verdict is advisory. Document the risks
and let the user decide whether to proceed despite concerns.

## 协作协议

本技能遵循协作设计原则：

1. **先扫描**：检查所有产出物和质量门禁
2. **询问未知项**：不要为无法验证的内容假设 PASS
3. **呈现发现**：展示带状态的完整清单
4. **用户决定**：判定是建议 —— 用户做最终决定
5. **获得批准**："我可以将此门禁检查报告写入 production/gate-checks/ 吗？"
6. **永远不要自动修复**：如必需产出物缺失，报告 FAIL 判定并
   指出要运行的技能（例如"运行 `/test-setup`"）。不要创建缺失文件或
   自动重新运行门禁。通过创建文件制造 PASS 会破坏
   门禁的目的。

**永远不要**阻止用户推进 —— 判定是建议性的。记录风险，
让用户决定是否在顾虑下继续推进。
