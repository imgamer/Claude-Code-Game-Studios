---
name: balance-check
description: "Analyzes game balance data files, formulas, and configuration to identify outliers, broken progressions, degenerate strategies, and economy imbalances. Use after modifying any balance-related data or design. Use when user says 'balance report', 'check game balance', 'run a balance check'."
argument-hint: "[system-name|path-to-data-file]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, AskUserQuestion
model: sonnet
agent: economy-designer
---
---
名称：balance-check
描述："分析游戏平衡数据文件、公式和配置以识别异常值、损坏的进度、退化的策略和经济失衡。在修改任何平衡相关数据或设计后使用。当用户说 'balance report'、'check game balance'、'run a balance check' 时使用。"
argument-hint："[system-name|path-to-data-file]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, AskUserQuestion
model：sonnet
agent：economy-designer
---

## Phase 1: Identify Balance Domain
## 阶段 1：识别平衡领域

Determine the balance domain from `$ARGUMENTS[0]`:
从 `$ARGUMENTS[0]` 确定平衡领域：

- **Combat** → weapon/ability DPS, time-to-kill, damage type interactions
- **Combat** → 武器/能力 DPS、击杀时间、伤害类型交互
- **Economy** → resource faucets/sinks, acquisition rates, item pricing
- **Economy** → 资源水龙头/水槽、获取速率、物品定价
- **Progression** → XP/power curves, dead zones, power spikes
- **Progression** → 经验/力量曲线、死区、力量尖峰
- **Loot** → rarity distribution, pity timers, inventory pressure
- **Loot** → 稀有度分布、保底计时器、库存压力
- **File path given** → load that file directly and infer domain from content
- **给定文件路径** → 直接加载该文件并从内容推断领域

If no argument, ask the user which system to check.
若无参数，询问用户要检查哪个系统。

---

## Phase 2: Read Data Files
## 阶段 2：读取数据文件

Read relevant files from `assets/data/` and `design/balance/` for the identified domain.
Note every file read — they will appear in the Data Sources section of the report.
从 `assets/data/` 和 `design/balance/` 读取已识别领域相关文件。
注明每个读取的文件 —— 它们将出现在报告的数据来源章节。

---

## Phase 3: Read Design Document
## 阶段 3：读取设计文档

Read the GDD for the system from `design/gdd/` to understand intended design targets,
tuning knobs, and expected value ranges. This is the baseline for "correct" behaviour.
从 `design/gdd/` 读取该系统的 GDD 以理解预期的设计目标、调参旋钮和预期值范围。这是"正确"行为的基线。

---

## Phase 4: Perform Analysis
## 阶段 4：执行分析

Run domain-specific checks:
运行领域特定检查：

**Combat balance:**
**战斗平衡：**
- Calculate DPS for all weapons/abilities at each power tier
- 计算每个力量等级所有武器/能力的 DPS
- Check time-to-kill at each tier
- 检查每个等级的击杀时间
- Identify any options that dominate all others (strictly better)
- 识别任何主导所有其他选项的选项（严格更好）
- Check if defensive options can create unkillable states
- 检查防御选项是否能创造不可杀状态
- Verify damage type/resistance interactions are balanced
- 验证伤害类型/抗性交互是否平衡

**Economy balance:**
**经济平衡：**
- Map all resource faucets and sinks with flow rates
- 用流速映射所有资源水龙头和水槽
- Project resource accumulation over time
- 预测资源随时间累积
- Check for infinite resource loops
- 检查无限资源循环
- Verify gold sinks scale with gold generation
- 验证金币水槽与金币生成成比例
- Check if any items are never worth purchasing
- 检查是否有物品永远不值得购买

**Progression balance:**
**进度平衡：**
- Plot the XP curve and power curve
- 绘制经验曲线和力量曲线
- Check for dead zones (no meaningful progression for too long)
- 检查死区（长时间无有意义进度）
- Check for power spikes (sudden jumps in capability)
- 检查力量尖峰（能力突然跳跃）
- Verify content gates align with expected player power
- 验证内容门禁与预期玩家力量对齐
- Check if skip/grind strategies break intended pacing
- 检查跳过/刷取策略是否破坏预期节奏

**Loot balance:**
**战利品平衡：**
- Calculate expected time to acquire each rarity tier
- 计算获取每个稀有度等级的预期时间
- Check pity timer math
- 检查保底计时器数学
- Verify no loot is strictly useless at any stage
- 验证任何阶段没有战利品完全无用
- Check inventory pressure vs acquisition rate
- 检查库存压力 vs 获取速率

---

## Phase 5: Output the Analysis
## 阶段 5：输出分析

```
## Balance Check: [System Name]
```
```
## 平衡检查：[系统名]
```

```
### Data Sources Analyzed
- [List of files read]
```
```
### 已分析数据来源
- [已读文件列表]
```

```
### Health Summary: [HEALTHY / CONCERNS / CRITICAL ISSUES]
```
```
### 健康摘要：[HEALTHY / CONCERNS / CRITICAL ISSUES]
```

```
### Outliers Detected
| Item/Value | Expected Range | Actual | Issue |
|-----------|---------------|--------|-------|
```
```
### 检测到的异常值
| 物品/值 | 预期范围 | 实际 | 问题 |
|-----------|---------------|--------|-------|
```

```
### Degenerate Strategies Found
- [Strategy description and why it is problematic]
```
```
### 发现的退化策略
- [策略描述及为何有问题]
```

```
### Progression Analysis
[Graph description or table showing progression curve health]
```
```
### 进度分析
[显示进度曲线健康度的图描述或表]
```

```
### Recommendations
| Priority | Issue | Suggested Fix | Impact |
|----------|-------|--------------|--------|
```
```
### 建议
| 优先级 | 问题 | 建议修复 | 影响 |
|----------|-------|--------------|--------|
```

```
### Values That Need Attention
[Specific values with suggested adjustments and rationale]
```
```
### 需要关注的值
[带建议调整和理由的具体值]
```

---

## Phase 6: Fix & Verify Cycle
## 阶段 6：修复和验证循环

After presenting the report, use `AskUserQuestion`:
呈现报告后，使用 `AskUserQuestion`：
- Prompt: "Balance check complete. What would you like to do next?"
- 提示："平衡检查完成。您接下来想做什么？"
- Options:
- 选项：
  - `[A] Fix highest-priority issue now — walk me through it`
  - `[A] 现在修复最高优先级问题 —— 引导我完成`
  - `[B] Save report to design/balance/balance-check-[system]-[date].md`
  - `[B] 将报告保存到 design/balance/balance-check-[system]-[date].md`
  - `[C] Stop here — I'll review the findings manually`
  - `[C] 在此停止 —— 我将手动评审发现`

If [A]:
若 [A]：
- Ask which issue to address first (refer to the Recommendations table by priority row)
- 询问先解决哪个问题（按优先级行参考建议表）
- Guide the user to update the relevant data file in `assets/data/` or formula in `design/balance/`
- 引导用户更新 `assets/data/` 中的相关数据文件或 `design/balance/` 中的公式
- After each fix, offer to re-run the relevant balance checks to verify no new outliers were introduced
- 每次修复后，提供重新运行相关平衡检查以验证未引入新异常值
- If the fix changes a tuning knob defined in a GDD or referenced by an ADR, remind the user:
  - 若修复更改了 GDD 中定义或 ADR 引用的调参旋钮，提醒用户：
  > "This value is defined in a design document. Run `/propagate-design-change [path]` on the affected GDD to find downstream impacts before committing."
  > "此值在设计文档中定义。在提交前对受影响的 GDD 运行 `/propagate-design-change [path]` 以查找下游影响。"

If [B]:
若 [B]：
- Write the report to `design/balance/balance-check-[system]-[date].md` (create the directory if needed). Use the current date for [date] in YYYY-MM-DD format.
- 将报告写入 `design/balance/balance-check-[system]-[date].md`（若需要则创建目录）。使用当前日期作为 [date]，格式为 YYYY-MM-DD。
- Confirm the file was written, then end with: "Re-run `/balance-check` after fixes to verify."
- 确认文件已写入，然后以："修复后重新运行 `/balance-check` 验证。" 结束。

If [C]:
若 [C]：
- Summarize open issues and end with: "Re-run `/balance-check` after fixes to verify."
- 总结未决问题并以："修复后重新运行 `/balance-check` 验证。" 结束。
