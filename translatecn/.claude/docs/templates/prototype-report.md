# Concept Prototype Report: [Concept Name]
# 概念原型报告：[概念名称]

> **Date**: [YYYY-MM-DD]
> **Prototype Path**: [HTML / Engine / Paper]
> **Concept File**: design/gdd/game-concept.md (if exists)
> **日期**：[YYYY-MM-DD]
> **原型路径**：[HTML / 引擎 / 纸面]
> **概念文件**：design/gdd/game-concept.md（如存在）

---

## Hypothesis
## 假设

[The falsifiable hypothesis this prototype set out to test:
"If the player [does X], they will feel [Y] — evidenced by [measurable signal Z]."]
[此原型设定测试的可证伪假设：
"如玩家 [做 X]，他们将感觉 [Y] — 由 [可测量信号 Z] 证明。"]

---

## Riskiest Assumption Tested
## 已测试的最风险假设

[What was identified as the biggest risk in the concept, and whether it proved out.]
[什么被识别为概念中最大风险，以及它是否证明成立。]

---

## Approach
## 方法

[What was built, how long it took, what shortcuts were taken deliberately.]
[构建了什么、花了多久、故意采取了什么捷径。]

**Path chosen:** [HTML / Engine / Paper]
**Reason for path:** [Why this path was appropriate for this hypothesis]
**所选路径：**[HTML / 引擎 / 纸面]
**路径原因：**[为何此路径适合此假设]

**Shortcuts taken (intentional):**
- [e.g., hardcoded values, placeholder art, no menus, etc.]
**采取的捷径（有意）：**
- [例如，硬编码值、占位美术、无菜单等]

---

## Result
## 结果

[What actually happened — specific observations, not opinions. Quote playtesters
directly where possible.]
[实际发生什么 — 具体观察，非意见。如可能直接引用试玩者。]

---

## Metrics
## 指标

| Metric | Value |
|--------|-------|
| Path used | [HTML / Engine / Paper] |
| Iterations to playable | [N — Engine path only; N/A otherwise] |
| Prototype duration | [e.g., 4 hours] |
| Playtesters | [N internal / N external] |
| Feel assessment | [Specific — "response felt sluggish at 200ms" not "felt bad"] |
| Hypothesis verdict | [CONFIRMED / PARTIALLY CONFIRMED / REFUTED] |
| 指标 | 值 |
|--------|-------|
| 所用路径 | [HTML / 引擎 / 纸面] |
| 到可玩的迭代 | [N — 仅引擎路径；否则 N/A] |
| 原型时长 | [例如，4 小时] |
| 试玩者 | [N 内部 / N 外部] |
| 手感评估 | [具体 — "200ms 时响应感觉迟缓" 而非 "感觉差"] |
| 假设裁决 | [已确认 / 部分确认 / 被反驳] |

---

## Recommendation: [PROCEED / PIVOT / KILL]
## 建议：[继续 / 转向 / 终止]

[One paragraph explaining the recommendation with evidence from the result above.]
[一段用上述结果的证据解释建议。]

---

## If Proceeding
## 如继续

[What the prototype revealed that should directly inform GDD writing:]
[原型揭示的应直接指导 GDD 编写的内容：]

- **Core tuning values discovered:** [e.g., "jump height of 3.5 units felt best"]
- **Assumptions confirmed:** [What the concept doc assumed that proved true]
- **Assumptions disproved:** [What the concept doc assumed that proved wrong]
- **Emergent mechanics:** [Behaviors that appeared during testing worth formalizing]
- **发现的核心调优值：**[例如，"3.5 单位跳跃高度感觉最佳"]
- **已确认假设：**[概念文档假设为真之事]
- **被反驳假设：**[概念文档假设为错之事]
- **涌现机制：**[测试中出现值得形式化的行为]

> Note: If HTML path was used and feel is uncertain, consider an engine prototype
> targeting feel specifically before committing to GDDs.
> 注：如使用 HTML 路径且手感不确定，考虑在投入 GDD 之前专门针对手感的引擎原型。

**Next steps:**
1. `/design-review design/gdd/game-concept.md`
2. `/gate-check`
3. `/map-systems`
4. `/design-system [mechanic]` (use learnings in Tuning Knobs and Formulas sections)
**后续步骤：**
1. `/design-review design/gdd/game-concept.md`
2. `/gate-check`
3. `/map-systems`
4. `/design-system [mechanic]`（在调优旋钮与公式节中使用学习）

---

## If Pivoting
## 如转向

[What alternative direction the results suggest — what felt almost right and what
to adjust. Be specific about what to change, not just that something needs changing.]
[结果暗示什么替代方向 — 什么感觉几乎对以及调整什么。
要具体关于改变什么，而非仅某事需改变。]

**Pivot direction:** [What to try differently]
**What to keep:** [What worked and should be preserved]
**Next step:** `/prototype [revised-concept]`
**转向方向：**[尝试什么不同]
**保留什么：**[什么有效且应保留]
**下一步：**`/prototype [revised-concept]`

---

## If Killing
## 如终止

[Why this concept does not work — what specific signal led to this verdict.
This report is the deliverable; no further action needed on this concept.]
[为何此概念不工作 — 什么具体信号导致此裁决。
此报告是交付物；此概念无需进一步行动。]

**Next step:** `/brainstorm [new-direction]`
**下一步：**`/brainstorm [new-direction]`

---

## Lessons Learned
## 经验教训

- **What assumptions were broken by actually building this?**
  [...]
- **什么假设被实际构建此打破？**
  [...]

- **What surprised us that didn't show up in the brainstorm?**
  [...]
- **什么令我们惊讶而头脑风暴中未出现？**
  [...]

- **What would we test differently next time?**
  [...]
- **下次我们会不同地测试什么？**
  [...]

---

> *Prototype code location: `prototypes/[concept-name]-concept/`*
> *This code is throwaway. Never refactor into production.*
> *原型代码位置：`prototypes/[concept-name]-concept/`*
> *此代码为丢弃物。永不重构入生产。*
