---
name: team-audio
description: "Orchestrate audio team: audio-director + sound-designer + technical-artist + gameplay-programmer for full audio pipeline from direction to implementation."
argument-hint: "[feature or area to design audio for] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---
---
name: team-audio
description: "编排音频团队：audio-director + sound-designer + technical-artist + gameplay-programmer，涵盖从方向到实现的完整音频管线。"
argument-hint: "[feature or area to design audio for] [--review full|lean|solo]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, Task, AskUserQuestion, TodoWrite
model: sonnet
---

If no argument is provided, output usage guidance and exit without spawning any agents:
> Usage: `/team-audio [feature or area]` — specify the feature or area to design audio for (e.g., `combat`, `main menu`, `forest biome`, `boss encounter`). Do not use `AskUserQuestion` here; output the guidance directly.
如果未提供参数，输出使用指南并退出，不派生任何智能体：
> 用法：`/team-audio [feature or area]` — 指定要为其设计音频的功能或区域（例如 `combat`、`main menu`、`forest biome`、`boss encounter`）。此处不要使用 `AskUserQuestion`；直接输出指南。

When this skill is invoked with an argument, orchestrate the audio team through a structured pipeline.
当此技能带参数被调用时，通过结构化的管线编排音频团队。

**Decision Points:** At each step transition, use `AskUserQuestion` to present
the user with the subagent's proposals as selectable options. Write the agent's
full analysis in conversation, then capture the decision with concise labels.
The user must approve before moving to the next step.
**决策点：** 在每次步骤转换时，使用 `AskUserQuestion` 向用户
呈现子智能体的建议作为可选选项。在对话中写入智能体的
完整分析，然后用简洁的标签捕获决策。
用户必须批准才能进入下一步。

## Phase 0: Resolve Review Mode
## 阶段 0：解析审查模式

1. If `--review [mode]` was passed as an argument, use that mode.
2. Else read `production/review-mode.txt` — use whatever is written there.
3. Else default to `lean`.
1. 如果作为参数传入了 `--review [mode]`，使用该模式。
2. 否则读取 `production/review-mode.txt` — 使用其中的内容。
3. 否则默认为 `lean`。

Modes:
- `full` — spawn all director and lead gates as described
- `lean` — skip director gates unless they are PHASE-GATE type (CD-PHASE-GATE, TD-PHASE-GATE, PR-PHASE-GATE, AD-PHASE-GATE)
- `solo` — skip all director gate spawning entirely; run the skill without any agent gates
模式：
- `full` — 按描述派生所有主管和负责人门禁
- `lean` — 跳过主管门禁，除非它们是 PHASE-GATE 类型（CD-PHASE-GATE、TD-PHASE-GATE、PR-PHASE-GATE、AD-PHASE-GATE）
- `solo` — 完全跳过所有主管门禁派生；在没有任何智能体门禁的情况下运行此技能

Store the resolved mode for use in all subsequent phases.
存储解析后的模式以用于所有后续阶段。

1. **Read the argument** for the target feature or area (e.g., `combat`,
   `main menu`, `forest biome`, `boss encounter`).
1. **读取参数** 以获取目标功能或区域（例如 `combat`、
   `main menu`、`forest biome`、`boss encounter`）。

2. **Gather context**:
   - Read relevant design docs in `design/gdd/` for the feature
   - Read the sound bible at `design/gdd/sound-bible.md` if it exists
   - Read existing audio asset lists in `assets/audio/`
   - Read any existing sound design docs for this area
2. **收集上下文**：
   - 读取 `design/gdd/` 中该功能的相关设计文档
   - 如果存在，读取 `design/gdd/sound-bible.md` 处的音频圣经
   - 读取 `assets/audio/` 中现有的音频资产列表
   - 读取此区域的任何现有声音设计文档

## How to Delegate
## 如何委派

Use the Task tool to spawn each team member as a subagent:
- `subagent_type: audio-director` — Sonic identity, emotional tone, audio palette
- `subagent_type: sound-designer` — SFX specifications, audio events, mixing groups
- `subagent_type: technical-artist` — Audio middleware, bus structure, memory budgets
- `subagent_type: [primary engine specialist]` — Validate audio integration patterns for the engine
- `subagent_type: gameplay-programmer` — Audio manager, gameplay triggers, adaptive music
使用 Task 工具将每个团队成员派生为子智能体：
- `subagent_type: audio-director` — 声音标识、情感基调、音频色板
- `subagent_type: sound-designer` — SFX 规范、音频事件、混音组
- `subagent_type: technical-artist` — 音频中间件、总线结构、内存预算
- `subagent_type: [primary engine specialist]` — 验证引擎的音频集成模式
- `subagent_type: gameplay-programmer` — 音频管理器、玩法触发器、自适应音乐

Always provide full context in each agent's prompt (feature description, existing audio assets, design doc references).
始终在每个智能体的提示中提供完整上下文（功能描述、现有音频资产、设计文档引用）。

3. **Orchestrate the audio team** in sequence:
3. **按顺序编排音频团队**：

### Step 1: Audio Direction (audio-director)
### 步骤 1：音频方向（audio-director）
Spawn the `audio-director` agent to:
- Define the sonic identity for this feature/area
- Specify the emotional tone and audio palette
- Set music direction (adaptive layers, stems, transitions)
- Define audio priorities and mix targets
- Establish any adaptive audio rules (combat intensity, exploration, tension)
派生 `audio-director` 智能体以：
- 定义此功能/区域的声音标识
- 指定情感基调和音频色板
- 设定音乐方向（自适应层、分轨、过渡）
- 定义音频优先级和混音目标
- 建立任何自适应音频规则（战斗强度、探索、紧张感）

### Step 2: Sound Design and Audio Accessibility (parallel)
### 步骤 2：声音设计和音频可访问性（并行）
Spawn the `sound-designer` agent to:
- Create detailed SFX specifications for every audio event
- Define sound categories (ambient, UI, gameplay, music, dialogue)
- Specify per-sound parameters (volume range, pitch variation, attenuation)
- Plan audio event list with trigger conditions
- Define mixing groups and ducking rules
派生 `sound-designer` 智能体以：
- 为每个音频事件创建详细的 SFX 规范
- 定义声音类别（环境、UI、玩法、音乐、对话）
- 指定每个声音的参数（音量范围、音调变化、衰减）
- 规划带触发条件的音频事件列表
- 定义混音组和闪避规则

Spawn the `accessibility-specialist` agent in parallel to:
- Identify which audio events carry critical gameplay information (damage received, enemy nearby, objective complete) and require visual alternatives for hearing-impaired players
- Specify subtitle requirements: which audio events need captions, what text format, on-screen duration
- Check that no gameplay state is communicated by audio alone (all must have a visual fallback)
- Review the audio event list for any that could cause issues for players with auditory sensitivities (high-frequency alerts, sudden loud events)
- Output: audio accessibility requirements list integrated into the audio event spec
并行派生 `accessibility-specialist` 智能体以：
- 识别哪些音频事件携带关键玩法信息（受到伤害、敌人靠近、目标完成）并需要为听力障碍玩家提供视觉替代
- 指定字幕要求：哪些音频事件需要字幕、什么文本格式、屏幕上持续时间
- 检查没有玩法状态仅通过音频传达（所有都必须有视觉后备）
- 审查音频事件列表以查找可能对听觉敏感玩家造成问题的事件（高频警报、突然的大声事件）
- 输出：集成到音频事件规范中的音频可访问性要求列表

### Step 3: Technical Implementation (parallel)
### 步骤 3：技术实现（并行）
Spawn the `technical-artist` agent to:
- Design the audio middleware integration (Wwise/FMOD/native)
- Define audio bus structure and routing
- Specify memory budgets for audio assets per platform
- Plan streaming vs preloaded asset strategy
- Design any audio-reactive visual effects
派生 `technical-artist` 智能体以：
- 设计音频中间件集成（Wwise/FMOD/原生）
- 定义音频总线结构和路由
- 指定每个平台的音频资产内存预算
- 规划流式加载 vs 预加载资产策略
- 设计任何音频反应式视觉效果

Spawn the **primary engine specialist** in parallel (from `.claude/docs/technical-preferences.md` Engine Specialists) to validate the integration approach:
- Is the proposed audio middleware integration idiomatic for the engine? (e.g., Godot's built-in AudioStreamPlayer vs FMOD, Unity's Audio Mixer vs Wwise, Unreal's MetaSounds vs FMOD)
- Any engine-specific audio node/component patterns that should be used?
- Known audio system changes in the pinned engine version that affect the integration plan?
- Output: engine audio integration notes to merge with the technical-artist's plan
并行派生 **主引擎专家**（来自 `.claude/docs/technical-preferences.md` Engine Specialists）以验证集成方法：
- 提议的音频中间件集成对该引擎是否地道？（例如，Godot 的内置 AudioStreamPlayer vs FMOD、Unity 的 Audio Mixer vs Wwise、Unreal 的 MetaSounds vs FMOD）
- 是否有应使用的引擎特定音频节点/组件模式？
- 固定引擎版本中影响集成计划的已知音频系统变更？
- 输出：要与 technical-artist 计划合并的引擎音频集成备注

If no engine is configured, skip the specialist spawn.
如果未配置引擎，跳过专家派生。

### Step 4: Code Integration (gameplay-programmer)
### 步骤 4：代码集成（gameplay-programmer）
Spawn the `gameplay-programmer` agent to:
- Implement audio manager system or review existing
- Wire up audio events to gameplay triggers
- Implement adaptive music system (if specified)
- Set up audio occlusion/reverb zones
- Write unit tests for audio event triggers
派生 `gameplay-programmer` 智能体以：
- 实现音频管理器系统或审查现有系统
- 将音频事件连接到玩法触发器
- 实现自适应音乐系统（如果指定）
- 设置音频遮挡/混响区域
- 为音频事件触发器编写单元测试

4. **Compile the audio design document** combining all team outputs.
4. **编译音频设计文档**，合并所有团队输出。

5. **Save to** `design/audio/audio-[feature].md`.
5. **保存到** `design/audio/audio-[feature].md`。

   Note: If `design/audio/` does not exist, the sub-agent writing the document should create it (the directory will be created automatically when the file is written).
   注意：如果 `design/audio/` 不存在，写入文档的子智能体应创建它（写入文件时将自动创建目录）。

6. **Output a summary** with: audio event count, estimated asset count,
   implementation tasks, and any open questions between team members.
6. **输出摘要**，包含：音频事件计数、估计资产计数、
   实现任务以及团队成员之间的任何待决问题。

Verdict: **COMPLETE** — audio design document produced and team pipeline finished.
结论：**已完成** — 音频设计文档已生成且团队管线已完成。

If the pipeline stops because a dependency is unresolved (e.g., critical accessibility gap or missing GDD not resolved by the user):
如果管线因依赖未解决而停止（例如，关键可访问性缺口或用户未解决的缺失 GDD）：

Verdict: **BLOCKED** — [reason]
结论：**已阻塞** — [原因]

## File Write Protocol
## 文件写入协议

All file writes (audio design docs, SFX specs, implementation files) are delegated
to sub-agents spawned via Task. Each sub-agent enforces the "May I write to [path]?"
protocol. This orchestrator does not write files directly.
所有文件写入（音频设计文档、SFX 规范、实现文件）都委派给
通过 Task 派生的子智能体。每个子智能体强制执行 "May I write to [path]?"
协议。此编排器不直接写入文件。

## Next Steps
## 后续步骤

- Review the audio design doc with the audio-director before implementation begins.
- Use `/dev-story` to implement the audio manager and event system once the design is approved.
- Run `/asset-audit` after audio assets are created to verify naming and format compliance.
- 在实现开始之前与 audio-director 一起审查音频设计文档。
- 设计批准后使用 `/dev-story` 实现音频管理器和事件系统。
- 创建音频资产后运行 `/asset-audit` 验证命名和格式合规性。

## Error Recovery Protocol
## 错误恢复协议

If any spawned agent (via Task) returns BLOCKED, errors, or cannot complete:
如果任何派生的智能体（通过 Task）返回 BLOCKED、错误或无法完成：

1. **Surface immediately**: Report "[AgentName]: BLOCKED — [reason]" to the user before continuing to dependent phases
2. **Assess dependencies**: Check whether the blocked agent's output is required by subsequent phases. If yes, do not proceed past that dependency point without user input.
3. **Offer options** via AskUserQuestion with choices:
   - Skip this agent and note the gap in the final report
   - Retry with narrower scope
   - Stop here and resolve the blocker first
4. **Always produce a partial report** — output whatever was completed. Never discard work because one agent blocked.
1. **立即呈现**：在继续依赖阶段之前向用户报告 "[AgentName]: BLOCKED — [reason]"
2. **评估依赖**：检查后续阶段是否需要被阻塞智能体的输出。如果需要，在没有用户输入的情况下不要超过该依赖点继续。
3. **通过 AskUserQuestion 提供选项**，供选择：
   - 跳过此智能体并在最终报告中记录缺口
   - 以更窄的范围重试
   - 在此停止并先解决阻塞问题
4. **始终生成部分报告** — 输出已完成的任何内容。绝不因为一个智能体被阻塞而丢弃工作。

Common blockers:
- Input file missing (story not found, GDD absent) → redirect to the skill that creates it
- ADR status is Proposed → do not implement; run `/architecture-decision` first
- Scope too large → split into two stories via `/create-stories`
- Conflicting instructions between ADR and story → surface the conflict, do not guess
常见阻塞：
- 输入文件缺失（未找到故事、GDD 缺失）→ 重定向到创建它的技能
- ADR 状态为 Proposed → 不要实现；先运行 `/architecture-decision`
- 范围太大 → 通过 `/create-stories` 拆分为两个故事
- ADR 和故事之间的指令冲突 → 呈现冲突，不要猜测
