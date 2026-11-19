# technical-preferences.md 完全指南

> 面向初学者：搞清这份“手写技术备忘”什么时候写、写了给谁用、和 `project.yaml` 谁说了算
>
> 配套阅读：[project.yaml完全指南.md](project.yaml完全指南.md)、[setup-engine引擎配置指南.md](setup-engine引擎配置指南.md)、[配置体系与rigor心智模型.md](配置体系与rigor心智模型.md)
>
> 说明：本文是学习材料。运行时真相以 `.claude/docs/effects-map.md`、`.claude/hooks/yaml-helper.sh` 和各技能的 `SKILL.md` 为准；与本文不一致时，按代码执行。

### 阅读路线

- **只有 5 分钟**：第 0、1、6 节。
- **准备跑 `/setup-engine`**：第 2、3 节。
- **想知道“为什么我改了没反应”**：第 6、8 节。
- **想知道某个区块谁在用**：第 5、9 节。
- **想建立整体心智模型**：第 0、7 节 + 第 10 节自测。

---

## 0. 先记住七句话

1. `technical-preferences.md` 是**给人读的技术备忘**，不是机器配置的主来源。
2. 机器配置的主来源是仓库根目录的 `project.yaml`；两者有同一个键时，**`project.yaml` 赢**。
3. 这份文件是 **v1.0 时代的主配置**，v1.1 之后降级为 **legacy fallback（旧版回退）**，但仍然被保留，不是废纸。
4. 它里面有三块**永远不会迁到 `project.yaml`**：Forbidden Patterns（禁止模式）、Allowed Libraries（允许的库）、Architecture Decisions Log（ADR 速查）。
5. 文件里的 `[TO BE CONFIGURED]` 大部分由 `/setup-engine` 一次填好；少数是**故意不填**的，或者要等决定出现时才填。
6. 开发过程中它**很少被自动更新**：主要写入时机是引擎配置、引擎升级，以及人工记录架构约束/依赖；“改配置”在 v1.1 里默认改的是 `project.yaml`。
7. 它的价值不在于“存值”，而在于**三件事**：旧项目还能跑、人有一份可读摘要、有些没有 YAML 对应项的约束只能住在这里。

---

## 1. 它是什么，不是什么

### 1.1 一句话定义

`.claude/docs/technical-preferences.md` 是**项目技术偏好的可读清单**：引擎、语言、平台与输入、命名、性能预算、测试框架、禁止模式、允许的库、专家路由。

它以前是唯一的技术配置源；现在它是**旧版兼容层 + 人工可读摘要 + 三个“无 YAML 等价物”区块的家**。

### 1.2 先看它和 `project.yaml` 的分工

| 维度 | `project.yaml` | `technical-preferences.md` |
|---|---|---|
| 主要读者 | 解析器、钩子、脚本、技能预处理 | 人；技能在 YAML 缺键时回退阅读 |
| 格式 | 结构化 YAML 键值 | Markdown 列表 + 表格 |
| 权威性 | **同键优先** | 仅在 YAML 该键缺失或为空时生效 |
| 写入者 | `/start`、`/setup-engine`、`/settings`、`/gate-check`、迁移脚本 | `/setup-engine`、人工（少数代理会建议写入） |
| 适合放什么 | 会被程序反复读取的事实与流程档位 | 解释性文字、没有 YAML 对应项的约束、速查 |
| 个人偏好 | 白名单键可放 `project.local.yaml` | **没有本地覆盖机制**，改了就是改团队文件 |

一句话记法：

> **`project.yaml` 是教务系统里的正式成绩；`technical-preferences.md` 是老师办公桌上那本手写备注。**
> 两边冲突时，以教务系统为准；教务系统没记的，才翻备注本。

### 1.3 为什么还留着它（历史原因）

这是一个“中途换过主数据库”的项目：

```text
v1.0：technical-preferences.md 是唯一配置源
        │
        ├─ 技能和脚本按 `- **Engine**: Godot 4.6` 这样的行去读
        │
v1.1：project.yaml 成为主配置源
        │
        ├─ /setup-engine 双写：两个文件都写
        ├─ 技能优先读 project.yaml，缺键才回退这份 Markdown
        ├─ /adopt --migrate 把旧值搬进 project.yaml
        └─ 这份 Markdown 保留，不删除
```

所以它同时承担三个角色：

1. **兼容**：没迁移过的老项目，技能仍能从它读到引擎。
2. **可读**：人想快速看“我们技术栈是什么”，看 Markdown 比看 YAML 直观。
3. **兜底**：有些内容（禁止模式、允许的库）在 `project.yaml` 里根本没有对应键，只能放这里。

### 1.4 逐句翻译文件头注释

文件开头有一段注释，值得逐句读懂：

> `project.yaml at the repo root is the machine-readable source of truth for engine, specialists, naming, performance, platform, and testing.framework.`

→ `project.yaml` 是这些内容的**机器可读**事实源：引擎、专家、命名、性能、平台、测试框架。

> `This file is the human-readable LEGACY FALLBACK: agents and skills resolve each key from project.yaml first and fall back here only when the project.yaml key is absent.`

→ 本文件是**人可读的旧版回退**；每个键先看 `project.yaml`，那个键缺失时才看这里。注意是**逐键**，不是“整份文件二选一”。

> `/setup-engine` dual-writes both.

→ `/setup-engine` 会**双写**两个文件。

> `Forbidden patterns and allowed libraries are NOT migrated — they live only in this file.`

→ 禁止模式和允许的库**不迁移**，只住在这里。

> `Populated by /setup-engine; updated as decisions are made.`

→ 由 `/setup-engine` 填充；之后随着决策产生而更新。

---

## 2. 逐节拆解：九个区块分别是什么

这份文件有 9 个 `##` 区块。每一节都按同一组问题来看：

- 它记录什么？
- 现在是什么占位符？
- 谁写、什么时候写？
- 谁读、读来干什么？
- 和 `project.yaml` 是什么关系？

### 2.0 总览表

| 区块 | 记录什么 | 主要写入者 | 现在还是不是“实际载荷” |
|---|---|---|---|
| Engine & Language | 引擎、版本、语言、渲染、物理 | `/setup-engine` | YAML 有键时以 YAML 为准；YAML 缺失时这里是唯一来源 |
| Input & Platform | 目标平台、输入方式、手柄/触屏支持 | `/setup-engine`（**只写这里**） | **是**：`/setup-engine` 不写 `platform.*`，所以这里是默认载荷 |
| Naming Conventions | 类/变量/信号/文件/场景/常量的命名风格 | `/setup-engine` | 双写；YAML 用枚举，这里是散文 |
| Performance Budgets | 目标帧率、帧预算、Draw Call、内存上限 | `/setup-engine`（可选）；人工 | YAML 有 `performance.*` 时以 YAML 为准 |
| Testing | 测试框架、最低覆盖率、必须覆盖的测试 | `/setup-engine`（框架）；人工 | YAML 有 `testing.framework` 时以 YAML 为准 |
| Forbidden Patterns | 绝不允许出现的写法 | **人工**（随架构决策） | **是**：没有 YAML 等价物 |
| Allowed Libraries / Addons | 批准使用的第三方依赖 | **人工**（随依赖引入） | **是**：没有 YAML 等价物 |
| Architecture Decisions Log | ADR 的快速索引 | **几乎无人维护** | 历史遗留；现行 ADR 在 `docs/architecture/` |
| Engine Specialists | 哪类文件交给哪个引擎专家 | `/setup-engine` | YAML 有 `specialists` 块时以 YAML 为准 |

### 2.1 Engine & Language（引擎与语言）

```markdown
- **Engine**: [TO BE CONFIGURED — run /setup-engine]
- **Language**: [TO BE CONFIGURED]
- **Rendering**: [TO BE CONFIGURED]
- **Physics**: [TO BE CONFIGURED]
```

| 字段 | 含义 | 谁来写 | 谁在读 |
|---|---|---|---|
| `Engine` | 引擎名，可带版本，如 `Godot 4.7`、`Unreal Engine 5.4` | `/setup-engine` | 状态栏、`project-state.sh`、`detect-gaps.sh`、`/help`、`workflow-catalog`，以及 `engine_values` 回退 |
| `Language` | 主玩法语言，如 `GDScript`、`C#`、`C++` | `/setup-engine` | `/test-helpers`、`ui-programmer`、`prototyper` 等处回退 |
| `Rendering` | 渲染器，如 `Forward+`、`Mobile`、`Compatibility` | `/setup-engine` | 主要给人看；迁移脚本会搬进 `project.yaml` |
| `Physics` | 物理后端，如 `Jolt`、`PhysX` | `/setup-engine` | 主要给人看；迁移脚本会搬进 `project.yaml` |

两个容易误解的点：

1. **`Engine` 一行同时承载名字和版本。** 迁移脚本取第一个词作引擎名；运行时的 `engine_values` 则把“最后一个、以数字开头的词”当版本，剩下的整段当名字。所以 `Godot 4.7`、`Unreal Engine 5.4`、`Unity 6000.3.23f1` 都能解析；写成 `Godot（最新版）` 这种没有数字的句子，版本就抽不出来。
2. **`[TO BE CONFIGURED]` 会被当成“没配置”。** 不是把它当字符串引擎名去用。所以占位符留着是安全的，不会污染下游。

和 `project.yaml` 的关系：

- `engine.name`、`engine.version`、`engine.language` 在 YAML 里有值时，技能优先用 YAML。
- `engine.rendering`、`engine.physics` 也会迁移，但当前主要由 `project-coherence.sh` 与实际项目文件比对。
- Godot 选 **Both（GDScript + C#）** 时，`project.yaml` 的 `engine.language` 写 `GDScript`（主玩法语言），C# 的使用情况只记录在这份 Markdown 里。

### 2.2 Input & Platform（输入与平台）

```markdown
- **Target Platforms**: [TO BE CONFIGURED — e.g., PC, Console, Mobile, Web]
- **Input Methods**: [TO BE CONFIGURED — e.g., Keyboard/Mouse, Gamepad, Touch, Mixed]
- **Primary Input**: [TO BE CONFIGURED — the dominant input for this game]
- **Gamepad Support**: [TO BE CONFIGURED — Full / Partial / None]
- **Touch Support**: [TO BE CONFIGURED — Full / Partial / None]
- **Platform Notes**: [TO BE CONFIGURED — any platform-specific UX constraints]
```

| 字段 | 含义 | 谁写 | 谁读 |
|---|---|---|---|
| `Target Platforms` | 发行平台 | `/setup-engine` | `/ux-design`、`/ux-review`、`input_methods` 回退 |
| `Input Methods` | 支持的输入方式 | `/setup-engine` | 同上 |
| `Primary Input` | 这个游戏最主要的输入 | `/setup-engine`；体裁未知时可留空 | 同上 |
| `Gamepad Support` | 手柄支持程度 | `/setup-engine` | 同上 |
| `Touch Support` | 触屏支持程度 | `/setup-engine` | 同上 |
| `Platform Notes` | 平台相关的 UX 约束，如“所有 UI 必须支持十字键导航” | 人工 | 主要给人看 |

`/setup-engine` 会用一张映射表推导默认值：

| 平台目标 | Gamepad Support | Touch Support |
|---|---|---|
| 仅 PC | Partial（推荐） | None |
| 主机 | Full | None |
| 移动 | None | Full |
| PC + 主机 | Full | None |
| PC + 移动 | Partial | Full |
| Web | Partial | Partial |

`Primary Input` 依赖体裁：动作/RPG/平台跳跃上主机 → Gamepad；策略/点击/RTS → Keyboard/Mouse；移动 → Touch；跨平台 → 问你。若此时体裁还没定（例如 `minimal` 档的 brief 还没写），规范要求**问你**，并允许你回答“先留 `[TO BE CONFIGURED]`”。

**这一节有一个关键事实：`/setup-engine` 只把它写进 `technical-preferences.md`，不会写 `project.yaml` 的 `platform` 块。** 所以对于新项目，这一节不是“旧版残留”，而是**实际生效的默认来源**。详见 6.4。

一个容易踩的坑：文件头注释说这一节被 `/ux-design`、`/ux-review`、`/test-setup`、`/team-ui`、`/dev-story` 读取。按当前代码核对，真正会在 `platform` 块缺失时回退读这一节的是 `/ux-design`、`/ux-review` 和 `resolve_config` 的 `input_methods` 行；`/test-setup` 只回退读引擎，`/team-ui` 只回退读 UI 专家，`/dev-story` 只回退读专家块。**注释比代码宽，排错时以代码为准。**

### 2.3 Naming Conventions（命名约定）

```markdown
- **Classes**: [TO BE CONFIGURED]
- **Variables**: [TO BE CONFIGURED]
- **Signals/Events**: [TO BE CONFIGURED]
- **Files**: [TO BE CONFIGURED]
- **Scenes/Prefabs**: [TO BE CONFIGURED]
- **Constants**: [TO BE CONFIGURED]
```

| 字段 | 含义 | 示例（Godot GDScript） |
|---|---|---|
| `Classes` | 类/节点脚本命名 | `PascalCase`，如 `PlayerController` |
| `Variables` | 变量命名 | `snake_case`，如 `move_speed` |
| `Signals/Events` | 信号/事件命名 | `snake_case`，如 `health_changed` |
| `Files` | 文件名 | `snake_case`，如 `player_controller.gd` |
| `Scenes/Prefabs` | 场景/预制体命名 | `PascalCase`，如 `Player.tscn` |
| `Constants` | 常量命名 | `SCREAMING_SNAKE_CASE`，如 `MAX_HEALTH` |

和 YAML 的差别很重要：

- 这里写的是**给人看的散文**（`PascalCase`）。
- `project.yaml` 的 `naming.*` 写的是**枚举**（`pascal_case`、`snake_case`、`camel_case`、`screaming_snake`）。
- 枚举会被校验；散文不会。两者由 `/setup-engine` 同时写入，并在 5.5.3 步**互相核对**，不一致就停下报告。
- 这是“同一件事的两种写法”的典型例子：**机器要枚举，人要自然语言**。

读取者包括 `/dev-story`（实现时代码要遵守）、`/create-control-manifest`（把命名规则写进清单）、`/create-architecture`、`/asset-spec`、`/art-bible`、`/vertical-slice`、`/prototype` 等。

### 2.4 Performance Budgets（性能预算）

```markdown
- **Target Framerate**: [TO BE CONFIGURED]
- **Frame Budget**: [TO BE CONFIGURED]
- **Draw Calls**: [TO BE CONFIGURED]
- **Memory Ceiling**: [TO BE CONFIGURED]
```

| 字段 | 含义 | 典型值 |
|---|---|---|
| `Target Framerate` | 目标帧率 | 60 |
| `Frame Budget` | 每帧可用时间 | 16.6 ms（60fps 的倒数） |
| `Draw Calls` | 每帧 Draw Call 上限 | 按引擎/平台给建议 |
| `Memory Ceiling` | 内存上限 | 按目标硬件给 |

写法和别的区块不同：

- `/setup-engine` **会先问你**：现在设默认值，还是以后知道目标硬件再设？
- 选 “Set defaults now” 才填（60fps / 16.6ms / 引擎合适的 Draw Call 上限）；选 “Leave” 就保留占位符。
- 这里的值可以是**带说明的散文**，比如“主机地板 30 FPS；PC 目标 60 FPS”。但迁移脚本只认**裸数字**，遇到散文会写进迁移报告让你手工处理。`project.yaml` 的 `performance.*` 必须是数字。

读取者：`/gate-check`（Polish 门禁对照预算）、`/perf-profile`、`/asset-spec`、`/art-bible`、`/soak-test`、`/create-control-manifest`、`/architecture-review`。`/dev-story` 注入的 `perf_budgets` 行**只读 `project.yaml`**，这是另一个不对称点（见 6.4）。

### 2.5 Testing（测试）

```markdown
- **Framework**: [TO BE CONFIGURED]
- **Minimum Coverage**: [TO BE CONFIGURED]
- **Required Tests**: Balance formulas, gameplay systems, networking (if applicable)
```

| 字段 | 含义 | 谁写 | 谁读 |
|---|---|---|---|
| `Framework` | 测试框架 | `/setup-engine` 建议并询问后写入 | `/test-helpers`（直接读键的只有它） |
| `Minimum Coverage` | 最低覆盖率 | 人工或后续决策 | 迁移时进 `qa.coverage_minimum`；`/gate-check` 在 `qa.level: full` 时用 |
| `Required Tests` | 必须覆盖的测试类别 | 模板预填，不需你改 | 主要给人看 |

引擎默认框架：

| 引擎 | 建议框架 |
|---|---|
| Godot | gdUnit4 |
| Unity | NUnit / Unity Test Framework |
| Unreal | Automation Spec / UE Automation |

一个容易被忽略的一致性要求：这份文件里的 `Framework` 必须和 `project.yaml` 的 `commands.test` 指向同一个框架。Godot 的 `commands.test` 写的是 gdUnit4 自带的命令行 runner；如果这里推荐 GUT、那里配置 gdUnit4，同一次配置就会自相矛盾。所以 `/setup-engine` 把这条写成硬性提醒。

`Required Tests` 那一行**不是占位符**，是模板给的建议清单；不需要“填完”。

### 2.6 Forbidden Patterns（禁止模式）

```markdown
- [None configured yet — add as architectural decisions are made]
```

这一节记录**这个项目绝不允许出现的写法**，例如：

- 用一个全局单例把两个系统硬绑在一起；
- 跨系统直接写对方的状态；
- 每帧轮询代替信号/事件。

它的三个特点：

1. **`/setup-engine` 故意不预填。** 规范明确写着 “Leave as placeholder — do NOT pre-populate”。因为禁止模式应该来自真实的架构决策，不是猜。
2. **它永远不迁到 `project.yaml`。** YAML 没有对应键，迁移脚本也会专门提示这一点。
3. **当前没有技能会自动往这里写。** 它是人工维护的清单；架构决策产生后，由人（或在你确认后由代理建议）追加。

谁在读它：

| 读者 | 用途 |
|---|---|
| `/create-control-manifest` | 把禁止模式**原样复制**进控制清单，成为实现和评审的共同约束 |
| `/architecture-review` | 载入项目标准时读取 |
| `/create-architecture` | 设计架构时作为约束 |
| `/dev-story` | 契约里写明：这是禁止模式的**唯一来源** |
| `/gate-check` | 质量检查时引用 |

换句话说：**它不生效于“配置解析”，而生效于“文档复制”**——控制清单是它真正发挥作用的跳板。

### 2.7 Allowed Libraries / Addons（允许的库/插件）

```markdown
- [None configured yet — add as dependencies are approved]
```

记录**已批准的第三方依赖**。规范里有两条硬规矩：

1. **不要预填。** 项目现在不需要的库，不要提前写进来。
2. **不要投机性添加。** 例如 Steam 集成还没开始，就不要先写 GodotSteam；等真正开始集成时再加。

写入者：

- 人工（依赖被批准时）。
- `godot-csharp-specialist` 代理在文档里要求“每加一个包，就记录到 Allowed Libraries”。

读者：`/create-architecture`、`/create-control-manifest`、`/architecture-review`、`/dev-story`。

它同样**不迁移**，因为“允许哪些库”是人为判断，不是一个固定枚举，也不适合让机器自动合并。

### 2.8 Architecture Decisions Log（ADR 速查）

```markdown
- [No ADRs yet — use /architecture-decision to create one]
```

这一节原本想做“ADR 的一行式索引”。但现状是：

- **没有技能读它。**
- **没有技能写它。**
- 真正的 ADR 在 `docs/architecture/adr-NNNN-*.md`，结构化立场在 `docs/registry/architecture.yaml`。

所以它是**历史遗留的快捷方式**，当前流程不依赖它。你可以把它当成一句提示：想看决策，去 `docs/architecture/`；想新建决策，跑 `/architecture-decision`。

> 学习要点：**“文件里有一节”不等于“这一节在生效”。** 判断一个配置是否真的有用，要找“写入者”和“读取者”，找不到就是装饰。

### 2.9 Engine Specialists（引擎专家路由）

```markdown
- **Primary**: [TO BE CONFIGURED — run /setup-engine]
- **Language/Code Specialist**: [TO BE CONFIGURED]
- **Shader Specialist**: [TO BE CONFIGURED]
- **UI Specialist**: [TO BE CONFIGURED]
- **Additional Specialists**: [TO BE CONFIGURED]
- **Routing Notes**: [TO BE CONFIGURED]
```

这一节告诉技能：**哪类工作该叫哪个专家代理来做**。

| 字段 | 含义 | Godot 示例 |
|---|---|---|
| `Primary` | 主专家，架构与通用引擎问题 | `godot-specialist` |
| `Language/Code Specialist` | 主语言代码评审 | `godot-gdscript-specialist` 或 `godot-csharp-specialist` |
| `Shader Specialist` | 着色器/材质 | `godot-shader-specialist` |
| `UI Specialist` | UI 实现 | `godot-specialist` |
| `Additional Specialists` | 其他专门领域 | `godot-gdextension-specialist` |
| `Routing Notes` | 文字说明，何时叫谁 | 人工可读 |

下面的 `### File Extension Routing` 表格把“文件后缀 → 专家”写成一张表：

| 文件类型 | 交给谁 |
|---|---|
| 主语言游戏代码 | 语言/代码专家 |
| 着色器/材质 | 着色器专家 |
| UI/界面 | UI 专家 |
| 场景/预制体/关卡 | 主专家或对应专家 |
| 原生扩展/插件 | 扩展专家 |
| 通用架构评审 | 主专家 |

需要知道的细节：

- **YAML 里存的是“裸代理名”**，如 `godot-gdscript-specialist`；这份 Markdown 里可以带括号说明，如 `godot-gdscript-specialist (GDScript 评审)`。迁移脚本会把括号说明剪掉，并在报告里提示。
- **实际路由以 `project.yaml` 的 `specialists.*` 为准**；只有整个 `specialists` 块都不存在时，`resolve_config` 才回退读这一节的四个标签。
- `Primary` 不是从这一节优先读的：它先由 `engine.name` 推导（Godot → `godot-specialist`），推不出来才读这里的 `Primary`。
- **这张 File Extension Routing 表没有脚本解析**。它是给人看的路由说明；技能真正选择专家时，看的是 `specialists.*` 和文件后缀。

读取者：`/code-review`、`/architecture-decision`、`/architecture-review`、`/design-review`、`/team-ui`、`/team-combat`、`/team-audio`，以及 `/dev-story` 的专家注入。

---

## 3. 没写完的部分，什么时候写入

### 3.1 一张总表

| 占位符所在区块 | 什么时候写 | 谁写 | 写到哪里 |
|---|---|---|---|
| Engine & Language | 跑 `/setup-engine` 时 | `/setup-engine` | 本文件 + `project.yaml` 的 `engine` 块 |
| Input & Platform | 跑 `/setup-engine` 时 | `/setup-engine` | **只写本文件**；`platform.*` 不会被它写 |
| Naming Conventions | 跑 `/setup-engine` 时 | `/setup-engine` | 本文件 + `project.yaml` 的 `naming` 块 |
| Performance Budgets | 跑 `/setup-engine` 时**你选择现在设默认值** | `/setup-engine` | 本文件；`performance.*` 不会被它写 |
| Testing / Framework | 跑 `/setup-engine` 时确认后 | `/setup-engine` | 本文件；`testing.framework` 不会被它写 |
| Testing / Minimum Coverage | 有明确覆盖率目标时 | 人工或后续决策 | 本文件；迁移时进 `qa.coverage_minimum` |
| Forbidden Patterns | 有架构决策时 | **人工** | 只在本文件 |
| Allowed Libraries | 真正引入依赖时 | **人工**（`godot-csharp-specialist` 会提醒记录） | 只在本文件 |
| Architecture Decisions Log | 基本不再维护 | 无 | 现行 ADR 在 `docs/architecture/` |
| Engine Specialists | 跑 `/setup-engine` 时 | `/setup-engine` | 本文件 + `project.yaml` 的 `specialists` 块 |

### 3.2 `/setup-engine` 一次会填哪些

它不是“一键填满所有占位符”，而是一步一步问、确认、再写：

```text
选引擎、查版本（不猜版本）
问语言 / 2D 还是 3D / 目标平台
写 CLAUDE.md 的技术栈，并加上引擎参考文档的 import
第 5 步：写 technical-preferences.md
├─ Engine & Language        ← 填
├─ Naming Conventions       ← 按引擎/语言填
├─ Input & Platform         ← 按平台表推导，展示给你确认
├─ Performance Budgets      ← 问你要不要现在设默认值
├─ Testing / Framework      ← 建议框架，问你确认
├─ Forbidden Patterns       ← 故意留空
├─ Allowed Libraries        ← 故意留空
└─ Engine Specialists       ← 填，含文件后缀路由表
第 5.5 步：把 engine / specialists / naming / commands 双写进 project.yaml
回读核对：engine.name、engine.language、naming 值必须两边一致，不一致就停下
生成 docs/engine-reference/<engine>/ 下的版本说明书
```

两个关键设计：

- **先写 Markdown，再写 YAML。** 因为 Markdown 是给人确认的那一份；确认完，再把结构化版本落到 YAML。
- **写 YAML 时会停在一个不一致上。** 5.5.3 的回读核对不是可选项：如果 `Engine` 写的是 Godot、`engine.name` 写的是 Unity，技能会报告并停止，而不是继续制造“精神分裂”的配置。

### 3.3 哪些是“故意不填”的

| 内容 | 为什么不自动填 |
|---|---|
| Forbidden Patterns | 禁止什么必须来自真实架构决策，预填等于替你做技术决定 |
| Allowed Libraries | 允许依赖必须来自真实引入，预填会鼓励“提前上库” |
| Architecture Decisions Log | 当前 ADR 体系在 `docs/architecture/` + registry，这节没有自动维护价值 |
| Performance Budgets（可选） | 目标硬件还没定，默认值可能是错的；由你决定“现在设”还是“以后设” |
| Primary Input（有条件） | 体裁还没确定时推导不出来；规范要求问你，允许先留占位符 |

### 3.4 v1.0 老项目迁移时填哪些

跑 `/adopt` 触发迁移脚本时，它会把旧 Markdown 的值搬进 `project.yaml`：

| 旧文件区块 | 新 YAML 键 |
|---|---|
| Engine & Language | `engine.name`、`engine.version`、`engine.language`、`engine.rendering`、`engine.physics` |
| Input & Platform | `platform.targets`、`platform.primary_input`、`platform.gamepad_support`、`platform.touch_support` |
| Naming Conventions | `naming.classes`、`variables`、`constants`、`signals`、`files`、`scenes` |
| Performance Budgets | `performance.target_framerate`、`frame_budget_ms`、`draw_call_limit`、`memory_ceiling_mb` |
| Testing | `testing.framework`、`qa.coverage_minimum`（只收裸数字） |
| Engine Specialists | `specialists.code`、`shader`、`ui`、`additional` |

迁移的几条“不静默丢数据”规则，很能说明这份文件的性格：

- 性能预算写成散文 → 不猜，写进迁移报告，让人手工设数字。
- 专家行带括号说明 → 只保留代理名，原文写进报告。
- 自定义 `##` 区块 → 报告“未迁移，请手工整合”。
- **迁移完成后也不删除这个 Markdown**，因为它还装着禁止模式和允许的库。

### 3.5 以后想改值，改哪里

这是初学者最容易走错的一步：

| 你想改 | 正确做法 | 错误做法 |
|---|---|---|
| 引擎/语言/命名 | 重跑 `/setup-engine`，或 `/settings engine.*` 等 | 只手改 Markdown |
| 平台/输入 | `/setup-engine`（写 Markdown）或 `/settings platform.*`（写 YAML） | 以为 `/setup-engine` 会更新 YAML |
| 性能预算 | `/settings performance.*`（写 YAML） | 只改 Markdown，然后奇怪 `perf_budgets` 还是 unset |
| 测试框架 | `/settings testing.framework=...`（写 YAML） | 只改 Markdown |
| 禁止模式/允许的库 | **直接编辑 Markdown**（没有别的家） | 找 YAML 键（不存在） |

> `/settings` **只写 `project.yaml` 和 `project.local.yaml`，不写这份 Markdown。**
> 所以 v1.1 项目里，Markdown 里的旧值可能逐渐“过期”；通常不造成行为问题，因为 YAML 同键优先。但如果你哪天**删掉** YAML 的键，旧值就会重新浮上来。见 6.6。

---

## 4. 开发全程：什么时候更新它

先给结论：**它不是“每次提交都改”的活文件。** 它只在“技术决策变成长期约束”时更新。

### 4.1 时间线

| 阶段 | 触发动作 | 更新什么 | 为什么 |
|---|---|---|---|
| Concept / Technical Setup | `/setup-engine` | 引擎、语言、平台、命名、测试框架、专家（可选性能预算） | 这些是后续所有技能的路由依据；没有它们，代码生成和评审会“盲飞” |
| Concept | `/brainstorm` | 不更新 | 只写 `design/game-brief.md`；技术配置不因文案变化 |
| Systems Design | `/map-systems`、`/design-system` | 通常不更新 | 设计文档不是技术配置 |
| Systems Design | `/settings platform.*`、`performance.*`、`testing.framework` | **更新 `project.yaml`**，不是这份 Markdown | v1.1 的正规入口是 YAML |
| Pre-Production | `/architecture-decision` | 不自动写；可能促使人往 Forbidden Patterns 加一条 | 决策产生“以后不许这样写”的约束 |
| Pre-Production | `/create-control-manifest` | 不写；**读取**禁止模式并复制进清单 | 让约束从“备忘”变成“实现规则” |
| Pre-Production | `/create-architecture` | 不写；读取命名、性能、禁止模式、允许库 | 架构必须建立在既有约束上 |
| Production | `/dev-story` | 不写；只读 | 实现时遵守，不改团队约束 |
| Production | `/code-review`、`/architecture-review` | 不写；只读 | 评审时对照 |
| Production | 引入新依赖 | **人工**往 Allowed Libraries 加一条 | 依赖批准是一个决策，不是自动结果 |
| Production | 发现某种写法必须禁止 | **人工**往 Forbidden Patterns 加一条 | 同上 |
| Testing | `/test-setup` | 不写 | 脚手架按引擎/框架生成；配置来源在别处 |
| Polish / Release | `/gate-check`、`/perf-profile`、`/soak-test` | 不写；只读 | 门禁对照预算和约束 |
| 引擎升级 | 重跑 `/setup-engine` | 引擎版本、语言、命名、专家可能更新；YAML 同步 | 版本变了，说明书、废弃 API、专家路由都可能变 |
| 引擎参考过期 | `/setup-engine refresh` | **不更新这份 Markdown**，只更新 `docs/engine-reference/` 和日期 | refresh 的职责是“查资料”，不是“改项目配置” |
| 旧项目接入 | `/adopt` + 迁移脚本 | 把旧值搬进 `project.yaml`，保留 Markdown | 兼容，不破坏历史 |

### 4.2 为什么要更新（而不是“更新了好看”）

每一条更新都对应一个**下游行为变化**：

- 加一条 Forbidden Pattern → `/create-control-manifest` 会把它复制进控制清单 → `/dev-story` 的实现简报会带上它 → `/code-review` 会按它判违规。
- 加一个 Allowed Library → 架构评审不再把它当“未批准依赖”。
- 改 Naming → 新代码的命名、控制清单、资产规格都会跟着变。
- 改引擎/版本 → 专家路由、引擎说明书、废弃 API 检查全部换挡。

所以判断“该不该更新”的标准是：

> **这个变化会不会改变以后产物长什么样、或者改变谁来做评审？**
> 会 → 写进配置；只是这次任务的临时想法 → 不写。

### 4.3 什么时候不该动它

- 只想改**自己本机**看到的评审强度 → 用 `project.local.yaml`，不要动团队文件。
- 只是想“试试看某个引擎” → 不要写进项目配置。
- 依赖还没真正引入 → 不要加进 Allowed Libraries。
- 只是一次性的实现选择 → 不要写成 Forbidden Pattern；写进 ADR 或代码注释。

> 这份文件是 **git 跟踪的团队文件**（已确认在版本库中）。你改它，所有人都会受影响；它没有 `project.local.yaml` 那样的个人覆盖层。

---

## 5. 各区块在什么时候被使用

### 5.1 按流程阶段看

| 时机 | 用到哪些区块 | 用来做什么 |
|---|---|---|
| 会话开始 | Engine（判断“引擎是否已配置”）、Engine Specialists | 状态栏提示、决定是否提醒你跑 `/setup-engine` |
| 设计/UX | Input & Platform | 交互规格要覆盖哪些输入方式、手柄/触屏检查 |
| 架构 | Forbidden Patterns、Allowed Libraries、Engine Specialists、Naming、Performance | 架构不能违反既有约束；路由到正确的引擎专家 |
| 实现 | Naming、Engine Specialists、Performance、Forbidden/Allowed、Testing | 代码风格、专家选择、预算、测试框架 |
| 评审 | Engine Specialists、Forbidden Patterns、Naming | 按文件类型选专家；检查是否用了禁止写法 |
| 测试 | Testing / Framework、Engine | 选测试脚手架和 runner |
| 门禁 | Engine、Performance、Naming、Forbidden Patterns | 检查产物是否符合预算和标准 |
| 发布 | Engine、Performance、Platform（经 YAML 或迁移） | 发布清单与性能验收 |
| 排错/状态 | Engine、Input Methods | `project-state.sh`、`statusline.sh`、`detect-gaps.sh`、`/help` |

### 5.2 一张“区块 × 主要消费者”矩阵

| 区块 | 主要消费者 | 消费方式 |
|---|---|---|
| Engine & Language | `engine_values` 行；`project-state.sh`；`statusline.sh`；`detect-gaps.sh`；`/help`；`/test-helpers`；`/ui-programmer`；`/prototyper`；`/localize`；`/review-all-gdds`；`/design-system` | 解析 `Engine:` 行或按标签取值；YAML 缺键时回退 |
| Input & Platform | `/ux-design`；`/ux-review`；`input_methods` 行 | `platform` 块缺失时读取 |
| Naming Conventions | `/dev-story`；`/create-control-manifest`；`/create-architecture`；`/asset-spec`；`/art-bible`；`/vertical-slice`；`/prototype`；`/architecture-review`；`/gate-check` | YAML 缺键时按标签读取 |
| Performance Budgets | `/gate-check`；`/perf-profile`；`/soak-test`；`/asset-spec`；`/art-bible`；`/create-control-manifest`；`/architecture-review` | YAML 缺键时回退 |
| Testing | `/test-helpers`（直接读 `Framework`）；迁移脚本 | 选测试辅助 API / 搬进 `qa.coverage_minimum` |
| Forbidden Patterns | `/create-control-manifest`；`/dev-story`；`/architecture-review`；`/create-architecture`；`/gate-check` | **唯一来源**，复制进控制清单 |
| Allowed Libraries | `/create-architecture`；`/create-control-manifest`；`/architecture-review`；`/dev-story` | 判断依赖是否获批 |
| Architecture Decisions Log | 无 | 历史遗留 |
| Engine Specialists | `/code-review`；`/architecture-decision`；`/architecture-review`；`/design-review`；`/team-ui`；`/team-combat`；`/team-audio`；`specialists` 行 | YAML 无 `specialists` 块时回退 |

### 5.3 一个特别的“流程用途”

除了被技能读值，这份文件还承担一个**流程信号**：

- `project-state.sh`、`statusline.sh`、`detect-gaps.sh` 用 `Engine:` 行判断“这个项目是不是空项目”。
- `workflow-catalog.yaml` 的 `engine-setup` 步骤接受“`project.yaml` 有 `engine.name`”**或**“旧文件有 `Engine:` 行”作为完成条件。
- `/help` 会报告是哪一个来源满足了条件。

这就是“legacy 兼容”的具体含义：**一个没迁移的老项目，不会被当成“从没配置过引擎”而反复催你跑 `/setup-engine`。**

---

## 6. 和 `project.yaml` 的关系：怎么配合

### 6.1 逐键优先，不是整文件优先

这是最重要、也最容易误解的一条。

```text
读 engine.name：
  project.yaml 有 engine.name？ → 用它
  没有？ → 读 technical-preferences.md 的 Engine 行
  还没有？ → 未设置

读 platform.primary_input：
  project.yaml 有 platform.primary_input？ → 用它
  没有（或整个 platform 块都没有）？ → 读 Markdown 的 Primary Input
  还没有？ → 未设置
```

所以完全可能出现这种“混合状态”：

```text
engine.name        ← project.yaml（Unity）
engine.language    ← technical-preferences.md（C#，因为 YAML 没写）
input_methods      ← technical-preferences.md（因为 YAML 没有 platform 块）
specialists.code   ← project.yaml
```

这是**设计允许**的，不是坏掉。它的目的是让迁移可以渐进：你配置了哪部分，就用哪部分。

### 6.2 双写：写哪些键、顺序、校验

`/setup-engine` 的写入范围是固定的四块：

| 写入 `project.yaml` 的块 | 对应 Markdown 区块 |
|---|---|
| `engine` | Engine & Language |
| `specialists` | Engine Specialists |
| `naming` | Naming Conventions |
| `commands`（build/test/run/smoke） | **Markdown 里没有对应区块**；`commands` 只存在于 YAML |

顺序：

1. 先写 `technical-preferences.md`（给人确认的那份）。
2. 再写 `project.yaml`（机器读的那份）。
3. 回读核对 `engine.name`、`engine.language`、`naming.*` 是否一致；不一致就停止并报告。

注意 `commands` 是“单向”的：它只在 `project.yaml`，Markdown 里没有“构建命令”这一节。这也是为什么“看 Markdown 就懂全部技术配置”是不成立的。

### 6.3 三块永久留在 Markdown

| 区块 | 为什么不迁移 |
|---|---|
| Forbidden Patterns | 没有 YAML 等价键；内容是会增长的自然语言清单，不适合固定 schema |
| Allowed Libraries / Addons | 同上；而且它是“人工批准”的清单，不该被自动推导 |
| Architecture Decisions Log | 现行 ADR 体系已在 `docs/architecture/` + registry；这节是历史遗留 |

迁移脚本和应用审计都明确写着：**不删这份 Markdown**，因为它还装着这些内容。

### 6.4 三个“不对称”的真相（最容易踩坑）

`/setup-engine` 只把四块写进 YAML，但 Markdown 有九个区块。于是有三处“Markdown 仍是主载荷”的地方：

| 内容 | `/setup-engine` 写 YAML 吗 | 实际后果 |
|---|---|---|
| Input & Platform | **不写** | 新项目的 `platform.*` 是空的；`input_methods`、`/ux-design`、`/ux-review` 会**回退读 Markdown**。想写 YAML 得用 `/settings platform.*` |
| Performance Budgets | **不写** | 即使你在 `/setup-engine` 选了“现在设默认值”，值也只进 Markdown；`perf_budgets` 行只读 YAML，会显示 `unset`。想让注入行看到，得 `/settings performance.*` |
| Testing / Framework | **不写** | Markdown 有框架名；`project.yaml` 的 `testing.framework` 可能为空，`/test-helpers` 会回退读 Markdown 的 `Framework` |

这不是 bug，而是迁移的“半程状态”：**引擎/专家/命名已经搬到新家，平台/性能/测试还留在旧家。** 理解这一点，就不会被“为什么 YAML 里没有 platform 却还能工作”弄糊涂。

### 6.5 解析器实际是怎么回退的

| 解析行的名字 | 来源规则 |
|---|---|
| `engine` | **只**从 `project.yaml`；没有就报告 “unset (fall back to …technical-preferences.md)” |
| `engine_values` | 名字/版本/语言分别从 YAML 取；缺失的字段回退 Markdown |
| `specialists` | `primary` 由 `engine.name` 推导，推导不出才读 Markdown 的 `Primary`；其余四项只在**整个 `specialists` 块缺失**时回退 Markdown |
| `input_methods` | 只要 `project.yaml` 有**任意** `platform` 子键，就从 YAML 推导；否则读 Markdown 的 `Input Methods` |
| `perf_budgets` | **只**从 `project.yaml` |

另外：

- Markdown 的 `[TO BE CONFIGURED]` 一律视为“未设置”。
- Markdown 的值不做枚举校验；散文、括号说明都能存活。
- YAML 的值会做枚举校验；非法值会被拒绝并写进 `notes`，然后继续往下一层回退。

### 6.6 冲突（split-brain）怎么处理

“精神分裂配置”指两个文件对同一个事实给出不同答案，例如：

```text
technical-preferences.md:  Engine: Godot 4.6
project.yaml:              engine.name: Unity
```

会发生什么：

- 读 `engine.name` 的技能拿 Unity（YAML 优先）。
- 用 `Engine:` 行做“是否已配置”判断的脚本看到 Godot（它们只读 Markdown）。
- 用 `Engine:` 行做回退的字段（某些 YAML 缺键的场景）看到 Godot。

结果就是**同一个项目里两套事实**。处理方式：

1. 优先重跑 `/setup-engine`，让它双写并核对。
2. 或者跑 `/adopt`，让迁移/审计流程指出问题。
3. 手工修时，**两个文件一起改**，不要只改一边。

另一个隐蔽的冲突来自“删除 YAML 键”：

```text
你删掉 project.yaml 的 performance.target_framerate（想“重置”）
        ↓
解析回退到 Markdown 的 Target Framerate
        ↓
一个你以为已经删掉的老值重新生效
```

所以“重置”不是删键，而是**显式写新值**；如果确实要清空，也要同步清理 Markdown 里对应的行。

### 6.7 迁移后为什么不能删

三个理由：

1. 它还装着禁止模式和允许的库（YAML 没地方放）。
2. 没迁移完的字段仍靠它回退（平台、性能、测试）。
3. 旧脚本和流程检查仍把它当作“已配置”的证据。

所以 `--finalize` 会删 `stage.txt`、`review-mode.txt`，但**永远不删** `technical-preferences.md`。

---

## 7. 心智模型：三张卡片 + 一条规则

### 7.1 类比：学校里的四种纸

| 文件 | 类比 | 特点 |
|---|---|---|
| `project.yaml` | 教务系统里的**正式成绩单** | 机器读、格式严格、同键最权威 |
| `technical-preferences.md` | 老师桌上的**手写备注本** | 人读、可以写解释、只在成绩单没记时作准 |
| `docs/engine-reference/<engine>/` | 厂家**说明书** | 讲这个版本能用什么 API、什么被废弃 |
| `CLAUDE.md` | 教室墙上的**总规矩** | 每次会话都加载，负责“让大家知道去看哪本说明书” |

技能的工作方式：

```text
先查成绩单（project.yaml）
   ↓ 没记
再翻备注本（technical-preferences.md）
   ↓ 还没有
用默认值 / 问人 / 报“未配置”
```

### 7.2 一条决策规则：新内容放哪？

```text
这个信息会被程序反复读取，而且团队必须一致吗？
├─ 是 → project.yaml
│        ├─ 只影响我自己本机的体验？ → project.local.yaml（白名单键）
│        └─ 不是 → project.yaml（提交）
└─ 否 → 它是解释性文字、人工清单，或 YAML 没有对应项吗？
         ├─ 是 → technical-preferences.md
         └─ 否 → 它其实是设计/架构产物 → 放 design/ 或 docs/architecture/
```

三个快速例子：

- “我们用 Godot 4.7” → `project.yaml` 的 `engine.*`（同时写 Markdown 作可读摘要）。
- “绝不允许跨系统直接改状态” → Markdown 的 Forbidden Patterns。
- “战斗伤害公式是 `atk * 2 - def`” → 这是设计内容，放 GDD，不是配置。

### 7.3 一条数据流图

```text
              ┌──────────────────────────────┐
              │ project.yaml（主，机读）      │
              └──────────────┬───────────────┘
                             │ 同键优先
                             ▼
  technical-preferences.md ──→ 逐键解析 ──→ 技能 / 钩子 / 脚本
        （回退，人读）             │
                                   ├─ engine_values：缺字段回退
                                   ├─ specialists：整块缺失才回退
                                   ├─ input_methods：无 platform 块才回退
                                   └─ perf_budgets：只看 YAML
```

### 7.4 记住“三个例外”

1. **平台/输入**：`/setup-engine` 只写 Markdown，所以它常常是实际来源。
2. **禁止模式/允许的库**：只住在 Markdown，且靠“复制进控制清单”生效。
3. **ADR 速查**：没人读写，别在这里维护决策。

---

## 8. 常见误解与排错

| 现象 | 真正的原因 | 怎么办 |
|---|---|---|
| 改了 `technical-preferences.md`，行为没变 | `project.yaml` 有同一个键，YAML 优先 | 改 YAML（`/settings`），或删/改 YAML 对应键 |
| 改了 `project.yaml`，技能还用旧值 | 技能正文是**加载时注入**的；已加载的技能不会自动重读 | 重新调用技能或重开会话 |
| `input_methods: unset`，但 Markdown 明明写了 | `project.yaml` 有一个空的 `platform` 块或某个子键 | 检查 `platform` 块；或删掉空块让回退生效 |
| `perf_budgets` 全是 `unset`，但 Markdown 有预算 | `perf_budgets` 只读 `project.yaml` | `/settings performance.target_framerate=60` 等 |
| `specialists` 显示 `unset`，但 Markdown 写了 | `project.yaml` 有 `specialists` 块，里面是 `null` | `null` 表示“明确未设置”；补值或删掉该键 |
| 迁移后两个文件不一致 | 迁移只搬了一部分，且 `/settings` 之后只改 YAML | 重跑 `/setup-engine`，或按 6.6 手工同步 |
| `[TO BE CONFIGURED]` 一直不动 | 该字段是故意不填的，或还没做对应决策 | 看第 3 节总表；禁止模式/允许库本来就要人工加 |
| 引擎升级后专家路由旧了 | 只更新了版本，没重跑配置 | 重跑 `/setup-engine`，不要只改版本号 |
| 评审突然要求重跑 | 评审回执把这份 Markdown 也纳入哈希；编辑它会让上次评审“过期” | 这是预期行为；改完配置重跑相关评审 |
| 禁止模式写了但没人管 | 它不会自动生效，必须被复制进控制清单 | 跑 `/create-control-manifest` |

### 8.1 一条排错顺序

```text
1. 这个键在 project.yaml 里有吗？ → 有：看它的值和来源
2. 没有 → 看 technical-preferences.md 对应标签
3. 还没有 → 是“故意不填”还是“该填没填”？看第 3 节
4. 两个文件值不同 → 按 YAML 优先理解行为，然后修成一致
5. 值对但没反应 → 是不是技能已加载？重开/重跑
```

---

## 9. 速查表

### 9.1 区块速查

| 区块 | 自动写入者 | 关键读者 | 只在这里吗 |
|---|---|---|---|
| Engine & Language | `/setup-engine` | 状态脚本、`engine_values`、测试/本地化技能 | 否（YAML 优先） |
| Input & Platform | `/setup-engine` | `/ux-design`、`/ux-review`、`input_methods` | **实际常是**（YAML 没写） |
| Naming Conventions | `/setup-engine` | 实现、清单、架构、资产 | 否（YAML 优先） |
| Performance Budgets | `/setup-engine`（可选） | 门禁、性能、资产、架构 | 否（YAML 优先） |
| Testing | `/setup-engine`（框架） | `/test-helpers`、迁移 | 否（YAML 优先） |
| Forbidden Patterns | 人工 | 控制清单、实现、架构、评审、门禁 | **是** |
| Allowed Libraries | 人工 | 架构、控制清单、评审、实现 | **是** |
| Architecture Decisions Log | 无 | 无 | **是（但已废弃）** |
| Engine Specialists | `/setup-engine` | 评审、架构、团队技能、`specialists` 行 | 否（YAML 优先） |

### 9.2 命令速查

| 我想…… | 跑什么 | 改哪里 |
|---|---|---|
| 第一次配置引擎 | `/setup-engine` | 两个文件 |
| 改引擎版本 | `/setup-engine` | 两个文件 |
| 只更新引擎资料 | `/setup-engine refresh` | `docs/engine-reference/` |
| 改平台/性能/测试框架 | `/settings <key>=<value>` | `project.yaml` |
| 只改我个人体验 | `/settings --local <key>=<value>` | `project.local.yaml` |
| 加禁止模式 | 直接编辑 | `technical-preferences.md` |
| 加允许的库 | 直接编辑 | `technical-preferences.md` |
| 新建架构决策 | `/architecture-decision` | `docs/architecture/` |
| 把禁止模式变成实现规则 | `/create-control-manifest` | 控制清单 |
| 老项目迁移 | `/adopt` | `project.yaml`（保留 Markdown） |

### 9.3 三个“别做”

- 别只改 Markdown 就期待 v1.1 的技能改行为（YAML 优先）。
- 别为了“重置”删 YAML 键（会露出 Markdown 的旧值）。
- 别把个人偏好写进这份团队文件（没有本地覆盖层）。

---

## 10. 自测：你真的理解了吗

1. `project.yaml` 的 `engine.name` 是 Unity，Markdown 的 `Engine` 是 Godot 4.6。`/code-review` 会叫哪个专家？为什么？
2. 为什么 `/setup-engine` 不在 `project.yaml` 里写 `platform` 块？这会导致什么回退行为？
3. `perf_budgets` 显示 `unset`，但 Markdown 写了 60fps。谁错了？
4. 迁移脚本为什么不肯删这份 Markdown？
5. 哪三个区块永远不会迁到 `project.yaml`？为什么？
6. `[TO BE CONFIGURED]` 在解析时算什么值？
7. 你删掉 `project.yaml` 的 `naming.classes`，会发生什么？
8. 为什么改了这份 Markdown 可能让 `/architecture-review` 要求重跑？

<details>
<summary>参考答案</summary>

1. Unity 专家。`engine.name` 在 YAML 里有值，同键 YAML 优先；但用 `Engine:` 行做“是否已配置”判断的旧脚本仍会看到 Godot，这就是 split-brain。
2. 规范只把 engine/specialists/naming/commands 四块双写；平台只写 Markdown。因此 `input_methods`、UX 技能会在 `platform` 块缺失时回退读 Markdown。
3. 都不是“错”。`perf_budgets` 行按设计只读 YAML；Markdown 的预算靠各个读 `performance.*` 的技能回退。想让注入行看到，就 `/settings performance.*`。
4. 它还装着 Forbidden Patterns 和 Allowed Libraries（YAML 无对应项），并且旧流程仍把它当配置证据。
5. Forbidden Patterns、Allowed Libraries、Architecture Decisions Log。前两个没有 YAML schema 且是人工判断清单；第三个已被 `docs/architecture/` + registry 取代。
6. 视为“未设置”，不会当成引擎名或普通字符串使用。
7. 解析回退到 Markdown 的 `Classes` 值；如果那里也没有，才是未设置。删键不等于清空。
8. 评审回执把这份文件纳入哈希；编辑后，上次评审的输入就变了，工具会提示重跑，避免用旧评审结论背书新配置。

</details>

---

## 11. 健康检查清单

- [ ] `project.yaml` 的 `engine.name` 与 Markdown 的 `Engine` 指向同一个引擎。
- [ ] `project.yaml` 的 `engine.language` 与 Markdown 的 `Language` 一致。
- [ ] 命名在 YAML 是枚举、在 Markdown 是散文，两边含义一致。
- [ ] 如果依赖 `platform.*`，确认它要么在 YAML 里，要么 Markdown 的 Input & Platform 已填。
- [ ] 如果依赖 `perf_budgets` 注入，确认 `performance.*` 在 YAML 里。
- [ ] `testing.framework` 与 `commands.test` 指向同一个框架。
- [ ] Forbidden Patterns 不是空的（至少有一条真实约束）。
- [ ] Allowed Libraries 里的每一条都真的在用。
- [ ] 不在这里维护 ADR 列表；决策去 `docs/architecture/`。
- [ ] 改完配置后，重跑受影响的评审/门禁。

---

## 12. 进一步阅读

| 想了解 | 看哪里 |
|---|---|
| `project.yaml` 每个键的取值、默认、读写者 | [project.yaml完全指南.md](project.yaml完全指南.md) 第 14 节 |
| 配置解析顺序、来源标签、失败模式 | `.claude/docs/config-resolution.md` |
| 每个设置的完整效果、保留未实现项 | `.claude/docs/effects-map.md` |
| `/setup-engine` 的完整步骤 | `.claude/skills/setup-engine/SKILL.md` 及其 `references/5-technical-preferences.md`、`references/5.5-project-yaml.md` |
| v1.0 → v1.1 迁移细节 | `.claude/scripts/migrate-v1-config.sh`、`/adopt` 技能 |
| 引擎配置实操 | [setup-engine引擎配置指南.md](setup-engine引擎配置指南.md) |

---

## 最后一段话

把这份文件当成**一本会被自动续写的备注本**，而不是“配置文件”：

- 配置的正式答案在 `project.yaml`；
- 备注本负责解释、兜底、以及收纳那些机器不需要读、但人必须遵守的约束；
- 它什么时候更新，取决于“有没有产生一个会改变以后产物的技术决策”，而不是取决于“有没有新任务”。

记住这条主线，你就不会在“到底改哪个文件”上迷路。
