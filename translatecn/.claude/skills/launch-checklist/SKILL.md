---
name: launch-checklist
description: "Complete launch readiness validation covering every department: code, content, store, marketing, community, infrastructure, legal, and go/no-go sign-offs."
argument-hint: "[launch-date or 'dry-run']"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write
model: sonnet
---
---
名称：launch-checklist
描述："完整的发布就绪验证，涵盖每个部门：代码、内容、商店、营销、社区、基础设施、法律以及通过/不通过签字。"
argument-hint："[launch-date or 'dry-run']"
user-invocable：true
allowed-tools：Read, Glob, Grep, Write
model：sonnet
---

> **Explicit invocation only**: This skill should only run when the user explicitly requests it with `/launch-checklist`. Do not auto-invoke based on context matching.
> **仅显式调用**：此技能仅应在用户明确请求 `/launch-checklist` 时运行。不要基于上下文匹配自动调用。

## Phase 1: Parse Arguments
## 阶段 1：解析参数

Read the argument for the launch date or `dry-run` mode. Dry-run mode generates the checklist without creating sign-off entries or writing files.
读取参数以获取发布日期或 `dry-run` 模式。试运行模式生成清单但不创建签字条目或写入文件。

---

## Phase 2: Gather Project Context
## 阶段 2：收集项目上下文

- Read `CLAUDE.md` for tech stack, target platforms, and team structure
- 读取 `CLAUDE.md` 以获取技术栈、目标平台和团队结构
- Read the latest milestone in `production/milestones/`
- 读取 `production/milestones/` 中的最新里程碑
- Read any existing release checklist in `production/releases/`
- 读取 `production/releases/` 中任何现有发布清单
- Read the content calendar in `design/live-ops/content-calendar.md` if it exists
- 若存在，读取 `design/live-ops/content-calendar.md` 中的内容日历

---

## Phase 3: Scan Codebase Health
## 阶段 3：扫描代码库健康状态

- Count `TODO`, `FIXME`, `HACK` comments and their locations
- 计数 `TODO`、`FIXME`、`HACK` 注释及其位置
- Check for any `console.log`, `print()`, or debug output left in production code
- 检查生产代码中遗留的任何 `console.log`、`print()` 或调试输出
- Check for placeholder assets (search for `placeholder`, `temp_`, `WIP_`)
- 检查占位资产（搜索 `placeholder`、`temp_`、`WIP_`）
- Check for hardcoded test/dev values (localhost, test credentials, debug flags)
- 检查硬编码的测试/开发值（localhost、测试凭据、调试标志）

---

## Phase 4: Generate the Launch Checklist
## 阶段 4：生成发布清单

```markdown
# Launch Checklist: [Game Title]
Target Launch: [Date or DRY RUN]
Generated: [Date]
```
```markdown
# 发布清单：[游戏标题]
目标发布：[日期或试运行]
生成：[日期]
```

```markdown
## 1. Code Readiness
```
```markdown
## 1. 代码就绪
```

```markdown
### Build Health
- [ ] Clean build on all target platforms
- [ ] Zero compiler warnings
- [ ] All unit tests passing
- [ ] All integration tests passing
- [ ] Performance benchmarks within targets
- [ ] No memory leaks (verified via extended soak test)
- [ ] Build size within platform limits
- [ ] Build version correctly set and tagged in source control
```
```markdown
### 构建健康
- [ ] 所有目标平台上的干净构建
- [ ] 零编译器警告
- [ ] 所有单元测试通过
- [ ] 所有集成测试通过
- [ ] 性能基准在目标内
- [ ] 无内存泄漏（通过扩展浸泡测试验证）
- [ ] 构建大小在平台限制内
- [ ] 构建版本正确设置并在源码控制中标记
```

```markdown
### Code Quality
- [ ] TODO count: [N] (zero required for launch, or documented exceptions)
- [ ] FIXME count: [N] (zero required)
- [ ] HACK count: [N] (each must have documented justification)
- [ ] No debug output in production code
- [ ] No hardcoded dev/test values
- [ ] All feature flags set to production values
- [ ] Error handling covers all critical paths
- [ ] Crash reporting integrated and verified
```
```markdown
### 代码质量
- [ ] TODO 计数：[N]（发布要求为零，或记录的例外）
- [ ] FIXME 计数：[N]（要求为零）
- [ ] HACK 计数：[N]（每个必须有记录的理由）
- [ ] 生产代码中无调试输出
- [ ] 无硬编码开发/测试值
- [ ] 所有功能标志设置为生产值
- [ ] 错误处理覆盖所有关键路径
- [ ] 崩溃报告已集成并验证
```

```markdown
### Security
- [ ] No exposed API keys or credentials in source
- [ ] Save data encrypted
- [ ] Network communication secured (TLS/DTLS)
- [ ] Anti-cheat measures active (if multiplayer)
- [ ] Input validation on all server endpoints (if multiplayer)
- [ ] Privacy policy compliance verified
```
```markdown
### 安全
- [ ] 源码中无暴露的 API 密钥或凭据
- [ ] 存档数据已加密
- [ ] 网络通信已保护（TLS/DTLS）
- [ ] 反作弊措施已激活（若多人）
- [ ] 所有服务器端点的输入验证（若多人）
- [ ] 隐私政策合规性已验证
```

```markdown
## 2. Content Readiness
```
```markdown
## 2. 内容就绪
```

```markdown
### Assets
- [ ] All placeholder art replaced with final assets
- [ ] All placeholder audio replaced with final audio
- [ ] Audio mix finalized and approved by audio director
- [ ] All VFX polished and performance-verified
- [ ] No missing or broken asset references
- [ ] Asset naming conventions enforced
```
```markdown
### 资产
- [ ] 所有占位美术已替换为最终资产
- [ ] 所有占位音频已替换为最终音频
- [ ] 音频混音已最终确定并由音频总监批准
- [ ] 所有 VFX 已打磨并通过性能验证
- [ ] 无缺失或损坏的资产引用
- [ ] 资产命名约定已强制执行
```

```markdown
### Text and Localization
- [ ] All player-facing text proofread
- [ ] No hardcoded strings (all externalized for localization)
- [ ] All supported languages translated and verified
- [ ] Text fits UI in all languages (text fitting pass complete)
- [ ] Font coverage verified for all supported languages
- [ ] Credits complete, accurate, and up to date
```
```markdown
### 文本和本地化
- [ ] 所有面向玩家的文本已校对
- [ ] 无硬编码字符串（全部外化以供本地化）
- [ ] 所有支持语言已翻译并验证
- [ ] 文本在所有语言中适配 UI（文本适配通过完成）
- [ ] 所有支持语言的字体覆盖已验证
- [ ] 制作人员名单完整、准确且最新
```

```markdown
### Game Content
- [ ] All levels/maps playable from start to finish
- [ ] Tutorial flow complete and tested with new players
- [ ] All achievements/trophies implemented and tested
- [ ] Save/load works correctly for all game states
- [ ] Difficulty settings balanced and tested
- [ ] End-game/credits sequence complete
```
```markdown
### 游戏内容
- [ ] 所有关卡/地图从头到尾可玩
- [ ] 教程流程完整并已与新玩家测试
- [ ] 所有成就/奖杯已实施并测试
- [ ] 存档/读档对所有游戏状态正确工作
- [ ] 难度设置已平衡并测试
- [ ] 终局/制作人员名单序列完整
```

```markdown
## 3. Quality Assurance
```
```markdown
## 3. 质量保证
```

```markdown
### Testing
- [ ] Full regression test suite passed
- [ ] Zero S1 (Critical) bugs open
- [ ] Zero S2 (Major) bugs open (or documented exceptions)
- [ ] Soak test passed (8+ hours continuous play)
- [ ] Multiplayer stress test passed (if applicable)
- [ ] All critical user paths tested on every platform
- [ ] Edge cases tested (full storage, no network, suspend/resume)
```
```markdown
### 测试
- [ ] 完整回归测试套件通过
- [ ] 零未解决 S1（关键）bug
- [ ] 零未解决 S2（重大）bug（或记录的例外）
- [ ] 浸泡测试通过（连续游玩 8+ 小时）
- [ ] 多人压力测试通过（若适用）
- [ ] 所有关键用户路径在每个平台测试
- [ ] 边缘情况已测试（存储满、无网络、暂停/恢复）
```

```markdown
### Platform Certification
- [ ] PC: Steam/Epic/GOG SDK requirements met
- [ ] Console: TRC/TCR/Lotcheck submission prepared
- [ ] Mobile: App Store/Play Store guidelines compliant
- [ ] Accessibility: minimum standards met (remapping, text scaling, colorblind)
- [ ] Age ratings obtained (ESRB, PEGI, regional)
```
```markdown
### 平台认证
- [ ] PC：Steam/Epic/GOG SDK 要求已满足
- [ ] 主机：TRC/TCR/Lotcheck 提交已准备
- [ ] 移动：App Store/Play Store 指南合规
- [ ] 无障碍：最低标准已满足（重映射、文本缩放、色盲）
- [ ] 年龄评级已获得（ESRB、PEGI、地区）
```

```markdown
### Performance
- [ ] Target FPS met on minimum spec hardware
- [ ] Load times within budget on all platforms
- [ ] Memory usage within budget on all platforms
- [ ] Network bandwidth within targets (if multiplayer)
- [ ] No frame hitches in critical gameplay moments
```
```markdown
### 性能
- [ ] 最低规格硬件上达到目标 FPS
- [ ] 所有平台加载时间在预算内
- [ ] 所有平台内存使用在预算内
- [ ] 网络带宽在目标内（若多人）
- [ ] 关键游戏玩法时刻无帧卡顿
```

```markdown
## 4. Store and Distribution
```
```markdown
## 4. 商店和分发
```

```markdown
### Store Pages
- [ ] Store page copy finalized and proofread
- [ ] Screenshots current and per-platform resolution
- [ ] Trailers current and approved
- [ ] Key art and capsule images finalized
- [ ] System requirements accurate (PC)
- [ ] Pricing configured for all regions
- [ ] Pre-purchase/wishlist campaigns active (if applicable)
```
```markdown
### 商店页面
- [ ] 商店页面文案已最终确定并校对
- [ ] 截图最新且符合各平台分辨率
- [ ] 预告片最新并已批准
- [ ] 主视觉和胶囊图片已最终确定
- [ ] 系统要求准确（PC）
- [ ] 所有地区定价已配置
- [ ] 预购/愿望单活动已激活（若适用）
```

```markdown
### Legal
- [ ] EULA finalized and approved by legal
- [ ] Privacy policy published and linked
- [ ] Third-party license attributions complete
- [ ] Music/audio licensing verified
- [ ] Trademark/IP clearance confirmed
- [ ] GDPR/CCPA compliance verified (data collection, consent, deletion)
```
```markdown
### 法律
- [ ] EULA 已最终确定并由法律批准
- [ ] 隐私政策已发布并链接
- [ ] 第三方许可归属完整
- [ ] 音乐/音频许可已验证
- [ ] 商标/IP 清除已确认
- [ ] GDPR/CCPA 合规已验证（数据收集、同意、删除）
```

```markdown
## 5. Infrastructure
```
```markdown
## 5. 基础设施
```

```markdown
### Servers (if multiplayer/online)
- [ ] Production servers provisioned and load-tested
- [ ] Auto-scaling configured and tested
- [ ] Database backups configured
- [ ] CDN configured for content delivery
- [ ] DDoS protection active
- [ ] Monitoring and alerting configured
```
```markdown
### 服务器（若多人/在线）
- [ ] 生产服务器已配置并经过负载测试
- [ ] 自动缩放已配置并测试
- [ ] 数据库备份已配置
- [ ] CDN 已配置用于内容交付
- [ ] DDoS 保护已激活
- [ ] 监控和告警已配置
```

```markdown
### Analytics and Monitoring
- [ ] Analytics pipeline verified and receiving data
- [ ] Crash reporting active and dashboard accessible
- [ ] Server monitoring dashboards live
- [ ] Key metrics tracked: DAU, session length, retention, crashes
- [ ] Alerts configured for critical thresholds
```
```markdown
### 分析和监控
- [ ] 分析流水线已验证并接收数据
- [ ] 崩溃报告已激活且仪表板可访问
- [ ] 服务器监控仪表板已上线
- [ ] 关键指标已跟踪：DAU、会话长度、留存、崩溃
- [ ] 关键阈值告警已配置
```

```markdown
## 6. Community and Marketing
```
```markdown
## 6. 社区和营销
```

```markdown
### Community Readiness
- [ ] Community guidelines published
- [ ] Moderation team briefed and tools ready
- [ ] Discord/forum/social channels set up
- [ ] FAQ and known issues page prepared
- [ ] Support email/ticketing system active
```
```markdown
### 社区就绪
- [ ] 社区指南已发布
- [ ] 审核团队已 briefed 且工具就绪
- [ ] Discord/论坛/社交渠道已设置
- [ ] FAQ 和已知问题页面已准备
- [ ] 支持邮箱/工单系统已激活
```

```markdown
### Marketing
- [ ] Launch trailer published
- [ ] Press/influencer review keys distributed
- [ ] Social media launch posts scheduled
- [ ] Launch day blog post/dev update drafted
- [ ] Patch notes for launch version published
```
```markdown
### 营销
- [ ] 发布预告片已发布
- [ ] 媒体/影响者评测密钥已分发
- [ ] 社交媒体发布帖子已安排
- [ ] 发布日博客文章/开发更新已起草
- [ ] 发布版本的补丁说明已发布
```

```markdown
## 7. Operations
```
```markdown
## 7. 运营
```

```markdown
### Team Readiness
- [ ] On-call schedule set for first 72 hours post-launch
- [ ] Incident response playbook reviewed by team
- [ ] Rollback plan documented and tested
- [ ] Hotfix pipeline tested (can ship emergency fix within 4 hours)
- [ ] Communication plan for launch issues (who posts, where, how fast)
```
```markdown
### 团队就绪
- [ ] 发布后前 72 小时的值班计划已设定
- [ ] 事件响应手册已由团队评审
- [ ] 回滚计划已记录并测试
- [ ] 热修复流水线已测试（可在 4 小时内交付紧急修复）
- [ ] 发布问题沟通计划（谁发布、在哪里、多快）
```

```markdown
### Day-One Plan
- [ ] Day-one patch prepared (if needed)
- [ ] Server unlock/go-live procedure documented
- [ ] Launch monitoring dashboard bookmarked by all leads
- [ ] War room/channel established for launch day
```
```markdown
### 首日计划
- [ ] 首日补丁已准备（若需要）
- [ ] 服务器解锁/上线流程已记录
- [ ] 发布监控仪表板已被所有主管收藏
- [ ] 已为发布日建立作战室/频道
```

```markdown
## Go / No-Go Decision
```
```markdown
## 通过 / 不通过决策
```

```markdown
**Overall Status**: [READY / NOT READY / CONDITIONAL]
```
```markdown
**整体状态**：[READY / NOT READY / CONDITIONAL]
```

```markdown
### Blocking Items
[List any items that must be resolved before launch]
```
```markdown
### 阻断项
[列出发布前必须解决的任何项]
```

```markdown
### Conditional Items
[List items that have documented workarounds or accepted risk]
```
```markdown
### 条件项
[列出有记录变通方法或已接受风险的项]
```

```markdown
### Sign-Offs Required
- [ ] Creative Director — Content and experience quality
- [ ] Technical Director — Technical health and stability
- [ ] QA Lead — Quality and test coverage
- [ ] Producer — Schedule and overall readiness
- [ ] Release Manager — Build and deployment readiness
```
```markdown
### 需要的签字
- [ ] 创意总监 —— 内容和体验质量
- [ ] 技术总监 —— 技术健康和稳定性
- [ ] QA 主管 —— 质量和测试覆盖
- [ ] 制作人 —— 进度和整体就绪
- [ ] 发布经理 —— 构建和部署就绪
```

---

## Phase 5: Save Checklist
## 阶段 5：保存清单

Present the completed checklist and summary to the user (total items, blocking items count, conditional items count, departments with incomplete sections).
向用户呈现已完成的清单和摘要（总项数、阻断项数、条件项数、章节未完成的部门数）。

If not in dry-run mode, ask: "May I write this to `production/releases/launch-checklist-[date].md`?"
若不在试运行模式，询问："我可以将此写入 `production/releases/launch-checklist-[date].md` 吗？"

If yes, write the file, creating directories as needed.
若是，写入文件，必要时创建目录。

---

## Phase 6: Next Steps
## 阶段 6：后续步骤

- Run `/gate-check` to get a formal PASS/CONCERNS/FAIL verdict before launch.
- 运行 `/gate-check` 在发布前获取正式 PASS/CONCERNS/FAIL 裁定。
- Coordinate sign-offs via `/team-release`.
- 通过 `/team-release` 协调签字。
