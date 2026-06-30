---
name: team-polish
description: "Orchestrate the polish team: coordinates performance-analyst, technical-artist, sound-designer, and qa-tester to optimize, polish, and harden a feature or area for release quality."
argument-hint: "[feature or area to polish] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---
---
名称：team-polish
描述："编排打磨团队：协调 performance-analyst、technical-artist、sound-designer 和 qa-tester，以优化、打磨并加固某个功能或区域，达到发布质量。"
argument-hint："[要打磨的功能或区域] [--review full|lean|solo]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model：sonnet
---

If no argument is provided, output usage guidance and exit without spawning any agents:
如果未提供参数，则输出使用说明并退出，不生成任何智能体：

> Usage: `/team-polish [feature or area]` — specify the feature or area to polish (e.g., `combat`, `main menu`, `inventory system`, `level-1`). Do not use `AskUserQuestion` here; output the guidance directly.
> 用法：`/team-polish [功能或区域]` — 指定要打磨的功能或区域（例如 `combat`、`main menu`、`inventory system`、`level-1`）。此处不要使用 `AskUserQuestion`；直接输出说明。

When this skill is invoked with an argument, orchestrate the polish team through a structured pipeline.
当此技能带参数被调用时，通过结构化的流水线编排打磨团队。

**Decision Points:** At each phase transition, use `AskUserQuestion` to present
the user with the subagent's proposals as selectable options. Write the agent's
full analysis in conversation, then capture the decision with concise labels.
The user must approve before moving to the next phase.
**决策点：** 在每个阶段转换时，使用 `AskUserQuestion` 将子智能体的提案作为可选项呈现给用户。先将智能体的完整分析写入对话，然后用简洁的标签捕获决策。用户必须批准后才能进入下一阶段。

## Phase 0: Resolve Review Mode
## 阶段 0：解析评审模式

1. If `--review [mode]` was passed as an argument, use that mode.
2. Else read `production/review-mode.txt` — use whatever is written there.
3. Else default to `lean`.
1. 如果参数中传入了 `--review [mode]`，则使用该模式。
2. 否则读取 `production/review-mode.txt` — 使用其中写入的值。
3. 否则默认使用 `lean`。

Modes:
模式：

- `full` — spawn all director and lead gates as described
- `lean` — skip director gates unless they are PHASE-GATE type (CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE)
- `solo` — skip all director gate spawning entirely; run the skill without any agent gates
- `full` — 按描述生成所有总监和主管门禁
- `lean` — 跳过总监门禁，除非是 PHASE-GATE 类型（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE）
- `solo` — 完全跳过所有总监门禁生成；在没有任何智能体门禁的情况下运行技能

Store the resolved mode for use in all subsequent phases.
将解析出的模式存储起来，供所有后续阶段使用。

**Director gate skip rule**: Before spawning any Tier 1 director or lead for review (outside of PHASE-GATE triggers), apply the resolved mode: skip if solo mode; skip if lean mode and this is not a PHASE-GATE.
**总监门禁跳过规则**：在生成任何一级总监或主管进行评审时（非 PHASE-GATE 触发的情况），应用解析出的模式：如果是 solo 模式则跳过；如果是 lean 模式且这不是 PHASE-GATE，则跳过。

## Team Composition
## 团队构成

- **performance-analyst** — Profiling, optimization, memory analysis, frame budget
- **engine-programmer** — Engine-level bottlenecks: rendering pipeline, memory, resource loading (invoke when performance-analyst identifies low-level root causes)
- **technical-artist** — VFX polish, shader optimization, visual quality
- **sound-designer** — Audio polish, mixing, ambient layers, feedback sounds
- **tools-programmer** — Content pipeline tool verification, editor tool stability, automation fixes (invoke when content authoring tools are involved in the polished area)
- **qa-tester** — Edge case testing, regression testing, soak testing
- **performance-analyst** — 性能分析、优化、内存分析、帧预算
- **engine-programmer** — 引擎层瓶颈：渲染管线、内存、资源加载（当 performance-analyst 识别出底层根因时调用）
- **technical-artist** — VFX 打磨、着色器优化、视觉质量
- **sound-designer** — 音频打磨、混音、环境音层、反馈音效
- **tools-programmer** — 内容管线工具验证、编辑器工具稳定性、自动化修复（当打磨区域涉及内容创作工具时调用）
- **qa-tester** — 边界情况测试、回归测试、压力测试

## How to Delegate
## 如何委派

Use the Task tool to spawn each team member as a subagent:
使用 Task 工具将每个团队成员作为子智能体生成：

- `subagent_type: performance-analyst` — Profiling, optimization, memory analysis
- `subagent_type: engine-programmer` — Engine-level fixes for rendering, memory, resource loading
- `subagent_type: technical-artist` — VFX polish, shader optimization, visual quality
- `subagent_type: sound-designer` — Audio polish, mixing, ambient layers
- `subagent_type: tools-programmer` — Content pipeline and editor tool verification
- `subagent_type: qa-tester` — Edge case testing, regression testing, soak testing
- `subagent_type: performance-analyst` — 性能分析、优化、内存分析
- `subagent_type: engine-programmer` — 渲染、内存、资源加载的引擎层修复
- `subagent_type: technical-artist` — VFX 打磨、着色器优化、视觉质量
- `subagent_type: sound-designer` — 音频打磨、混音、环境音层
- `subagent_type: tools-programmer` — 内容管线与编辑器工具验证
- `subagent_type: qa-tester` — 边界情况测试、回归测试、压力测试

Always provide full context in each agent's prompt (target feature/area, performance budgets, known issues). Launch independent agents in parallel where the pipeline allows it (e.g., Phases 3 and 4 can run simultaneously).
始终在每个智能体的提示中提供完整上下文（目标功能/区域、性能预算、已知问题）。在流水线允许的情况下并行启动独立的智能体（例如，阶段 3 和阶段 4 可以同时运行）。

## Pipeline
## 流水线

### Phase 1: Assessment
### 阶段 1：评估

Delegate to **performance-analyst**:
委派给 **performance-analyst**：

- Profile the target feature/area using `/perf-profile`
- Identify performance bottlenecks and frame budget violations
- Measure memory usage and check for leaks
- Benchmark against target hardware specs
- Output: performance report with prioritized optimization list
- 使用 `/perf-profile` 对目标功能/区域进行性能分析
- 识别性能瓶颈和帧预算违规
- 测量内存使用情况并检查内存泄漏
- 对照目标硬件规格进行基准测试
- 输出：包含优先级优化列表的性能报告

### Phase 2: Optimization
### 阶段 2：优化

Delegate to **performance-analyst** (with relevant programmers as needed):
委派给 **performance-analyst**（按需配合相关程序员）：

- Fix performance hotspots identified in Phase 1
- Optimize draw calls, reduce overdraw
- Fix memory leaks and reduce allocation pressure
- Verify optimizations don't change gameplay behavior
- Output: optimized code with before/after metrics
- 修复阶段 1 中识别的性能热点
- 优化绘制调用，减少过度绘制
- 修复内存泄漏并降低分配压力
- 验证优化不会改变游戏玩法行为
- 输出：优化后的代码及前后对比指标

If Phase 1 identified engine-level root causes (rendering pipeline, resource loading, memory allocator), delegate those fixes to **engine-programmer** in parallel:
如果阶段 1 识别出引擎层根因（渲染管线、资源加载、内存分配器），则将这些修复并行委派给 **engine-programmer**：

- Optimize hot paths in engine systems
- Fix allocation pressure in core loops
- Output: engine-level fixes with profiler validation
- 优化引擎系统中的热路径
- 修复核心循环中的分配压力
- 输出：带性能分析器验证的引擎层修复

### Phase 3: Visual Polish (parallel with Phase 2)
### 阶段 3：视觉打磨（与阶段 2 并行）

Delegate to **technical-artist**:
委派给 **technical-artist**：

- Review VFX for quality and consistency with art bible
- Optimize particle systems and shader effects
- Add screen shake, camera effects, and visual juice where appropriate
- Ensure effects degrade gracefully on lower settings
- Output: polished visual effects
- 审查 VFX 的质量及与美术圣经的一致性
- 优化粒子系统和着色器效果
- 在适当处添加屏幕震动、镜头效果和视觉冲击力
- 确保效果在较低画质设置下能优雅降级
- 输出：打磨后的视觉效果

### Phase 4: Audio Polish (parallel with Phase 2)
### 阶段 4：音频打磨（与阶段 2 并行）

Delegate to **sound-designer**:
委派给 **sound-designer**：

- Review audio events for completeness (are any actions missing sound feedback?)
- Check audio mix levels — nothing too loud or too quiet relative to the mix
- Add ambient audio layers for atmosphere
- Verify audio plays correctly with spatial positioning
- Output: audio polish list and mixing notes
- 审查音频事件的完整性（是否有动作缺少声音反馈？）
- 检查音频混音电平 — 相对于整体混音没有过大或过小的部分
- 添加环境音层以营造氛围
- 验证音频在空间定位下正确播放
- 输出：音频打磨清单和混音备注

### Phase 5: Hardening
### 阶段 5：加固

Delegate to **qa-tester**:
委派给 **qa-tester**：

- Test all edge cases: boundary conditions, rapid inputs, unusual sequences
- Soak test: run the feature for extended periods checking for degradation
- Stress test: maximum entities, worst-case scenarios
- Regression test: verify polish changes haven't broken existing functionality
- Test on minimum spec hardware (if available)
- Output: test results with any remaining issues
- 测试所有边界情况：边界条件、快速输入、异常操作序列
- 压力测试：长时间运行功能，检查是否退化
- 极限测试：最大实体数、最坏场景
- 回归测试：验证打磨改动未破坏现有功能
- 在最低规格硬件上测试（如果可用）
- 输出：测试结果及任何遗留问题

### Phase 6: Sign-off
### 阶段 6：签收

- Collect results from all team members
- Compare performance metrics against budgets
- Report: READY FOR RELEASE / NEEDS MORE WORK
- List any remaining issues with severity and recommendations
- 收集所有团队成员的结果
- 将性能指标与预算进行对比
- 报告：READY FOR RELEASE / NEEDS MORE WORK
- 列出任何遗留问题及其严重程度和建议

## Error Recovery Protocol
## 错误恢复协议

If any spawned agent (via Task) returns BLOCKED, errors, or cannot complete:
如果任何生成的智能体（通过 Task）返回 BLOCKED、错误或无法完成：

1. **Surface immediately**: Report "[AgentName]: BLOCKED — [reason]" to the user before continuing to dependent phases
2. **Assess dependencies**: Check whether the blocked agent's output is required by subsequent phases. If yes, do not proceed past that dependency point without user input.
3. **Offer options** via AskUserQuestion with choices:
   - Skip this agent and note the gap in the final report
   - Retry with narrower scope
   - Stop here and resolve the blocker first
4. **Always produce a partial report** — output whatever was completed. Never discard work because one agent blocked.
1. **立即上报**：在继续进入依赖阶段之前，向用户报告"[AgentName]：BLOCKED — [原因]"
2. **评估依赖**：检查被阻塞智能体的输出是否为后续阶段所必需。如果是，则在用户输入之前不要越过该依赖点继续推进。
3. **通过 AskUserQuestion 提供选项**：
   - 跳过此智能体并在最终报告中标注缺口
   - 以更窄的范围重试
   - 在此停止并先解决阻塞问题
4. **始终产出部分报告** — 输出已完成的内容。不要因为一个智能体被阻塞就丢弃已完成的工作。

Common blockers:
常见阻塞问题：

- Input file missing (story not found, GDD absent) → redirect to the skill that creates it
- ADR status is Proposed → do not implement; run `/architecture-decision` first
- Scope too large → split into two stories via `/create-stories`
- Conflicting instructions between ADR and story → surface the conflict, do not guess
- 输入文件缺失（未找到故事、GDD 缺失）→ 重定向到创建该文件的技能
- ADR 状态为 Proposed → 不要实施；先运行 `/architecture-decision`
- 范围过大 → 通过 `/create-stories` 拆分为两个故事
- ADR 与故事之间的指令冲突 → 上报冲突，不要猜测

## File Write Protocol
## 文件写入协议

All file writes (performance reports, test results, evidence docs) are delegated to
sub-agents spawned via Task. Each sub-agent enforces the "May I write to [path]?"
protocol. This orchestrator does not write files directly.
所有文件写入（性能报告、测试结果、证据文档）都委派给通过 Task 生成的子智能体。每个子智能体执行"是否可以写入 [path]？"协议。此编排器不直接写入文件。

## Output
## 输出

A summary report covering: performance before/after metrics, visual polish changes, audio polish changes, test results, and release readiness assessment.
一份摘要报告，涵盖：性能前后对比指标、视觉打磨改动、音频打磨改动、测试结果以及发布就绪评估。

## Next Steps
## 后续步骤

- If READY FOR RELEASE: run `/release-checklist` for the final pre-release validation.
- If NEEDS MORE WORK: schedule remaining issues in `/sprint-plan update` and re-run `/team-polish` after fixes.
- Run `/gate-check` for a formal phase gate verdict before handing off to release.
- 如果 READY FOR RELEASE：运行 `/release-checklist` 进行最终发布前验证。
- 如果 NEEDS MORE WORK：在 `/sprint-plan update` 中安排遗留问题，并在修复后重新运行 `/team-polish`。
- 运行 `/gate-check` 在移交给发布之前获取正式的阶段门禁裁决。
