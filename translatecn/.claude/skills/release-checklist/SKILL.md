---
name: release-checklist
description: "Generates a comprehensive pre-release validation checklist covering build verification, certification requirements, store metadata, and launch readiness."
argument-hint: "[platform: pc|console|mobile|all]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---
---
名称：release-checklist
描述："生成全面的发布前验证清单，涵盖构建验证、认证要求、商店元数据和发布就绪。"
argument-hint："[platform: pc|console|mobile|all]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write
model：sonnet
---

> **Explicit invocation only**: This skill should only run when the user explicitly requests it with `/release-checklist`. Do not auto-invoke based on context matching.
> **仅显式调用**：此技能仅应在用户明确请求 `/release-checklist` 时运行。不要基于上下文匹配自动调用。

## Phase 1: Parse Arguments
## 阶段 1：解析参数

Read the argument for the target platform (`pc`, `console`, `mobile`, or `all`). If no platform is specified, default to `all`.
读取参数以获取目标平台（`pc`、`console`、`mobile` 或 `all`）。若未指定平台，默认为 `all`。

---

## Phase 2: Load Project Context
## 阶段 2：加载项目上下文

- Read `CLAUDE.md` for project context, version information, and platform targets.
- 读取 `CLAUDE.md` 以获取项目上下文、版本信息和平台目标。
- Read the current milestone from `production/milestones/` to understand what features and content should be included in this release.
- 读取 `production/milestones/` 中的当前里程碑以了解此版本应包含哪些功能和内容。

---

## Phase 3: Scan Codebase
## 阶段 3：扫描代码库

Scan for outstanding issues:
扫描未决问题：

- Count `TODO` comments
- 计数 `TODO` 注释
- Count `FIXME` comments
- 计数 `FIXME` 注释
- Count `HACK` comments
- 计数 `HACK` 注释
- Note their locations and severity
- 注明其位置和严重性

Check for test results in any test output directories or CI logs if available.
若可用，检查任何测试输出目录或 CI 日志中的测试结果。

---

## Phase 4: Generate the Release Checklist
## 阶段 4：生成发布清单

```markdown
## Release Checklist: [Version] -- [Platform]
Generated: [Date]
```
```markdown
## 发布清单：[版本] —— [平台]
生成：[日期]
```

```markdown
### Codebase Health
- TODO count: [N] ([list top 5 if many])
- FIXME count: [N] ([list all -- these are potential blockers])
- HACK count: [N] ([list all -- these need review])
```
```markdown
### 代码库健康
- TODO 计数：[N]（[若多则列出前 5]）
- FIXME 计数：[N]（[列出全部 —— 这些是潜在阻断项]）
- HACK 计数：[N]（[列出全部 —— 这些需要评审]）
```

```markdown
### Build Verification
- [ ] Clean build succeeds on all target platforms
- [ ] No compiler warnings (zero-warning policy)
- [ ] All assets included and loading correctly
- [ ] Build size within budget ([target size])
- [ ] Build version number correctly set ([version])
- [ ] Build is reproducible from tagged commit
```
```markdown
### 构建验证
- [ ] 所有目标平台上干净构建成功
- [ ] 无编译器警告（零警告策略）
- [ ] 所有资产已包含并正确加载
- [ ] 构建大小在预算内（[目标大小]）
- [ ] 构建版本号正确设置（[版本]）
- [ ] 构建可从标记的提交重现
```

```markdown
### Quality Gates
- [ ] Zero S1 (Critical) bugs
- [ ] Zero S2 (Major) bugs -- or documented exceptions with producer approval
- [ ] All critical path features tested and signed off by QA
- [ ] Performance within budgets:
  - [ ] Target FPS met on minimum spec hardware
  - [ ] Memory usage within budget
  - [ ] Load times within budget
  - [ ] No memory leaks over extended play sessions
- [ ] No regression from previous build
- [ ] Soak test passed (4+ hours continuous play)
```
```markdown
### 质量门禁
- [ ] 零 S1（关键）bug
- [ ] 零 S2（重大）bug —— 或制作人批准的记录例外
- [ ] 所有关键路径功能已测试并由 QA 签字
- [ ] 性能在预算内：
  - [ ] 最低规格硬件上达到目标 FPS
  - [ ] 内存使用在预算内
  - [ ] 加载时间在预算内
  - [ ] 长时间游玩会话无内存泄漏
- [ ] 与上一个构建无回归
- [ ] 浸泡测试通过（连续游玩 4+ 小时）
```

```markdown
### Content Complete
- [ ] All placeholder assets replaced with final versions
- [ ] All TODO/FIXME in content files resolved or documented
- [ ] All player-facing text proofread
- [ ] All text localization-ready (no hardcoded strings)
- [ ] Audio mix finalized and approved
- [ ] Credits complete and accurate
```
```markdown
### 内容完成
- [ ] 所有占位资产已替换为最终版本
- [ ] 内容文件中的所有 TODO/FIXME 已解决或记录
- [ ] 所有面向玩家的文本已校对
- [ ] 所有文本已准备好本地化（无硬编码字符串）
- [ ] 音频混音已最终确定并批准
- [ ] 制作人员名单完整且准确
```

Add platform-specific sections based on the argument:
基于参数添加平台特定章节：

**For `pc`:**
**对于 `pc`：**
```markdown
### Platform Requirements: PC
- [ ] Minimum and recommended specs verified and documented
- [ ] Keyboard+mouse controls fully functional
- [ ] Controller support tested (Xbox, PlayStation, generic)
- [ ] Resolution scaling tested (1080p, 1440p, 4K, ultrawide)
- [ ] Windowed, borderless, and fullscreen modes working
- [ ] Graphics settings save and load correctly
- [ ] Steam/Epic/GOG SDK integrated and tested
- [ ] Achievements functional
- [ ] Cloud saves functional
- [ ] Steam Deck compatibility verified (if targeting)
```
```markdown
### 平台要求：PC
- [ ] 最低和推荐规格已验证并记录
- [ ] 键盘+鼠标控制完全功能
- [ ] 控制器支持已测试（Xbox、PlayStation、通用）
- [ ] 分辨率缩放已测试（1080p、1440p、4K、超宽）
- [ ] 窗口、无边框和全屏模式工作正常
- [ ] 图形设置正确保存和加载
- [ ] Steam/Epic/GOG SDK 已集成并测试
- [ ] 成就功能正常
- [ ] 云存档功能正常
- [ ] Steam Deck 兼容性已验证（若目标）
```

**For `console`:**
**对于 `console`：**
```markdown
### Platform Requirements: Console
- [ ] TRC/TCR/Lotcheck requirements checklist complete
- [ ] Platform-specific controller prompts display correctly
- [ ] Suspend/resume works correctly
- [ ] User switching handled properly
- [ ] Network connectivity loss handled gracefully
- [ ] Storage full scenario handled
- [ ] Parental controls respected
- [ ] Platform-specific achievement/trophy integration tested
- [ ] First-party certification submission prepared
```
```markdown
### 平台要求：主机
- [ ] TRC/TCR/Lotcheck 要求清单完成
- [ ] 平台特定控制器提示正确显示
- [ ] 暂停/恢复正常工作
- [ ] 用户切换处理正确
- [ ] 网络连接丢失优雅处理
- [ ] 存储满场景处理
- [ ] 家长控制受到尊重
- [ ] 平台特定成就/奖杯集成已测试
- [ ] 第一方认证提交已准备
```

**For `mobile`:**
**对于 `mobile`：**
```markdown
### Platform Requirements: Mobile
- [ ] App store guidelines compliance verified
- [ ] All required device permissions justified and documented
- [ ] Privacy policy linked and accurate
- [ ] Data safety/nutrition labels completed
- [ ] Touch controls tested on multiple screen sizes
- [ ] Battery usage within acceptable range
- [ ] Background behavior correct (pause, resume, terminate)
- [ ] Push notification permissions handled correctly
- [ ] In-app purchase flow tested (if applicable)
- [ ] App size within store limits
```
```markdown
### 平台要求：移动
- [ ] 应用商店指南合规已验证
- [ ] 所有所需设备权限已论证并记录
- [ ] 隐私政策已链接且准确
- [ ] 数据安全/营养标签已完成
- [ ] 触摸控制已在多种屏幕尺寸上测试
- [ ] 电池使用在可接受范围内
- [ ] 后台行为正确（暂停、恢复、终止）
- [ ] 推送通知权限正确处理
- [ ] 应用内购买流程已测试（若适用）
- [ ] 应用大小在商店限制内
```

**Store and launch sections (all platforms):**
**商店和发布章节（所有平台）：**
```markdown
### Store / Distribution
- [ ] Store page metadata complete and proofread
  - [ ] Short description
  - [ ] Long description
  - [ ] Feature list
  - [ ] System requirements (PC)
- [ ] Screenshots up to date and per-platform resolution requirements met
- [ ] Trailers up to date
- [ ] Key art and capsule images current
- [ ] Age rating obtained and configured:
  - [ ] ESRB
  - [ ] PEGI
  - [ ] Other regional ratings as required
- [ ] Legal notices, EULA, and privacy policy in place
- [ ] Third-party license attributions complete
- [ ] Pricing configured for all regions
```
```markdown
### 商店 / 分发
- [ ] 商店页面元数据完整并已校对
  - [ ] 简短描述
  - [ ] 长描述
  - [ ] 功能列表
  - [ ] 系统要求（PC）
- [ ] 截图最新且符合各平台分辨率要求
- [ ] 预告片最新
- [ ] 主视觉和胶囊图片最新
- [ ] 年龄评级已获得并配置：
  - [ ] ESRB
  - [ ] PEGI
  - [ ] 其他所需地区评级
- [ ] 法律声明、EULA 和隐私政策到位
- [ ] 第三方许可归属完整
- [ ] 所有地区定价已配置
```

```markdown
### Launch Readiness
- [ ] Analytics / telemetry verified and receiving data
- [ ] Crash reporting configured and dashboard accessible
- [ ] Day-one patch prepared and tested (if needed)
- [ ] On-call team schedule set for first 72 hours
- [ ] Community launch announcements drafted
- [ ] Press/influencer keys prepared for distribution
- [ ] Support team briefed on known issues and FAQ
- [ ] Rollback plan documented (if critical issues found post-launch)
```
```markdown
### 发布就绪
- [ ] 分析/遥测已验证并接收数据
- [ ] 崩溃报告已配置且仪表板可访问
- [ ] 首日补丁已准备并测试（若需要）
- [ ] 前 72 小时的值班团队计划已设定
- [ ] 社区发布公告已起草
- [ ] 媒体/影响者密钥已准备分发
- [ ] 支持团队已 briefing 已知问题和 FAQ
- [ ] 回滚计划已记录（若发布后发现关键问题）
```

```markdown
### Go / No-Go: [READY / NOT READY]

**Rationale:**
[Summary of readiness assessment. List any blocking items that must be
resolved before launch. If NOT READY, list the specific items that need
resolution and estimated time to address them.]

**Sign-offs Required:**
- [ ] QA Lead
- [ ] Technical Director
- [ ] Producer
- [ ] Creative Director
```
```markdown
### 通过 / 不通过：[READY / NOT READY]

**理由：**
[就绪评估摘要。列出发布前必须解决的任何阻断项。
若 NOT READY，列出需要解决的具体项及估计解决时间。]

**需要的签字：**
- [ ] QA 主管
- [ ] 技术总监
- [ ] 制作人
- [ ] 创意总监
```

---

## Phase 5: Save Checklist
## 阶段 5：保存清单

Present the checklist to the user with: total checklist items, number of known blockers (FIXME/HACK counts, known bugs).
向用户呈现清单，包含：清单总项数、已知阻断项数（FIXME/HACK 计数、已知 bug）。

Ask: "May I write this to `production/releases/release-checklist-[version].md`?"
询问："我可以将此写入 `production/releases/release-checklist-[version].md` 吗？"

If yes, write the file, creating the directory if needed.
若是，写入文件，必要时创建目录。

---

## Phase 6: Next Steps
## 阶段 6：后续步骤

- Run `/gate-check` for a formal phase gate verdict before proceeding to release.
- 在继续发布前运行 `/gate-check` 获取正式阶段门禁裁定。
- Coordinate final sign-offs via `/team-release`.
- 通过 `/team-release` 协调最终签字。
