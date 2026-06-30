<p align="center">
  <h1 align="center">Claude Code Game Studios</h1>
  <p align="center">
    Turn a single Claude Code session into a full game development studio.
    <br />
    49 agents. 73 skills. One coordinated AI team.
  </p>
</p>

<p align="center">
  <h1 align="center">Claude Code Game Studios</h1>
  <p align="center">
    把单个 Claude Code 会话变成一个完整的游戏开发工作室。
    <br />
    49 个智能体。73 个技能。一个协同的 AI 团队。
  </p>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="MIT License"></a>
  <a href=".claude/agents"><img src="https://img.shields.io/badge/agents-49-blueviolet" alt="49 个智能体"></a>
  <a href=".claude/skills"><img src="https://img.shields.io/badge/skills-73-green" alt="73 个技能"></a>
  <a href=".claude/hooks"><img src="https://img.shields.io/badge/hooks-12-orange" alt="12 个钩子"></a>
  <a href=".claude/rules"><img src="https://img.shields.io/badge/rules-11-red" alt="11 条规则"></a>
  <a href="https://docs.anthropic.com/en/docs/claude-code"><img src="https://img.shields.io/badge/built%20for-Claude%20Code-f5f5f5?logo=anthropic" alt="为 Claude Code 而构建"></a>
  <a href="https://www.buymeacoffee.com/donchitos3"><img src="https://img.shields.io/badge/Buy%20Me%20a%20Coffee-Support%20this%20project-FFDD00?logo=buymeacoffee&logoColor=black" alt="请我喝杯咖啡"></a>
  <a href="https://github.com/sponsors/Donchitos"><img src="https://img.shields.io/badge/GitHub%20Sponsors-Support%20this%20project-ea4aaa?logo=githubsponsors&logoColor=white" alt="GitHub 赞助"></a>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="MIT 许可证"></a>
  <a href=".claude/agents"><img src="https://img.shields.io/badge/agents-49-blueviolet" alt="49 个智能体"></a>
  <a href=".claude/skills"><img src="https://img.shields.io/badge/skills-73-green" alt="73 个技能"></a>
  <a href=".claude/hooks"><img src="https://img.shields.io/badge/hooks-12-orange" alt="12 个钩子"></a>
  <a href=".claude/rules"><img src="https://img.shields.io/badge/rules-11-red" alt="11 条规则"></a>
  <a href="https://docs.anthropic.com/en/docs/claude-code"><img src="https://img.shields.io/badge/built%20for-Claude%20Code-f5f5f5?logo=anthropic" alt="为 Claude Code 而构建"></a>
  <a href="https://www.buymeacoffee.com/donchitos3"><img src="https://img.shields.io/badge/Buy%20Me%20a%20Coffee-Support%20this%20project-FFDD00?logo=buymeacoffee&logoColor=black" alt="请我喝杯咖啡"></a>
  <a href="https://github.com/sponsors/Donchitos"><img src="https://img.shields.io/badge/GitHub%20Sponsors-Support%20this%20project-ea4aaa?logo=githubsponsors&logoColor=white" alt="GitHub 赞助"></a>
</p>

---

## Why This Exists
## 为什么会有这个项目

Building a game solo with AI is powerful — but a single chat session has no structure. No one stops you from hardcoding magic numbers, skipping design docs, or writing spaghetti code. There's no QA pass, no design review, no one asking "does this actually fit the game's vision?"
独立用 AI 开发游戏很强大——但单个聊天会话没有结构。没人会阻止你硬编码魔法数字、跳过设计文档，或者写意大利面条式代码。没有 QA 环节、没有设计评审，也没人问"这真的符合游戏愿景吗？"

**Claude Code Game Studios** solves this by giving your AI session the structure of a real studio. Instead of one general-purpose assistant, you get 49 specialized agents organized into a studio hierarchy — directors who guard the vision, department leads who own their domains, and specialists who do the hands-on work. Each agent has defined responsibilities, escalation paths, and quality gates.
**Claude Code Game Studios** 通过为你的 AI 会话赋予真实工作室的结构来解决这些问题。你获得的不是一个通用助手，而是 49 个按工作室层级组织的专业智能体——守护愿景的总监、各自负责领域的部门主管，以及完成具体工作的专家。每个智能体都有明确的职责、升级路径和质量门禁。

The result: you still make every decision, but now you have a team that asks the right questions, catches mistakes early, and keeps your project organized from first brainstorm to launch.
结果是：你仍然做出每一个决定，但现在你拥有一个团队——会提出正确的问题、及早发现错误，并让你的项目从最初头脑风暴到发布都井然有序。

---

## Table of Contents
## 目录

- [What's Included](#whats-included)
- [包含内容](#whats-included)

- [Studio Hierarchy](#studio-hierarchy)
- [工作室层级](#studio-hierarchy)

- [Slash Commands](#slash-commands)
- [斜杠命令](#slash-commands)

- [Getting Started](#getting-started)
- [快速开始](#getting-started)

- [Upgrading](#upgrading)
- [升级](#upgrading)

- [Project Structure](#project-structure)
- [项目结构](#project-structure)

- [How It Works](#how-it-works)
- [工作原理](#how-it-works)

- [Design Philosophy](#design-philosophy)
- [设计理念](#design-philosophy)

- [Customization](#customization)
- [自定义](#customization)

- [Platform Support](#platform-support)
- [平台支持](#platform-support)

- [Community](#community)
- [社区](#community)

- [Supporting This Project](#supporting-this-project)
- [支持本项目](#supporting-this-project)

- [License](#license)
- [许可证](#license)

---

## What's Included
## 包含内容

| Category | Count | Description |
|----------|-------|-------------|
| **Agents** | 49 | Specialized subagents across design, programming, art, audio, narrative, QA, and production |
| **Skills** | 73 | Slash commands for every workflow phase (`/start`, `/design-system`, `/create-epics`, `/create-stories`, `/dev-story`, `/story-done`, etc.) |
| **Hooks** | 12 | Automated validation on commits, pushes, asset changes, session lifecycle, agent audit trail, and gap detection |
| **Rules** | 11 | Path-scoped coding standards enforced when editing gameplay, engine, AI, UI, network code, and more |
| **Templates** | 41 | Document templates for GDDs, UX specs, ADRs, sprint plans, HUD design, accessibility, and more |

| 类别 | 数量 | 描述 |
|----------|-------|-------------|
| **智能体** | 49 | 涵盖设计、编程、美术、音频、叙事、QA 和制作的专用子智能体 |
| **技能** | 73 | 覆盖工作流每个阶段的斜杠命令（`/start`、`/design-system`、`/create-epics`、`/create-stories`、`/dev-story`、`/story-done` 等） |
| **钩子** | 12 | 对提交、推送、资产变更、会话生命周期、智能体审计追踪和缺口检测进行自动化验证 |
| **规则** | 11 | 在编辑玩法、引擎、AI、UI、网络代码等时强制执行的按路径限定范围的编码标准 |
| **模板** | 41 | 用于 GDD、UX 规格、ADR、冲刺计划、HUD 设计、可访问性等的文档模板 |

## Studio Hierarchy
## 工作室层级

Agents are organized into three tiers, matching how real studios operate:
智能体被组织为三个层级，与现实工作室的运作方式一致：

```
Tier 1 — Directors (Opus)
  creative-director    technical-director    producer

Tier 2 — Department Leads (Sonnet)
  game-designer        lead-programmer       art-director
  audio-director       narrative-director    qa-lead
  release-manager      localization-lead

Tier 3 — Specialists (Sonnet/Haiku)
  gameplay-programmer  engine-programmer     ai-programmer
  network-programmer   tools-programmer      ui-programmer
  systems-designer     level-designer        economy-designer
  technical-artist     sound-designer        writer
  world-builder        ux-designer           prototyper
  performance-analyst  devops-engineer       analytics-engineer
  security-engineer    qa-tester             accessibility-specialist
  live-ops-designer    community-manager
```

```
Tier 1 — Directors (Opus)
  creative-director    technical-director    producer

Tier 2 — Department Leads (Sonnet)
  game-designer        lead-programmer       art-director
  audio-director       narrative-director    qa-lead
  release-manager      localization-lead

Tier 3 — Specialists (Sonnet/Haiku)
  gameplay-programmer  engine-programmer     ai-programmer
  network-programmer   tools-programmer      ui-programmer
  systems-designer     level-designer        economy-designer
  technical-artist     sound-designer        writer
  world-builder        ux-designer           prototyper
  performance-analyst  devops-engineer       analytics-engineer
  security-engineer    qa-tester             accessibility-specialist
  live-ops-designer    community-manager
```

### Engine Specialists
### 引擎专家

The template includes agent sets for all three major engines. Use the set that matches your project:
该模板包含所有三大主流引擎的智能体集合。请使用与你的项目匹配的那一组：

| Engine | Lead Agent | Sub-Specialists |
|--------|-----------|-----------------|
| **Godot 4** | `godot-specialist` | GDScript, Shaders, GDExtension |
| **Unity** | `unity-specialist` | DOTS/ECS, Shaders/VFX, Addressables, UI Toolkit |
| **Unreal Engine 5** | `unreal-specialist` | GAS, Blueprints, Replication, UMG/CommonUI |

| 引擎 | 主管智能体 | 子专家 |
|--------|-----------|-----------------|
| **Godot 4** | `godot-specialist` | GDScript、Shaders、GDExtension |
| **Unity** | `unity-specialist` | DOTS/ECS、Shaders/VFX、Addressables、UI Toolkit |
| **Unreal Engine 5** | `unreal-specialist` | GAS、Blueprints、Replication、UMG/CommonUI |

## Slash Commands
## 斜杠命令

Type `/` in Claude Code to access all 73 skills:
在 Claude Code 中输入 `/` 即可访问全部 73 个技能：

**Onboarding & Navigation**
**入门与导航**

`/start` `/help` `/project-stage-detect` `/setup-engine` `/adopt`
`/start` `/help` `/project-stage-detect` `/setup-engine` `/adopt`

**Game Design**
**游戏设计**

`/brainstorm` `/map-systems` `/design-system` `/quick-design` `/review-all-gdds` `/propagate-design-change`
`/brainstorm` `/map-systems` `/design-system` `/quick-design` `/review-all-gdds` `/propagate-design-change`

**Art & Assets**
**美术与资产**

`/art-bible` `/asset-spec` `/asset-audit`
`/art-bible` `/asset-spec` `/asset-audit`

**UX & Interface Design**
**UX 与界面设计**

`/ux-design` `/ux-review`
`/ux-design` `/ux-review`

**Architecture**
**架构**

`/create-architecture` `/architecture-decision` `/architecture-review` `/create-control-manifest`
`/create-architecture` `/architecture-decision` `/architecture-review` `/create-control-manifest`

**Stories & Sprints**
**故事与冲刺**

`/create-epics` `/create-stories` `/dev-story` `/sprint-plan` `/sprint-status` `/story-readiness` `/story-done` `/estimate`
`/create-epics` `/create-stories` `/dev-story` `/sprint-plan` `/sprint-status` `/story-readiness` `/story-done` `/estimate`

**Reviews & Analysis**
**评审与分析**

`/design-review` `/code-review` `/balance-check` `/content-audit` `/scope-check` `/perf-profile` `/tech-debt` `/gate-check` `/consistency-check` `/security-audit`
`/design-review` `/code-review` `/balance-check` `/content-audit` `/scope-check` `/perf-profile` `/tech-debt` `/gate-check` `/consistency-check` `/security-audit`

**QA & Testing**
**QA 与测试**

`/qa-plan` `/smoke-check` `/soak-test` `/regression-suite` `/test-setup` `/test-helpers` `/test-evidence-review` `/test-flakiness` `/skill-test` `/skill-improve`
`/qa-plan` `/smoke-check` `/soak-test` `/regression-suite` `/test-setup` `/test-helpers` `/test-evidence-review` `/test-flakiness` `/skill-test` `/skill-improve`

**Production**
**制作**

`/milestone-review` `/retrospective` `/bug-report` `/bug-triage` `/reverse-document` `/playtest-report`
`/milestone-review` `/retrospective` `/bug-report` `/bug-triage` `/reverse-document` `/playtest-report`

**Release**
**发布**

`/release-checklist` `/launch-checklist` `/changelog` `/patch-notes` `/hotfix` `/day-one-patch`
`/release-checklist` `/launch-checklist` `/changelog` `/patch-notes` `/hotfix` `/day-one-patch`

**Creative & Content**
**创意与内容**

`/prototype` `/onboard` `/localize`
`/prototype` `/onboard` `/localize`

**Team Orchestration** (coordinate multiple agents on a single feature)
**团队编排**（在单一功能上协调多个智能体）

`/team-combat` `/team-narrative` `/team-ui` `/team-release` `/team-polish` `/team-audio` `/team-level` `/team-live-ops` `/team-qa`
`/team-combat` `/team-narrative` `/team-ui` `/team-release` `/team-polish` `/team-audio` `/team-level` `/team-live-ops` `/team-qa`

## Getting Started
## 快速开始

### Prerequisites
### 前置条件

- [Git](https://git-scm.com/)
- [Git](https://git-scm.com/)

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (`npm install -g @anthropic-ai/claude-code`)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)（`npm install -g @anthropic-ai/claude-code`）

- **Recommended**: [jq](https://jqlang.github.io/jq/) (for hook validation) and Python 3 (for JSON validation)
- **推荐**：[jq](https://jqlang.github.io/jq/)（用于钩子验证）和 Python 3（用于 JSON 验证）

All hooks fail gracefully if optional tools are missing — nothing breaks, you just lose validation.
所有钩子在缺少可选工具时都会优雅降级——不会出任何问题，你只是失去了验证功能。

### Setup
### 安装

1. **Clone or use as template**:
1. **克隆或作为模板使用**：

   ```bash
   git clone https://github.com/Donchitos/Claude-Code-Game-Studios.git my-game
   cd my-game
   ```

   ```bash
   git clone https://github.com/Donchitos/Claude-Code-Game-Studios.git my-game
   cd my-game
   ```

2. **Open Claude Code** and start a session:
2. **打开 Claude Code** 并启动一个会话：

   ```bash
   claude
   ```

   ```bash
   claude
   ```

3. **Run `/start`** — the system asks where you are (no idea, vague concept,
   clear design, existing work) and guides you to the right workflow. No assumptions.
3. **运行 `/start`** —— 系统会询问你当前所处阶段（毫无头绪、模糊概念、
   清晰设计、已有作品），并引导你到合适的工作流。不会做任何假设。

   Or jump directly to a specific skill if you already know what you need:
   如果你已经清楚需要什么，也可以直接跳到具体技能：

   - `/brainstorm` — explore game ideas from scratch
   - `/brainstorm` — 从零开始探索游戏创意

   - `/setup-engine godot 4.6` — configure your engine if you already know
   - `/setup-engine godot 4.6` — 如果你已经确定，则配置你的引擎

   - `/project-stage-detect` — analyze an existing project
   - `/project-stage-detect` — 分析现有项目

## Upgrading
## 升级

Already using an older version of this template? See [UPGRADING.md](UPGRADING.md)
for step-by-step migration instructions, a breakdown of what changed between
versions, and which files are safe to overwrite vs. which need a manual merge.
已经在使用本模板的旧版本？请参见 [UPGRADING.md](UPGRADING.md)
了解分步迁移指南、各版本之间的变更明细，以及哪些文件可以安全覆盖、哪些需要手动合并。

## Project Structure
## 项目结构

```
CLAUDE.md                           # Master configuration
.claude/
  settings.json                     # Hooks, permissions, safety rules
  agents/                           # 49 agent definitions (markdown + YAML frontmatter)
  skills/                           # 73 slash commands (subdirectory per skill)
  hooks/                            # 12 hook scripts (bash, cross-platform)
  rules/                            # 11 path-scoped coding standards
  statusline.sh                     # Status line script (context%, model, stage, epic breadcrumb)
  docs/
    workflow-catalog.yaml           # 7-phase pipeline definition (read by /help)
    templates/                      # 41 document templates
src/                                # Game source code
assets/                             # Art, audio, VFX, shaders, data files
design/                             # GDDs, narrative docs, level designs
docs/                               # Technical documentation and ADRs
tests/                              # Test suites (unit, integration, performance, playtest)
tools/                              # Build and pipeline tools
prototypes/                         # Throwaway prototypes (isolated from src/)
production/                         # Sprint plans, milestones, release tracking
```

```
CLAUDE.md                           # 主配置
.claude/
  settings.json                     # 钩子、权限、安全规则
  agents/                           # 49 个智能体定义（markdown + YAML frontmatter）
  skills/                           # 73 个斜杠命令（每个技能一个子目录）
  hooks/                            # 12 个钩子脚本（bash，跨平台）
  rules/                            # 11 条按路径限定范围的编码标准
  statusline.sh                     # 状态行脚本（上下文百分比%、模型、阶段、史诗面包屑）
  docs/
    workflow-catalog.yaml           # 7 阶段流水线定义（由 /help 读取）
    templates/                      # 41 个文档模板
src/                                # 游戏源代码
assets/                             # 美术、音频、VFX、着色器、数据文件
design/                             # GDD、叙事文档、关卡设计
docs/                               # 技术文档和 ADR
tests/                              # 测试套件（单元、集成、性能、试玩）
tools/                              # 构建与管线工具
prototypes/                         # 一次性原型（与 src/ 隔离）
production/                         # 冲刺计划、里程碑、发布跟踪
```

## How It Works
## 工作原理

### Agent Coordination
### 智能体协调

Agents follow a structured delegation model:
智能体遵循一个结构化的委派模型：

1. **Vertical delegation** — directors delegate to leads, leads delegate to specialists
1. **纵向委派** —— 总监委派给主管，主管委派给专家

2. **Horizontal consultation** — same-tier agents can consult each other but can't make binding cross-domain decisions
2. **横向协商** —— 同层级的智能体可以互相咨询，但不能做出有约束力的跨领域决定

3. **Conflict resolution** — disagreements escalate up to the shared parent (`creative-director` for design, `technical-director` for technical)
3. **冲突解决** —— 分歧向上升级到共同的父级（设计相关升级给 `creative-director`，技术相关升级给 `technical-director`）

4. **Change propagation** — cross-department changes are coordinated by `producer`
4. **变更传播** —— 跨部门变更由 `producer` 协调

5. **Domain boundaries** — agents don't modify files outside their domain without explicit delegation
5. **领域边界** —— 未经明确委派，智能体不修改其领域之外的文件

### Collaborative, Not Autonomous
### 协作，而非自主

This is **not** an auto-pilot system. Every agent follows a strict collaboration protocol:
这**不是**自动驾驶系统。每个智能体都遵循严格的协作协议：

1. **Ask** — agents ask questions before proposing solutions
1. **提问** —— 智能体在提出方案前先提问

2. **Present options** — agents show 2-4 options with pros/cons
2. **呈现选项** —— 智能体展示 2-4 个选项及其优缺点

3. **You decide** — the user always makes the call
3. **你做决定** —— 始终由用户拍板

4. **Draft** — agents show work before finalizing
4. **草稿** —— 智能体在定稿前先展示成果

5. **Approve** — nothing gets written without your sign-off
5. **批准** —— 没有你的签字确认，什么都不会写入

You stay in control. The agents provide structure and expertise, not autonomy.
你始终掌握控制权。智能体提供的是结构和专业知识，而非自主权。

### Automated Safety
### 自动化安全

**Hooks** run automatically on every session:
**钩子**在每次会话时自动运行：

| Hook | Trigger | What It Does |
|------|---------|--------------|
| `validate-commit.sh` | PreToolUse (Bash) | Checks for hardcoded values, TODO format, JSON validity, design doc sections — exits early if the command is not `git commit` |
| `validate-push.sh` | PreToolUse (Bash) | Warns on pushes to protected branches — exits early if the command is not `git push` |
| `validate-assets.sh` | PostToolUse (Write/Edit) | Validates naming conventions and JSON structure — exits early if the file is not in `assets/` |
| `session-start.sh` | Session open | Shows current branch and recent commits for orientation |
| `detect-gaps.sh` | Session open | Detects fresh projects (suggests `/start`) and missing design docs when code or prototypes exist |
| `pre-compact.sh` | Before compaction | Preserves session progress notes |
| `post-compact.sh` | After compaction | Reminds Claude to restore session state from `active.md` |
| `notify.sh` | Notification event | Shows Windows toast notification via PowerShell |
| `session-stop.sh` | Session close | Archives `active.md` to session log and records git activity |
| `log-agent.sh` | Agent spawned | Audit trail start — logs subagent invocation |
| `log-agent-stop.sh` | Agent stops | Audit trail stop — completes subagent record |
| `validate-skill-change.sh` | PostToolUse (Write/Edit) | Advises running `/skill-test` after any `.claude/skills/` change |

| 钩子 | 触发时机 | 作用 |
|------|---------|--------------|
| `validate-commit.sh` | PreToolUse（Bash） | 检查硬编码值、TODO 格式、JSON 有效性、设计文档章节 —— 如果命令不是 `git commit` 则提前退出 |
| `validate-push.sh` | PreToolUse（Bash） | 在向受保护分支推送时发出警告 —— 如果命令不是 `git push` 则提前退出 |
| `validate-assets.sh` | PostToolUse（Write/Edit） | 验证命名规范和 JSON 结构 —— 如果文件不在 `assets/` 中则提前退出 |
| `session-start.sh` | 会话打开时 | 显示当前分支和最近提交，便于快速定位 |
| `detect-gaps.sh` | 会话打开时 | 检测新项目（建议运行 `/start`）以及在已存在代码或原型时缺失的设计文档 |
| `pre-compact.sh` | 压缩前 | 保留会话进度笔记 |
| `post-compact.sh` | 压缩后 | 提醒 Claude 从 `active.md` 恢复会话状态 |
| `notify.sh` | 通知事件 | 通过 PowerShell 显示 Windows 弹窗通知 |
| `session-stop.sh` | 会话关闭时 | 将 `active.md` 归档到会话日志并记录 git 活动 |
| `log-agent.sh` | 智能体生成时 | 审计追踪起点 —— 记录子智能体调用 |
| `log-agent-stop.sh` | 智能体停止时 | 审计追踪终点 —— 完成子智能体记录 |
| `validate-skill-change.sh` | PostToolUse（Write/Edit） | 在 `.claude/skills/` 发生任何变更后建议运行 `/skill-test` |

> **Note**: `validate-commit.sh`, `validate-assets.sh`, and `validate-skill-change.sh` fire on every Bash/Write tool call and exit immediately (exit 0) when the command or file path is not relevant. This is normal hook behavior — not a performance concern.
> **注意**：`validate-commit.sh`、`validate-assets.sh` 和 `validate-skill-change.sh` 会在每次 Bash/Write 工具调用时触发，并在命令或文件路径不相关时立即退出（exit 0）。这是正常的钩子行为——不会带来性能问题。

**Permission rules** in `settings.json` auto-allow safe operations (git status, test runs) and block dangerous ones (force push, `rm -rf`, reading `.env` files).
`settings.json` 中的**权限规则**会自动放行安全操作（git status、运行测试），并拦截危险操作（强制推送、`rm -rf`、读取 `.env` 文件）。

### Path-Scoped Rules
### 按路径限定范围的规则

Coding standards are automatically enforced based on file location:
编码标准会根据文件位置自动执行：

| Path | Enforces |
|------|----------|
| `src/gameplay/**` | Data-driven values, delta time usage, no UI references |
| `src/core/**` | Zero allocations in hot paths, thread safety, API stability |
| `src/ai/**` | Performance budgets, debuggability, data-driven parameters |
| `src/networking/**` | Server-authoritative, versioned messages, security |
| `src/ui/**` | No game state ownership, localization-ready, accessibility |
| `design/gdd/**` | Required 8 sections, formula format, edge cases |
| `tests/**` | Test naming, coverage requirements, fixture patterns |
| `prototypes/**` | Relaxed standards, README required, hypothesis documented |

| 路径 | 强制执行 |
|------|----------|
| `src/gameplay/**` | 数据驱动取值、使用 delta time、不引用 UI |
| `src/core/**` | 热点路径零分配、线程安全、API 稳定性 |
| `src/ai/**` | 性能预算、可调试性、数据驱动参数 |
| `src/networking/**` | 服务器权威、消息版本化、安全性 |
| `src/ui/**` | 不持有游戏状态、本地化就绪、可访问性 |
| `design/gdd/**` | 必需的 8 个章节、公式格式、边界情况 |
| `tests/**` | 测试命名、覆盖率要求、夹具模式 |
| `prototypes/**` | 放宽的标准、需 README、记录假设 |

## Design Philosophy
## 设计理念

This template is grounded in professional game development practices:
本模板以专业游戏开发实践为基础：

- **MDA Framework** — Mechanics, Dynamics, Aesthetics analysis for game design
- **MDA 框架** —— 用于游戏设计的机制（Mechanics）、动态（Dynamics）、美学（Aesthetics）分析

- **Self-Determination Theory** — Autonomy, Competence, Relatedness for player motivation
- **自我决定理论** —— 用于玩家动机的自主性（Autonomy）、胜任感（Competence）、关联感（Relatedness）

- **Flow State Design** — Challenge-skill balance for player engagement
- **心流状态设计** —— 用于玩家投入的挑战-技能平衡

- **Bartle Player Types** — Audience targeting and validation
- **Bartle 玩家类型** —— 受众定位和验证

- **Verification-Driven Development** — Tests first, then implementation
- **验证驱动开发** —— 先测试，后实现

## Customization
## 自定义

This is a **template**, not a locked framework. Everything is meant to be customized:
这是一个**模板**，而非锁定框架。一切都是为了被定制而设计的：

- **Add/remove agents** — delete agent files you don't need, add new ones for your domains
- **新增/移除智能体** —— 删除你不需要的智能体文件，为你的领域添加新的智能体

- **Edit agent prompts** — tune agent behavior, add project-specific knowledge
- **编辑智能体提示词** —— 调节智能体行为，添加项目专属知识

- **Modify skills** — adjust workflows to match your team's process
- **修改技能** —— 调整工作流以匹配你团队的过程

- **Add rules** — create new path-scoped rules for your project's directory structure
- **新增规则** —— 为你的项目目录结构创建新的按路径限定范围的规则

- **Tune hooks** — adjust validation strictness, add new checks
- **调节钩子** —— 调整验证严格程度，添加新的检查

- **Pick your engine** — use the Godot, Unity, or Unreal agent set (or none)
- **选择你的引擎** —— 使用 Godot、Unity 或 Unreal 智能体集合（或都不用）

- **Set review intensity** — `full` (all director gates), `lean` (phase gates only), or `solo` (none). Set during `/start` or edit `production/review-mode.txt`. Override per-run with `--review solo` on any skill.
- **设置评审强度** —— `full`（所有总监门禁）、`lean`（仅阶段门禁）或 `solo`（无门禁）。在 `/start` 时设置，或编辑 `production/review-mode.txt`。在任何技能上通过 `--review solo` 进行单次运行覆盖。

## Platform Support
## 平台支持

Primary development and testing on **Windows 10** with Git Bash. All hooks use POSIX-compatible patterns (`grep -E`, not `grep -P`) and include fallbacks for missing tools, so they should run on macOS and Linux. The `notify.sh` hook uses PowerShell for Windows toast notifications and is a no-op elsewhere — desktop notifications on macOS/Linux are not yet wired. Cross-platform testing is ongoing; please file issues for any platform-specific breakage.
主要在 **Windows 10** 上使用 Git Bash 进行开发和测试。所有钩子都使用 POSIX 兼容模式（`grep -E`，而不是 `grep -P`），并为缺失的工具提供降级方案，因此它们应该能在 macOS 和 Linux 上运行。`notify.sh` 钩子使用 PowerShell 显示 Windows 弹窗通知，在其他平台上是空操作——macOS/Linux 上的桌面通知尚未接入。跨平台测试仍在进行中；如遇任何平台特定的破坏，请提 issue。

## Community
## 社区

- **Discussions** — [GitHub Discussions](https://github.com/Donchitos/Claude-Code-Game-Studios/discussions) for questions, ideas, and showcasing what you've built
- **讨论** —— [GitHub Discussions](https://github.com/Donchitos/Claude-Code-Game-Studios/discussions) 用于提问、分享想法以及展示你构建的成果

- **Issues** — [Bug reports and feature requests](https://github.com/Donchitos/Claude-Code-Game-Studios/issues)
- **Issues** —— [Bug 报告和功能请求](https://github.com/Donchitos/Claude-Code-Game-Studios/issues)

---

## Supporting This Project
## 支持本项目

Claude Code Game Studios is free and open source. If it saves you time or helps you ship your game, consider supporting continued development:
Claude Code Game Studios 是免费且开源的。如果它为你节省了时间或帮助你发布了游戏，请考虑支持持续开发：

<p>
  <a href="https://www.buymeacoffee.com/donchitos3"><img src="https://img.shields.io/badge/Buy%20Me%20a%20Coffee-FFDD00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black" alt="Buy Me a Coffee"></a>
  &nbsp;
  <a href="https://github.com/sponsors/Donchitos"><img src="https://img.shields.io/badge/GitHub%20Sponsors-ea4aaa?style=for-the-badge&logo=githubsponsors&logoColor=white" alt="GitHub Sponsors"></a>
</p>

<p>
  <a href="https://www.buymeacoffee.com/donchitos3"><img src="https://img.shields.io/badge/Buy%20Me%20a%20Coffee-FFDD00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black" alt="请我喝杯咖啡"></a>
  &nbsp;
  <a href="https://github.com/sponsors/Donchitos"><img src="https://img.shields.io/badge/GitHub%20Sponsors-ea4aaa?style=for-the-badge&logo=githubsponsors&logoColor=white" alt="GitHub 赞助"></a>
</p>

- **[Buy Me a Coffee](https://www.buymeacoffee.com/donchitos3)** — one-time support
- **[请我喝杯咖啡](https://www.buymeacoffee.com/donchitos3)** —— 一次性支持

- **[GitHub Sponsors](https://github.com/sponsors/Donchitos)** — recurring support through GitHub
- **[GitHub 赞助](https://github.com/sponsors/Donchitos)** —— 通过 GitHub 进行持续支持

Sponsorships help fund time spent maintaining skills, adding new agents, keeping up with Claude Code and engine API changes, and responding to community issues.
赞助有助于资助维护技能、添加新智能体、跟进 Claude Code 与引擎 API 变更以及响应社区问题所花费的时间。

---

*Built for Claude Code. Maintained and extended — contributions welcome via [GitHub Discussions](https://github.com/Donchitos/Claude-Code-Game-Studios/discussions).*
*为 Claude Code 而构建。持续维护与扩展 —— 欢迎通过 [GitHub Discussions](https://github.com/Donchitos/Claude-Code-Game-Studios/discussions) 贡献。*

## License
## 许可证

MIT License. See [LICENSE](LICENSE) for details.
MIT 许可证。详情请参见 [LICENSE](LICENSE)。
