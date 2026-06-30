# 16 · design 目录指南

## 这篇文档解决什么问题

上一篇（[15 · docs 目录指南](file:///workspace/study-docs/15-docs目录指南.md)）讲了项目根目录下的 `docs/`——那里放的是"技术文档 + 引擎字典 + 案例"。这篇专门讲另一个根目录 [design/](file:///workspace/design/)——它放的是**"游戏设计文档"**（Game Design Documents，简称 GDD）。

读完这篇你会明白：

- `design/` 里每个文件是干什么的（作用）
- 怎么用它（如何使用）
- 为什么需要它（为什么要用）
- 在 7 阶段开发流程的什么时候会碰到它（开发流程中何时被使用）

> ⚠️ **重要前提**：当前项目的 `design/` 目录是"骨架状态"——只有两个规范文件，还没有实际的设计文档。这是正常的：设计文档是在你开始做游戏后、由技能按需产出的。这篇会讲清"现在有什么"和"将来会有什么"。

---

## 一句话定位

> **`design/` = 游戏的"设计大脑"。`docs/` 回答"怎么实现"，`design/` 回答"做什么、为什么这么做"。**

---

## 类比：`design/` vs `docs/`，别搞混

很多人第一次看会混淆这两个目录。用"盖房子"类比：

| | `design/`（设计） | `docs/`（技术） |
|---|---|---|
| 类比 | 建筑师的**设计图纸**（户型、风格、用途） | 工程师的**施工图纸**（承重、管线、材料） |
| 回答什么 | 这个房间是卧室还是厨房？多大？给谁用？ | 这面墙用什么砖？承重多少？电线怎么走？ |
| 游戏里 | 战斗系统怎么玩？伤害怎么算？敌人掉什么？ | 用什么架构？状态归谁管？用哪个引擎 API？ |
| 谁产出 | 游戏设计师（game-designer 等设计师智能体） | 程序员（lead-programmer 等技术智能体） |
| 文件类型 | GDD、UX 规范、quick-spec | ADR、引擎参考、注册表 |

**记住**：先有 `design/`（设计决定做什么），后有 `docs/`（技术决定怎么做）。设计文档是技术文档的输入。

---

## 类比：把 `design/` 想象成游戏公司的"设计部档案柜"

想象你走进游戏公司的设计部，档案柜分几层：

| 档案柜分层 | 对应 `design/` 里的东西 | 你什么时候翻 |
|-----------|----------------------|-------------|
| 柜门贴的"写作须知" | [CLAUDE.md](file:///workspace/design/CLAUDE.md) | 要往这个柜子放新设计文档前 |
| 一本"实体登记册" | [registry/entities.yaml](file:///workspace/design/registry/entities.yaml) | 写新文档时查"哥布林血量是多少？谁也在用这个数？" |
| **还没开的抽屉**（按需创建） | `gdd/`、`ux/`、`quick-specs/`、`narrative/`、`levels/`、`balance/` | 做到对应阶段才会打开 |

关键：**现在只有前两层（写作须知 + 实体登记册），抽屉是空的等你来填**。

---

## 全景地图

```
design/
├── CLAUDE.md                      # 目录级写作规范（写 GDD / UX spec 时遵守）
├── registry/
│   └── entities.yaml              # 游戏世界实体注册表（跨 GDD 一致性的"事实之源"）
│
│   ── 以下子目录在开发流程中按需创建，目前不存在 ──
├── gdd/                           # 游戏设计文档（每个系统一份）
│   ├── systems-index.md           # 系统总索引（所有系统、依赖、优先级）
│   ├── game-concept.md            # 游戏概念文档（立项时产出）
│   └── [system-slug].md           # 各系统 GDD（combat-system.md 等）
├── quick-specs/                   # 轻量规范（小改动、调数值用）
├── ux/                            # UX 规范（每个屏幕一份）
│   ├── [screen-name].md
│   ├── hud.md
│   ├── interaction-patterns.md
│   └── accessibility-requirements.md
├── narrative/                     # 故事、设定、对话
├── levels/                        # 关卡设计文档
└── balance/                       # 平衡性数据表
```

下面逐个讲。**先讲现在就存在的两个文件，再讲将来会出现的子目录**。

---

## 现在就存在的文件

### 1. `design/CLAUDE.md` —— 目录级写作规范

**作用**：这是贴在 `design/` 目录门口的"写作须知"。它告诉 AI（和人类）：在这个目录下写或改设计文档时，必须遵守哪些格式标准。主要管三类东西——**GDD（游戏设计文档）、quick-specs（轻量规范）、UX 规范**。

**关键内容**（来自 [design/CLAUDE.md](file:///workspace/design/CLAUDE.md)）：

GDD 必须包含 **8 个必填章节**，且顺序固定：

| # | 章节 | 写什么 |
|---|------|--------|
| 1 | Overview（概述） | 一段话总结这个系统 |
| 2 | Player Fantasy（玩家幻想） | 玩家用这个系统时想象自己是什么、感受什么 |
| 3 | Detailed Rules（详细规则） | 不含糊的机制规则 |
| 4 | Formulas（公式） | 所有数学计算，带变量定义和取值范围 |
| 5 | Edge Cases（边界情况） | 罕见情况怎么处理，明确写出 |
| 6 | Dependencies（依赖） | 这个系统连着哪些别的系统（双向） |
| 7 | Tuning Knobs（调节旋钮） | 哪些数值设计师可以安全改，安全范围是多少 |
| 8 | Acceptance Criteria（验收标准） | 怎么测试这个系统算"做完了"，具体可衡量 |

其他规则：
- 文件命名：`[system-slug].md`，如 `movement-system.md`、`combat-system.md`
- 系统索引：`design/gdd/systems-index.md`——加新 GDD 时要更新它
- 设计顺序：Foundation（基础）→ Core（核心）→ Feature（功能）→ Presentation（表现）→ Polish（打磨）
- 校验：写完任一 GDD 跑 `/design-review`；写完一批相关的跑 `/review-all-gdds`

**如何使用**：你不用主动"运行"它。Claude Code 进入 `design/` 目录工作时自动读到它（CLAUDE.md 是目录级配置文件）。你只要知道：**当 AI 帮你创建 GDD 或 UX 规范时，它会自动按这里的规矩来**——8 个章节一个不少、顺序不变、命名用 slug。

**为什么要用**：没有它，每个 GDD 长得都不一样，有的漏了边界情况、有的没写验收标准。后续的 `/design-review`、`/create-stories`、`/story-done` 都没法自动解析。统一格式 = 自动化能跑起来的前提，也是质量保证——8 个章节是游戏设计行业的硬经验，漏一个都可能埋雷。

**开发流程中何时被使用**：**Phase 2（系统设计）** 最密集——这是 GDD 集中产出的阶段。Phase 4（预制作）写 UX 规范时也会触发。

> 📌 CLAUDE.md 的机制和 `docs/CLAUDE.md` 完全一样，只是管的文档类型不同。想理解 CLAUDE.md 怎么工作，看 [11-CLAUDE.md 编写指导](file:///workspace/study-docs/11-CLAUDE.md编写指导.md)。

---

### 2. `design/registry/entities.yaml` —— 游戏世界实体注册表

**作用**：这是**跨 GDD 一致性的"事实之源"（single source of truth）**。单个 GDD 只管一个系统，但游戏世界里有些东西会出现在多个文档里——比如"哥布林"这个敌人，在战斗 GDD 里定义血量和伤害，在物品 GDD 里定义它掉什么，在经济 GDD 里定义掉落物值多少钱。这个文件把这些**跨文档共享的事实**登记下来，让写新 GDD 时能提前发现冲突。

**登记四类东西**（来自 [design/registry/entities.yaml](file:///workspace/design/registry/entities.yaml)）：

| 类别 | 防止什么冲突 | 举例 |
|------|-------------|------|
| **entities**（实体） | 两个 GDD 对同一敌人血量写不一样的数 = 玩家体验混乱 | 哥布林血量 40，在 combat.md 定义，inventory.md 引用 |
| **items**（物品） | 同一物品在不同 GDD 里价值/重量不一致 = 经济崩坏 | 哥布林手臂值 5 金、重 1、可堆叠 |
| **formulas**（公式） | 一个 GDD 的公式输出对不上另一个 GDD 的输入 = 集成 bug | 伤害公式输出 0-999，血量系统按这个扣血 |
| **constants**（常量） | 跨系统约定的数值对不上 = 逻辑错误 | 金币携带上限 9999，经济定义、背包执行、HUD 显示 |

**关键规则**：

- 只登记**跨系统边界**的事实。只在一个 GDD 里用的内部细节不用登记。
- **永远不删条目**——作废了标 `status: deprecated`。
- 值改了就更新值、设 `revised` 日期、加注释记旧值和谁改的。
- `source` 是"权威 GDD"（拥有这个事实的文档），其他引用它的 GDD 列在 `referenced_by` 里。
- 新 GDD 引用已有条目时，把自己的路径加到 `referenced_by`，**不要建重复条目**。

**谁写谁读**：

| 角色 | 技能 / 时机 |
|------|------------|
| 写入 | `/design-system`（GDD 章节批准后，Phase 5） |
| 写入 | `/consistency-check`（解决冲突时） |
| 读取 | `/design-system`（写之前查冲突；写完 Section C/D 后再查一次） |
| 读取 | `/consistency-check`（主要输入——先 grep 这个文件，再查 GDD） |
| 读取 | `/review-all-gdds`（Phase 1 建立基线） |
| 读取 | `/architecture-review`（验证数据结构和接口） |

**如何使用**：你**不手动编辑**这个文件。它由 `/design-system` 和 `/consistency-check` 自动维护。文件里还贴心地写了 `Grep` 搜索模式——比如查"所有实体名"用 `Grep pattern="^  - name:"`，查"哥布林"用 `Grep pattern="  - name: goblin"`，查"combat.md 拥有什么"用 `Grep pattern="source: design/gdd/combat.md"`。

**为什么要用**：假设战斗 GDD 写"哥布林血量 40"，后来平衡性调整改成 50，但物品 GDD 里还按 40 算掉落逻辑——玩家就会遇到"血量显示 50 但打死不掉东西"的 bug。有了这个注册表，`/design-system` 写新 GDD 前会先 grep 它，发现"哥布林"已存在就引用而不是重写，`/consistency-check` 能扫出数值不一致。

**开发流程中何时被使用**：

- **Phase 2**（写 GDD）——这是它最活跃的阶段。`/design-system` 写每个 GDD 时都会读写它。
- **Phase 5**（生产）——`/consistency-check` 跨 GDD 检查时读它。

> 📌 这个文件和 [docs/registry/architecture.yaml](file:///workspace/docs/registry/architecture.yaml) 是一对孪生兄弟：那个登记跨 ADR 的**技术约束**（状态归属、接口契约、性能预算），这个登记跨 GDD 的**设计事实**（实体、物品、公式、常量）。机制完全一样，只是管的领域不同。

---

## 将来会出现的子目录（按需创建）

下面这些子目录现在不存在，会在开发流程中由技能按需创建。你只要知道"做到那一步时它们会出现"。

### 3. `design/gdd/` —— 游戏设计文档

**作用**：放每个系统的 GDD，是 `design/` 的核心。每个文件是一个系统的完整设计——8 个章节齐全。

**会出现的文件**：

| 文件 | 何时产出 | 谁产出 |
|------|---------|--------|
| `game-concept.md` | Phase 1（概念） | `/brainstorm` 协作产出 |
| `systems-index.md` | Phase 1 末/Phase 2 初 | `/map-systems` 产出 |
| `[system-slug].md`（如 `combat-system.md`） | Phase 2（系统设计） | `/design-system` 逐节产出 |

**如何使用**：你不手动创建这些文件。`/design-system` 会引导你一节一节写，每写完一节就存盘（防崩溃丢成果）。`systems-index.md` 是总目录，记录所有系统、它们的依赖关系、优先级（MVP/垂直切片/Alpha/完整愿景）。

**为什么要用**：GDD 是整个开发流程的"源头"。`/create-stories` 把 GDD 拆成可实现的故事，`/dev-story` 实现时对照 GDD，`/story-done` 验收时对照 GDD 的验收标准。没有 GDD，后续全是空中楼阁。

**开发流程中何时被使用**：Phase 1 末开始（概念文档、系统索引），Phase 2 集中产出（各系统 GDD），Phase 5 反复引用（实现和验收）。

---

### 4. `design/quick-specs/` —— 轻量规范

**作用**：放小改动、调数值、微调机制用的"轻量规范"——不值得写一整份 8 章节 GDD 的小修改。

**如何使用**：跑 `/quick-design "描述"`，比如 `/quick-design "侧翼攻击加 10% 伤害"`。它在 `design/quick-specs/` 下产出一个轻量 spec，而不是完整 GDD。

**为什么要用**：不是所有改动都值得写 8 章节。调个数值、加个小机制，用 quick-spec 省时间又不丢记录。

**开发流程中何时被使用**：Phase 2 之后随时——生产中调平衡、加小功能时最常用。

---

### 5. `design/ux/` —— UX 规范

**作用**：放每个屏幕的 UX（用户体验）设计规范。GDD 管"系统怎么玩"，UX 规范管"屏幕长什么样、玩家怎么交互"。

**会出现的文件**（来自 [design/CLAUDE.md](file:///workspace/design/CLAUDE.md)）：

| 文件 | 内容 |
|------|------|
| `[screen-name].md` | 每个屏幕一份（主菜单、核心游戏 HUD 等） |
| `hud.md` | HUD（抬头显示）设计 |
| `interaction-patterns.md` | 交互模式库（16 种标准控件 + 游戏专属模式） |
| `accessibility-requirements.md` | 无障碍需求（定一个等级：基础/标准/全面/典范） |

**如何使用**：跑 `/ux-design [屏幕名]` 产出，比如 `/ux-design main-menu`。写完跑 `/ux-review` 校验，过了才能交给 `/team-ui` 实现。

**为什么要用**：不写 UX 规范，程序员做 UI 时只能猜，做出来常常不符合设计意图。UX 规范把"屏幕分区、状态、交互、数据需求、事件、无障碍、本地化"提前定好。无障碍需求尤其重要——它在 Phase 3 就要定等级，Phase 4 的 UX 规范都要符合这个等级。

**开发流程中何时被使用**：`accessibility-requirements.md` 在 Phase 3（技术搭建）就要定；其他 UX 规范在 Phase 4（预制作），写完才能拆故事。

---

### 6. `design/narrative/` —— 故事、设定、对话

**作用**：放世界观、故事弧、角色表、对话等叙事内容。

**如何使用**：用 `world-builder` 智能体建世界观，用 `narrative-director` 设计故事结构，用 `narrative-character-sheet.md` 模板写角色表。

**为什么要用**：有故事的游戏需要把叙事内容和系统设计分开管理，避免混在 GDD 里太乱。

**开发流程中何时被使用**：Phase 2（系统设计）末段——如果游戏有叙事，这是建叙事内容的时候。

---

### 7. `design/levels/` —— 关卡设计文档

**作用**：放每个关卡的设计文档——布局、遭遇、流程。

**如何使用**：用 `level-designer` 智能体产出，参考 `.claude/docs/templates/level-design-document.md` 模板。

**为什么要用**：关卡是游戏体验的载体，需要和系统设计分开文档化。

**开发流程中何时被使用**：Phase 5（生产）——开始做实际关卡时。

---

### 8. `design/balance/` —— 平衡性数据

**作用**：放平衡性电子表格和数据——伤害表、掉落率、经济曲线等。

**如何使用**：配合 `/balance-check` 技能分析平衡数据，找异常值、坏掉的进度曲线、退化策略。

**为什么要用**：平衡数据集中管理，方便 `/balance-check` 自动分析，也方便设计师调数值。

**开发流程中何时被使用**：Phase 5-6（生产和打磨）——有实际数值可调时。

---

## 在开发流程中何时碰到 `design/`

把上面所有文件映射到 7 阶段开发流程：

| 阶段 | 主要碰到的 `design/` 文件 | 干什么 |
|------|--------------------------|--------|
| **Phase 1 概念** | `gdd/game-concept.md`、`gdd/systems-index.md` | `/brainstorm` 产概念文档；`/map-systems` 产系统索引 |
| **Phase 2 系统设计** | `gdd/[system].md`、[registry/entities.yaml](file:///workspace/design/registry/entities.yaml)、[CLAUDE.md](file:///workspace/design/CLAUDE.md)、`narrative/` | `/design-system` 逐个写 GDD（遵守 CLAUDE.md 格式）；每写一个就更新 entities 注册表；有叙事就建 narrative |
| **Phase 3 技术搭建** | `ux/accessibility-requirements.md` | 定无障碍等级（这是 UX 的前置，但归在 Phase 3） |
| **Phase 4 预制作** | `ux/*.md`、`quick-specs/` | `/ux-design` 写各屏幕 UX 规范；小改动用 `/quick-design` |
| **Phase 5 生产** | `gdd/`、`levels/`、`balance/`、[registry/entities.yaml](file:///workspace/design/registry/entities.yaml) | 实现时反复查 GDD；做关卡产出 levels/；调数值产出 balance/；`/consistency-check` 跨 GDD 查一致性 |
| **Phase 6 打磨** | `balance/`、`quick-specs/` | `/balance-check` 分析平衡；微调用 quick-spec |
| **Phase 7 发布** | （不直接碰 `design/`） | 发布主要靠技能和 `docs/`，设计文档这时已稳定 |
| **全程** | [CLAUDE.md](file:///workspace/design/CLAUDE.md) | 写任何设计文档都受它约束 |

**一句话总结**：`design/` 在 **Phase 2（系统设计）最密集**——这是 GDD 集中产出、entities 注册表最活跃的阶段。Phase 4 写 UX，Phase 5-6 做关卡和平衡。

---

## 现在为什么是"骨架"？

你会注意到：当前 `design/` 只有 `CLAUDE.md` 和 `registry/entities.yaml` 两个文件，`gdd/`、`ux/` 等子目录都不存在。原因：

1. **这是模板项目，不是某个具体游戏**。设计文档是"做某个游戏"时才产生的——比如你决定了"做一个 Roguelike 牌组构建游戏"，才会有 `combat-system.md`、`card-system.md` 这些具体 GDD。
2. **子目录按需创建**。WORKFLOW-GUIDE 里说得很清楚："你不需要 day one 就建好所有目录，做到需要它的阶段再建"。系统会在你跑到对应技能时自动创建。
3. **规范先行**。`CLAUDE.md` 和 `entities.yaml` 是"规则"，规则要先于"内容"存在——这样内容产出时才有规矩可循。

所以**当前状态完全正常**。等你开始 `/brainstorm` 立项、`/design-system` 写 GDD 时，这些子目录就会逐个出现。

---

## 常见误区

**误区 1："我得手动创建 `gdd/`、`ux/` 这些子目录。"**
❌ 不用。技能跑到对应步骤会自动创建。你手动建反而可能建错位置或漏建配置。

**误区 2："entities.yaml 我得自己填。"**
❌ 不要手动编辑。它由 `/design-system`（写 GDD 时）和 `/consistency-check`（解决冲突时）自动维护。手动填容易和 GDD 不同步。

**误区 3："GDD 我想怎么写就怎么写。"**
❌ 不行。[CLAUDE.md](file:///workspace/design/CLAUDE.md) 规定 8 个必填章节且顺序固定。`/design-review` 会校验，缺章节会判 NEEDS REVISION。这 8 个章节是行业经验，漏一个都可能埋雷（比如漏了 Edge Cases，实现时遇到罕见情况就得猜）。

**误区 4："`design/` 和 `docs/` 是一回事。"**
❌ 不是。`design/` 管"做什么、为什么"（游戏设计），`docs/` 管"怎么实现"（技术架构）。GDD 是 ADR 的输入——先有设计决策，才有技术决策。

**误区 5："概念文档（game-concept.md）也是 GDD。"**
⚠️ 不完全是。它放在 `design/gdd/` 下，但它是"立项文档"——定义游戏 pillars、核心循环、目标受众，不是某个系统的 8 章节 GDD。系统的 GDD 是 `combat-system.md` 这种。

---

## 小测验

**Q1**：你让 AI 帮你写战斗系统的 GDD，它只写了 5 个章节就存盘了。哪个文件规定了必须有 8 个章节？

<details>
<summary>答案</summary>

[design/CLAUDE.md](file:///workspace/design/CLAUDE.md)——它规定 GDD 必须包含 8 个必填章节（Overview / Player Fantasy / Detailed Rules / Formulas / Edge Cases / Dependencies / Tuning Knobs / Acceptance Criteria）且顺序固定。`/design-review` 会校验，缺章节判 NEEDS REVISION。

</details>

**Q2**：你的战斗 GDD 里写"哥布林血量 40"，物品 GDD 里却写"哥布林血量 35"。哪个文件能帮 AI 在写第二个 GDD 时就发现冲突？

<details>
<summary>答案</summary>

[design/registry/entities.yaml](file:///workspace/design/registry/entities.yaml)——它的 `entities` 段登记跨 GDD 共享的实体。`/design-system` 写新 GDD 前会先 grep 它，发现"哥布林"已存在（source 是 combat.md，血量 40）就会引用而不是重写，避免数值不一致。

</details>

**Q3**：你想加一个小改动"侧翼攻击加 10% 伤害"，不值得写整份 GDD。该用什么技能、产物放哪？

<details>
<summary>答案</summary>

用 `/quick-design "侧翼攻击加 10% 伤害"`，产物放在 `design/quick-specs/` 下。这是轻量规范，用于调数值、小机制、微调，不用走完整 8 章节 GDD 流程。

</details>

**Q4**：`design/` 和 `docs/` 都有 `registry/` 下的 yaml 文件。它们分别管什么？

<details>
<summary>答案</summary>

- [design/registry/entities.yaml](file:///workspace/design/registry/entities.yaml) 管跨 GDD 的**设计事实**（实体、物品、公式、常量）——防"哥布林血量两个文档写不一样的数"。
- [docs/registry/architecture.yaml](file:///workspace/docs/registry/architecture.yaml) 管跨 ADR 的**技术约束**（状态归属、接口契约、性能预算、API 选择、禁止模式）——防"两个 ADR 都说自己管玩家血量"。

机制一样（永久 ID、source/referenced_by、不删只标 deprecated），领域不同。

</details>

---

## 动手

1. 打开 [design/CLAUDE.md](file:///workspace/design/CLAUDE.md)，数一下 GDD 的 8 个必填章节，想想每个章节防的是什么坑（比如 Edge Cases 防"罕见情况没考虑"）。
2. 打开 [design/registry/entities.yaml](file:///workspace/design/registry/entities.yaml)，读注释里的示例条目（goblin、goblin_arm、damage_formula、gold_carry_limit），理解"source 是权威 GDD，referenced_by 是引用方"的关系。
3. 对比 [design/registry/entities.yaml](file:///workspace/design/registry/entities.yaml) 和 [docs/registry/architecture.yaml](file:///workspace/docs/registry/architecture.yaml)——看它们的注释结构有多像，体会"设计事实"和"技术约束"的分工。
4. 翻 [docs/WORKFLOW-GUIDE.md](file:///workspace/docs/WORKFLOW-GUIDE.md) 的 Phase 2 部分，看 `/design-system` 怎么一节一节产出 GDD 并更新 entities 注册表。

---

## 一句话带走

> **`design/` = 游戏的"设计大脑"。现在只有写作规范（CLAUDE.md）和实体登记册（entities.yaml）两个骨架文件，将来会按需长出 GDD、UX 规范、quick-specs、叙事、关卡、平衡等子目录。规范先行、内容后填——规则立好了，技能产出内容时才不会跑偏。**
