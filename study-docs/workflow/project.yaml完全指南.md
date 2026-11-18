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
| `testing.framework` | 用哪个测试框架 | 引擎默认：Godot `gdunit4`、Unity `unity-test-framework`、Unreal `unreal-automation`；由 `/setup-engine` 自动填 | `/setup-engine`、`/settings` 写；`qa-plan`、`dev-story`、`regression-suite`、`smoke-check` 读 |
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
| `system_overrides` | `workflow_overrides.system_overrides` | 14 个消费技能一次性拿到全部覆盖 |
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

## 13. 进一步阅读

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

---

## 附：一句话总结

`project.yaml` 不是一张“填完就完了”的表，而是一套**分层的决策协议**：

- 项目事实放在哪里、
- 流程档位由谁决定、
- 个人偏好如何不污染团队、
- 非法输入如何降级、
- 每个值来自哪里、

——这五件事，才是它真正想解决的问题。理解了这些，你就不需要背键名，也能从任意一个配置项推断出它对项目的影响。
