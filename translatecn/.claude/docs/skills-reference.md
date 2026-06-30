# Available Skills (Slash Commands)
# 可用技能（斜杠命令）

73 slash commands organized by phase. Type `/` in Claude Code to access any of them.
73 个按阶段组织的斜杠命令。在 Claude Code 中输入 `/` 即可访问。

## Onboarding & Navigation
## 入职与导航

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/start` | First-time onboarding — asks where you are, then guides you to the right workflow |
| `/start` | 首次入职 — 询问你处于哪个阶段，然后引导你到正确的工作流 |
| `/help` | Context-aware "what do I do next?" — reads current stage and surfaces the required next step |
| `/help` | 上下文感知的"下一步该做什么？" — 读取当前阶段并呈现必需的下一步 |
| `/project-stage-detect` | Full project audit — detect phase, identify existence gaps, recommend next steps |
| `/project-stage-detect` | 完整项目审计 — 检测阶段、识别存在差距、推荐下一步 |
| `/setup-engine` | Configure engine + version, detect knowledge gaps, populate version-aware reference docs |
| `/setup-engine` | 配置引擎和版本、检测知识缺口、填充版本感知参考文档 |
| `/adopt` | Brownfield format audit — checks internal structure of existing GDDs/ADRs/stories, produces migration plan |
| `/adopt` | 棕地格式审计 — 检查现有 GDD/ADR/story 的内部结构，生成迁移计划 |

## Game Design
## 游戏设计

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/brainstorm` | Guided ideation using professional studio methods (MDA, SDT, Bartle, verb-first) |
| `/brainstorm` | 使用专业工作室方法（MDA、SDT、Bartle、动词优先）的引导式创意 |
| `/map-systems` | Decompose game concept into systems, map dependencies, prioritize design order |
| `/map-systems` | 将游戏概念分解为系统、映射依赖、确定设计优先级 |
| `/design-system` | Guided, section-by-section GDD authoring for a single game system |
| `/design-system` | 针对单个游戏系统的引导式、逐章节 GDD 编写 |
| `/quick-design` | Lightweight design spec for small changes — tuning, tweaks, minor additions |
| `/quick-design` | 用于小变更的轻量设计规格 — 调参、微调、小型新增 |
| `/review-all-gdds` | Cross-GDD consistency and game design holism review across all design docs |
| `/review-all-gdds` | 跨所有设计文档的 GDD 一致性与游戏设计整体性评审 |
| `/propagate-design-change` | When a GDD is revised, find affected ADRs and produce an impact report |
| `/propagate-design-change` | 当 GDD 修订时，查找受影响的 ADR 并生成影响报告 |

## Art & Assets
## 美术与资源

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/art-bible` | Guided, section-by-section Art Bible authoring — creates visual identity spec before asset production begins |
| `/art-bible` | 引导式、逐章节美术圣经编写 — 在资源生产开始前创建视觉身份规格 |
| `/asset-spec` | Generate per-asset visual specifications and AI generation prompts from GDDs, level docs, or character profiles |
| `/asset-spec` | 从 GDD、关卡文档或角色档案生成逐资源视觉规格和 AI 生成提示词 |
| `/asset-audit` | Audit assets for naming conventions, file size budgets, and pipeline compliance |
| `/asset-audit` | 审计资源的命名约定、文件大小预算和流水线合规性 |

## UX & Interface Design
## UX 与界面设计

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/ux-design` | Guided section-by-section UX spec authoring (screen/flow, HUD, or pattern library) |
| `/ux-design` | 引导式逐章节 UX 规格编写（屏幕/流程、HUD 或模式库） |
| `/ux-review` | Validate UX specs for GDD alignment, accessibility, and pattern compliance |
| `/ux-review` | 验证 UX 规格的 GDD 对齐、无障碍性和模式合规性 |

## Architecture
## 架构

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/create-architecture` | Guided authoring of the master architecture document |
| `/create-architecture` | 引导式编写主架构文档 |
| `/architecture-decision` | Create an Architecture Decision Record (ADR) |
| `/architecture-decision` | 创建架构决策记录（ADR） |
| `/architecture-review` | Validate all ADRs for completeness, dependency ordering, and GDD coverage |
| `/architecture-review` | 验证所有 ADR 的完整性、依赖顺序和 GDD 覆盖 |
| `/create-control-manifest` | Generate flat programmer rules sheet from accepted ADRs |
| `/create-control-manifest` | 从已接受 ADR 生成扁平程序员规则表 |

## Stories & Sprints
## Story 与冲刺

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/create-epics` | Translate GDDs + ADRs into epics — one per architectural module |
| `/create-epics` | 将 GDD + ADR 转换为 epic — 每个架构模块一个 |
| `/create-stories` | Break a single epic into implementable story files |
| `/create-stories` | 将单个 epic 拆分为可实现的 story 文件 |
| `/dev-story` | Read a story and implement it — routes to the correct programmer agent |
| `/dev-story` | 读取 story 并实现 — 路由至正确的程序员智能体 |
| `/sprint-plan` | Generate or update a sprint plan; initializes sprint-status.yaml |
| `/sprint-plan` | 生成或更新冲刺计划；初始化 sprint-status.yaml |
| `/sprint-status` | Fast 30-line sprint snapshot (reads sprint-status.yaml) |
| `/sprint-status` | 快速 30 行冲刺快照（读取 sprint-status.yaml） |
| `/story-readiness` | Validate a story is implementation-ready before pickup (READY/NEEDS WORK/BLOCKED) |
| `/story-readiness` | 在领取前验证 story 是否已准备好实现（READY/NEEDS WORK/BLOCKED） |
| `/story-done` | 8-phase completion review after implementation; updates story file, surfaces next story |
| `/story-done` | 实现后的 8 阶段完成评审；更新 story 文件、呈现下一个 story |
| `/estimate` | Structured effort estimate with complexity, dependencies, and risk breakdown |
| `/estimate` | 带复杂度、依赖和风险分解的结构化工作量估算 |

## Reviews & Analysis
## 评审与分析

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/design-review` | Review a game design document for completeness and consistency |
| `/design-review` | 评审游戏设计文档的完整性和一致性 |
| `/code-review` | Architectural code review for a file or changeset |
| `/code-review` | 针对文件或变更集的架构代码评审 |
| `/balance-check` | Analyze game balance data, formulas, and config — flag outliers |
| `/balance-check` | 分析游戏平衡数据、公式和配置 — 标记异常值 |
| `/content-audit` | Audit GDD-specified content counts against implemented content |
| `/content-audit` | 审计 GDD 规定内容数量与已实现内容的对比 |
| `/scope-check` | Analyze feature or sprint scope against original plan, flag scope creep |
| `/scope-check` | 相对原始计划分析功能或冲刺范围，标记范围蔓延 |
| `/perf-profile` | Structured performance profiling with bottleneck identification |
| `/perf-profile` | 带瓶颈识别的结构化性能分析 |
| `/tech-debt` | Scan, track, prioritize, and report on technical debt |
| `/tech-debt` | 扫描、跟踪、优先处理并报告技术债 |
| `/gate-check` | Validate readiness to advance between development phases (PASS/CONCERNS/FAIL) |
| `/gate-check` | 验证在开发阶段间推进的就绪度（PASS/CONCERNS/FAIL） |
| `/consistency-check` | Scan all GDDs against the entity registry to detect cross-document inconsistencies (stats, names, rules that contradict each other) |
| `/consistency-check` | 相对实体注册表扫描所有 GDD 以检测跨文档不一致（相互冲突的数值、名称、规则） |
| `/security-audit` | Audit the game for security vulnerabilities: save tampering, cheat vectors, network exploits, data exposure, and input validation gaps |
| `/security-audit` | 审计游戏安全漏洞：存档篡改、作弊向量、网络漏洞、数据暴露和输入验证缺口 |

## QA & Testing
## QA 与测试

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/qa-plan` | Generate a QA test plan for a sprint or feature |
| `/qa-plan` | 为冲刺或功能生成 QA 测试计划 |
| `/smoke-check` | Run critical path smoke test gate before QA hand-off |
| `/smoke-check` | 在 QA 交接前运行关键路径冒烟测试门禁 |
| `/soak-test` | Generate a soak test protocol for extended play sessions |
| `/soak-test` | 为长时间游玩会话生成压力测试协议 |
| `/regression-suite` | Map test coverage to GDD critical paths, identify fixed bugs without regression tests |
| `/regression-suite` | 将测试覆盖率映射到 GDD 关键路径，识别已修复但无回归测试的 bug |
| `/test-setup` | Scaffold the test framework and CI/CD pipeline for the project's engine |
| `/test-setup` | 为项目引擎搭建测试框架和 CI/CD 流水线 |
| `/test-helpers` | Generate engine-specific test helper libraries for the test suite |
| `/test-helpers` | 为测试套件生成引擎特定的测试辅助库 |
| `/test-evidence-review` | Quality review of test files and manual evidence documents |
| `/test-evidence-review` | 测试文件和手动证据文档的质量评审 |
| `/test-flakiness` | Detect non-deterministic (flaky) tests from CI run logs |
| `/test-flakiness` | 从 CI 运行日志检测非确定性（不稳定）测试 |
| `/skill-test` | Validate skill files for structural compliance and behavioral correctness |
| `/skill-test` | 验证技能文件的结构合规性和行为正确性 |
| `/skill-improve` | Improve a skill using a test-fix-retest loop — diagnose, propose fix, rewrite, verify |
| `/skill-improve` | 通过测试-修复-重测循环改进技能 — 诊断、提出修复、重写、验证 |

## Production
## 生产

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/milestone-review` | Review milestone progress and generate status report |
| `/milestone-review` | 评审里程碑进度并生成状态报告 |
| `/retrospective` | Run a structured sprint or milestone retrospective |
| `/retrospective` | 运行结构化的冲刺或里程碑回顾 |
| `/bug-report` | Create a structured bug report |
| `/bug-report` | 创建结构化 bug 报告 |
| `/bug-triage` | Read all open bugs, re-evaluate priority vs. severity, assign owner and label |
| `/bug-triage` | 读取所有开放 bug、重新评估优先级与严重性、分配所有者和标签 |
| `/reverse-document` | Generate design or architecture docs from existing implementation |
| `/reverse-document` | 从现有实现生成设计或架构文档 |
| `/playtest-report` | Generate a structured playtest report or analyze existing playtest notes |
| `/playtest-report` | 生成结构化试玩报告或分析现有试玩笔记 |

## Release
## 发布

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/release-checklist` | Generate and validate a pre-release checklist for the current build |
| `/release-checklist` | 为当前构建生成并验证发布前检查清单 |
| `/launch-checklist` | Complete launch readiness validation across all departments |
| `/launch-checklist` | 跨所有部门完成上线就绪度验证 |
| `/changelog` | Auto-generate changelog from git commits and sprint data |
| `/changelog` | 从 git 提交和冲刺数据自动生成更新日志 |
| `/patch-notes` | Generate player-facing patch notes from git history and internal data |
| `/patch-notes` | 从 git 历史和内部数据生成面向玩家的补丁说明 |
| `/hotfix` | Emergency fix workflow with audit trail, bypassing normal sprint process |
| `/hotfix` | 带审计跟踪的紧急修复工作流，绕过常规冲刺流程 |
| `/day-one-patch` | Prepare a focused day-one patch for known issues discovered after gold master but before or at public launch |
| `/day-one-patch` | 为金盘后、公开上线前或上线时发现的已知问题准备有针对性的首日补丁 |

## Creative & Content
## 创意与内容

| Command | Purpose |
| 命令 | 用途 |
|---------|---------|
| `/prototype` | Concept prototype — throwaway build right after brainstorm to validate core idea (Phase 1) |
| `/prototype` | 概念原型 — 头脑风暴后立即构建的一次性产物，用于验证核心想法（阶段 1） |
| `/vertical-slice` | Pre-Production validation — production-quality end-to-end build before committing to Production (Phase 4) |
| `/vertical-slice` | 前期制作验证 — 在承诺进入 Production 前的生产质量端到端构建（阶段 4） |
| `/onboard` | Generate contextual onboarding document for a new contributor or agent |
| `/onboard` | 为新贡献者或智能体生成上下文化的入职文档 |
| `/localize` | Localization workflow: string extraction, validation, translation readiness |
| `/localize` | 本地化工作流：字符串提取、验证、翻译就绪度 |

## Team Orchestration
## 团队编排

Coordinate multiple agents on a single feature area:
在单个功能领域协调多个智能体：

| Command | Coordinates |
| 命令 | 协调 |
|---------|-------------|
| `/team-combat` | game-designer + gameplay-programmer + ai-programmer + technical-artist + sound-designer + qa-tester |
| `/team-combat` | game-designer + gameplay-programmer + ai-programmer + technical-artist + sound-designer + qa-tester |
| `/team-narrative` | narrative-director + writer + world-builder + level-designer |
| `/team-narrative` | narrative-director + writer + world-builder + level-designer |
| `/team-ui` | ux-designer + ui-programmer + art-director + accessibility-specialist |
| `/team-ui` | ux-designer + ui-programmer + art-director + accessibility-specialist |
| `/team-release` | release-manager + qa-lead + devops-engineer + producer |
| `/team-release` | release-manager + qa-lead + devops-engineer + producer |
| `/team-polish` | performance-analyst + technical-artist + sound-designer + qa-tester |
| `/team-polish` | performance-analyst + technical-artist + sound-designer + qa-tester |
| `/team-audio` | audio-director + sound-designer + technical-artist + gameplay-programmer |
| `/team-audio` | audio-director + sound-designer + technical-artist + gameplay-programmer |
| `/team-level` | level-designer + narrative-director + world-builder + art-director + systems-designer + qa-tester |
| `/team-level` | level-designer + narrative-director + world-builder + art-director + systems-designer + qa-tester |
| `/team-live-ops` | live-ops-designer + economy-designer + community-manager + analytics-engineer |
| `/team-live-ops` | live-ops-designer + economy-designer + community-manager + analytics-engineer |
| `/team-qa` | qa-lead + qa-tester + gameplay-programmer + producer |
| `/team-qa` | qa-lead + qa-tester + gameplay-programmer + producer |
