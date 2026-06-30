---
name: perf-profile
description: "Structured performance profiling workflow. Identifies bottlenecks, measures against budgets, and generates optimization recommendations with priority rankings."
argument-hint: "[system-name or 'full']"
user-invocable: true
agent: performance-analyst
allowed-tools: Read, Glob, Grep, Bash
model: sonnet
---
---
名称：perf-profile
描述："结构化的性能分析工作流。识别瓶颈、对照预算进行衡量，并生成带优先级排序的优化建议。"
argument-hint："[系统名或 'full']"
user-invocable：true
agent：performance-analyst
allowed-tools：Read, Glob, Grep, Bash
model：sonnet
---

## Phase 1: Determine Scope
## 阶段 1：确定范围

Read the argument:
读取参数：

- System name → focus profiling on that specific system
- `full` → run a comprehensive profile across all systems
- 系统名 → 将性能分析聚焦于该特定系统
- `full` → 对所有系统进行全面性能分析

---

## Phase 2: Load Performance Budgets
## 阶段 2：加载性能预算

Check for existing performance targets in design docs or CLAUDE.md:
在设计文档或 CLAUDE.md 中检查现有的性能目标：

- Target FPS (e.g., 60fps = 16.67ms frame budget)
- Memory budget (total and per-system)
- Load time targets
- Draw call budgets
- Network bandwidth limits (if multiplayer)
- 目标帧率（例如，60fps = 16.67ms 帧预算）
- 内存预算（总量和各系统分量）
- 加载时间目标
- 绘制调用预算
- 网络带宽限制（如果是多人游戏）

---

## Phase 3: Analyze Codebase
## 阶段 3：分析代码库

**CPU Profiling Targets:**
- `_process()` / `Update()` / `Tick()` functions — list all and estimate cost
- Nested loops over large collections
- String operations in hot paths
- Allocation patterns in per-frame code
- Unoptimized search/sort over game entities
- Expensive physics queries (raycasts, overlaps) every frame
**CPU 性能分析目标：**
- `_process()` / `Update()` / `Tick()` 函数 — 列出所有并估算成本
- 对大型集合的嵌套循环
- 热路径中的字符串操作
- 每帧代码中的分配模式
- 对游戏实体的未优化搜索/排序
- 每帧执行的高开销物理查询（射线检测、重叠检测）

**Memory Profiling Targets:**
- Large data structures and their growth patterns
- Texture/asset memory footprint estimates
- Object pool vs instantiate/destroy patterns
- Leaked references (objects that should be freed but aren't)
- Cache sizes and eviction policies
**内存性能分析目标：**
- 大型数据结构及其增长模式
- 纹理/资产内存占用估算
- 对象池 vs 实例化/销毁模式
- 泄漏的引用（应释放但未释放的对象）
- 缓存大小和淘汰策略

**Rendering Targets (if applicable):**
- Draw call estimates
- Overdraw from overlapping transparent objects
- Shader complexity
- Unoptimized particle systems
- Missing LODs or occlusion culling
**渲染分析目标（如适用）：**
- 绘制调用估算
- 重叠透明对象导致的过度绘制
- 着色器复杂度
- 未优化的粒子系统
- 缺失 LOD 或遮挡剔除

**I/O Targets:**
- Save/load performance
- Asset loading patterns (sync vs async)
- Network message frequency and size
**I/O 分析目标：**
- 存档/读档性能
- 资产加载模式（同步 vs 异步）
- 网络消息频率和大小

---

## Phase 4: Generate Profiling Report
## 阶段 4：生成性能分析报告

```markdown
## Performance Profile: [System or Full]
Generated: [Date]

### Performance Budgets
| Metric | Budget | Estimated Current | Status |
|--------|--------|-------------------|--------|
| Frame time | [16.67ms] | [estimate] | [OK/WARNING/OVER] |
| Memory | [target] | [estimate] | [OK/WARNING/OVER] |
| Load time | [target] | [estimate] | [OK/WARNING/OVER] |
| Draw calls | [target] | [estimate] | [OK/WARNING/OVER] |

### Hotspots Identified
| # | Location | Issue | Estimated Impact | Fix Effort |
|---|----------|-------|------------------|------------|

### Optimization Recommendations (Priority Order)
1. **[Title]** — [Description]
   - Location: [file:line]
   - Expected gain: [estimate]
   - Risk: [Low/Med/High]
   - Approach: [How to implement]

### Quick Wins (< 1 hour each)
- [Simple optimization 1]

### Requires Investigation
- [Area that needs actual runtime profiling to confirm impact]
```

Output the report with a summary: top 3 hotspots, estimated headroom vs budget, and recommended next action.
输出报告并附带摘要：前 3 个热点、相对于预算的预计余量，以及建议的下一步行动。

---

## Phase 5: Scope and Timeline Decision
## 阶段 5：范围与时间线决策

Activate this phase only if any hotspot has Fix Effort rated M or L.
仅当任何热点的修复工作量被评为 M 或 L 时，才激活此阶段。

Present significant-effort items and ask the user to choose for each:
呈现工作量较大的条目，并请用户对每个条目进行选择：

- **A) Implement the optimization** (proceed with fix now or schedule it)
- **B) Reduce feature scope** (run `/scope-check [feature]` to analyze trade-offs)
- **C) Accept the performance hit and defer to Polish phase** (log as known issue)
- **D) Escalate to technical-director for an architectural decision** (run `/architecture-decision`)
- **A) 实施优化**（现在进行修复或安排时间）
- **B) 缩小功能范围**（运行 `/scope-check [feature]` 分析权衡）
- **C) 接受性能损失并推迟到打磨阶段**（记录为已知问题）
- **D) 上报给 technical-director 进行架构决策**（运行 `/architecture-decision`）

If multiple items are deferred to Polish (choice C), record them under `### Deferred to Polish`.
如果多个条目被推迟到打磨阶段（选项 C），将它们记录在 `### Deferred to Polish` 下。

This skill is read-only — no files are written. Verdict: **COMPLETE** — performance profile generated.
此技能是只读的 — 不写入任何文件。裁决：**COMPLETE** — 性能分析已生成。

---

## Phase 6: Next Steps
## 阶段 6：后续步骤

- If bottlenecks require architectural change: run `/architecture-decision`.
- If scope reduction is needed: run `/scope-check [feature]`.
- To schedule optimizations: run `/sprint-plan update`.
- 如果瓶颈需要架构变更：运行 `/architecture-decision`。
- 如果需要缩小范围：运行 `/scope-check [feature]`。
- 如需安排优化任务：运行 `/sprint-plan update`。

### Rules
### 规则

- Never optimize without measuring first — gut feelings about performance are unreliable
- Recommendations must include estimated impact — "make it faster" is not actionable
- Profile on target hardware, not just development machines
- Static analysis (this skill) identifies candidates; runtime profiling confirms
- 永远不要在没有先测量的情况下进行优化 — 对性能的直觉是不可靠的
- 建议必须包含预计影响 — "让它更快"不可操作
- 在目标硬件上进行分析，而不仅仅是在开发机上
- 静态分析（此技能）识别候选项；运行时性能分析进行确认
