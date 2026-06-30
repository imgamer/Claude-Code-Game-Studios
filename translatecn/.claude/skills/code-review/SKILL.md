---
name: code-review
description: "Performs an architectural and quality code review on a specified file or set of files. Checks for coding standard compliance, architectural pattern adherence, SOLID principles, testability, and performance concerns."
argument-hint: "[path-to-file-or-directory]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Task, AskUserQuestion
model: sonnet
agent: lead-programmer
---
---
名称：code-review
描述："对指定文件或文件集执行架构和质量代码评审。检查编码标准合规性、架构模式遵循、SOLID 原则、可测试性和性能问题。"
argument-hint："[path-to-file-or-directory]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Bash, Task, AskUserQuestion
model：sonnet
agent：lead-programmer
---

## Phase 1: Load Target Files
## 阶段 1：加载目标文件

Read the target file(s) in full. Read CLAUDE.md for project coding standards.
完整读取目标文件。读取 CLAUDE.md 以获取项目编码标准。

---

## Phase 2: Identify Engine Specialists
## 阶段 2：识别引擎专家

Read `.claude/docs/technical-preferences.md`, section `## Engine Specialists`. Note:
读取 `.claude/docs/technical-preferences.md` 的 `## Engine Specialists` 章节。注明：

- The **Primary** specialist (used for architecture and broad engine concerns)
- **Primary** 专家（用于架构和广泛引擎问题）
- The **Language/Code Specialist** (used when reviewing the project's primary language files)
- **Language/Code Specialist**（评审项目主要语言文件时使用）
- The **Shader Specialist** (used when reviewing shader files)
- **Shader Specialist**（评审着色器文件时使用）
- The **UI Specialist** (used when reviewing UI code)
- **UI Specialist**（评审 UI 代码时使用）

If the section reads `[TO BE CONFIGURED]`, no engine is pinned — skip engine specialist steps.
若该章节显示 `[TO BE CONFIGURED]`，则未固定引擎 —— 跳过引擎专家步骤。

---

## Phase 3: ADR Compliance Check
## 阶段 3：ADR 合规检查

**Argument:** `/code-review [file(s)]` may optionally include a story file path as the last argument (e.g., `/code-review src/combat/attack.gd production/epics/combat/story-001.md`). If a story path is provided, read it to extract the governing ADR reference.
**参数：** `/code-review [file(s)]` 可选地包含故事文件路径作为最后一个参数（例如 `/code-review src/combat/attack.gd production/epics/combat/story-001.md`）。若提供故事路径，读取它以提取治理 ADR 引用。

Search for ADR references in, in priority order:
按以下优先级顺序搜索 ADR 引用：
1. The story file (if provided as argument)
1. 故事文件（若作为参数提供）
2. Header comments at the top of the implementation files
2. 实施文件顶部的头注释
3. Commit messages referencing these files (`git log --oneline -- [file]`)
3. 引用这些文件的提交消息（`git log --oneline -- [file]`）

Look for patterns like `ADR-NNN` or `docs/architecture/ADR-`.
查找 `ADR-NNN` 或 `docs/architecture/ADR-` 等模式。

If no ADR references found, note: "No ADR references found — ADR compliance check skipped. For full ADR compliance review, provide the story path: `/code-review [files] [story-path]`."
若未找到 ADR 引用，注明："未找到 ADR 引用 —— ADR 合规检查已跳过。要进行完整 ADR 合规评审，请提供故事路径：`/code-review [files] [story-path]`。"

For each referenced ADR: read the file, extract the **Decision** and **Consequences** sections, then classify any deviation:
对每个引用的 ADR：读取文件，提取 **Decision** 和 **Consequences** 章节，然后对任何偏差分类：

- **ARCHITECTURAL VIOLATION** (BLOCKING): Uses a pattern explicitly rejected in the ADR
- **ARCHITECTURAL VIOLATION**（阻断）：使用 ADR 中明确拒绝的模式
- **ADR DRIFT** (WARNING): Meaningfully diverges from the chosen approach without using a forbidden pattern
- **ADR DRIFT**（警告）：从所选方法明显偏离但未使用禁止模式
- **MINOR DEVIATION** (INFO): Small difference from ADR guidance that doesn't affect overall architecture
- **MINOR DEVIATION**（信息）：与 ADR 指导的小差异，不影响整体架构

---

## Phase 4: Standards Compliance
## 阶段 4：标准合规

Identify the system category (engine, gameplay, AI, networking, UI, tools) and evaluate:
识别系统类别（引擎、游戏玩法、AI、网络、UI、工具）并评估：

- [ ] Public methods and classes have doc comments
- [ ] 公共方法和类有文档注释
- [ ] Cyclomatic complexity under 10 per method
- [ ] 每个方法的圈复杂度低于 10
- [ ] No method exceeds 40 lines (excluding data declarations)
- [ ] 没有方法超过 40 行（不包括数据声明）
- [ ] Dependencies are injected (no static singletons for game state)
- [ ] 依赖已注入（游戏状态无静态单例）
- [ ] Configuration values loaded from data files
- [ ] 配置值从数据文件加载
- [ ] Systems expose interfaces (not concrete class dependencies)
- [ ] 系统暴露接口（而非具体类依赖）

---

## Phase 5: Architecture and SOLID
## 阶段 5：架构和 SOLID

**Architecture:**
**架构：**
- [ ] Correct dependency direction (engine <- gameplay, not reverse)
- [ ] 正确的依赖方向（引擎 <- 游戏玩法，而非反向）
- [ ] No circular dependencies between modules
- [ ] 模块间无循环依赖
- [ ] Proper layer separation (UI does not own game state)
- [ ] 适当的层分离（UI 不拥有游戏状态）
- [ ] Events/signals used for cross-system communication
- [ ] 事件/信号用于跨系统通信
- [ ] Consistent with established patterns in the codebase
- [ ] 与代码库中已建立的模式一致

**SOLID:**
**SOLID：**
- [ ] Single Responsibility: Each class has one reason to change
- [ ] 单一职责：每个类只有一个变更原因
- [ ] Open/Closed: Extendable without modification
- [ ] 开闭原则：可扩展而不修改
- [ ] Liskov Substitution: Subtypes substitutable for base types
- [ ] 里氏替换：子类型可替换基类型
- [ ] Interface Segregation: No fat interfaces
- [ ] 接口隔离：无臃肿接口
- [ ] Dependency Inversion: Depends on abstractions, not concretions
- [ ] 依赖倒置：依赖抽象而非具体

---

## Phase 6: Game-Specific Concerns
## 阶段 6：游戏特定关注点

- [ ] Frame-rate independence (delta time usage)
- [ ] 帧率独立（delta 时间使用）
- [ ] No allocations in hot paths (update loops)
- [ ] 热路径中无分配（更新循环）
- [ ] Proper null/empty state handling
- [ ] 适当的 null/空状态处理
- [ ] Thread safety where required
- [ ] 所需处的线程安全
- [ ] Resource cleanup (no leaks)
- [ ] 资源清理（无泄漏）

---

## Phase 7: Specialist Reviews (Parallel)
## 阶段 7：专家评审（并行）

Spawn all applicable specialists simultaneously via Task — do not wait for one before starting the next.
通过 Task 同时派生所有适用的专家 —— 不要在启动下一个前等待前一个完成。

### Engine Specialists
### 引擎专家

If an engine is configured, determine which specialist applies to each file and spawn in parallel:
若已配置引擎，确定哪个专家适用于每个文件并并行派生：

- Primary language files (`.gd`, `.cs`, `.cpp`) → Language/Code Specialist
- 主要语言文件（`.gd`、`.cs`、`.cpp`）→ Language/Code Specialist
- Shader files (`.gdshader`, `.hlsl`, shader graph) → Shader Specialist
- 着色器文件（`.gdshader`、`.hlsl`、shader graph）→ Shader Specialist
- UI screen/widget code → UI Specialist
- UI 屏幕/部件代码 → UI Specialist
- Cross-cutting or unclear → Primary Specialist
- 跨领域或不明确 → Primary Specialist

Also spawn the **Primary Specialist** for any file touching engine architecture (scene structure, node hierarchy, lifecycle hooks).
对于触及引擎架构的任何文件（场景结构、节点层次结构、生命周期钩子），也派生 **Primary Specialist**。

### QA Testability Review
### QA 可测试性评审

For Logic and Integration stories, also spawn `qa-tester` via Task in parallel with the engine specialists. Pass:
对于逻辑和集成故事，也与引擎专家并行通过 Task 派生 `qa-tester`。传递：
- The implementation files being reviewed
- 正在评审的实施文件
- The story's `## QA Test Cases` section (the pre-written test specs from qa-lead)
- 故事的 `## QA Test Cases` 章节（来自 qa-lead 的预写测试规范）
- The story's `## Acceptance Criteria`
- 故事的 `## Acceptance Criteria`

Ask the qa-tester to evaluate:
要求 qa-tester 评估：
- [ ] Are all test hooks and interfaces exposed (not hidden behind private/internal access)?
- [ ] 所有测试钩子和接口是否已暴露（而非隐藏在 private/internal 访问后）？
- [ ] Do the QA test cases from the story's `## QA Test Cases` section map to testable code paths?
- [ ] 故事 `## QA Test Cases` 章节中的 QA 测试用例是否映射到可测试的代码路径？
- [ ] Are any acceptance criteria untestable as implemented (e.g., hardcoded values, no seam for injection)?
- [ ] 任何验收标准是否如实施那样不可测试（例如硬编码值、无注入接缝）？
- [ ] Does the implementation introduce any new edge cases not covered by the existing QA test cases?
- [ ] 实施是否引入了现有 QA 测试用例未涵盖的新边界情况？
- [ ] Are there any observable side effects that should have a test but don't?
- [ ] 是否有任何应有测试但没有的可观察副作用？

For Visual/Feel and UI stories: qa-tester reviews whether the manual verification steps in `## QA Test Cases` are achievable with the implementation as written — e.g., "is the state the manual checker needs to reach actually reachable?"
对于 Visual/Feel 和 UI 故事：qa-tester 评审 `## QA Test Cases` 中的手动验证步骤是否如实施所写可达成 —— 例如，"手动检查员需要达到的状态是否实际可达？"

Collect all specialist findings before producing output.
收集所有专家发现后再产生输出。

---

## Phase 8: Output Review
## 阶段 8：输出评审

```
## Code Review: [File/System Name]
```
```
## 代码评审：[文件/系统名]
```

```
### Engine Specialist Findings: [N/A — no engine configured / CLEAN / ISSUES FOUND]
[Findings from engine specialist(s), or "No engine configured." if skipped]
```
```
### 引擎专家发现：[N/A —— 未配置引擎 / CLEAN / 发现问题]
[来自引擎专家的发现，或若跳过则为 "No engine configured."]
```

```
### Testability: [N/A — Visual/Feel or Config story / TESTABLE / GAPS / BLOCKING]
[qa-tester findings: test hooks, coverage gaps, untestable paths, new edge cases]
[If BLOCKING: implementation must expose [X] before tests in ## QA Test Cases can run]
```
```
### 可测试性：[N/A —— Visual/Feel 或 Config 故事 / TESTABLE / GAPS / BLOCKING]
[qa-tester 发现：测试钩子、覆盖缺口、不可测试路径、新边界情况]
[若 BLOCKING：实施必须暴露 [X] 才能运行 ## QA Test Cases 中的测试]
```

```
### ADR Compliance: [NO ADRS FOUND / COMPLIANT / DRIFT / VIOLATION]
[List each ADR checked, result, and any deviations with severity]
```
```
### ADR 合规：[未找到 ADR / COMPLIANT / DRIFT / VIOLATION]
[列出每个检查的 ADR、结果及任何带严重性的偏差]
```

```
### Standards Compliance: [X/6 passing]
[List failures with line references]
```
```
### 标准合规：[X/6 通过]
[列出失败及行引用]
```

```
### Architecture: [CLEAN / MINOR ISSUES / VIOLATIONS FOUND]
[List specific architectural concerns]
```
```
### 架构：[CLEAN / 次要问题 / 发现违规]
[列出具体架构疑虑]
```

```
### SOLID: [COMPLIANT / ISSUES FOUND]
[List specific violations]
```
```
### SOLID：[COMPLIANT / 发现问题]
[列出具体违规]
```

```
### Game-Specific Concerns
[List game development specific issues]
```
```
### 游戏特定关注点
[列出游戏开发特定问题]
```

```
### Positive Observations
[What is done well -- always include this section]
```
```
### 正面观察
[做得好的地方 —— 始终包含此章节]
```

```
### Required Changes
[Must-fix items before approval — ARCHITECTURAL VIOLATIONs always appear here]
```
```
### 必需变更
[批准前必须修复的项 —— ARCHITECTURAL VIOLATION 始终出现在这里]
```

```
### Suggestions
[Nice-to-have improvements]
```
```
### 建议
[可有可无的改进]
```

```
### Verdict: [APPROVED / APPROVED WITH SUGGESTIONS / CHANGES REQUIRED]
```
```
### 裁定：[APPROVED / APPROVED WITH SUGGESTIONS / CHANGES REQUIRED]
```

This skill is read-only — no files are written.
此技能为只读 —— 不写入任何文件。

---

## Phase 9: Next Steps
## 阶段 9：后续步骤

Use `AskUserQuestion`:
使用 `AskUserQuestion`：
- Prompt: "Code review complete — verdict: [APPROVED / CHANGES REQUIRED / MAJOR REVISION]. How would you like to proceed?"
- 提示："代码评审完成 —— 裁定：[APPROVED / CHANGES REQUIRED / MAJOR REVISION]。您希望如何进行？"
- Options (adjust based on verdict):
- 选项（基于裁定调整）：
  - If APPROVED:
  - 若 APPROVED：
    - `[A] Run /story-done to mark the story complete`
    - `[A] 运行 /story-done 标记故事完成`
    - `[B] Stop here`
    - `[B] 在此停止`
  - If CHANGES REQUIRED or MAJOR REVISION:
  - 若 CHANGES REQUIRED 或 MAJOR REVISION：
    - `[A] Fix the issues and re-run /code-review`
    - `[A] 修复问题并重新运行 /code-review`
    - `[B] Run /story-done anyway with noted exceptions`
    - `[B] 仍运行 /story-done 并注明例外`
    - `[C] Stop here`
    - `[C] 在此停止`

If an ARCHITECTURAL VIOLATION is found:
若发现 ARCHITECTURAL VIOLATION：
- If the violation contradicts an **existing ADR**: fix the implementation to comply with `docs/architecture/[adr-file].md`. If the design has legitimately changed, run `/architecture-decision` to formally *revise* the existing ADR — do not create a competing one.
- 若违规与**现有 ADR** 矛盾：修复实施以遵守 `docs/architecture/[adr-file].md`。若设计已合理变更，运行 `/architecture-decision` 正式*修订*现有 ADR —— 不要创建竞争性 ADR。
- If **no ADR exists** for the pattern that was violated: run `/architecture-decision` to document the correct approach before fixing the code.
- 若被违规的模式**无 ADR 存在**：运行 `/architecture-decision` 在修复代码前记录正确方法。
