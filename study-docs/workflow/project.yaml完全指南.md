# project.yaml 完全指南

> 面向初学者：从一张配置表理解整个 CCGS 项目
>
> 配套阅读：[配置体系与rigor心智模型.md](配置体系与rigor心智模型.md)、[7阶段逐阶指南.md](7阶段逐阶指南.md)、[setup-engine引擎配置指南.md](setup-engine引擎配置指南.md)
>
> 说明：本文是学习材料。运行时真相以 `.claude/docs/effects-map.md` 和 `.claude/hooks/yaml-helper.sh` 为准；两者与本文不一致时，按代码与效果表执行。

### 阅读路线

- **只有 10 分钟**：第 0、1、3 节 + 11.1 + 第 12 节。
- **准备改配置**：第 4、8 节 + 第 11 节。
- **配置不生效，来排错**：第 6、9 节。
- **想理解项目架构**：第 2、6、10 节。
- **想搞懂技术栈**：第 13 节。
- **想知道旧技术栈文件怎么装进 YAML、会不会漏**：13.13。
- **想查某个键的取值/默认/谁读谁写**：第 14 节。

---

## 0. 先记住五句话

1. `project.yaml` 是**项目级、团队共享、机器可读**的配置，不是代码，也不是设计文档。
2. 它只回答两类问题：**这个项目是什么**（引擎、平台、阶段），以及**流程要跑多重的档位**（严谨度、评审、自动化、文档密度）。
3. 你在技能里看到的配置值，不是技能自己去 YAML 里逐行找的，而是启动时由 `yaml-helper.sh` **一次性解析好、带上来源标签**送进去的。
4. 解析顺序是：`project.local.yaml` → `project.yaml` → 旧版文本镜像 → `modes.rigor` 派生 → 默认值。**显式值永远优先于派生值。**
5. 当前仓库里的 `project.yaml` 很空，是**故意的**：它是模板种子，真正的值由 `/start`、`/setup-engine`、`/settings` 和 `/gate-check` 逐步写入。

---

## 1. project.yaml 是什么，不是什么

### 1.1 一句话定义

`project.yaml` 是这个项目的**结构化配置单一事实源**（single source of truth for project settings）。

它把“这个项目的事实”和“我们希望流程怎么跑”变成一组稳定的键值，让 49 个子代理、几十个技能、若干钩子和脚本在每次工作时，都能拿到同一套答案。

### 1.2 边界表

| 它是 | 它不是 |
|---|---|
| 团队共享的配置（提交进 Git） | 个人偏好文件（那是 `project.local.yaml`） |
| 结构化、可解析的键值 | 长篇设计文档（GDD、架构文档、美术圣经） |
| 项目事实与流程档位 | 运行时状态（当前故事、检查点、日志） |
| 人类可读、也可手改的 YAML | 密钥仓库（API key、token 永远不要放这里） |
| 技能行为的输入 | 技能本身（行为写在 `SKILL.md` 和代理定义里） |

### 1.3 为什么是 YAML，而不是 JSON 或数据库

- 能写注释：模板里大量“为什么这样设计”的说明，只有 YAML 放得自然。
- 能分层：`modes.review_mode`、`testing.strict.logic` 这种点路径直观。
- 能手改：出问题时可以直接打开看，不需要工具链。
- 对 shell 友好：仓库里的解析器是自带的轻量解析器，不依赖外部 YAML 库。

注意：它用的是**仓库自带的简化解析器**，不是完整 YAML 实现。后面“YAML 边界”一节会讲哪些写法不要用。

### 1.4 谁在消费它

| 消费者 | 典型行为 | 例子 |
|---|---|---|
| 技能（skills） | 启动时把解析结果注入技能正文 | `/dev-story` 读自动化、QA 级别、引擎与专家路由 |
| 钩子（hooks） | 在会话开始、读写文件、压缩上下文时读取 | `session-start.sh` 读阶段、引擎、会话状态、更新检查 |
| 脚本（scripts） | 做校验、推进阶段、写配置、跑一致性检查 | `config-set.sh`、`set-stage.sh`、`project-coherence.sh` |

---

## 2. 设计思路：它为什么长这样

理解一个配置文件，最好的方式不是背键名，而是理解它背后的几个决定。

### 2.1 单一事实源，但不吞掉一切

结构化、会被程序反复读取的事实放进 `project.yaml`；需要人读的长文仍然留在 Markdown 产物里。

例如：

- “用 Godot 4.7、GDScript” → `engine.*`（配置）
- “战斗系统的详细规则” → `design/gdd/combat.md`（产物）
- “这个项目现在在 Production 阶段” → `project.stage`（配置）
- “本 sprint 做哪几个故事” → `production/epics/...`（产物）

配置负责“约束和路由”，产物负责“内容和证据”。

### 2.2 把三类信息分开

| 类别 | 回答的问题 | 典型键 |
|---|---|---|
| 项目属性 | 这个项目**是什么** | `project.*`、`platform.*`、`engine.*`、`specialists.*`、`naming.*` |
| 流程档位 | 流程**怎么跑** | `modes.*`、`docs.*`、`qa.*`、`team.*`、`testing.*`、`performance.*` |
| 运行机制 | 工具**怎么配合** | `commands.*`、`features.*`、`updates.*`、`workflow_overrides` |

这解释了一个常见困惑：为什么 `platform.multiplayer` 不在 `modes` 里？因为它是“游戏事实”，不是“流程偏好”。为什么 `project.stage` 不在 `modes` 里？因为它是生命周期位置，不是工作模式。

### 2.3 预设 + 单点：rigor 是“套餐”，六个键是“单点”

`modes.rigor` 只回答一个问题：**这个项目整体要背多重的流程**。

它不直接生效，而是作为**兜底来源**，一次性供给六个键：

| `modes.rigor` | `modes.workflow` | `docs.density` | `qa.level` | `modes.story_granularity` | `modes.review_mode` | `team.size` |
|---|---|---|---|---|---|---|
| `minimal`（默认） | `minimal` | `terse` | `minimal` | `coarse` | `solo` | `individual` |
| `standard` | `standard` | `balanced` | `standard` | `balanced` | `lean` | `individual` |
| `full` | `full` | `thorough` | `full` | `fine` | `full` | `studio` |

关键规则：

- 你显式写的单点值，**永远赢过** rigor 派生值。
- 单点值可以比 rigor 更松，也可以更严；这是双向的。
- 所以模板里**不能预置**这六个键。预置等于“钉死”，rigor 就再也改不动它们了。

这就是当前模板注释里反复强调“六个键必须保持缺席”的原因。

### 2.4 解析一次，处处使用

技能不应该在正文里写一长串“先看 local，再看 yaml，再看旧文件……”的说明，那样每个技能都要重复实现一遍，而且容易不一致。

所以设计成：**技能加载前，预处理器执行一次解析命令，把结果直接替换进技能正文**。技能看到的是一份“已经算好的答案”，只需要“按原样使用”。

### 2.5 显式 > 派生 > 默认，并且必须能看见来源

每个解析结果都带来源标签：

```
review_mode: lean (project.yaml)
docs.density: balanced (rigor:standard)
automation: autonomous (project.local.yaml)
project.stage: Production (production/stage.txt)
```

这就是 **provenance（来源）**。它解决的是排错问题：当行为不符合预期时，你能立刻知道这个值到底来自哪里，而不是猜“是不是被谁覆盖了”。

### 2.6 团队标准与个人体验分离

判断一个设置能不能被个人覆盖，只有一条规则：

> **会影响磁盘上存在什么产物、或产物长什么样的设置，必须锁定在 `project.yaml`；只影响个人体验的设置，才允许写进 `project.local.yaml`。**

因此：

- `modes.workflow`、`qa.level`、`docs.density`、`engine.*`、`naming.*` → 锁定，团队一致。
- `modes.review_mode`、`modes.automation`、`testing.strict.*`、`performance.enforce`、`team.size` → 个人可覆盖，因为只改变“你本机看到多少评审/提示”，不改变团队产物要求。

这条规则防止一种真实事故：A 在本机把 `qa.level` 调成 `minimal`，故事不带测试证据就标记完成；B 拉下代码后按团队的 `standard` 跑门禁，发现一堆“已完成”的故事缺证据。

### 2.7 失败要可见、可降级、可迁移

- **可见**：非法值会在解析结果的 `notes:` 里被点名，而不是静默生效。
- **可降级**：非法枚举值不会赢，解析会继续往下一层找，最终落到默认值，避免把 `review_mode: chaotic` 传遍所有技能。
- **可迁移**：`schema_version` 让未来的版本知道这份文件该不该迁移；`framework.version` 只记录模板版本，不参与行为。
- **不假装生效**：文档里明确标注 `RESERVED - NOT IMPLEMENTED` 的设置，`/settings` 会告诉你“没人读它”。

### 2.8 配置必须“接线”

一个设置不是“被文档提到”就等于生效。它必须真的有读取者（技能、钩子或脚本）。

仓库里有一份“死设置审计”的思路：如果一个设置失去了读取者，它应该被发现；如果一个设置看起来是配置、实际没人读，`/settings` 应该警告。

所以本指南会把**保留但未实现**的键单独标出来，避免初学者“设了但没反应”。

### 2.9 一张图看懂数据流

```text
project.local.yaml ─┐
project.yaml ───────┼─→ yaml-helper.sh resolve_config ─→ 已解析配置（带来源）
旧版文本镜像 ───────┤                                      │
modes.rigor 派生 ───┤                                      ├─→ 技能正文（预处理注入）
默认值 ─────────────┘                                      ├─→ 钩子（直接调用函数）
                                                           └─→ 脚本（校验/推进/写入）
```

---

## 3. 逐行读当前模板

当前仓库的 `project.yaml` 只有 27 行，但每一行都值得读懂。

```yaml
# CCGS project configuration — single source of truth for project settings.
# See .claude/docs/effects-map.md for full schema.

schema_version: 1

framework:
  version: 1.2.0
  last_upgraded: 2026-10-19

# NOTE: this template ships with no `modes:` block, and the six knobs below must
# never be added to one.
#
# `/start` DOES add `modes.rigor` and `modes.automation` here — that is its job,
# and a `modes:` block containing those two is the expected state of a configured
# project, not a mistake.
#
# What must stay absent is the six knobs `modes.rigor` FRONTS:
# `modes.review_mode`, `modes.workflow`, `docs.density`, `qa.level`,
# `modes.story_granularity` and `team.size`. Seeding any of them in this file
# pins that value and shadows the rigor expansion, so the rigor `/start` infers
# and saves would stop changing anything. `/start` states the same rule for the
# settings it writes.
#
# Per-developer preferences belong in `project.local.yaml` (gitignored), not
# here. `/settings --local <key>=<value>` writes there; `/settings <key>=<value>`
# edits this file. See `.claude/docs/config-resolution.md` for the full
# precedence order.
```

### 逐行解释

| 行 | 含义 | 初学者要注意什么 |
|---|---|---|
| 第 1–2 行 | 文件用途声明 + 完整 schema 入口 | 真正完整的字段说明在 `.claude/docs/effects-map.md` |
| `schema_version: 1` | 这份 YAML 的**结构版本** | 不是框架版本；未来结构变化时用于迁移 |
| `framework.version: 1.2.0` | 哪个 CCGS 模板版本创建/更新了它 | 纯元数据，没有技能根据它分支 |
| `framework.last_upgraded` | 最近一次模板升级日期 | 也只用于记录，不驱动行为 |
| 注释块 | 解释为什么没有 `modes:` | 这是整份模板最重要的设计说明 |
| 没有 `modes:` | 模板故意不预置流程档位 | 配置过的项目通常会有一个只含 `rigor` 和 `automation` 的 `modes:` |
| 没有 `project:` | 阶段由 `/start` 写入；身份字段可选 | 空模板不代表配置损坏 |

### 为什么“没有 modes”不是错误

- `/start` 会写入 `modes.rigor` 和 `modes.automation`，所以配置过的项目通常会看到：

```yaml
modes:
  rigor: minimal
  automation: guided
```

- 但这六个键**不能**预置：`modes.review_mode`、`modes.workflow`、`docs.density`、`qa.level`、`modes.story_granularity`、`team.size`。
- 一旦预置，它们就成了显式值，会盖住 rigor 的派生。结果就是：你改 `rigor` 感觉“没反应”。

### 一个重要区分

`schema_version` 和 `framework.version` 是两条独立的版本轴：

- `schema_version`：**文件结构**的版本，决定是否要迁移。
- `framework.version`：**框架模板**的版本，决定这份文件来自哪一版 CCGS。

它们不需要相等。当前模板就是 `schema_version: 1`、`framework.version: 1.2.0`。

---

## 4. 配置全景：每个键讲清楚

### 4.1 先记住命名规律

| 前缀 | 回答什么 | 例子 | 是否流程模式 |
|---|---|---|---|
| `project.*` | 项目身份与生命周期 | `project.stage` | 不是 |
| `platform.*` | 发行平台与联网事实 | `platform.targets`、`platform.cert_tier` | 不是 |
| `engine.*` | 引擎技术事实 | `engine.name`、`engine.version` | 不是 |
| `specialists.*` | 引擎专家路由表 | `specialists.code` | 不是 |
| `naming.*` | 代码命名约定 | `naming.classes` | 不是 |
| `modes.*` | 流程怎么跑 | `modes.workflow`、`modes.automation` | 是 |
| `docs.*` | 文档写多深 | `docs.density` | 是 |
| `qa.*` | 质量证据要求 | `qa.level` | 是 |
| `testing.*` | 测试框架与失败是否阻断 | `testing.strict.*` | 是 |
| `team.*` | 默认启用多少代理 | `team.size` | 是 |
| `performance.*` | 性能预算与超限处理 | `performance.enforce` | 是 |
| `commands.*` | 构建/测试/运行/冒烟命令 | `commands.test` | 不是 |
| `features.*` | 工具链功能开关 | `features.session_state` | 不是 |
| `updates.*` | 更新提示 | `updates.check` | 不是 |
| `workflow_overrides` | 对 workflow 的例外 | `system_overrides` | 是（例外） |

一个快速判断法：

- 如果它描述“游戏/项目本身”，多半是 `project`、`platform`、`engine`。
- 如果它描述“我们希望 AI 怎么工作”，多半是 `modes`、`docs`、`qa`、`team`、`testing`。
- 如果它描述“怎么运行这个仓库”，多半是 `commands`、`features`、`updates`。

### 4.2 `schema_version`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `schema_version` | 这份 `project.yaml` 的结构版本 | 整数；默认 `1` | `/start`、`/adopt` 写；所有解析器和迁移逻辑读 |

行为：

- 等于当前期望版本：正常运行。
- 低于期望版本：`/adopt` 提示迁移，技能可能带弃用警告。
- 高于期望版本：所有技能警告“这份文件来自更新版本，部分设置可能被忽略”。
- 缺失：按 legacy（v1.1 之前）处理，下次 `/start` 或 `/adopt` 提示迁移。

### 4.3 `framework`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `framework.version` | 创建/最近更新这份文件的 CCGS 模板版本 | 语义化版本字符串 | 模板创建时写；`/start`/`/setup-engine`/`/gate-check` 在新建 `project.yaml` 时写；迁移脚本写；没有技能根据它分支 |
| `framework.last_upgraded` | 最近一次模板升级日期 | ISO 日期 | 同上 |

这是**文件元数据**，不是行为配置。不要指望改它来改变流程。

### 4.4 `project`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `project.name` | 项目名 | 字符串；可选 | 主要给人/工具看；当前几乎没有技能依赖 |
| `project.genre` | 类型标签（如 ARPG） | 字符串；可选 | 元数据 |
| `project.version` | 项目版本 | 字符串；可选 | 元数据 |
| `project.stage` | 当前开发阶段 | `Concept`、`Systems Design`、`Technical Setup`、`Pre-Production`、`Production`、`Polish`、`Release`；未设置时检测器按 `Concept` 处理 | `/start` 和 `/gate-check` 写；大量技能读 |
| `project.kind` | 项目类型 | `game`、`tool`、`engine`、`mod`、`library`；默认 `game` | **保留未实现**：没有技能读 |

`project.stage` 是这里唯一真正驱动流程的键：

- `/gate-check` 根据它决定要检查哪一道门。
- 门的 PASS 或用户明确接受的 CONCERNS 才会推进阶段。
- FAIL 永远不会推进。
- 它会**双写**到 `production/stage.txt`，供还没迁移的旧钩子读取。
- 阶段还会被 `project-stage-detect`、`help`、`team-release`、`launch-checklist` 等读取。

不要手动改 `project.stage` 来“跳过门禁”。那会让磁盘上的阶段和产物脱节。

### 4.5 `modes`

这是最容易被误解的一块。`modes` 不是“游戏模式”，而是**流程模式**。

#### 4.5.1 `modes` 下的六个键

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `modes.rigor` | 整体流程重量；六个键的预设来源 | `minimal`、`standard`、`full`；默认 `minimal` | `/start`、`/settings` 写；没有技能直接读，都是通过它派生的六键 |
| `modes.review_mode` | 评审时生成多少导演/专家关卡 | `full`、`lean`、`solo`；无终端默认，由 rigor 派生 | `/settings` 或迁移脚本写；大量评审类技能读 |
| `modes.workflow` | 代码开工前要求多少设计文档 | `minimal`、`standard`、`full`；无终端默认，由 rigor 派生 | `/settings` 写；设计、门禁、规划类技能读 |
| `modes.automation` | 技能是“先问再做”还是“边说边做” | `collaborative`、`guided`、`autonomous`；默认 `collaborative` | `/start`、`/settings` 写；几乎所有会写文件/提问的技能读 |
| `modes.automation_always_ask` | 无论自动化多高都必须问的决策类别 | 类别列表；默认 `scope_changes`、`file_deletions`、`schema_changes` | `/settings` 写；遵守 automation 的技能读 |
| `modes.story_granularity` | 故事切多大 | `coarse`、`balanced`、`fine`；无终端默认，由 rigor 派生 | `/settings` 写；故事/冲刺类技能读 |

#### 4.5.2 `rigor` 的心智模型

把 `rigor` 想成**套餐**，把六个键想成**单点**：

- 点套餐：只写 `modes.rigor: standard`，六个键自动变成 `standard` 档。
- 改单点：再写 `modes.workflow: full`，只有 workflow 变成 full，其他仍按 standard 派生。
- 套餐不会覆盖单点：显式值永远优先。

这也是为什么 `/start` 把 rigor 做成一个总问题，而不是问你六个问题。

#### 4.5.3 `review_mode` 与 `workflow` 不是一回事

这两个键最容易被混为一谈：

| 键 | 回答的问题 |
|---|---|
| `modes.review_mode` | 评审时**要不要**生成导演/专家（以及生成多少层） |
| `modes.workflow` | **要求哪些文档/产物存在**，以及门禁要检查哪些产物、导演面板多宽 |

举例：

- `review_mode: solo` 可以跳过评审关卡，但 `workflow: full` 仍然要求完整的 GDD 章节。
- `workflow: minimal` 可以只要求一页 brief，但 `review_mode: full` 仍然可以在你主动评审时生成完整评审团。

`workflow` 的三个值：

| 值 | 代码开工前要求什么 | 适合 |
|---|---|---|
| `minimal` | 一页 `design/game-brief.md` + 选定引擎；brief 里的 build order 就是计划 | 小范围、game jam、设计已在脑中 |
| `standard` | 每个系统 5 节 GDD、一份架构文档、关键 ADR、游戏概念、系统索引 | 已有多个系统交互，或需要别人实现 |
| `full` | 每个系统 8 节 GDD、完整架构、全部 ADR、美术圣经、逐屏 UX 规格 | 团队、商业项目、想走完整流程 |

注意：`workflow` 规定的是**最低要求**，不是**允许范围**。任何技能在任何档位都可以手动调用；它改变的是门禁强制什么，不是你能否做。

`/gate-check` 是一个特例：`full` 和 `lean` 都会跑阶段关卡，只有 `solo` 会跳过；面板宽度由 `workflow` 决定（minimal 1 人、standard 2 人、full 4 人），而不是由 `review_mode` 决定。

`review_mode` 的三个值：

| 值 | 行为 | 适合 |
|---|---|---|
| `full` | 所有导演/专家关卡都生成；评审面板最宽 | 团队、商业项目、想学习完整评审 |
| `lean` | 只跑阶段关卡，技能内部的导演签收跳过 | 大多数项目的平衡点 |
| `solo` | 不生成导演/专家关卡；管线自带的审查仍会跑 | 自信的单人快速迭代 |

不是所有技能都读 `review_mode`：`dev-story`、`code-review`、`architecture-review` 等始终按自己的路由运行，不受这个档位影响。

#### 4.5.4 `automation` 的三个档位

| 值 | 行为 | 适合 |
|---|---|---|
| `collaborative` | 每个多选项都问；每个文件写入前展示草稿并请求批准 | 想完全掌控、学习流程 |
| `guided` | 小决策直接说明并推进；大/不可逆决策才问 | 大多数人的平衡点 |
| `autonomous` | 不问，直接做并记录决策日志；只对 `automation_always_ask` 类别停下 | 快速迭代、信任流程 |

例外：`hotfix`、`gate-check`、`day-one-patch`、`setup-engine` 永远按 `collaborative` 行为，不受 `automation` 影响。原因是紧急修复、门禁结论、发布补丁和一次性引擎选择都需要明确的人工确认。

`automation_always_ask` 是 `autonomous` 的安全网。它支持这些类别：

| 类别 | 典型决策 |
|---|---|
| `scope_changes` | 砍功能、改范围、拆/合并 epic、删除验收标准 |
| `file_deletions` | 删故事、删 GDD、删系统、删测试文件 |
| `schema_changes` | 改 `project.yaml`、故事模板、控制清单、ADR 模板 |
| `architecture_decisions` | 新建/替换 ADR、改系统边界 |
| `version_bumps` | 引擎版本、框架版本、依赖大版本 |
| `external_calls` | 自主模式下调用外部生成服务 |

注意：修改 `project.yaml`、故事模板、控制清单、ADR 模板属于 `schema_changes`；这类结构变更即使在 `autonomous` 下也会默认停下确认。`/settings` 写入前同样会展示变更并请求批准。

#### 4.5.5 本机覆盖规则

- `modes.review_mode`、`modes.automation`、`modes.automation_always_ask`、`team.size`：**可以**写进 `project.local.yaml`。
- `modes.rigor`、`modes.workflow`、`docs.density`、`qa.level`、`modes.story_granularity`：**锁定**，只能写在 `project.yaml`。

原因还是那条：会改变磁盘产物的，不能各自为政。

#### 4.5.6 `story_granularity` 的三个值

| 值 | 故事形状 | 每冲刺故事数 | 适合 |
|---|---|---|---|
| `coarse` | 1 个故事 = 1 个功能，5–10 条验收标准 | 2–4 | 单人、原型、“我知道要做什么” |
| `balanced` | 1 个故事 = 1 个任务，2–4 条验收标准 | 6–10 | 大多数项目 |
| `fine` | 1 个故事 = 1 条验收标准，便于逐条评审 | 15–25 | 团队、学习、需要细致交接 |

它会被 `/create-stories`、`/sprint-plan`、`/sprint-status`、`/story-readiness` 读取。

### 4.6 `workflow_overrides`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `workflow_overrides.edge_cases` | 强制要求 GDD 的 Edge Cases 章节 | `true`/`false`；默认 `false` | `/settings` 写；设计/评审/门禁技能读 |
| `workflow_overrides.tuning_knobs` | 强制要求 Tuning Knobs 章节 | `true`/`false`；默认 `false` | 同上 |
| `workflow_overrides.art_bible_strict` | 不管有没有视觉资产，都要求美术圣经全部 9 节 | `true`/`false`；默认 `false` | 同上 |
| `workflow_overrides.system_overrides` | 给某个系统单独指定 workflow 档位 | 映射：`系统名: minimal/standard/full`；默认空 | `/settings` 写；设计/评审/门禁技能读 |

两个关键点：

1. **它在文档根目录，不在 `modes` 下面。** 嵌套进 `modes:` 仍是合法 YAML，但所有读取者都从根路径读，结果就是“静默不生效”。
2. 三个布尔标志是**加法**（只会更严格）；`system_overrides` 不是加法，它**双向替换**某个系统的档位，可以更松也可以更严。

例子：

```yaml
workflow_overrides:
  edge_cases: true
  system_overrides:
    combat: full
    inventory: minimal
```

含义：全局仍按 `modes.workflow`，但战斗系统按 `full` 要求，背包系统按 `minimal` 要求。

### 4.7 `docs.density`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `docs.density` | 每个文档章节写多深 | `terse`、`balanced`、`thorough`；无终端默认，由 rigor 派生 | `/settings` 写；设计/美术/架构/UX/地图/头脑风暴类技能读 |

它和 `workflow` 的区别：

- `workflow` 决定**哪些章节必须存在**。
- `density` 决定**每个章节写多深**。

组合矩阵：

|  | `density: terse` | `density: balanced` | `density: thorough` |
|---|---|---|---|
| `workflow: minimal` | 要点式一页 brief（默认） | 段落式 brief | brief + 理由 |
| `workflow: standard` | 5 节，要点 | 5 节，段落 | 5 节 + 例子 |
| `workflow: full` | 8 节，要点（全面但紧凑） | 8 节，段落 | 8 节 + 理由 + 例子 |

“全面但紧凑”就是 `workflow: full` + `density: terse`，这是 rigor 单独表达不出来的离对角线组合。

| 值 | 每节写法 |
|---|---|
| `terse` | 要点列表，每节 2–5 行；跳过理由和铺垫 |
| `balanced` | 段落 + 轻量理由；必要时给例子 |
| `thorough` | 完整段落；每个决定都给理由、备选方案、设计历史 |

### 4.8 `testing`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `testing.framework` | 用哪个测试框架 | 引擎默认：Godot `gdunit4`、Unity `unity-test-framework`、Unreal `unreal-automation`；由 `/setup-engine` 自动填 | `/setup-engine`、`/settings` 写；直接读这个键的是 `/test-helpers`。`qa-plan`、`dev-story`、`regression-suite`、`smoke-check` 受框架影响，但按效果表核对并不逐字读这个键 |
| `testing.strict.logic` | 逻辑测试失败是否阻断故事关闭 | `true`/`false` | `/settings` 写；`story-done`、`story-readiness`、`gate-check`、`smoke-check`、`dev-story` 读 |
| `testing.strict.integration` | 集成测试失败是否阻断 | `true`/`false` | 同上 |
| `testing.strict.visual` | 视觉证据缺失/失败是否阻断 | `true`/`false` | 同上 |
| `testing.strict.ui` | UI 截图缺失是否阻断 | `true`/`false` | 同上 |
| `testing.strict.config` | 配置/数据冒烟失败是否阻断 | `true`/`false` | 同上 |

重要细节：

- `testing.strict` 的推荐形态是**映射**，不是单个布尔值。
- 文档意图的默认是：`logic/integration/visual/ui = true`，`config = false`。
- 但解析器**不在这里替技能做默认**，而是输出 `unset`，让每个技能应用自己的默认。
- `smoke-check` 是例外：作为构建健康门，它把未设置的 `testing.strict.config` 当作**严格**，只有显式 `false` 才降为建议。
- 旧写法 `testing.strict: true/false` 仍然被兼容，作为所有类型的兜底。

和 `qa.level` 的区别：

- `qa.level` 决定**要不要提供证据**。
- `testing.strict` 决定**证据失败时阻不阻断**。

如果 `qa.level: minimal`，根本不需要测试证据，strict 映射基本变成空操作。

### 4.9 `qa`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `qa.level` | 标记故事 Done 需要什么测试证据 | `minimal`、`standard`、`full`；无终端默认，由 rigor 派生 | `/settings` 写；`story-done`、`story-readiness`、`gate-check`、`qa-plan`、`team-qa` 等读 |
| `qa.coverage_minimum` | 最低代码覆盖率 | 整数 0–100，或 `null`；默认 `null` | `/settings` 写；只有 `/gate-check` 在 `qa.level: full` 时执行 |

`qa.level` 的三个值：

| 值 | 逻辑/集成故事 | 视觉/UI 故事 | 配置/数据故事 |
|---|---|---|---|
| `minimal` | 不要求测试，也不标记缺口 | 截图（视觉还要主美签收），**从不豁免** | 不要求证据 |
| `standard` | 逻辑：单元测试；集成：集成测试或试玩文档 | 同上 | 冒烟检查，建议性 |
| `full` | 每个验收标准都被测试覆盖 + 回归 | 同上 | 冒烟检查，默认阻断 |

注意：

- 即使 `qa.level: minimal`，视觉/UI 故事仍然要求截图证据——“看起来对不对”从不豁免；`testing.strict.visual/ui` 只控制缺失时是否阻断。
- `qa.coverage_minimum` 只在 `qa.level: full` 有意义。非 full 时设置它，会在会话开始时收到“不会被强制”的警告。
- 不同引擎的覆盖率报告格式不同，仓库不统一，`gate-check` 读取对应引擎的报告。
- `null` 不等于 0：它是“没有承诺覆盖率目标”。

### 4.10 `strict_gate_checks`

| 键 | 控制什么 | 取值与默认 | 状态 |
|---|---|---|---|
| `strict_gate_checks` | 阶段门失败是阻断还是只警告 | `true`/`false`；文档默认 `true` | **保留未实现**：没有技能/钩子读 |

它和 `review_mode: solo` 的区别：

- `solo` 是**根本不跑**关卡。
- `strict_gate_checks: false` 是**跑，但把结论当建议**。

目前不要依赖它。

### 4.11 `team.size`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `team.size` | 默认启用多少代理 | `individual`、`small`、`studio`；无终端默认，由 rigor 派生（`full` → `studio`，否则 `individual`） | `/settings` 写；所有 `team-*`、`gate-check`、会生成代理的技能读 |

它不是“团队人数”，而是**默认活跃代理集合**：

- `individual`：核心 8 个代理。
- `small`：约 15 个（导演 + 组长 + 关键执行者）。
- `studio`：49 个代理全开，含引擎子专家和对抗式评审。

它是**个人体验**设置，可以写进 `project.local.yaml`：只改变你本机默认生成谁，不改变磁盘产物。

### 4.12 `platform`

这些是**游戏事实**，不是流程模式，全部锁定在 `project.yaml`。

| 键 | 控制什么 | 取值与默认 | 谁读 |
|---|---|---|---|
| `platform.multiplayer` | 是否有实时联机 | `true`/`false`；默认未设置（不等于 `false`） | `security-audit`、`team-release` |
| `platform.online` | 是否有任何在线功能（排行榜、云存档、遥测、内购） | `true`/`false`；默认未设置 | `security-audit`、`team-release` |
| `platform.cert_tier` | 认证目标 | `none`、`itch`、`steam`、`console`；默认未设置（不等于 `none`） | `launch-checklist`、`release-checklist` |
| `platform.targets` | 目标平台列表 | `PC`、`Mobile`、`Web`、`Console` 的列表 | 派生 `input_methods`；引擎/平台相关技能参考 |
| `platform.primary_input` | 主要输入方式 | 字符串，如 `Keyboard/Mouse`、`Gamepad`、`Touch` | 派生 `input_methods` |
| `platform.gamepad_support` | 手柄支持程度 | `Full`、`Partial`、`None` | 派生 `input_methods` |
| `platform.touch_support` | 触屏支持程度 | `Full`、`Partial`、`None` | 派生 `input_methods` |

两个“未设置不是 false / none”的例子：

- `platform.cert_tier` 未设置时，发布清单技能会**问**你目标平台；`none` 是“明确不做认证”，两者不同。
- `platform.multiplayer` 未设置时，安全审计会**问**是否联机，而不是擅自当作单机跳过。

`input_methods` 的派生规则：

- 目标含 `PC` 或 `Web` → 加入 `keyboard/mouse`。
- `gamepad_support` 为 `Full` 或 `Partial` → 加入 `gamepad`。
- `touch_support` 为 `Full` 或 `Partial` → 加入 `touch`。
- 再加上 `primary_input` 归一化后的值。

### 4.13 `engine`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `engine.name` | 引擎 | `Godot`、`Unity`、`Unreal`；默认未设置 | `/setup-engine` 写；路由专家、架构、代码评审等读 |
| `engine.version` | 引擎版本 | 字符串，如 `4.7` | `/setup-engine` 写；一致性检查读 |
| `engine.language` | 主语言 | `GDScript`、`C#`、`C++`、`Blueprint` | `/setup-engine` 写；专家路由读 |
| `engine.rendering` | 渲染后端 | 引擎相关字符串 | `/setup-engine` 写；`project-coherence.sh` 对照引擎工程文件检查 |
| `engine.physics` | 物理后端 | 引擎相关字符串 | `/setup-engine` 写；一致性检查读 |
| `engine.path` | 编辑器安装路径 | 字符串；编辑器不在 `PATH` 时写 | 代理/脚本在需要定位编辑器时读 |

为什么 `rendering`/`physics` 也要写：

- Godot 2D Web 项目如果用默认 Jolt/Forward+，会指向项目根本不用的后端。
- `project-coherence.sh` 会拿它和 `project.godot` 里的实际设置对比，给出“声明与实际不一致”的警告。

改了 `engine.name` 要重新跑 `/setup-engine`：

- `CLAUDE.md` 里有一行 `@docs/engine-reference/<engine>/VERSION.md` 的导入。
- 只改 YAML 不改导入，会导致每次会话加载错引擎参考。会话启动钩子会检测并警告这种不一致。

### 4.14 `specialists`

| 键 | 控制什么 | 取值与默认 | 谁写 / 谁读 |
|---|---|---|---|
| `specialists.code` | 语言/代码专家代理名 | 字符串；`null` 表示跳过 | `/setup-engine` 写；`dev-story`、`code-review` 等读 |
| `specialists.shader` | 着色器专家 | 字符串；`null` 表示跳过 | 同上 |
| `specialists.ui` | UI 专家 | 字符串；`null` 表示跳过 | 同上 |
| `specialists.additional` | 额外专家列表 | 字符串列表 | 同上 |

`primary`（主引擎专家）不是这里的键，而是解析器根据 `engine.name` 派生的：

- Godot → `godot-specialist`
- Unity → `unity-specialist`
- Unreal → `unreal-specialist`
- 其他/未设置 → 回退到 `technical-preferences.md` 的 `Primary` 行

如果整个 `specialists` 块不存在，解析器会回退到旧版 `technical-preferences.md`。

### 4.15 `naming`

| 键 | 控制什么 | 取值 | 引擎默认 |
|---|---|---|---|
| `naming.classes` | 类名 | `PascalCase`、`snake_case`、`camelCase` | 都是 PascalCase |
| `naming.variables` | 变量名 | `snake_case`、`camelCase`、`PascalCase` | Godot GDScript snake_case；Unity camelCase；Unreal PascalCase |
| `naming.constants` | 常量名 | `SCREAMING_SNAKE`、`PascalCase` | Godot/Unity/Unreal SCREAMING_SNAKE；Godot C# PascalCase |
| `naming.signals` | 事件/信号名 | `past_tense`、`on_event_name` | Godot past_tense；Godot C# on_event_name；Unity/Unreal `On<Event>` |
| `naming.files` | 源文件名 | `snake_case`、`PascalCase`、`kebab-case` | Godot snake_case；Unity/Unreal PascalCase |
| `naming.scenes` / `naming.prefabs` | 场景/预制体名 | `snake_case`、`PascalCase` | 同上 |

> 具体默认以 `/setup-engine` 按引擎/语言写入的值为准；上表是常见约定，用来帮你读懂配置，不是门禁。

关键认知：这些是**指导**，不是门禁。

- 它们会被传给程序员代理、控制清单、资产规格和架构文档。
- 但 `/code-review` 不会拿它们逐文件校验。
- 只有 Godot 的 `validate-assets.sh` 会对 `assets/` 下的大写、空格、连字符做警告，而且它不读 `naming.*`。

所以“我设置了命名规范，为什么代码没被拦住？”——因为设计上它就不是强制检查。

### 4.16 `accessibility` 与 `cadence`（保留未实现）

| 键 | 控制什么 | 取值与默认 | 状态 |
|---|---|---|---|
| `accessibility.target` | 无障碍承诺等级 | `none`、`standard`、`aaa`；默认 `none` | **保留未实现**：没有技能读 |
| `cadence.sprint_length` | 冲刺长度 | `1w`、`2w`、`3w`、`4w`、`none`；默认 `2w` | **保留未实现** |
| `cadence.milestone_length` | 里程碑长度 | `4w`、`6w`、`8w`、`12w`；默认 `8w` | **保留未实现** |

不要在这里花时间调参，除非你确认未来的版本已经接线。`/settings` 在你设置保留项时会明确提示。

### 4.17 `performance`

| 键 | 控制什么 | 取值与默认 | 谁读 |
|---|---|---|---|
| `performance.target_framerate` | 目标帧率 | 整数，如 `60`；默认未设置 | `perf-profile`、`gate-check`、`team-polish` |
| `performance.frame_budget_ms` | 每帧毫秒预算 | 数字，如 `16.67`；默认未设置 | 同上 |
| `performance.draw_call_limit` | 每帧 Draw Call 上限 | 整数；默认未设置 | 同上 |
| `performance.memory_ceiling_mb` | 内存上限（MB） | 整数；默认未设置 | 同上 |
| `performance.enforce` | 超预算时警告/阻断/关闭 | `warn`、`block`、`off`；默认 `warn` | `perf-profile`、`gate-check`、CI |

关键细节：

- **未设置不是 0**。没有承诺预算时，性能分析应报告 `NOT ASSESSED`，而不是拿 0 当目标。
- `performance.enforce` 是本机可覆盖的（`project.local.yaml`），因为 CI 仍会用 `project.yaml` 的团队标准。
- 解析时必须通过 `resolve_config --keys performance.enforce` 读取，不能直接读 `project.yaml`，否则会漏掉本机覆盖。

### 4.18 `commands`

默认全部未设置，由 `/setup-engine` 按引擎填入；`build` 必须问你导出预设名。

| 键 | 控制什么 | 取值 | 谁读 |
|---|---|---|---|
| `commands.build` | 完整构建（CI/发布） | 字符串，或按 OS 的映射 | `smoke-check` 在特定回退路径用；`project-coherence.sh` 检查导出预设；`launch-checklist` 只问产物是否有效 |
| `commands.test` | 跑完整测试套件 | 字符串/映射 | `smoke-check`；`hotfix`、`bug-report`、`day-one-patch` 会缩小范围使用 |
| `commands.run` | 启动游戏窗口（不是编辑器） | 字符串/映射 | `dev-story` 在观察功能/截图时用 |
| `commands.smoke` | 最小启动检查（能不能起来） | 字符串/映射 | `dev-story` 的解析检查；`smoke-check` 的构建检查 |

两种写法：

```yaml
# 简单形式：所有 OS 用同一条
commands:
  test: "godot --headless ..."

# 按 OS：
commands:
  build:
    default: "godot --headless --export-debug 'Linux/X11'"
    windows: "godot.exe --headless --export-debug 'Windows Desktop'"
    macos: "godot --headless --export-debug 'macOS'"
```

解析规则：字符串就直接用；映射就按当前 OS 找 `linux`/`windows`/`macos`，找不到用 `default`。

`build` 是最特殊的一行：导出预设名是项目自定义字符串，`/setup-engine` 必须**问你**，不能硬编码。没有预设时写 `[TO BE CONFIGURED]`，不要猜。

### 4.19 `features`

| 键 | 控制什么 | 取值与默认 | 谁读 |
|---|---|---|---|
| `features.session_state` | 是否启用完整会话跟踪（日志、检查点预览、代理审计） | `on`/`off`；默认 `on` | 5 个钩子读；技能不读 |
| `features.token_budget_warn_at` | 上下文用量告警阈值 | 0.0–1.0；文档默认 `0.7` | **保留未实现** |
| `features.read_guard` | 是否拒绝整文件读取大设计文档 | `on`/`off`，也接受 `true`/`false`；默认 `on` | `validate-read.sh` |
| `features.read_guard_kb` | 超过多少 KB 拒绝整文件读取 | 整数 KB；默认 `40` | 同上 |

`features.session_state` 有一个容易踩的坑：

- 它的枚举是 `on`/`off`，不是布尔。
- **只有字面量 `off` 才会关闭**；`session_state_enabled()` 只判断“是否等于 off”。
- 写 `false`、`no`、`0` 都不会关闭，一律视为开启。
- 想关就写 `off`。

`read_guard` 的行为：

- 对 `design/`、`docs/architecture/`、`production/session-state/` 下超过阈值的文件，**无 offset/limit 的整文件 Read 会被拒绝**。
- 拒绝时会列出标题和行号，提示你分段读。
- 带 range 的读取永远放行；图片/PDF 不检查。
- 它阻断而不是警告，因为“读进去”本身就是成本。

### 4.20 `updates`

| 键 | 控制什么 | 取值与默认 | 谁读 |
|---|---|---|---|
| `updates.check` | 会话开始时是否提示有新版本 | `true`/`false`；默认 `true` | `session-start.sh`（通过 `update-check.sh`） |

行为：

- 最多每天一次，后台请求 GitHub 最新 release，缓存在用户目录。
- 会话开始只读缓存，不联网、不等待。
- CI 环境自动关闭（没人看会话开始）。
- 本机可关闭：`/settings --local updates.check=false`。

### 4.21 派生量：它们看起来像配置，但不是键

技能经常会请求这些名字，但它们**不是** `project.yaml` 里的键，而是解析器算出来的派生结果：

| 派生名 | 来源 | 用途 |
|---|---|---|
| `review_gate` | `modes.review_mode` | 技能自己的导演关卡是否跑、阶段关卡是否跑 |
| `gate_level` | `testing.strict.*` | 每种故事类型是 `blocking` 还是 `advisory` |
| `engine_values` | `engine.name/version/language` | 引擎路由与实现简报 |
| `specialists` | `engine.name` + `specialists.*` | 主专家与各领域专家 |
| `input_methods` | `platform.*` | 支持哪些输入方式 |
| `perf_budgets` | `performance.*` 四个预算 | 性能分析/门禁 |
| `cert_tier` | `platform.cert_tier` 的请求别名 | 发布清单 |
| `system_overrides` | `workflow_overrides.system_overrides` | 所有按系统工作的技能一次性拿到全部覆盖 |
| `workflow[系统名]` | `system_overrides` 或项目 workflow | 单个系统的有效档位 |

看到技能里写 `resolve_config --keys gate_level`，不要以为你要在 YAML 里加一个 `gate_level:`。它只是“请把测试严格度换算成阻断等级”。

### 4.22 哪些是活的，哪些是保留的

| 状态 | 键 |
|---|---|
| **真正生效** | `schema_version`、`framework.*`（元数据）、`project.stage`、`modes.*`、`workflow_overrides.*`、`docs.density`、`testing.*`、`qa.*`、`team.size`、`platform.*`、`engine.*`、`specialists.*`、`naming.*`（指导）、`performance.*`、`commands.*`、`features.session_state`、`features.read_guard*`、`updates.check` |
| **保留未实现** | `project.kind`、`strict_gate_checks`、`accessibility.target`、`cadence.sprint_length`、`cadence.milestone_length`、`features.token_budget_warn_at` |
| **元数据/几乎无行为** | `project.name`、`project.genre`、`project.version`、`framework.version`、`framework.last_upgraded` |

这张表能解释大多数“我设置了但没反应”的问题。

---

## 5. 术语与命名语义

### 5.1 配置词

| 术语 | 白话解释 | 在本项目里的含义 |
|---|---|---|
| `rigor` | 严谨度/流程重量 | 一个套餐键，派生六个流程键 |
| `workflow` | 工作流 | 要求哪些设计文档/产物存在 |
| `review_mode` | 评审模式 | 评审时生成多少导演/专家关卡 |
| `automation` | 自动化程度 | 先问再做，还是边说边做 |
| `density` | 密度 | 每个文档章节写多深 |
| `granularity` | 粒度 | 故事切多大 |
| `level` | 档位/等级 | QA 证据要求的高低 |
| `strict` | 严格 | 失败是否阻断 |
| `enforce` | 强制执行 | 性能超限时警告/阻断/关闭 |
| `tier` | 层级/档位 | 认证目标（itch/steam/console） |
| `targets` | 目标平台 | PC/Mobile/Web/Console |
| `primary_input` | 主要输入 | 键鼠/手柄/触屏 |
| `specialists` | 专家代理 | 引擎相关的执行/评审专家 |
| `session_state` | 会话状态 | 是否启用会话日志和检查点 |
| `read_guard` | 读取守卫 | 是否拒绝整文件读大文档 |
| `overrides` | 覆盖/例外 | 对全局档位的局部改写 |
| `stage` | 阶段 | 项目生命周期位置 |
| `schema_version` | 结构版本 | YAML 结构是否需要迁移 |
| `framework.version` | 框架版本 | 模板来源，不驱动行为 |

### 5.2 项目词

| 术语 | 白话解释 |
|---|---|
| 技能（skill） | 一个可调用的工作流单元，通常有 `SKILL.md` |
| 钩子（hook） | 在特定事件（会话开始、读文件、压缩上下文）自动执行的脚本 |
| 子代理（agent） | 有专门职责的 AI 角色，如程序员、美术、QA |
| 导演（director） | 高层评审角色，如创意总监、技术总监、制作人 |
| 专家（specialist） | 引擎/领域执行专家，如 Godot 专家、着色器专家 |
| 关卡（gate） | 阶段推进前的质量检查 |
| 阶段（stage） | `Concept` 到 `Release` 的七个生命周期位置 |
| 相位（phase） | 一个技能内部的执行步骤（如 Phase 3b）；不要和项目 stage 混用 |
| 产物（artifact） | 磁盘上的文档/代码/报告，如 GDD、ADR、测试报告 |
| GDD | 游戏设计文档（Game Design Document） |
| ADR | 架构决策记录（Architecture Decision Record） |
| TR | 技术需求（Technical Requirement） |
| AC | 验收标准（Acceptance Criteria） |
| epic / story / sprint | 史诗 / 故事 / 冲刺：工作分解的三级单位 |
| vertical slice | 垂直切片：一条端到端可玩的窄功能 |
| cert | 平台认证（如主机 TRC/XR） |
| enum | 枚举：值只能从固定集合里选 |
| provenance | 来源标签：这个值到底从哪个文件/规则来 |
| locked | 锁定：只能写在 `project.yaml`，本机覆盖无效 |
| whitelist | 白名单：允许写进 `project.local.yaml` 的键 |
| deep merge | 深合并：按叶子键逐层合并，而不是整块替换 |
| dual-write | 双写：同时写新 YAML 和旧版文本镜像 |
| legacy mirror | 旧版镜像：为兼容旧技能保留的纯文本文件 |
| migration | 迁移：把旧结构转换成新结构 |
| pin | 钉死：显式写死一个值，使派生/套餐不再能改变它 |

### 5.3 几个刻意的命名选择

- `standard` 而不是 `indie`：它描述**流程量**，不描述开发者身份；`minimal`/`standard`/`full` 是同一根轴。
- `rigor` 而不是 `mode`：它是总档位，不直接决定某个行为；真正生效的是它派生的六个键。
- `lean` 而不是 `medium`：它不是“中等数量”，而是“只保留阶段门，跳过技能内签收”。
- `solo` 而不是 `off`：它不生成导演/专家关卡，但管线自带的审查仍然运行。
- `density` 而不是 `detail`：它只控制每节写多深，不控制哪些节存在（那是 `workflow`）。
- `overrides` 而不是 `settings`：它是主配置之上的例外层，不是另一套完整配置。

---

## 6. 它到底是怎么被用起来的

这一节回答“如何做到”。理解了机制，配置就不再神秘。

### 6.1 会话启动时

`session-start.sh` 会读取 `project.yaml` 的若干关键值，把上下文打印给会话：

- `project.stage`：当前阶段、下一步建议。
- `engine.name`：与 `CLAUDE.md` 的引擎参考导入做一致性检查。
- `features.session_state`：是否显示 `active.md` 的恢复预览。
- `updates.check`：是否显示新版本提示。

这一步是“会话级上下文”，不是完整配置解析。

### 6.2 技能加载时：预处理注入

大多数技能在 `SKILL.md` 正文顶部都有类似这样的一行：

```markdown
!`bash "${CLAUDE_SKILL_DIR}/../../hooks/yaml-helper.sh" resolve_config --keys automation,workflow`
```

它的执行时机是：**模型看到技能之前**。命令输出会直接替换这行占位符，成为技能正文的一部分。

所以技能作者不需要在正文里写解析链，只需要：

```markdown
解析结果如下，请按原样使用：
...（注入的已解析配置）...
```

同时技能前端会在 `allowed-tools` 里预批准这条命令，否则注入会失败，整个技能都不会启动。

一个重要的工程细节：这类命令必须写在**正文**，不能写在 frontmatter 的 `context:` 里；`context:` 只接受 `fork`，不会执行 shell。

### 6.3 钩子直接读

钩子不经过“注入”，而是直接调用 `yaml-helper.sh` 的函数：

| 钩子 | 读取 | 用途 |
|---|---|---|
| `session-start.sh` | stage、engine、session_state、updates | 会话上下文与一致性检查 |
| `validate-read.sh` | `features.read_guard*` | 拒绝整文件读大文档 |
| `session-stop.sh` | `features.session_state` | 归档会话、写日志 |
| `pre-compact.sh` | `features.session_state` | 记录压缩事件 |
| `log-agent*.sh` | `features.session_state` | 记录代理启停 |

钩子是“事件驱动”的；技能是“调用驱动”的。

### 6.4 脚本

脚本通常用于校验、写入和推进：

- `config-set.sh`：写单个键，检查已知键、锁定范围、写后回读。
- `set-stage.sh`：推进/设置阶段，双写 `project.yaml` 和 `production/stage.txt`。
- `config-set.sh` 要求 `project.yaml` 存在；不存在时提示先跑 `/start`。
- `set-stage.sh` 更宽松：没有 `project.yaml` 时会从最小模板创建一个，然后写入 `project.stage`。
- `project-coherence.sh`：检查 `engine.version/rendering/physics` 与真实工程文件是否一致。
- `migrate-v1-config.sh`：把旧版配置迁移成新结构。

### 6.5 解析算法的伪代码

```text
resolve_setting(path):
  0. 如果技能传了内联参数（如 --review），返回 (参数值, "inline flag")
  1. 如果 path 在本机白名单，且 project.local.yaml 存在：
       读 local 的值；如果合法，返回 (值, "project.local.yaml")
  2. 读 project.yaml：
       如果合法，返回 (值, "project.yaml")
  3. 如果 path 有旧版镜像（review_mode → review-mode.txt；stage → stage.txt）：
       读旧文件；如果合法，返回 (值, "旧文件路径")
  4. 如果 path 是 rigor 派生的六个键之一：
       返回 (rigor 展开值, "rigor:<level>")
  5. 如果 path 有文档默认值：
       返回 (默认值, "default")
  6. 返回 (空, "unset")
```

关键点：

- 每一步都会做**枚举校验**；非法值被丢弃，继续往下走。
- 被丢弃的值会出现在 `notes:` 行，不会被静默吞掉。
- 只有三个键由 `resolve_setting` 直接给“解析器级默认”：`modes.automation=collaborative`、`modes.rigor=minimal`、`performance.enforce=warn`。
- 其他键的默认由技能/钩子自己决定（如 `features.read_guard=on`、`features.session_state=on`、`updates.check=true`），或由 rigor/引擎派生。

### 6.6 来源标签

来源标签的完整词表：

```text
project.local.yaml
project.yaml
<legacy path>（如 production/stage.txt）
rigor:<level>
default
unset
```

看到 `(rigor:standard)`，说明这个值不是你写的，是套餐派生的。
看到 `(production/stage.txt)`，说明它来自旧版镜像，可能是历史遗留。
看到 `(unset)`，说明没人设置，技能会走自己的默认/提问分支。

### 6.7 本机覆盖与白名单

`project.local.yaml` 是**个人文件**，被 `.gitignore` 忽略，不进入团队仓库。

它不能单独存在：没有 `project.yaml` 时，本机覆盖会硬报错，提示先跑 `/start`。`/start` 只创建 `project.yaml`，不会创建 `project.local.yaml`；第一次 `--local` 写入时才创建。

它按**叶子键**深合并：

```yaml
# project.yaml
modes:
  automation: collaborative
  review_mode: lean
```

```yaml
# project.local.yaml
modes:
  automation: autonomous
```

结果：

- `automation` = `autonomous`（local 赢）
- `review_mode` = `lean`（local 没写，回落到 project）

不是“整块 `modes` 替换”，所以不会把 `review_mode` 弄丢。

当前代码里的本机白名单是 15 条：

```text
modes.review_mode
modes.automation
modes.automation_always_ask
team.size
testing.strict.logic
testing.strict.integration
testing.strict.visual
testing.strict.ui
testing.strict.config
performance.enforce
features.session_state
features.token_budget_warn_at
features.read_guard
features.read_guard_kb
updates.check
```

早期文档里可能写成 12 条（把 `features.*` 折叠计数），以 `yaml-helper.sh` 当前的白名单为准：15 条。

写其他键会怎样：

- `/settings --local <锁定键>` 会直接拒绝，并告诉你“项目级设置，请用 /settings 不带 --local”。
- 手动把锁定键写进 `project.local.yaml`，解析时会忽略，并在 `notes:` 里提示“不可本机覆盖，已忽略”。
- CI 里没有 `project.local.yaml`（被 gitignore），所以 CI 始终按 `project.yaml` 的团队标准运行；本机可以放松，CI 不会跟着放松。

### 6.8 谁写 project.yaml

| 写入者 | 写什么 | 备注 |
|---|---|---|
| `/start` | `project.stage`（+ `stage.txt`）、`modes.rigor`、`modes.automation` | 仅在用户明确同意后写；不写六个派生键 |
| `/setup-engine` | `engine.*`、`specialists.*`、`naming.*`、`commands.*` | 同时双写旧版 `technical-preferences.md` |
| `/gate-check` | `project.stage`（+ `stage.txt`） | PASS 或用户接受的 CONCERNS 才推进；FAIL 不推进 |
| `/settings` | 任意已知键 | 默认写 `project.yaml`；`--local` 写白名单键 |
| `/adopt` / `migrate-v1-config.sh` | `schema_version`、`framework.version`、迁移后的键 | 迁移旧项目 |
| 你本人 | 任意键 | 允许手改，但没有 `/settings` 的校验和提示 |

### 6.9 校验与失败行为

| 情况 | 行为 |
|---|---|
| 枚举值非法 | 丢弃该值，继续下一层；`notes:` 点名 |
| 未知键（手改） | 解析器不报错，只是没人读；`/settings` 会拒绝并给出最接近的已知键 |
| 锁定键写进 local | 忽略并提示；不自动修改文件 |
| 重复键 | 块合并，标量以最后一次为准；`notes:` 报告重复 |
| `project.yaml` 为空 | 按默认值运行，但 `notes:` 明确提示“文件是空的，可能写入被截断” |
| `project.yaml` 不存在 | 默认值 + 提示“运行 /start 创建” |
| 无 Python 解释器 | 依赖 Python 的 YAML 解析不可用，大部分设置只能走旧文件/默认值；`notes:` 提示 |
| 保留设置 | `/settings` 明确告知“没人读它”；手改不会触发运行时警告 |
| 设置版本更高 | 警告“来自更新版本，部分设置可能被忽略” |

一个例外：`features.session_state` 的钩子读取是直接判断“是否等于 `off`”，不走上面的枚举丢弃流程；写 `false` 不会出现 `notes:`，只是被当作开启。

### 6.10 YAML 边界：这个解析器不喜欢什么

它不是完整 YAML 实现，而是一个按缩进解析的轻量器。为了少踩坑：

- 用**两个空格**缩进，不要用 Tab。
- 一行一个键，不要用复杂的行内集合。
- 不要用锚点/别名（`&`、`*`）、多文档（`---`）等高级特性。
- 字符串里如果有 ` #`，用引号包起来，否则可能被当注释截断。
- 版本号、日期建议加引号：`version: "4.7"`、`last_upgraded: "2026-10-19"`。
- 布尔值写 `true`/`false`；`features.session_state` 例外，写 `on`/`off`。
- 列表用短横线逐行写：

```yaml
modes:
  automation_always_ask:
    - scope_changes
    - file_deletions
```

- 空映射可以写 `{}`，但不要指望它承载复杂结构。

---

## 7. 配置生命周期：从空模板到成熟项目

下面是一条典型路径。不同项目会跳过或调整，但顺序能帮你建立心智模型。

### 第 0 步：刚克隆模板

```yaml
schema_version: 1
framework:
  version: 1.2.0
  last_upgraded: 2026-10-19
```

此时：

- 没有阶段 → 默认 `Concept`。
- 没有引擎 → 技能会回退到 `technical-preferences.md` 或提问。
- 没有 modes → `rigor=minimal`、`automation=collaborative`，其余六个键按 minimal 派生。

### 第 1 步：`/start`

它先探测现状，再问“从哪里开始”，然后：

- 推断阶段（Concept / Systems Design / Technical Setup）。
- 推断 rigor（minimal / standard / full）。
- 问自动化偏好。
- 只有你点“保存”，才写入：

```yaml
project:
  stage: Concept
modes:
  rigor: minimal
  automation: guided
```

注意：它**不写** `review_mode`、`workflow`、`docs.density`、`qa.level`、`story_granularity`、`team.size`。这六个保持缺席，继续由 rigor 派生。

同时 `production/stage.txt` 被双写为 `Concept`。

### 第 2 步：`/setup-engine`

它写入四个块：

```yaml
engine:
  name: Godot
  version: "4.7"
  language: GDScript
  rendering: Forward+
  physics: Jolt

specialists:
  code: godot-gdscript-specialist
  shader: godot-shader-specialist
  ui: godot-specialist
  additional: [godot-gdextension-specialist]

naming:
  classes: PascalCase
  variables: snake_case
  constants: SCREAMING_SNAKE
  signals: past_tense
  files: snake_case
  scenes: snake_case

commands:
  build: "godot --headless --export-debug '<你的预设名>'"
  test: "godot --headless ..."
  run: "godot --path . --windowed --resolution 1280x720"
  smoke: "godot --headless --quit-after 5"
```

它还会：

- 重写 `CLAUDE.md` 的引擎参考导入行。
- 双写旧版 `technical-preferences.md`。
- 对 `build` 行询问你的导出预设名，不猜。

如果编辑器不在 `PATH`，还会写 `engine.path`。

### 第 3 步：设计阶段

`/brainstorm`、`/design-system`、`/create-architecture` 等会读取：

- `modes.workflow`：要求哪些章节。
- `docs.density`：每章写多深。
- `modes.review_mode`：评审时生成多少专家/关卡。
- `modes.automation`：写文件前是否要批准。

此时配置的作用是“决定产出物的形状”，而不是“决定代码怎么写”。

### 第 4 步：故事与冲刺

`/create-stories`、`/sprint-plan` 会读：

- `modes.story_granularity`：故事切多大。
- `qa.level`：故事需要什么证据。
- `testing.strict.*`：证据失败是否阻断。
- `team.size`：默认生成哪些代理。

### 第 5 步：实现故事

`/dev-story` 的解析请求通常包含：

```text
automation, workflow, qa.level, gate_level, engine_values, specialists, perf_budgets, system_overrides
```

它据此决定：

- 要不要在写文件前问。
- 需要哪些设计/ADR 前置。
- 测试证据要求。
- 路由到哪个语言/引擎专家。
- 性能预算是否存在。

### 第 6 步：`/gate-check` 推进阶段

`/gate-check` 读取：

```text
review_mode, workflow, qa.level, testing.strict, performance.enforce, team.size, project.stage, system_overrides
```

它根据当前阶段检查产物，给出 PASS / CONCERNS / FAIL：

- PASS：推进 `project.stage`，同时双写 `stage.txt`。
- CONCERNS：问你是否接受风险；接受才推进，并记录风险。
- FAIL：不推进。

### 第 7 步：`/settings` 调参

```text
/settings                          # 查看所有有效值与来源
/settings modes.workflow           # 查看单个键
/settings modes.workflow=full      # 写 project.yaml
/settings --local modes.automation=autonomous   # 写个人 local
```

它会在写入前做：

- 已知键检查（拼错会给出最近键名）。
- 枚举校验。
- 锁定范围检查（`--local` 不允许项目级键）。
- 写后回读，确认值真的写进去了。

---

## 8. 组合效应：几个必须理解的联动

### 8.1 rigor × 六个键

- `rigor` 只是套餐；显式单点永远优先。
- 预置六个键 = 钉死，套餐失效。
- 改 `rigor` 不会重写已有单点值。

### 8.2 workflow × docs.density

- `workflow` 管“有没有这一节”。
- `density` 管“这一节写多深”。
- `full + terse` = 全面但紧凑；`minimal + thorough` = 少而深。

### 8.3 qa.level × testing.strict

- `qa.level`：要不要证据。
- `testing.strict`：证据失败时阻不阻断。
- `qa.level: minimal` 时，strict 基本没有可阻断的对象。

### 8.4 review_mode × workflow

- `review_mode`：评审关卡**跑不跑**。
- `workflow`：要求多少文档、导演面板**多宽**。
- `/gate-check` 在 `full` 和 `lean` 都跑阶段关卡；只有 `solo` 跳过。

### 8.5 automation × automation_always_ask

- `automation` 决定默认问不问。
- `always_ask` 是一票否决：不管多自动，命中的类别必须问。
- 删掉 `schema_changes` 会让 `/settings` 在自主模式下也不确认写配置；这是有风险的操作。

### 8.6 team.size × review_mode

- `team.size` 决定“有哪些代理可用”。
- `review_mode` 决定“评审时生成多少关卡”。
- 两者不是一回事：`team.size: individual` 仍然可以被显式调用某个非核心代理，只是默认不启用。

### 8.7 performance 预算 × enforce

- 四个预算键定义“目标是什么”。
- `enforce` 定义“超了怎么办”。
- 预算未设置时，不要用 0 代替；应报告 `NOT ASSESSED`。

### 8.8 platform / engine → 路由

- `platform.multiplayer: true` → 安全审计跑联机类别，发布流程加网络程序员。
- `engine.name` → 主专家、语言专家、着色器/UI 专家路由。
- `platform.targets` + 输入支持 → `input_methods`。

### 8.9 stage × 门禁产物

- 阶段决定“检查哪道门”。
- `workflow` 决定“这道门要求哪些产物”。
- 产物缺失 → FAIL 或 CONCERNS；不会自动生成。

---

## 9. 常见误区与排错

### 9.1 “我设置了，但没生效”排查顺序

1. **看来源**：`/settings <key>` 或 `/settings`，确认值来自哪里。
2. **是不是保留设置**：`project.kind`、`accessibility.target`、`cadence.*`、`strict_gate_checks`、`features.token_budget_warn_at` 目前没人读。
3. **是不是写进了 local 的锁定键**：看 `notes:` 有没有“not locally overridable, ignored”。
4. **是不是拼错了键**：手改未知键不会报错，只是没人读；用 `/settings` 设置会校验。
5. **是不是枚举值非法**：非法值会回退，`notes:` 会点名。
6. **是不是被 rigor 派生盖住**：显式值优先；但如果你以为写了却没写，派生值会接管。
7. **是不是写死了六个键**：模板里预置 `workflow`/`density`/`qa.level` 等会让 rigor 失效。
8. **是不是旧文件在起作用**：`production/stage.txt`、`production/review-mode.txt` 可能仍是来源。
9. **是不是引擎导入不一致**：改了 `engine.name` 但没重跑 `/setup-engine`，`CLAUDE.md` 仍导入旧引擎参考。

### 9.2 常见症状表

| 症状 | 很可能的原因 | 处理 |
|---|---|---|
| 改了 `modes.rigor`，行为没变 | 六个键里有人被显式写死 | 删除对应单点键，或直接改单点 |
| `workflow_overrides` 完全没反应 | 被嵌套在 `modes:` 下 | 移到文档根目录 |
| `project.local.yaml` 里某键无效 | 该键不在本机白名单 | 移到 `project.yaml`，或删掉 |
| `/settings --local modes.workflow=minimal` 被拒 | workflow 是项目级设置 | 用 `/settings modes.workflow=minimal` |
| `features.session_state: false` 没关闭 | 只有字面量 `off` 才会关闭；`false` 被视为开启 | 写 `off` |
| `performance` 预算显示 0 或通过 | 预算是未设置，不是 0 | 让技能报告 `NOT ASSESSED`，或显式设预算 |
| `platform.cert_tier: none` 和未设置混淆 | 未设置是“还没决定”，`none` 是“明确不做” | 按真实意图设置 |
| `project.stage` 手动改了但门禁不对 | 阶段应与产物一致，且要双写 | 用 `/gate-check` 推进，或修正后同步 `stage.txt` |
| 命名规范没拦住代码 | `naming.*` 是指南，不是门禁 | 需要强制就自己加检查 |
| `testing.framework` 改了没反应 | 真正执行测试的是 `commands.test`，框架键只影响测试计划/命名/框架假设 | 同时确认 `commands.test` 与 `testing.framework` 匹配 |
| 故事完成但缺测试证据 | `qa.level` 太低或 `testing.strict` 太松 | 调高 `qa.level` / strict |
| 自主模式下仍然被问 | 命中 `automation_always_ask` | 调整类别列表，或接受这是安全网 |

### 9.3 不要做的事

- 不要把 `project.local.yaml` 当团队配置提交（它被 `.gitignore` 忽略）。
- 不要把密钥写进任一 YAML。
- 不要为了“跳过门禁”手动改 `project.stage`。
- 不要在模板里预置六个 rigor 派生键。
- 不要把 `workflow_overrides` 放进 `modes`。
- 不要用 0 表示“没有性能预算”。
- 不要用 `false` 关闭 `session_state`，用 `off`。

---

## 10. 从 project.yaml 展开到项目的其它配置

`project.yaml` 不是孤岛。它和以下文件共同构成项目的“配置层”。

### 10.1 配置文件地图

| 文件 | 角色 | 与 `project.yaml` 的关系 | 什么时候改 |
|---|---|---|---|
| `project.yaml` | 团队共享的结构化配置 | 主配置 | 用 `/settings` 或手改 |
| `project.local.yaml` | 个人本机覆盖 | 只覆盖白名单键，gitignored | `/settings --local` |
| `CLAUDE.md` | 给代理的常驻指令与导入 | 导入引擎参考；不是设置存储 | 改引擎/流程说明时 |
| `.claude/settings.json` | 工具权限、钩子注册 | 决定钩子是否运行、命令是否预批准 | 改工具链/权限时 |
| `.claude/settings.local.json` | 个人工具设置 | 本机权限 | 个人环境调整 |
| `.claude/docs/technical-preferences.md` | v1 旧版引擎事实 | 在 `project.yaml` 缺键时回退 | 一般由 `/setup-engine` 双写，不手改 |
| `production/stage.txt` | 阶段旧版镜像 | `project.stage` 的 fallback / 双写目标 | 由 `set-stage.sh` 写 |
| `production/review-mode.txt` | 评审模式旧版镜像 | `modes.review_mode` 的 fallback | 迁移/旧项目 |
| `.claude/docs/workflow-catalog.yaml` | 七阶段流程目录 | 定义阶段、技能、产物、门禁，不是用户偏好 | 改流程本身时 |
| `docs/engine-reference/<engine>/VERSION.md` | 引擎知识参考 | 由 `CLAUDE.md` 导入 | 换引擎版本时 |
| `production/session-state/active.md` | 运行时检查点 | 不是配置，技能读写它做恢复 | 会话中自动/技能写 |
| `.claude/hooks/yaml-helper.sh` | 解析器/校验器/解析入口 | 所有配置读取的运行时实现 | 一般不改 |
| `.claude/scripts/config-set.sh` | 单键写入器 | 写 `project.yaml`/`local`，带校验 | 由 `/settings` 调用 |
| `.claude/scripts/set-stage.sh` | 阶段写入器 | 双写 stage | 由 `/start`/`/gate-check` 调用 |
| `.gitignore` | 忽略本机文件 | 排除 `project.local.yaml` 和会话日志 | 一般不改 |

### 10.2 三个层次，不要混

```text
团队事实层：project.yaml
    ├─ 项目属性：project / platform / engine / specialists / naming
    ├─ 流程档位：modes / docs / qa / testing / team / performance
    └─ 运行机制：commands / features / updates / workflow_overrides

个人体验层：project.local.yaml
    └─ 只允许白名单：自动化、评审深度、测试严格度、性能强制、读取守卫、更新提示等

代理行为层：CLAUDE.md + .claude/rules + 技能/代理定义
    └─ 不是“值”，而是“怎么工作”的指令和协议
```

`CLAUDE.md` 不存配置值，但它会导入引擎参考，并声明协作协议。它和 `project.yaml` 的关系是：

- `project.yaml` 说“用 Godot 4.7”。
- `CLAUDE.md` 说“每次会话加载 `docs/engine-reference/godot/VERSION.md`”。
- 两边不一致时，会话启动钩子会警告。

### 10.3 跨文件优先级总表

| 读取的东西 | 优先级 |
|---|---|
| 普通设置 | `project.local.yaml`（仅白名单） → `project.yaml` → 旧版镜像 → rigor 派生 → 默认 |
| `project.stage` | `project.yaml` → `production/stage.txt` → 产物启发式检测 |
| `modes.review_mode` | 技能内联 `--review` 参数 → `project.yaml` → `production/review-mode.txt` → rigor 派生 |
| `testing.strict.*` | local 叶子 → project 叶子 → `testing.strict` 标量 → 技能默认 |
| `engine.*` / `specialists.*` / `input_methods` | `project.yaml` → `technical-preferences.md` 回退 |
| 钩子的功能开关 | `project.local.yaml` → `project.yaml` → 默认 |
| 技能行为 | 技能定义 + 注入的已解析配置；不要直接读文件 |

### 10.4 什么时候编辑哪个文件

| 你想做的事 | 改哪里 |
|---|---|
| 全队统一改流程档位 | `project.yaml`，优先用 `/settings <key>=<value>` |
| 只让自己少被问 | `project.local.yaml`，用 `/settings --local` |
| 换引擎/版本/语言 | 跑 `/setup-engine`，不要只手改 `engine.name` |
| 推进阶段 | 跑 `/gate-check`，不要手改 `project.stage` |
| 改构建/测试命令 | `/settings commands.test=...` 或手改 `project.yaml` |
| 改代理协作协议 | `CLAUDE.md` 和 `.claude/rules/` |
| 改钩子权限 | `.claude/settings.json` / `settings.local.json` |
| 改七阶段流程 | `.claude/docs/workflow-catalog.yaml`（高级） |
| 恢复会话 | 读 `production/session-state/active.md`，不是配置 |

---

## 11. 初学者操作手册

### 11.1 最常用的命令

| 命令 | 作用 |
|---|---|
| `/start` | 首次配置：阶段、rigor、自动化 |
| `/setup-engine` | 配置引擎、专家、命名、命令 |
| `/settings` | 查看所有有效值 + 来源 |
| `/settings modes.workflow` | 查看单个值 |
| `/settings modes.workflow=full` | 写入团队配置 |
| `/settings --local modes.automation=autonomous` | 写入个人配置 |
| `/gate-check` | 检查当前阶段并推进 |
| `/help` | 根据阶段给下一步建议 |

### 11.2 常用配方

**想更快到代码：**

```text
/settings modes.rigor=minimal
```

如果你之前显式设过六个键，先删掉它们，否则它们会继续压住 rigor。

**想更严谨：**

```text
/settings modes.rigor=full
```

**只想让文档更深，但章节不变：**

```text
/settings docs.density=thorough
```

**只想让评审更严格，但文档要求不变：**

```text
/settings modes.review_mode=full
```

**本机不想被频繁打断：**

```text
/settings --local modes.automation=autonomous
```

保留默认的 `scope_changes`、`file_deletions`、`schema_changes` 安全网。

**本机想关闭会话状态日志：**

```text
/settings --local features.session_state=off
```

注意是 `off`，不是 `false`。

**强制要求 Edge Cases：**

```text
/settings workflow_overrides.edge_cases=true
```

**给某个系统单独提高要求：**

```yaml
workflow_overrides:
  system_overrides:
    combat: full
```

**设置性能预算：**

```text
/settings performance.target_framerate=60
/settings performance.frame_budget_ms=16.67
/settings performance.enforce=block
```

### 11.3 手改 YAML 的安全流程

1. 先 `/settings <key>` 看当前值和来源；需要看原始解析结果时，可以运行：

```bash
bash .claude/hooks/yaml-helper.sh resolve_config --keys automation,workflow,qa.level
```

2. 备份或提交当前文件。
3. 只改目标键，保持两个空格缩进。
4. 运行 `/settings` 看有效值是否变化、来源是否正确。
5. 如果改的是阶段/引擎，跑对应技能或一致性检查。
6. 如果出现 `notes:`，按提示修正。

### 11.4 一分钟看懂一份陌生项目的配置

按这个顺序看：

1. `project.stage`：项目在哪。
2. `modes.rigor`：流程重量。
3. 六个派生键是否被显式覆盖：`workflow`、`docs.density`、`qa.level`、`story_granularity`、`review_mode`、`team.size`。
4. `engine.*`：技术栈。
5. `platform.*`：发行目标。
6. `workflow_overrides`：有没有例外。
7. `project.local.yaml`：本机有没有偏离团队。
8. `/settings`：最终有效值和来源。

---

## 12. 自测：你真的理解了吗

| 问题 | 答案 |
|---|---|
| `schema_version` 和 `framework.version` 是一回事吗？ | 不是。前者是文件结构版本，后者是模板版本；后者不驱动行为。 |
| 为什么模板里没有 `modes:`？ | 避免预置六个键；预置会钉死值，让 rigor 失效。 |
| `/start` 会写哪三个设置？ | `project.stage`、`modes.rigor`、`modes.automation`。 |
| `rigor: standard` 会让 `docs.density` 变成什么？ | `balanced`，除非你显式写了别的值。 |
| `workflow` 和 `docs.density` 的区别？ | 前者管哪些章节存在，后者管每章写多深。 |
| `qa.level` 和 `testing.strict` 的区别？ | 前者管要不要证据，后者管证据失败是否阻断。 |
| `review_mode` 和 `workflow` 在 gate-check 里分别管什么？ | 前者管关卡跑不跑，后者管导演面板多宽/要求多少产物。 |
| 为什么 `modes.workflow` 不能写进 local？ | 它会改变磁盘产物要求，必须团队一致。 |
| `features.session_state: false` 会关闭吗？ | 不会。只有字面量 `off` 才关闭；`false` 会被视为开启。 |
| 未设置性能预算等于 0 吗？ | 不等于。应报告 `NOT ASSESSED`。 |
| `workflow_overrides` 应该放哪里？ | 文档根目录，和 `modes` 平级，不能嵌套进 `modes`。 |
| 哪些设置是保留未实现的？ | `project.kind`、`strict_gate_checks`、`accessibility.target`、`cadence.*`、`features.token_budget_warn_at`。 |
| 技能是怎么拿到配置的？ | 加载前由 `resolve_config` 预处理注入，技能按原样使用。 |
| 来源标签 `(rigor:standard)` 是什么意思？ | 这个值来自 rigor 派生，不是你显式写的。 |
| 改了 `engine.name` 之后还要做什么？ | 重跑 `/setup-engine`，同步 `CLAUDE.md` 的引擎参考导入。 |

---

## 13. 技术栈：项目怎么说明自己“用什么做游戏”

### 13.1 先说人话：技术栈不只是“引擎”

很多初学者以为，技术栈就是写一句“我们用 Godot”。这只说对了一小半。

技术栈其实在回答四个问题：

1. **用什么工具做**：引擎、版本、编程语言。
2. **做出来的东西跑在哪**：PC、手机、网页、主机；键鼠、手柄、触屏；是否联网。
3. **谁来干**：代码、着色器、UI 分别找哪个专家代理。
4. **怎么写字、怎么跑**：命名规范、构建/测试/运行命令、测试框架。

打个比方：

| 配置 | 像什么 |
|---|---|
| `project.yaml` | 工程档案卡：材料、工具、工艺都记在上面 |
| `CLAUDE.md` | 贴在工地墙上的总规矩：每次开工都先看 |
| `docs/engine-reference/` | 厂家说明书：这个版本的引擎哪些 API 能用、哪些废弃了 |
| `specialists.*` | “什么活找哪位师傅”的分工表 |
| `commands.*` | 电钻、切割机的开关命令 |
| `platform.*` | 工地在哪里：室内、室外、还是网页上 |
| `naming.*` | 标签和文件的书写规范 |

只写 `engine: Godot`，就像只说“我们用锤子”，但没说是木工还是铁工、钉子多大、谁来钉。代理会知道引擎，但可能不知道语言、平台、该找哪个专家、测试怎么跑。

所以：**技术栈不是一行配置，而是一套分散在多个文件里的上下文。**

最短答案：

- `engine` 写“用什么”。
- `platform` 写“跑在哪”。
- `specialists` 写“找谁”。
- `naming` 写“怎么写字”。
- `commands` 写“怎么跑”。
- `testing.framework` 写“用什么测试”。
- `CLAUDE.md` 和 `docs/engine-reference/` 负责“让代理真的知道，而不是凭记忆猜”。

注意：技术栈只是项目上下文的一部分。技能还会读阶段、GDD、ADR、故事、QA 证据；技术栈不能替代这些。

### 13.2 技术栈分散在九个地方

| 存放位置 | 回答什么问题 | 打个比方 | 谁写 |
|---|---|---|---|
| `project.yaml` 的 `engine.*` | 用什么引擎、版本、语言、渲染、物理 | 材料清单 | `/setup-engine`、`/settings` |
| `project.yaml` 的 `platform.*` | 跑在哪些平台、什么输入、是否联网、什么认证 | 施工场地 | `/settings`、迁移脚本 |
| `project.yaml` 的 `specialists.*` | 代码/着色器/UI 分别找谁 | 分工表 | `/setup-engine`、`/settings` |
| `project.yaml` 的 `naming.*` | 类、变量、文件怎么命名 | 标签规范 | `/setup-engine`、`/settings` |
| `project.yaml` 的 `commands.*` | 怎么构建、测试、运行、冒烟 | 机器开关 | `/setup-engine`、`/settings` |
| `project.yaml` 的 `testing.framework` | 用哪个测试框架 | 质检工具 | `/setup-engine`、`/settings` |
| `CLAUDE.md` | 每次会话都要遵守的总指令、加载哪份引擎说明书 | 墙上的总规矩 | `/setup-engine`、手动 |
| `docs/engine-reference/<engine>/` | 这个引擎版本的 API、废弃项、破坏性变更、最佳实践 | 厂家说明书 | `/setup-engine`（可借助 WebSearch 核对） |
| `.claude/docs/technical-preferences.md` | v1.0 旧版技术偏好，缺键时兜底 | 旧档案 | `/setup-engine` 双写、迁移 |

注意最后一项：它是**旧版兼容文件**。新项目应优先看 `project.yaml`；只有某个键在 YAML 里缺失时，才会回退到它。

### 13.3 `engine` 块：技术栈的“身份证”

`engine` 是最核心的一块。它告诉所有技能和代理：这个项目到底在什么引擎、什么版本、什么语言上工作。

| 键 | 取值 | 默认 | 谁写 | 谁读 |
|---|---|---|---|---|
| `engine.name` | `Godot`、`Unity`、`Unreal` | 未设置（不是默认 Godot） | `/setup-engine`、`/settings` | `engine_values`、专家路由、`project-coherence.sh` |
| `engine.version` | 字符串，如 `"4.7"`、`"6000.3"`、`"5.8"` | 未设置 | `/setup-engine`、`/settings` | `engine_values`、版本一致性检查、引擎参考文档 |
| `engine.language` | `GDScript`、`C#`、`C++`、`Blueprint` | 未设置 | `/setup-engine`、`/settings` | `engine_values`、代码专家选择 |
| `engine.rendering` | 引擎相关字符串，如 `Forward+`、`Compatibility`、`URP` | 由引擎和项目形态决定 | `/setup-engine`、`/settings` | `project-coherence.sh`、平台/渲染相关技能 |
| `engine.physics` | 引擎相关字符串，如 `Jolt`、`Godot Physics 2D`、`PhysX`、`Chaos` | 由引擎和项目形态决定 | `/setup-engine`、`/settings` | `project-coherence.sh`、物理相关技能 |
| `engine.path` | 编辑器可执行文件或引擎根目录的路径 | 未设置 | `/setup-engine`、`/settings` | `smoke-check`、需要定位编辑器的代理/脚本 |

注意一个容易误解的地方：`engine.name` 未设置时，模板的参考文档默认是 Godot；但配置本身并没有写 `engine.name: Godot`。不要把“模板默认”误当成“项目已经配置好”。

一个最小可用的技术栈声明长这样：

```yaml
engine:
  name: Godot
  version: "4.7"
  language: GDScript
```

但只有这三行还不够。至少还要补上 `specialists`、`naming`、`commands`，代理才知道“找谁、怎么写字、怎么跑”。

三种引擎的完整例子：

```yaml
# Godot 2D 网页项目
engine:
  name: Godot
  version: "4.7"
  language: GDScript
  rendering: Compatibility
  physics: Godot Physics 2D
```

```yaml
# Unity 桌面项目
engine:
  name: Unity
  version: "6000.3"
  language: C#
  rendering: URP
  physics: PhysX
```

```yaml
# Unreal 3D 项目
engine:
  name: Unreal
  version: "5.8"
  language: C++
  rendering: Lumen + Nanite (deferred)
  physics: Chaos
```

为什么 `rendering` 和 `physics` 不能只看引擎名？

- Godot 的 `Jolt` 是 **3D 默认物理**，2D 项目仍然用 `Godot Physics 2D`。给 2D 项目写 `Jolt`，会把实现者引到一个游戏根本不用的后端。
- Godot 的 `Forward+` 偏向桌面；网页项目通常要 `Compatibility`，才能覆盖更多浏览器/显卡。
- 所以 `/setup-engine` 会先问你“2D 还是 3D、是否上网页”，再决定这两个值。

`engine.path` 容易被忽略，但它很重要：

- 它不是“可选装饰”。如果编辑器不在 `PATH`，代理可能误以为这台机器没装引擎。
- 它**不会**自动拼进 `commands.*`。命令要按写好的完整路径执行，所以 `commands` 里也要写全路径。
- 它主要给需要定位编辑器的脚本/代理用，比如冒烟检查。

### 13.4 `platform` 块：技术栈的“施工场地”

`engine` 说明“用什么工具”，`platform` 说明“做出来的东西在哪里跑”。

| 键 | 取值 | 默认 | 谁写 | 谁读 |
|---|---|---|---|---|
| `platform.multiplayer` | `true` / `false` | 未设置（不等于 `false`） | `/settings` | `security-audit`、`team-release` |
| `platform.online` | `true` / `false` | 未设置（不等于 `false`） | `/settings` | `security-audit`、`team-release` |
| `platform.cert_tier` | `none`、`itch`、`steam`、`console` | 未设置（不等于 `none`） | `/settings` | `launch-checklist`、`release-checklist` |
| `platform.targets` | `PC`、`Mobile`、`Web`、`Console` 的列表 | 未设置 | `/settings`、迁移脚本 | `input_methods`、`setup-engine`、UX/安全相关技能 |
| `platform.primary_input` | 字符串，如 `Keyboard/Mouse`、`Gamepad`、`Touch` | 未设置 | `/settings`、迁移脚本 | `input_methods` |
| `platform.gamepad_support` | `Full`、`Partial`、`None` | 未设置 | `/settings`、迁移脚本 | `input_methods` |
| `platform.touch_support` | `Full`、`Partial`、`None` | 未设置 | `/settings`、迁移脚本 | `input_methods` |

两个非常重要的“未设置”规则：

1. `platform.multiplayer` 未设置，不代表“单机”。它代表“还没决定”。安全审计会**问你**，而不是擅自跳过联机检查。
2. `platform.cert_tier` 未设置，不代表“不做认证”。`none` 是“明确不做”，未设置是“还没决定”。发布清单会问你。

还有一个容易忽略的事实：**`/setup-engine` 和 `/start` 都不会写 `platform` 块**。它们只在选择 `rendering`/`physics` 时参考平台信息；真正把 `platform.*` 写进 `project.yaml` 的，通常是你运行 `/settings`。

一个平台声明的例子：

```yaml
platform:
  targets:
    - PC
    - Web
  primary_input: Keyboard/Mouse
  gamepad_support: Partial
  touch_support: None
  multiplayer: false
  online: true
  cert_tier: steam
```

它会派生出：

- `input_methods`：`keyboard/mouse`、`gamepad`（因为 `Partial` 也算支持）。
- 安全审计：因为 `online: true`，会跑在线相关内容。
- 发布清单：因为 `cert_tier: steam`，会按 Steam 的要求列清单。

### 13.5 `specialists`、`naming`、`commands`：把技术栈变成“谁来干、怎么写字、怎么跑”

#### 13.5.1 `specialists`：分工表

| 键 | 含义 | 取值 | 谁写 | 谁读 |
|---|---|---|---|---|
| `specialists.code` | 代码/语言专家 | 代理名，如 `godot-gdscript-specialist` | `/setup-engine`、`/settings` | `dev-story`、`code-review`、`team-*` |
| `specialists.shader` | 着色器专家 | 代理名 | 同上 | 同上 |
| `specialists.ui` | UI 专家 | 代理名 | 同上 | 同上 |
| `specialists.additional` | 额外专家列表 | 代理名列表 | 同上 | 同上 |

`primary` 不是 YAML 键，而是根据 `engine.name` 算出来的：

- Godot → `godot-specialist`
- Unity → `unity-specialist`
- Unreal → `unreal-specialist`

为什么 Godot 还需要 `specialists.code`？

- `engine.name: Godot` 只知道“Godot”，不知道你用 GDScript 还是 C#。
- `specialists.code` 才能区分 `godot-gdscript-specialist` 和 `godot-csharp-specialist`。
- 所以 `dev-story` 会同时看 `engine_values` 和 `specialists`。

#### 13.5.2 `naming`：写字规范

`naming.*` 是“建议怎么命名”，不是“自动门禁”。

| 键 | 取值 | Godot 默认 | Unity 默认 | Unreal 默认 |
|---|---|---|---|---|
| `naming.classes` | `PascalCase`、`snake_case`、`camelCase` | PascalCase | PascalCase | PascalCase |
| `naming.variables` | `snake_case`、`camelCase` | snake_case | camelCase | PascalCase |
| `naming.constants` | `SCREAMING_SNAKE`、`PascalCase` | SCREAMING_SNAKE | SCREAMING_SNAKE | SCREAMING_SNAKE |
| `naming.signals` | `past_tense`、`on_event_name` | past_tense | on_event_name | PascalCase `On<Event>` |
| `naming.files` | `snake_case`、`PascalCase`、`kebab-case` | snake_case | PascalCase | PascalCase |
| `naming.scenes`（Unity 常叫 `prefabs`；Unreal 对应 Levels/Maps） | `snake_case`、`PascalCase` | snake_case | PascalCase | PascalCase |

它会被 `dev-story`、`create-control-manifest`、`asset-spec`、`create-architecture`、`architecture-review` 等技能读取，传给程序员或写进清单/规格。

但请注意：**`/code-review` 不会逐文件校验命名**。如果你需要强制检查，要自己加工具或规则。

另外，`naming.signals` 在 Unreal 里的实际惯例是 PascalCase `On<Event>`，但配置枚举写作 `on_event_name`；这是枚举表达力的限制，不要因为字面值不同就以为配置写错了。

#### 13.5.3 `commands`：机器开关

| 键 | 证明什么 | 谁读 |
|---|---|---|
| `commands.build` | 能不能打出完整构建 | `smoke-check`、`project-coherence.sh`、发布清单（只引用） |
| `commands.test` | 测试套件能不能跑、通不通过 | `smoke-check`、`hotfix`、`bug-report`、`day-one-patch` |
| `commands.run` | 游戏窗口能不能起来（不是编辑器） | `dev-story` 的“运行并观察”步骤 |
| `commands.smoke` | 最小启动检查能不能过 | `dev-story`、`smoke-check` |

两种写法：

```yaml
# 所有系统用同一条
commands:
  test: "godot --headless ..."

# 按操作系统区分
commands:
  build:
    default: "godot --headless --export-debug 'Linux/X11'"
    windows: "godot.exe --headless --export-debug 'Windows Desktop'"
    macos: "godot --headless --export-debug 'macOS'"
```

`commands.build` 是最特殊的一行：导出预设名是项目自定义的，`/setup-engine` 必须问你，不能猜。没有预设就写 `[TO BE CONFIGURED]`。

#### 13.5.4 `testing.framework`：质检工具

`testing.framework` 告诉测试相关技能用哪个框架：

- Godot：`gdunit4`
- Unity：`unity-test-framework`
- Unreal：`unreal-automation`

它由 `/setup-engine` 自动填。实际读取者主要是 `/test-helpers`；很多测试行为也依赖 `commands.test` 里写好的命令。

#### 13.5.5 一份完整的技术栈配置模板

下面是一份 Godot 2D Web 项目的示意配置，把技术栈的六块放在一起。**不要整段照抄**，按你的项目改；`/setup-engine` 会替你生成大部分。

```yaml
engine:
  name: Godot
  version: "4.7"
  language: GDScript
  rendering: Compatibility
  physics: Godot Physics 2D
  # path: "/path/to/godot"   # 编辑器不在 PATH 时才需要

platform:
  targets:
    - PC
    - Web
  primary_input: Keyboard/Mouse
  gamepad_support: Partial
  touch_support: None
  multiplayer: false
  online: false
  cert_tier: none

specialists:
  code: godot-gdscript-specialist
  shader: godot-shader-specialist
  ui: godot-specialist
  additional:
    - godot-gdextension-specialist

naming:
  classes: PascalCase
  variables: snake_case
  constants: SCREAMING_SNAKE
  signals: past_tense
  files: snake_case
  scenes: snake_case

commands:
  build: "godot --headless --export-debug '<你的导出预设名>'"
  test: "<你的测试命令>"
  run: "godot --path . --windowed"
  smoke: "godot --headless --quit-after 5"

testing:
  framework: gdunit4
```

读这份模板时注意：

- `engine` 是“用什么”。
- `platform` 是“跑在哪”。
- `specialists` 是“找谁”。
- `naming` 是“怎么写字”。
- `commands` 是“怎么跑”。
- `testing.framework` 是“用什么质检工具”。
- `build` 里的预设名和 `test` 命令必须按你的项目替换；带 `< >` 的是占位符，不是能直接执行的命令。

#### 13.5.6 从零开始，技术栈的配置顺序

1. 跑 `/setup-engine`，选引擎、版本、语言；回答“2D 还是 3D、目标平台是什么”（这决定 `rendering`/`physics`）。
2. 它写入 `engine`、`specialists`、`naming`、`commands` 四块，双写旧版文件，改写 `CLAUDE.md` 的导入，并生成引擎参考文档。
3. 用 `/settings` 补 `platform.*`——`/setup-engine` 和 `/start` 都不写这一块。
4. 需要的话，用 `/settings` 补性能预算。
5. 按 13.12 的清单检查，并跑一次 `project-coherence.sh`。

### 13.6 技术栈上下文是怎么一步步送到的：六层传递链

代理没有“长期记忆”。它不会在两次会话之间记住你的引擎。技术栈上下文是**每次从磁盘重新装配**的，像接力棒一样经过六层。

```text
第 1 层  /setup-engine
         ├─ 写 project.yaml: engine / specialists / naming / commands
         ├─ 双写 technical-preferences.md（旧版兼容）
         ├─ 改写 CLAUDE.md 的 @docs/engine-reference/<engine>/VERSION.md
         ├─ 生成 docs/engine-reference/<engine>/ 下的说明书
         └─ 必要时搭建引擎工程骨架

第 2 层  CLAUDE.md
         ├─ Technology Stack 段落：人看的摘要
         └─ @import：每次会话自动加载 VERSION.md

第 3 层  session-start.sh
         ├─ 打印当前阶段、引擎
         └─ 检查 engine.name 与 CLAUDE.md 的 import 是否一致

第 4 层  技能加载
         └─ resolve_config --keys engine_values,specialists,...
            把结果直接注入技能正文

第 5 层  子代理
         ├─ 技能把活派给 programmer / specialist
         └─ specialist 的 Version Awareness 要求它读
            VERSION.md、deprecated-apis.md、breaking-changes.md、
            current-best-practices.md、modules/*.md
            （文件名以 Godot 专家为例；Unity/Unreal 专家结构类似）

第 6 层  执行与检查
         ├─ commands.test / run / smoke / build 真正执行
         ├─ project-coherence.sh 对比声明与真实工程文件
         └─ gate-check / CI 用这些结果做判断
```

#### 第 1 层：`/setup-engine` 写什么

它做四件关键事：

1. 把 `engine`、`specialists`、`naming`、`commands` 四个块写进 `project.yaml`。
2. 把同样的引擎事实双写到 `.claude/docs/technical-preferences.md`（旧版镜像）。
3. 改写 `CLAUDE.md` 里的引擎参考导入行。
4. 生成 `docs/engine-reference/<engine>/` 的版本说明书。

它还会验证双写是否一致；不一致就停下来报告，因为“两个配置打架”会污染后面的所有读取。

#### 第 2 层：`CLAUDE.md` 的导入

`CLAUDE.md` 里有一行：

```markdown
@docs/engine-reference/godot/VERSION.md
```

这行的作用是：**每次会话开始，自动把这份引擎说明书加载进上下文**。

`/setup-engine` 会把它改成你选的引擎：

- Godot → `@docs/engine-reference/godot/VERSION.md`
- Unity → `@docs/engine-reference/unity/VERSION.md`
- Unreal → `@docs/engine-reference/unreal/VERSION.md`

如果你只手动改了 `project.yaml` 的 `engine.name`，却没有重跑 `/setup-engine`，这一行仍然指向旧引擎。于是：

- 配置说 Unity；
- 每次会话却加载 Godot 的说明书；
- 代理会用错知识，而且不一定会报错。

`session-start.sh` 会检测这种不一致并警告。

#### 第 3 层：会话启动

`session-start.sh` 会读 `project.yaml` 的关键值，打印当前上下文：

- 当前阶段；
- 引擎名；
- 是否显示会话恢复预览；
- 是否有新版本提示。

它还会做一致性检查：

- `engine.name` 和 `CLAUDE.md` 的 `@import` 是否指向同一个引擎；
- `project.stage` 和旧版 `stage.txt` 是否一致；
- 配置里有没有非法枚举值。

这一层不改变配置，只负责“把问题尽早说出来”。

#### 第 4 层：技能加载时的注入

技能不会自己去 YAML 里一行一行找。它们在加载前执行类似：

```markdown
!`bash "${CLAUDE_SKILL_DIR}/../../hooks/yaml-helper.sh" resolve_config --keys automation,workflow,qa.level,gate_level,engine_values,specialists,perf_budgets,system_overrides`
```

输出会被直接替换进技能正文。你会看到类似：

```text
engine_values: name=Godot (project.yaml) version=4.7 (project.yaml) language=GDScript (project.yaml)
specialists: primary=godot-specialist (engine.name) code=godot-gdscript-specialist shader=godot-shader-specialist ui=godot-specialist additional=godot-gdextension-specialist (project.yaml)
```

（以上是示意值，实际以你的配置为准。）

这样技能作者只需要写“按上面的 engine_values 和 specialists 执行”，不用重复实现解析链。

#### 第 5 层：子代理的“版本意识”

技术专家代理（如 `godot-specialist`）的提示词里有一段 **Version Awareness**：

1. 读 `docs/engine-reference/<engine>/VERSION.md`，确认版本。
2. 查 `deprecated-apis.md`，避免建议已废弃的 API。
3. 查 `breaking-changes.md`，注意版本迁移。
4. 读 `current-best-practices.md` 和相关 `modules/*.md`。
5. 如果资料里没有这个 API，就说“未验证”，不要凭记忆编。

为什么需要这一层？因为模型的训练数据有截止日期，而引擎每年都在变。**说明书比记忆可靠。**

代理还会检查 `engine.name`：

- 如果项目是 Unity，却被要求用 Godot 的概念回答，它应该说“我是错误的专家”，而不是硬翻译。
- 这防止“看起来什么都会，其实用错引擎”的问题。

#### 第 6 层：执行与检查

最后，技术栈要落到“真的能跑”：

- `commands.test` 真的执行测试。
- `commands.run` 真的打开游戏窗口。
- `commands.smoke` 真的做最小启动检查。
- `commands.build` 真的出构建。
- `project-coherence.sh` 对比 `project.yaml` 和真实工程文件：
  - `engine.version` 与 `VERSION.md` 的 pin 是否一致；
  - `engine.rendering` 与 Godot 的 `project.godot` 是否一致；
  - `engine.physics` 与实际物理后端是否一致；
  - `commands.build` 的导出预设是否有对应文件；
  - 声明与安装的编辑器是否一致（能探测到时）。

它只报告差异，不替你做判断；是否阻断由 `/gate-check` 或 CI 决定。

这就是“技术栈上下文”的完整闭环：**写进配置 → 加载说明书 → 注入技能 → 路由专家 → 执行命令 → 检查落地。**

### 13.7 技能眼里的技术栈：两条解析结果

以 `/dev-story` 为例，它的注入行包含：

```text
engine_values, specialists, perf_budgets, ...
```

它拿到的不是“引擎对象”，而是两条扁平文本：

1. `engine_values`：`name`、`version`、`language`，每项带来源标签。
2. `specialists`：`primary`、`code`、`shader`、`ui`、`additional`，以及来源。

然后它做三件事：

- 根据故事类型选程序员代理（gameplay / AI / network / UI 等）。
- 把改动文件交给 `specialists.code` 做引擎 API 检查。
- 如果故事涉及着色器/UI，再分别找 `shader`/`ui` 专家。

这里有一个容易误解的点：**不是每个代理都会自动拿到整份 `project.yaml`。** 技能只把这次任务需要的那几行传下去（引擎、版本、命名、预算、专家名）；专家代理再按提示词去读参考文档。所以如果技能忘了注入，代理就可能“什么都不知道”，或者只凭记忆猜。

注意：`engine_values` 在 `project.yaml` 缺键时会回退到 `technical-preferences.md`。这就是旧项目还能工作的原因。

### 13.8 代理眼里的技术栈：不是“记住”，而是“查书”

一个引擎专家代理工作时，脑子里大致是这样：

```text
我是 Godot 专家。
先看 VERSION.md：项目是 4.7，不是 4.6。
再看 deprecated-apis.md：这个 API 在 4.5 废弃了，不能建议。
再看 breaking-changes.md：4.6 → 4.7 有行为变化，要提醒。
最后看 modules/rendering.md：这个渲染后端支持什么。
如果文件里没写，我就说“未验证”，不凭记忆下结论。
```

如果 `VERSION.md` 的 `Installed at pin time` 是 `NOT DETERMINED`，说明安装版本探测没有跑；代理应该先问实际安装的版本，而不是把 pin 当成已安装版本。

这解决了三个常见问题：

- 模型记错 API；
- 模型用旧版本的知识回答新版本；
- 模型把 Unity 的概念套到 Godot 上。

### 13.9 设计、实现、评审、测试、发布：技术栈在每一站怎么用

| 阶段/技能 | 用到的技术栈事实 | 具体表现 |
|---|---|---|
| 设计 `create-architecture` | `engine.name/version/language`、平台 | 架构文档写入引擎约束、目录结构、模块边界 |
| 架构决策 `architecture-decision` | 引擎参考文档 | 写 ADR 前先读 `VERSION.md`，再让专家验证 API |
| 实现 `dev-story` | `specialists`、`naming`、`commands.run`、`engine.path` | 选程序员和专家、传命名规范、运行游戏观察 |
| 评审 `code-review` | 文件类型 + 专家路由 | `.gd` 找 GDScript 专家，`.shader` 找着色器专家，UI 找 UI 专家 |
| 测试 `smoke-check` | `commands.test/smoke/build`、`testing.framework` | 真正执行测试/启动/构建 |
| 性能 `perf-profile` | `engine.*`、`performance.*` | 按引擎给分析建议，对照预算 |
| 发布 `launch-checklist` | `platform.*`、`commands.build` | 按平台/认证级别列要求，确认构建产物 |
| 门禁 `gate-check` | 阶段 + 架构/性能/QA 证据 | 检查技术栈声明是否落地到产物 |

### 13.10 冲突时谁说了算

初学者最容易在这里迷路：`project.yaml`、`CLAUDE.md`、引擎说明书、真实工程文件，到底听谁的？

先分清两个问题：

- **“团队想用什么？”** → 看 `project.yaml`（主配置），缺键时看 `technical-preferences.md`。
- **“实际用的是什么？”** → 看真实工程文件（`project.godot`、`.uproject`、`ProjectSettings/`、包清单等）。

两者冲突时，不要猜，也不要静默选一个。正确做法是报告并修正。

更准确地说，要看你问的是哪类问题：

| 你在问什么 | 先看 | 再看 | 不要拿来当证据 |
|---|---|---|---|
| 团队想用什么 | `project.yaml` | `technical-preferences.md`（旧版兜底） | 模型记忆 |
| 实际用的是什么 | 真实工程文件 | `project-coherence.sh` 的报告 | 只凭 `project.yaml` 下结论 |
| 这个 API 怎么用 | `docs/engine-reference/` | 官方文档 / WebSearch | 模型记忆 |
| 这次会话该加载哪份说明书 | `CLAUDE.md` 的 `@import` | `session-start.sh` 的警告 | 猜 |

一句话：

- **配置值**以 `project.yaml` 为准。
- **落地事实**以真实工程文件为准。
- **API 知识**以版本化参考文档为准。
- 三者不一致时，报告并修正，不要静默选一个。

检查“三处一致”：

1. `project.yaml` 的 `engine.name` = `CLAUDE.md` 的 `@docs/engine-reference/<engine>/VERSION.md` = `docs/engine-reference/<engine>/` 存在。
2. `project.yaml` 的 `engine.version` = `VERSION.md` 的 pin = 安装的编辑器版本（按主.次版本比较；探测不到时会显示 `NOT DETERMINED`）。
3. `project.yaml` 的 `rendering`/`physics` = 真实工程文件里的设置。

`project-coherence.sh` 会检查其中大部分；`session-start.sh` 会检查第 1 条。

### 13.11 常见技术栈问题

| 症状 | 很可能的原因 | 处理 |
|---|---|---|
| 项目是 Unity，代理却满口 Godot | `engine.name` 没改，或 `CLAUDE.md` 的 import 还指向 Godot | 重跑 `/setup-engine`，不要只手改 YAML |
| 代理建议了一个不存在的 API | 引擎参考文档过时，或版本与项目不一致 | `/setup-engine refresh` 或 `upgrade` |
| `commands.test` 跑不起来 | 编辑器不在 `PATH`，`engine.path` 没写，命令里也没写全路径 | 补 `engine.path`，并把全路径写进 `commands.*` |
| 没有生成引擎专家 | `engine.name` 未设置，或 `specialists` 块缺失 | 跑 `/setup-engine` |
| 总被问“是否联机/什么平台” | `platform.*` 未设置 | 用 `/settings` 明确设置 |
| 构建出来的是错误平台 | `commands.build` 里硬编码了别的平台预设 | 让 `/setup-engine` 问你要构建的预设 |
| `rendering`/`physics` 和实际工程不一致 | 声明值写错，或工程文件被手动改过 | 跑 `project-coherence.sh`，按报告修正 |
| 命名规范没拦住代码 | 设计上就不是自动门禁 | 需要强制就自己加检查 |
| 模型“记得”一个旧 API | 模型记忆不可靠 | 以 `docs/engine-reference/` 为准 |

### 13.12 最小可用技术栈检查清单

新项目配置完，至少确认这九件事：

- [ ] `engine.name`、`engine.version`、`engine.language` 已设置。
- [ ] `specialists.code`、`shader`、`ui` 已按引擎填好。
- [ ] `naming.*` 已按引擎填好（至少 `classes`、`variables`、`files`）。
- [ ] `commands.test`、`run`、`smoke` 能实际执行；`build` 已问过预设或标了 TODO。
- [ ] `platform.targets`、`primary_input`、`gamepad_support`、`touch_support` 已设置。
- [ ] `platform.multiplayer`、`online` 已明确（即使答案是“没有”）。
- [ ] `CLAUDE.md` 的 `@import` 指向当前引擎。
- [ ] `docs/engine-reference/<engine>/VERSION.md` 存在且版本一致。
- [ ] `project-coherence.sh` 没有未处理的版本/渲染/物理不一致。

### 13.13 旧版 `technical-preferences.md`，是怎么装进 `project.yaml` 的

前面讲过“技术栈分布在九个地方”。这一节专门回答一个更尖锐的问题：

> 旧文件有 9 个区块、几十个字段，内容那么多；`project.yaml` 凭什么装得下？会不会漏？

先说结论，避免误解：

> **`project.yaml` 不是 `technical-preferences.md` 的“YAML 版复印件”，而是它的“可执行子集 + 重新建模”。**
> 能被机器反复使用的事实，抽成键值；只能给人读的解释性文字，留在原地；重复的信息，改成派生。

#### 13.13.1 逐块对照：每一条旧内容去了哪里

| 旧文件位置 | 旧内容 | 新位置 | 容纳方式 | 是否等价 |
|---|---|---|---|---|
| Engine & Language | `Engine: Godot 4.7` | `engine.name` + `engine.version` | 一行拆成两个键 | 否：没写版本号就拆不出 `version` |
| Engine & Language | `Language` | `engine.language` | 直接映射 | 是 |
| Engine & Language | `Rendering` | `engine.rendering` | 直接映射 | 是 |
| Engine & Language | `Physics` | `engine.physics` | 直接映射 | 是 |
| Input & Platform | `Target Platforms` | `platform.targets` | 逗号散文 → 数组 | 轻微：`PC + Console` 会变成一个元素 |
| Input & Platform | `Input Methods` | **没有同名键** | 由 `targets` / `gamepad` / `touch` / `primary_input` 重算成 `input_methods` | 否：`Mixed` 或特殊组合无法完整回读 |
| Input & Platform | `Primary Input` | `platform.primary_input` | 直接映射 | 是 |
| Input & Platform | `Gamepad Support` | `platform.gamepad_support` | 直接映射 | 是 |
| Input & Platform | `Touch Support` | `platform.touch_support` | 直接映射 | 是 |
| Input & Platform | `Platform Notes` | **无** | 留在 Markdown | 不迁移 |
| Naming Conventions | `Classes` | `naming.classes` | 原文搬运（不翻译写法） | 值保留，但写法可能与仓库约定不一致 |
| Naming Conventions | `Variables` | `naming.variables` | 原文搬运 | 否：`_camelCase` 私有字段等细节丢失 |
| Naming Conventions | `Signals/Events` | `naming.signals` | 原文搬运 | 否：自定义前缀/后缀丢失 |
| Naming Conventions | `Files` | `naming.files` | 原文搬运 | 值保留，写法可能与约定不一致 |
| Naming Conventions | `Scenes/Prefabs` | `naming.scenes` | 原文搬运 | 值保留，写法可能与约定不一致 |
| Naming Conventions | `Constants` | `naming.constants` | 原文搬运 | 否：`PascalCase 或 UPPER_SNAKE` 只能选一个 |
| Performance Budgets | `Target Framerate` | `performance.target_framerate` | 散文 → 数字 | 否：区间/分平台目标无法容纳 |
| Performance Budgets | `Frame Budget` | `performance.frame_budget_ms` | 散文 → 数字 | 否：同上 |
| Performance Budgets | `Draw Calls` | `performance.draw_call_limit` | 散文 → 数字 | 否：同上 |
| Performance Budgets | `Memory Ceiling` | `performance.memory_ceiling_mb` | 散文 → 数字 | 否：同上 |
| Testing | `Framework` | `testing.framework` | 原文搬运（别名需人工规范） | 轻微 |
| Testing | `Minimum Coverage` | `qa.coverage_minimum` | 去单位后仍非数字则**跳过** | 否：`80` 或 `80%` 可以，`尽量高`、`80% 以上` 不行且不报错 |
| Testing | `Required Tests` | **无** | 留在 Markdown | 不迁移 |
| Forbidden Patterns | 整节 | **无** | **故意留在 Markdown** | 不迁移（设计如此） |
| Allowed Libraries | 整节 | **无** | **故意留在 Markdown** | 不迁移（设计如此） |
| Architecture Decisions Log | 整节 | **无** | 由 `docs/architecture/` + `docs/registry/architecture.yaml` 取代 | 迁移到别处 |
| Engine Specialists | `Primary` | **无 `specialists.primary` 键** | 由 `engine.name` 推导 | 否：自定义主专家无法表达 |
| Engine Specialists | `Language/Code Specialist` | `specialists.code` | 只保留裸代理名 | 是（括号说明被剪掉） |
| Engine Specialists | `Shader Specialist` | `specialists.shader` | 只保留裸代理名 | 是 |
| Engine Specialists | `UI Specialist` | `specialists.ui` | 只保留裸代理名 | 是 |
| Engine Specialists | `Additional Specialists` | `specialists.additional` | 逗号散文 → 数组 | 是 |
| Engine Specialists | `Routing Notes` | **无** | 留在 Markdown | 不迁移 |
| Engine Specialists | `File Extension Routing` 表 | **无** | 技能按 `specialists.*` + 文件后缀自行路由 | 否：自定义行无法表达 |
| （旧文件没有） | 构建/测试/运行/冒烟命令 | `commands.build` / `test` / `run` / `smoke` | 新增能力 | 不适用 |
| （旧文件没有） | 联机、在线、认证等级 | `platform.multiplayer` / `online` / `cert_tier` | 新增能力 | 不适用 |

读这张表的正确方式：

- **“直接映射”**：YAML 里有等价键，迁移脚本能机械搬运。
- **“散文 → 枚举/数字”**：这不是复制，是**翻译**；翻译就有信息损失。
- **“无”**：不是遗漏，而是**有意不搬**或**留在旧家**。
- **“新增能力”**：旧文件本来就没有，谈不上遗漏。

#### 13.13.2 先看懂“地址”：区块、字段、值

不熟悉 YAML 的读者，先记住一张图。旧文件里的这一段：

```markdown
## Engine & Language
- **Engine**: Godot 4.7
```

在新文件里变成：

```yaml
engine:
  name: "Godot"
  version: "4.7"
```

可以这样理解：

| 词 | 白话比喻 | 上面的例子 |
|---|---|---|
| 区块（block） | 一个抽屉、一个大栏目 | `engine:` |
| 字段（key） | 抽屉里的小格子 | `name:` |
| 值（value） | 格子里填的内容 | `"Godot"` |
| 点路径（dot path） | 格子的完整地址：抽屉.格子 | `engine.name` |

旧文件像**一本手写笔记**：一章一章往下写，一条一行，冒号后面跟一段话。
新文件像**一张登记表**：先分抽屉，再分格子，每个格子只填一个值。

所以“一一对应”**不是“一行对一行”**，而是“**一个信息点对一个格子**”。这会带来三种偏差：

- **拆开**：旧文件的一行，拆成新文件的两个格子（`Engine: Godot 4.7`）。
- **合并**：旧文件的好几行，合成一个新格子里的清单（`PC, Console, Mobile`）。
- **不搬**：这段话是给人看的判断，程序用不上，就继续留在笔记里（禁止模式、平台备注）。

#### 13.13.3 每一项的一一对应（人话版）

下面按旧文件的分区逐条列。第一列是“旧文件里长这样”，第二列是“新文件里对应哪个格子”。

**引擎与语言**

| 旧文件写法 | 新文件里的格子 | 一句话解释 | 对应方式 |
|---|---|---|---|
| `Engine: Godot 4.7` | `engine.name` + `engine.version` | 用哪个引擎、哪个版本 | 拆成两格 |
| `Language: GDScript` | `engine.language` | 写代码用什么语言 | 原样搬 |
| `Rendering: Forward+` | `engine.rendering` | 画面用什么渲染方式 | 原样搬 |
| `Physics: Jolt` | `engine.physics` | 物理效果用什么引擎 | 原样搬 |

**输入与平台**

| 旧文件写法 | 新文件里的格子 | 一句话解释 | 对应方式 |
|---|---|---|---|
| `Target Platforms: PC, Console` | `platform.targets` | 游戏要发到哪些平台 | 逗号清单 → 逐项列出 |
| `Input Methods: Keyboard/Mouse, Gamepad` | **没有这一格**，由平台四格算出 `input_methods` | 玩家能用哪些设备操作 | 不抄，重新算 |
| `Primary Input: Keyboard/Mouse` | `platform.primary_input` | 玩家主要用哪个设备 | 原样搬 |
| `Gamepad Support: Full` | `platform.gamepad_support` | 对手柄支持到什么程度 | 原样搬 |
| `Touch Support: None` | `platform.touch_support` | 对触屏支持到什么程度 | 原样搬 |
| `Platform Notes: ...` | **没有** | 平台相关的额外提醒 | 不搬，留在旧文件 |

**命名约定**

| 旧文件写法 | 新文件里的格子 | 一句话解释 | 对应方式 |
|---|---|---|---|
| `Classes: PascalCase` | `naming.classes` | 类名怎么起 | 原样搬 |
| `Variables: snake_case` | `naming.variables` | 变量名怎么起 | 原样搬 |
| `Signals/Events: past_tense` | `naming.signals` | 事件/信号名怎么起 | 原样搬（旧标签可写成短名） |
| `Files: snake_case` | `naming.files` | 文件名怎么起 | 原样搬 |
| `Scenes/Prefabs: PascalCase` | `naming.scenes` | 场景/预制体名怎么起 | 原样搬 |
| `Constants: SCREAMING_SNAKE` | `naming.constants` | 常量名怎么起 | 原样搬 |

**性能预算**

| 旧文件写法 | 新文件里的格子 | 一句话解释 | 对应方式 |
|---|---|---|---|
| `Target Framerate: 60 fps` | `performance.target_framerate` | 一秒至少画多少帧 | 去掉单位，只留数字 |
| `Frame Budget: 16.67ms` | `performance.frame_budget_ms` | 每一帧最多花多少毫秒 | 去掉单位，只留数字 |
| `Draw Calls: 2000` | `performance.draw_call_limit` | 一帧最多提交多少次绘制 | 只留数字 |
| `Memory Ceiling: 1024MB` | `performance.memory_ceiling_mb` | 内存最多用多少 MB | 去掉单位，只留数字 |

**测试**

| 旧文件写法 | 新文件里的格子 | 一句话解释 | 对应方式 |
|---|---|---|---|
| `Framework: gdUnit4` | `testing.framework` | 自动化测试用哪个工具 | 原样搬 |
| `Minimum Coverage: 80%` | `qa.coverage_minimum` | 代码至少被测试覆盖多少 | 去掉 `%`，只留数字 |
| `Required Tests: ...` | **没有** | 哪些东西必须写测试 | 不搬，留在旧文件 |

注意：`Minimum Coverage` 没有搬进 `testing:` 块，而是搬进了 `qa:` 块。因为它约束的是“质量证据要求”，测试框架只是手段。

**三条“只留在旧文件”的规则**

| 旧文件写法 | 新文件里的格子 | 一句话解释 | 对应方式 |
|---|---|---|---|
| `Forbidden Patterns` 整节 | **没有** | 绝对不能出现的写法 | 故意不搬 |
| `Allowed Libraries / Addons` 整节 | **没有** | 允许使用的第三方库 | 故意不搬 |
| `Architecture Decisions Log` 整节 | **没有** | 重大架构决定的记录 | 改用 `docs/architecture/` + registry |

**引擎专家**

| 旧文件写法 | 新文件里的格子 | 一句话解释 | 对应方式 |
|---|---|---|---|
| `Primary: godot-specialist` | **没有 `specialists.primary`** | 主要是哪个专家负责 | 不抄，按 `engine.name` 算 |
| `Language/Code Specialist: ...` | `specialists.code` | 谁来看代码 | 只搬代理名 |
| `Shader Specialist: ...` | `specialists.shader` | 谁来看着色器 | 只搬代理名 |
| `UI Specialist: ...` | `specialists.ui` | 谁来看界面 | 只搬代理名 |
| `Additional Specialists: a, b` | `specialists.additional` | 还要叫上哪些专家 | 逗号清单 → 逐项列出 |
| `Routing Notes: ...` | **没有** | 什么文件交给谁看 | 不搬，留在旧文件 |
| `File Extension Routing` 表 | **没有** | 按文件后缀分派专家 | 不搬，技能自己按后缀判断 |

**新表里“多出来”的格子**（旧文件根本没有，谈不上对应）：

| 新格子 | 干什么用 |
|---|---|
| `commands.build` / `test` / `run` / `smoke` | 构建、测试、运行、冒烟检查具体跑什么命令 |
| `platform.multiplayer` / `online` / `cert_tier` | 是否联机、是否有在线功能、走哪个发行渠道 |
| `engine.path` | 编辑器装在哪，便于脚本找到它 |
| `framework.*`、`features.*`、`updates.*` | 仓库工具链的版本与开关，和游戏技术栈无关 |

#### 13.13.4 五种“不是照抄”的情况，看例子就懂

**① 拆开：一句话里藏着两个信息**

```yaml
# 旧文件
- **Engine**: Godot 4.7

# 新文件
engine:
  name: "Godot"
  version: "4.7"
```

为什么拆：程序要比较版本号，必须单独拿到 `4.7`，不能每次从一句话里猜。
迁移规则：第一个词当引擎名，最后一个“像版本号”的词当版本。所以 `Unity 6.3 LTS` 的版本会拆不出来，需要手补。

**② 清单：逗号句子变成一行一项**

```yaml
# 旧文件
- **Target Platforms**: PC, Console, Mobile

# 新文件
platform:
  targets:
    - PC
    - Console
    - Mobile
```

为什么：逗号既可能分隔平台，也可能出现在括号说明里；写成清单就没有歧义。
注意：`PC + Console` 没有逗号，会被当成一个平台，需要手改。

**③ 数字：去掉单位，只留能计算的数**

```yaml
# 旧文件
- **Frame Budget**: 16.67ms
- **Minimum Coverage**: 80%

# 新文件
performance:
  frame_budget_ms: 16.67
qa:
  coverage_minimum: 80
```

迁移会先去掉 `ms`、`MB`、`fps`、`%` 这类单位；如果剩下的还不是纯数字，性能预算会写 `null` 并在报告里提示，覆盖率则直接不写。
所以 `尽量高`、`60 帧左右` 这类写法搬不过去，需要人给一个准确数字。

**④ 算出来：不存第二份答案**

```yaml
# 旧文件
- **Primary**: godot-specialist
- **Input Methods**: Keyboard/Mouse, Gamepad

# 新文件：这两行都不存
engine:
  name: "Godot"          # → 推导出主专家 godot-specialist
platform:
  targets: [PC]          # → 含 PC，所以有键盘鼠标
  gamepad_support: Full  # → 有手柄
  primary_input: "Keyboard/Mouse"
```

为什么：同一个结论存两份，迟早会互相矛盾（引擎改成 Unity，Primary 还写着 Godot 专家）。
代价：`Input Methods: Mixed` 这种“结论型”写法，重算后会变成具体设备列表，不一定一字不差。

**⑤ 不搬：给人看的判断，留在旧本子**

- `Forbidden Patterns`、`Allowed Libraries`：评审时会被**整段复制**进提示词的管控清单，程序并不逐条解析它们，所以留在 Markdown 最合适。
- `Platform Notes`、`Required Tests`、`Routing Notes`、`File Extension Routing`：都是解释性文字或自定义规则，没有对应的 YAML 表达方式。

**抄写口诀**：

> 一格一值；清单分行；单位去掉；算得出来的不抄；判断和说明留在旧本子。

#### 13.13.5 为什么这套格式能装下：七个设计手法

`project.yaml` 能容纳旧内容，不是因为它“更长”，而是因为它换了数据模型。具体有七个手法：

**① 区块镜像旧文件的 `##`**

旧文件的每个 `##` 区块，在新文件里几乎都能找到同名或近名的顶层块：

| 旧 `##` 区块 | 新顶层块 |
|---|---|
| Engine & Language | `engine:` |
| Input & Platform | `platform:` |
| Naming Conventions | `naming:` |
| Performance Budgets | `performance:` |
| Testing | `testing:` + `qa.coverage_minimum` |
| Engine Specialists | `specialists:` |

**分区一一对应**，所以“哪段旧内容该去哪个块”不需要猜；迁移脚本也是按这个对应关系写死的。

**② 点路径给了每个字段一个稳定地址**

旧文件靠“区块标题 + 粗体标签”定位（`## Naming Conventions` 下的 `- **Classes**`）。新文件把两层压成一个地址：

```text
naming.classes
platform.gamepad_support
performance.frame_budget_ms
```

这样技能、钩子、脚本都能用同一个字符串稳定地取到一个值，不依赖 Markdown 标题是否被手改过。

**③ 枚举把“自由文本”收窄成“可校验值”**

旧文件写 `PascalCase`、`Full`、`gdUnit4`；这些是给人看的。新文件统一成仓库约定的写法，常见的有 `PascalCase`、`snake_case`、`SCREAMING_SNAKE`、`Full`/`Partial`/`None`、`gdunit4`，并且**解析器会对真正有枚举的键做校验**：

- 合法值 → 正常使用；
- 非法值 → 被拒绝，写进 `notes`，继续回退，不会把 `platform.gamepad_support: 也许吧` 传遍所有技能。

这是“能容纳”与“能可靠使用”的分界线：**旧文件能写任何话，新文件只接受能执行的话。**

> 迁移是一个例外：迁移脚本为了不猜错，**按原文搬运**命名和测试框架，不做写法翻译。所以迁移后建议用 `/settings` 复核一遍这些值，让写法统一到仓库约定。

**④ 数组容纳“一对多”**

旧文件用逗号散文表达集合：`PC, Console, Mobile`、`godot-specialist, godot-shader-specialist`。新文件用数组：

```yaml
platform:
  targets:
    - PC
    - Console
    - Mobile

specialists:
  additional:
    - godot-gdextension-specialist
```

数组让“一个字段多个值”有了明确边界，解析器不需要猜逗号是不是分隔符（旧文件里 `ue-gas-specialist (abilities, effects)` 的括号内逗号就是个真实陷阱）。

**⑤ 派生代替存储，消除“两份答案”**

旧文件有两个“看起来像字段、其实是结论”的项：

- `Primary` 专家：新文件**不存**，由 `engine.name` 推导（Godot → `godot-specialist`）。
- `Input Methods`：新文件**不存**，由平台支持能力推导（有手柄 → 含 Gamepad；有触屏 → 含 Touch）。

这样就不会出现“引擎写 Unity、Primary 写 godot-specialist”这种自相矛盾。**能算出来的，就不要存。**

**⑥ `null`、注释、开放字符串各司其职**

| 手法 | 作用 | 例子 |
|---|---|---|
| `null` | 表示“明确未设置”，区别于“这个键不存在” | `specialists.shader: null` |
| 注释 | 保留“为什么这样设计”，Markdown 的说明能力不丢 | 模板顶部的 `modes` 说明 |
| 开放字符串 | 不强行枚举项目自定义值 | `engine.rendering`、`commands.build`、`engine.path` |

**⑦ schema 与效果表兜底**

格式能装下只是第一步；还要保证“装了有人读”。仓库用 `effects-map.md` 给每个键记录 **取值 / 默认 / 写入者 / 读取者**，并用“死设置审计”发现“看起来是配置、其实没人读”的键。这解释了为什么 `project.kind`、`cadence.*` 会被标成“保留未实现”——**能写进 YAML，不等于已经接线**。

#### 13.13.6 新机制是怎么跑起来的：四条链路

```text
【写入】
  /setup-engine ──→ 先写 technical-preferences.md（人确认）
                 └─→ 再双写 project.yaml 的 engine / specialists / naming / commands
  /settings     ──→ 直接写 project.yaml（枚举校验），不写 Markdown
  /adopt        ──→ 迁移脚本把旧 Markdown 的值搬进 project.yaml，保留 Markdown
  /start        ──→ 写 modes / project.stage

【读取】
  resolve_config：project.local.yaml → project.yaml → 旧 Markdown → rigor 派生 → 默认
  技能正文：用解析结果；个别键在 YAML 缺失时按标签回退读 Markdown
  钩子/脚本：直接调用解析函数或读 `Engine:` 行

【校验】
  枚举拒绝非法值 → notes
  /setup-engine 5.5.3 → 回读核对双写一致性，不一致就停
  project-coherence.sh → 声明值 vs 实际工程文件
  /adopt 审计 → 任一来源有值就算“已配置”

【演进】
  schema_version → 结构迁移
  effects-map → 每个键的契约
```

四条链路里，最关键的是**写入不是“一次性搬家”，而是“持续双轨”**：

- 引擎、专家、命名、命令：每次 `/setup-engine` 都双写；
- 平台、性能、测试框架：`/setup-engine` 只写 Markdown，之后由 `/settings` 写 YAML；
- 禁止模式、允许的库：永远只写 Markdown；
- 旧项目：先迁移，再按上面的规则继续。

所以“新机制”不是“把 Markdown 废掉”，而是**把每个字段的权威位置重新分配了一遍**。

#### 13.13.7 会不会有遗漏？

会。但要分清五种“遗漏”，因为它们的性质完全不同：

| 类型 | 具体内容 | 结果 | 风险 |
|---|---|---|---|
| **完整迁移** | 引擎四字段、命名六字段、平台四字段、性能四字段、测试框架/覆盖率、专家四字段 | YAML 有等价键，技能优先读 YAML | 低 |
| **有损转换** | 散文 → 枚举/数字；`Engine` 一行拆两键；`Input Methods` 重算；专家括号说明被剪 | 迁移报告会提示一部分；另一部分按约定翻译 | 中：需要人复核 |
| **故意保留** | Forbidden Patterns、Allowed Libraries | 只住在 Markdown，由控制清单复制生效 | 低（设计如此），但**不能删文件** |
| **无处安放** | `Platform Notes`、`Required Tests`、`Routing Notes`、`File Extension Routing` 自定义行 | **只住在 Markdown**；迁移脚本不会搬，也基本不会报 | 高：删文件就真丢了 |
| **已被取代** | Architecture Decisions Log | 现行 ADR 在 `docs/architecture/` + registry | 低：内容没有丢，只是换了家 |

这里有三个**静默盲区**，必须明确说出来：

1. **`Platform Notes` 不在迁移键清单里。** 它既不会被搬进 YAML，也不会出现在“未迁移”报告里——因为它在一个被识别的区块内，只是迁移脚本不读这个标签。
2. **`Required Tests` 明确不迁移。** 脚本注释写着它是“给人看的指导”，从不传给解析函数。
3. **`Routing Notes` 和整张 `File Extension Routing` 表不迁移。** 实际路由由技能的 `specialists.*` + 文件后缀逻辑完成；自定义路由规则没有 YAML 表达方式。

另外还有两类“看起来像遗漏、其实不是”的情况：

- **`Input Methods` 不是被丢了，而是被重算了。** 只要 `platform.targets`、`gamepad_support`、`touch_support`、`primary_input` 都在，`input_methods` 就能重新推出一个合理值；但旧文件里写 `Mixed` 这种“结论型”描述，可能无法一比一还原。
- **`Primary` 专家不是被丢了，而是被推导了。** 只有推导失败（引擎名不认识）时，才会回退读旧文件的 `Primary`。

所以，**“不会遗漏”靠的不是格式万能，而是三条工程保证**：

1. **迁移脚本从不删除 `technical-preferences.md`**——无处安放的内容继续安全地待在那里。
2. **迁移报告显式列出被保留、被手工处理、被警告的内容**，让人有机会复核。
3. **效果表和审计能发现“有键没人读”**，避免把“写进去了”误当成“生效了”。

#### 13.13.8 自查：我的项目到底迁全了没有

按顺序做这六件事：

1. **看迁移报告**：`production/migration-report.md` 里有没有 `PRESERVED`、manual、warning 条目。
2. **看 YAML 九个块**：`engine`、`platform`、`naming`、`performance`、`testing`、`specialists`、`commands` 是否齐；`qa.coverage_minimum` 是否设置。
3. **看 Markdown 还剩什么**：如果只剩 Forbidden Patterns、Allowed Libraries 和少量说明文字，说明迁移基本完成；如果 `Engine`、`Naming` 还是真实值，说明它仍在兜底。
4. **看平台与性能**：这两块 `/setup-engine` 不写 YAML，必须确认是你有意让 Markdown 兜底，还是漏了 `/settings`。
5. **看来源标签**：用 `/settings` 或解析输出查看每个值的来源；出现 `(legacy)` 就说明还在读旧文件。
6. **人工确认三类“无处安放”**：Platform Notes、Required Tests、Routing Notes 是否还有效；有效就保留 Markdown，不要为了“迁干净”而删文件。

一句话收尾：

> **`project.yaml` 装下的是“机器要执行的技术事实”；装不下的是“人要阅读的判断与说明”。**
> 判断一次迁移是否完整，不是看 YAML 有多长，而是看**每个仍然生效的约束，是否还有一个明确的家**。

---

## 14. 配置项字典（逐项速查）

这一节像字典，不需要一次读完。查某个键时看对应的行即可。

先说明表里的四个约定：

- **写入者**：主要写这个键的地方；手改也允许，但没有 `/settings` 的校验。
- **默认**：这里的“默认”有三种，不能混：
  - **解析器默认**：`yaml-helper.sh` 直接给的兜底值。
  - **派生默认**：由 `modes.rigor` 或 `engine.name` 算出来的值。
  - **技能默认**：解析器输出 `unset`，由具体技能自己决定。
- **读取者**：主要读取者，通常是 grep 能看到的“下限”，不等于全部。
- **同上**：与上一行完全相同，不是“还有别的”。
- **`*`**：表示这个键可以写进 `project.local.yaml`（本机白名单）。

写入者简称：

| 简称 | 实际是谁 |
|---|---|
| `start` | `/start` |
| `setup` | `/setup-engine` |
| `settings` | `/settings` |
| `gate` | `/gate-check` |
| `migrate` | `/adopt` 或 `migrate-v1-config.sh` |
| `hand` | 人手写 |

举个例子：查 `qa.level` 这一行，能读出“它有三个值；不写就跟着 rigor；用 `/settings` 写；主要影响故事完成和门禁；和引擎无关”。会读这一行，就会读整张表。

### 14.1 身份、版本与生命周期

| 键 | 取值 | 默认 | 写入者 | 主要读取者 | 备注 |
|---|---|---|---|---|---|
| `schema_version` | 整数 | `1` | `start`、`setup`（创建时）、`migrate` | 所有解析器、迁移逻辑 | 低版本提示迁移；高版本警告“部分设置可能被忽略” |
| `framework.version` | 语义化版本 | 模板版本，如 `1.2.0` | 模板、`setup`（创建时）、`migrate` | 无行为读取者 | 只记录模板来源 |
| `framework.last_upgraded` | ISO 日期 | 模板日期 | 模板、`setup`（创建时）、`migrate` | 无行为读取者 | 只记录升级时间 |
| `project.name` | 字符串 | 未设置 | `hand` | 几乎没有 | 元数据；不要指望它驱动流程 |
| `project.genre` | 字符串 | 未设置 | `hand` | 几乎没有 | 元数据 |
| `project.version` | 字符串 | 未设置 | `hand` | 几乎没有 | 元数据 |
| `project.stage` | `Concept`、`Systems Design`、`Technical Setup`、`Pre-Production`、`Production`、`Polish`、`Release` | `Concept`（检测器推断） | `start`、`gate` | `/help`、`/gate-check`、`/project-stage-detect`、状态栏、`/team-release`、`/launch-checklist` | 会双写 `production/stage.txt`；不要手改来跳过门禁 |
| `project.kind` | `game`、`tool`、`engine`、`mod`、`library` | `game`（文档默认） | `settings`（会警告“保留”）、`hand` | **无人读（保留）** | 设置了也不会改变行为 |

### 14.2 流程档位、文档、QA 与性能

| 键 | 取值 | 默认 | 写入者 | 主要读取者 | 备注 |
|---|---|---|---|---|---|
| `modes.rigor` | `minimal`、`standard`、`full` | `minimal` | `start`、`settings` | 无技能直接读；只作为派生源 | 套餐键；不要预置它派生的六个键 |
| `modes.review_mode` `*` | `full`、`lean`、`solo` | 由 rigor 派生：minimal→solo，standard→lean，full→full | `settings`、`migrate` | `architecture-review`、`design-review`、`review-all-gdds`、`gate-check`、`design-system`、`art-bible`、`ux-design`、`brainstorm` | 决定“评审关卡跑不跑/多宽”，不决定文档要求 |
| `modes.workflow` | `minimal`、`standard`、`full` | 由 rigor 派生：同值 | `settings`、`migrate` | `design-system`、`create-architecture`、`gate-check`、`create-epics`、`create-stories`、`story-readiness`、`review-all-gdds` | 决定“要求哪些文档/产物存在” |
| `modes.automation` `*` | `collaborative`、`guided`、`autonomous` | `collaborative` | `start`、`settings` | 几乎所有会写文件/提问的技能 | 决定“先问再做”还是“边说边做” |
| `modes.automation_always_ask` `*` | 类别列表：`scope_changes`、`file_deletions`、`schema_changes`、`architecture_decisions`、`version_bumps`、`external_calls` | `scope_changes`、`file_deletions`、`schema_changes` | `settings` | 所有遵守 automation 的技能 | 自主模式下的“一票否决”清单 |
| `modes.story_granularity` | `coarse`、`balanced`、`fine` | 由 rigor 派生：coarse/balanced/fine | `settings` | `/create-stories`、`/sprint-plan`、`/sprint-status`、`/story-readiness` | 决定故事切多大 |
| `docs.density` | `terse`、`balanced`、`thorough` | 由 rigor 派生：terse/balanced/thorough | `settings` | `design-system`、`art-bible`、`create-architecture`、`ux-design`、`map-design`、`brainstorm` | 决定每节写多深；不决定哪些节存在 |
| `qa.level` | `minimal`、`standard`、`full` | 由 rigor 派生：同值 | `settings`、`migrate` | `/story-done`、`/story-readiness`、`/gate-check`、`/qa-plan`、`/team-qa` | 决定“要不要测试证据”；视觉/UI 截图从不豁免 |
| `qa.coverage_minimum` | `0`–`100` 或 `null` | `null` | `settings` | 只有 `/gate-check`（且 `qa.level: full`） | `null` 是“没有承诺”，不是 0 |
| `testing.framework` | `gdunit4`、`unity-test-framework`、`unreal-automation` | 引擎默认 | `setup`、`settings` | `/test-helpers` | 主要影响测试脚手架说明；实际执行看 `commands.test` |
| `testing.strict.logic` `*` | `true` / `false` | 未设置；技能默认 `true` | `settings`、`migrate` | `/story-done`、`/story-readiness`、`/gate-check`、`/smoke-check`、`/dev-story` | 逻辑测试失败是否阻断 |
| `testing.strict.integration` `*` | `true` / `false` | 未设置；技能默认 `true` | 同上 | 同上 | 集成测试失败是否阻断 |
| `testing.strict.visual` `*` | `true` / `false` | 未设置；技能默认 `true` | 同上 | 同上 | 视觉证据缺失/失败是否阻断 |
| `testing.strict.ui` `*` | `true` / `false` | 未设置；技能默认 `true` | 同上 | 同上 | UI 截图缺失是否阻断 |
| `testing.strict.config` `*` | `true` / `false` | 未设置；技能默认 `false`；`smoke-check` 未设置时按严格处理 | 同上 | 同上 | 配置/数据冒烟失败是否阻断 |
| `strict_gate_checks` | `true` / `false` | `true`（文档默认） | `settings`（会警告“保留”） | **无人读（保留）** | 不要依赖 |
| `team.size` `*` | `individual`、`small`、`studio` | 由 rigor 派生：full→studio，否则 individual | `settings` | 所有 `team-*`、`/gate-check`、会生成代理的技能 | 决定默认启用多少代理，不是团队人数 |
| `performance.target_framerate` | 整数，如 `60` | 未设置 | `settings`、`migrate` | `/perf-profile`、`/gate-check`、`/team-polish` | 未设置不是 0 |
| `performance.frame_budget_ms` | 数字，如 `16.67` | 未设置 | 同上 | 同上 | 未设置不是 0 |
| `performance.draw_call_limit` | 整数 | 未设置 | 同上 | 同上 | 未设置不是 0 |
| `performance.memory_ceiling_mb` | 整数 | 未设置 | 同上 | 同上 | 未设置不是 0 |
| `performance.enforce` `*` | `warn`、`block`、`off` | `warn` | `settings` | `/perf-profile`、`/gate-check`、CI | 超预算时警告/阻断/关闭 |

### 14.3 技术栈与运行

| 键 | 取值 | 默认 | 写入者 | 主要读取者 | 备注 |
|---|---|---|---|---|---|
| `engine.name` | `Godot`、`Unity`、`Unreal` | 未设置 | `setup`、`settings` | `engine_values`、专家路由、`project-coherence.sh`、`session-start.sh` | 模板的参考文档默认是 Godot，但 `engine.name` 本身不默认 Godot |
| `engine.version` | 字符串 | 未设置 | `setup`、`settings` | `engine_values`、`project-coherence.sh`、参考文档 pin | 建议加引号 |
| `engine.language` | `GDScript`、`C#`、`C++`、`Blueprint` | 未设置 | `setup`、`settings` | `engine_values`、`specialists.code` 选择 | Godot 的 C#/GDScript 区分还依赖 `specialists.code` |
| `engine.rendering` | 引擎相关字符串 | 由引擎和项目形态决定 | `setup`、`settings` | `project-coherence.sh`、渲染/平台相关技能 | Godot 2D 网页常用 `Compatibility` |
| `engine.physics` | 引擎相关字符串 | 由引擎和项目形态决定 | `setup`、`settings` | `project-coherence.sh`、物理相关技能 | Godot 2D 用 `Godot Physics 2D`，不是 `Jolt` |
| `engine.path` | 路径字符串 | 未设置 | `setup`、`settings` | `/smoke-check`、需要定位编辑器的代理/脚本 | 不会自动拼进 `commands.*` |
| `platform.multiplayer` | `true` / `false` | 未设置（不等于 `false`） | `settings` | `/security-audit`、`/team-release`、`security-engineer` | 未设置会触发提问 |
| `platform.online` | `true` / `false` | 未设置（不等于 `false`） | `settings` | `/security-audit`、`/team-release`、`security-engineer` | 与 multiplayer 独立 |
| `platform.cert_tier` | `none`、`itch`、`steam`、`console` | 未设置（不等于 `none`） | `settings` | `/launch-checklist`、`/release-checklist` | 未设置会问；`none` 是明确选择 |
| `platform.targets` | `PC`、`Mobile`、`Web`、`Console` 的列表 | 未设置 | `settings`、`migrate` | `input_methods`、`/setup-engine`、UX/安全相关技能 | 平台列表会参与输入法派生 |
| `platform.primary_input` | 字符串 | 未设置 | `settings`、`migrate` | `input_methods` | 会被归一化 |
| `platform.gamepad_support` | `Full`、`Partial`、`None` | 未设置 | `settings`、`migrate` | `input_methods` | `Partial` 也算支持 |
| `platform.touch_support` | `Full`、`Partial`、`None` | 未设置 | `settings`、`migrate` | `input_methods` | `Partial` 也算支持 |
| `specialists.code` | 代理名 | 未设置；`primary` 另由 `engine.name` 派生 | `setup`、`settings` | `/dev-story`、`/code-review`、`/architecture-*`、`/team-*` | Godot 用它区分 GDScript/C# |
| `specialists.shader` | 代理名 | 未设置 | `setup`、`settings` | 同上 | 着色器相关任务 |
| `specialists.ui` | 代理名 | 未设置 | `setup`、`settings` | 同上 | UI 相关任务 |
| `specialists.additional` | 代理名列表 | 未设置 | `setup`、`settings` | 同上 | 没有就跳过 |
| `naming.classes` | `PascalCase`、`snake_case`、`camelCase` | 引擎默认 | `setup`、`settings` | `/dev-story`、`/create-control-manifest`、`/asset-spec`、架构类技能 | 指南，不是自动门禁 |
| `naming.variables` | `snake_case`、`camelCase` | 引擎默认 | 同上 | 同上 | 同上 |
| `naming.constants` | `SCREAMING_SNAKE`、`PascalCase` | 引擎默认 | 同上 | 同上 | 三个引擎的默认都是 SCREAMING_SNAKE |
| `naming.signals` | `past_tense`、`on_event_name` | 引擎默认 | 同上 | 同上 | Unreal 语义上是 PascalCase `On<Event>` |
| `naming.files` | `snake_case`、`PascalCase`、`kebab-case` | 引擎默认 | 同上 | 同上 | 只描述源文件 |
| `naming.scenes` | `snake_case`、`PascalCase` | 引擎默认 | 同上 | 同上 | Unity 常叫 prefabs；Unreal 对应 Levels/Maps |
| `commands.build` | 字符串或 OS 映射 | 未设置；setup 填 | `setup`、`settings` | `/smoke-check`、`project-coherence.sh`、发布清单（只引用） | 预设名必须问，不能猜 |
| `commands.test` | 字符串或 OS 映射 | 未设置；setup 填 | `setup`、`settings` | `/smoke-check`、`/hotfix`、`/bug-report`、`/day-one-patch`、`/dev-story` | 真正执行测试 |
| `commands.run` | 字符串或 OS 映射 | 未设置；setup 填 | `setup`、`settings` | `/dev-story` 的运行观察步骤 | 启动游戏，不是编辑器 |
| `commands.smoke` | 字符串或 OS 映射 | 未设置；setup 填 | `setup`、`settings` | `/dev-story`、`/smoke-check` | 最小启动检查 |

### 14.4 例外、开关与保留项

| 键 | 取值 | 默认 | 写入者 | 主要读取者 | 备注 |
|---|---|---|---|---|---|
| `workflow_overrides.edge_cases` | `true` / `false` | `false` | `settings` | 设计/评审/门禁类技能 | `standard` 已要求 Edge Cases，这里是额外强制 |
| `workflow_overrides.tuning_knobs` | `true` / `false` | `false` | `settings` | 同上 | `full` 已要求 Tuning Knobs |
| `workflow_overrides.art_bible_strict` | `true` / `false` | `false` | `settings` | 美术/门禁类技能 | 没有视觉资产也要求全 9 节 |
| `workflow_overrides.system_overrides` | 映射：系统名 → `minimal/standard/full` | 空 | `settings` | 所有按系统工作的技能 | 双向替换，可更松也可更严；必须在根目录 |
| `features.session_state` `*` | `on` / `off` | `on` | `settings` | `session-start.sh`、`session-stop.sh`、`pre-compact.sh`、`log-agent*.sh` | 只有 `off` 关闭；`false` 是非法值，会回退到 `on` |
| `features.token_budget_warn_at` `*` | `0.0`–`1.0` | `0.7`（文档默认） | `settings` | **无人读（保留）** | 不要依赖 |
| `features.read_guard` `*` | `on` / `off`（也接受 `true`/`false`） | `on` | `settings` | `validate-read.sh` / `read-guard.sh` | 拒绝整文件读大文档 |
| `features.read_guard_kb` `*` | 整数 KB | `40` | `settings` | 同上 | 超过阈值触发守卫 |
| `updates.check` `*` | `true` / `false` | `true` | `settings` | `session-start.sh` / `update-check.sh` | CI 自动关闭；最多每天一次 |
| `accessibility.target` | `none`、`standard`、`aaa` | `none`（文档默认） | `settings` | **无人读（保留）** | 不要依赖 |
| `cadence.sprint_length` | `1w`、`2w`、`3w`、`4w`、`none` | `2w`（文档默认） | `settings` | **无人读（保留）** | 不要依赖 |
| `cadence.milestone_length` | `4w`、`6w`、`8w`、`12w` | `8w`（文档默认） | `settings` | **无人读（保留）** | 不要依赖 |

### 14.5 派生关系速查

| 你设置的值 | 可能派生出什么 | 用在哪里 |
|---|---|---|
| `modes.rigor` | `review_mode`、`workflow`、`docs.density`、`qa.level`、`story_granularity`、`team.size` | 所有设计/故事/评审技能 |
| `engine.name` | `specialists.primary` | 所有引擎相关实现/评审 |
| `specialists.*` | 无（直接使用） | `dev-story`、`code-review`、`team-*` |
| `platform.*` | `input_methods` | UX、输入、安全、发布 |
| `testing.strict.*` | `gate_level`（blocking/advisory） | 故事完成、门禁、冒烟 |
| `modes.review_mode` | `review_gate`（skill-gates/phase-gates） | 评审类技能、门禁 |
| `performance.*` | `perf_budgets` | 性能分析、门禁 |

记住一句话：**派生值不是配置键。** 你不需要在 YAML 里写 `gate_level`、`input_methods`、`perf_budgets`；它们是用上面的源键算出来的。

## 15. 进一步阅读

| 文件 | 读什么 |
|---|---|
| [.claude/docs/effects-map.md](../../.claude/docs/effects-map.md) | 最完整的 schema 与每个设置的影响表（权威） |
| [.claude/docs/config-resolution.md](../../.claude/docs/config-resolution.md) | 解析链、技能注入方式、来源标签 |
| [.claude/docs/settings-guidance.md](../../.claude/docs/settings-guidance.md) | 怎么根据项目情况选档位 |
| [.claude/hooks/yaml-helper.sh](../../.claude/hooks/yaml-helper.sh) | 运行时真相：枚举、默认值、白名单、派生算法 |
| [.claude/scripts/config-set.sh](../../.claude/scripts/config-set.sh) | 单键写入与校验 |
| [.claude/scripts/set-stage.sh](../../.claude/scripts/set-stage.sh) | 阶段写入与双写 |
| [.claude/skills/setup-engine/references/5.5-project-yaml.md](../../.claude/skills/setup-engine/references/5.5-project-yaml.md) | 引擎四块配置的生成规则 |
| [.claude/docs/workflow-catalog.yaml](../../.claude/docs/workflow-catalog.yaml) | 七阶段流程目录 |
| [CLAUDE.md](../../CLAUDE.md) | 代理常驻指令与引擎参考导入 |
| [配置体系与rigor心智模型.md](配置体系与rigor心智模型.md) | rigor 的设计动机与配置体系总览 |
| [7阶段逐阶指南.md](7阶段逐阶指南.md) | 每个阶段该做什么、门禁要什么 |
| [setup-engine引擎配置指南.md](setup-engine引擎配置指南.md) | 引擎配置的完整操作 |
| [technical-preferences完全指南.md](technical-preferences完全指南.md) | 旧技术栈文件的逐节解析、生命周期与回退机制 |

---

## 附：一句话总结

`project.yaml` 不是一张“填完就完了”的表，而是一套**分层的决策协议**：

- 项目事实放在哪里、
- 流程档位由谁决定、
- 个人偏好如何不污染团队、
- 非法输入如何降级、
- 每个值来自哪里、

——这五件事，才是它真正想解决的问题。理解了这些，你就不需要背键名，也能从任意一个配置项推断出它对项目的影响。

技术栈也是同一套思路：它不是一行 `engine: Godot`，而是“配置 → 说明书 → 技能注入 → 专家路由 → 命令执行 → 一致性检查”的接力。任何一环缺失，代理都可能在“看起来懂”的情况下做错。
