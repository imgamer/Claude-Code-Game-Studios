# Upgrading Claude Code Game Studios
# 升级 Claude Code Game Studios

This guide covers upgrading your existing game project repo from one version
of the template to the next.
本指南介绍如何将你现有的游戏项目仓库从一个版本的模板升级到下一个版本。

**Find your current version** in your git log:
**在 git log 中查找你的当前版本**：

```bash
git log --oneline | grep -i "release\|setup"
```

```bash
git log --oneline | grep -i "release\|setup"
```

Or check `README.md` for the version badge.
或查看 `README.md` 中的版本徽章。

---

## Table of Contents
## 目录

- [Upgrade Strategies](#upgrade-strategies)
- [升级策略](#upgrade-strategies)

- [v1.0.0-beta → v1.0](#v100-beta--v10)
- [v1.0.0-beta → v1.0](#v100-beta--v10)

- [v0.4.x → v1.0](#v04x--v10)
- [v0.4.x → v1.0](#v04x--v10)

- [v0.4.0 → v0.4.1](#v040--v041)
- [v0.4.0 → v0.4.1](#v040--v041)

- [v0.3.0 → v0.4.0](#v030--v040)
- [v0.3.0 → v0.4.0](#v030--v040)

- [v0.2.0 → v0.3.0](#v020--v030)
- [v0.2.0 → v0.3.0](#v020--v030)

- [v0.1.0 → v0.2.0](#v010--v020)
- [v0.1.0 → v0.2.0](#v010--v020)

---

## Upgrade Strategies
## 升级策略

There are three ways to pull in template updates. Choose based on how your
repo is set up.
有三种方式可以拉取模板更新。请根据你的仓库设置方式选择。

### Strategy A — Git Remote Merge (recommended)
### 策略 A —— Git 远程合并（推荐）

Best when: you cloned the template and have your own commits on top of it.
适用场景：你克隆了模板，并在其上有自己的提交。

```bash
# Add the template as a remote (one-time setup)
git remote add template https://github.com/Donchitos/Claude-Code-Game-Studios.git

# Fetch the new version
git fetch template main

# Merge into your branch
git merge template/main --allow-unrelated-histories
```

```bash
# Add the template as a remote (one-time setup)
git remote add template https://github.com/Donchitos/Claude-Code-Game-Studios.git

# Fetch the new version
git fetch template main

# Merge into your branch
git merge template/main --allow-unrelated-histories
```

Git will flag conflicts only in files that both the template *and* you have
changed. Resolve each one — your game content goes in, structural improvements
come along for the ride. Then commit the merge.
Git 只会在模板*和*你双方都改动过的文件中标记冲突。逐一解决这些冲突——你的游戏内容保留下来，结构性改进也一同带入。然后提交这次合并。

**Tip:** The files most likely to conflict are `CLAUDE.md` and
`.claude/docs/technical-preferences.md`, because you've filled them in with
your engine and project settings. Keep your content; accept the structural changes.
**提示：** 最可能发生冲突的文件是 `CLAUDE.md` 和
`.claude/docs/technical-preferences.md`，因为你已经填入了引擎和项目设置。
保留你的内容；接受结构性变更。

---

### Strategy B — Cherry-pick specific commits
### 策略 B —— 挑选特定提交

Best when: you only want one specific feature (e.g., just the new skill, not
the full update).
适用场景：你只想要某个特定功能（例如只要那个新技能，而不要完整更新）。

```bash
git remote add template https://github.com/Donchitos/Claude-Code-Game-Studios.git
git fetch template main

# Cherry-pick the specific commit(s) you want
git cherry-pick <commit-sha>
```

```bash
git remote add template https://github.com/Donchitos/Claude-Code-Game-Studios.git
git fetch template main

# Cherry-pick the specific commit(s) you want
git cherry-pick <commit-sha>
```

Commit SHAs for each version are listed in the version sections below.
每个版本的提交 SHA 列在下方的各版本章节中。

---

### Strategy C — Manual file copy
### 策略 C —— 手动复制文件

Best when: you didn't use git to set up the template (just downloaded a zip).
适用场景：你没有用 git 设置模板（只是下载了一个 zip）。

1. Download or clone the new version alongside your repo.
1. 在你的仓库旁边下载或克隆新版本。

2. Copy the files listed under **"Safe to overwrite"** directly.
2. 直接复制列在 **"可安全覆盖"** 下的文件。

3. For files under **"Merge carefully"**, open both versions side-by-side
   and manually merge the structural changes while keeping your content.
3. 对于 **"谨慎合并"** 下的文件，并排打开两个版本，
   手动合并结构性变更，同时保留你的内容。

---

## v0.4.1
## v0.4.1

**Released:** 2026-04-02
**发布日期：** 2026-04-02

**Key themes:** Art direction integration, asset specification pipeline
**关键主题：** 美术方向整合、资产规格管线

### What Changed
### 变更内容

| Category | Changes |
|----------|---------|
| **New skill** | `/art-bible` — guided section-by-section visual identity authoring (9 sections). Mandatory art-director Task spawn per section. AD-ART-BIBLE sign-off gate. Required at Technical Setup phase. |
| **New skill** | `/asset-spec` — per-asset visual spec and AI generation prompt generator. Reads art bible + GDD/level/character docs. Writes `design/assets/specs/` files and `design/assets/asset-manifest.md`. Full/lean/solo modes. |
| **New director gates (3)** | `AD-CONCEPT-VISUAL` (brainstorm Phase 4), `AD-ART-BIBLE` (art bible sign-off), `AD-PHASE-GATE` (gate-check panel) |
| **`/brainstorm` update** | Added `Task` to allowed-tools (was missing — blocked all director spawning). Art-director now spawns in parallel with creative-director after pillars lock. Visual Identity Anchor written to game-concept.md. |
| **`/gate-check` update** | Art-director added as 4th parallel director (AD-PHASE-GATE). Visual artifact checks: Visual Identity Anchor (Concept gate), art bible (Technical Setup gate), AD-ART-BIBLE sign-off + character visual profiles (Pre-Production gate). |
| **`/team-level` update** | Art-director added to Step 1 parallel spawn (visual direction before layout). Level-designer now receives art-director targets as explicit constraints. Step 4 art-director role corrected to production-concepts only. |
| **`/team-narrative` update** | Art-director added to Phase 2 parallel spawn (character visual design, environmental storytelling, cinematic tone). |
| **`/design-system` update** | Routing table expanded with art-director + technical-artist for Combat, UI, Dialogue, Animation/VFX, Character categories. Visual/Audio section now mandatory (with art-director Task spawn) for 7 system categories. |
| **`workflow-catalog.yaml`** | `/art-bible` added to Technical Setup (required). `/asset-spec` added to Pre-Production (optional, repeatable). |

| 类别 | 变更 |
|----------|---------|
| **新技能** | `/art-bible` —— 逐章节引导式视觉身份编写（9 个章节）。每章节强制生成 art-director Task。AD-ART-BIBLE 签字确认门禁。在 Technical Setup 阶段为必需。 |
| **新技能** | `/asset-spec` —— 按资产生成视觉规格与 AI 生成提示词。读取 art bible + GDD/关卡/角色文档。写入 `design/assets/specs/` 文件和 `design/assets/asset-manifest.md`。支持 full/lean/solo 三种模式。 |
| **新增总监门禁（3 个）** | `AD-CONCEPT-VISUAL`（brainstorm 第 4 阶段）、`AD-ART-BIBLE`（art bible 签字确认）、`AD-PHASE-GATE`（gate-check 评审组） |
| **`/brainstorm` 更新** | 将 `Task` 加入 allowed-tools（此前缺失 —— 导致所有总监生成被阻塞）。art-director 现在在核心支柱锁定后与 creative-director 并行生成。视觉身份锚点写入 game-concept.md。 |
| **`/gate-check` 更新** | art-director 作为第 4 个并行总监加入（AD-PHASE-GATE）。视觉产出物检查：视觉身份锚点（Concept 门禁）、art bible（Technical Setup 门禁）、AD-ART-BIBLE 签字确认 + 角色视觉档案（Pre-Production 门禁）。 |
| **`/team-level` 更新** | art-director 加入第 1 步并行生成（在布局之前确定视觉方向）。level-designer 现在将 art-director 的目标作为显式约束接收。第 4 步 art-director 角色修正为仅负责 production-concepts。 |
| **`/team-narrative` 更新** | art-director 加入第 2 阶段并行生成（角色视觉设计、环境叙事、电影基调）。 |
| **`/design-system` 更新** | 路由表为 Combat、UI、Dialogue、Animation/VFX、Character 类别扩展了 art-director + technical-artist。Visual/Audio 章节现在对 7 个系统类别为必需（伴随 art-director Task 生成）。 |
| **`workflow-catalog.yaml`** | `/art-bible` 加入 Technical Setup（必需）。`/asset-spec` 加入 Pre-Production（可选，可重复）。 |

### Files: Safe to Overwrite
### 文件：可安全覆盖

**New files to add:**
**需要新增的文件：**

```
.claude/skills/art-bible/SKILL.md
.claude/skills/asset-spec/SKILL.md
.claude/docs/director-gates.md
```

```
.claude/skills/art-bible/SKILL.md
.claude/skills/asset-spec/SKILL.md
.claude/docs/director-gates.md
```

**Existing files to overwrite (no user content):**
**需要覆盖的现有文件（无用户内容）：**

```
.claude/skills/brainstorm/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/team-level/SKILL.md
.claude/skills/team-narrative/SKILL.md
.claude/skills/design-system/SKILL.md
.claude/docs/workflow-catalog.yaml
README.md
UPGRADING.md
```

```
.claude/skills/brainstorm/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/team-level/SKILL.md
.claude/skills/team-narrative/SKILL.md
.claude/skills/design-system/SKILL.md
.claude/docs/workflow-catalog.yaml
README.md
UPGRADING.md
```

### Files: Merge Carefully
### 文件：谨慎合并

None — all changes are to infrastructure files with no user content.
无 —— 所有变更都针对不含用户内容的基础设施文件。

---

## v1.0.0-beta → v1.0
## v1.0.0-beta → v1.0

**Released:** 2026-05-13
**发布日期：** 2026-05-13

**Commit range:** `49d1e45..HEAD`
**提交范围：** `49d1e45..HEAD`

**Key themes:** New `/vertical-slice` gate, skill polish & bug fixes, contributor docs
**关键主题：** 新增 `/vertical-slice` 门禁、技能打磨与 bug 修复、贡献者文档

### What Changed
### 变更内容

| Category | Changes |
|----------|---------|
| **New skill** | `/vertical-slice` — Pre-Production gate that validates the full game loop with a production-quality end-to-end build before Production. Pairs with the overhauled `/prototype` (concept validation right after `/brainstorm`). |
| **New flow** | Entity inventory step in `/map-systems` — surfaces all named entities up front for cleaner downstream GDD authoring. |
| **UX polish** | Added missing `AskUserQuestion` widgets to 7 skills; comprehensive skill audit for consistency, prompts, and flow gaps; exposed `--review` flag in `argument-hints` for all `team-*` skills. |
| **Bug fixes** | `#21` log-agent hooks logged "unknown" `agent_type`; `#36` missing `allowed-tools` in `/architecture-decision` and `/story-done`; `#42` `rg --type gdscript` is invalid (now uses `--glob *.gd`); `#43` session-start preview showed oldest state instead of newest; `#45` duplicate `## 0.` heading and broken step numbering in `/architecture-decision`. |
| **Project docs** | Added `CONTRIBUTING.md` (framework contribution guidelines) and `SECURITY.md` (coordinated disclosure policy). |
| **Counts/refs** | Synced agent/skill/hook counts across `WORKFLOW-GUIDE.md`, `README.md`, and agent rosters; fixed stale agent names and skill model-tier fields. |

| 类别 | 变更 |
|----------|---------|
| **新技能** | `/vertical-slice` —— Pre-Production 门禁，在进入 Production 之前用一个生产质量的端到端构建验证完整游戏循环。与全面翻新的 `/prototype`（在 `/brainstorm` 之后立即进行概念验证）配对使用。 |
| **新流程** | `/map-systems` 中新增实体清单步骤 —— 提前暴露所有命名实体，让下游 GDD 编写更干净。 |
| **UX 打磨** | 为 7 个技能补充缺失的 `AskUserQuestion` 控件；对技能进行一致性、提示词和流程缺口的全面审计；为所有 `team-*` 技能在 `argument-hints` 中暴露 `--review` 标志。 |
| **Bug 修复** | `#21` log-agent 钩子记录的 `agent_type` 为 "unknown"；`#36` `/architecture-decision` 和 `/story-done` 缺失 `allowed-tools`；`#42` `rg --type gdscript` 无效（现改用 `--glob *.gd`）；`#43` session-start 预览显示的是最旧状态而非最新；`#45` `/architecture-decision` 中 `## 0.` 标题重复且步骤编号损坏。 |
| **项目文档** | 新增 `CONTRIBUTING.md`（框架贡献指南）和 `SECURITY.md`（协调披露策略）。 |
| **计数/引用** | 在 `WORKFLOW-GUIDE.md`、`README.md` 和智能体名册之间同步智能体/技能/钩子计数；修复过时的智能体名称和技能模型层级字段。 |

---

### Files: Safe to Overwrite
### 文件：可安全覆盖

**New files to add:**
**需要新增的文件：**

```
.claude/skills/vertical-slice/SKILL.md
CONTRIBUTING.md
SECURITY.md
```

```
.claude/skills/vertical-slice/SKILL.md
CONTRIBUTING.md
SECURITY.md
```

**Existing files to overwrite (no user content):**
**需要覆盖的现有文件（无用户内容）：**

- All files under `.claude/skills/` modified in the commit range (skill audit + AskUserQuestion widgets + `--review` argument-hints)
- All files under `.claude/skills/` modified in the commit range (skill audit + AskUserQuestion widgets + `--review` argument-hints)

- `.claude/hooks/log-agent.sh` (fix #21)
- `.claude/hooks/log-agent.sh`（修复 #21）

- `README.md`, `docs/WORKFLOW-GUIDE.md`, `docs/examples/skill-flow-diagrams.md`
- `README.md`、`docs/WORKFLOW-GUIDE.md`、`docs/examples/skill-flow-diagrams.md`

- `UPGRADING.md`
- `UPGRADING.md`

---

### Files: Merge Carefully
### 文件：谨慎合并

None — all changes are to infrastructure files with no user content.
无 —— 所有变更都针对不含用户内容的基础设施文件。

---

## v0.4.x → v1.0
## v0.4.x → v1.0

**Released:** 2026-03-29
**发布日期：** 2026-03-29

**Commit range:** `6c041ac..HEAD`
**提交范围：** `6c041ac..HEAD`

**Key themes:** Director gates system, gate intensity modes, Godot C# specialist
**关键主题：** 总监门禁系统、门禁强度模式、Godot C# 专家

### What Changed
### 变更内容

| Category | Changes |
|----------|---------|
| **New system** | Director gates — named review checkpoints shared across all workflow skills. Defined in `.claude/docs/director-gates.md` |
| **New feature** | Gate intensity modes: `full` (all director gates), `lean` (phase gates only), `solo` (no directors). Set globally via `production/review-mode.txt` during `/start`, or override per-run with `--review [mode]` on any gate-using skill |
| **New agent** | `godot-csharp-specialist` — C# code quality in Godot 4 projects |
| **Skill updates (13)** | All gate-using skills now parse `--review [full\|lean\|solo]` and include it in their argument-hint: `brainstorm`, `map-systems`, `design-system`, `architecture-decision`, `create-architecture`, `create-epics`, `create-stories`, `sprint-plan`, `milestone-review`, `playtest-report`, `prototype`, `story-done`, `gate-check` |
| **`/start` update** | Added Phase 3b — sets review mode during onboarding, writes `production/review-mode.txt` |
| **`/setup-engine` update** | Language selection step for Godot (GDScript vs C#) |
| **Docs** | `director-gates.md` — full gate catalog; `WORKFLOW-GUIDE.md` — Director Review Modes section; `README.md` — review intensity customization |

| 类别 | 变更 |
|----------|---------|
| **新系统** | 总监门禁 —— 在所有工作流技能间共享的命名评审检查点。定义于 `.claude/docs/director-gates.md` |
| **新功能** | 门禁强度模式：`full`（所有总监门禁）、`lean`（仅阶段门禁）、`solo`（无总监）。在 `/start` 期间通过 `production/review-mode.txt` 全局设置，或在任何使用门禁的技能上用 `--review [mode]` 进行单次运行覆盖 |
| **新智能体** | `godot-csharp-specialist` —— 在 Godot 4 项目中保障 C# 代码质量 |
| **技能更新（13 个）** | 所有使用门禁的技能现在都会解析 `--review [full\|lean\|solo]` 并将其包含在 argument-hint 中：`brainstorm`、`map-systems`、`design-system`、`architecture-decision`、`create-architecture`、`create-epics`、`create-stories`、`sprint-plan`、`milestone-review`、`playtest-report`、`prototype`、`story-done`、`gate-check` |
| **`/start` 更新** | 新增第 3b 阶段 —— 在入门期间设置评审模式，写入 `production/review-mode.txt` |
| **`/setup-engine` 更新** | 为 Godot 增加语言选择步骤（GDScript vs C#） |
| **文档** | `director-gates.md` —— 完整门禁目录；`WORKFLOW-GUIDE.md` —— Director Review Modes 章节；`README.md` —— 评审强度自定义 |

---

### Files: Safe to Overwrite
### 文件：可安全覆盖

**New files to add:**
**需要新增的文件：**

```
.claude/agents/godot-csharp-specialist.md
.claude/docs/director-gates.md
```

```
.claude/agents/godot-csharp-specialist.md
.claude/docs/director-gates.md
```

**Existing files to overwrite (no user content):**
**需要覆盖的现有文件（无用户内容）：**

```
.claude/skills/brainstorm/SKILL.md
.claude/skills/map-systems/SKILL.md
.claude/skills/design-system/SKILL.md
.claude/skills/architecture-decision/SKILL.md
.claude/skills/create-architecture/SKILL.md
.claude/skills/create-epics/SKILL.md
.claude/skills/create-stories/SKILL.md
.claude/skills/sprint-plan/SKILL.md
.claude/skills/milestone-review/SKILL.md
.claude/skills/playtest-report/SKILL.md
.claude/skills/prototype/SKILL.md
.claude/skills/story-done/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/start/SKILL.md
.claude/skills/quick-design/SKILL.md
.claude/skills/setup-engine/SKILL.md
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

```
.claude/skills/brainstorm/SKILL.md
.claude/skills/map-systems/SKILL.md
.claude/skills/design-system/SKILL.md
.claude/skills/architecture-decision/SKILL.md
.claude/skills/create-architecture/SKILL.md
.claude/skills/create-epics/SKILL.md
.claude/skills/create-stories/SKILL.md
.claude/skills/sprint-plan/SKILL.md
.claude/skills/milestone-review/SKILL.md
.claude/skills/playtest-report/SKILL.md
.claude/skills/prototype/SKILL.md
.claude/skills/story-done/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/start/SKILL.md
.claude/skills/quick-design/SKILL.md
.claude/skills/setup-engine/SKILL.md
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

---

### Files: Merge Carefully
### 文件：谨慎合并

No files require manual merging in this release. All changes are to infrastructure files with no user content.
本次发布中无需手动合并的文件。所有变更都针对不含用户内容的基础设施文件。

---

### New Features
### 新功能

#### Director Gates System
#### 总监门禁系统

All major workflow skills now reference named gate checkpoints defined in
`.claude/docs/director-gates.md`. Gates are identified by domain prefix and name
(e.g., `CD-CONCEPT`, `TD-ARCHITECTURE`, `LP-CODE-REVIEW`). Each gate defines
which director to spawn, what inputs to pass, what verdicts mean, and how
lean/solo modes affect it.
所有主要工作流技能现在都引用定义在
`.claude/docs/director-gates.md` 中的命名门禁检查点。门禁通过领域前缀和名称来标识
（例如 `CD-CONCEPT`、`TD-ARCHITECTURE`、`LP-CODE-REVIEW`）。每个门禁都定义了
要生成哪个总监、传入什么输入、各项裁决的含义，以及 lean/solo 模式如何影响它。

Skills spawn gates using `Task` with the gate ID and documented inputs, rather
than embedding director prompts inline. This keeps skill bodies clean and makes
gate behavior consistent across all workflow phases.
技能使用 `Task` 并传入门禁 ID 和已文档化的输入来生成门禁，而不是
将总监提示词内嵌其中。这让技能主体保持简洁，并使门禁行为在所有工作流阶段保持一致。

#### Gate Intensity Modes
#### 门禁强度模式

Three modes let you control how much director review you get:
三种模式让你控制获得多少总监评审：

- **`full`** (default) — all director gates run at every review checkpoint
- **`full`**（默认）—— 所有总监门禁在每个评审检查点都运行

- **`lean`** — per-skill director reviews are skipped; phase gates at `/gate-check` still run
- **`lean`** —— 跳过按技能进行的总监评审；`/gate-check` 上的阶段门禁仍会运行

- **`solo`** — no director gates anywhere; `/gate-check` checks artifact existence only
- **`solo`** —— 任何地方都没有总监门禁；`/gate-check` 仅检查产出物是否存在

Set globally during `/start` (writes `production/review-mode.txt`). Override any
individual run with `--review [mode]` on any gate-using skill:
在 `/start` 期间全局设置（写入 `production/review-mode.txt`）。在任何使用门禁的技能上
通过 `--review [mode]` 覆盖单次运行：

```
/design-system combat --review lean
/gate-check concept --review full
/brainstorm my-game-idea --review solo
```

```
/design-system combat --review lean
/gate-check concept --review full
/brainstorm my-game-idea --review solo
```

---

### After Upgrading
### 升级之后

1. Run `/start` once to set your preferred review mode — or create `production/review-mode.txt` manually with `full`, `lean`, or `solo`.
1. 运行一次 `/start` 设置你偏好的评审模式 —— 或手动创建 `production/review-mode.txt`，填入 `full`、`lean` 或 `solo`。

2. If you're mid-project, review `.claude/docs/director-gates.md` to understand which gates apply to your current phase.
2. 如果你处于项目中期，请查阅 `.claude/docs/director-gates.md` 以了解哪些门禁适用于你当前所处的阶段。

3. Run `/skill-test static all` to verify all skills pass structural checks.
3. 运行 `/skill-test static all` 以验证所有技能通过结构性检查。

---

## v0.4.0 → v0.4.1
## v0.4.0 → v0.4.1

**Released:** 2026-03-26
**发布日期：** 2026-03-26

**Commit range:** `04ed5d5..HEAD`
**提交范围：** `04ed5d5..HEAD`

**Key themes:** Genre-agnostic agents, new skills, skill fixes
**关键主题：** 与游戏类型无关的智能体、新技能、技能修复

### What Changed
### 变更内容

| Category | Changes |
|----------|---------|
| **New skills (1)** | `/consistency-check` — cross-GDD entity consistency scanner |
| **Skill fixes (all team-*)** | Added no-argument guards, formal `Verdict: COMPLETE / BLOCKED` keywords, per-step AskUserQuestion gates, adjacent area dependency checks (team-level), ethics enforcement (team-live-ops), NO-GO path with Phase skip (team-release) |
| **Agent fixes (4)** | Genre-agnostic language in game-designer, systems-designer, economy-designer, live-ops-designer — removed RPG-specific terms |

| 类别 | 变更 |
|----------|---------|
| **新技能（1 个）** | `/consistency-check` —— 跨 GDD 实体一致性扫描器 |
| **技能修复（所有 team-*）** | 增加无参数守卫、正式的 `Verdict: COMPLETE / BLOCKED` 关键字、每步 AskUserQuestion 门禁、相邻区域依赖检查（team-level）、伦理执行（team-live-ops）、带阶段跳过的 NO-GO 路径（team-release） |
| **智能体修复（4 个）** | game-designer、systems-designer、economy-designer、live-ops-designer 中使用与类型无关的语言 —— 移除了 RPG 专有术语 |

---

### Files: Safe to Overwrite
### 文件：可安全覆盖

**New files to add:**
**需要新增的文件：**

```
.claude/skills/consistency-check/SKILL.md
```

```
.claude/skills/consistency-check/SKILL.md
```

**Existing files to overwrite (no user content):**
**需要覆盖的现有文件（无用户内容）：**

```
.claude/skills/team-combat/SKILL.md      ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-narrative/SKILL.md   ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-ui/SKILL.md          ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-release/SKILL.md     ← no-arg guard, verdict keywords, NO-GO path
.claude/skills/team-polish/SKILL.md      ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-audio/SKILL.md       ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-level/SKILL.md       ← no-arg guard, verdict keywords, adjacent area checks
.claude/skills/team-live-ops/SKILL.md    ← no-arg guard, verdict keywords, ethics enforcement
.claude/skills/team-qa/SKILL.md          ← no-arg guard, verdict keywords, gate improvements
.claude/skills/map-systems/SKILL.md      ← verdict keywords
.claude/skills/create-epics/SKILL.md     ← "May I write" protocol fix, verdict keywords
.claude/skills/create-stories/SKILL.md   ← verdict keywords
.claude/agents/game-designer.md          ← genre-agnostic language
.claude/agents/systems-designer.md       ← genre-agnostic language
.claude/agents/economy-designer.md       ← genre-agnostic language
.claude/agents/live-ops-designer.md      ← genre-agnostic language
```

```
.claude/skills/team-combat/SKILL.md      ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-narrative/SKILL.md   ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-ui/SKILL.md          ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-release/SKILL.md     ← no-arg guard, verdict keywords, NO-GO path
.claude/skills/team-polish/SKILL.md      ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-audio/SKILL.md       ← no-arg guard, verdict keywords, gate improvements
.claude/skills/team-level/SKILL.md       ← no-arg guard, verdict keywords, adjacent area checks
.claude/skills/team-live-ops/SKILL.md    ← no-arg guard, verdict keywords, ethics enforcement
.claude/skills/team-qa/SKILL.md          ← no-arg guard, verdict keywords, gate improvements
.claude/skills/map-systems/SKILL.md      ← verdict keywords
.claude/skills/create-epics/SKILL.md     ← "May I write" protocol fix, verdict keywords
.claude/skills/create-stories/SKILL.md   ← verdict keywords
.claude/agents/game-designer.md          ← genre-agnostic language
.claude/agents/systems-designer.md       ← genre-agnostic language
.claude/agents/economy-designer.md       ← genre-agnostic language
.claude/agents/live-ops-designer.md      ← genre-agnostic language
```

---

### Files: Merge Carefully
### 文件：谨慎合并

No files require manual merging in this release. All changes are to infrastructure files with no user content.
本次发布中无需手动合并的文件。所有变更都针对不含用户内容的基础设施文件。

---

### After Upgrading
### 升级之后

1. Run `/skill-test catalog` to verify all skills are indexed.
1. 运行 `/skill-test catalog` 验证所有技能都已建立索引。

2. Run `/skill-test lint [skill-name]` after any skill edits to check structural compliance.
2. 在任何技能编辑之后运行 `/skill-test lint [skill-name]` 检查结构合规性。

3. If you've customized any team-* skills, review the updated versions — no-argument guard and `Verdict:` keywords are now required for all team-* skills.
3. 如果你定制过任何 team-* 技能，请查阅更新后的版本 —— 无参数守卫和 `Verdict:` 关键字现在对所有 team-* 技能都是必需的。

---

## v0.3.0 → v0.4.0
## v0.3.0 → v0.4.0

**Released:** 2026-03-21
**发布日期：** 2026-03-21

**Commit range:** `b1cad29..HEAD`
**提交范围：** `b1cad29..HEAD`

**Key themes:** Full UX/UI pipeline, complete story lifecycle, brownfield adoption, comprehensive QA/testing framework, pipeline integrity, 29 new skills
**关键主题：** 完整的 UX/UI 管线、完整的故事生命周期、棕地采用、全面的 QA/测试框架、管线完整性、29 个新技能

### What Changed
### 变更内容

| Category | Changes |
|----------|---------|
| **New skills (17)** | `/ux-design`, `/ux-review`, `/help`, `/quick-design`, `/review-all-gdds`, `/story-readiness`, `/story-done`, `/sprint-status`, `/adopt`, `/create-architecture`, `/create-control-manifest`, `/create-epics`, `/create-stories`, `/dev-story`, `/propagate-design-change`, `/content-audit`, `/architecture-review` |
| **New skills QA (12)** | `/qa-plan`, `/smoke-check`, `/soak-test`, `/regression-suite`, `/test-setup`, `/test-helpers`, `/test-evidence-review`, `/test-flakiness`, `/skill-test`, `/bug-triage`, `/team-live-ops`, `/team-qa` |
| **New hooks (4)** | `log-agent-stop.sh` — agent audit trail stop; `notify.sh` — Windows toast notifications; `post-compact.sh` — session recovery reminder after compaction; `validate-skill-change.sh` — advises `/skill-test` after skill edits |
| **New templates (8)** | `ux-spec.md`, `hud-design.md`, `accessibility-requirements.md`, `interaction-pattern-library.md`, `player-journey.md`, `difficulty-curve.md`, and 2 adoption plan templates |
| **New infrastructure** | `workflow-catalog.yaml` (7-phase pipeline, read by `/help`), `docs/architecture/tr-registry.yaml` (stable TR-IDs), `production/sprint-status.yaml` schema |
| **Skill updates** | `/gate-check` — 3 gates now require UX artifacts; Pre-Production gate requires vertical slice (HARD gate) |
| **Skill updates** | `/sprint-plan` — writes `sprint-status.yaml`; `/sprint-status` reads it |
| **Skill updates** | `/story-done` — 8-phase completion review, updates story file, surfaces next ready story |
| **Skill updates** | `/design-review` — removed architecture gap check (wrong stage) |
| **Skill updates** | `/team-ui` — full UX pipeline (ux-design → ux-review → team phases) |
| **Agent updates** | 14 specialist agents — `memory: project` added |
| **Agent updates** | `prototyper` — `isolation: worktree` (throwaway work in isolated git branch) |
| **Model routing** | Haiku/Sonnet/Opus tier assignments documented in coordination rules; skills declare their tier in frontmatter |
| **Directory CLAUDE.md** | Scaffolded `design/CLAUDE.md`, `src/CLAUDE.md`, `docs/CLAUDE.md` — path-scoped instructions for each directory |
| **Pipeline integrity** | TR-ID stability, manifest versioning, ADR status gates, TR-ID reference not quote |
| **GDD template** | `## Game Feel` section added (input responsiveness, animation targets, impact moments) |

| 类别 | 变更 |
|----------|---------|
| **新技能（17 个）** | `/ux-design`、`/ux-review`、`/help`、`/quick-design`、`/review-all-gdds`、`/story-readiness`、`/story-done`、`/sprint-status`、`/adopt`、`/create-architecture`、`/create-control-manifest`、`/create-epics`、`/create-stories`、`/dev-story`、`/propagate-design-change`、`/content-audit`、`/architecture-review` |
| **新技能 QA（12 个）** | `/qa-plan`、`/smoke-check`、`/soak-test`、`/regression-suite`、`/test-setup`、`/test-helpers`、`/test-evidence-review`、`/test-flakiness`、`/skill-test`、`/bug-triage`、`/team-live-ops`、`/team-qa` |
| **新钩子（4 个）** | `log-agent-stop.sh` —— 智能体审计追踪结束；`notify.sh` —— Windows 弹窗通知；`post-compact.sh` —— 压缩后会话恢复提醒；`validate-skill-change.sh` —— 在技能编辑后建议运行 `/skill-test` |
| **新模板（8 个）** | `ux-spec.md`、`hud-design.md`、`accessibility-requirements.md`、`interaction-pattern-library.md`、`player-journey.md`、`difficulty-curve.md`，以及 2 个采用计划模板 |
| **新基础设施** | `workflow-catalog.yaml`（7 阶段流水线，由 `/help` 读取）、`docs/architecture/tr-registry.yaml`（稳定的 TR-ID）、`production/sprint-status.yaml` schema |
| **技能更新** | `/gate-check` —— 3 个门禁现在要求 UX 产出物；Pre-Production 门禁要求垂直切片（HARD 硬门禁） |
| **技能更新** | `/sprint-plan` —— 写入 `sprint-status.yaml`；`/sprint-status` 读取它 |
| **技能更新** | `/story-done` —— 8 阶段完成评审，更新故事文件，浮现下一个就绪的故事 |
| **技能更新** | `/design-review` —— 移除架构缺口检查（阶段不对） |
| **技能更新** | `/team-ui` —— 完整 UX 管线（ux-design → ux-review → team 阶段） |
| **智能体更新** | 14 个专家智能体 —— 新增 `memory: project` |
| **智能体更新** | `prototyper` —— `isolation: worktree`（在隔离的 git 分支中处理一次性工作） |
| **模型路由** | Haiku/Sonnet/Opus 层级分配记录在协调规则中；技能在 frontmatter 中声明其层级 |
| **目录 CLAUDE.md** | 脚手架搭建了 `design/CLAUDE.md`、`src/CLAUDE.md`、`docs/CLAUDE.md` —— 每个目录的按路径限定范围的指令 |
| **管线完整性** | TR-ID 稳定性、清单版本化、ADR 状态门禁、TR-ID 引用而非引号 |
| **GDD 模板** | 新增 `## Game Feel` 章节（输入响应性、动画目标、冲击瞬间） |

---

### Files: Safe to Overwrite
### 文件：可安全覆盖

**New files to add:**
**需要新增的文件：**

```
.claude/skills/ux-design/SKILL.md
.claude/skills/ux-review/SKILL.md
.claude/skills/help/SKILL.md
.claude/skills/quick-design/SKILL.md
.claude/skills/review-all-gdds/SKILL.md
.claude/skills/story-readiness/SKILL.md
.claude/skills/story-done/SKILL.md
.claude/skills/sprint-status/SKILL.md
.claude/skills/adopt/SKILL.md
.claude/skills/create-architecture/SKILL.md
.claude/skills/create-control-manifest/SKILL.md
.claude/skills/create-epics/SKILL.md
.claude/skills/create-stories/SKILL.md
.claude/skills/dev-story/SKILL.md
.claude/skills/propagate-design-change/SKILL.md
.claude/skills/content-audit/SKILL.md
.claude/skills/architecture-review/SKILL.md
.claude/skills/qa-plan/SKILL.md
.claude/skills/smoke-check/SKILL.md
.claude/skills/soak-test/SKILL.md
.claude/skills/regression-suite/SKILL.md
.claude/skills/test-setup/SKILL.md
.claude/skills/test-helpers/SKILL.md
.claude/skills/test-evidence-review/SKILL.md
.claude/skills/test-flakiness/SKILL.md
.claude/skills/skill-test/SKILL.md
.claude/skills/bug-triage/SKILL.md
.claude/skills/team-live-ops/SKILL.md
.claude/skills/team-qa/SKILL.md
.claude/hooks/log-agent-stop.sh
.claude/hooks/notify.sh
.claude/hooks/post-compact.sh
.claude/hooks/validate-skill-change.sh
.claude/docs/workflow-catalog.yaml
.claude/docs/templates/ux-spec.md
.claude/docs/templates/hud-design.md
.claude/docs/templates/accessibility-requirements.md
.claude/docs/templates/interaction-pattern-library.md
.claude/docs/templates/player-journey.md
.claude/docs/templates/difficulty-curve.md
design/CLAUDE.md
src/CLAUDE.md
docs/CLAUDE.md
```

```
.claude/skills/ux-design/SKILL.md
.claude/skills/ux-review/SKILL.md
.claude/skills/help/SKILL.md
.claude/skills/quick-design/SKILL.md
.claude/skills/review-all-gdds/SKILL.md
.claude/skills/story-readiness/SKILL.md
.claude/skills/story-done/SKILL.md
.claude/skills/sprint-status/SKILL.md
.claude/skills/adopt/SKILL.md
.claude/skills/create-architecture/SKILL.md
.claude/skills/create-control-manifest/SKILL.md
.claude/skills/create-epics/SKILL.md
.claude/skills/create-stories/SKILL.md
.claude/skills/dev-story/SKILL.md
.claude/skills/propagate-design-change/SKILL.md
.claude/skills/content-audit/SKILL.md
.claude/skills/architecture-review/SKILL.md
.claude/skills/qa-plan/SKILL.md
.claude/skills/smoke-check/SKILL.md
.claude/skills/soak-test/SKILL.md
.claude/skills/regression-suite/SKILL.md
.claude/skills/test-setup/SKILL.md
.claude/skills/test-helpers/SKILL.md
.claude/skills/test-evidence-review/SKILL.md
.claude/skills/test-flakiness/SKILL.md
.claude/skills/skill-test/SKILL.md
.claude/skills/bug-triage/SKILL.md
.claude/skills/team-live-ops/SKILL.md
.claude/skills/team-qa/SKILL.md
.claude/hooks/log-agent-stop.sh
.claude/hooks/notify.sh
.claude/hooks/post-compact.sh
.claude/hooks/validate-skill-change.sh
.claude/docs/workflow-catalog.yaml
.claude/docs/templates/ux-spec.md
.claude/docs/templates/hud-design.md
.claude/docs/templates/accessibility-requirements.md
.claude/docs/templates/interaction-pattern-library.md
.claude/docs/templates/player-journey.md
.claude/docs/templates/difficulty-curve.md
design/CLAUDE.md
src/CLAUDE.md
docs/CLAUDE.md
```

**Existing files to overwrite (no user content):**
**需要覆盖的现有文件（无用户内容）：**

```
.claude/skills/gate-check/SKILL.md
.claude/skills/sprint-plan/SKILL.md
.claude/skills/sprint-status/SKILL.md
.claude/skills/design-review/SKILL.md
.claude/skills/team-ui/SKILL.md
.claude/skills/story-readiness/SKILL.md
.claude/skills/story-done/SKILL.md
.claude/docs/templates/game-design-document.md    ← adds Game Feel section
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

```
.claude/skills/gate-check/SKILL.md
.claude/skills/sprint-plan/SKILL.md
.claude/skills/sprint-status/SKILL.md
.claude/skills/design-review/SKILL.md
.claude/skills/team-ui/SKILL.md
.claude/skills/story-readiness/SKILL.md
.claude/skills/story-done/SKILL.md
.claude/docs/templates/game-design-document.md    ← adds Game Feel section
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

**Agent files to overwrite** (if you haven't written custom prompts into them):
**需要覆盖的智能体文件**（如果你没有在其中写入自定义提示词）：

```
.claude/agents/prototyper.md         ← adds isolation: worktree
.claude/agents/art-director.md       ← adds memory: project
.claude/agents/audio-director.md     ← adds memory: project
.claude/agents/economy-designer.md   ← adds memory: project
.claude/agents/game-designer.md      ← adds memory: project
.claude/agents/gameplay-programmer.md ← adds memory: project
.claude/agents/lead-programmer.md    ← adds memory: project
.claude/agents/level-designer.md     ← adds memory: project
.claude/agents/narrative-director.md ← adds memory: project
.claude/agents/systems-designer.md   ← adds memory: project
.claude/agents/technical-artist.md   ← adds memory: project
.claude/agents/ui-programmer.md      ← adds memory: project
.claude/agents/ux-designer.md        ← adds memory: project
.claude/agents/world-builder.md      ← adds memory: project
```

```
.claude/agents/prototyper.md         ← adds isolation: worktree
.claude/agents/art-director.md       ← adds memory: project
.claude/agents/audio-director.md     ← adds memory: project
.claude/agents/economy-designer.md   ← adds memory: project
.claude/agents/game-designer.md      ← adds memory: project
.claude/agents/gameplay-programmer.md ← adds memory: project
.claude/agents/lead-programmer.md    ← adds memory: project
.claude/agents/level-designer.md     ← adds memory: project
.claude/agents/narrative-director.md ← adds memory: project
.claude/agents/systems-designer.md   ← adds memory: project
.claude/agents/technical-artist.md   ← adds memory: project
.claude/agents/ui-programmer.md      ← adds memory: project
.claude/agents/ux-designer.md        ← adds memory: project
.claude/agents/world-builder.md      ← adds memory: project
```

---

### Files: Merge Carefully
### 文件：谨慎合并

#### `.claude/settings.json`
#### `.claude/settings.json`

Four new hooks are registered in this version. If you haven't customized `settings.json`, overwriting is safe. Otherwise, add the following hook entries manually:
本版本注册了四个新钩子。如果你没有定制过 `settings.json`，覆盖是安全的。否则请手动添加以下钩子条目：

- `log-agent-stop.sh` — `SubagentStop` event (agent audit trail stop)
- `log-agent-stop.sh` —— `SubagentStop` 事件（智能体审计追踪结束）

- `notify.sh` — `Notification` event (Windows toast notification)
- `notify.sh` —— `Notification` 事件（Windows 弹窗通知）

- `post-compact.sh` — `PostCompact` event (session recovery reminder)
- `post-compact.sh` —— `PostCompact` 事件（会话恢复提醒）

- `validate-skill-change.sh` — `PostToolUse` event filtered to `.claude/skills/` writes
- `validate-skill-change.sh` —— `PostToolUse` 事件，过滤 `.claude/skills/` 写入

#### Customized agent files
#### 已定制的智能体文件

If you've added project-specific knowledge to agent `.md` files, do a diff and manually add the `memory: project` line to the YAML frontmatter where appropriate. Creative and technical director agents intentionally keep `memory: user` — only specialist agents get `memory: project`.
如果你在智能体 `.md` 文件中添加过项目专属知识，请做 diff 并在合适的位置手动将 `memory: project` 行添加到 YAML frontmatter。创意和技术总监智能体有意保留 `memory: user` —— 只有专家智能体才有 `memory: project`。

---

### New Features
### 新功能

#### Complete Story Lifecycle
#### 完整的故事生命周期

Stories now have a formal lifecycle enforced by two skills:
故事现在有一个由两个技能强制执行的正式生命周期：

- **`/story-readiness`** — validates a story is implementation-ready before a developer picks it up. Checks Design (GDD req linked), Architecture (ADR accepted), Scope (criteria testable), and DoD (manifest version current). Verdict: READY / NEEDS WORK / BLOCKED.
- **`/story-readiness`** —— 在开发者接手之前验证故事是否已准备好实现。检查设计（关联了 GDD 需求）、架构（ADR 已接受）、范围（验收标准可测试）以及 DoD（清单版本为最新）。裁决：READY / NEEDS WORK / BLOCKED。

- **`/story-done`** — 8-phase completion review after implementation. Verifies each acceptance criterion, checks for GDD/ADR deviations, prompts code review, updates the story file to `Status: Complete`, and surfaces the next ready story.
- **`/story-done`** —— 实现之后的 8 阶段完成评审。验证每个验收标准，检查 GDD/ADR 偏差，提示进行代码评审，将故事文件更新为 `Status: Complete`，并浮现下一个就绪的故事。

Flow: `/story-readiness` → implement → `/story-done` → next story
流程：`/story-readiness` → 实现 → `/story-done` → 下一个故事

#### Full UX/UI Pipeline
#### 完整的 UX/UI 管线

- **`/ux-design`** — guided section-by-section UX spec authoring. Three modes: screen/flow, HUD, or interaction pattern library. Reads GDD UI requirements and player journey. Output to `design/ux/`.
- **`/ux-design`** —— 逐章节引导式 UX 规格编写。三种模式：屏幕/流程、HUD 或交互模式库。读取 GDD UI 需求和玩家旅程。输出到 `design/ux/`。

- **`/ux-review`** — validates UX specs against GDD alignment, accessibility tier, and pattern library. Verdict: APPROVED / NEEDS REVISION / MAJOR REVISION.
- **`/ux-review`** —— 根据 GDD 一致性、可访问性层级和模式库验证 UX 规格。裁决：APPROVED / NEEDS REVISION / MAJOR REVISION。

- **`/team-ui`** updated: Phase 1 now runs `/ux-design` + `/ux-review` as a hard gate before visual design begins.
- **`/team-ui`** 更新：第 1 阶段现在会在视觉设计开始之前，将 `/ux-design` + `/ux-review` 作为硬门禁运行。

#### Brownfield Adoption
#### 棕地采用

**`/adopt`** onboards existing projects to the template format. Audits internal structure of GDDs, ADRs, stories, systems-index, and infra. Classifies gaps (BLOCKING/HIGH/MEDIUM/LOW). Builds an ordered migration plan. Never regenerates existing artifacts — only fills gaps.
**`/adopt`** 将现有项目引入到模板格式中。审计 GDD、ADR、故事、systems-index 和基础设施的内部结构。将缺口分类（BLOCKING/HIGH/MEDIUM/LOW）。构建有序的迁移计划。从不重新生成已有产出物 —— 仅填补缺口。

Argument modes: `full | gdds | adrs | stories | infra`
参数模式：`full | gdds | adrs | stories | infra`

Also: `/design-system retrofit [path]` and `/architecture-decision retrofit [path]` detect existing files and add only missing sections.
另外：`/design-system retrofit [path]` 和 `/architecture-decision retrofit [path]` 会检测已有文件并只添加缺失的章节。

#### Sprint Tracking YAML
#### 冲刺跟踪 YAML

`production/sprint-status.yaml` is now the authoritative story tracking format:
`production/sprint-status.yaml` 现在是权威的故事跟踪格式：

- Written by `/sprint-plan` (initializes all stories) and `/story-done` (sets status to `done`)
- 由 `/sprint-plan`（初始化所有故事）和 `/story-done`（将状态设置为 `done`）写入

- Read by `/sprint-status` (fast snapshot) and `/help` (per-story status in production phase)
- 由 `/sprint-status`（快速快照）和 `/help`（制作阶段中每个故事的状态）读取

- Status values: `backlog | ready-for-dev | in-progress | review | done | blocked`
- 状态取值：`backlog | ready-for-dev | in-progress | review | done | blocked`

- Falls back gracefully to markdown scanning if file doesn't exist
- 如果文件不存在，则优雅降级为扫描 markdown

#### `/help` — Context-Aware Next Step
#### `/help` —— 上下文感知的下一步

`/help` reads your current stage and in-progress work, checks which artifacts are complete, and tells you exactly what to do next — one primary required step, plus optional opportunities. Distinct from `/start` (first-time only) and `/project-stage-detect` (full audit).
`/help` 会读取你当前所处的阶段和进行中的工作，检查哪些产出物已完成，并准确告诉你下一步该做什么 —— 一个主要必需步骤，加上可选的机会。与 `/start`（仅首次使用）和 `/project-stage-detect`（完整审计）有所区别。

#### Comprehensive QA and Testing Framework
#### 全面的 QA 和测试框架

Nine new QA/testing skills covering the full testing lifecycle:
九个新的 QA/测试技能，覆盖完整的测试生命周期：

- **`/test-setup`** — scaffolds the test framework and CI/CD pipeline for your engine
- **`/test-setup`** —— 为你的引擎搭建测试框架和 CI/CD 管线

- **`/test-helpers`** — generates engine-specific test helper libraries (GDUnit4, NUnit, etc.)
- **`/test-helpers`** —— 生成引擎专用的测试辅助库（GDUnit4、NUnit 等）

- **`/qa-plan`** — generates a QA test plan for a sprint or feature, classifying stories by test type
- **`/qa-plan`** —— 为某个冲刺或功能生成 QA 测试计划，按测试类型对故事进行分类

- **`/smoke-check`** — runs the critical path smoke test gate before QA hand-off
- **`/smoke-check`** —— 在 QA 交接之前运行关键路径冒烟测试门禁

- **`/soak-test`** — generates a soak test protocol for extended play sessions (stability, memory leaks)
- **`/soak-test`** —— 为长时间游玩会话生成浸泡测试协议（稳定性、内存泄漏）

- **`/regression-suite`** — maps test coverage to GDD critical paths, identifies fixed bugs lacking regression tests
- **`/regression-suite`** —— 将测试覆盖映射到 GDD 关键路径，识别缺乏回归测试的已修复 bug

- **`/test-evidence-review`** — quality review of test files and manual evidence documents
- **`/test-evidence-review`** —— 对测试文件和手工证据文档进行质量评审

- **`/test-flakiness`** — detects non-deterministic tests by reading CI run logs
- **`/test-flakiness`** —— 通过读取 CI 运行日志检测非确定性测试

- **`/skill-test`** — validates skill files for structural compliance and behavioral correctness (three modes: lint, spec, catalog)
- **`/skill-test`** —— 验证技能文件的结构合规性和行为正确性（三种模式：lint、spec、catalog）

Also new: **`/bug-triage`** re-evaluates all open bugs for priority, severity, and ownership.
新增的还有：**`/bug-triage`** 重新评估所有未关闭 bug 的优先级、严重性和归属。

#### Skill Validator (`/skill-test`)
#### 技能验证器（`/skill-test`）

`/skill-test` is a meta-skill for validating the harness itself. Run it after editing any skill file. Three modes:
`/skill-test` 是一个用于验证工具框架本身的元技能。在编辑任何技能文件后运行它。三种模式：

- `lint` — validates YAML frontmatter and required fields
- `lint` —— 验证 YAML frontmatter 和必填字段

- `spec [skill-name]` — runs behavioral spec tests against a specific skill
- `spec [skill-name]` —— 对特定技能运行行为规格测试

- `catalog` — checks that all skills in `.claude/skills/` are indexed in the catalog
- `catalog` —— 检查 `.claude/skills/` 中所有技能都已编入目录

The new `validate-skill-change.sh` hook reminds you to run `/skill-test` automatically when a skill file is modified.
新增的 `validate-skill-change.sh` 钩子会在技能文件被修改时自动提醒你运行 `/skill-test`。

#### Team Live-Ops and Team QA Orchestration
#### Team Live-Ops 与 Team QA 编排

- **`/team-live-ops`** — coordinates live-ops-designer + economy-designer + community-manager + analytics-engineer for post-launch content planning (seasonal events, battle pass, retention)
- **`/team-live-ops`** —— 协调 live-ops-designer + economy-designer + community-manager + analytics-engineer，用于上线后的内容规划（赛季活动、战斗通行证、留存）

- **`/team-qa`** — orchestrates qa-lead + qa-tester + gameplay-programmer + producer through a full QA cycle: strategy, execution, coverage, and sign-off
- **`/team-qa`** —— 编排 qa-lead + qa-tester + gameplay-programmer + producer 完成完整 QA 周期：策略、执行、覆盖和签字确认

#### Model Tier Routing
#### 模型层级路由

Skills are now explicitly assigned to Haiku, Sonnet, or Opus tiers based on task complexity. Read-only status checks use Haiku; complex multi-document synthesis uses Opus; everything else defaults to Sonnet. Tier assignments are documented in `.claude/docs/coordination-rules.md`.
技能现在会根据任务复杂度显式分配到 Haiku、Sonnet 或 Opus 层级。只读状态检查使用 Haiku；复杂的多文档综合使用 Opus；其他所有情况默认使用 Sonnet。层级分配记录在 `.claude/docs/coordination-rules.md` 中。

#### Directory CLAUDE.md Files
#### 目录级 CLAUDE.md 文件

Three new directory-scoped CLAUDE.md files (`design/`, `src/`, `docs/`) provide path-specific instructions to agents working in those directories. These load automatically when Claude Code reads files in that directory.
三个新的目录限定范围的 CLAUDE.md 文件（`design/`、`src/`、`docs/`）为在这些目录中工作的智能体提供路径专属指令。当 Claude Code 读取该目录中的文件时会自动加载它们。

---

### After Upgrading
### 升级之后

1. **Verify new hooks** are registered in `.claude/settings.json` — check for all four: `log-agent-stop.sh`, `notify.sh`, `post-compact.sh`, `validate-skill-change.sh`.
1. **验证新钩子**已在 `.claude/settings.json` 中注册 —— 检查全部四个：`log-agent-stop.sh`、`notify.sh`、`post-compact.sh`、`validate-skill-change.sh`。

2. **Test the audit trail** by spawning any subagent — both start and stop events should appear in `production/session-logs/`.
2. **测试审计追踪** —— 生成任意子智能体，开始和结束事件都应出现在 `production/session-logs/`。

3. **Generate sprint-status.yaml** if you're in active production:
3. 如果你处于活跃制作阶段，**生成 sprint-status.yaml**：

   ```
   /sprint-plan status
   ```

   ```
   /sprint-plan status
   ```

4. **Run `/adopt`** if you have existing GDDs or ADRs that predate this template version — it will identify which sections need to be added without overwriting your content.
4. 如果你已有早于本模板版本的 GDD 或 ADR，**运行 `/adopt`** —— 它会识别需要添加哪些章节，而不会覆盖你的内容。

5. **Validate your skills** after any skill edits with `/skill-test` — the new `validate-skill-change.sh` hook will automatically remind you to do this.
5. 在任何技能编辑之后用 `/skill-test` **验证你的技能** —— 新的 `validate-skill-change.sh` 钩子会自动提醒你这样做。

---

## v0.2.0 → v0.3.0
## v0.2.0 → v0.3.0

**Released:** 2026-03-09
**发布日期：** 2026-03-09

**Commit range:** `e289ce9..HEAD`
**提交范围：** `e289ce9..HEAD`

**Key themes:** `/design-system` GDD authoring, `/map-systems` rename, custom status line
**关键主题：** `/design-system` GDD 编写、`/map-systems` 重命名、自定义状态行

### Breaking Changes
### 破坏性变更

#### `/design-systems` renamed to `/map-systems`
#### `/design-systems` 重命名为 `/map-systems`

The `/design-systems` skill was renamed to `/map-systems` for clarity
(decomposing = *mapping*, not *designing*).
`/design-systems` 技能被重命名为 `/map-systems` 以提高清晰度
（分解 = *映射*，而非 *设计*）。

**Action required:** Update any documentation, notes, or scripts that invoke
`/design-systems`. The new invocation is `/map-systems`.
**需要执行的操作：** 更新任何调用
`/design-systems` 的文档、笔记或脚本。新的调用方式是 `/map-systems`。

### What Changed
### 变更内容

| Category | Changes |
|----------|---------|
| **New skills** | `/design-system` (guided GDD authoring, section-by-section) |
| **Renamed skills** | `/design-systems` → `/map-systems` (breaking rename) |
| **New files** | `.claude/statusline.sh`, `.claude/settings.json` statusline config |
| **Skill updates** | `/gate-check` — writes `production/stage.txt` on PASS, new phase definitions |
| **Skill updates** | `brainstorm`, `start`, `design-review`, `project-stage-detect`, `setup-engine` — cross-reference fixes |
| **Bug fixes** | `log-agent.sh`, `validate-commit.sh` — hook execution fixed |
| **Docs** | `UPGRADING.md` added, `README.md` updated, `WORKFLOW-GUIDE.md` updated |

| 类别 | 变更 |
|----------|---------|
| **新技能** | `/design-system`（逐章节引导式 GDD 编写） |
| **重命名的技能** | `/design-systems` → `/map-systems`（破坏性重命名） |
| **新文件** | `.claude/statusline.sh`、`.claude/settings.json` statusline 配置 |
| **技能更新** | `/gate-check` —— 在 PASS 时写入 `production/stage.txt`，新增阶段定义 |
| **技能更新** | `brainstorm`、`start`、`design-review`、`project-stage-detect`、`setup-engine` —— 交叉引用修复 |
| **Bug 修复** | `log-agent.sh`、`validate-commit.sh` —— 钩子执行修复 |
| **文档** | 新增 `UPGRADING.md`，更新 `README.md`，更新 `WORKFLOW-GUIDE.md` |

---

### Files: Safe to Overwrite
### 文件：可安全覆盖

**New files to add:**
**需要新增的文件：**

```
.claude/skills/design-system/SKILL.md
.claude/statusline.sh
```

```
.claude/skills/design-system/SKILL.md
.claude/statusline.sh
```

**Existing files to overwrite (no user content):**
**需要覆盖的现有文件（无用户内容）：**

```
.claude/skills/map-systems/SKILL.md      ← was design-systems/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/brainstorm/SKILL.md
.claude/skills/start/SKILL.md
.claude/skills/design-review/SKILL.md
.claude/skills/project-stage-detect/SKILL.md
.claude/skills/setup-engine/SKILL.md
.claude/hooks/log-agent.sh
.claude/hooks/validate-commit.sh
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

```
.claude/skills/map-systems/SKILL.md      ← was design-systems/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/brainstorm/SKILL.md
.claude/skills/start/SKILL.md
.claude/skills/design-review/SKILL.md
.claude/skills/project-stage-detect/SKILL.md
.claude/skills/setup-engine/SKILL.md
.claude/hooks/log-agent.sh
.claude/hooks/validate-commit.sh
README.md
docs/WORKFLOW-GUIDE.md
UPGRADING.md
```

**Delete (replaced by rename):**
**删除（由重命名替代）：**

```
.claude/skills/design-systems/   ← entire directory; replaced by map-systems/
```

```
.claude/skills/design-systems/   ← entire directory; replaced by map-systems/
```

---

### Files: Merge Carefully
### 文件：谨慎合并

#### `.claude/settings.json`
#### `.claude/settings.json`

The new version adds a `statusLine` configuration block pointing to
`.claude/statusline.sh`. If you haven't customized `settings.json`, overwriting
is safe. Otherwise, add this block manually:
新版本添加了一个 `statusLine` 配置块，指向
`.claude/statusline.sh`。如果你没有定制过 `settings.json`，覆盖是安全的。否则请手动添加此块：

```json
"statusLine": {
  "script": ".claude/statusline.sh"
}
```

```json
"statusLine": {
  "script": ".claude/statusline.sh"
}
```

---

### New Features
### 新功能

#### Custom Status Line
#### 自定义状态行

`.claude/statusline.sh` displays a 7-stage production pipeline breadcrumb in
the terminal status line:
`.claude/statusline.sh` 在终端状态行中显示一个 7 阶段制作流水线面包屑：

```
ctx: 42% | claude-sonnet-4-6 | Systems Design
```

```
ctx: 42% | claude-sonnet-4-6 | Systems Design
```

In Production/Polish/Release stages, it also shows the active Epic/Feature/Task
from `production/session-state/active.md` if a `<!-- STATUS -->` block is present:
在 Production/Polish/Release 阶段，如果 `production/session-state/active.md` 中存在 `<!-- STATUS -->` 块，它还会显示当前活动的 Epic/Feature/Task：

```
ctx: 42% | claude-sonnet-4-6 | Production | Combat System > Melee Combat > Hitboxes
```

```
ctx: 42% | claude-sonnet-4-6 | Production | Combat System > Melee Combat > Hitboxes
```

The current stage is auto-detected from project artifacts, or can be pinned by
writing a stage name to `production/stage.txt`.
当前阶段从项目产出物中自动检测，也可以通过将阶段名称写入 `production/stage.txt` 来固定。

#### `/gate-check` Stage Advancement
#### `/gate-check` 阶段推进

When a gate PASS verdict is confirmed, `/gate-check` now writes the new stage
name to `production/stage.txt`. This immediately updates the status line for all
future sessions without requiring manual file edits.
当某个门禁的 PASS 裁决被确认时，`/gate-check` 现在会将新的阶段名称
写入 `production/stage.txt`。这会立即为所有后续会话更新状态行，无需手动编辑文件。

---

### After Upgrading
### 升级之后

1. **Delete the old skill directory:**
1. **删除旧技能目录：**

   ```bash
   rm -rf .claude/skills/design-systems/
   ```

   ```bash
   rm -rf .claude/skills/design-systems/
   ```

2. **Test the status line** by starting a Claude Code session — you should see
   the stage breadcrumb in the terminal footer.
2. **测试状态行** —— 启动一个 Claude Code 会话，你应能在终端底部看到阶段面包屑。

3. **Verify hook execution** still works:
3. **验证钩子执行**仍然正常：

   ```bash
   bash .claude/hooks/log-agent.sh '{}' '{}'
   bash .claude/hooks/validate-commit.sh '{}' '{}'
   ```

   ```bash
   bash .claude/hooks/log-agent.sh '{}' '{}'
   bash .claude/hooks/validate-commit.sh '{}' '{}'
   ```

---

## v0.1.0 → v0.2.0
## v0.1.0 → v0.2.0

**Released:** 2026-02-21
**发布日期：** 2026-02-21

**Commit range:** `ad540fe..e289ce9`
**提交范围：** `ad540fe..e289ce9`

**Key themes:** Context Resilience, AskUserQuestion integration, `/map-systems` skill
**关键主题：** 上下文韧性、AskUserQuestion 集成、`/map-systems` 技能

### What Changed
### 变更内容

| Category | Changes |
|----------|---------|
| **New skills** | `/start` (onboarding), `/map-systems` (systems decomposition), `/design-system` (guided GDD authoring) |
| **New hooks** | `session-start.sh` (recovery), `detect-gaps.sh` (gap detection) |
| **New templates** | `systems-index.md`, 3 collaborative-protocol templates |
| **Context management** | Major rewrite — file-backed state strategy added |
| **Agent updates** | 14 design/creative agents — AskUserQuestion integration |
| **Skill updates** | All 7 `team-*` skills + `brainstorm` — AskUserQuestion at phase transitions |
| **CLAUDE.md** | Slimmed from ~159 to ~60 lines; 5 doc imports instead of 10 |
| **Hook updates** | All 8 hooks — Windows compatibility fixes, new features |
| **Docs removed** | `docs/IMPROVEMENTS-PROPOSAL.md`, `docs/MULTI-STAGE-DOCUMENT-WORKFLOW.md` |

| 类别 | 变更 |
|----------|---------|
| **新技能** | `/start`（入门）、`/map-systems`（系统分解）、`/design-system`（引导式 GDD 编写） |
| **新钩子** | `session-start.sh`（恢复）、`detect-gaps.sh`（缺口检测） |
| **新模板** | `systems-index.md`、3 个协作协议模板 |
| **上下文管理** | 大规模重写 —— 新增文件支撑的状态策略 |
| **智能体更新** | 14 个设计/创意智能体 —— AskUserQuestion 集成 |
| **技能更新** | 所有 7 个 `team-*` 技能 + `brainstorm` —— 在阶段转换处使用 AskUserQuestion |
| **CLAUDE.md** | 从约 159 行精简到约 60 行；5 个文档导入替代 10 个 |
| **钩子更新** | 所有 8 个钩子 —— Windows 兼容性修复、新功能 |
| **删除的文档** | `docs/IMPROVEMENTS-PROPOSAL.md`、`docs/MULTI-STAGE-DOCUMENT-WORKFLOW.md` |

---

### Files: Safe to Overwrite
### 文件：可安全覆盖

These are pure infrastructure — you have not customized them. Copy the new
versions directly with no risk to your project content.
这些都是纯基础设施 —— 你没有定制过它们。直接复制新版本，对你的项目内容没有任何风险。

**New files to add:**
**需要新增的文件：**

```
.claude/skills/start/SKILL.md
.claude/skills/map-systems/SKILL.md
.claude/skills/design-system/SKILL.md
.claude/docs/templates/systems-index.md
.claude/docs/templates/collaborative-protocols/design-agent-protocol.md
.claude/docs/templates/collaborative-protocols/implementation-agent-protocol.md
.claude/docs/templates/collaborative-protocols/leadership-agent-protocol.md
.claude/hooks/detect-gaps.sh
.claude/hooks/session-start.sh
production/session-state/.gitkeep
docs/examples/README.md
.github/ISSUE_TEMPLATE/bug_report.md
.github/ISSUE_TEMPLATE/feature_request.md
.github/PULL_REQUEST_TEMPLATE.md
```

```
.claude/skills/start/SKILL.md
.claude/skills/map-systems/SKILL.md
.claude/skills/design-system/SKILL.md
.claude/docs/templates/systems-index.md
.claude/docs/templates/collaborative-protocols/design-agent-protocol.md
.claude/docs/templates/collaborative-protocols/implementation-agent-protocol.md
.claude/docs/templates/collaborative-protocols/leadership-agent-protocol.md
.claude/hooks/detect-gaps.sh
.claude/hooks/session-start.sh
production/session-state/.gitkeep
docs/examples/README.md
.github/ISSUE_TEMPLATE/bug_report.md
.github/ISSUE_TEMPLATE/feature_request.md
.github/PULL_REQUEST_TEMPLATE.md
```

**Existing files to overwrite (no user content):**
**需要覆盖的现有文件（无用户内容）：**

```
.claude/skills/brainstorm/SKILL.md
.claude/skills/design-review/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/project-stage-detect/SKILL.md
.claude/skills/setup-engine/SKILL.md
.claude/skills/team-audio/SKILL.md
.claude/skills/team-combat/SKILL.md
.claude/skills/team-level/SKILL.md
.claude/skills/team-narrative/SKILL.md
.claude/skills/team-polish/SKILL.md
.claude/skills/team-release/SKILL.md
.claude/skills/team-ui/SKILL.md
.claude/hooks/log-agent.sh
.claude/hooks/pre-compact.sh
.claude/hooks/session-stop.sh
.claude/hooks/validate-assets.sh
.claude/hooks/validate-commit.sh
.claude/hooks/validate-push.sh
.claude/rules/design-docs.md
.claude/docs/hooks-reference.md
.claude/docs/skills-reference.md
.claude/docs/quick-start.md
.claude/docs/directory-structure.md
.claude/docs/context-management.md
docs/COLLABORATIVE-DESIGN-PRINCIPLE.md
docs/WORKFLOW-GUIDE.md
README.md
```

```
.claude/skills/brainstorm/SKILL.md
.claude/skills/design-review/SKILL.md
.claude/skills/gate-check/SKILL.md
.claude/skills/project-stage-detect/SKILL.md
.claude/skills/setup-engine/SKILL.md
.claude/skills/team-audio/SKILL.md
.claude/skills/team-combat/SKILL.md
.claude/skills/team-level/SKILL.md
.claude/skills/team-narrative/SKILL.md
.claude/skills/team-polish/SKILL.md
.claude/skills/team-release/SKILL.md
.claude/skills/team-ui/SKILL.md
.claude/hooks/log-agent.sh
.claude/hooks/pre-compact.sh
.claude/hooks/session-stop.sh
.claude/hooks/validate-assets.sh
.claude/hooks/validate-commit.sh
.claude/hooks/validate-push.sh
.claude/rules/design-docs.md
.claude/docs/hooks-reference.md
.claude/docs/skills-reference.md
.claude/docs/quick-start.md
.claude/docs/directory-structure.md
.claude/docs/context-management.md
docs/COLLABORATIVE-DESIGN-PRINCIPLE.md
docs/WORKFLOW-GUIDE.md
README.md
```

**Agent files to overwrite** (if you haven't written custom prompts into them):
**需要覆盖的智能体文件**（如果你没有在其中写入自定义提示词）：

```
.claude/agents/art-director.md
.claude/agents/audio-director.md
.claude/agents/creative-director.md
.claude/agents/economy-designer.md
.claude/agents/game-designer.md
.claude/agents/level-designer.md
.claude/agents/live-ops-designer.md
.claude/agents/narrative-director.md
.claude/agents/producer.md
.claude/agents/systems-designer.md
.claude/agents/technical-director.md
.claude/agents/ux-designer.md
.claude/agents/world-builder.md
.claude/agents/writer.md
```

```
.claude/agents/art-director.md
.claude/agents/audio-director.md
.claude/agents/creative-director.md
.claude/agents/economy-designer.md
.claude/agents/game-designer.md
.claude/agents/level-designer.md
.claude/agents/live-ops-designer.md
.claude/agents/narrative-director.md
.claude/agents/producer.md
.claude/agents/systems-designer.md
.claude/agents/technical-director.md
.claude/agents/ux-designer.md
.claude/agents/world-builder.md
.claude/agents/writer.md
```

If you *have* customized agent prompts, see "Merge carefully" below.
如果你*已经*定制了智能体提示词，请参见下方的"谨慎合并"。

---

### Files: Merge Carefully
### 文件：谨慎合并

These files contain both template structure and your project-specific content.
Do **not** overwrite them — merge the changes manually.
这些文件既包含模板结构，也包含你的项目专属内容。
**不要**覆盖它们 —— 手动合并变更。

#### `CLAUDE.md`
#### `CLAUDE.md`

The template version was slimmed from ~159 lines to ~60 lines. The key
structural change: 5 doc imports were removed because they're auto-loaded
by Claude Code anyway (agent-roster, skills-reference, hooks-reference,
rules-reference, review-workflow).
模板版本从约 159 行精简到约 60 行。关键的结构性变更：移除了 5 个文档导入，因为它们无论如何都会被 Claude Code 自动加载（agent-roster、skills-reference、hooks-reference、rules-reference、review-workflow）。

**What to keep from your version:**
**从你的版本中保留：**

- The `## Technology Stack` section (your engine/language choices)
- `## Technology Stack` 章节（你的引擎/语言选择）

- Any project-specific additions you made
- 你添加的任何项目专属内容

**What to adopt from the new version:**
**从新版本中采纳：**

- Slimmer imports list (drop the 5 redundant `@` imports if present)
- 更精简的导入列表（如有则去掉 5 个冗余的 `@` 导入）

- Updated collaboration protocol wording
- 更新后的协作协议措辞

#### `.claude/docs/technical-preferences.md`
#### `.claude/docs/technical-preferences.md`

If you ran `/setup-engine`, this file has your engine config, naming
conventions, and performance budgets. Keep all of it. The template version
is just the empty placeholder.
如果你运行过 `/setup-engine`，此文件中有你的引擎配置、命名规范
和性能预算。请保留全部内容。模板版本只是空占位符。

#### `.claude/docs/templates/game-concept.md`
#### `.claude/docs/templates/game-concept.md`

Minor structural update — a `## Next Steps` section was added pointing to
`/map-systems`. Add that section to your copy if you want the updated
guidance, but it's not required.
小幅结构性更新 —— 新增了一个指向
`/map-systems` 的 `## Next Steps` 章节。如果你想要更新后的
指导，可将该章节添加到你的副本中，但这不是必需的。

#### `.claude/settings.json`
#### `.claude/settings.json`

Check whether the new version adds any permission rules you want. The change
was minor (schema update). If you haven't customized your `settings.json`,
overwriting is safe.
检查新版本是否添加了你想要的任何权限规则。变更很小
（schema 更新）。如果你没有定制过 `settings.json`，
覆盖是安全的。

#### Customized agent files
#### 已定制的智能体文件

If you've added project-specific knowledge or custom behavior to any agent
`.md` file, do a diff and manually add the new AskUserQuestion integration
sections rather than overwriting. The change in each agent is a standardized
collaborative protocol block at the end of the system prompt.
如果你在任何智能体
`.md` 文件中添加了项目专属知识或自定义行为，请做 diff 并手动添加新的 AskUserQuestion 集成
章节，而不是覆盖。每个智能体中的变更是系统提示词末尾
一个标准化的协作协议块。

---

### Files: Delete
### 文件：删除

These files were removed in v0.2.0. If present in your repo, you can safely
delete them — they're replaced by better-organized alternatives.
这些文件在 v0.2.0 中被移除。如果你的仓库中存在这些文件，可以安全删除 —— 它们已被组织得更好的替代方案取代。

```
docs/IMPROVEMENTS-PROPOSAL.md      → superseded by WORKFLOW-GUIDE.md
docs/MULTI-STAGE-DOCUMENT-WORKFLOW.md → content merged into context-management.md
```

```
docs/IMPROVEMENTS-PROPOSAL.md      → superseded by WORKFLOW-GUIDE.md
docs/MULTI-STAGE-DOCUMENT-WORKFLOW.md → content merged into context-management.md
```

---

### After Upgrading
### 升级之后

1. **Run `/project-stage-detect`** to verify the system reads your project
   correctly with the new detection logic.
1. **运行 `/project-stage-detect`** 验证系统能用新的检测逻辑正确读取你的项目。

2. **Run `/start`** once if you haven't used it — it now correctly identifies
   your stage and skips onboarding steps you've already done.
2. 如果你没用过，**运行一次 `/start`** —— 它现在能正确识别你的阶段，并跳过你已完成的入门步骤。

3. **Check `production/session-state/`** exists and is gitignored:
3. **检查 `production/session-state/`** 是否存在并已被 gitignore：

   ```bash
   ls production/session-state/
   cat .gitignore | grep session-state
   ```

   ```bash
   ls production/session-state/
   cat .gitignore | grep session-state
   ```

4. **Test hook execution** — if you're on Windows, verify the new hooks run
   without errors in Git Bash:
4. **测试钩子执行** —— 如果你在 Windows 上，请验证新钩子在 Git Bash 中
   无错误运行：

   ```bash
   bash .claude/hooks/detect-gaps.sh '{}' '{}'
   bash .claude/hooks/session-start.sh '{}' '{}'
   ```

   ```bash
   bash .claude/hooks/detect-gaps.sh '{}' '{}'
   bash .claude/hooks/session-start.sh '{}' '{}'
   ```

---

*Each future version will have its own section in this file.*
*未来的每个版本都将在此文件中拥有自己的章节。*
