---
name: setup-engine
description: "Configure the project's game engine and version. Pins the engine in CLAUDE.md, detects knowledge gaps, and populates engine reference docs via WebSearch when the version is beyond the LLM's training data."
argument-hint: "[engine] | [engine version] | refresh | upgrade [old-version] [new-version] | no args for guided selection"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, WebSearch, WebFetch, Task, AskUserQuestion
model: sonnet
---
---
name: setup-engine
description: "配置项目的游戏引擎和版本。在 CLAUDE.md 中锁定引擎，检测知识缺口，并在版本超出 LLM 训练数据时通过 WebSearch 填充引擎参考文档。"
argument-hint: "[engine] | [engine version] | refresh | upgrade [old-version] [new-version] | no args for guided selection"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, WebSearch, WebFetch, Task, AskUserQuestion
model: sonnet
---

When this skill is invoked:

## 1. Parse Arguments

Four modes:

- **Full spec**: `/setup-engine godot 4.6` — engine and version provided
- **Engine only**: `/setup-engine unity` — engine provided, version will be looked up
- **No args**: `/setup-engine` — fully guided mode (engine recommendation + version)
- **Refresh**: `/setup-engine refresh` — update reference docs (see Section 10)
- **Upgrade**: `/setup-engine upgrade [old-version] [new-version]` — migrate to a new engine version (see Section 11)

当调用此技能时：

## 1. 解析参数

四种模式：

- **完整规格**：`/setup-engine godot 4.6` —— 提供引擎和版本
- **仅引擎**：`/setup-engine unity` —— 提供引擎，版本将被查找
- **无参数**：`/setup-engine` —— 完全引导模式（引擎推荐 + 版本）
- **刷新**：`/setup-engine refresh` —— 更新参考文档（见第 10 节）
- **升级**：`/setup-engine upgrade [old-version] [new-version]` —— 迁移到新引擎版本（见第 11 节）

---

## 2. Guided Mode (No Arguments)

If no engine is specified, run an interactive engine selection process:

### Check for existing game concept
- Read `design/gdd/game-concept.md` if it exists — extract genre, scope, platform
  targets, art style, team size, and any engine recommendation from `/brainstorm`
- If no concept exists, inform the user:
  > "No game concept found. Consider running `/brainstorm` first to discover what
  > you want to build — it will also recommend an engine. Or tell me about your
  > game and I can help you pick."

### If the user wants to pick without a concept, ask in this order:

**Question 1 — Prior experience** (ask this first, always, via `AskUserQuestion`):
- Prompt: "Have you worked in any of these engines before?"
- Options: `Godot` / `Unity` / `Unreal Engine 5` / `Multiple — I'll explain` / `None of them`
- If they pick a specific engine → recommend that engine. Prior experience outweighs all other factors. Confirm with them and skip the matrix.
- If "None" or "Multiple" → continue to the questions below.

**Questions 2-6 — Decision matrix inputs** (only if no prior engine experience):

**Question 2 — Target platform** (ask this second, always, via `AskUserQuestion` — platform eliminates or heavily weights engines before any other factor):
- Prompt: "What platforms are you targeting for this game?"
- Options: `PC (Steam / Epic)` / `Mobile (iOS / Android)` / `Console` / `Web / Browser` / `Multiple platforms`
- Platform rules that feed directly into the recommendation:
  - Mobile → Unity strongly preferred; Unreal is a poor fit; Godot is viable for simple mobile
  - Console → Unity or Unreal; Godot console support requires third-party publishers or significant extra work
  - Web → Godot exports cleanly to web; Unity WebGL is functional; Unreal has poor web support
  - PC only → all engines viable; other factors decide
  - Multiple → Unity is the most portable across PC/mobile/console

1. **What kind of game?** (2D, 3D, or both?)
2. **Primary input method?** (keyboard/mouse, gamepad, touch, or mixed?)
3. **Team size and experience?** (solo beginner, solo experienced, small team?)
4. **Any strong language preferences?** (GDScript, C#, C++, visual scripting?)
5. **Budget for engine licensing?** (free only, or commercial licenses OK?)

### Produce a recommendation

Do NOT use a simple scoring matrix that eliminates engines. Instead, reason through the user's profile against the honest tradeoffs below, then present 1-2 recommendations with full context. Always end with the user choosing — never force a verdict.

**Engine honest tradeoffs:**

**Godot 4**
- Genuine strengths: 2D (best in class), stylized/indie 3D, rapid iteration, free forever (MIT), open source, gentlest learning curve, best for solo devs who want full control
- Real limitations: 3D ecosystem is thin compared to Unity/Unreal (fewer tutorials, assets, community answers for 3D-specific problems); large open-world 3D is very hard and largely untested in Godot; console export requires third-party publishers or significant extra work; smaller professional job market
- Licensing reality: Truly free with no revenue thresholds ever. MIT license means you own everything.
- Best fit: 2D games of any scope; stylized/atmospheric 3D; contained 3D worlds (not open-world); first game projects where learning curve matters; projects where budget is a hard constraint at any scale

**Unity**
- Genuine strengths: Industry standard for mid-scope 3D and mobile; massive asset store and tutorial ecosystem; C# is a professional language; best console certification support for indie; strong community for almost every genre
- Real limitations: Licensing controversy in 2023 damaged trust (runtime fee was proposed then walked back — the risk of policy changes remains real); C# has a steeper initial curve than GDScript; heavier editor than Godot for simple projects
- Licensing reality: Free under $200K revenue AND 200K installs (Unity Personal/Plus). Only becomes costly if the game is genuinely successful — most indie games never hit this threshold. The 2023 controversy is worth knowing about but the actual current terms are reasonable for most indie developers.
- Best fit: Mobile games; mid-scope 3D; games targeting console; developers with C# background; projects needing large asset store; teams of 2-5

**Unreal Engine 5**
- Genuine strengths: Best-in-class 3D visuals (Lumen, Nanite, Chaos physics); industry standard for AAA and photorealistic 3D; large open-world support is mature and production-tested; Blueprint visual scripting lowers C++ barrier; strong for games targeting high-end PC or console
- Real limitations: Steepest learning curve; heaviest editor (slow compile times, large project sizes); overkill for stylized/2D/small-scope games; C++ is genuinely hard; not suitable for mobile or web; 5% royalty past $1M gross revenue
- Licensing reality: 5% royalty only applies AFTER $1M gross revenue per title. For a first game or any game that doesn't reach $1M, it costs nothing. This threshold is high enough that most indie developers will never pay it.
- Best fit: AAA-quality 3D; large open-world games; photorealistic visuals; developers with C++ experience or willing to use Blueprint; games targeting high-end PC/console where visual fidelity is a core selling point

**Genre-specific guidance** (factor this into the recommendation):
- 2D any style → Godot strongly preferred
- 3D stylized / atmospheric / contained world → Godot viable, Unity solid alternative
- 3D open world (large, seamless) → Unity or Unreal; Godot is not production-proven for this
- 3D photorealistic / AAA-quality → Unreal
- Mobile-first → Unity strongly preferred
- Console-first → Unity or Unreal; Godot console support requires extra work
- Horror / narrative / walking sim → any engine; match to art style and team experience
- Action RPG / Soulslike → Unity or Unreal for 3D; community support and assets matter here
- Platformer 2D → Godot
- Strategy / top-down / RTS → Godot or Unity depending on 2D vs 3D

**Recommendation format:**
1. Show a comparison table with the user's specific factors as rows
2. Give a primary recommendation with honest reasoning
3. Name the best alternative and when to choose it instead
4. Explicitly state: "This is a starting point, not a verdict — you can always migrate engines, and many developers switch between projects."
5. Use `AskUserQuestion` to confirm: "Does this recommendation feel right, or would you like to explore a different engine?"
   - Options: `[Primary engine] (Recommended)` / `[Alternative engine]` / `[Third engine]` / `Explore further` / `Type something`

**If the user picks "Explore further":**
Use `AskUserQuestion` with concept-specific deep-dive topics. Always generate these options from the user's actual concept — do not use generic options. Always include at minimum:
- The primary engine's specific limitations for this concept (e.g., "How far can Godot 3D actually go for [genre]?")
- The alternative engine's specific tradeoffs for this concept
- Language choice impact on this concept's technical challenges
- Any concept-specific technical concern (e.g., adaptive audio, open-world streaming, multiplayer netcode)

The user can select multiple topics. Answer each selected topic in depth before returning to the engine confirmation question.

## 2. 引导模式（无参数）

如未指定引擎，运行交互式引擎选择流程：

### 检查现有游戏概念
- 如 `design/gdd/game-concept.md` 存在则读取它 —— 提取类型、范围、平台
  目标、美术风格、团队规模和来自 `/brainstorm` 的任何引擎推荐
- 如无概念，告知用户：
  > "未找到游戏概念。考虑先运行 `/brainstorm` 以发现
  > 你想构建什么 —— 它也会推荐引擎。或者告诉我你的
  > 游戏，我可以帮你选择。"

### 如用户要在无概念情况下选择，按以下顺序询问：

**问题 1 —— 先前经验**（始终先问此问题，通过 `AskUserQuestion`）：
- 提示："你之前用过这些引擎中的任何一个吗？"
- 选项：`Godot` / `Unity` / `Unreal Engine 5` / `Multiple — I'll explain` / `None of them`
- 如选择特定引擎 → 推荐该引擎。先前经验胜过所有其他因素。与他们确认并跳过矩阵。
- 如 "None" 或 "Multiple" → 继续下面的问题。

**问题 2-6 —— 决策矩阵输入**（仅在无先前引擎经验时）：

**问题 2 —— 目标平台**（始终第二问，通过 `AskUserQuestion` —— 平台在任何其他因素前就排除或重度加权引擎）：
- 提示："你的游戏目标平台是什么？"
- 选项：`PC (Steam / Epic)` / `Mobile (iOS / Android)` / `Console` / `Web / Browser` / `Multiple platforms`
- 直接影响推荐的平台规则：
  - 移动 → Unity 强烈首选；Unreal 不合适；Godot 对简单移动可行
  - 主机 → Unity 或 Unreal；Godot 主机支持需要第三方发行商或大量额外工作
  - Web → Godot 干净导出到 web；Unity WebGL 可用；Unreal 对 web 支持差
  - 仅 PC → 所有引擎可行；其他因素决定
  - 多平台 → Unity 在 PC/移动/主机间最便携

1. **什么类型的游戏？**（2D、3D，或两者？）
2. **主要输入方式？**（键盘/鼠标、手柄、触控，或混合？）
3. **团队规模和经验？**（独立新手、独立有经验、小团队？）
4. **有强烈语言偏好吗？**（GDScript、C#、C++、可视化脚本？）
5. **引擎许可预算？**（仅免费，还是商业许可也可？）

### 生成推荐

不要使用简单的评分矩阵来排除引擎。而是根据下面诚实的权衡推理用户的配置，然后呈现 1-2 个带完整上下文的推荐。始终以用户选择结束 —— 永远不要强制判定。

**引擎诚实权衡：**

**Godot 4**
- 真实优势：2D（同类最佳）、风格化/独立 3D、快速迭代、永久免费（MIT）、开源、学习曲线最平缓、最适合想要完全掌控的独立开发者
- 真实限制：3D 生态相比 Unity/Unreal 较薄（针对 3D 特定问题的教程、资产、社区答案更少）；大型开放世界 3D 在 Godot 中非常难且基本未经测试；主机导出需要第三方发行商或大量额外工作；专业就业市场较小
- 许可现实：真正免费，无任何收入阈值。MIT 许可意味着你拥有一切。
- 最佳匹配：任何规模的 2D 游戏；风格化/氛围 3D；封闭 3D 世界（非开放世界）；学习曲线重要的首个游戏项目；任何规模下预算是硬约束的项目

**Unity**
- 真实优势：中型 3D 和移动的行业标准；庞大的资产商店和教程生态；C# 是专业语言；独立团队中最佳主机认证支持；几乎所有类型都有强社区
- 真实限制：2023 年的许可争议损害了信任（运行时费用被提出后撤回 —— 政策变化的风险仍然存在）；C# 的初始曲线比 GDScript 更陡；对简单项目编辑器比 Godot 更重
- 许可现实：收入低于 $200K 且安装量低于 200K 时免费（Unity Personal/Plus）。仅在游戏真正成功时才变得昂贵 —— 大多数独立游戏永远不会达到此阈值。2023 年争议值得了解，但当前条款对大多数独立开发者来说是合理的。
- 最佳匹配：移动游戏；中型 3D；目标主机的游戏；有 C# 背景的开发者；需要大型资产商店的项目；2-5 人团队

**Unreal Engine 5**
- 真实优势：同类最佳的 3D 视觉效果（Lumen、Nanite、Chaos 物理）；AAA 和照片级 3D 的行业标准；大型开放世界支持成熟且经过生产验证；Blueprint 可视化脚本降低 C++ 门槛；适合目标高端 PC 或主机的游戏
- 真实限制：学习曲线最陡；编辑器最重（编译慢、项目大）；对风格化/2D/小规模游戏过度；C++ 确实难；不适合移动或 web；超过 $1M 总收入后 5% 版税
- 许可现实：5% 版税仅在每款游戏总收入超过 $1M 后适用。对首个游戏或任何不到 $1M 的游戏，免费。此阈值足够高，大多数独立开发者永远不会支付。
- 最佳匹配：AAA 质量 3D；大型开放世界游戏；照片级视觉效果；有 C++ 经验或愿意使用 Blueprint 的开发者；视觉保真度是核心卖点的高端 PC/主机游戏

**类型特定指导**（将其纳入推荐）：
- 任何风格 2D → Godot 强烈首选
- 风格化 / 氛围 / 封闭世界 3D → Godot 可行，Unity 是可靠替代
- 3D 开放世界（大型、无缝）→ Unity 或 Unreal；Godot 在这方面未经验证
- 3D 照片级 / AAA 质量 → Unreal
- 移动优先 → Unity 强烈首选
- 主机优先 → Unity 或 Unreal；Godot 主机支持需额外工作
- 恐怖 / 叙事 / 步行模拟 → 任何引擎；匹配美术风格和团队经验
- 动作 RPG / 魂系 → 3D 用 Unity 或 Unreal；社区支持和资产在此重要
- 2D 平台 → Godot
- 策略 / 俯视 / RTS → 视 2D 或 3D 而定 Godot 或 Unity

**推荐格式：**
1. 展示以用户特定因素为行的对比表
2. 给出带诚实推理的主推荐
3. 命名最佳替代及何时改选它
4. 明确说明："这是起点，不是判定 —— 你随时可以迁移引擎，许多开发者会跨项目切换。"
5. 使用 `AskUserQuestion` 确认："这个推荐感觉对吗，还是想探索不同引擎？"
   - 选项：`[主引擎] (Recommended)` / `[替代引擎]` / `[第三引擎]` / `进一步探索` / `键入其他`

**如用户选择"进一步探索"：**
使用 `AskUserQuestion` 提供概念特定的深度主题。始终从用户实际概念生成这些选项 —— 不要使用通用选项。始终至少包括：
- 主引擎对此概念的具体限制（例如"Godot 3D 对 [类型] 实际能走多远？"）
- 替代引擎对此概念的具体权衡
- 语言选择对此概念技术挑战的影响
- 任何概念特定的技术关注（例如自适应音频、开放世界流送、多人网络代码）

用户可选择多个主题。在返回引擎确认问题前深入回答每个所选主题。

---

## 3. Look Up Current Version

Once the engine is chosen:

- If version was provided, use it
- If no version provided, use WebSearch to find the latest stable release:
  - Search: `"[engine] latest stable version [current year]"`
  - Confirm with the user: "The latest stable [engine] is [version]. Use this?"

## 3. 查找当前版本

一旦选定引擎：

- 如提供了版本，使用它
- 如未提供版本，使用 WebSearch 查找最新稳定版本：
  - 搜索：`"[engine] latest stable version [current year]"`
  - 与用户确认："最新稳定 [engine] 是 [version]。使用此版本？"

---

## 4. Update CLAUDE.md Technology Stack

### Language Selection (Godot only)

If Godot was chosen, ask the user which language to use **before** showing the proposed Technology Stack:

> "Godot supports two primary languages:
>
>   **A) GDScript** — Python-like, Godot-native, fastest iteration. Best for beginners, solo devs, and teams coming from Python or Lua.
>   **B) C#** — .NET 8+, familiar to Unity developers, stronger IDE tooling (Rider / Visual Studio), slight performance advantage on heavy logic.
>   **C) Both** — GDScript for gameplay/UI scripting, C# for performance-critical systems. Advanced setup — requires .NET SDK alongside Godot.
>
> Which will this project primarily use?"

Record the choice. It determines the CLAUDE.md template, naming conventions, specialist routing, and which agent is spawned for code files throughout the project.

---

Read `CLAUDE.md` and show the user the proposed Technology Stack changes.
Ask: "May I write these engine settings to `CLAUDE.md`?"

Wait for confirmation before making any edits.

Update the Technology Stack section, replacing the `[CHOOSE]` placeholders with the actual values:

**For Godot** — use the template matching the language chosen above. See **Appendix A** at the bottom of this skill for all three variants (GDScript, C#, Both).

**For Unity:**
```markdown
- **Engine**: Unity [version]
- **Language**: C#
- **Build System**: Unity Build Pipeline
- **Asset Pipeline**: Unity Asset Import Pipeline + Addressables
```

**For Unreal:**
```markdown
- **Engine**: Unreal Engine [version]
- **Language**: C++ (primary), Blueprint (gameplay prototyping)
- **Build System**: Unreal Build Tool (UBT)
- **Asset Pipeline**: Unreal Content Pipeline
```

## 4. 更新 CLAUDE.md 技术栈

### 语言选择（仅 Godot）

如选择了 Godot，在展示拟议技术栈 **之前** 询问用户使用哪种语言：

> "Godot 支持两种主要语言：
>
>   **A) GDScript** —— 类 Python、Godot 原生、迭代最快。最适合新手、独立开发者、来自 Python 或 Lua 的团队。
>   **B) C#** —— .NET 8+、Unity 开发者熟悉、更强的 IDE 工具（Rider / Visual Studio）、重逻辑略有性能优势。
>   **C) Both** —— GDScript 用于玩法/UI 脚本，C# 用于性能关键系统。高级设置 —— 需要 .NET SDK 与 Godot 并存。
>
> 此项目主要使用哪种？"

记录选择。它决定整个项目中的 CLAUDE.md 模板、命名约定、专家路由和为代码文件派生哪个智能体。

---

读取 `CLAUDE.md` 并向用户展示拟议的技术栈变更。
询问："可以将这些引擎设置写入 `CLAUDE.md` 吗？"

进行任何编辑前等待确认。

更新 Technology Stack 章节，将 `[CHOOSE]` 占位符替换为实际值：

**For Godot** —— 使用匹配上面所选语言的模板。三种变体（GDScript、C#、Both）见本技能底部的 **Appendix A**。

**For Unity:**
```markdown
- **Engine**: Unity [version]
- **Language**: C#
- **Build System**: Unity Build Pipeline
- **Asset Pipeline**: Unity Asset Import Pipeline + Addressables
```

**For Unreal:**
```markdown
- **Engine**: Unreal Engine [version]
- **Language**: C++ (primary), Blueprint (gameplay prototyping)
- **Build System**: Unreal Build Tool (UBT)
- **Asset Pipeline**: Unreal Content Pipeline
```

---

## 5. Populate Technical Preferences

After updating CLAUDE.md, create or update `.claude/docs/technical-preferences.md` with
engine-appropriate defaults. Read the existing template first, then fill in:

### Engine & Language Section
- Fill from the engine choice made in step 4

### Naming Conventions (engine defaults)

**For Godot** — see **Appendix A** for GDScript, C#, and Both variants.

**For Unity (C#):**
- Classes: PascalCase (e.g., `PlayerController`)
- Public fields/properties: PascalCase (e.g., `MoveSpeed`)
- Private fields: _camelCase (e.g., `_moveSpeed`)
- Methods: PascalCase (e.g., `TakeDamage()`)
- Files: PascalCase matching class (e.g., `PlayerController.cs`)
- Constants: PascalCase or UPPER_SNAKE_CASE

**For Unreal (C++):**
- Classes: Prefixed PascalCase (`A` for Actor, `U` for UObject, `F` for struct)
- Variables: PascalCase (e.g., `MoveSpeed`)
- Functions: PascalCase (e.g., `TakeDamage()`)
- Booleans: `b` prefix (e.g., `bIsAlive`)
- Files: Match class without prefix (e.g., `PlayerController.h`)

### Input & Platform Section

Populate `## Input & Platform` using the answers gathered in Section 2 (or extracted
from the game concept). Derive the values using this mapping:

| Platform target | Gamepad Support | Touch Support |
|-----------------|-----------------|---------------|
| PC only | Partial (recommended) | None |
| Console | Full | None |
| Mobile | None | Full |
| PC + Console | Full | None |
| PC + Mobile | Partial | Full |
| Web | Partial | Partial |

For **Primary Input**, use the dominant input for the game genre:
- Action/RPG/platformer targeting console → Gamepad
- Strategy/point-and-click/RTS → Keyboard/Mouse
- Mobile game → Touch
- Cross-platform → ask the user

Present the derived values and ask the user to confirm or adjust before writing.

Example filled section:
```markdown
## Input & Platform
- **Target Platforms**: PC, Console
- **Input Methods**: Keyboard/Mouse, Gamepad
- **Primary Input**: Gamepad
- **Gamepad Support**: Full
- **Touch Support**: None
- **Platform Notes**: All UI must support d-pad navigation. No hover-only interactions.
```

### Remaining Sections
- **Performance Budgets**: Use `AskUserQuestion`:
  - Prompt: "Should I set default performance budgets now, or leave them for later?"
  - Options: `[A] Set defaults now (60fps, 16.6ms frame budget, engine-appropriate draw call limit)` / `[B] Leave as [TO BE CONFIGURED] — I'll set these when I know my target hardware`
  - If [A]: populate with the suggested defaults. If [B]: leave as placeholder.
- **Testing**: Suggest engine-appropriate framework (GUT for Godot, NUnit for Unity, etc.) — ask before adding.
- **Forbidden Patterns**: Leave as placeholder — do NOT pre-populate.
- **Allowed Libraries**: Leave as placeholder — do NOT pre-populate dependencies the project does not currently need. Only add a library here when it is actively being integrated, not speculatively.

> **Guardrail**: Never add speculative dependencies to Allowed Libraries. For example, do NOT add GodotSteam unless Steam integration is actively beginning in this session. Post-launch integrations should be added to Allowed Libraries when that work begins, not during engine setup.

### Engine Specialists Routing

Also populate the `## Engine Specialists` section in `technical-preferences.md` with the correct routing for the chosen engine:

**For Godot** — see **Appendix A** for the routing table matching the language chosen.

**For Unity:**
```markdown
## Engine Specialists
- **Primary**: unity-specialist
- **Language/Code Specialist**: unity-specialist (C# review — primary covers it)
- **Shader Specialist**: unity-shader-specialist (Shader Graph, HLSL, URP/HDRP materials)
- **UI Specialist**: unity-ui-specialist (UI Toolkit UXML/USS, UGUI Canvas, runtime UI)
- **Additional Specialists**: unity-dots-specialist (ECS, Jobs system, Burst compiler), unity-addressables-specialist (asset loading, memory management, content catalogs)
- **Routing Notes**: Invoke primary for architecture and general C# code review. Invoke DOTS specialist for any ECS/Jobs/Burst code. Invoke shader specialist for rendering and visual effects. Invoke UI specialist for all interface implementation. Invoke Addressables specialist for asset management systems.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.cs files) | unity-specialist |
| Shader / material files (.shader, .shadergraph, .mat) | unity-shader-specialist |
| UI / screen files (.uxml, .uss, Canvas prefabs) | unity-ui-specialist |
| Scene / prefab / level files (.unity, .prefab) | unity-specialist |
| Native extension / plugin files (.dll, native plugins) | unity-specialist |
| General architecture review | unity-specialist |
```

**For Unreal:**
```markdown
## Engine Specialists
- **Primary**: unreal-specialist
- **Language/Code Specialist**: ue-blueprint-specialist (Blueprint graphs) or unreal-specialist (C++)
- **Shader Specialist**: unreal-specialist (no dedicated shader specialist — primary covers materials)
- **UI Specialist**: ue-umg-specialist (UMG widgets, CommonUI, input routing, widget styling)
- **Additional Specialists**: ue-gas-specialist (Gameplay Ability System, attributes, gameplay effects), ue-replication-specialist (property replication, RPCs, client prediction, netcode)
- **Routing Notes**: Invoke primary for C++ architecture and broad engine decisions. Invoke Blueprint specialist for Blueprint graph architecture and BP/C++ boundary design. Invoke GAS specialist for all ability and attribute code. Invoke replication specialist for any multiplayer or networked systems. Invoke UMG specialist for all UI implementation.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.cpp, .h files) | unreal-specialist |
| Shader / material files (.usf, .ush, Material assets) | unreal-specialist |
| UI / screen files (.umg, UMG Widget Blueprints) | ue-umg-specialist |
| Scene / prefab / level files (.umap, .uasset) | unreal-specialist |
| Native extension / plugin files (Plugin .uplugin, modules) | unreal-specialist |
| Blueprint graphs (.uasset BP classes) | ue-blueprint-specialist |
| General architecture review | unreal-specialist |
```

### Collaborative Step

Present the filled-in preferences to the user. For Godot, include the chosen language and note where the full naming conventions and routing tables live:
> "Here are the default technical preferences for [engine] ([language if Godot]). The naming conventions and specialist routing are in Appendix A of this skill — I'll apply the [GDScript/C#/Both] variant. Want to customize any of these, or shall I save the defaults?"

For all other engines, present the defaults directly without referencing the appendix.

Wait for approval before writing the file.

## 5. 填充技术偏好

更新 CLAUDE.md 后，创建或更新 `.claude/docs/technical-preferences.md`
填充适合引擎的默认值。先读取现有模板，然后填充：

### 引擎与语言章节
- 从步骤 4 的引擎选择填充

### 命名约定（引擎默认值）

**For Godot** —— 三种变体（GDScript、C#、Both）见 **Appendix A**。

**For Unity (C#):**
- 类：PascalCase（例如 `PlayerController`）
- 公共字段/属性：PascalCase（例如 `MoveSpeed`）
- 私有字段：_camelCase（例如 `_moveSpeed`）
- 方法：PascalCase（例如 `TakeDamage()`）
- 文件：PascalCase 匹配类（例如 `PlayerController.cs`）
- 常量：PascalCase 或 UPPER_SNAKE_CASE

**For Unreal (C++):**
- 类：带前缀的 PascalCase（Actor 用 `A`，UObject 用 `U`，struct 用 `F`）
- 变量：PascalCase（例如 `MoveSpeed`）
- 函数：PascalCase（例如 `TakeDamage()`）
- 布尔值：`b` 前缀（例如 `bIsAlive`）
- 文件：匹配类无前缀（例如 `PlayerController.h`）

### 输入与平台章节

使用第 2 节收集的答案（或从游戏概念提取）填充 `## Input & Platform`。使用此映射推导值：

| 平台目标 | Gamepad Support | Touch Support |
|-----------------|-----------------|---------------|
| 仅 PC | Partial（推荐） | None |
| 主机 | Full | None |
| 移动 | None | Full |
| PC + 主机 | Full | None |
| PC + 移动 | Partial | Full |
| Web | Partial | Partial |

对于 **Primary Input**，使用游戏类型的主导输入：
- 动作/RPG/平台游戏目标主机 → Gamepad
- 策略/点击/RTS → Keyboard/Mouse
- 移动游戏 → Touch
- 跨平台 → 询问用户

呈现推导值并请用户在写入前确认或调整。

填充示例：
```markdown
## Input & Platform
- **Target Platforms**: PC, Console
- **Input Methods**: Keyboard/Mouse, Gamepad
- **Primary Input**: Gamepad
- **Gamepad Support**: Full
- **Touch Support**: None
- **Platform Notes**: All UI must support d-pad navigation. No hover-only interactions.
```

### 剩余章节
- **Performance Budgets**：使用 `AskUserQuestion`：
  - 提示："现在应该设置默认性能预算，还是稍后？"
  - 选项：`[A] Set defaults now (60fps, 16.6ms frame budget, engine-appropriate draw call limit)` / `[B] Leave as [TO BE CONFIGURED] — I'll set these when I know my target hardware`
  - 如 [A]：用建议默认值填充。如 [B]：保留为占位符。
- **Testing**：建议适合引擎的框架（Godot 用 GUT，Unity 用 NUnit 等）—— 添加前询问。
- **Forbidden Patterns**：保留为占位符 —— 不要预填充。
- **Allowed Libraries**：保留为占位符 —— 不要预填充项目当前不需要的依赖。仅在某个库正被积极集成时才在此添加，而非投机性添加。

> **护栏**：永远不要向 Allowed Libraries 添加投机性依赖。例如，除非 Steam 集成在本会话中正在开始，否则不要添加 GodotSteam。上线后的集成应在工作开始时添加到 Allowed Libraries，而非引擎设置时。

### 引擎专家路由

同时使用所选引擎的正确路由填充 `technical-preferences.md` 的 `## Engine Specialists` 章节：

**For Godot** —— 匹配所选语言的路由表见 **Appendix A**。

**For Unity:**
```markdown
## Engine Specialists
- **Primary**: unity-specialist
- **Language/Code Specialist**: unity-specialist (C# review — primary covers it)
- **Shader Specialist**: unity-shader-specialist (Shader Graph, HLSL, URP/HDRP materials)
- **UI Specialist**: unity-ui-specialist (UI Toolkit UXML/USS, UGUI Canvas, runtime UI)
- **Additional Specialists**: unity-dots-specialist (ECS, Jobs system, Burst compiler), unity-addressables-specialist (asset loading, memory management, content catalogs)
- **Routing Notes**: Invoke primary for architecture and general C# code review. Invoke DOTS specialist for any ECS/Jobs/Burst code. Invoke shader specialist for rendering and visual effects. Invoke UI specialist for all interface implementation. Invoke Addressables specialist for asset management systems.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.cs files) | unity-specialist |
| Shader / material files (.shader, .shadergraph, .mat) | unity-shader-specialist |
| UI / screen files (.uxml, .uss, Canvas prefabs) | unity-ui-specialist |
| Scene / prefab / level files (.unity, .prefab) | unity-specialist |
| Native extension / plugin files (.dll, native plugins) | unity-specialist |
| General architecture review | unity-specialist |
```

**For Unreal:**
```markdown
## Engine Specialists
- **Primary**: unreal-specialist
- **Language/Code Specialist**: ue-blueprint-specialist (Blueprint graphs) or unreal-specialist (C++)
- **Shader Specialist**: unreal-specialist (no dedicated shader specialist — primary covers materials)
- **UI Specialist**: ue-umg-specialist (UMG widgets, CommonUI, input routing, widget styling)
- **Additional Specialists**: ue-gas-specialist (Gameplay Ability System, attributes, gameplay effects), ue-replication-specialist (property replication, RPCs, client prediction, netcode)
- **Routing Notes**: Invoke primary for C++ architecture and broad engine decisions. Invoke Blueprint specialist for Blueprint graph architecture and BP/C++ boundary design. Invoke GAS specialist for all ability and attribute code. Invoke replication specialist for any multiplayer or networked systems. Invoke UMG specialist for all UI implementation.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.cpp, .h files) | unreal-specialist |
| Shader / material files (.usf, .ush, Material assets) | unreal-specialist |
| UI / screen files (.umg, UMG Widget Blueprints) | ue-umg-specialist |
| Scene / prefab / level files (.umap, .uasset) | unreal-specialist |
| Native extension / plugin files (Plugin .uplugin, modules) | unreal-specialist |
| Blueprint graphs (.uasset BP classes) | ue-blueprint-specialist |
| General architecture review | unreal-specialist |
```

### 协作步骤

向用户呈现填充后的偏好。对于 Godot，包括所选语言并注明完整命名约定和路由表所在位置：
> "这里是 [engine]（[language if Godot]）的默认技术偏好。命名约定和专家路由在本技能的 Appendix A —— 我将应用 [GDScript/C#/Both] 变体。要自定义其中任何内容，还是我保存默认值？"

对于所有其他引擎，直接呈现默认值而不引用附录。

写入文件前等待批准。

---

## 6. Determine Knowledge Gap

Check whether the engine version is likely beyond the LLM's training data.

**Known approximate coverage** (update this as models change):
- LLM knowledge cutoff: **May 2025**
- Godot: training data likely covers up to ~4.3
- Unity: training data likely covers up to ~2023.x / early 6000.x
- Unreal: training data likely covers up to ~5.3 / early 5.4

Compare the user's chosen version against these baselines:

- **Within training data** → `LOW RISK` — reference docs optional but recommended
- **Near the edge** → `MEDIUM RISK` — reference docs recommended
- **Beyond training data** → `HIGH RISK` — reference docs required

Inform the user which category they're in and why.

## 6. 确定知识缺口

检查引擎版本是否可能超出 LLM 的训练数据。

**已知近似覆盖**（随模型变化更新此内容）：
- LLM 知识截止线：**May 2025**
- Godot：训练数据可能覆盖到 ~4.3
- Unity：训练数据可能覆盖到 ~2023.x / 早期 6000.x
- Unreal：训练数据可能覆盖到 ~5.3 / 早期 5.4

将用户所选版本与这些基线对比：

- **在训练数据内** → `LOW RISK` —— 参考文档可选但推荐
- **接近边缘** → `MEDIUM RISK` —— 推荐参考文档
- **超出训练数据** → `HIGH RISK` —— 必需参考文档

告知用户他们属于哪一类及原因。

---

## 7. Populate Engine Reference Docs

### If WITHIN training data (LOW RISK):

Create a minimal `docs/engine-reference/<engine>/VERSION.md`:

```markdown
# [Engine] — Version Reference

| Field | Value |
|-------|-------|
| **Engine Version** | [version] |
| **Project Pinned** | [today's date] |
| **LLM Knowledge Cutoff** | May 2025 |
| **Risk Level** | LOW — version is within LLM training data |

## Note

This engine version is within the LLM's training data. Engine reference
docs are optional but can be added later if agents suggest incorrect APIs.

Run `/setup-engine refresh` to populate full reference docs at any time.
```

Do NOT create breaking-changes.md, deprecated-apis.md, etc. — they would
add context cost with minimal value.

### If BEYOND training data (MEDIUM or HIGH RISK):

Create the full reference doc set by searching the web:

1. **Search for the official migration/upgrade guide**:
   - `"[engine] [old version] to [new version] migration guide"`
   - `"[engine] [version] breaking changes"`
   - `"[engine] [version] changelog"`
   - `"[engine] [version] deprecated API"`

2. **Fetch and extract** from official documentation:
   - Breaking changes between each version from the training cutoff to current
   - Deprecated APIs with replacements
   - New features and best practices

Ask: "May I create the engine reference docs under `docs/engine-reference/<engine>/`?"

Wait for confirmation before writing any files.

3. **Create the full reference directory**:
   ```
   docs/engine-reference/<engine>/
   ├── VERSION.md              # Version pin + knowledge gap analysis
   ├── breaking-changes.md     # Version-by-version breaking changes
   ├── deprecated-apis.md      # "Don't use X → Use Y" tables
   ├── current-best-practices.md  # New practices since training cutoff
   └── modules/                # Per-subsystem references (create as needed)
   ```

4. **Populate each file** using real data from the web searches, following
   the format established in existing reference docs. Every file must have
   a "Last verified: [date]" header.

5. **For module files**: Only create modules for subsystems where significant
   changes occurred. Don't create empty or minimal module files.

## 7. 填充引擎参考文档

### 如在训练数据内（LOW RISK）：

创建最小化的 `docs/engine-reference/<engine>/VERSION.md`：

```markdown
# [Engine] — Version Reference

| Field | Value |
|-------|-------|
| **Engine Version** | [version] |
| **Project Pinned** | [today's date] |
| **LLM Knowledge Cutoff** | May 2025 |
| **Risk Level** | LOW — version is within LLM training data |

## Note

This engine version is within the LLM's training data. Engine reference
docs are optional but can be added later if agents suggest incorrect APIs.

Run `/setup-engine refresh` to populate full reference docs at any time.
```

不要创建 breaking-changes.md、deprecated-apis.md 等 —— 它们会
增加上下文成本而价值有限。

### 如超出训练数据（MEDIUM 或 HIGH RISK）：

通过搜索网络创建完整参考文档集：

1. **搜索官方迁移/升级指南**：
   - `"[engine] [old version] to [new version] migration guide"`
   - `"[engine] [version] breaking changes"`
   - `"[engine] [version] changelog"`
   - `"[engine] [version] deprecated API"`

2. **从官方文档抓取并提取**：
   - 训练截止线到当前版本之间每个版本的重大变更
   - 弃用 API 及替代品
   - 新功能和最佳实践

询问："可以在 `docs/engine-reference/<engine>/` 下创建引擎参考文档吗？"

写入任何文件前等待确认。

3. **创建完整参考目录**：
   ```
   docs/engine-reference/<engine>/
   ├── VERSION.md              # Version pin + knowledge gap analysis
   ├── breaking-changes.md     # Version-by-version breaking changes
   ├── deprecated-apis.md      # "Don't use X → Use Y" tables
   ├── current-best-practices.md  # New practices since training cutoff
   └── modules/                # Per-subsystem references (create as needed)
   ```

4. **填充每个文件**，使用网络搜索的真实数据，遵循
   现有参考文档中建立的格式。每个文件必须有
   "Last verified: [date]" 头部。

5. **对于模块文件**：仅为发生重大变更的子系统创建模块。
   不要创建空或最小化的模块文件。

---

## 8. Update CLAUDE.md Import

Ask: "May I update the `@` import in `CLAUDE.md` to point to the new engine reference?"

Wait for confirmation, then update the `@` import under "Engine Version Reference" to point to the
correct engine:

```markdown
## Engine Version Reference

@docs/engine-reference/<engine>/VERSION.md
```

If the previous import pointed to a different engine (e.g., switching from
Godot to Unity), update it.

## 8. 更新 CLAUDE.md 导入

询问："可以更新 `CLAUDE.md` 中的 `@` 导入以指向新引擎参考吗？"

等待确认，然后更新 "Engine Version Reference" 下的 `@` 导入以指向
正确引擎：

```markdown
## Engine Version Reference

@docs/engine-reference/<engine>/VERSION.md
```

如先前导入指向不同引擎（例如从
Godot 切换到 Unity），更新它。

---

## 9. Update Agent Instructions

Ask: "May I add a Version Awareness section to the engine specialist agent files?" before making any edits.

For the chosen engine's specialist agents, verify they have a
"Version Awareness" section. If not, add one following the pattern in
the existing Godot specialist agents.

The section should instruct the agent to:
1. Read `docs/engine-reference/<engine>/VERSION.md`
2. Check deprecated APIs before suggesting code
3. Check breaking changes for relevant version transitions
4. Use WebSearch to verify uncertain APIs

## 9. 更新智能体指令

进行任何编辑前询问："可以在引擎专家智能体文件中添加 Version Awareness 章节吗？"

对于所选引擎的专家智能体，验证它们有
"Version Awareness" 章节。如无，按照
现有 Godot 专家智能体中的模式添加一个。

该章节应指示智能体：
1. 读取 `docs/engine-reference/<engine>/VERSION.md`
2. 在建议代码前检查弃用 API
3. 检查相关版本过渡的重大变更
4. 使用 WebSearch 验证不确定的 API

---

## 10. Refresh Subcommand

If invoked as `/setup-engine refresh`:

1. Read the existing `docs/engine-reference/<engine>/VERSION.md` to get
   the current engine and version
2. Use WebSearch to check for:
   - New engine releases since last verification
   - Updated migration guides
   - Newly deprecated APIs
3. Update all reference docs with new findings
4. Update "Last verified" dates on all modified files
5. Report what changed

## 10. 刷新子命令

如以 `/setup-engine refresh` 调用：

1. 读取现有 `docs/engine-reference/<engine>/VERSION.md` 以获取
   当前引擎和版本
2. 使用 WebSearch 检查：
   - 上次验证后的新引擎发布
   - 更新的迁移指南
   - 新弃用的 API
3. 用新发现更新所有参考文档
4. 更新所有修改文件的 "Last verified" 日期
5. 报告变更内容

---

## 11. Upgrade Subcommand

If invoked as `/setup-engine upgrade [old-version] [new-version]`:

### Step 1 — Read Current Version State

Read `docs/engine-reference/<engine>/VERSION.md` to confirm the current pinned
version, risk level, and any migration note URLs already recorded. If
`old-version` was not provided as an argument, use the pinned version from this
file.

### Step 2 — Fetch Migration Guide

Use WebSearch and WebFetch to locate the official migration guide between
`old-version` and `new-version`:

- Search: `"[engine] [old-version] to [new-version] migration guide"`
- Search: `"[engine] [new-version] breaking changes changelog"`
- Fetch the migration guide URL from VERSION.md if one is already recorded,
  or use the URL found via search.

Extract: renamed APIs, removed APIs, changed defaults, behavior changes, and
any "must migrate" items.

### Step 3 — Pre-Upgrade Audit

Scan `src/` for code that uses APIs known to be deprecated or changed in the
target version:

- Use Grep to search for deprecated API names extracted from the migration
  guide (e.g., old function names, removed node types, changed property names)
- List each file that matches, with the specific API reference found

Present the audit results as a table:

```
Pre-Upgrade Audit: [engine] [old-version] → [new-version]
==========================================================

Files requiring changes:
  File                              | Deprecated API Found       | Effort
  --------------------------------- | -------------------------- | ------
  src/gameplay/player_movement.gd   | old_api_name               | Low
  src/ui/hud.gd                     | removed_node_type          | Medium

Breaking changes to watch for:
  - [change description from migration guide]
  - [change description from migration guide]

Recommended migration order (dependency-sorted):
  1. [system/layer with fewest dependencies first]
  2. [next system]
  ...
```

If no deprecated APIs are found in `src/`, report: "No deprecated API usage
found in src/ — upgrade may be low-risk."

### Step 4 — Confirm Before Updating

Ask the user before making any changes:

> "Pre-upgrade audit complete. Found [N] files using deprecated APIs.
> Proceed with upgrading VERSION.md to [new-version]?
> (This will update the pinned version and add migration notes — it does NOT
> change any source files. Source migration is done manually or via stories.)"

Wait for explicit confirmation before continuing.

### Step 5 — Update VERSION.md

After confirmation:

1. Update `docs/engine-reference/<engine>/VERSION.md`:
   - `Engine Version` → `[new-version]`
   - `Project Pinned` → today's date
   - `Last Docs Verified` → today's date
   - Re-evaluate and update the `Risk Level` and `Post-Cutoff Version Timeline`
     table if the new version falls beyond the LLM knowledge cutoff
   - Add a `## Migration Notes — [old-version] → [new-version]` section
     containing: migration guide URL, key breaking changes, deprecated APIs
     found in this project, and recommended migration order from the audit

2. If `breaking-changes.md` or `deprecated-apis.md` exist in the engine
   reference directory, append the new version's changes to those files.

### Step 6 — Post-Upgrade Reminder

After updating VERSION.md, output:

```
VERSION.md updated: [engine] [old-version] → [new-version]

Next steps:
1. Migrate deprecated API usages in the [N] files listed above
2. Run /setup-engine refresh after upgrading the actual engine binary to
   verify no new deprecations were missed
3. Run /architecture-review — the engine upgrade may invalidate ADRs that
   reference specific APIs or engine capabilities
4. If any ADRs are invalidated, run /propagate-design-change to update
   downstream stories
```

## 11. 升级子命令

如以 `/setup-engine upgrade [old-version] [new-version]` 调用：

### 步骤 1 —— 读取当前版本状态

读取 `docs/engine-reference/<engine>/VERSION.md` 以确认当前锁定
版本、风险等级和已记录的任何迁移说明 URL。如
未提供 `old-version` 参数，使用此文件中的锁定版本。

### 步骤 2 —— 抓取迁移指南

使用 WebSearch 和 WebFetch 定位
`old-version` 与 `new-version` 之间的官方迁移指南：

- 搜索：`"[engine] [old-version] to [new-version] migration guide"`
- 搜索：`"[engine] [new-version] breaking changes changelog"`
- 如 VERSION.md 中已记录迁移指南 URL，抓取它，
  或使用搜索找到的 URL。

提取：重命名的 API、移除的 API、变更的默认值、行为变更，以及
任何"必须迁移"项。

### 步骤 3 —— 升级前审计

扫描 `src/` 中使用了已知在目标版本中弃用或变更的 API 的代码：

- 使用 Grep 搜索从迁移指南提取的弃用 API 名称
  （例如旧函数名、移除的节点类型、变更的属性名）
- 列出每个匹配文件及找到的具体 API 引用

将审计结果呈现为表格：

```
Pre-Upgrade Audit: [engine] [old-version] → [new-version]
==========================================================

Files requiring changes:
  File                              | Deprecated API Found       | Effort
  --------------------------------- | -------------------------- | ------
  src/gameplay/player_movement.gd   | old_api_name               | Low
  src/ui/hud.gd                     | removed_node_type          | Medium

Breaking changes to watch for:
  - [change description from migration guide]
  - [change description from migration guide]

Recommended migration order (dependency-sorted):
  1. [system/layer with fewest dependencies first]
  2. [next system]
  ...
```

如 `src/` 中未找到弃用 API，报告："No deprecated API usage
found in src/ — upgrade may be low-risk."

### 步骤 4 —— 更新前确认

进行任何变更前询问用户：

> "升级前审计完成。发现 [N] 个文件使用了弃用 API。
> 是否继续升级 VERSION.md 到 [new-version]？
> （这将更新锁定版本并添加迁移说明 —— 它不会
> 更改任何源文件。源代码迁移通过手动或故事完成。）"

继续前等待显式确认。

### 步骤 5 —— 更新 VERSION.md

确认后：

1. 更新 `docs/engine-reference/<engine>/VERSION.md`：
   - `Engine Version` → `[new-version]`
   - `Project Pinned` → today's date
   - `Last Docs Verified` → today's date
   - 如新版本超出 LLM 知识截止线，重新评估并更新 `Risk Level` 和 `Post-Cutoff Version Timeline`
     表
   - 添加 `## Migration Notes — [old-version] → [new-version]` 章节，
     包含：迁移指南 URL、关键重大变更、
     本项目中发现的弃用 API，以及来自审计的推荐迁移顺序

2. 如 `breaking-changes.md` 或 `deprecated-apis.md` 存在于引擎
   参考目录，将新版本的变更追加到这些文件。

### 步骤 6 —— 升级后提醒

更新 VERSION.md 后，输出：

```
VERSION.md updated: [engine] [old-version] → [new-version]

Next steps:
1. Migrate deprecated API usages in the [N] files listed above
2. Run /setup-engine refresh after upgrading the actual engine binary to
   verify no new deprecations were missed
3. Run /architecture-review — the engine upgrade may invalidate ADRs that
   reference specific APIs or engine capabilities
4. If any ADRs are invalidated, run /propagate-design-change to update
   downstream stories
```

---

## 12. Output Summary

After setup is complete, output:

```
Engine Setup Complete
=====================
Engine:          [name] [version]
Language:        [GDScript | C# | GDScript + C# | C# | C++ + Blueprint]
Knowledge Risk:  [LOW/MEDIUM/HIGH]
Reference Docs:  [created/skipped]
CLAUDE.md:       [updated]
Tech Prefs:      [created/updated]
Agent Config:    [verified]

Next Steps:
1. Review docs/engine-reference/<engine>/VERSION.md
2. [If from /brainstorm] Run /map-systems to decompose your concept into individual systems
3. [If from /brainstorm] Run /design-system to author per-system GDDs (guided, section-by-section)
4. [If from /brainstorm] Run /prototype [core-mechanic] to validate the core idea before writing GDDs
5. [If fresh start] Run /brainstorm to discover your game concept
6. Create your first milestone: /sprint-plan new
```

## 12. 输出总结

设置完成后，输出：

```
Engine Setup Complete
=====================
Engine:          [name] [version]
Language:        [GDScript | C# | GDScript + C# | C# | C++ + Blueprint]
Knowledge Risk:  [LOW/MEDIUM/HIGH]
Reference Docs:  [created/skipped]
CLAUDE.md:       [updated]
Tech Prefs:      [created/updated]
Agent Config:    [verified]

Next Steps:
1. Review docs/engine-reference/<engine>/VERSION.md
2. [If from /brainstorm] Run /map-systems to decompose your concept into individual systems
3. [If from /brainstorm] Run /design-system to author per-system GDDs (guided, section-by-section)
4. [If from /brainstorm] Run /prototype [core-mechanic] to validate the core idea before writing GDDs
5. [If fresh start] Run /brainstorm to discover your game concept
6. Create your first milestone: /sprint-plan new
```

---

Verdict: **COMPLETE** — engine configured and reference docs populated.

## Guardrails

- NEVER guess an engine version — always verify via WebSearch or user confirmation
- NEVER overwrite existing reference docs without asking — append or update
- If reference docs already exist for a different engine, ask before replacing
- Always show the user what you're about to change before making CLAUDE.md edits
- If WebSearch returns ambiguous results, show the user and let them decide
- When the user chose **GDScript**: copy the GDScript CLAUDE.md template from Appendix A1 exactly. NEVER add "C++ via GDExtension" to the Language field. GDScript projects may use GDExtension, but it is not a primary project language. The `godot-gdextension-specialist` in the routing table is available for when native extensions are needed — it does not make C++ a project language.

判定：**COMPLETE** —— 引擎已配置，参考文档已填充。

## 护栏

- 永远不要猜测引擎版本 —— 始终通过 WebSearch 或用户确认验证
- 永远不要在不询问的情况下覆盖现有参考文档 —— 追加或更新
- 如参考文档已为不同引擎存在，替换前询问
- 在进行 CLAUDE.md 编辑前始终向用户展示将变更的内容
- 如 WebSearch 返回模糊结果，向用户展示并让他们决定
- 当用户选择 **GDScript** 时：完全从 Appendix A1 复制 GDScript CLAUDE.md 模板。永远不要在 Language 字段添加 "C++ via GDExtension"。GDScript 项目可以使用 GDExtension，但它不是主要项目语言。路由表中的 `godot-gdextension-specialist` 在需要原生扩展时可用 —— 它不会使 C++ 成为项目语言。

---

## Appendix A — Godot Language Configuration

All Godot-specific variants for language-dependent configuration. Referenced from Sections 4 and 5 — only relevant when Godot is the chosen engine. Use the subsection matching the language chosen in Section 4.

## Appendix A —— Godot 语言配置

所有 Godot 特定的语言相关配置变体。从第 4 和第 5 节引用 —— 仅在选择 Godot 作为引擎时相关。使用匹配第 4 节中所选语言的子章节。

---

### A1. CLAUDE.md Technology Stack Templates

**GDScript:**
```markdown
- **Engine**: Godot [version]
- **Language**: GDScript
- **Build System**: SCons (engine), Godot Export Templates
- **Asset Pipeline**: Godot Import System + custom resource pipeline
```

> **Guardrail**: When using this GDScript template, write the Language field as exactly "`GDScript`" — no additions. Do NOT append "C++ via GDExtension" or any other language. The C# template below includes GDExtension because C# projects commonly wrap native code; GDScript projects do not.

**C#:**
```markdown
- **Engine**: Godot [version]
- **Language**: C# (.NET 8+, primary), C++ via GDExtension (native plugins only)
- **Build System**: .NET SDK + Godot Export Templates
- **Asset Pipeline**: Godot Import System + custom resource pipeline
```

**Both — GDScript + C#:**
```markdown
- **Engine**: Godot [version]
- **Language**: GDScript (gameplay/UI scripting), C# (performance-critical systems), C++ via GDExtension (native only)
- **Build System**: .NET SDK + Godot Export Templates
- **Asset Pipeline**: Godot Import System + custom resource pipeline
```

### A1. CLAUDE.md 技术栈模板

**GDScript:**
```markdown
- **Engine**: Godot [version]
- **Language**: GDScript
- **Build System**: SCons (engine), Godot Export Templates
- **Asset Pipeline**: Godot Import System + custom resource pipeline
```

> **护栏**：使用此 GDScript 模板时，Language 字段完全写为 "`GDScript`" —— 无附加。不要追加 "C++ via GDExtension" 或任何其他语言。下面的 C# 模板包含 GDExtension，因为 C# 项目通常包装原生代码；GDScript 项目则不。

**C#:**
```markdown
- **Engine**: Godot [version]
- **Language**: C# (.NET 8+, primary), C++ via GDExtension (native plugins only)
- **Build System**: .NET SDK + Godot Export Templates
- **Asset Pipeline**: Godot Import System + custom resource pipeline
```

**Both —— GDScript + C#:**
```markdown
- **Engine**: Godot [version]
- **Language**: GDScript (gameplay/UI scripting), C# (performance-critical systems), C++ via GDExtension (native only)
- **Build System**: .NET SDK + Godot Export Templates
- **Asset Pipeline**: Godot Import System + custom resource pipeline
```

---

### A2. Naming Conventions

**GDScript:**
- Classes: PascalCase (e.g., `PlayerController`)
- Variables/functions: snake_case (e.g., `move_speed`)
- Signals: snake_case past tense (e.g., `health_changed`)
- Files: snake_case matching class (e.g., `player_controller.gd`)
- Scenes: PascalCase matching root node (e.g., `PlayerController.tscn`)
- Constants: UPPER_SNAKE_CASE (e.g., `MAX_HEALTH`)

**C#:**
- Classes: PascalCase (`PlayerController`) — must also be `partial`
- Public properties/fields: PascalCase (`MoveSpeed`, `JumpVelocity`)
- Private fields: `_camelCase` (`_currentHealth`, `_isGrounded`)
- Methods: PascalCase (`TakeDamage()`, `GetCurrentHealth()`)
- Signal delegates: PascalCase + `EventHandler` suffix (`HealthChangedEventHandler`)
- Files: PascalCase matching class (`PlayerController.cs`)
- Scenes: PascalCase matching root node (`PlayerController.tscn`)
- Constants: PascalCase (`MaxHealth`, `DefaultMoveSpeed`)

**Both — GDScript + C#:**
Use GDScript conventions for `.gd` files and C# conventions for `.cs` files. Mixed-language files do not exist — the boundary is per-file. When in doubt about which language a new system should use, ask the user and record the decision in `technical-preferences.md`.

### A2. 命名约定

**GDScript:**
- Classes: PascalCase (e.g., `PlayerController`)
- Variables/functions: snake_case (e.g., `move_speed`)
- Signals: snake_case past tense (e.g., `health_changed`)
- Files: snake_case matching class (e.g., `player_controller.gd`)
- Scenes: PascalCase matching root node (e.g., `PlayerController.tscn`)
- Constants: UPPER_SNAKE_CASE (e.g., `MAX_HEALTH`)

**C#:**
- Classes: PascalCase (`PlayerController`) — must also be `partial`
- Public properties/fields: PascalCase (`MoveSpeed`, `JumpVelocity`)
- Private fields: `_camelCase` (`_currentHealth`, `_isGrounded`)
- Methods: PascalCase (`TakeDamage()`, `GetCurrentHealth()`)
- Signal delegates: PascalCase + `EventHandler` suffix (`HealthChangedEventHandler`)
- Files: PascalCase matching class (`PlayerController.cs`)
- Scenes: PascalCase matching root node (`PlayerController.tscn`)
- Constants: PascalCase (`MaxHealth`, `DefaultMoveSpeed`)

**Both —— GDScript + C#:**
`.gd` 文件使用 GDScript 约定，`.cs` 文件使用 C# 约定。不存在混合语言文件 —— 边界按文件划分。对新系统应使用哪种语言存疑时，询问用户并将决策记录到 `technical-preferences.md`。

---

### A3. Engine Specialists Routing

**GDScript:**
```markdown
## Engine Specialists
- **Primary**: godot-specialist
- **Language/Code Specialist**: godot-gdscript-specialist (all .gd files)
- **Shader Specialist**: godot-shader-specialist (.gdshader files, VisualShader resources)
- **UI Specialist**: godot-specialist (no dedicated UI specialist — primary covers all UI)
- **Additional Specialists**: godot-gdextension-specialist (GDExtension / native C++ bindings only)
- **Routing Notes**: Invoke primary for architecture decisions, ADR validation, and cross-cutting code review. Invoke GDScript specialist for code quality, signal architecture, static typing enforcement, and GDScript idioms. Invoke shader specialist for material design and shader code. Invoke GDExtension specialist only when native extensions are involved.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.gd files) | godot-gdscript-specialist |
| Shader / material files (.gdshader, VisualShader) | godot-shader-specialist |
| UI / screen files (Control nodes, CanvasLayer) | godot-specialist |
| Scene / prefab / level files (.tscn, .tres) | godot-specialist |
| Native extension / plugin files (.gdextension, C++) | godot-gdextension-specialist |
| General architecture review | godot-specialist |
```

**C#:**
```markdown
## Engine Specialists
- **Primary**: godot-specialist
- **Language/Code Specialist**: godot-csharp-specialist (all .cs files)
- **Shader Specialist**: godot-shader-specialist (.gdshader files, VisualShader resources)
- **UI Specialist**: godot-specialist (no dedicated UI specialist — primary covers all UI)
- **Additional Specialists**: godot-gdextension-specialist (GDExtension / native C++ bindings only)
- **Routing Notes**: Invoke primary for architecture decisions, ADR validation, and cross-cutting code review. Invoke C# specialist for code quality, [Signal] delegate patterns, [Export] attributes, .csproj management, and C#-specific Godot idioms. Invoke shader specialist for material design and shader code. Invoke GDExtension specialist only when native C++ plugins are involved.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.cs files) | godot-csharp-specialist |
| Shader / material files (.gdshader, VisualShader) | godot-shader-specialist |
| UI / screen files (Control nodes, CanvasLayer) | godot-specialist |
| Scene / prefab / level files (.tscn, .tres) | godot-specialist |
| Project config (.csproj, NuGet) | godot-csharp-specialist |
| Native extension / plugin files (.gdextension, C++) | godot-gdextension-specialist |
| General architecture review | godot-specialist |
```

**Both — GDScript + C#:**
```markdown
## Engine Specialists
- **Primary**: godot-specialist
- **GDScript Specialist**: godot-gdscript-specialist (.gd files — gameplay/UI scripts)
- **C# Specialist**: godot-csharp-specialist (.cs files — performance-critical systems)
- **Shader Specialist**: godot-shader-specialist (.gdshader files, VisualShader resources)
- **UI Specialist**: godot-specialist (no dedicated UI specialist — primary covers all UI)
- **Additional Specialists**: godot-gdextension-specialist (GDExtension / native C++ bindings only)
- **Routing Notes**: Invoke primary for cross-language architecture decisions and which systems belong in which language. Invoke GDScript specialist for .gd files. Invoke C# specialist for .cs files and .csproj management. Prefer signals over direct cross-language method calls at the boundary.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.gd files) | godot-gdscript-specialist |
| Game code (.cs files) | godot-csharp-specialist |
| Cross-language boundary decisions | godot-specialist |
| Shader / material files (.gdshader, VisualShader) | godot-shader-specialist |
| UI / screen files (Control nodes, CanvasLayer) | godot-specialist |
| Scene / prefab / level files (.tscn, .tres) | godot-specialist |
| Project config (.csproj, NuGet) | godot-csharp-specialist |
| Native extension / plugin files (.gdextension, C++) | godot-gdextension-specialist |
| General architecture review | godot-specialist |
```

### A3. 引擎专家路由

**GDScript:**
```markdown
## Engine Specialists
- **Primary**: godot-specialist
- **Language/Code Specialist**: godot-gdscript-specialist (all .gd files)
- **Shader Specialist**: godot-shader-specialist (.gdshader files, VisualShader resources)
- **UI Specialist**: godot-specialist (no dedicated UI specialist — primary covers all UI)
- **Additional Specialists**: godot-gdextension-specialist (GDExtension / native C++ bindings only)
- **Routing Notes**: Invoke primary for architecture decisions, ADR validation, and cross-cutting code review. Invoke GDScript specialist for code quality, signal architecture, static typing enforcement, and GDScript idioms. Invoke shader specialist for material design and shader code. Invoke GDExtension specialist only when native extensions are involved.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.gd files) | godot-gdscript-specialist |
| Shader / material files (.gdshader, VisualShader) | godot-shader-specialist |
| UI / screen files (Control nodes, CanvasLayer) | godot-specialist |
| Scene / prefab / level files (.tscn, .tres) | godot-specialist |
| Native extension / plugin files (.gdextension, C++) | godot-gdextension-specialist |
| General architecture review | godot-specialist |
```

**C#:**
```markdown
## Engine Specialists
- **Primary**: godot-specialist
- **Language/Code Specialist**: godot-csharp-specialist (all .cs files)
- **Shader Specialist**: godot-shader-specialist (.gdshader files, VisualShader resources)
- **UI Specialist**: godot-specialist (no dedicated UI specialist — primary covers all UI)
- **Additional Specialists**: godot-gdextension-specialist (GDExtension / native C++ bindings only)
- **Routing Notes**: Invoke primary for architecture decisions, ADR validation, and cross-cutting code review. Invoke C# specialist for code quality, [Signal] delegate patterns, [Export] attributes, .csproj management, and C#-specific Godot idioms. Invoke shader specialist for material design and shader code. Invoke GDExtension specialist only when native C++ plugins are involved.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.cs files) | godot-csharp-specialist |
| Shader / material files (.gdshader, VisualShader) | godot-shader-specialist |
| UI / screen files (Control nodes, CanvasLayer) | godot-specialist |
| Scene / prefab / level files (.tscn, .tres) | godot-specialist |
| Project config (.csproj, NuGet) | godot-csharp-specialist |
| Native extension / plugin files (.gdextension, C++) | godot-gdextension-specialist |
| General architecture review | godot-specialist |
```

**Both —— GDScript + C#:**
```markdown
## Engine Specialists
- **Primary**: godot-specialist
- **GDScript Specialist**: godot-gdscript-specialist (.gd files — gameplay/UI scripts)
- **C# Specialist**: godot-csharp-specialist (.cs files — performance-critical systems)
- **Shader Specialist**: godot-shader-specialist (.gdshader files, VisualShader resources)
- **UI Specialist**: godot-specialist (no dedicated UI specialist — primary covers all UI)
- **Additional Specialists**: godot-gdextension-specialist (GDExtension / native C++ bindings only)
- **Routing Notes**: Invoke primary for cross-language architecture decisions and which systems belong in which language. Invoke GDScript specialist for .gd files. Invoke C# specialist for .cs files and .csproj management. Prefer signals over direct cross-language method calls at the boundary.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.gd files) | godot-gdscript-specialist |
| Game code (.cs files) | godot-csharp-specialist |
| Cross-language boundary decisions | godot-specialist |
| Shader / material files (.gdshader, VisualShader) | godot-shader-specialist |
| UI / screen files (Control nodes, CanvasLayer) | godot-specialist |
| Scene / prefab / level files (.tscn, .tres) | godot-specialist |
| Project config (.csproj, NuGet) | godot-csharp-specialist |
| Native extension / plugin files (.gdextension, C++) | godot-gdextension-specialist |
| General architecture review | godot-specialist |
```
