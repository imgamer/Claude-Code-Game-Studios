# Agent Coordination and Delegation Map
# 智能体协调与委派映射

## Organizational Hierarchy
## 组织层级

```
                           [Human Developer]
                                 |
                 +---------------+---------------+
                 |               |               |
         creative-director  technical-director  producer
                 |               |               |
        +--------+--------+     |        (coordinates all)
        |        |        |     |
  game-designer art-dir  narr-dir  lead-programmer  qa-lead  audio-dir
        |        |        |         |                |        |
     +--+--+     |     +--+--+  +--+--+--+--+--+   |        |
     |  |  |     |     |     |  |  |  |  |  |  |   |        |
    sys lvl eco  ta   wrt  wrld gp ep  ai net tl ui qa-t    snd
                                 |
                             +---+---+
                             |       |
                          perf-a   devops   analytics

  Additional Leads (report to producer/directors):
    release-manager         -- Release pipeline, versioning, deployment
    localization-lead       -- i18n, string tables, translation pipeline
    prototyper              -- Rapid throwaway prototypes, concept validation
    security-engineer       -- Anti-cheat, exploits, data privacy, network security
    accessibility-specialist -- WCAG, colorblind, remapping, text scaling
    live-ops-designer       -- Seasons, events, battle passes, retention, live economy
    community-manager       -- Patch notes, player feedback, crisis comms

  Engine Specialists (use the SET matching your engine):
    unreal-specialist  -- UE5 lead: Blueprint/C++, GAS overview, UE subsystems
      ue-gas-specialist         -- GAS: abilities, effects, attributes, tags, prediction
      ue-blueprint-specialist   -- Blueprint: BP/C++ boundary, graph standards, optimization
      ue-replication-specialist -- Networking: replication, RPCs, prediction, bandwidth
      ue-umg-specialist         -- UI: UMG, CommonUI, widget hierarchy, data binding

    unity-specialist   -- Unity lead: MonoBehaviour/DOTS, Addressables, URP/HDRP
      unity-dots-specialist         -- DOTS/ECS: Jobs, Burst, hybrid renderer
      unity-shader-specialist       -- Shaders: Shader Graph, VFX Graph, SRP customization
      unity-addressables-specialist -- Assets: async loading, bundles, memory, CDN
      unity-ui-specialist           -- UI: UI Toolkit, UGUI, UXML/USS, data binding

    godot-specialist   -- Godot 4 lead: GDScript, node/scene, signals, resources
      godot-gdscript-specialist    -- GDScript: static typing, patterns, signals, performance
      godot-csharp-specialist      -- C#: .NET patterns, [Signal] delegates, async, type-safe node access
      godot-shader-specialist      -- Shaders: Godot shading language, visual shaders, VFX
      godot-gdextension-specialist -- Native: C++/Rust bindings, GDExtension, build systems
```

### Legend
### 图例
```
sys  = systems-designer       gp  = gameplay-programmer
lvl  = level-designer         ep  = engine-programmer
eco  = economy-designer       ai  = ai-programmer
ta   = technical-artist       net = network-programmer
wrt  = writer                 tl  = tools-programmer
wrld = world-builder          ui  = ui-programmer
snd  = sound-designer         qa-t = qa-tester
narr-dir = narrative-director perf-a = performance-analyst
art-dir = art-director
```

## Delegation Rules
## 委派规则

### Who Can Delegate to Whom
### 谁可以委派给谁

| From | Can Delegate To |
| 来源 | 可委派给 |
|------|----------------|
| creative-director | game-designer, art-director, audio-director, narrative-director |
| creative-director | game-designer、art-director、audio-director、narrative-director |
| technical-director | lead-programmer, devops-engineer, performance-analyst, technical-artist (technical decisions) |
| technical-director | lead-programmer、devops-engineer、performance-analyst、technical-artist（技术决策） |
| producer | Any agent (task assignment within their domain only) |
| producer | 任何智能体（仅限在其领域内进行任务分配） |
| game-designer | systems-designer, level-designer, economy-designer |
| game-designer | systems-designer、level-designer、economy-designer |
| lead-programmer | gameplay-programmer, engine-programmer, ai-programmer, network-programmer, tools-programmer, ui-programmer |
| lead-programmer | gameplay-programmer、engine-programmer、ai-programmer、network-programmer、tools-programmer、ui-programmer |
| art-director | technical-artist, ux-designer |
| art-director | technical-artist、ux-designer |
| audio-director | sound-designer |
| audio-director | sound-designer |
| narrative-director | writer, world-builder |
| narrative-director | writer、world-builder |
| qa-lead | qa-tester |
| qa-lead | qa-tester |
| release-manager | devops-engineer (release builds), qa-lead (release testing) |
| release-manager | devops-engineer（发布构建）、qa-lead（发布测试） |
| localization-lead | writer (string review), ui-programmer (text fitting) |
| localization-lead | writer（字符串评审）、ui-programmer（文本适配） |
| prototyper | (works independently, reports findings to producer and relevant leads) |
| prototyper | （独立工作，向 producer 和相关主管汇报发现） |
| security-engineer | network-programmer (security review), lead-programmer (secure patterns) |
| security-engineer | network-programmer（安全评审）、lead-programmer（安全模式） |
| accessibility-specialist | ux-designer (accessible patterns), ui-programmer (implementation), qa-tester (a11y testing) |
| accessibility-specialist | ux-designer（无障碍模式）、ui-programmer（实现）、qa-tester（无障碍测试） |
| [engine]-specialist | engine sub-specialists (delegates subsystem-specific work) |
| [engine]-specialist | 引擎子专家（委派子系统特定工作） |
| [engine] sub-specialists | (advises all programmers on engine subsystem patterns and optimization) |
| [engine] 子专家 | （为所有程序员提供引擎子系统模式和优化建议） |
| live-ops-designer | economy-designer (live economy), community-manager (event comms), analytics-engineer (engagement metrics) |
| live-ops-designer | economy-designer（在线经济）、community-manager（活动沟通）、analytics-engineer（参与度指标） |
| community-manager | (works with producer for approval, release-manager for patch note timing) |
| community-manager | （与 producer 协作审批，与 release-manager 协调补丁说明时机） |

### Escalation Paths
### 升级路径

| Situation | Escalate To |
| 情形 | 升级至 |
|-----------|------------|
| Two designers disagree on a mechanic | game-designer |
| 两位设计师对某个机制存在分歧 | game-designer |
| Game design vs narrative conflict | creative-director |
| 游戏设计与叙事冲突 | creative-director |
| Game design vs technical feasibility | producer (facilitates), then creative-director + technical-director |
| 游戏设计与技术可行性冲突 | producer（协调），然后 creative-director + technical-director |
| Art vs audio tonal conflict | creative-director |
| 美术与音频基调冲突 | creative-director |
| Code architecture disagreement | technical-director |
| 代码架构分歧 | technical-director |
| Cross-system code conflict | lead-programmer, then technical-director |
| 跨系统代码冲突 | lead-programmer，然后 technical-director |
| Schedule conflict between departments | producer |
| 部门间进度冲突 | producer |
| Scope exceeds capacity | producer, then creative-director for cuts |
| 范围超出容量 | producer，然后由 creative-director 决定裁剪 |
| Quality gate disagreement | qa-lead, then technical-director |
| 质量门禁分歧 | qa-lead，然后 technical-director |
| Performance budget violation | performance-analyst flags, technical-director decides |
| 性能预算超限 | performance-analyst 标记，technical-director 决策 |

## Common Workflow Patterns
## 常见工作流模式

### Pattern 1: New Feature (Full Pipeline)
### 模式 1：新功能（完整流水线）

```
1. creative-director  -- Approves feature concept aligns with vision
2. game-designer      -- Creates design document with full spec
3. producer           -- Schedules work, identifies dependencies
4. lead-programmer    -- Designs code architecture, creates interface sketch
5. [specialist-programmer] -- Implements the feature
6. technical-artist   -- Implements visual effects (if needed)
7. writer             -- Creates text content (if needed)
8. sound-designer     -- Creates audio event list (if needed)
9. qa-tester          -- Writes test cases
10. qa-lead           -- Reviews and approves test coverage
11. lead-programmer   -- Code review
12. qa-tester         -- Executes tests
13. producer          -- Marks task complete
```

### Pattern 2: Bug Fix
### 模式 2：Bug 修复

```
1. qa-tester          -- Files bug report with /bug-report
2. qa-lead            -- Triages severity and priority
3. producer           -- Assigns to sprint (if not S1)
4. lead-programmer    -- Identifies root cause, assigns to programmer
5. [specialist-programmer] -- Fixes the bug
6. lead-programmer    -- Code review
7. qa-tester          -- Verifies fix and runs regression
8. qa-lead            -- Closes bug
```

### Pattern 3: Balance Adjustment
### 模式 3：平衡性调整

```
1. analytics-engineer -- Identifies imbalance from data (or player reports)
2. game-designer      -- Evaluates the issue against design intent
3. economy-designer   -- Models the adjustment
4. game-designer      -- Approves the new values
5. [data file update] -- Change configuration values
6. qa-tester          -- Regression test affected systems
7. analytics-engineer -- Monitor post-change metrics
```

### Pattern 4: New Area/Level
### 模式 4：新区域/关卡

```
1. narrative-director -- Defines narrative purpose and beats for the area
2. world-builder      -- Creates lore and environmental context
3. level-designer     -- Designs layout, encounters, pacing
4. game-designer      -- Reviews mechanical design of encounters
5. art-director       -- Defines visual direction for the area
6. audio-director     -- Defines audio direction for the area
7. [implementation by relevant programmers and artists]
8. writer             -- Creates area-specific text content
9. qa-tester          -- Tests the complete area
```

### Pattern 5: Sprint Cycle
### 模式 5：冲刺周期

```
1. producer           -- Plans sprint with /sprint-plan new
2. [All agents]       -- Execute assigned tasks
3. producer           -- Daily status with /sprint-plan status
4. qa-lead            -- Continuous testing during sprint
5. lead-programmer    -- Continuous code review during sprint
6. producer           -- Sprint retrospective with post-sprint hook
7. producer           -- Plans next sprint incorporating learnings
```

### Pattern 6: Milestone Checkpoint
### 模式 6：里程碑检查点

```
1. producer           -- Runs /milestone-review
2. creative-director  -- Reviews creative progress
3. technical-director -- Reviews technical health
4. qa-lead            -- Reviews quality metrics
5. producer           -- Facilitates go/no-go discussion
6. [All directors]    -- Agree on scope adjustments if needed
7. producer           -- Documents decisions and updates plans
```

### Pattern 7: Release Pipeline
### 模式 7：发布流水线

```text
1. producer             -- Declares release candidate, confirms milestone criteria met
2. release-manager      -- Cuts release branch, generates /release-checklist
3. qa-lead              -- Runs full regression, signs off on quality
4. localization-lead    -- Verifies all strings translated, text fitting passes
5. performance-analyst  -- Confirms performance benchmarks within targets
6. devops-engineer      -- Builds release artifacts, runs deployment pipeline
7. release-manager      -- Generates /changelog, tags release, creates release notes
8. technical-director   -- Final sign-off on major releases
9. release-manager      -- Deploys and monitors for 48 hours
10. producer            -- Marks release complete
```

### Pattern 8: Concept Prototype (early — before GDDs)
### 模式 8：概念原型（早期 — 在 GDD 之前）

```text
1. game-designer        -- Defines the hypothesis and success criteria
2. prototyper           -- Scaffolds concept prototype with /prototype
3. prototyper           -- Builds minimal implementation (1-3 days)
4. game-designer        -- Evaluates prototype against criteria
5. prototyper           -- Documents findings in REPORT.md
6. creative-director    -- PROCEED / PIVOT / KILL decision (full mode only)
7. game-designer        -- Informs GDD writing with prototype learnings if PROCEED
```

### Pattern 8b: Vertical Slice (pre-production — after GDDs and architecture)
### 模式 8b：垂直切片（前期制作 — 在 GDD 和架构之后）

```text
1. game-designer        -- Confirms slice scope against GDDs
2. prototyper           -- Builds production-quality end-to-end build with /vertical-slice
3. prototyper           -- Conducts internal playtest sessions (minimum 1)
4. prototyper           -- Documents findings in REPORT.md
5. creative-director    -- Go/no-go decision on proceeding to Production (full mode)
6. producer             -- Schedules Production epics/sprints if PROCEED
```

### Pattern 9: Live Event / Season Launch
### 模式 9：在线活动 / 赛季上线

```text
1. live-ops-designer     -- Designs event/season content, rewards, schedule
2. game-designer         -- Validates gameplay mechanics for event
3. economy-designer      -- Balances event economy and reward values
4. narrative-director    -- Provides seasonal narrative theme
5. writer                -- Creates event descriptions and lore
6. producer              -- Schedules implementation work
7. [implementation by relevant programmers]
8. qa-lead               -- Test event flow end-to-end
9. community-manager     -- Drafts event announcement and patch notes
10. release-manager      -- Deploys event content
11. analytics-engineer   -- Monitors event participation and metrics
12. live-ops-designer    -- Post-event analysis and learnings
```

## Cross-Domain Communication Protocols
## 跨领域沟通协议

### Design Change Notification
### 设计变更通知

When a design document changes, the game-designer must notify:
当设计文档变更时，game-designer 必须通知：
- lead-programmer (implementation impact)
- lead-programmer（实现影响）
- qa-lead (test plan update needed)
- qa-lead（需要更新测试计划）
- producer (schedule impact assessment)
- producer（进度影响评估）
- Relevant specialist agents depending on the change
- 根据变更内容通知相关专家智能体

### Architecture Change Notification
### 架构变更通知

When an ADR is created or modified, the technical-director must notify:
当创建或修改 ADR 时，technical-director 必须通知：
- lead-programmer (code changes needed)
- lead-programmer（需要修改代码）
- All affected specialist programmers
- 所有受影响的专家程序员
- qa-lead (testing strategy may change)
- qa-lead（测试策略可能变更）
- producer (schedule impact)
- producer（进度影响）

### Asset Standard Change Notification
### 资源标准变更通知

When the art bible or asset standards change, the art-director must notify:
当美术圣经或资源标准变更时，art-director 必须通知：
- technical-artist (pipeline changes)
- technical-artist（流水线变更）
- All content creators working with affected assets
- 所有使用受影响资源的内容创作者
- devops-engineer (if build pipeline is affected)
- devops-engineer（如构建流水线受影响）

## Anti-Patterns to Avoid
## 应避免的反模式

1. **Bypassing the hierarchy**: A specialist agent should never make decisions
   that belong to their lead without consultation.
1. **绕过层级**：专家智能体绝不应在未咨询的情况下做出属于其主管的决策。
2. **Cross-domain implementation**: An agent should never modify files outside
   their designated area without explicit delegation from the relevant owner.
2. **跨领域实现**：智能体绝不应在未获得相关所有者明确委派的情况下修改其指定区域之外的文件。
3. **Shadow decisions**: All decisions must be documented. Verbal agreements
   without written records lead to contradictions.
3. **影子决策**：所有决策必须留有记录。没有书面记录的口头协议会导致矛盾。
4. **Monolithic tasks**: Every task assigned to an agent should be completable
   in 1-3 days. If it is larger, it must be broken down first.
4. **单体任务**：分配给智能体的每个任务都应能在 1-3 天内完成。如果任务过大，必须先拆分。
5. **Assumption-based implementation**: If a spec is ambiguous, the implementer
   must ask the specifier rather than guessing. Wrong guesses are more expensive
   than a question.
5. **基于假设的实现**：如果规格不明确，实现者必须询问规格编写者而非猜测。错误的猜测比提问成本更高。
