# Vertical Slice Report: [Concept Name]
# 垂直切片报告：[概念名称]

> **Date**: [YYYY-MM-DD]
> **日期**：[YYYY-MM-DD]
> **Slice Duration**: [N days]
> **切片时长**：[N 天]
> **Target Scope**: 3–5 minutes of polished, continuous gameplay
> **目标范围**：3–5 分钟精打细磨的连续玩法
> **Source GDD**: design/gdd/game-concept.md
> **源 GDD**：design/gdd/game-concept.md

---

## Validation Question
## 验证问题

[The full game loop question this build was proving — both experience AND feasibility:
"Does a player, starting from nothing, experience [core fantasy] within [N] minutes,
without developer guidance — and can we build one such loop in [X] days at
representative quality?"]
[本次构建所要证明的完整游戏循环问题 — 既包括体验也包括可行性：
"玩家从零开始，是否能在 [N] 分钟内、无需开发者引导地体验到 [核心幻想] —
我们能否在 [X] 天内以代表性品质构建这样一个循环？"]

---

## Scope Built
## 已构建范围

[Systems implemented, art quality level, what was intentionally omitted.]
[已实现的系统、美术品质水平、有意省略的内容。]

**Systems included:**
**已包含系统：**
- [System 1]
- [系统 1]
- [System 2]
- [系统 2]
- [...]
- […]

**Art/audio quality level:** [Placeholder / Representative / Near-shipping]
**美术/音频品质水平：**[占位 / 代表性 / 接近发售]
**Shortcuts taken deliberately:** [List]
**有意采取的捷径：**[列表]
**What was cut from original scope:** [List]
**从原始范围中裁剪的内容：**[列表]

---

## Build Velocity Log
## 构建速度记录

[Day-by-day record of what was completed. This is your real production rate data —
use it in sprint planning.]
[每日完成情况记录。这是你真实的生产速率数据 — 用于冲刺规划。]

| Day | Completed |
|-----|-----------|
| Day 1 | [What was built] |
| Day 2 | [What was built] |
| Day 3 | [What was built] |
| ... | ... |

| 日期 | 已完成 |
|-----|-----------|
| 第 1 天 | [构建了什么] |
| 第 2 天 | [构建了什么] |
| 第 3 天 | [构建了什么] |
| … | … |

**Total elapsed:** [N days] for [scope summary]
**总耗时：**[N 天]，用于 [范围概要]
**Velocity estimate:** [N hours per equivalent scope unit — e.g., "1 day per combat
encounter, 0.5 days per UI screen"]
**速度估计：**[每个等效范围单位 N 小时 — 例如 "每次战斗遭遇 1 天，每个 UI 屏 0.5 天"]

---

## Playtest Results
## 试玩结果

| Attribute | Value |
|-----------|-------|
| Total sessions | [N] |
| Internal testers | [N] |
| External testers | [N — people who had not seen the game, if available] |
| Avg session length | [N minutes (target: [N] minutes)] |
| Time to first meaningful action | [N seconds (target: [N] seconds)] |

| 属性 | 值 |
|-----------|-------|
| 总场次 | [N] |
| 内部试玩者 | [N] |
| 外部试玩者 | [N — 若可用，未见过本游戏的人] |
| 平均会话时长 | [N 分钟（目标：[N] 分钟）] |
| 首次有意义行动时间 | [N 秒（目标：[N] 秒）] |

---

## Observations
## 观察

[Specific, non-opinion observations from playtest sessions. Quote testers where useful.]
[来自试玩场次的具体、非观点性观察。如有用可引用试玩者原话。]

**Where testers succeeded without guidance:**
**试玩者在无引导下成功的方面：**
- [...]
- […]

**Where testers were confused or stuck:**
**试玩者困惑或卡住的地方：**
- [...]
- […]

**Emotional reactions observed:**
**观察到的情感反应：**
- [...]
- […]

---

## Metrics
## 指标

| Metric | Target | Actual |
|--------|--------|--------|
| Time to first meaningful action | [N sec] | [N sec] |
| Session length | [N min] | [N min] |
| Critical fun blockers found | 0 | [N] |
| Pipeline blockers found | 0 | [N] |
| Architecture surprises | 0 | [N] |

| 指标 | 目标 | 实际 |
|--------|--------|--------|
| 首次有意义行动时间 | [N 秒] | [N 秒] |
| 会话时长 | [N 分钟] | [N 分钟] |
| 发现的关键乐趣阻塞 | 0 | [N] |
| 发现的管线阻塞 | 0 | [N] |
| 架构意外 | 0 | [N] |

**Feel assessment:** [Specific — "combat feedback weak; no impact sound on hit" not "felt rough"]
**手感评估：**[要具体 — "战斗反馈弱；命中无冲击音"而非 "感觉粗糙"]

---

## Recommendation: [PROCEED / PIVOT / KILL]
## 建议：[推进 / 转向 / 终止]

[One paragraph with evidence — reference the validation question directly. Did a
player experience the core fantasy within the target time, without developer guidance?
Can the team build at this quality on the projected schedule?]
[一段带证据的话 — 直接引用验证问题。玩家是否在目标时间内、无开发者引导下体验到核心幻想？
团队能否在预计排期内以此品质构建？]

---

## If Proceeding
## 若推进

**Production requirements** (what must change from slice to production):
**生产需求**（从切片到生产须改变什么）：
- [e.g., "Replace placeholder art with shipped assets"]
- [例如 "用发售资产替换占位美术"]
- [e.g., "Combat system needs 2 more weapon types"]
- [例如 "战斗系统需再加 2 种武器类型"]

**Architecture adjustments needed:**
**所需架构调整：**
- [ADR to update or create]
- [要更新或创建的 ADR]

**Sprint velocity estimate based on slice data:**
**基于切片数据的冲刺速度估计：**
- [e.g., "1 day per enemy type, 2 days per level section, 0.5 days per UI screen"]
- [例如 "每种敌人 1 天、每段关卡 2 天、每个 UI 屏 0.5 天"]

**Scope adjustments from original design:**
**相对原始设计的范围调整：**
- [What the slice revealed about the true production scope]
- [切片揭示的真实生产范围]

**Performance targets:** [Confirmed / Revised — list changes if revised]
**性能目标：**[已确认 / 已修订 — 若修订请列出变更]

**Playtest note:** Run `/playtest-report` to structure additional session data
before running `/gate-check pre-production`.
**试玩备注：**在运行 `/gate-check pre-production` 之前，运行 `/playtest-report` 以结构化额外会话数据。

**Next steps:**
**后续步骤：**
1. `/gate-check pre-production` — formally advance to Production
1. `/gate-check pre-production` — 正式推进至生产
2. `/create-epics layer:foundation` — plan Foundation layer epics
2. `/create-epics layer:foundation` — 规划基础层史诗
3. `/create-epics layer:core` — plan Core layer epics
3. `/create-epics layer:core` — 规划核心层史诗
4. `/sprint-plan` — use velocity data from this report in the estimate
4. `/sprint-plan` — 在估计中使用本报告的速度数据

---

## If Pivoting
## 若转向

[Which GDDs need revision and why — be specific about the failure mode observed.]
[哪些 GDD 需要修订以及原因 — 具体说明观察到的失败模式。]

**Systems requiring GDD revision:** [List]
**需要 GDD 修订的系统：**[列表]
**Architecture decisions to revisit:** [List — use `/architecture-decision` to update]
**需重新审视的架构决策：**[列表 — 使用 `/architecture-decision` 更新]
**Core loop change needed:** [What specifically to change]
**所需的核心循环变更：**[具体改什么]

**Next steps:**
**后续步骤：**
1. `/design-system [mechanic]` — revise affected GDDs
1. `/design-system [机制]` — 修订受影响的 GDD
2. `/architecture-decision [decision]` — address architecture issues
2. `/architecture-decision [决策]` — 处理架构问题
3. `/vertical-slice` — re-validate after revisions
3. `/vertical-slice` — 修订后重新验证

---

## If Killing
## 若终止

[Why the full game loop does not work at this quality level. What specifically
prevented the player from experiencing the core fantasy. What to do instead.]
[为何完整游戏循环在此品质水平下不可行。具体是什么阻止了玩家体验核心幻想。应转而做什么。]

**Next step:** `/brainstorm` to explore a new direction, or `/prototype [new-concept]`
to test a different concept cheaply before investing in another vertical slice.
**下一步：**`/brainstorm` 探索新方向，或 `/prototype [new-concept]` 在投入下一个垂直切片之前低成本测试不同概念。

---

## Lessons Learned
## 经验教训

- **What assumptions were broken by building to near-production quality?**
- **构建至接近生产品质打破了哪些假设？**
  [...]

- **What surprised us about the pipeline or architecture?**
- **管线或架构方面有何意外？**
  [...]

- **What would we change about the slice scope if we ran this again?**
- **若重新进行，我们会在切片范围上改变什么？**
  [...]

---

> *Vertical slice code location: `prototypes/[concept-name]-vertical-slice/`*
> *垂直切片代码位置：`prototypes/[concept-name]-vertical-slice/`*
> *This code is reference material only. Production implementation is written from scratch.*
> *此代码仅作参考资料。生产实现从零编写。*
> *Never import or refactor this code into production.*
> *切勿将此代码导入或重构进生产。*
