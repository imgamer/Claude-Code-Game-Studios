---
name: onboard
description: "Generates a contextual onboarding document for a new contributor or agent joining the project. Summarizes project state, architecture, conventions, and current priorities relevant to the specified role or area."
argument-hint: "[role|area]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: haiku
---
---
name: onboard
description: "为新加入项目的贡献者或智能体生成上下文化的入门文档。总结与指定角色或领域相关的项目状态、架构、约定和当前优先级。"
argument-hint: "[role|area]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: haiku
---

## Phase 1: Load Project Context

Read CLAUDE.md for project overview and standards.

Read the relevant agent definition from `.claude/agents/` if a specific role is specified.

## 第 1 阶段：加载项目上下文

读取 CLAUDE.md 获取项目概览和标准。

如指定了具体角色，从 `.claude/agents/` 读取相关智能体定义。

---

## Phase 2: Scan Relevant Area

- For programmers: scan `src/` for architecture, patterns, key files
- For designers: scan `design/` for existing design documents
- For narrative: scan `design/narrative/` for world-building and story docs
- For QA: scan `tests/` for existing test coverage
- For production: scan `production/` for current sprint and milestone

Read recent changes (git log if available) to understand current momentum.

## 第 2 阶段：扫描相关领域

- 程序员：扫描 `src/` 了解架构、模式、关键文件
- 设计师：扫描 `design/` 了解现有设计文档
- 叙事：扫描 `design/narrative/` 了解世界观和故事文档
- QA：扫描 `tests/` 了解现有测试覆盖
- 制作：扫描 `production/` 了解当前冲刺和里程碑

读取近期变更（如可用 git log）以理解当前势头。

---

## Phase 3: Generate Onboarding Document

```markdown
# Onboarding: [Role/Area]

## Project Summary
[2-3 sentence summary of what this game is and its current state]

## Your Role
[What this role does on this project, key responsibilities, who you report to]

## Project Architecture
[Relevant architectural overview for this role]

### Key Directories
| Directory | Contents | Your Interaction |
|-----------|----------|-----------------|

### Key Files
| File | Purpose | Read Priority |
|------|---------|--------------|

## Current Standards and Conventions
[Summary of conventions relevant to this role from CLAUDE.md and agent definition]

## Current State of Your Area
[What has been built, what is in progress, what is planned next]

## Current Sprint Context
[What the team is working on now and what is expected of this role]

## Key Dependencies
[What other roles/systems this role interacts with most]

## Common Pitfalls
[Things that trip up new contributors in this area]

## First Tasks
[Suggested first tasks to get oriented and productive]

1. [Read these documents first]
2. [Review this code/content]
3. [Start with this small task]

## Questions to Ask
[Questions the new contributor should ask to get fully oriented]
```

## 第 3 阶段：生成入门文档

```markdown
# Onboarding: [Role/Area]

## Project Summary
[2-3 sentence summary of what this game is and its current state]

## Your Role
[What this role does on this project, key responsibilities, who you report to]

## Project Architecture
[Relevant architectural overview for this role]

### Key Directories
| Directory | Contents | Your Interaction |
|-----------|----------|-----------------|

### Key Files
| File | Purpose | Read Priority |
|------|---------|--------------|

## Current Standards and Conventions
[Summary of conventions relevant to this role from CLAUDE.md and agent definition]

## Current State of Your Area
[What has been built, what is in progress, what is planned next]

## Current Sprint Context
[What the team is working on now and what is expected of this role]

## Key Dependencies
[What other roles/systems this role interacts with most]

## Common Pitfalls
[Things that trip up new contributors in this area]

## First Tasks
[Suggested first tasks to get oriented and productive]

1. [Read these documents first]
2. [Review this code/content]
3. [Start with this small task]

## Questions to Ask
[Questions the new contributor should ask to get fully oriented]
```

---

## Phase 4: Save Document

Present the onboarding document to the user.

Ask: "May I write this to `production/onboarding/onboard-[role]-[date].md`?"

If yes, write the file, creating the directory if needed.

## 第 4 阶段：保存文档

向用户呈现入门文档。

询问："可以将其写入 `production/onboarding/onboard-[role]-[date].md` 吗？"

如同意，写入文件，如需要则创建目录。

---

## Phase 5: Next Steps

Verdict: **COMPLETE** — onboarding document generated.

- Share the onboarding doc with the new contributor before their first session.
- Run `/sprint-status` to show the new contributor current progress.
- Run `/help` if the contributor needs guidance on what to work on next.

## 第 5 阶段：下一步

判定：**COMPLETE** —— 入门文档已生成。

- 在新贡献者首次会话前与其分享入门文档。
- 运行 `/sprint-status` 向新贡献者展示当前进度。
- 如贡献者需要下一步做什么的指导，运行 `/help`。
