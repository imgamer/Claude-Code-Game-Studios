---
name: tech-debt
description: "Track, categorize, and prioritize technical debt across the codebase. Scans for debt indicators, maintains a debt register, and recommends repayment scheduling."
argument-hint: "[scan|add|prioritize|report]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
---
---
名称：tech-debt
描述："跨代码库跟踪、分类和优先级排序技术债务。扫描债务指标，维护债务登记册，并建议偿还计划。"
argument-hint："[scan|add|prioritize|report]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, AskUserQuestion
model：sonnet
---

## Phase 1: Parse Subcommand
## 阶段 1：解析子命令

Determine the mode from the argument:
从参数确定模式：

- `scan` — Scan the codebase for tech debt indicators
- `scan` —— 扫描代码库查找技术债务指标
- `add` — Add a new tech debt entry manually
- `add` —— 手动添加新的技术债务条目
- `prioritize` — Re-prioritize the existing debt register
- `prioritize` —— 重新对现有债务登记册排序优先级
- `report` — Generate a summary report of current debt status
- `report` —— 生成当前债务状态的摘要报告

If no subcommand is provided, output usage and stop. Verdict: **FAIL** — missing required subcommand.
若未提供子命令，输出用法并停止。裁定：**FAIL** —— 缺少必需子命令。

---

## Phase 2A: Scan Mode
## 阶段 2A：扫描模式

Search the codebase for debt indicators:
搜索代码库查找债务指标：

- `TODO` comments (count and categorize)
- `TODO` 注释（计数和分类）
- `FIXME` comments (these are bugs disguised as debt)
- `FIXME` 注释（这些是伪装成债务的 bug）
- `HACK` comments (workarounds that need proper solutions)
- `HACK` 注释（需要正确解决方案的变通方法）
- `@deprecated` markers
- `@deprecated` 标记
- Duplicated code blocks (similar patterns in multiple files)
- 重复代码块（多文件中的相似模式）
- Files over 500 lines (potential god objects)
- 超过 500 行的文件（潜在上帝对象）
- Functions over 50 lines (potential complexity)
- 超过 50 行的函数（潜在复杂性）

Categorize each finding:
对每个发现分类：

- **Architecture Debt**: Wrong abstractions, missing patterns, coupling issues
- **架构债务**：错误抽象、缺失模式、耦合问题
- **Code Quality Debt**: Duplication, complexity, naming, missing types
- **代码质量债务**：重复、复杂性、命名、缺失类型
- **Test Debt**: Missing tests, flaky tests, untested edge cases
- **测试债务**：缺失测试、不稳定测试、未测试的边界情况
- **Documentation Debt**: Missing docs, outdated docs, undocumented APIs
- **文档债务**：缺失文档、过时文档、未记录的 API
- **Dependency Debt**: Outdated packages, deprecated APIs, version conflicts
- **依赖债务**：过时包、已弃用 API、版本冲突
- **Performance Debt**: Known slow paths, unoptimized queries, memory issues
- **性能债务**：已知慢路径、未优化查询、内存问题

Present the findings to the user.
向用户呈现发现。

Ask: "May I write these findings to `docs/tech-debt-register.md`?"
询问："我可以将这些发现写入 `docs/tech-debt-register.md` 吗？"

If yes, update the register (append new entries, do not overwrite existing ones). Verdict: **COMPLETE** — scan findings written to register.
若是，更新登记册（追加新条目，不覆盖现有条目）。裁定：**COMPLETE** —— 扫描发现已写入登记册。

If no, stop here. Verdict: **BLOCKED** — user declined write.
若否，在此停止。裁定：**BLOCKED** —— 用户拒绝写入。

---

## Phase 2B: Add Mode
## 阶段 2B：添加模式

Ask the user for the description, affected files, and impact if left unfixed (plain text prompts).
询问用户描述、受影响文件以及若不修复的影响（纯文本提示）。

Then use `AskUserQuestion` to collect the **category**:
然后使用 `AskUserQuestion` 收集**类别**：
- Prompt: "What category does this tech debt belong to?"
- 提示："此技术债务属于哪个类别？"
- Options:
- 选项：
  - `[A] Architecture Debt — wrong abstractions, missing patterns, coupling issues`
  - `[A] 架构债务 —— 错误抽象、缺失模式、耦合问题`
  - `[B] Code Quality Debt — duplication, complexity, naming, missing types`
  - `[B] 代码质量债务 —— 重复、复杂性、命名、缺失类型`
  - `[C] Test Debt — missing tests, flaky tests, untested edge cases`
  - `[C] 测试债务 —— 缺失测试、不稳定测试、未测试的边界情况`
  - `[D] Documentation Debt — missing/outdated docs, undocumented APIs`
  - `[D] 文档债务 —— 缺失/过时文档、未记录的 API`
  - `[E] Dependency Debt — outdated packages, deprecated APIs, version conflicts`
  - `[E] 依赖债务 —— 过时包、已弃用 API、版本冲突`
  - `[F] Performance Debt — known slow paths, memory issues, unoptimized queries`
  - `[F] 性能债务 —— 已知慢路径、内存问题、未优化查询`

Then use `AskUserQuestion` to collect the **estimated fix effort**:
然后使用 `AskUserQuestion` 收集**估计修复工作量**：
- Prompt: "What is the estimated effort to fix this item?"
- 提示："修复此项的估计工作量是多少？"
- Options:
- 选项：
  - `[A] S — Small (under 1 day)`
  - `[A] S —— 小（不到 1 天）`
  - `[B] M — Medium (1–3 days)`
  - `[B] M —— 中（1–3 天）`
  - `[C] L — Large (3–7 days)`
  - `[C] L —— 大（3–7 天）`
  - `[D] XL — Extra Large (over 1 week)`
  - `[D] XL —— 超大（超过 1 周）`

Present the complete new entry to the user.
向用户呈现完整的新条目。

Ask: "May I append this entry to `docs/tech-debt-register.md`?"
询问："我可以将此条目追加到 `docs/tech-debt-register.md` 吗？"

If yes, append the entry. Verdict: **COMPLETE** — entry added to register.
若是，追加条目。裁定：**COMPLETE** —— 条目已添加到登记册。

If no, stop here. Verdict: **BLOCKED** — user declined write.
若否，在此停止。裁定：**BLOCKED** —— 用户拒绝写入。

---

## Phase 2C: Prioritize Mode
## 阶段 2C：优先级排序模式

Read the debt register at `docs/tech-debt-register.md`.
读取 `docs/tech-debt-register.md` 中的债务登记册。

Score each item by: `(impact_if_unfixed × frequency_of_encounter) / fix_effort`
按 `(未修复影响 × 遭遇频率) / 修复工作量` 对每项评分

Re-sort the register by priority score and recommend which items to include in the next sprint.
按优先级分数重新排序登记册，并建议在下一个冲刺中包含哪些项。

Present the re-prioritized register to the user.
向用户呈现重新排序后的登记册。

Ask: "May I write the re-prioritized register back to `docs/tech-debt-register.md`?"
询问："我可以将重新排序后的登记册写回 `docs/tech-debt-register.md` 吗？"

If yes, write the updated file. Verdict: **COMPLETE** — register re-prioritized and saved.
若是，写入更新后的文件。裁定：**COMPLETE** —— 登记册已重新排序并保存。

If no, stop here. Verdict: **BLOCKED** — user declined write.
若否，在此停止。裁定：**BLOCKED** —— 用户拒绝写入。

---

## Phase 2D: Report Mode
## 阶段 2D：报告模式

Read the debt register. Generate summary statistics:
读取债务登记册。生成摘要统计：

- Total items by category
- 按类别总条目数
- Total estimated fix effort
- 估计总修复工作量
- Items added vs resolved since last report
- 自上次报告以来添加 vs 解决的条目
- Trending direction (growing / stable / shrinking)
- 趋势方向（增长 / 稳定 / 缩减）

Flag any items that have been in the register for more than 3 sprints.
标记登记册中超过 3 个冲刺的任何条目。

Output the report to the user. This mode is read-only — no files are written. Verdict: **COMPLETE** — debt report generated.
向用户输出报告。此模式为只读 —— 不写入文件。裁定：**COMPLETE** —— 债务报告已生成。

---

## Phase 3: Next Steps
## 阶段 3：后续步骤

- Run `/sprint-plan` to schedule high-priority debt items into the next sprint.
- 运行 `/sprint-plan` 将高优先级债务项安排到下一个冲刺。
- Run `/tech-debt report` at the start of each sprint to track debt trends over time.
- 在每个冲刺开始时运行 `/tech-debt report` 以跟踪债务随时间的趋势。

### Debt Register Format
### 债务登记册格式

```markdown
## Technical Debt Register
Last updated: [Date]
Total items: [N] | Estimated total effort: [T-shirt sizes summed]

| ID | Category | Description | Files | Effort | Impact | Priority | Added | Sprint |
|----|----------|-------------|-------|--------|--------|----------|-------|--------|
| TD-001 | [Cat] | [Description] | [files] | [S/M/L/XL] | [Low/Med/High/Critical] | [Score] | [Date] | [Sprint to fix or "Backlog"] |
```
```markdown
## 技术债务登记册
最后更新：[日期]
总条目：[N] | 估计总工作量：[T 恤尺寸求和]

| ID | 类别 | 描述 | 文件 | 工作量 | 影响 | 优先级 | 添加日期 | 冲刺 |
|----|----------|-------------|-------|--------|--------|----------|-------|--------|
| TD-001 | [类别] | [描述] | [文件] | [S/M/L/XL] | [Low/Med/High/Critical] | [分数] | [日期] | [修复冲刺或 "Backlog"] |
```

### Rules
### 规则
- Tech debt is not inherently bad — it is a tool. The register tracks conscious decisions.
- 技术债务本质上并不坏 —— 它是工具。登记册跟踪有意识的决策。
- Every debt entry must explain WHY it was accepted (deadline, prototype, missing info)
- 每个债务条目必须解释为何接受它（截止日期、原型、信息缺失）
- "Scan" should run at least once per sprint to catch new debt
- "扫描"应至少每个冲刺运行一次以捕获新债务
- Items older than 3 sprints without action should either be fixed or consciously accepted with a documented reason
- 超过 3 个冲刺无行动的条目应被修复或有意识地接受并附记录的原因
