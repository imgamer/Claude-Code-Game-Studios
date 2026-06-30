# ADR: [Decision Name]
# ADR：[决策名称]

---
**Status**: Reverse-Documented
**Source**: `[path to implementation code]`
**Date**: [YYYY-MM-DD]
**Decision Makers**: [User name or "inferred from code"]
**Implementation Status**: [Deployed | Partial | Planned]
---
**状态**：反向文档化
**来源**：`[path to implementation code]`
**日期**：[YYYY-MM-DD]
**决策者**：[用户名或 "inferred from code"]
**实现状态**：[已部署 | 部分 | 计划中]

> **⚠️ Reverse-Documentation Notice**
>
> This Architecture Decision Record was created **after** the implementation already
> existed. It captures the current implementation approach and clarified rationale
> based on code analysis and user consultation. Some context may be reconstructed
> rather than contemporaneously documented.
> **⚠️ 反向文档化说明**
>
> 此架构决策记录是在实现**已经存在**之后创建的。它基于代码分析和用户咨询记录了
> 当前实现方法和已澄清的理由。某些上下文可能是重建的，而非同时记录的。

---

## Context
## 上下文

**Problem Statement**: [What problem did this implementation solve?]
**问题陈述**：[此实现解决了什么问题？]

**Background** (inferred from code):
- [Context 1 — why this problem needed solving]
- [Context 2 — constraints at the time]
- [Context 3 — alternatives that were likely considered]
**背景**（从代码推断）：
- [上下文 1 — 为何需解决此问题]
- [上下文 2 — 当时约束]
- [上下文 3 — 可能考虑的替代方案]

**System Scope**: [What parts of the codebase does this affect?]
**系统范围**：[这影响代码库哪些部分？]

**Stakeholders**:
- [Role 1]: [Their concern or requirement]
- [Role 2]: [Their concern or requirement]
**利益相关者**：
- [角色 1]：[他们的关注或要求]
- [角色 2]：[他们的关注或要求]

---

## Decision
## 决策

**Approach Taken** (as implemented):
**采用的方法**（已实现）：

[Describe the architectural approach found in the code]
[描述代码中发现的架构方法]

**Key Implementation Details**:
- [Detail 1]: [How it works]
- [Detail 2]: [Pattern or structure used]
- [Detail 3]: [Notable design choice]
**关键实现细节**：
- [细节 1]：[它如何工作]
- [细节 2]：[使用的模式或结构]
- [细节 3]：[显著设计选择]

**Clarified Rationale** (from user):
- [Reason 1 — why this approach was chosen]
- [Reason 2 — what problem it solves]
- [Reason 3 — what benefit it provides]
**澄清的理由**（来自用户）：
- [理由 1 — 为何选择此方法]
- [理由 2 — 它解决什么问题]
- [理由 3 — 它提供什么收益]

**Code Locations**:
- `[file/path 1]`: [What's there]
- `[file/path 2]`: [What's there]
**代码位置**：
- `[file/path 1]`：[那里有什么]
- `[file/path 2]`：[那里有什么]

---

## Alternatives Considered
## 考虑的替代方案

*(These may be inferred or clarified with user)*
*（这些可能是推断的或与用户澄清的）*

### Alternative 1: [Approach Name]
### 替代方案 1：[方法名称]

**Description**: [What this alternative would have been]
**描述**：[此替代方案会是什么]

**Pros**:
- ✅ [Advantage 1]
- ✅ [Advantage 2]
**优点**：
- ✅ [优点 1]
- ✅ [优点 2]

**Cons**:
- ❌ [Disadvantage 1]
- ❌ [Disadvantage 2]
**缺点**：
- ❌ [缺点 1]
- ❌ [缺点 2]

**Why Not Chosen**: [Reason — from user clarification or inference]
**为何未选**：[理由 — 来自用户澄清或推断]

### Alternative 2: [Approach Name]
### 替代方案 2：[方法名称]

**Description**: [What this alternative would have been]
**描述**：[此替代方案会是什么]

**Pros**:
- ✅ [Advantage 1]
- ✅ [Advantage 2]
**优点**：
- ✅ [优点 1]
- ✅ [优点 2]

**Cons**:
- ❌ [Disadvantage 1]
- ❌ [Disadvantage 2]
**缺点**：
- ❌ [缺点 1]
- ❌ [缺点 2]

**Why Not Chosen**: [Reason]
**为何未选**：[理由]

### Alternative 3: [Status Quo / No Change]
### 替代方案 3：[维持现状 / 无变更]

**Description**: [What "doing nothing" would mean]
**描述**：["不做"会意味着什么]

**Why Not Acceptable**: [Why the problem needed solving]
**为何不可接受**：[为何需要解决问题]

---

## Consequences
## 后果

### Positive Consequences (Benefits Realized)
### 正面后果（已实现收益）

✅ **[Benefit 1]**: [How the implementation provides this]

✅ **[Benefit 2]**: [Impact]

✅ **[Benefit 3]**: [Impact]
✅ **[收益 1]**：[实现如何提供此]

✅ **[收益 2]**：[影响]

✅ **[收益 3]**：[影响]

### Negative Consequences (Trade-offs Accepted)
### 负面后果（已接受的权衡）

⚠️ **[Trade-off 1]**: [What was sacrificed or made harder]

⚠️ **[Trade-off 2]**: [Limitation or cost]

⚠️ **[Trade-off 3]**: [Complexity or maintenance burden]
⚠️ **[权衡 1]**：[什么被牺牲或变得更难]

⚠️ **[权衡 2]**：[限制或成本]

⚠️ **[权衡 3]**：[复杂度或维护负担]

### Neutral Consequences (Observations)
### 中性后果（观察）

ℹ️ **[Observation 1]**: [Emergent property or side effect]

ℹ️ **[Observation 2]**: [Unexpected outcome]
ℹ️ **[观察 1]**：[涌现属性或副作用]

ℹ️ **[观察 2]**：[意外结果]

---

## Implementation Notes
## 实现备注

**Patterns Used**:
- [Pattern 1]: [Where and why]
- [Pattern 2]: [Where and why]
**使用的模式**：
- [模式 1]：[何处及为何]
- [模式 2]：[何处及为何]

**Dependencies Introduced**:
- [Dependency 1]: [Why needed]
- [Dependency 2]: [Why needed]
**引入的依赖**：
- [依赖 1]：[为何需要]
- [依赖 2]：[为何需要]

**Performance Characteristics**:
- Time complexity: [O(n), etc.]
- Space complexity: [Memory usage]
- Bottlenecks: [Known performance concerns]
**性能特征**：
- 时间复杂度：[O(n) 等]
- 空间复杂度：[内存使用]
- 瓶颈：[已知性能问题]

**Thread Safety**:
- [Thread safety approach — single-threaded, mutex-protected, lock-free, etc.]
**线程安全**：
- [线程安全方法 — 单线程、互斥锁保护、无锁等]

**Testing Strategy**:
- [How this is tested — unit tests, integration tests, etc.]
- Coverage: [Estimated or measured]
**测试策略**：
- [如何测试 — 单元测试、集成测试等]
- 覆盖率：[估算或测量]

---

## Validation
## 验证

**How We Know This Works**:
- ✅ [Evidence 1 — e.g., "6 months in production without issues"]
- ✅ [Evidence 2 — e.g., "handles 10k entities at 60 FPS"]
- ⚠️ [Evidence 3 — e.g., "works but needs monitoring"]
**我们如何知道此有效**：
- ✅ [证据 1 — 例如，"生产 6 个月无问题"]
- ✅ [证据 2 — 例如，"60 FPS 处理 10k 实体"]
- ⚠️ [证据 3 — 例如，"工作但需监控"]

**Known Issues** (discovered during analysis):
- ⚠️ [Issue 1]: [Problem and potential fix]
- ⚠️ [Issue 2]: [Problem and potential fix]
**已知问题**（分析中发现）：
- ⚠️ [问题 1]：[问题与潜在修复]
- ⚠️ [问题 2]：[问题与潜在修复]

**Risks**:
- [Risk 1]: [Potential problem if X happens]
- [Risk 2]: [Scalability concern]
**风险**：
- [风险 1]：[如 X 发生的潜在问题]
- [风险 2]：[可扩展性顾虑]

---

## Open Questions
## 待解决问题

**Unresolved During Reverse-Documentation**:
1. **[Question 1]**: [What's unclear about the decision or implementation?]
   - Needs clarification from: [Who]
   - Impact if unresolved: [Consequence]

2. **[Question 2]**: [What needs to be decided for future work?]
**反向文档化期间未解决**：
1. **[问题 1]**：[决策或实现什么不明确？]
   - 需澄清来自：[谁]
   - 如未解决的影响：[后果]

2. **[问题 2]**：[未来工作需决定什么？]

---

## Follow-Up Work
## 后续工作

**Immediate**:
- [ ] [Task 1 — e.g., "Add missing unit tests"]
- [ ] [Task 2 — e.g., "Document edge case handling"]
**即时**：
- [ ] [任务 1 — 例如，"添加缺失单元测试"]
- [ ] [任务 2 — 例如，"记录边界情况处理"]

**Short-Term**:
- [ ] [Task 3 — e.g., "Refactor X for clarity"]
- [ ] [Task 4 — e.g., "Add performance monitoring"]
**短期**：
- [ ] [任务 3 — 例如，"为清晰重构 X"]
- [ ] [任务 4 — 例如，"添加性能监控"]

**Long-Term**:
- [ ] [Task 5 — e.g., "Revisit decision when Y is available"]
**长期**：
- [ ] [任务 5 — 例如，"当 Y 可用时重新审视决策"]

---

## Related Decisions
## 相关决策

**Depends On** (ADRs this builds upon):
- [ADR-XXX]: [Related decision]
**依赖于**（此构建于其上的 ADR）：
- [ADR-XXX]：[相关决策]

**Influences** (ADRs affected by this):
- [ADR-YYY]: [How this impacts it]
**影响**（受此影响的 ADR）：
- [ADR-YYY]：[这如何影响它]

**Supersedes**:
- [ADR-ZZZ]: [Old decision this replaces, if any]
**取代**：
- [ADR-ZZZ]：[此取代的旧决策，如有]

**Superseded By**:
- [None yet | ADR-WWW if this decision is later replaced]
**被取代**：
- [尚无 | 如此决策后来被取代则为 ADR-WWW]

---

## References
## 参考

**Code Locations**:
- `[path/file 1]`: [Primary implementation]
- `[path/file 2]`: [Related code]
**代码位置**：
- `[path/file 1]`：[主要实现]
- `[path/file 2]`：[相关代码]

**External Resources**:
- [Article/Book]: [Relevant pattern or technique reference]
- [Documentation]: [Engine or library docs consulted]
**外部资源**：
- [文章/书]：[相关模式或技术参考]
- [文档]：[参考的引擎或库文档]

**Design Documents**:
- [GDD Section]: [If this implements a design]
**设计文档**：
- [GDD 章节]：[如此实现一个设计]

---

## Version History
## 版本历史

| Date | Author | Changes |
|------|--------|---------|
| [Date] | Claude (reverse-doc) | Initial reverse-documentation from `[source path]` |
| [Date] | [User] | Clarified rationale for [X] |
| 日期 | 作者 | 变更 |
|------|--------|---------|
| [Date] | Claude (反向文档) | 从 `[source path]` 初始反向文档化 |
| [Date] | [User] | 澄清 [X] 的理由 |

---

## Status Legend
## 状态图例

- **Proposed**: Under discussion, not implemented
- **Accepted**: Decided, implementation in progress
- **Deprecated**: No longer recommended, but may exist in code
- **Superseded**: Replaced by another decision
- **Reverse-Documented**: Created after implementation (this document)
- **提议**：讨论中，未实现
- **已接受**：已决定，实现进行中
- **弃用**：不再推荐，但可能存在于代码中
- **被取代**：被另一决策取代
- **反向文档化**：实现后创建（本文档）

---

**Current Status**: **Reverse-Documented**
**当前状态**：**反向文档化**

---

*This ADR was generated by `/reverse-document architecture [path]`*
*此 ADR 由 `/reverse-document architecture [path]` 生成*

---

## Appendix: Code Snippets
## 附录：代码片段

**Key Implementation Pattern**:
**关键实现模式**：

```[language]
[Code snippet showing the core pattern or decision]
```

**Rationale**: [Why this code structure embodies the decision]
**理由**：[为何此代码结构体现该决策]

**Alternative Approach** (not chosen):
**替代方法**（未选）：

```[language]
[Code snippet showing what the alternative would look like]
```

**Why Not**: [Why the implemented approach was preferred]
**为何不**：[为何偏好已实现方法]
