# CLAUDE.md 编写指导

> 基于 [Claude Code Game Studios](file:///workspace/CLAUDE.md) 项目的实战范例，结合编写实践与原理，教你写出高质量的 CLAUDE.md。

---

## 这份文档解决什么问题

很多人写 CLAUDE.md 容易陷入两个极端：

- **太简陋**：只写一句"你是一个 Python 专家"，浪费了 CLAUDE.md 这个最强配置入口。
- **太冗长**：把所有规范、流程、上下文都塞进去，撑爆上下文窗口，AI 反而抓不住重点。

这个项目示范了**第三条路**：精简的主文件 + `@` 引用拆分 + 分层 CLAUDE.md。我们用它当活教材，回答这些常见问题：

- 一开始要设定"专家角色"吗？
- 为什么这个项目先写项目名称和介绍？
- `@路径` 引用语法背后是什么原理？
- 哪些内容该放 CLAUDE.md，哪些该拆出去？
- 子目录的 CLAUDE.md 有什么用？
- 怎么避免 CLAUDE.md 撑爆上下文？

---

## 第一性原理：CLAUDE.md 到底是什么

在讲"怎么写"之前，先讲"它是什么、为什么"。

### CLAUDE.md 是"项目级系统提示词"

Claude Code 启动时会**自动读取**项目根目录的 `CLAUDE.md`，把它的内容注入到 AI 的系统提示词里。这意味着：

- **它是对话开始前就生效的"预设"**，不是对话中临时给的指令。
- **它对每次对话都生效**，不需要你每次重复说。
- **它和 `.claude/settings.json` 是两大配置入口**：CLAUDE.md 管"AI 该知道什么、该怎么做"，settings.json 管"AI 能做什么、不能做什么"。

**类比**：CLAUDE.md 是"新员工入职手册"，settings.json 是"门禁系统"。新员工上班第一天先读入职手册（知道公司是干嘛的、怎么干活），门禁系统决定他能进哪些门。

### 为什么 CLAUDE.md 比单次对话指令强

| 维度 | 单次对话指令 | CLAUDE.md |
|------|--------------|-----------|
| 持久性 | 只对当前对话生效 | 每次对话自动加载 |
| 一致性 | 容易忘、容易变 | 始终一致 |
| 可维护性 | 改一次要重发 | 改文件即生效 |
| 团队协作 | 各人各写 | 团队共享一份 |
| 上下文成本 | 占用对话 token | 启动时加载，对话中不重复 |

**关键洞察**：CLAUDE.md 是"投入产出比最高"的配置。写一次，每次对话都受益。

---

## 核心问题 1：一开始要设定"专家角色"吗

**短答**：不一定。这个项目**没有**在 CLAUDE.md 开头设定"你是一个游戏开发专家"。

看这个项目的 [CLAUDE.md](file:///workspace/CLAUDE.md) 开头：

```markdown
# Claude Code Game Studios -- Game Studio Agent Architecture

Indie game development managed through 49 coordinated Claude Code subagents.
Each agent owns a specific domain, enforcing separation of concerns and quality.
```

它做的是**介绍项目**，不是设定角色。

### 为什么不在主 CLAUDE.md 设定角色

这个项目的角色设定在**每个 agent 的定义文件里**，不在主 CLAUDE.md。看 [creative-director.md](file:///workspace/.claude/agents/creative-director.md)：

```markdown
---
name: creative-director
model: opus
---

You are the Creative Director for an indie game project. You are the final
authority on all creative decisions...
```

**角色设定下沉到 agent 层**，主 CLAUDE.md 只管"项目是什么、怎么协作"。

### 背后的技术原理

Claude Code 的多智能体架构里，主会话和子智能体是**不同的上下文**：

- **主会话**读 CLAUDE.md → 它是"协调者/调度者"，不需要是专家。
- **子智能体**读自己的 agent 定义 → 它才是"专家"，有明确角色。

如果在主 CLAUDE.md 写"你是创意总监"，会和子智能体的角色设定**冲突或冗余**。主会话既不是创意总监，也不该是——它是调度创意总监的人。

### 什么时候该在 CLAUDE.md 设定角色

**单智能体场景**（不用 subagent）：可以在 CLAUDE.md 开头设定角色。比如：

```markdown
# 我的 Python 项目

你是一个资深 Python 后端工程师，精通 FastAPI 和 SQLAlchemy。
```

**多智能体场景**（这个项目）：主 CLAUDE.md 不设定角色，角色下沉到 agent 定义。

**判断标准**：你的项目用 `Task` 派子智能体吗？用 → 角色放 agent 文件。不用 → 角色可以放 CLAUDE.md。

---

## 核心问题 2：为什么先写项目名称和介绍

看这个项目 CLAUDE.md 的前 4 行：

```markdown
# Claude Code Game Studios -- Game Studio Agent Architecture

Indie game development managed through 49 coordinated Claude Code subagents.
Each agent owns a specific domain, enforcing separation of concerns and quality.
```

**两件事**：项目名 + 一句话定位。

### 背后的技术原理：上下文锚定

大语言模型的工作方式是**基于上下文预测下一个 token**。上下文的"开头"有**锚定效应（priming effect）**——开头的设定会影响后续所有推理。

**写"项目名称 + 一句话定位"的作用**：

1. **建立领域上下文**：让 AI 知道"我们在做游戏开发"，后续所有对话都在这个领域里理解。比如"player"在游戏语境是"玩家"，在音乐语境是"播放器"。
2. **设定项目身份**：49 个 subagent、领域分离、质量把关——这三个关键词定义了项目的"性格"。AI 后续决策会向这个性格靠拢。
3. **提供决策依据**：当 AI 面对模糊指令时，会用项目定位消歧。"加一个登录系统"在游戏项目里可能是"玩家账号系统"，在企业项目里是"员工 SSO"。

**类比**：你进一家餐厅，墙上写着"本店主营川菜"。这句话决定了你后续所有点菜、问询、期待的基调。没有这句话，你可能会问"有寿司吗"，浪费来回。

### 怎么写好这一句定位

**好定位的三要素**：领域 + 规模/方式 + 核心特征。

看这个项目：
- 领域：indie game development（独立游戏开发）
- 规模/方式：managed through 49 coordinated Claude Code subagents（49 个协同子智能体管理）
- 核心特征：each agent owns a specific domain, enforcing separation of concerns and quality（领域分离、关注点分离、质量）

**反例**：
```
# 我的项目
这是一个用 Claude Code 做的项目。
```
← 没有领域、没有规模、没有特征。AI 知道等于不知道。

**正例**：
```
# 电商后台管理系统

基于 Spring Boot 3 + Vue 3 的 B2C 电商后台，管理商品、订单、用户、营销。
采用领域驱动设计，每个模块有独立的聚合根和仓储。
```
← 领域（电商后台）、技术栈（Spring Boot 3 + Vue 3）、架构方式（DDD）。

---

## 核心问题 3：`@路径` 引用语法背后是什么原理

看这个项目 CLAUDE.md 的主体：

```markdown
## Project Structure
@.claude/docs/directory-structure.md

## Engine Version Reference
@docs/engine-reference/godot/VERSION.md

## Technical Preferences
@.claude/docs/technical-preferences.md

## Coordination Rules
@.claude/docs/coordination-rules.md

## Coding Standards
@.claude/docs/coding-standards.md

## Context Management
@.claude/docs/context-management.md
```

**6 个 `@` 引用**。主文件只有 53 行，但实际加载的内容是这 6 个子文件的总和。

### `@` 引用的技术原理

`@路径` 是 Claude Code 的**文件引用语法**。它的作用是：

> 在读取 CLAUDE.md 时，把 `@` 后面那个文件的内容**内联展开**到当前位置。

**等价于**：你手动把那个文件的内容复制粘贴到 CLAUDE.md 里。但比手动复制有三个优势：

1. **可维护性**：子文件独立修改，主文件不动。
2. **可复用性**：一个子文件可以被多个地方引用（虽然 CLAUDE.md 里通常只引一次）。
3. **按需加载**：某些实现支持延迟加载，不一定全部塞进初始上下文。

### 为什么不直接全写进 CLAUDE.md

**核心原因：上下文窗口有限**。

这个项目的 6 个子文件加起来内容很多（technical-preferences.md 87 行、coordination-rules.md 65 行、coding-standards.md 66 行、context-management.md 99 行……）。如果全塞进 CLAUDE.md：

- 主文件几百行，**难以阅读和维护**。
- 每次对话启动都加载全部，**上下文成本高**。
- 修改一处要动主文件，**容易误伤其他部分**。

`@` 引用让主文件保持"目录"的角色，细节进子文件。

### `@` 引用的设计原则

**该用 `@` 引用的**：
- 详细规范（编码标准、命名规范、测试要求）
- 长文档（架构说明、协作规则）
- 可独立维护的内容（技术栈配置、引擎版本）
- 多个 agent/skill 共同引用的"公共知识"

**不该用 `@` 引用（直接写在 CLAUDE.md）的**：
- 项目定位（开头一句话）
- 核心协作协议（几条铁律）
- 紧急提醒（"第一次会话跑 /start"）
- 跨所有子文件的总原则

看这个项目的对比：

```markdown
# 直接写（核心、简短、每次都要见）
## Collaboration Protocol
**User-driven collaboration, not autonomous execution.**
Every task follows: Question -> Options -> Decision -> Draft -> Approval
- Agents MUST ask "May I write this to [filepath]?" before using Write/Edit tools
...

# @ 引用（详细、长、可独立维护）
## Coding Standards
@.claude/docs/coding-standards.md
```

**判断标准**：内容超过 20 行且能独立成篇 → 拆出去用 `@` 引用。内容少于 10 行且是核心铁律 → 直接写。

---

## 核心问题 4：哪些内容该放 CLAUDE.md

这个项目的 CLAUDE.md 包含这些章节：

| 章节 | 内容 | 直接写还是 @ 引用 |
|------|------|---------------------|
| 标题 + 定位 | 项目名 + 一句话 | 直接写 |
| Technology Stack | 技术栈选择 | 直接写（简短） |
| Project Structure | 目录结构 | `@` 引用 |
| Engine Version Reference | 引擎版本 | `@` 引用 |
| Technical Preferences | 技术偏好 | `@` 引用 |
| Coordination Rules | 协作规则 | `@` 引用 |
| Collaboration Protocol | 协作协议 | 直接写（核心铁律） |
| Coding Standards | 编码标准 | `@` 引用 |
| Context Management | 上下文管理 | `@` 引用 |

### CLAUDE.md 应该包含的"标准章节"

基于这个项目和编写实践，推荐结构：

#### 1. 项目定位（必写，直接写）
```markdown
# 项目名 -- 一句话副标题

一段话定位：领域 + 规模/方式 + 核心特征。
```

#### 2. 技术栈（必写，直接写）
```markdown
## Technology Stack
- **Language**: Python 3.12
- **Framework**: FastAPI 0.110
- **Database**: PostgreSQL 16
- **ORM**: SQLAlchemy 2.0
```
← 简短的关键决策，直接写。AI 一眼知道用什么。

#### 3. 项目结构（推荐，@ 引用）
```markdown
## Project Structure
@.claude/docs/directory-structure.md
```
← 目录结构可能长，拆出去。

#### 4. 编码标准（必写，@ 引用）
```markdown
## Coding Standards
@.claude/docs/coding-standards.md
```
← 编码标准通常详细，拆出去。

#### 5. 协作协议（必写，直接写核心 + @ 引用细节）
```markdown
## Collaboration Protocol
**User-driven collaboration, not autonomous execution.**
- 写文件前必须问"可以写到 [路径] 吗？"
- 不经批准不提交

详见 @docs/collaboration-principle.md
```
← 核心铁律直接写（每次都要见），细节拆出去。

#### 6. 上下文管理（推荐，@ 引用）
```markdown
## Context Management
@.claude/docs/context-management.md
```
← 长会话项目必写，告诉 AI 怎么管理上下文。

#### 7. 入门指引（推荐，直接写）
```markdown
> **第一次会话？** 跑 `/start` 开始引导流程。
```
← 新人入门提示，简短直接写。

### 不该放 CLAUDE.md 的内容

- **具体业务逻辑**：放代码注释或专门文档。
- **临时指令**：放对话里，不要污染 CLAUDE.md。
- **个人偏好**：放 `CLAUDE.local.md`（见下文）。
- **agent 定义**：放 `.claude/agents/`。
- **skill 定义**：放 `.claude/skills/`。
- **hook 脚本**：放 `.claude/hooks/`。

---

## 核心问题 5：子目录的 CLAUDE.md 有什么用

这个项目有**多个 CLAUDE.md**，分布在不同目录：

```
/workspace/CLAUDE.md           ← 主配置（项目根）
/workspace/src/CLAUDE.md       ← 源码目录
/workspace/design/CLAUDE.md    ← 设计文档目录
/workspace/docs/CLAUDE.md      ← 技术文档目录
```

### 分层 CLAUDE.md 的原理

Claude Code 支持**分层 CLAUDE.md**：当 AI 在某个目录工作时，会读取该目录的 CLAUDE.md（如果有），叠加到主 CLAUDE.md 之上。

**作用**：让"目录专属规则"只在那个目录生效，不污染全局上下文。

看 [src/CLAUDE.md](file:///workspace/src/CLAUDE.md)：

```markdown
# Source Directory

When writing or editing game code in this directory, follow these standards.

## Engine Version Warning
The LLM's training data predates the pinned engine version.
**Always check `docs/engine-reference/` before using any engine API.**

## Coding Standards
- All public APIs require doc comments
- Gameplay values must be **data-driven** (external config files), never hardcoded
...
```

**关键内容**：源码目录专属的规则——引擎版本警告、编码标准、文件路由、测试要求。

**为什么不放主 CLAUDE.md**：
- 这些规则只在写 `src/` 下代码时相关，写 `design/` 文档时无关。
- 放主 CLAUDE.md 会增加无关上下文成本。
- 分层让规则"按需加载"。

### 看 [design/CLAUDE.md](file:///workspace/design/CLAUDE.md) 的对比

```markdown
# Design Directory

## GDD Files (`design/gdd/`)
Every GDD must include all **8 required sections** in this order:
1. Overview
2. Player Fantasy
...
```

**这里强调的是 GDD 的 8 章节要求**——只在写设计文档时相关，和写代码无关。

### 分层 CLAUDE.md 的设计原则

1. **主 CLAUDE.md 写全局的**：项目定位、技术栈、总协作协议、引用子文档。
2. **子目录 CLAUDE.md 写局部的**：该目录的专属规则、文件命名、结构要求。
3. **不要重复**：子目录 CLAUDE.md 不重复主 CLAUDE.md 的内容，只补充。
4. **按目录性质分**：源码目录、文档目录、测试目录各有不同规则。

**判断标准**：如果某个规则只在一类文件下相关，就放那个目录的 CLAUDE.md。如果所有文件都相关，放主 CLAUDE.md。

---

## 核心问题 6：怎么避免 CLAUDE.md 撑爆上下文

这是编写 CLAUDE.md 最实际的挑战。这个项目用了几个策略：

### 策略 1：主文件精简，细节 `@` 引用

主 CLAUDE.md 只有 53 行，但通过 6 个 `@` 引用加载了完整配置。

**原则**：主文件是"目录页"，不是"正文"。

### 策略 2：分层 CLAUDE.md 按需加载

`src/CLAUDE.md` 只在写源码时加载，`design/CLAUDE.md` 只在写设计文档时加载。

**原则**：局部规则放局部，不污染全局。

### 策略 3：CLAUDE.md vs CLAUDE.local.md

看 [CLAUDE-local-template.md](file:///workspace/.claude/docs/CLAUDE-local-template.md)：

```markdown
# Personal Preferences
## Model Preferences
- Prefer Opus for complex design tasks
## Local Environment
- Python command: python (or py / python3)
## Personal Shortcuts
- When I say "review", run /code-review on the last changed files
```

**`CLAUDE.local.md` 是个人配置**：
- 不提交到 git（gitignored）
- 个人偏好（模型选择、本地环境、个人快捷方式）
- 叠加在 CLAUDE.md 之上

**分工**：
- `CLAUDE.md`：团队共享的项目配置，提交到 git。
- `CLAUDE.local.md`：个人偏好，不提交。

**原则**：团队共享的写 CLAUDE.md，个人的写 CLAUDE.local.md。不要把个人偏好污染团队配置。

### 策略 4：区分"每次都要见"和"按需见"

**每次都要见**（直接写 CLAUDE.md）：
- 项目定位
- 核心协作协议（"写文件前问"）
- 技术栈
- 入门指引

**按需见**（`@` 引用或子目录 CLAUDE.md）：
- 详细编码标准
- 目录结构
- 上下文管理策略
- 引擎版本参考

### 策略 5：定期审查和精简

CLAUDE.md 不是"写一次就完"。定期问：
- 这条规则是不是每次对话都需要？不是 → 拆出去。
- 这段内容是不是太长了？是 → 拆出去。
- 这条是不是个人偏好？是 → 移到 CLAUDE.local.md。
- 这条是不是只对某类文件相关？是 → 移到子目录 CLAUDE.md。

---

## 实战范例：这个项目 CLAUDE.md 的逐段解析

让我们逐段拆解 [CLAUDE.md](file:///workspace/CLAUDE.md)，看每段为什么这么写。

### 第 1-4 行：标题 + 定位

```markdown
# Claude Code Game Studios -- Game Studio Agent Architecture

Indie game development managed through 49 coordinated Claude Code subagents.
Each agent owns a specific domain, enforcing separation of concerns and quality.
```

**为什么这么写**：
- 标题用 `--` 加副标题，副标题点明"这是关于 agent 架构的"。
- 定位句三要素齐全：领域（indie game dev）+ 方式（49 subagents）+ 特征（domain ownership、separation of concerns、quality）。
- "separation of concerns"和"quality"是后续所有规则的根基，提前锚定。

### 第 6-15 行：技术栈

```markdown
## Technology Stack
- **Engine**: [CHOOSE: Godot 4 / Unity / Unreal Engine 5]
- **Language**: [CHOOSE: GDScript / C# / C++ / Blueprint]
- **Version Control**: Git with trunk-based development
- **Build System**: [SPECIFY after choosing engine]
- **Asset Pipeline**: [SPECIFY after choosing engine]

> **Note**: Engine-specialist agents exist for Godot, Unity, and Unreal with
> dedicated sub-specialists. Use the set matching your engine.
```

**为什么这么写**：
- 用 `[CHOOSE: ...]` 占位符标记待决策项，AI 知道这些还没定。
- 已决策的（Git with trunk-based development）直接写。
- Note 补充说明引擎专家 agent 的存在，引导用户选引擎后用对应 agent。

**技术原理**：技术栈是 AI 做所有技术决策的"约束条件"。不写技术栈，AI 可能建议用 React（但项目用 Vue），用 PostgreSQL（但项目用 MySQL），全跑偏。

### 第 17-19 行：项目结构（@ 引用）

```markdown
## Project Structure
@.claude/docs/directory-structure.md
```

**为什么 @ 引用**：[directory-structure.md](file:///workspace/.claude/docs/directory-structure.md) 是个目录树图，相对独立，拆出去主文件更干净。

### 第 21-23 行：引擎版本参考（@ 引用）

```markdown
## Engine Version Reference
@docs/engine-reference/godot/VERSION.md
```

**为什么重要**：大模型训练数据有截止日期，引擎 API 可能已变。这个引用提醒 AI"用引擎 API 前先查版本参考"。

### 第 25-27 行：技术偏好（@ 引用）

```markdown
## Technical Preferences
@.claude/docs/technical-preferences.md
```

**为什么 @ 引用**：[technical-preferences.md](file:///workspace/.claude/docs/technical-preferences.md) 是个 87 行的详细配置（命名规范、性能预算、测试要求、禁止模式、引擎专家路由表）。太长，拆出去。

### 第 29-31 行：协作规则（@ 引用）

```markdown
## Coordination Rules
@.claude/docs/coordination-rules.md
```

**为什么 @ 引用**：[coordination-rules.md](file:///workspace/.claude/docs/coordination-rules.md) 包含委派规则、模型分层、subagent vs agent team、并行协议。长且独立。

### 第 33-43 行：协作协议（直接写）

```markdown
## Collaboration Protocol

**User-driven collaboration, not autonomous execution.**
Every task follows: **Question -> Options -> Decision -> Draft -> Approval**

- Agents MUST ask "May I write this to [filepath]?" before using Write/Edit tools
- Agents MUST show drafts or summaries before requesting approval
- Multi-file changes require explicit approval for the full changeset
- No commits without user instruction

See `docs/COLLABORATIVE-DESIGN-PRINCIPLE.md` for full protocol and examples.

> **First session?** If the project has no engine configured and no game concept,
> run `/start` to begin the guided onboarding flow.
```

**为什么直接写**：
- 这是**核心铁律**，每次对话每次写文件都要见。
- 短（10 行），不撑上下文。
- "No commits without user instruction"这种红线必须显式。

**为什么还引用 `docs/COLLABORATIVE-DESIGN-PRINCIPLE.md`**：细节（五步协作的完整范例、AskUserQuestion 用法）在那儿，主文件只放铁律。

**为什么有"First session?"提示**：新人入门指引。简短、直接写、用 `>` 引用块视觉强调。

### 第 48-53 行：编码标准 + 上下文管理（@ 引用）

```markdown
## Coding Standards
@.claude/docs/coding-standards.md

## Context Management
@.claude/docs/context-management.md
```

**为什么 @ 引用**：两个都是长文档，独立性强。

---

## 编写 CLAUDE.md 的完整流程

基于以上分析，给你一个可操作的编写流程：

### 第 1 步：写项目定位（5 分钟）

回答三个问题：
1. 这是什么领域的项目？
2. 用什么方式/规模做的？
3. 核心特征是什么？

写成一句话 + 一个标题。

### 第 2 步：写技术栈（5 分钟）

列出：
- 语言 + 版本
- 框架 + 版本
- 数据库
- 关键依赖
- 版本控制策略

已定的直接写，未定的用 `[CHOOSE: ...]` 占位。

### 第 3 步：写核心协作协议（10 分钟）

回答：
- AI 写文件前要不要问？
- AI 能不能自己提交？
- 多文件改动怎么批准？
- 决策模式是什么（自治 vs 协作）？

写成 3-5 条铁律，直接放 CLAUDE.md。

### 第 4 步：识别"该拆出去"的内容（10 分钟）

问自己：
- 编码标准会不会超过 20 行？→ 拆 `@.claude/docs/coding-standards.md`
- 目录结构要不要详细说明？→ 拆 `@.claude/docs/directory-structure.md`
- 有没有专门的协作规则？→ 拆 `@.claude/docs/coordination-rules.md`
- 有没有上下文管理策略？→ 拆 `@.claude/docs/context-management.md`

为每个拆出去的内容创建子文件，在 CLAUDE.md 用 `@` 引用。

### 第 5 步：识别"该分层"的内容（10 分钟）

问自己：
- 源码目录有没有专属规则？→ 写 `src/CLAUDE.md`
- 文档目录有没有专属规则？→ 写 `docs/CLAUDE.md`
- 测试目录有没有专属规则？→ 写 `tests/CLAUDE.md`

每个子目录 CLAUDE.md 只写该目录专属的规则，不重复主 CLAUDE.md。

### 第 6 步：写入门指引（5 分钟）

新人打开项目第一眼该干嘛？写一句提示，用 `>` 引用块强调。

### 第 7 步：审查和精简（持续）

定期问：
- 主 CLAUDE.md 超过 100 行了吗？超了 → 拆。
- 有没有内容每次对话都用不到？有 → 拆。
- 有没有个人偏好混进来了？有 → 移到 CLAUDE.local.md。

---

## CLAUDE.md 编写检查清单

写完 CLAUDE.md 后，用这个清单自检：

### 必备项
- [ ] 有标题和一句话定位（领域 + 方式 + 特征）
- [ ] 有技术栈（语言、框架、版本）
- [ ] 有核心协作协议（写文件前问、不自动提交）
- [ ] 有入门指引（新人第一眼知道干嘛）

### 结构项
- [ ] 主文件不超过 100 行（超过就拆）
- [ ] 长内容用 `@` 引用拆出去
- [ ] 局部规则放子目录 CLAUDE.md
- [ ] 个人偏好放 CLAUDE.local.md

### 内容项
- [ ] 没有具体业务逻辑（放代码注释）
- [ ] 没有临时指令（放对话里）
- [ ] 没有 agent/skill/hook 定义（放 .claude/ 对应目录）
- [ ] 没有重复内容

### 可读性项
- [ ] 用 `##` 分章节，清晰
- [ ] 用 `-` 列表，不堆段落
- [ ] 用 `>` 引用块强调重要提示
- [ ] 用 `**加粗**` 标记关键词

---

## 常见反模式

### 反模式 1：一句话 CLAUDE.md

```markdown
# 我的项目
你是一个 Python 专家，帮我写代码。
```

**问题**：没有领域、没有技术栈、没有协作协议。AI 知道等于不知道。

**修正**：补充定位、技术栈、协作协议。

### 反模式 2：万字 CLAUDE.md

把所有规范、流程、上下文都塞进 CLAUDE.md，几百行。

**问题**：撑爆上下文，AI 抓不住重点，修改困难。

**修正**：主文件精简到 100 行内，细节用 `@` 引用拆出去。

### 反模式 3：角色冲突

```markdown
# 我的项目
你是一个创意总监，负责游戏愿景。
```

但项目用了 subagent，subagent 定义里也写了"你是创意总监"。

**问题**：主会话和子智能体角色冲突。

**修正**：多智能体项目，主 CLAUDE.md 不设定角色，角色放 agent 定义。

### 反模式 4：个人偏好污染团队配置

```markdown
# 项目 CLAUDE.md
## 个人偏好
- 我喜欢用 Opus
- 我的 Python 命令是 py
```

**问题**：个人偏好提交到 git，团队其他人被影响。

**修正**：个人偏好放 CLAUDE.local.md（gitignored）。

### 反模式 5：规则重复

主 CLAUDE.md 写了"写文件前问"，子目录 CLAUDE.md 又写一遍。

**问题**：重复内容浪费上下文，修改时容易漏改。

**修正**：子目录 CLAUDE.md 只补充，不重复主文件内容。

### 反模式 6：临时指令永久化

```markdown
# 项目 CLAUDE.md
## 当前任务
今天帮我修登录页的 bug。
```

**问题**：临时任务写进 CLAUDE.md，每次对话都加载，任务完成后还在。

**修正**：临时任务放对话里，CLAUDE.md 只放长期有效的配置。

---

## 一个最小可用模板

如果你只想快速起步，用这个最小模板：

```markdown
# 项目名 -- 一句话副标题

领域 + 规模/方式 + 核心特征的一句话定位。

## Technology Stack
- **Language**: Python 3.12
- **Framework**: FastAPI
- **Database**: PostgreSQL

## Collaboration Protocol
- 写文件前必须问"可以写到 [路径] 吗？"
- 不经批准不提交
- 决策模式：Question → Options → Decision → Draft → Approval

## Coding Standards
@.claude/docs/coding-standards.md

## Project Structure
@.claude/docs/directory-structure.md

> **第一次会话？** 跑 `/start` 开始。
```

存成 `CLAUDE.md`，按需扩展。

---

## 进阶：这个项目还示范了什么

如果你已经掌握基础，这个项目还有几个进阶技巧值得学：

### 1. 用 CLAUDE.md 引导 agent 路由

[technical-preferences.md](file:///workspace/.claude/docs/technical-preferences.md) 里的"Engine Specialists"和"File Extension Routing"表，告诉 skill 该为每种文件派哪个 agent。这是"用 CLAUDE.md 驱动编排"的进阶用法。

### 2. 用 CLAUDE.md 锚定"模型知识边界"

[src/CLAUDE.md](file:///workspace/src/CLAUDE.md) 里写：

```markdown
## Engine Version Warning
The LLM's training data predates the pinned engine version.
**Always check `docs/engine-reference/` before using any engine API.**
```

**这是很聪明的设计**：承认 AI 知识有截止日期，强制它查最新文档。防止 AI 用过时 API。

### 3. 用 CLAUDE.md 强制"验证驱动开发"

[coding-standards.md](file:///workspace/.claude/docs/coding-standards.md) 里写：

```markdown
- **Verification-driven development**: Write tests first when adding gameplay systems.
  For UI changes, verify with screenshots. Compare expected output to actual output
  before marking work complete.
```

**这是把方法论写进 CLAUDE.md**：不只规定"做什么"，还规定"怎么验证做了"。

### 4. 用 CLAUDE.md 区分"BLOCKING"和"ADVISORY"

[coding-standards.md](file:///workspace/.claude/docs/coding-standards.md) 的测试证据表：

```markdown
| Story Type | Required Evidence | Gate Level |
|---|---|---|
| Logic | Automated unit test | BLOCKING |
| Visual/Feel | Screenshot + lead sign-off | ADVISORY |
```

**这是给 AI 明确"严格度等级"**：BLOCKING 必须满足，ADVISORY 建议满足。AI 知道哪些能通融，哪些不能。

---

## 总结：CLAUDE.md 编写的五条铁律

1. **先定位，后细节**——开头一句话锚定领域和特征，后续所有内容在这个上下文里理解。
2. **主文件精简，细节 `@` 引用**——主文件是目录页，不是正文。超过 100 行就该拆。
3. **核心铁律直接写，长文档拆出去**——每次都要见的直接写，按需见的 `@` 引用。
4. **局部规则放子目录 CLAUDE.md**——不污染全局上下文，按需加载。
5. **团队配置和个人偏好分开**——CLAUDE.md 提交 git，CLAUDE.local.md 不提交。

**一句话**：CLAUDE.md 是"项目宪法"——简短、稳定、锚定方向；细节法律拆到子文件，地方法规放地方。

---

## 延伸阅读

- [这个项目的 CLAUDE.md](file:///workspace/CLAUDE.md)——本文的主要分析对象
- [CLAUDE-local-template.md](file:///workspace/.claude/docs/CLAUDE-local-template.md)——个人配置模板
- [src/CLAUDE.md](file:///workspace/src/CLAUDE.md)——源码目录分层 CLAUDE.md
- [design/CLAUDE.md](file:///workspace/design/CLAUDE.md)——设计目录分层 CLAUDE.md
- [docs/CLAUDE.md](file:///workspace/docs/CLAUDE.md)——文档目录分层 CLAUDE.md
- [technical-preferences.md](file:///workspace/.claude/docs/technical-preferences.md)——技术偏好子文件范例
- [coding-standards.md](file:///workspace/.claude/docs/coding-standards.md)——编码标准子文件范例
- [study-docs/02-Claude-Code基础概念.md](file:///workspace/study-docs/02-Claude-Code基础概念.md)——本系列的基础概念篇
- [study-docs/07-协作协议与人在环.md](file:///workspace/study-docs/07-协作协议与人在环.md)——协作协议详解
