# Architecture Traceability Index
# 架构可追溯性索引

<!-- Living document — updated by /architecture-review after each review run.
     Do not edit manually unless correcting an error. -->
<!-- 活文档 — 由 /architecture-review 在每次评审运行后更新。
     除非更正错误，否则请勿手动编辑。 -->

## Document Status
## 文档状态

- **Last Updated**: [YYYY-MM-DD]
- **最后更新**：[YYYY-MM-DD]
- **Engine**: [e.g. Godot 4.6]
- **引擎**：[例如 Godot 4.6]
- **GDDs Indexed**: [N]
- **已索引 GDD 数**：[N]
- **ADRs Indexed**: [M]
- **已索引 ADR 数**：[M]
- **Last Review**: [link to docs/architecture/architecture-review-[date].md]
- **上次评审**：[链接至 docs/architecture/architecture-review-[date].md]

## Coverage Summary
## 覆盖率汇总

| Status | Count | Percentage |
|--------|-------|-----------|
| ✅ Covered | [X] | [%] |
| ⚠️ Partial | [Y] | [%] |
| ❌ Gap | [Z] | [%] |
| **Total** | **[N]** | |

| 状态 | 数量 | 百分比 |
|--------|-------|-----------|
| ✅ 已覆盖 | [X] | [%] |
| ⚠️ 部分覆盖 | [Y] | [%] |
| ❌ 缺口 | [Z] | [%] |
| **总计** | **[N]** | |

---

## Traceability Matrix
## 可追溯性矩阵

<!-- One row per technical requirement extracted from a GDD.
     A "technical requirement" is any GDD statement that implies a specific
     architectural decision: data structures, performance constraints, engine
     capabilities needed, cross-system communication, state persistence. -->
<!-- 从 GDD 提取的每条技术需求占一行。
     "技术需求"指任何暗示特定架构决策的 GDD 陈述：数据结构、性能约束、
     所需引擎能力、跨系统通信、状态持久化。 -->

| Req ID | GDD | System | Requirement Summary | ADR(s) | Status | Notes |
|--------|-----|--------|---------------------|--------|--------|-------|
| TR-[gdd]-001 | [filename] | [system name] | [one-line summary] | [ADR-NNNN] | ✅ | |
| TR-[gdd]-002 | [filename] | [system name] | [one-line summary] | — | ❌ GAP | Needs `/architecture-decision [title]` |

| 需求 ID | GDD | 系统 | 需求摘要 | ADR | 状态 | 备注 |
|--------|-----|--------|---------------------|--------|--------|-------|
| TR-[gdd]-001 | [文件名] | [系统名] | [一行摘要] | [ADR-NNNN] | ✅ | |
| TR-[gdd]-002 | [文件名] | [系统名] | [一行摘要] | — | ❌ 缺口 | 需要 `/architecture-decision [title]` |

---

## Known Gaps
## 已知缺口

Requirements with no ADR coverage, prioritised by layer (Foundation first):
无 ADR 覆盖的需求，按层（基础层优先）排序：

### Foundation Layer Gaps (BLOCKING — must resolve before coding)
### 基础层缺口（阻塞性 — 编码前必须解决）
- [ ] TR-[id]: [requirement] — GDD: [file] — Suggested ADR: "[title]"
- [ ] TR-[id]：[需求] — GDD：[文件] — 建议 ADR："[标题]"

### Core Layer Gaps (must resolve before relevant system is built)
### 核心层缺口（构建相关系统前必须解决）
- [ ] TR-[id]: [requirement] — GDD: [file] — Suggested ADR: "[title]"
- [ ] TR-[id]：[需求] — GDD：[文件] — 建议 ADR："[标题]"

### Feature Layer Gaps (should resolve before feature sprint)
### 功能层缺口（功能冲刺前应解决）
- [ ] TR-[id]: [requirement] — GDD: [file] — Suggested ADR: "[title]"
- [ ] TR-[id]：[需求] — GDD：[文件] — 建议 ADR："[标题]"

### Presentation Layer Gaps (can defer to implementation)
### 表现层缺口（可推迟至实现阶段）
- [ ] TR-[id]: [requirement] — GDD: [file] — Suggested ADR: "[title]"
- [ ] TR-[id]：[需求] — GDD：[文件] — 建议 ADR："[标题]"

---

## Cross-ADR Conflicts
## ADR 间冲突

<!-- Pairs of ADRs that make contradictory claims. Must be resolved. -->
<!-- 相互矛盾的一对 ADR。必须解决。 -->

| Conflict ID | ADR A | ADR B | Type | Status |
|-------------|-------|-------|------|--------|
| CONFLICT-001 | ADR-NNNN | ADR-MMMM | Data ownership | 🔴 Unresolved |

| 冲突 ID | ADR A | ADR B | 类型 | 状态 |
|-------------|-------|-------|------|--------|
| CONFLICT-001 | ADR-NNNN | ADR-MMMM | 数据归属 | 🔴 未解决 |

---

## ADR → GDD Coverage (Reverse Index)
## ADR → GDD 覆盖（反向索引）

<!-- For each ADR, which GDD requirements does it address? -->
<!-- 每个 ADR 解决了哪些 GDD 需求？ -->

| ADR | Title | GDD Requirements Addressed | Engine Risk |
|-----|-------|---------------------------|-------------|
| ADR-0001 | [title] | TR-combat-001, TR-combat-002 | HIGH |

| ADR | 标题 | 已解决的 GDD 需求 | 引擎风险 |
|-----|-------|---------------------------|-------------|
| ADR-0001 | [标题] | TR-combat-001, TR-combat-002 | 高 |

---

## Superseded Requirements
## 已被取代的需求

<!-- Requirements that existed in a GDD when an ADR was written, but the GDD
     has since changed. The ADR may need updating. -->
<!-- 编写 ADR 时存在于 GDD 中、但 GDD 之后已变更的需求。
     ADR 可能需要更新。 -->

| Req ID | GDD | Change | Affected ADR | Status |
|--------|-----|--------|-------------|--------|
| TR-[id] | [file] | [what changed] | ADR-NNNN | 🔴 ADR needs update |

| 需求 ID | GDD | 变更内容 | 受影响的 ADR | 状态 |
|--------|-----|--------|-------------|--------|
| TR-[id] | [文件] | [变更内容] | ADR-NNNN | 🔴 ADR 需更新 |

---

## How to Use This Document
## 如何使用本文档

**When writing a new ADR**: Add it to the "ADR → GDD Coverage" table and mark
the requirements it satisfies as ✅ in the matrix.
**编写新 ADR 时**：将其加入 "ADR → GDD 覆盖" 表，并在矩阵中将它满足的需求标记为 ✅。

**When approving a GDD change**: Scan the matrix for requirements from that GDD
and check whether the change invalidates any existing ADR. Add to "Superseded
Requirements" if so.
**批准 GDD 变更时**：扫描矩阵中来自该 GDD 的需求，检查变更是否会使任何现有 ADR 失效。如果会失效，请加入 "已被取代的需求"。

**When running `/architecture-review`**: The skill will update this document
automatically with the current state.
**运行 `/architecture-review` 时**：技能会自动用当前状态更新本文档。

**Gate check**: The Pre-Production gate requires this document to exist and to
have zero Foundation Layer Gaps.
**门检查**：Pre-Production 门要求本文档存在且基础层缺口为零。
