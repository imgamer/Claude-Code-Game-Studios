# templates/guidance/ 目录详解 —— AI 写文档时的"每一节说明书"

> **一句话总览**：`guidance/` 是 `.claude/docs/templates/` 根目录下**配套模板文件的"作者指南"**——模板是"空骨架"（带 `[To be designed]` 占位符），guidance 是"每一节该写什么、用什么表格、为什么要写、好例子和坏例子"。它的读者是**AI 技能**（`/design-system`、`/ux-design` 等），不是终端用户。它的核心设计约束是**按节懒加载**——写哪一节只读哪一节，绝不整文件读。

依据 [game-design-document-guide.md](file:///workspace/.claude/docs/templates/guidance/game-design-document-guide.md)、[ux-spec-guide.md](file:///workspace/.claude/docs/templates/guidance/ux-spec-guide.md)、[hud-design-guide.md](file:///workspace/.claude/docs/templates/guidance/hud-design-guide.md)、[accessibility-requirements-guide.md](file:///workspace/.claude/docs/templates/guidance/accessibility-requirements-guide.md)、[interaction-pattern-library-guide.md](file:///workspace/.claude/docs/templates/guidance/interaction-pattern-library-guide.md)、[design-system/references/4-section-design.md](file:///workspace/.claude/skills/design-system/references/4-section-design.md)、[ux-design/SKILL.md](file:///workspace/.claude/skills/ux-design/SKILL.md) 整理。

---

## 0. 先回答你最可能有的三个疑问

**疑问一：这是给 AI 看的还是给用户看的？**
**给 AI 看的**。guidance 文件里写的都是"当 AI 写这一节时，应该写什么、用什么表格、避免什么错误"——`Authoring guidance`、`Load only the part covering the section currently being authored`、`Do NOT write [Formula TBD]` 这些指令全是给模型的行为约束。模板文件（根目录）也**不直接给用户看**——它被 AI 技能消费后变成 GDD、UX Spec 等**用户交付物**。用户永远看不到 `[To be designed]` 占位符和 guidance 文件里的指令。

**疑问二：既然模板已经有章节标题了，为什么还需要 guidance？**
模板只说"有 Summary 这一节"，不说"Summary 要写 2-3 句话、写系统是什么/给玩家做什么/为什么存在、为分层上下文加载服务"（这是 guidance 里的原话）。模板只说"有 Formulas 这一节"，不说"必须给每个变量建类型表、标 Source（data file vs constant）、写 Expected output range、处理 Edge case"。**模板定义结构，guidance 定义内容**。

**疑问三：为什么不把 guidance 合并到模板里？**
三个原因——① 模板会被 AI 写入项目仓库（`design/gdd/combat.md`），如果模板里混着"AI 指令"，项目文件里就会出现 `> Do NOT write [Formula TBD]` 这种给模型看的文字，污染交付物；② guidance 文件体积大（game-design-document-guide.md 264 行），模板体积小（game-design-document.md 77 行）——整文件加载浪费 token，按节加载才高效；③ guidance 是**多技能共享**的——`/design-system` 和 `/review-all-gdds` 都读同一个 guidance，如果混进模板，每个技能都要自己复制一份 guidance。

---

## 1. 初中生版：写作模板 + 写作指南

想象老师让你写一篇 8 段式作文：

- **模板**（根目录的 `.md` 文件）= 作文纸，已经打印好 8 个标题："开头"、"背景"、"核心论点"……每个标题下面写着 `[请填写]`；
- **guidance**（`guidance/` 目录的 `-guide.md` 文件）= 老师给你的**写作指导手册**，告诉你每一段应该写几句、用什么例子、避免什么错误、好作文怎么写、坏作文怎么写。

关键规则：**你一次只看"当前正在写的那一段"的指导**——写"开头"时只看"开头指导"，不看"核心论点指导"，这样不会被其他段的要求干扰。

AI 技能就是那个"学生"，guidance 就是那个"老师给的分段指导手册"。

---

## 2. 完整对照：8 组模板 + guidance

templates 根目录里有 **5 个模板文件**，每个配一个 guidance 文件（interaction-pattern-library 特殊——guidance 又拆成 3 个子文件，共 8 个 guidance 文件）：

| 模板文件（骨架） | guidance 文件（每节说明） | 谁消费 guidance |
|-----------------|--------------------------|----------------|
| [game-design-document.md](file:///workspace/.claude/docs/templates/game-design-document.md) | [game-design-document-guide.md](file:///workspace/.claude/docs/templates/guidance/game-design-document-guide.md) | `/design-system` §4 逐节设计 + `/review-all-gdds` §2c 交叉引用检查 |
| [ux-spec.md](file:///workspace/.claude/docs/templates/ux-spec.md) | [ux-spec-guide.md](file:///workspace/.claude/docs/templates/guidance/ux-spec-guide.md) | `/ux-design` Section 1-8 逐节写 |
| [hud-design.md](file:///workspace/.claude/docs/templates/hud-design.md) | [hud-design-guide.md](file:///workspace/.claude/docs/templates/guidance/hud-design-guide.md) | `/ux-design` HUD Design 分支 |
| [accessibility-requirements.md](file:///workspace/.claude/docs/templates/accessibility-requirements.md) | [accessibility-requirements-guide.md](file:///workspace/.claude/docs/templates/guidance/accessibility-requirements-guide.md) | `/ux-design` Accessibility Requirements 分支 + `/ux-review` |
| [interaction-pattern-library.md](file:///workspace/.claude/docs/templates/interaction-pattern-library.md) | [interaction-pattern-library-guide.md](file:///workspace/.claude/docs/templates/guidance/interaction-pattern-library-guide.md)（索引） + 3 个子文件（standard-controls / game-specific / navigation-feedback） | `/ux-design` Interaction Pattern Library 分支 |

**注意**：不是所有模板都有 guidance——templates 根目录有 64 个模板文件，但只有 5 个配了 guidance。那些**没有 guidance 的模板**（如 `game-concept.md`、`art-bible.md`、`architecture-decision-record.md`）的作者指令直接写在**技能的 references 目录**里（比如 `/brainstorm/references/full-concept.md` 就是 game-concept.md 的 guidance），或者由门禁定义隐含约束。**"有 guidance"意味着"这个模板被多个技能消费，guidance 需要跨技能共享"**。

---

## 3. 格式解剖：一个 guidance 文件长什么样

### 3.1 文件头：三行固定声明

每个 guidance 文件的前 3 行都是固定格式：

```markdown
> Authoring guidance for [模板文件名]. Load only the part covering the section currently being authored — not the whole file at once.

# [模板名] Template — Authoring Guidance
```

第 1 行是**机器指令**（给 AI 看的，不是给用户看的），第 3 行是标题。注意第 1 行是 blockquote（`>` 开头）——这让它在 markdown 渲染时是灰色小字，不影响人类阅读，但 AI 能识别它是指令。

### 3.2 节结构：每节 = 一个 `## Section N` 段落

以 [ux-spec-guide.md](file:///workspace/.claude/docs/templates/guidance/ux-spec-guide.md) 为例：

```markdown
## Section 1 — Purpose & Player Need

> **Why this section exists**: Every screen must justify its existence from the
> player's perspective. Screens designed from a developer perspective produce
> cluttered interfaces.

**Authoring the player need**:
[一段散文指导 + 坏例子 + 好例子]

**Authoring the player goal**:
[同上]

**Authoring the game goal**:
[同上]

---
```

每节的内容遵循**四件套**：

| 组件 | 作用 | 例子 |
|------|------|------|
| `> **Why this section exists**` | 解释这一节存在的理由（设计意图） | "每个屏幕必须从玩家视角证明自己的存在" |
| `**Authoring the X**: [指导散文]` | 告诉 AI 应该写什么内容 | "让玩家在打开这个屏幕时觉得……" |
| 坏例子 | 告诉 AI 什么是错误的写法 | 坏："显示玩家的物品"；好："让玩家理解自己带了什么……" |
| 好例子 | 告诉 AI 什么是正确的写法 | 好："让玩家觉得他们的进度是安全的……" |

### 3.3 表格规范：精确到列名

guidance 文件里会精确规定表格的**列名**（因为模板也是用这些列名建的，下游消费者也按列名匹配）。例如 game-design-document-guide.md 对 Formulas 节的规定：

```markdown
| Variable | Type | Range | Source | Description |
|----------|------|-------|--------|-------------|
```

**注意 `Source` 列**——它不是普通的"变量来源"，它把 coding-standards 的规则**嵌入进了设计文档的格式**：值为 `data file` 的变量是调优旋钮（应该存外部配置），值为 `constant` 的变量是刻意的硬编码例外。design-system 的 4-section-design.md 第 262-266 行专门引用了这个列名规范：

> "These columns match `templates/guidance/game-design-document-guide.md` exactly; do not rename them. The `Source` column carries the coding-standards rule *'gameplay values must be data-driven'* into the design document."

**这就是 guidance 的跨层传播作用**：coding-standards 是工程规范，guidance 把它翻译成设计文档的列名规范，design-system 照着这个规范写，code-review 按列名检查。

### 3.4 interaction-pattern-library 的特殊拆分

interaction-pattern-library-guide.md **本身只是一个索引**（[第 1-14 行](file:///workspace/.claude/docs/templates/guidance/interaction-pattern-library-guide.md)），它把 guidance 拆到了 3 个子文件：

```
interaction-pattern-library-guide.md          ← 索引表
interaction-pattern-library-guide-standard-controls.md    ← 14 种标准控件
interaction-pattern-library-guide-game-specific.md        ← 6 种游戏特有模式
interaction-pattern-library-guide-navigation-feedback.md  ← 导航 + 反馈模式
```

**为什么要拆**：interaction-pattern-library 覆盖了 25+ 种 UI 模式，每种模式的 guidance 里有完整的状态表、时序、音频值、无障碍要求——整文件可能 3000+ 行，一次加载绝对不可能。拆分后每个子文件 300-500 行，单次加载可控。**这是 guidance 按节懒加载的极端形式**——一节拆一个文件。

---

## 4. 实际怎么用（全链路：从写 GDD 到跨文档审查）

### 4.1 `/design-system` §4 逐节设计怎么消费 guidance

[4-section-design.md](file:///workspace/.claude/skills/design-system/references/4-section-design.md) 是 `/design-system` §4 的执行手册，它的核心指令是：

> "Before drafting each section, load the corresponding section from `.claude/docs/templates/guidance/game-design-document-guide.md`. Load only that section — use offset/limit or Grep to find `## [Section Name]` and read from there to the next `##` divider."

**具体流程**（collaborative 模式下的一个 section）：

```
AI：Grep "## Player Fantasy" game-design-document-guide.md
    → 找到行号 L，Read guidance 文件 L 到下一个 ##
    → 读到："What should the player FEEL... emotional or power fantasy..."
AI：AskUserQuestion —— "当玩家使用这个机制时，他应该有什么感觉？"
AI：用户选了一个方向
AI：起草 Player Fantasy 节（按 guidance 说的"写情感目标，不是功能描述"）
AI：展示草稿 → 用户批准
AI：Edit combat.md，把 Player Fantasy 节从 [To be designed] 替换成草稿
```

**关键约束**：AI **绝不能**在用户批准之前写任何一节（design-system 的协作协议），也**绝不能**整文件加载 guidance（token 经济学）。guidance 文件本身**永远不会被写入项目仓库**——它只在 AI 的处理过程中存在于 Claude Code 的上下文里。

### 4.2 `/ux-design` 怎么消费 guidance

[ux-design/SKILL.md 第 25-35 行](file:///workspace/.claude/skills/ux-design/SKILL.md#L25-L35) 有一个**路由表**，把要写的文档类型路由到对应的 guidance：

| 要写的文档 | guidance 文件 |
|-----------|--------------|
| UX Spec（每个屏幕） | `ux-spec-guide.md` |
| HUD Design | `hud-design-guide.md` |
| Interaction Pattern Library | `interaction-pattern-library-guide.md`（索引 → 选子文件） |
| Accessibility Requirements | `accessibility-requirements-guide.md` |

**跨模板共享**：accessibility-requirements-guide.md 特别值得注意——它**不放在 `ux-design/references/`**，而是放在 `templates/guidance/`（ux-design SKILL.md 第 103-107 行解释了为什么）：

> "The accessibility guidance lives under `templates/guidance/` rather than this skill's `references/` because the template it documents (`accessibility-requirements.md`) is consumed by `/ux-review` and the gate files too."

**设计思路**：谁写谁负责？不是。**谁共享谁放在 templates/guidance/**。如果 guidance 只被一个技能用，放那个技能的 references 里；如果被多个技能/门禁用，放 templates/guidance/——这样每个消费者都能从同一个权威位置读，不会各有各的副本漂移。

### 4.3 `/review-all-gdds` 怎么用 guidance 验证

[2-consistency.md 第 59-60 行](file:///workspace/.claude/skills/review-all-gdds/references/2-consistency.md#L59-L60)：

> "Start from the `## Cross-References` table where a GDD has one. That table is the document's own declaration of what it depends on — `templates/guidance/game-design-document-guide.md` tells authors it is machine-checked here, and `/design-system` requires it whenever Dependencies names another GDD."

**设计思路**：guidance 是**契约**——它告诉写文档的 AI"Cross-References 表是必须的"，同时告诉审文档的 AI"Cross-References 表是要被机器检查的"。两个技能从同一个 guidance 文件读到同一条规则，保证了"写方知道自己写的东西会被审、审方知道写方应该写什么"。

### 4.4 模板文件自己也引用 guidance（自检式设计）

每个模板文件的第 3 行都有一句显式提示：

```markdown
> Authoring guidance: .claude/docs/templates/guidance/game-design-document-guide.md (load per-section as you author — do not read entirely).
```

**为什么模板文件要自己引用 guidance**：因为 AI 可能**不经过 design-system** 直接打开模板（比如用户手动要求"帮我写 combat GDD"）。模板文件里的引用相当于一个**内嵌的路由指引**——不管谁来、从哪条路来，只要打开了模板，就能知道"有 guidance 可以参考"。这是一种"**自我文档化的骨架**"——空骨架本身就能告诉你"怎么填"。

---

## 5. 设计思路：为什么拆成模板 + guidance（6 条原理）

| 原理 | 应用 | 深层动机 |
|------|------|---------|
| **关注点分离** | 模板只管结构，guidance 只管内容 | 同一个骨架可以配合不同深度的内容（terse/balanced/thorough 档用同一个模板，guidance 说深度变浅变深） |
| **Token 经济学** | guidance 按节懒加载，不整文件读 | 写 8 节 GDD 时，每节只加载对应的 guidance 段落（约 20-40 行），8 次共 200-300 行，而不是 264 行全量加载 |
| **多技能共享** | guidance 放 templates/guidance/ 而非某个技能的 references/ | `/design-system`、`/review-all-gdds`、`/ux-design`、`/ux-review` 都能读同一个版本，不会漂移 |
| **零污染交付** | guidance 只在 AI 上下文里存在，永远不写入项目仓库 | 项目文件里没有 `> Do NOT write [Formula TBD]` 这种 AI 指令文字 |
| **跨层传播** | coding-standards 规则（如"gameplay values must be data-driven"）→ guidance 里的列名规范 → 模板里的表格 → 消费者按列名检查 | 一条工程规则通过格式传播到设计、编码、评审三个阶段 |
| **自我路由** | 模板文件第 3 行显式引用 guidance | 不管 AI 从哪条路来，打开模板就能知道有 guidance 可参考 |

---

## 6. 哪些模板有 guidance、哪些没有

这是理解 guidance 目录**边界**的关键——不是所有模板都需要 guidance：

### 有 guidance 的模板（5 个）——**多技能消费**

| 模板 | guidance 存在理由 |
|------|------------------|
| game-design-document.md | `/design-system` 写 + `/review-all-gdds` 审 + `/design-review` 审 |
| ux-spec.md | `/ux-design` 写 + `/ux-review` 审 |
| hud-design.md | `/ux-design` 写 + `/ux-review` 审 |
| accessibility-requirements.md | `/ux-design` 写 + `/ux-review` 审 + 门禁文件检查 |
| interaction-pattern-library.md | `/ux-design` 写 + `/ux-review` 审 |

**规律**：5 个都是**用户交付物**（GDD、UX 规格、HUD 设计、无障碍要求、交互模式库），被**写+审两类技能**消费——需要"写方知道怎么写、审方知道怎么审"的共享契约。

### 没有 guidance 的模板（约 59 个）——**单技能消费或隐式约束**

| 类型 | 例子 | guidance 去哪了 |
|------|------|----------------|
| 单技能 references | `game-concept.md`、`art-bible.md` | 放在消费它的技能的 references 目录（如 `/brainstorm/references/full-concept.md`） |
| ADR 类 | `architecture-decision-record.md` | guidance 直接写在模板注释里，或由门禁定义约束 |
| 计划/状态类 | `sprint-plan.md`、`session-state.md` | 结构本身就是约束，内容是自由格式 |
| 报告类 | `post-mortem.md`、`prototype-report.md` | 结构只做建议，内容由实际事件决定 |

**设计规律**：guidance 存在 = "这个模板的内容**有确定的写法**、**会被多个角色消费**、**写法错误会导致下游问题**"。反之——如果内容是自由格式、只被一个技能用、或结构本身就是约束——就不需要 guidance。

---

## 7. 术语表

| 术语 | 字面含义 | 在这个项目中的确切意思 |
|------|----------|----------------------|
| **Template（模板）** | 模板 | `.claude/docs/templates/` 根目录的 `.md` 文件——带章节标题和 `[To be designed]` 占位符的空骨架 |
| **Guidance（指南）** | 指导、指南 | `.claude/docs/templates/guidance/` 的 `-guide.md` 文件——告诉 AI 每一节该写什么、用什么表格、好/坏例子 |
| **Authoring Guidance** | 作者指南 | guidance 文件的正式名称——给"写文档的作者"（这里的作者是 AI）的指令 |
| **Load per-section** | 按节加载 | guidance 的核心消费方式——写哪一节只读哪一节的 guidance，用 Grep 定位 `## Section N` 然后 Read offset+limit |
| **Why this section exists** | 这一节存在的理由 | guidance 每节开头的设计意图解释——告诉 AI "为什么要写这一节"，不是只说"写这一节" |
| **Column name contract** | 列名契约 | guidance 里精确规定的表格列名——下游消费者按列名匹配，改列名等于改 schema |
| **Cross-layer propagation** | 跨层传播 | coding-standards 的工程规则 → guidance 的格式规范 → 模板的表格 → 消费者的检查规则 |
| **Zero-contamination delivery** | 零污染交付 | guidance 只在 AI 上下文里存在，永远不写入项目仓库——项目文件里没有 AI 指令文字 |
| **Self-routing template** | 自我路由模板 | 模板文件第 3 行显式引用 guidance——不管谁来填都能找到指导 |
| **Grep-then-Read** | 先 grep 后 read | guidance 的标准消费技术——先 Grep 定位章节行号，再 Read offset+limit 读该节 |

---

## 8. 常见误区

1. **"guidance 是给用户看的写作指南"**。完全错。它是给 AI 看的**行为约束指令**。人类用户不需要知道"Formulas 节的 Source 列要填 data file 还是 constant"——这是 AI 保证的质量标准。
2. **"guidance 就是模板的注释，合一起也行"**。不行。合一起模板会膨胀到 300+ 行，污染项目仓库，且整文件加载浪费 token。拆成两份是 token 经济学和交付物清洁的双重要求。
3. **"所有模板都应该配 guidance"**。反了。只有"多技能消费 + 有确定写法 + 写法错误影响下游"的模板才需要 guidance。59 个没有 guidance 的模板不缺 guidance——它们的 guidance 要么在技能 references 里，要么是隐式约束，要么内容是自由格式。
4. **"guidance 可以整文件读一次，后面慢慢用"**。不行。token 预算有限——一次加载 264 行 guidance 吃掉 5000+ token，而 8 节逐加载每次只吃 300 token。技能指令里**明令禁止**整文件加载。
5. **"为什么不把 coding-standards 的规则直接写进模板的每一节"**。因为模板会被写入项目仓库，guidance 不会。把规则写进模板 = 把 AI 指令写进项目交付物 = 零污染交付原则被破坏。
6. **"guidance 文件应该用 YAML/JSON 格式让机器更容易读"**。反了。guidance 里有大量散文（坏例子、好例子、设计意图解释）——机器不需要读这些散文，**模型需要**。markdown 散文是模型最容易理解的格式。表格部分机器可以解析，但散文部分只有模型能理解。
7. **"accessibility-requirements-guide 放在 templates/guidance/ 是因为它是最重要的"**。不是。是因为它被 `/ux-design` 和 `/ux-review` 两个技能 + 门禁文件三方共享——放 templates/guidance/ 是"共享位置"，不是"重要性标志"。

---

## 9. 小测验（答案都在上文）

**Q1**：为什么 interaction-pattern-library-guide.md 本身只是一个索引，真正的 guidance 拆到了 3 个子文件里？

> 答案：interaction-pattern-library 覆盖 25+ 种 UI 模式，每种的 guidance 有完整状态表、时序、音频值、无障碍要求——整文件可能 3000+ 行，一次加载绝对不可能。拆分后每个子文件 300-500 行，单次加载可控。这是 guidance 按节懒加载的极端形式——一节拆一个文件。

**Q2**：guidance 里"Formulas 节的 Source 列要填 data file 还是 constant"这条规则，是从哪里来的？它为什么重要？

> 答案：它来自 coding-standards.md 里的一条工程规则——"gameplay values must be data-driven (external config), never hardcoded"。guidance 把这条规则翻译成设计文档的列名规范（Source 列），这样设计时就被迫区分"哪些值是可调的"和"哪些值是刻意的硬编码"。下游 code-review 按列名检查——data file 列的变量不应该在代码里直接写死。这是跨层传播的典型例子。

**Q3**：如果 AI 不经过 `/design-system` 技能，直接打开模板文件手动帮你写 GDD，它还能找到 guidance 吗？

> 答案：能。每个模板文件的第 3 行都有一句显式引用 `> Authoring guidance: .claude/docs/templates/guidance/game-design-document-guide.md`——这是"自我路由"设计。不管 AI 从哪条路来，只要打开模板，就能知道有 guidance 可参考。

---

## 10. 动手任务

1. **读一对模板+guidance**：任选一组（推荐 Formulas 节），对照 guidance 的"Variable/Type/Range/Source/Description"五列规定，看模板里的占位符怎么对得上、`design-system` 的 4-section-design.md 怎么引用这个列名规范。
2. **模拟一次懒加载**：执行 `grep -n "## Formulas" .claude/docs/templates/guidance/game-design-document-guide.md` 找到行号，然后 `sed -n '75,93p'`（从 Formulas 节到 Edge Cases 节之前）——这就是 AI 写 Formulas 时实际加载的内容。
3. **找 guidance 里的跨层传播例子**：在 8 个 guidance 文件里搜 "coding-standards"、"design-test"、"pillar"、"acceptance" 这些关键词，看哪些 guidance 条款是从别的层级传播过来的规则。
4. **判断一个模板该不该有 guidance**：挑 templates 根目录里任意一个没有 guidance 的模板（比如 `game-concept.md`），判断它"多技能消费？有确定写法？写法错误影响下游？"——如果三条都不满足，就是正确的"不需要 guidance"。

---

## 相关文档

- [系统设计阶段详解](file:///workspace/study-docs/workflow/系统设计阶段详解.md) —— `/design-system` §4 逐节设计的完整流程，guidance 在其中的消费位置
- [概念阶段详解](file:///workspace/study-docs/workflow/概念阶段详解.md) —— `/brainstorm` 的 concept guidance 放在 references 目录而非 templates/guidance/ 的原因（单技能消费）
- [注册表机制详解](file:///workspace/study-docs/workflow/注册表机制详解.md) —— 跨层传播的另一种形式（事实值的单一事实源 vs guidance 的格式契约）
- [回填与哈希重跑机制](file:///workspace/study-docs/workflow/回填与哈希重跑机制.md) —— `/review-all-gdds` 的 Cross-References 检查为什么要引用 guidance
- [23-上下文体系与逻辑流程总览](file:///workspace/study-docs/23-上下文体系与逻辑流程总览.md) —— 9 环文档链里 guidance 在"技能消费模板"环节的位置
