# Claude Code Game Studios -- Game Studio Agent Architecture
# Claude Code Game Studios -- 游戏工作室智能体架构

Indie game development managed through 49 coordinated Claude Code subagents.
通过 49 个协同工作的 Claude Code 子智能体管理独立游戏开发。

Each agent owns a specific domain, enforcing separation of concerns and quality.
每个智能体负责一个特定领域，强制执行关注点分离和质量保障。

## Technology Stack
## 技术栈

- **Engine**: [CHOOSE: Godot 4 / Unity / Unreal Engine 5]
- **引擎**: [选择：Godot 4 / Unity / Unreal Engine 5]

- **Language**: [CHOOSE: GDScript / C# / C++ / Blueprint]
- **语言**: [选择：GDScript / C# / C++ / Blueprint]

- **Version Control**: Git with trunk-based development
- **版本控制**: Git，采用主干开发模式

- **Build System**: [SPECIFY after choosing engine]
- **构建系统**: [选择引擎后指定]

- **Asset Pipeline**: [SPECIFY after choosing engine]
- **资产管线**: [选择引擎后指定]

> **Note**: Engine-specialist agents exist for Godot, Unity, and Unreal with
> dedicated sub-specialists. Use the set matching your engine.
> **注意**: Godot、Unity 和 Unreal 都有引擎专家智能体，并配备专门的子专家。请使用与你的引擎匹配的那一组。

## Project Structure
## 项目结构

@.claude/docs/directory-structure.md
@.claude/docs/directory-structure.md

## Engine Version Reference
## 引擎版本参考

@docs/engine-reference/godot/VERSION.md
@docs/engine-reference/godot/VERSION.md

## Technical Preferences
## 技术偏好

@.claude/docs/technical-preferences.md
@.claude/docs/technical-preferences.md

## Coordination Rules
## 协调规则

@.claude/docs/coordination-rules.md
@.claude/docs/coordination-rules.md

## Collaboration Protocol
## 协作协议

**User-driven collaboration, not autonomous execution.**
**用户驱动的协作，而非自主执行。**

Every task follows: **Question -> Options -> Decision -> Draft -> Approval**
每个任务遵循：**提问 -> 选项 -> 决策 -> 草稿 -> 批准**

- Agents MUST ask "May I write this to [filepath]?" before using Write/Edit tools
- 智能体在使用 Write/Edit 工具之前必须询问"是否可以将此内容写入 [filepath]？"

- Agents MUST show drafts or summaries before requesting approval
- 智能体在请求批准之前必须先展示草稿或摘要

- Multi-file changes require explicit approval for the full changeset
- 多文件变更需要对整个变更集进行明确批准

- No commits without user instruction
- 未经用户指示不得提交

See `docs/COLLABORATIVE-DESIGN-PRINCIPLE.md` for full protocol and examples.
完整协议和示例请参见 `docs/COLLABORATIVE-DESIGN-PRINCIPLE.md`。

> **First session?** If the project has no engine configured and no game concept,
> run `/start` to begin the guided onboarding flow.
> **首次使用？** 如果项目尚未配置引擎且没有游戏概念，
> 请运行 `/start` 开始引导式入门流程。

## Coding Standards
## 编码标准

@.claude/docs/coding-standards.md
@.claude/docs/coding-standards.md

## Context Management
## 上下文管理

@.claude/docs/context-management.md
@.claude/docs/context-management.md
