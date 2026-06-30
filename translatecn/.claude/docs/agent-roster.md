# Agent Roster
# 智能体花名册

The following agents are available. Each has a dedicated definition file in
`.claude/agents/`. Use the agent best suited to the task at hand. When a task
spans multiple domains, the coordinating agent (usually `producer` or the
domain lead) should delegate to specialists.
以下智能体可用。每个都在 `.claude/agents/` 中有专门的定义文件。请使用最适合当前任务的智能体。当任务跨越多个领域时，协调智能体（通常是 `producer` 或领域主管）应委派给专家。

## Tier 1 -- Leadership Agents (Opus)
## 层级 1 — 领导智能体（Opus）
| Agent | Domain | When to Use |
| 智能体 | 领域 | 使用时机 |
|-------|--------|-------------|
| `creative-director` | High-level vision | Major creative decisions, pillar conflicts, tone/direction |
| `creative-director` | 高层愿景 | 重大创意决策、支柱冲突、基调/方向 |
| `technical-director` | Technical vision | Architecture decisions, tech stack choices, performance strategy |
| `technical-director` | 技术愿景 | 架构决策、技术栈选择、性能策略 |
| `producer` | Production management | Sprint planning, milestone tracking, risk management, coordination |
| `producer` | 生产管理 | 冲刺规划、里程碑跟踪、风险管理、协调 |

## Tier 2 -- Department Lead Agents (Sonnet)
## 层级 2 — 部门主管智能体（Sonnet）
| Agent | Domain | When to Use |
| 智能体 | 领域 | 使用时机 |
|-------|--------|-------------|
| `game-designer` | Game design | Mechanics, systems, progression, economy, balancing |
| `game-designer` | 游戏设计 | 机制、系统、进度、经济、平衡 |
| `lead-programmer` | Code architecture | System design, code review, API design, refactoring |
| `lead-programmer` | 代码架构 | 系统设计、代码评审、API 设计、重构 |
| `art-director` | Visual direction | Style guides, art bible, asset standards, UI/UX direction |
| `art-director` | 视觉方向 | 风格指南、美术圣经、资源标准、UI/UX 方向 |
| `audio-director` | Audio direction | Music direction, sound palette, audio implementation strategy |
| `audio-director` | 音频方向 | 音乐方向、声音调色板、音频实现策略 |
| `narrative-director` | Story and writing | Story arcs, world-building, character design, dialogue strategy |
| `narrative-director` | 故事与编剧 | 故事弧线、世界观构建、角色设计、对话策略 |
| `qa-lead` | Quality assurance | Test strategy, bug triage, release readiness, regression planning |
| `qa-lead` | 质量保证 | 测试策略、bug 分类、发布就绪度、回归规划 |
| `release-manager` | Release pipeline | Build management, versioning, changelogs, deployment, rollbacks |
| `release-manager` | 发布流水线 | 构建管理、版本控制、更新日志、部署、回滚 |
| `localization-lead` | Internationalization | String externalization, translation pipeline, locale testing |
| `localization-lead` | 国际化 | 字符串外部化、翻译流水线、区域设置测试 |

## Tier 3 -- Specialist Agents (Sonnet or Haiku)
## 层级 3 — 专家智能体（Sonnet 或 Haiku）
| Agent | Domain | Model | When to Use |
| 智能体 | 领域 | 模型 | 使用时机 |
|-------|--------|-------|-------------|
| `systems-designer` | Systems design | Sonnet | Specific mechanic implementation, formula design, loops |
| `systems-designer` | 系统设计 | Sonnet | 具体机制实现、公式设计、循环 |
| `level-designer` | Level design | Sonnet | Level layouts, pacing, encounter design, flow |
| `level-designer` | 关卡设计 | Sonnet | 关卡布局、节奏、遭遇设计、流程 |
| `economy-designer` | Economy/balance | Sonnet | Resource economies, loot tables, progression curves |
| `economy-designer` | 经济/平衡 | Sonnet | 资源经济、掉落表、进度曲线 |
| `gameplay-programmer` | Gameplay code | Sonnet | Feature implementation, gameplay systems code |
| `gameplay-programmer` | 游戏玩法代码 | Sonnet | 功能实现、游戏玩法系统代码 |
| `engine-programmer` | Engine systems | Sonnet | Core engine, rendering, physics, memory management |
| `engine-programmer` | 引擎系统 | Sonnet | 核心引擎、渲染、物理、内存管理 |
| `ai-programmer` | AI systems | Sonnet | Behavior trees, pathfinding, NPC logic, state machines |
| `ai-programmer` | AI 系统 | Sonnet | 行为树、寻路、NPC 逻辑、状态机 |
| `network-programmer` | Networking | Sonnet | Netcode, replication, lag compensation, matchmaking |
| `network-programmer` | 网络 | Sonnet | 网络代码、复制、延迟补偿、匹配 |
| `tools-programmer` | Dev tools | Sonnet | Editor extensions, pipeline tools, debug utilities |
| `tools-programmer` | 开发工具 | Sonnet | 编辑器扩展、流水线工具、调试工具 |
| `ui-programmer` | UI implementation | Sonnet | UI framework, screens, widgets, data binding |
| `ui-programmer` | UI 实现 | Sonnet | UI 框架、屏幕、控件、数据绑定 |
| `technical-artist` | Tech art | Sonnet | Shaders, VFX, optimization, art pipeline tools |
| `technical-artist` | 技术美术 | Sonnet | 着色器、VFX、优化、美术流水线工具 |
| `sound-designer` | Sound design | Sonnet | SFX design docs, audio event lists, mixing notes |
| `sound-designer` | 音效设计 | Sonnet | 音效设计文档、音频事件列表、混音笔记 |
| `writer` | Dialogue/lore | Sonnet | Dialogue writing, lore entries, item descriptions |
| `writer` | 对话/设定 | Sonnet | 对话编写、设定条目、物品描述 |
| `world-builder` | World/lore design | Sonnet | World rules, faction design, history, geography |
| `world-builder` | 世界/设定设计 | Sonnet | 世界规则、阵营设计、历史、地理 |
| `qa-tester` | Test execution | Haiku | Writing test cases, bug reports, test checklists |
| `qa-tester` | 测试执行 | Haiku | 编写测试用例、bug 报告、测试检查清单 |
| `performance-analyst` | Performance | Sonnet | Profiling, optimization recs, memory analysis |
| `performance-analyst` | 性能 | Sonnet | 性能分析、优化建议、内存分析 |
| `devops-engineer` | Build/deploy | Haiku | CI/CD, build scripts, version control workflow |
| `devops-engineer` | 构建/部署 | Haiku | CI/CD、构建脚本、版本控制工作流 |
| `analytics-engineer` | Telemetry | Sonnet | Event tracking, dashboards, A/B test design |
| `analytics-engineer` | 遥测 | Sonnet | 事件追踪、仪表盘、A/B 测试设计 |
| `ux-designer` | UX flows | Sonnet | User flows, wireframes, accessibility, input handling |
| `ux-designer` | UX 流程 | Sonnet | 用户流程、线框图、无障碍、输入处理 |
| `prototyper` | Rapid prototyping | Sonnet | Throwaway prototypes, mechanic testing, feasibility validation |
| `prototyper` | 快速原型 | Sonnet | 一次性原型、机制测试、可行性验证 |
| `security-engineer` | Security | Sonnet | Anti-cheat, exploit prevention, save encryption, network security |
| `security-engineer` | 安全 | Sonnet | 反作弊、漏洞防护、存档加密、网络安全 |
| `accessibility-specialist` | Accessibility | Haiku | WCAG compliance, colorblind modes, remapping, text scaling |
| `accessibility-specialist` | 无障碍 | Haiku | WCAG 合规、色盲模式、重映射、文本缩放 |
| `live-ops-designer` | Live operations | Sonnet | Seasons, events, battle passes, retention, live economy |
| `live-ops-designer` | 在线运营 | Sonnet | 赛季、活动、战斗通行证、留存、在线经济 |
| `community-manager` | Community | Haiku | Patch notes, player feedback, crisis comms, community health |
| `community-manager` | 社区 | Haiku | 补丁说明、玩家反馈、危机沟通、社区健康 |

## Engine-Specific Agents (use the set matching your engine)
## 引擎特定智能体（使用与你的引擎匹配的集合）

### Engine Leads
### 引擎负责人

| Agent | Engine | Model | When to Use |
| 智能体 | 引擎 | 模型 | 使用时机 |
| ---- | ---- | ---- | ---- |
| `unreal-specialist` | Unreal Engine 5 | Sonnet | Blueprint vs C++, GAS overview, UE subsystems, Unreal optimization |
| `unreal-specialist` | Unreal Engine 5 | Sonnet | Blueprint vs C++、GAS 概览、UE 子系统、Unreal 优化 |
| `unity-specialist` | Unity | Sonnet | MonoBehaviour vs DOTS, Addressables, URP/HDRP, Unity optimization |
| `unity-specialist` | Unity | Sonnet | MonoBehaviour vs DOTS、Addressables、URP/HDRP、Unity 优化 |
| `godot-specialist` | Godot 4 | Sonnet | GDScript patterns, node/scene architecture, signals, Godot optimization |
| `godot-specialist` | Godot 4 | Sonnet | GDScript 模式、节点/场景架构、信号、Godot 优化 |

### Unreal Engine Sub-Specialists
### Unreal Engine 子专家

| Agent | Subsystem | Model | When to Use |
| 智能体 | 子系统 | 模型 | 使用时机 |
| ---- | ---- | ---- | ---- |
| `ue-gas-specialist` | Gameplay Ability System | Sonnet | Abilities, gameplay effects, attribute sets, tags, prediction |
| `ue-gas-specialist` | Gameplay Ability System | Sonnet | 能力、游戏效果、属性集、标签、预测 |
| `ue-blueprint-specialist` | Blueprint Architecture | Sonnet | BP/C++ boundary, graph standards, naming, BP optimization |
| `ue-blueprint-specialist` | Blueprint 架构 | Sonnet | BP/C++ 边界、图标准、命名、BP 优化 |
| `ue-replication-specialist` | Networking/Replication | Sonnet | Property replication, RPCs, prediction, relevancy, bandwidth |
| `ue-replication-specialist` | 网络/复制 | Sonnet | 属性复制、RPC、预测、相关性、带宽 |
| `ue-umg-specialist` | UMG/CommonUI | Sonnet | Widget hierarchy, data binding, CommonUI input, UI performance |
| `ue-umg-specialist` | UMG/CommonUI | Sonnet | 控件层级、数据绑定、CommonUI 输入、UI 性能 |

### Unity Sub-Specialists
### Unity 子专家

| Agent | Subsystem | Model | When to Use |
| 智能体 | 子系统 | 模型 | 使用时机 |
| ---- | ---- | ---- | ---- |
| `unity-dots-specialist` | DOTS/ECS | Sonnet | Entity Component System, Jobs, Burst compiler, hybrid renderer |
| `unity-dots-specialist` | DOTS/ECS | Sonnet | 实体组件系统、Jobs、Burst 编译器、混合渲染器 |
| `unity-shader-specialist` | Shaders/VFX | Sonnet | Shader Graph, VFX Graph, URP/HDRP customization, post-processing |
| `unity-shader-specialist` | 着色器/VFX | Sonnet | Shader Graph、VFX Graph、URP/HDRP 定制、后期处理 |
| `unity-addressables-specialist` | Asset Management | Sonnet | Addressable groups, async loading, memory, content delivery |
| `unity-addressables-specialist` | 资源管理 | Sonnet | Addressable 组、异步加载、内存、内容分发 |
| `unity-ui-specialist` | UI Toolkit/UGUI | Sonnet | UI Toolkit, UXML/USS, UGUI Canvas, data binding, cross-platform input |
| `unity-ui-specialist` | UI Toolkit/UGUI | Sonnet | UI Toolkit、UXML/USS、UGUI Canvas、数据绑定、跨平台输入 |

### Godot Sub-Specialists
### Godot 子专家

| Agent | Subsystem | Model | When to Use |
| 智能体 | 子系统 | 模型 | 使用时机 |
| ---- | ---- | ---- | ---- |
| `godot-gdscript-specialist` | GDScript | Sonnet | Static typing, design patterns, signals, coroutines, GDScript performance |
| `godot-gdscript-specialist` | GDScript | Sonnet | 静态类型、设计模式、信号、协程、GDScript 性能 |
| `godot-csharp-specialist` | C# / .NET | Sonnet | .NET patterns, [Signal] delegates, async, nullable types, type-safe node access |
| `godot-csharp-specialist` | C# / .NET | Sonnet | .NET 模式、[Signal] 委托、async、可空类型、类型安全节点访问 |
| `godot-shader-specialist` | Shaders/Rendering | Sonnet | Godot shading language, visual shaders, particles, post-processing |
| `godot-shader-specialist` | 着色器/渲染 | Sonnet | Godot 着色语言、可视化着色器、粒子、后期处理 |
| `godot-gdextension-specialist` | GDExtension | Sonnet | C++/Rust bindings, native performance, custom nodes, build systems |
| `godot-gdextension-specialist` | GDExtension | Sonnet | C++/Rust 绑定、原生性能、自定义节点、构建系统 |
