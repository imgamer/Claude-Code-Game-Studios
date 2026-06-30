# Release Checklist: [Version] -- [Platform]
# 发布检查清单：[版本] -- [平台]

**Release Date**: [Target Date]
**Release Manager**: [Name]
**Status**: [ ] GO / [ ] NO-GO
**发布日期**：[目标日期]
**发布经理**：[Name]
**状态**：[ ] 发布 / [ ] 不发布

---

## Build Verification
## 构建验证

- [ ] Clean build succeeds on all target platforms
- [ ] No compiler warnings (zero-warning policy)
- [ ] Build version number set correctly: `[version]`
- [ ] Build is reproducible from tagged commit: `[commit hash]`
- [ ] Build size within budget: [actual] / [budget]
- [ ] All assets included and loading correctly
- [ ] No debug/development features enabled in release build
- [ ] 在所有目标平台上干净构建成功
- [ ] 无编译器警告（零警告策略）
- [ ] 构建版本号设置正确：`[version]`
- [ ] 从标记提交可重现构建：`[commit hash]`
- [ ] 构建大小在预算内：[实际] / [预算]
- [ ] 所有资产已包含并正确加载
- [ ] 发布构建中无调试/开发功能启用

---

## Quality Gates
## 质量门

### Critical Bugs
### 关键错误
- [ ] Zero S1 (Critical) bugs open
- [ ] Zero S2 (Major) bugs -- or documented exceptions below:
- [ ] 无 S1（关键）错误未解决
- [ ] 无 S2（重大）错误未解决 -- 或以下记录的例外：

| Bug ID | Description | Exception Rationale | Approved By |
| ---- | ---- | ---- | ---- |
| | | | |
| 错误 ID | 描述 | 例外理由 | 批准人 |
| ---- | ---- | ---- | ---- |
| | | | |

### Test Coverage
### 测试覆盖率
- [ ] All critical path features tested and signed off
- [ ] Full regression suite passed: [pass rate]%
- [ ] Soak test passed (4+ hours continuous play)
- [ ] Edge case testing complete
- [ ] 所有关键路径功能已测试并签署
- [ ] 完整回归套件通过：[通过率]%
- [ ] 浸泡测试通过（4+ 小时连续游玩）
- [ ] 边界情况测试完成

### Performance
### 性能
- [ ] Target FPS met on minimum spec: [actual] / [target] FPS
- [ ] Memory usage within budget: [actual] / [budget] MB
- [ ] Load times within budget: [actual] / [target] seconds
- [ ] No memory leaks over extended play (soak test)
- [ ] No frame drops below [threshold] in normal gameplay
- [ ] 最低规格达到目标 FPS：[实际] / [目标] FPS
- [ ] 内存使用在预算内：[实际] / [预算] MB
- [ ] 加载时间在预算内：[实际] / [目标] 秒
- [ ] 长时间游玩无内存泄漏（浸泡测试）
- [ ] 正常游戏中无低于 [阈值] 的帧率下降

---

## Content Complete
## 内容完整

- [ ] All placeholder assets replaced with final versions
- [ ] All player-facing text proofread
- [ ] All text localization-ready (no hardcoded strings)
- [ ] Localization complete for: [list locales]
- [ ] Audio mix finalized and approved
- [ ] Credits complete and accurate
- [ ] Legal notices and third-party attributions complete
- [ ] 所有占位资产已替换为最终版本
- [ ] 所有面向玩家的文本已校对
- [ ] 所有文本本地化就绪（无硬编码字符串）
- [ ] 本地化完成：[列出区域设置]
- [ ] 音频混音已定稿并批准
- [ ] 鸣谢完整准确
- [ ] 法律声明与第三方归属完整

---

## Platform: PC
## 平台：PC

- [ ] Minimum and recommended specs documented
- [ ] Keyboard+mouse controls fully functional
- [ ] Controller support tested (Xbox, PlayStation, generic)
- [ ] Resolution scaling tested: 1080p, 1440p, 4K, ultrawide
- [ ] Windowed, borderless, fullscreen modes working
- [ ] Graphics settings save and load correctly
- [ ] Store SDK integrated and tested: [Steam/Epic/GOG]
- [ ] Achievements functional
- [ ] Cloud saves functional
- [ ] 最低与推荐规格已记录
- [ ] 键盘 + 鼠标控制完全功能
- [ ] 控制器支持已测试（Xbox、PlayStation、通用）
- [ ] 分辨率缩放已测试：1080p、1440p、4K、超宽屏
- [ ] 窗口、无边框、全屏模式工作正常
- [ ] 图形设置保存与加载正确
- [ ] 商店 SDK 已集成并测试：[Steam/Epic/GOG]
- [ ] 成就功能正常
- [ ] 云存档功能正常

## Platform: Console (if applicable)
## 平台：主机（如适用）

- [ ] TRC/TCR/Lotcheck requirements met
- [ ] Platform controller prompts correct
- [ ] Suspend/resume works
- [ ] User switching handled
- [ ] Network loss handled gracefully
- [ ] Storage full scenario handled
- [ ] Parental controls respected
- [ ] Certification submission prepared
- [ ] 满足 TRC/TCR/Lotcheck 要求
- [ ] 平台控制器提示正确
- [ ] 挂起/恢复工作正常
- [ ] 用户切换已处理
- [ ] 网络丢失优雅处理
- [ ] 存储满场景已处理
- [ ] 家长控制受尊重
- [ ] 认证提交已准备

---

## Store and Distribution
## 商店与分发

- [ ] Store page metadata complete and proofread
- [ ] Screenshots current and meet platform requirements
- [ ] Trailer current
- [ ] Key art and capsule images final
- [ ] Age ratings obtained: [ ] ESRB [ ] PEGI [ ] Other
- [ ] Legal: EULA, Privacy Policy, Terms of Service
- [ ] Pricing configured for all regions
- [ ] 商店页面元数据完整并校对
- [ ] 截图最新且满足平台要求
- [ ] 预告片最新
- [ ] 主视觉图与胶囊图定稿
- [ ] 已获年龄评级：[ ] ESRB [ ] PEGI [ ] 其他
- [ ] 法律：EULA、隐私政策、服务条款
- [ ] 所有区域定价已配置

---

## Launch Readiness
## 上线就绪

- [ ] Analytics/telemetry verified and receiving data
- [ ] Crash reporting configured: [service name]
- [ ] Day-one patch prepared (if needed)
- [ ] On-call team schedule set for first 72 hours
- [ ] Community announcements drafted
- [ ] Press/influencer keys prepared
- [ ] Support team briefed on known issues
- [ ] Rollback plan documented and tested
- [ ] 分析/遥测已验证并接收数据
- [ ] 崩溃报告已配置：[service name]
- [ ] 首日补丁已准备（如需）
- [ ] 前 72 小时值班团队日程已设
- [ ] 社群公告已起草
- [ ] 媒体/影响者密钥已准备
- [ ] 支持团队已就已知问题获简报
- [ ] 回滚计划已记录并测试

---

## Sign-offs
## 签署

| Role | Name | Status | Date |
| ---- | ---- | ---- | ---- |
| QA Lead | | [ ] Approved | |
| Technical Director | | [ ] Approved | |
| Producer | | [ ] Approved | |
| Creative Director | | [ ] Approved | |
| 角色 | 姓名 | 状态 | 日期 |
| ---- | ---- | ---- | ---- |
| QA 主管 | | [ ] 已批准 | |
| 技术总监 | | [ ] 已批准 | |
| 制作人 | | [ ] 已批准 | |
| 创意总监 | | [ ] 已批准 | |

---

## Final Decision
## 最终决策

**GO / NO-GO**: ____________
**发布 / 不发布**：____________

**Rationale**: [Summary of readiness. If NO-GO, list specific blocking items and estimated time to resolve.]
**理由**：[就绪情况摘要。如不发布，列出具体阻碍项与估算解决时间。]

**Notes**: [Any additional context, known risks accepted, or conditions on the release.]
**备注**：[任何额外上下文、已接受的已知风险或发布条件。]
