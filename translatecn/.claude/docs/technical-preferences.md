# Technical Preferences
# 技术偏好

<!-- Populated by /setup-engine. Updated as the user makes decisions throughout development. -->
<!-- 由 /setup-engine 填充。随着用户在开发过程中做出决策而更新。 -->
<!-- All agents reference this file for project-specific standards and conventions. -->
<!-- 所有智能体参考此文件以获取项目特定的标准和约定。 -->

## Engine & Language
## 引擎与语言

- **Engine**: [TO BE CONFIGURED — run /setup-engine]
- **Engine**: [待配置 — 运行 /setup-engine]
- **Language**: [TO BE CONFIGURED]
- **Language**: [待配置]
- **Rendering**: [TO BE CONFIGURED]
- **Rendering**: [待配置]
- **Physics**: [TO BE CONFIGURED]
- **Physics**: [待配置]

## Input & Platform
## 输入与平台

<!-- Written by /setup-engine. Read by /ux-design, /ux-review, /test-setup, /team-ui, and /dev-story -->
<!-- 由 /setup-engine 写入。由 /ux-design、/ux-review、/test-setup、/team-ui 和 /dev-story 读取 -->
<!-- to scope interaction specs, test helpers, and implementation to the correct input methods. -->
<!-- 用于将交互规格、测试辅助和实现限定到正确的输入方式。 -->

- **Target Platforms**: [TO BE CONFIGURED — e.g., PC, Console, Mobile, Web]
- **Target Platforms**: [待配置 — 例如 PC、主机、移动端、网页]
- **Input Methods**: [TO BE CONFIGURED — e.g., Keyboard/Mouse, Gamepad, Touch, Mixed]
- **Input Methods**: [待配置 — 例如 键盘/鼠标、手柄、触控、混合]
- **Primary Input**: [TO BE CONFIGURED — the dominant input for this game]
- **Primary Input**: [待配置 — 本游戏的主要输入方式]
- **Gamepad Support**: [TO BE CONFIGURED — Full / Partial / None]
- **Gamepad Support**: [待配置 — 完整 / 部分 / 无]
- **Touch Support**: [TO BE CONFIGURED — Full / Partial / None]
- **Touch Support**: [待配置 — 完整 / 部分 / 无]
- **Platform Notes**: [TO BE CONFIGURED — any platform-specific UX constraints]
- **Platform Notes**: [待配置 — 任何平台特定的 UX 约束]

## Naming Conventions
## 命名约定

- **Classes**: [TO BE CONFIGURED]
- **Classes**: [待配置]
- **Variables**: [TO BE CONFIGURED]
- **Variables**: [待配置]
- **Signals/Events**: [TO BE CONFIGURED]
- **Signals/Events**: [待配置]
- **Files**: [TO BE CONFIGURED]
- **Files**: [待配置]
- **Scenes/Prefabs**: [TO BE CONFIGURED]
- **Scenes/Prefabs**: [待配置]
- **Constants**: [TO BE CONFIGURED]
- **Constants**: [待配置]

## Performance Budgets
## 性能预算

- **Target Framerate**: [TO BE CONFIGURED]
- **Target Framerate**: [待配置]
- **Frame Budget**: [TO BE CONFIGURED]
- **Frame Budget**: [待配置]
- **Draw Calls**: [TO BE CONFIGURED]
- **Draw Calls**: [待配置]
- **Memory Ceiling**: [TO BE CONFIGURED]
- **Memory Ceiling**: [待配置]

## Testing
## 测试

- **Framework**: [TO BE CONFIGURED]
- **Framework**: [待配置]
- **Minimum Coverage**: [TO BE CONFIGURED]
- **Minimum Coverage**: [待配置]
- **Required Tests**: Balance formulas, gameplay systems, networking (if applicable)
- **Required Tests**: 平衡公式、游戏玩法系统、网络（如适用）

## Forbidden Patterns
## 禁止模式

<!-- Add patterns that should never appear in this project's codebase -->
<!-- 添加绝不应出现在本项目代码库中的模式 -->
- [None configured yet — add as architectural decisions are made]
- [尚未配置 — 在做出架构决策时添加]

## Allowed Libraries / Addons
## 允许的库 / 插件

<!-- Add approved third-party dependencies here -->
<!-- 在此处添加已批准的第三方依赖 -->
- [None configured yet — add as dependencies are approved]
- [尚未配置 — 在依赖获批时添加]

## Architecture Decisions Log
## 架构决策日志

<!-- Quick reference linking to full ADRs in docs/architecture/ -->
<!-- 指向 docs/architecture/ 中完整 ADR 的快速参考 -->
- [No ADRs yet — use /architecture-decision to create one]
- [尚无 ADR — 使用 /architecture-decision 创建一个]

## Engine Specialists
## 引擎专家

<!-- Written by /setup-engine when engine is configured. -->
<!-- 在配置引擎时由 /setup-engine 写入。 -->
<!-- Read by /code-review, /architecture-decision, /architecture-review, and team skills -->
<!-- 由 /code-review、/architecture-decision、/architecture-review 和团队技能读取 -->
<!-- to know which specialist to spawn for engine-specific validation. -->
<!-- 以了解在引擎特定验证时应生成哪位专家。 -->

- **Primary**: [TO BE CONFIGURED — run /setup-engine]
- **Primary**: [待配置 — 运行 /setup-engine]
- **Language/Code Specialist**: [TO BE CONFIGURED]
- **Language/Code Specialist**: [待配置]
- **Shader Specialist**: [TO BE CONFIGURED]
- **Shader Specialist**: [待配置]
- **UI Specialist**: [TO BE CONFIGURED]
- **UI Specialist**: [待配置]
- **Additional Specialists**: [TO BE CONFIGURED]
- **Additional Specialists**: [待配置]
- **Routing Notes**: [TO BE CONFIGURED]
- **Routing Notes**: [待配置]

### File Extension Routing
### 文件扩展名路由

<!-- Skills use this table to select the right specialist per file type. -->
<!-- 技能使用此表为每种文件类型选择正确的专家。 -->
<!-- If a row says [TO BE CONFIGURED], fall back to Primary for that file type. -->
<!-- 如果某行显示 [TO BE CONFIGURED]，则该文件类型回退到 Primary。 -->

| File Extension / Type | Specialist to Spawn |
| 文件扩展名 / 类型 | 要生成的专家 |
|-----------------------|---------------------|
| Game code (primary language) | [TO BE CONFIGURED] |
| 游戏代码（主要语言） | [待配置] |
| Shader / material files | [TO BE CONFIGURED] |
| 着色器 / 材质文件 | [待配置] |
| UI / screen files | [TO BE CONFIGURED] |
| UI / 屏幕文件 | [待配置] |
| Scene / prefab / level files | [TO BE CONFIGURED] |
| 场景 / 预制体 / 关卡文件 | [待配置] |
| Native extension / plugin files | [TO BE CONFIGURED] |
| 原生扩展 / 插件文件 | [待配置] |
| General architecture review | Primary |
| 通用架构评审 | Primary |
