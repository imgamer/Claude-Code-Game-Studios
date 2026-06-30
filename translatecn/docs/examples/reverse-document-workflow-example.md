# Reverse Documentation Workflow Example
# 反向文档工作流示例

## Scenario
## 场景
Developer built a working skill tree system but never wrote a design doc.
Code exists in `src/gameplay/skills/`, but `design/gdd/` has no skill-system.md.
开发者构建了一个可用的技能树系统但从未编写设计文档。
代码存在于 `src/gameplay/skills/`，但 `design/gdd/` 中没有 skill-system.md。

---

## Session Transcript
## 会话记录

**User:**
**用户：**
```
I have a working skill tree system in src/gameplay/skills/ with about 1200 lines
of code. I never wrote a design doc. Can we create one from the implementation?
```

**Game-Designer (reads code, analyzes):**
**Game-Designer（阅读代码，分析）：**
```
I've analyzed your skill tree implementation. Here's what I found:

STRUCTURE:
- 3 skill trees: Combat, Survival, Tech
- 5 tiers per tree (unlock via level + prerequisites)
- Total 45 skills (15 per tree)
- Cost: skill points (1 per level, can respec for gold)

MECHANICS:
- Passive skills (stat bonuses)
- Active skills (abilities with cooldowns)
- Synergy bonuses (cross-tree unlocks at tier 4+)

IMPLEMENTATION DETAILS:
- Data-driven (JSON files in assets/data/skills/)
- Save-compatible (skill state serialized)
- UI shows locked/unlocked/available states

QUESTIONS before I draft the design doc:

1. **Design Intent:** Was the 3-tree structure meant to represent player archetypes
   (fighter/survivor/engineer) or playstyle flexibility?

2. **Respec Cost:** Respec costs gold - is this meant to:
   - Discourage frequent respecs (commitment to builds)?
   - Create a gold sink (economy balance)?
   - Or should it be free (experimentation encouraged)?

3. **Synergy System:** Cross-tree synergies appear at tier 4. Was this:
   - Intentional (reward hybrid builds late-game)?
   - Or emergent (accidentally creates hybrid meta)?

4. **Balance Philosophy:** I see some skills are clearly better (tier-5 passive
   is +50% damage). Is this:
   - Power fantasy (late-game should feel OP)?
   - Or unbalanced (needs tuning)?
```

**User:**
**用户：**
```
Good questions! Here's the intent:

1. Yes, the 3 trees are archetypes but I want flexibility
2. Respec should be cheap - I want experimentation
3. Synergies were intentional - hybrid builds are a feature
4. Yeah, tier 5 is meant to be powerful, but +50% might be too much
```

**Game-Designer:**
**Game-Designer：**
```
Perfect, that clarifies the vision. Let me draft the design doc:

[Shows draft of design/gdd/skill-system.md]

SECTIONS:
1. Overview (3-tree system, hybrid-friendly, respec-encouraged)
2. Design Pillars:
   - Archetype clarity (each tree has identity)
   - Build flexibility (no lock-in, cheap respec)
   - Synergy rewards (cross-tree combinations viable)
3. Detailed Design (tree structure, unlock rules, respec)
4. Balance Framework (tier power curve, synergy limits)
5. Edge Cases (respec during combat? max skills? cap?)
6. Dependencies (UI, save system, tutorial)
7. Acceptance Criteria (all trees viable, 10+ hybrid builds, clear tooltips)
8. Open Questions:
   - Should respec cost scale with level or stay flat?
   - Tier 5 damage bonus: tune to +30% or keep +50%?

**May I write this to design/gdd/skill-system.md?**
```

**User:**
**用户：**
```
Yes, write it. But let's tune tier 5 to +30% and make respec cost scale.
```

**Game-Designer:**
**Game-Designer：**
```
✅ Written to design/gdd/skill-system.md
✅ Marked as [REVERSE-DOCUMENTED FROM IMPLEMENTATION]
✅ Added TODO: Update tier-5 passive to +30% in assets/data/skills/combat-tree.json
✅ Added TODO: Implement scaling respec cost formula

Next steps:
- Run /balance-check to validate skill tree progression curve?
- Create ADR documenting why 3 trees instead of 5?
- Update tutorial to explain hybrid synergies?
```

---

## Key Observations
## 关键观察

1. **Agent asked clarifying questions** - didn't just describe code
1. **智能体提出了澄清问题** —— 不只是描述代码
2. **Separated intent from implementation** - user corrected "accidental" imbalance
2. **将意图与实现分离** —— 用户纠正了"意外的"不平衡
3. **Design doc captures "why"** - not just "what the code does"
3. **设计文档捕获"为什么"** —— 而不只是"代码做了什么"
4. **Identified missing pieces** - edge cases not in code
4. **识别缺失部分** —— 代码中没有的边界情况
5. **Flagged follow-up work** - balance tuning, ADRs, tutorial
5. **标记后续工作** —— 平衡调优、ADR、教程

## Outcome
## 结果

The design doc now exists and **matches reality** while also capturing the **intended vision**. Future changes will reference this doc, and new team members understand the *why* behind decisions.
设计文档现在存在并**与现实匹配**，同时捕获了**预期愿景**。未来的更改将引用此文档，新团队成员理解决策背后的*为什么*。
