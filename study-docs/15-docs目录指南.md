# 15 · docs 目录指南

## 这篇文档解决什么问题

前 14 篇讲的都是 `.claude/` 下的"编排骨架"（agents / skills / hooks / rules）。这篇专门讲项目根目录下的 [docs/](file:///workspace/docs/) ——它是**项目自己的"档案室 + 操作手册 + 引擎字典 + 案例库"**。

读完这篇你会明白：

- `docs/` 里每个文件是干什么的（作用）
- 怎么用它（如何使用）
- 为什么需要它（为什么要用）
- 在 7 阶段开发流程的什么时候会碰到它（开发流程中何时被使用）

---

## 一句话定位

> **`docs/` 存的不是代码，而是"让 AI 团队不犯傻、不遗忘、不跑偏"的三类参考资料：写作规范、引擎版本快照、真实会话案例。**

---

## 类比：把 `docs/` 想象成一家公司的"资料中心"

想象你刚入职一家游戏公司，行政带你参观"资料中心"，里面分几个区：

| 资料中心分区 | 对应 `docs/` 里的文件 | 你什么时候去 |
|------------|---------------------|-------------|
| 门口贴的"写作须知" | [CLAUDE.md](file:///workspace/docs/CLAUDE.md) | 你要往这个目录写文件前 |
| 公司的"协作行为准则"小册子 | [COLLABORATIVE-DESIGN-PRINCIPLE.md](file:///workspace/docs/COLLABORATIVE-DESIGN-PRINCIPLE.md) | 想搞清楚"AI 该怎么跟我配合" |
| 《从立项到上市操作手册》厚书 | [WORKFLOW-GUIDE.md](file:///workspace/docs/WORKFLOW-GUIDE.md) | 想知道"现在该干啥、下一步干啥" |
| 编号台账 + 架构登记本 | [architecture/tr-registry.yaml](file:///workspace/docs/architecture/tr-registry.yaml)、[registry/architecture.yaml](file:///workspace/docs/registry/architecture.yaml) | 写架构决策、写故事时查编号 |
| 三本"引擎字典"（Godot / Unity / Unreal） | [engine-reference/](file:///workspace/docs/engine-reference/) | AI 要写引擎 API 代码前，先查这里防过期 |
| "老员工怎么干的"案例库 | [examples/](file:///workspace/docs/examples/) | 第一次用某个技能，先看别人怎么用 |

记住这张表，下面逐个拆。

---

## 全景地图

```
docs/
├── CLAUDE.md                          # 目录级写作规范（写 ADR / 引擎参考时遵守）
├── COLLABORATIVE-DESIGN-PRINCIPLE.md  # 协作设计原则（AI 是顾问，不是自动驾驶）
├── WORKFLOW-GUIDE.md                  # 7 阶段完整工作流指南（从零到发布）
├── architecture/
│   └── tr-registry.yaml               # 技术需求 ID 注册表（TR-ID 永久编号）
├── registry/
│   └── architecture.yaml              # 架构决策注册表（跨 ADR 约束的"事实之源"）
├── engine-reference/
│   ├── README.md                      # 引擎参考目录的说明
│   ├── godot/                         # Godot 4.6 版本快照
│   ├── unity/                         # Unity 6.3 LTS 版本快照
│   └── unreal/                        # Unreal Engine 5.7 版本快照
└── examples/
    ├── README.md                      # 案例索引
    ├── skill-flow-diagrams.md         # 技能串联的视觉流程图
    ├── session-*.md                   # 8 个真实会话记录
    └── reverse-document-workflow-example.md  # 从代码反推设计文档的示例
```

---

## 逐个讲解

### 1. `CLAUDE.md` —— 目录级写作规范

**作用**：这是贴在 `docs/` 目录门口的"写作须知"。它告诉 AI（和人类）：在这个目录下写或改文件时，必须遵守哪些格式标准。主要管两类东西——**架构决策记录（ADR）** 和 **引擎参考文档**。

**关键内容**（来自 [docs/CLAUDE.md](file:///workspace/docs/CLAUDE.md)）：

- ADR 必须用 `.claude/docs/templates/architecture-decision-record.md` 模板
- ADR 必填章节：Title / Status / Context / Decision / Consequences / ADR Dependencies / Engine Compatibility / GDD Requirements Addressed
- ADR 状态生命周期：`Proposed` → `Accepted` → `Superseded`，**不能跳过 `Accepted`**（引用 `Proposed` ADR 的故事会被自动封禁）
- TR Registry 的 ID 永久不能重新编号，只能追加
- 写完一批 ADR 后要跑 `/architecture-review` 校验

**如何使用**：你不用主动"运行"它。Claude Code 进入这个目录工作时会自动读到它（CLAUDE.md 是 Claude Code 的"目录级配置文件"，子目录的 CLAUDE.md 会覆盖父目录的）。你只要知道：**当 AI 帮你创建 ADR 或引擎参考文件时，它会自动按这里的规矩来**。

**为什么要用**：没有它，每个 ADR 长得都不一样，后续的 `/architecture-review`、`/create-stories` 都没法自动解析。统一格式 = 自动化能跑起来的前提。

**开发流程中何时被使用**：**Phase 3（技术搭建）** 最密集——这是 ADR 集中产出的阶段。Phase 5（生产）写故事引用 ADR 时也会间接触发。

---

### 2. `COLLABORATIVE-DESIGN-PRINCIPLE.md` —— 协作设计原则

**作用**：这是整套系统的"宪法"——**规定 AI 是"协作顾问"，不是"自动驾驶生成器"**。用户是创意总监，做最终决定；AI 负责提问、研究、给选项、解释权衡、起草草案，**写文件前必须先问"可以写到这个路径吗？"**。

**核心模式**（来自 [docs/COLLABORATIVE-DESIGN-PRINCIPLE.md](file:///workspace/docs/COLLABORATIVE-DESIGN-PRINCIPLE.md)）：

```
Question（提问）→ Options（给选项）→ Decision（你决定）→ Draft（起草）→ Approval（批准才写）
```

**如何使用**：这个文件你也不会"运行"。它被嵌入到了：

- 项目根 [CLAUDE.md](file:///workspace/CLAUDE.md) 的"协作协议"章节
- 全部 48 个智能体定义里（强制它们提问 + 求批准）
- 全部技能里（强制写文件前求批准）
- `AskUserQuestion` 工具集成进了 16 个技能（弹出结构化选项 UI）

如果你发现某个 AI 没问你就写了文件，可以提醒它：

> "请遵守 docs/COLLABORATIVE-DESIGN-PRINCIPLE.md 的协作协议"

**为什么要用**：单聊会话里 AI 最容易犯的错就是"自作主张写一堆"。这个原则把"人拍板"固化成流程，避免 AI 猜错你的意图后浪费一整轮重写。它还规定了"分段增量写入"——每写完一节就存盘，长会话崩溃也不丢成果。

**开发流程中何时被使用**：**全程**。任何阶段、任何技能、任何智能体调用都受它约束。它不是某个阶段的产物，而是贯穿所有阶段的"行为底线"。

> 📌 想深入了解这条原则怎么落地，看 [07-协作协议与人在环](file:///workspace/study-docs/07-协作协议与人在环.md)。

---

### 3. `WORKFLOW-GUIDE.md` —— 完整工作流指南

**作用**：这是项目最厚的"操作手册"——**告诉你怎么用一个 Claude Code 会话，从"零想法"一路走到"发布游戏"**。它把整个流程拆成 7 个阶段，每阶段有管线图、具体步骤、要跑的技能、要过的门禁。

**7 个阶段**（来自 [docs/WORKFLOW-GUIDE.md](file:///workspace/docs/WORKFLOW-GUIDE.md)）：

| 阶段 | 名字 | 干什么 |
|------|------|--------|
| Phase 1 | Concept（概念） | 从没想法到有结构化的游戏概念文档 |
| Phase 2 | Systems Design（系统设计） | 把概念拆成一个个系统，每个写一份 GDD |
| Phase 3 | Technical Setup（技术搭建） | 写架构决策 ADR，产出给程序员的规则清单 |
| Phase 4 | Pre-Production（预制作） | 写 UX 规范、做垂直切片、拆故事、规划冲刺 |
| Phase 5 | Production（生产） | 一个个故事实现，循环冲刺直到功能完整 |
| Phase 6 | Polish（打磨） | 性能、平衡、无障碍、音频、视觉打磨 |
| Phase 7 | Release（发布） | 上线检查、补丁说明、协调发布 |

**如何使用**：

- **第一次用这个项目**：从头读一遍，建立全局观
- **日常查"下一步干啥"**：直接跑 `/help`，它会读你的当前阶段，告诉你下一步（不用自己翻这本文档）
- **想看某阶段细节**：跳到对应章节，里面有管线图和具体技能调用顺序
- **想看常见工作流**：翻到文末"Appendix C: Common Workflows"，有 8 个典型场景（"我刚起步没想法"、"我想加个复杂功能"、"发布游戏"等）

**为什么要用**：没有它，73 个技能就是 73 个散装命令，你不知道先跑哪个后跑哪个。这本指南把它们串成"管线"，每个技能的输入是上一个技能的输出。

**开发流程中何时被使用**：**全程**。它是"地图"，`/help` 是"导航"。新手第一次必读，老手偶尔查"这个阶段还要过什么门禁"。

> 📌 这本指南本身就是"导览"，想看它怎么被拆成学习材料，看 [04-Skill技能系统](file:///workspace/study-docs/04-Skill技能系统.md)。

---

### 4. `architecture/tr-registry.yaml` —— 技术需求 ID 注册表

**作用**：给每条 GDD（游戏设计文档）里的技术需求分配一个**永久稳定的 ID**，比如 `TR-combat-001`。这个 ID 串起"GDD 需求 → ADR 决策 → 故事文件"，让它们之间能互相引用而不靠"复制粘贴文字"（文字会改，ID 不变）。

**关键规则**（来自 [docs/architecture/tr-registry.yaml](file:///workspace/docs/architecture/tr-registry.yaml)）：

- ID 格式：`TR-[系统名]-[三位序号]`，如 `TR-combat-001`
- **ID 永久不能重新编号，不能删除**（需求作废了就标 `status: deprecated`）
- 新需求只能在系统列表末尾追加
- 需求文字可以改（加 `revised` 日期），ID 不变
- 需求被替换就标 `status: superseded-by: TR-combat-001`

**谁写谁读**：

| 角色 | 技能 / 时机 |
|------|------------|
| 写入 | `/architecture-review`（追加新条目，绝不覆盖） |
| 读取 | `/create-stories`（把 ID 嵌进故事文件） |
| 读取 | `/story-done`（评审时查当前需求文字） |
| 读取 | `/story-readiness`（验证 TR-ID 存在且有效） |

**如何使用**：你**不手动编辑**这个文件。它由 `/architecture-review` 自动维护。你只要知道：写故事时会看到 `TR-xxx-NNN` 这种引用，那就是这里的 ID。

**为什么要用**：假设 GDD 里写"连击窗口 0.4 秒"，后来改成 0.5 秒。如果故事文件里复制了这句文字，GDD 改了故事不会知道，就出 bug。用 ID 引用，`/story-done` 评审时去注册表查最新文字，永远对得上。

**开发流程中何时被使用**：

- **Phase 2**（写 GDD）后段开始产生需求
- **Phase 3**（`/architecture-review`）首次填充这个文件
- **Phase 4**（`/create-stories`）把 ID 嵌进故事
- **Phase 5**（`/story-done`、`/story-readiness`）反复读它对账

---

### 5. `registry/architecture.yaml` —— 架构决策注册表

**作用**：这是**跨 ADR 约束的"事实之源"（single source of truth）**。单个 ADR 只管一个决策，但有些决策会约束别的系统怎么建——比如"玩家血量归谁管"、"系统间用信号还是直接调用"、"战斗系统分到多少毫秒预算"。这个文件把这些**跨系统的约束**登记下来，让写新 ADR 时能提前发现冲突。

**登记五类东西**（来自 [docs/registry/architecture.yaml](file:///workspace/docs/registry/architecture.yaml)）：

| 类别 | 防止什么冲突 | 举例 |
|------|-------------|------|
| **state_ownership**（状态归属） | 两个 ADR 都说自己拥有同一份状态 = 数据竞争 | `player_health` 归 `health-system`，别人只读 |
| **interfaces**（接口契约） | 一个 ADR 期望信号、另一个期望直接调用 = 集成 bug | 伤害传递用 `damage_dealt` 信号 |
| **performance_budgets**（性能预算） | 各系统预算加起来超过总帧预算 = 不能上线 | 战斗系统分 2.0ms |
| **api_decisions**（API 选择） | 同一目的用了不同引擎 API = 实现不一致 | 物理射线用 `PhysicsServer3D` 不用 `RayCast3D` |
| **forbidden_patterns**（禁止模式） | 新 ADR 不小心用了被禁的反模式 | 禁止直接引用 Autoload 单例名 |

**如何使用**：你**也不手动编辑**。它由 `/architecture-decision` 在 ADR 批准后（Phase 5）自动写入。写新 ADR 前会先读它检查冲突。文件里写得很清楚——支持用 `Grep` 查"谁拥有 player_health"、"战斗系统的预算是多少"。

**为什么要用**：ADR 是一份份独立文档，没有这个注册表，"两个 ADR 都说自己管玩家血量"这种冲突要等到代码集成时才暴露。有了它，`/architecture-review` 在 Phase 4 就能在作者阶段挡住矛盾。

**开发流程中何时被使用**：

- **Phase 3**（`/architecture-decision` 写 ADR 前查它、批 ADR 后写它；`/architecture-review` 拿它当冲突基线）
- **Phase 4**（`/create-stories` 把架构约束嵌进故事）
- **Phase 5**（`/dev-story` 实现时对照已接受的约束）

---

### 6. `engine-reference/` —— 引擎参考文档

**作用**：这是 `docs/` 里**文件最多、也最特殊**的子目录。它解决一个要命的问题：**大模型（LLM）的训练数据有截止日期（目前是 2025 年 5 月），但游戏引擎更新很快，AI 不知道新版本 API 长啥样，会给出过时代码。** 这里的文件就是"版本锁定的引擎 API 快照"，让 AI 写代码前先查"这个版本到底用哪个 API"。

#### 6.1 `engine-reference/README.md` —— 引擎参考的"使用说明"

**作用**：解释这个目录为什么存在、怎么组织、AI 怎么用、什么时候该更新。

**如何使用**：第一次接触引擎参考目录时先读它。它说明了每个引擎子目录的结构（VERSION / breaking-changes / deprecated-apis / current-best-practices / modules）和维护规则（每个文件要有"Last verified"日期、模块文件不超过 150 行）。

**为什么要用**：让维护者知道"怎么写一份合格的引擎参考"——必须有日期、必须给代码示例、必须只记和模型训练数据不同的部分。

#### 6.2 `<engine>/VERSION.md` —— 版本锁定 + 知识缺口警告

**作用**：钉死项目用哪个引擎版本，并明确告诉 AI"你的训练数据覆盖到哪、哪之后的版本你不知道"。

举例（来自 [docs/engine-reference/godot/VERSION.md](file:///workspace/docs/engine-reference/godot/VERSION.md)）：

| 字段 | Godot | Unity | Unreal |
|------|-------|-------|--------|
| 引擎版本 | Godot 4.6 | Unity 6.3 LTS | UE 5.7 |
| 发布日期 | 2026 年 1 月 | 2025 年 12 月 | 2025 年 11 月 |
| LLM 知识截止 | 2025 年 5 月 | 2025 年 5 月 | 2025 年 5 月 |
| 知识缺口 | 4.4 / 4.5 / 4.6 都不知道 | 整个 Unity 6 系列都不知道 | 5.4 / 5.5 / 5.6 / 5.7 都不知道 |

**如何使用**：AI 写引擎代码前先读它确认版本。看到"知识缺口警告"就知道必须查 breaking-changes 和 deprecated-apis。

**为什么要用**：不查这个，AI 会用 Godot 4.3 的 API 写 Godot 4.6 的代码——比如 4.6 把 Jolt 物理引擎设成默认了，AI 还在推荐 GodotPhysics3D。

#### 6.3 `<engine>/breaking-changes.md` —— 版本间破坏性变更

**作用**：列出新版本相对旧版本的"破坏性 API 变更"，按版本和风险等级组织。

举例（来自 [docs/engine-reference/godot/breaking-changes.md](file:///workspace/docs/engine-reference/godot/breaking-changes.md)）：

- Godot 4.5→4.6：Jolt 成默认 3D 物理引擎、Glow 改在色调映射前处理、D3D12 成 Windows 默认
- Godot 4.4→4.5：GDScript 支持可变参数、`@abstract` 装饰器、AccessKit 屏幕阅读器

**如何使用**：AI 跨版本升级或写可能受影响的子系统时查这里。

**为什么要用**：升级引擎版本后，旧代码可能跑不起来或行为变了。这个文件提前预警"哪里会坏"。

#### 6.4 `<engine>/deprecated-apis.md` —— "别用 X，改用 Y"速查表

**作用**：列已废弃的 API 和替代方案，是"查表式"的——AI 如果建议了"废弃"列的 API，必须换成"改用"列。

举例（来自 [docs/engine-reference/godot/deprecated-apis.md](file:///workspace/docs/engine-reference/godot/deprecated-apis.md)）：

| 废弃 | 改用 | 起始版本 |
|------|------|---------|
| `TileMap` | `TileMapLayer` | 4.3 |
| `yield()` | `await signal` | 4.0 |
| `connect("信号", obj, "方法")` | `信号.connect(callable)` | 4.0 |
| `OS.get_ticks_msec()` | `Time.get_ticks_msec()` | 4.0 |
| 嵌套资源用 `duplicate()` | `duplicate_deep()` | 4.5 |

**如何使用**：AI 每次建议引擎 API 前都该查这个表。

**为什么要用**：AI 训练数据里全是旧 API（`yield`、字符串 connect），不查就会写出能跑但已废弃的代码，未来升级就坏。

#### 6.5 `<engine>/current-best-practices.md` —— 模型不知道的新实践

**作用**：记录"模型训练数据之后才出现的新实践"，补充（不是替代）AI 的内置知识。

举例（来自 [docs/engine-reference/godot/current-best-practices.md](file:///workspace/docs/engine-reference/godot/current-best-practices.md)）：

- GDScript 4.5+ 支持可变参数和 `@abstract` 抽象类
- Godot 4.6 Jolt 是默认 3D 物理引擎，2D 物理不变
- Shader Baker 预编译着色器，启动快 20 倍
- `ripgrep` 没有 `gdscript` 类型，必须用 `--glob "*.gd"`（这是个容易踩的坑）

**如何使用**：写新代码时查这里看有没有更好的新写法。

**为什么要用**：AI 会用老办法写能跑的代码，但错过新版本的改进。这个文件让 AI 知道"有更现代的做法"。

#### 6.6 `<engine>/modules/*.md` —— 各子系统速查

**作用**：每个引擎子系统一个文件（约 150 行以内），快速查这个子系统在新版本有啥变化。三个引擎都有这 8 个模块：`rendering` / `physics` / `animation` / `audio` / `input` / `navigation` / `networking` / `ui`。

举例（来自 [docs/engine-reference/godot/modules/physics.md](file:///workspace/docs/engine-reference/godot/modules/physics.md)）：讲 4.6 Jolt 默认、4.5 物理插值重构、Jolt 和 GodotPhysics3D 的对比表。

**如何使用**：做某个子系统的工作时，读对应的 `modules/xxx.md`。

**为什么要用**：breaking-changes 是按"版本"组织的，modules 是按"子系统"组织的。你要做物理就只读物理那篇，不用翻全版本变更。模块文件控制在 150 行内是为了省 AI 的上下文预算。

#### 6.7 `<engine>/plugins/` 和 `PLUGINS.md`（仅 Unity / Unreal）

**作用**：Unity 和 Unreal 有大量"可选包/插件"（Cinemachine、Addressables、GAS、CommonUI 等），这里给每个常用插件一份详细文档。`PLUGINS.md` 是插件索引，`plugins/xxx.md` 是单个插件的深入指南。

**如何使用**：用到某个插件时查对应的 `plugins/xxx.md`。

**为什么要用**：插件 API 变化比引擎核心还快，且模型训练数据覆盖更差。Godot 没有这个目录是因为 Godot 生态以核心引擎为主。

> 📌 引擎参考目录的维护节奏：升级引擎版本后、模型更新知识截止后、发现 AI 把某个 API 写错时，都要更新这里的文件并刷新"Last verified"日期。

---

### 7. `examples/` —— 实战会话示例库

**作用**：这是"老员工怎么干"的案例库——8 个完整的、端到端的真实会话记录，演示协作工作流在实际中长啥样。每个示例都展示 `Question → Options → Decision → Draft → Approval` 的协作模式。

#### 7.1 `examples/README.md` —— 案例索引

**作用**：列出所有示例，按"复杂度 / 类型 / 时长 / 关键时刻 / 你能学到什么"组织，告诉你"第一次用某场景该读哪个"。

**如何使用**：第一次跑某个技能前，先来这里找对应示例读一遍。比如：

- 第一次跑 `/design-system` → 读 [session-design-system-skill.md](file:///workspace/docs/examples/session-design-system-skill.md)
- 第一次接故事 → 读 [session-story-lifecycle.md](file:///workspace/docs/examples/session-story-lifecycle.md)
- 要过阶段门禁 → 读 [session-gate-check-phase-transition.md](file:///workspace/docs/examples/session-gate-check-phase-transition.md)
- 有现成项目要接入 → 读 [session-adopt-brownfield.md](file:///workspace/docs/examples/session-adopt-brownfield.md)

**为什么要用**：光看技能说明不知道实际跑起来啥样。案例库给你"标准答案"，建立心理预期——AI 该问什么、你该怎么批、什么时候该改。

#### 7.2 `examples/skill-flow-diagrams.md` —— 技能串联流程图

**作用**：用 ASCII 流程图画出"从零到发布"的全管线，以及 4 个重点子流程（design-system、故事生命周期、UX 管线、brownfield 接入）的技能串联。**新手上路第一篇就读这个**，能建立全局观。

**如何使用**：先看"Full Pipeline Overview"建立 7 阶段全局观，再按需查子流程图。

**为什么要用**：73 个技能怎么串成管线，文字描述不如一张图清楚。

#### 7.3 `examples/session-*.md` —— 8 个真实会话记录

**作用**：每个文件是一个具体场景的完整会话 transcript（含用户和 AI 的来回对话），展示协作模式怎么落地。

8 个示例覆盖的场景（来自 [examples/README.md](file:///workspace/docs/examples/README.md)）：

| 文件 | 场景 | 演示什么 |
|------|------|---------|
| `session-design-system-skill.md` | 用 `/design-system` 写移动系统 GDD | 分段写入、崩溃恢复、依赖信号 |
| `session-story-lifecycle.md` | 故事从就绪到完成的完整生命周期 | `/story-readiness` 提前抓歧义、延迟条件处理 |
| `session-gate-check-phase-transition.md` | 阶段门禁检查 | 门禁验什么、PASS/CONCERNS/FAIL 怎么判 |
| `session-ux-pipeline.md` | UX 设计→评审→团队实现 | 无障碍在设计期就抓、阻塞 vs 建议区分 |
| `session-adopt-brownfield.md` | 现成项目接入 | 格式合规 vs 文件存在、迁移而非替换 |
| `session-design-crafting-system.md` | 设计制作系统 | AI 怎么用 MDA 理论给选项 |
| `session-implement-combat-damage.md` | 实现战斗伤害计算 | 实现型 AI 怎么先澄清再写 |
| `session-scope-crisis-decision.md` | 范围危机的战略决策 | 领导型 AI 怎么给权衡分析 |

**如何使用**：按需读。第一次做某类工作时读对应示例，看完心里有底了再上手。

**为什么要用**：这是"活的规范"——光读原则文档不知道实际语气和节奏，看案例能学到"AI 该多主动提问、你该怎么回应"。

#### 7.4 `examples/reverse-document-workflow-example.md` —— 从代码反推设计文档

**作用**：单独一个示例，演示"代码已经写了但没设计文档"时怎么用 `/reverse-document` 反推。AI 读代码、推断设计意图、问澄清问题、产出 GDD。

**如何使用**：接手遗留代码、或自己以前写的代码没文档时，参考这个示例。

**为什么要用**：brownfield（现成项目）接入时这是常见场景——代码先于文档存在，需要补文档而不重写代码。

---

## 在开发流程中何时碰到 `docs/`

把上面所有文档映射到 7 阶段开发流程，你大概在什么时候会用到哪个：

| 阶段 | 主要碰到的 `docs/` 文件 | 干什么 |
|------|------------------------|--------|
| **开始前** | [examples/skill-flow-diagrams.md](file:///workspace/docs/examples/skill-flow-diagrams.md)、[WORKFLOW-GUIDE.md](file:///workspace/docs/WORKFLOW-GUIDE.md) | 建立全局观，知道流程长啥样 |
| **Phase 1 概念** | [examples/session-design-crafting-system.md](file:///workspace/docs/examples/session-design-crafting-system.md) | 看设计示例；`/setup-engine` 会初始化 [engine-reference/](file:///workspace/docs/engine-reference/) |
| **Phase 2 系统设计** | [examples/session-design-system-skill.md](file:///workspace/docs/examples/session-design-system-skill.md) | 看怎么写 GDD；需求开始累积，准备进 TR Registry |
| **Phase 3 技术搭建** | [CLAUDE.md](file:///workspace/docs/CLAUDE.md)、[architecture/tr-registry.yaml](file:///workspace/docs/architecture/tr-registry.yaml)、[registry/architecture.yaml](file:///workspace/docs/registry/architecture.yaml)、[engine-reference/](file:///workspace/docs/engine-reference/) | 写 ADR（遵守 CLAUDE.md 格式）、填 TR Registry、填架构注册表、AI 写代码前查引擎参考 |
| **Phase 4 预制作** | [examples/session-ux-pipeline.md](file:///workspace/docs/examples/session-ux-pipeline.md)、[examples/session-adopt-brownfield.md](file:///workspace/docs/examples/session-adopt-brownfield.md) | 看 UX 管线示例；现成项目看接入示例 |
| **Phase 5 生产** | [examples/session-story-lifecycle.md](file:///workspace/docs/examples/session-story-lifecycle.md)、[architecture/tr-registry.yaml](file:///workspace/docs/architecture/tr-registry.yaml)、[engine-reference/](file:///workspace/docs/engine-reference/) | 看故事生命周期示例；故事用 TR-ID 引用；实现时反复查引擎参考 |
| **Phase 6 打磨** | [engine-reference/](file:///workspace/docs/engine-reference/) | 性能优化时查引擎最新 API |
| **Phase 7 发布** | [examples/session-scope-crisis-decision.md](file:///workspace/docs/examples/session-scope-crisis-decision.md) | 战略决策参考；发布主要靠技能而非 docs |
| **全程** | [COLLABORATIVE-DESIGN-PRINCIPLE.md](file:///workspace/docs/COLLABORATIVE-DESIGN-PRINCIPLE.md) | 协作底线，每时每刻生效 |
| **任何阶段卡住** | [WORKFLOW-GUIDE.md](file:///workspace/docs/WORKFLOW-GUIDE.md) 的 Appendix C | 查 8 个常见工作流怎么办 |

**一句话总结**：`docs/` 里**最常被读到的是 `engine-reference/`**（AI 每次写引擎代码都查），**最常被自动维护的是两个 yaml 注册表**（由技能写入），**最该人手通读一遍的是 `WORKFLOW-GUIDE.md` 和 `examples/skill-flow-diagrams.md`**。

---

## 常见误区

**误区 1："这些文档我都要手动写。"**
❌ 错。两个 yaml 注册表由技能自动维护（`/architecture-review`、`/architecture-decision`），你**不要手动编辑**。引擎参考目录由 `/setup-engine` 初始化、人工维护。你主要写的是 ADR 和 GDD（在 `design/` 和 `docs/architecture/` 下）。

**误区 2："CLAUDE.md 是给人类看的。"**
❌ 半对。它**主要给 Claude Code 看**——它是目录级配置，AI 进这个目录工作时会自动读到。人类看它是为了知道"AI 会按什么规矩来"。

**误区 3："引擎参考目录只是文档，不影响代码。"**
❌ 错。它**直接影响 AI 写出的代码质量**。不查它，AI 会用 Godot 4.3 的 `yield()`、Unity 2022 的旧 Input Manager、UE 5.3 的旧材质系统——全是过时的。

**误区 4："examples 只是示例，可读可不读。"**
❌ 错（对新手而言）。examples 是"标准答案"——第一次用某技能前读对应示例，能省掉大量试错。它也是"协作模式的活教材"。

**误区 5："`COLLABORATIVE-DESIGN-PRINCIPLE.md` 是建议，可以不遵守。"**
❌ 错。它已经被嵌入全部 48 个智能体和全部技能里，是**强制行为**。AI 必须提问、必须给选项、必须求批准才写文件。

---

## 小测验

**Q1**：你让 AI 帮你写一段 Godot 4.6 的物理代码，它直接用了 `RayCast3D` 节点。但项目 ADR 规定用 `PhysicsServer3D.space_get_direct_state()`。哪个 `docs/` 文件能让 AI 提前知道这个约束？

<details>
<summary>答案</summary>

[registry/architecture.yaml](file:///workspace/docs/registry/architecture.yaml) 的 `api_decisions` 段——它登记了"physics_raycast 用 PhysicsServer3D，不用 RayCast3D"。`/architecture-decision` 写新 ADR 前会先读它查冲突。

</details>

**Q2**：你升级 Godot 4.5 到 4.6 后，发现旧项目的 Glow 效果变样了。该查哪个文件？

<details>
<summary>答案</summary>

[docs/engine-reference/godot/breaking-changes.md](file:///workspace/docs/engine-reference/godot/breaking-changes.md) 的"4.5 → 4.6"段——它记录了"Glow 现在在色调映射前处理"。

</details>

**Q3**：你跑 `/create-stories` 时，故事文件里出现 `TR-combat-001` 这种引用。这个 ID 在哪定义、谁能改？

<details>
<summary>答案</summary>

在 [architecture/tr-registry.yaml](file:///workspace/docs/architecture/tr-registry.yaml) 定义。由 `/architecture-review` 自动维护。**ID 永久不能改、不能删**——需求作废了标 `deprecated`，被替换了标 `superseded-by`。

</details>

**Q4**：AI 没问你就直接写了文件。你该提醒它哪个文档？

<details>
<summary>答案</summary>

[docs/COLLABORATIVE-DESIGN-PRINCIPLE.md](file:///workspace/docs/COLLABORATIVE-DESIGN-PRINCIPLE.md)——它规定"写文件前必须先问'可以写到这个路径吗？'"。

</details>

---

## 动手

1. 打开 [docs/engine-reference/godot/VERSION.md](file:///workspace/docs/engine-reference/godot/VERSION.md)，看看 LLM 知识截止和当前引擎版本差了几个版本。
2. 打开 [docs/registry/architecture.yaml](file:///workspace/docs/registry/architecture.yaml)，读注释里的示例条目，理解"状态归属"防的是什么冲突。
3. 打开 [docs/examples/skill-flow-diagrams.md](file:///workspace/docs/examples/skill-flow-diagrams.md)，把"Full Pipeline Overview"那张图从头到尾顺一遍，建立 7 阶段全局观。
4. 翻 [docs/WORKFLOW-GUIDE.md](file:///workspace/docs/WORKFLOW-GUIDE.md) 的 Appendix C，找"Workflow 1: 我刚起步没想法"，对照流程图看。

---

## 一句话带走

> **`docs/` = 写作规范（CLAUDE.md）+ 协作宪法（COLLABORATIVE-DESIGN-PRINCIPLE）+ 流程手册（WORKFLOW-GUIDE）+ 两个自动维护的注册表（tr-registry / architecture registry）+ 三本引擎字典（engine-reference）+ 一本案例库（examples）。前三个给人读，后三类给 AI 查。**
