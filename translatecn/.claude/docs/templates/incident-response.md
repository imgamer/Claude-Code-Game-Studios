# Incident Response: [Incident Title]
# 事件响应：[事件标题]

**Severity**: [S1-Critical / S2-Major / S3-Moderate / S4-Minor]
**Status**: [Active / Mitigated / Resolved / Post-Mortem Complete]
**Detected**: [Date Time UTC]
**Resolved**: [Date Time UTC or ONGOING]
**Duration**: [Total time from detection to resolution]
**Incident Commander**: [Name/Role]
**严重度**：[S1-关键 / S2-重大 / S3-中等 / S4-轻微]
**状态**：[活跃 / 已缓解 / 已解决 / 复盘完成]
**检测**：[Date Time UTC]
**解决**：[Date Time UTC 或 ONGOING]
**持续时间**：[从检测到解决的总时间]
**事件指挥**：[姓名/角色]

---

## Impact Summary
## 影响摘要

[2-3 sentences describing what players experienced. Write from the player
perspective, not the technical perspective.]
[2-3 句描述玩家经历了什么。从玩家视角而非技术视角写。]

- **Players affected**: [estimated count or percentage]
- **Platforms affected**: [PC / Console / Mobile / All]
- **Regions affected**: [All / specific regions]
- **Revenue impact**: [estimated if applicable]
- **受影响玩家**：[估算数量或百分比]
- **受影响平台**：[PC / 主机 / 移动 / 全部]
- **受影响区域**：[全部 / 特定区域]
- **收入影响**：[如适用，估算]

---

## Timeline
## 时间线

| Time (UTC) | Event | Action Taken |
| ---- | ---- | ---- |
| [HH:MM] | Incident detected via [monitoring/player report/etc.] | Incident commander assigned |
| [HH:MM] | Root cause identified | [Brief description of cause] |
| [HH:MM] | Mitigation deployed | [What was done] |
| [HH:MM] | Service restored / Fix confirmed | Monitoring for recurrence |
| [HH:MM] | All-clear declared | Post-mortem scheduled |
| 时间（UTC） | 事件 | 采取动作 |
| ---- | ---- | ---- |
| [HH:MM] | 通过 [监控/玩家报告等] 检测到事件 | 分配事件指挥 |
| [HH:MM] | 识别根本原因 | [原因简要描述] |
| [HH:MM] | 部署缓解 | [做了什么] |
| [HH:MM] | 服务恢复 / 修复确认 | 监控复发 |
| [HH:MM] | 宣布解除警报 | 计划复盘 |

---

## Root Cause
## 根本原因

### What Happened
### 发生了什么
[Technical description of the root cause. Be specific about the chain of events
that led to the incident.]
[根本原因的技术描述。具体说明导致事件的事件链。]

### Why It Happened
### 为何发生
[Systemic cause — why did existing processes, tests, or safeguards fail to
prevent this? This is more important than the technical cause.]
[系统性原因 — 为何现有流程、测试或安全措施未能防止此？
这比技术原因更重要。]

### Contributing Factors
### 促成因素
- [Factor 1 — e.g., "Insufficient load testing for the new matchmaking system"]
- [Factor 2 — e.g., "Monitoring alert threshold was set too high"]
- [Factor 3]
- [因素 1 — 例如，"新匹配系统负载测试不足"]
- [因素 2 — 例如，"监控警报阈值设置过高"]
- [因素 3]

---

## Mitigation and Resolution
## 缓解与解决

### Immediate Actions (during incident)
### 即时动作（事件期间）
1. [Action taken to stop the bleeding]
2. [Action taken to restore service]
3. [Action taken to verify resolution]
1. [止血采取的动作]
2. [恢复服务采取的动作]
3. [验证解决采取的动作]

### Follow-Up Actions (after resolution)
### 后续动作（解决后）
1. [Permanent fix if immediate action was a workaround]
2. [Additional testing or monitoring added]
3. [Process changes to prevent recurrence]
1. [如即时动作为权宜之计的永久修复]
2. [添加的额外测试或监控]
3. [防止复发的流程变更]

---

## Player Communication
## 玩家沟通

### Initial Acknowledgment
### 初始确认
*Sent: [Time] via [channel]*
> [Exact text of the first public message acknowledging the issue]
*发送：[时间] 通过 [渠道]*
> [确认问题的首条公开消息原文]

### Status Updates
### 状态更新
*Sent: [Time] via [channel]*
> [Text of each subsequent update]
*发送：[时间] 通过 [渠道]*
> [每个后续更新的文本]

### Resolution Notice
### 解决通知
*Sent: [Time] via [channel]*
> [Text announcing the fix and any compensation]
*发送：[时间] 通过 [渠道]*
> [宣布修复与任何补偿的文本]

### Compensation (if applicable)
### 补偿（如适用）
- **What**: [description of compensation — e.g., "500 premium currency + 24-hour XP boost"]
- **Who**: [all players / affected players only / players who logged in during incident]
- **When**: [delivery date and method]
- **Rationale**: [why this compensation is appropriate for the impact]
- **什么**：[补偿描述 — 例如，"500 高级货币 + 24 小时经验加成"]
- **谁**：[所有玩家 / 仅受影响玩家 / 事件期间登录的玩家]
- **何时**：[交付日期与方法]
- **理由**：[为何此补偿适合此影响]

---

## Prevention
## 预防

### What We Are Changing
### 我们在改变什么

| Action Item | Owner | Deadline | Status |
| ---- | ---- | ---- | ---- |
| [Specific preventive measure] | [Role] | [Date] | [TODO/Done] |
| [Add monitoring for X] | [Role] | [Date] | [TODO/Done] |
| [Add test coverage for Y] | [Role] | [Date] | [TODO/Done] |
| [Update runbook for Z] | [Role] | [Date] | [TODO/Done] |
| 行动项 | 负责人 | 截止日期 | 状态 |
| ---- | ---- | ---- | ---- |
| [具体预防措施] | [角色] | [Date] | [待办/完成] |
| [为 X 添加监控] | [角色] | [Date] | [待办/完成] |
| [为 Y 添加测试覆盖] | [角色] | [Date] | [待办/完成] |
| [为 Z 更新运行手册] | [角色] | [Date] | [待办/完成] |

### Process Improvements
### 流程改进
- [Process change to prevent similar incidents]
- [Monitoring/alerting improvement]
- [Testing improvement]
- [防止类似事件的流程变更]
- [监控/警报改进]
- [测试改进]

---

## Lessons Learned
## 经验教训

### What Went Well
### 表现良好之处
- [Positive aspect of incident response — e.g., "Detection was fast due to
  monitoring alerts"]
- [Positive aspect]
- [事件响应的积极方面 — 例如，"由于监控警报检测很快"]
- [积极方面]

### What Went Poorly
### 表现不佳之处
- [Problem with response — e.g., "Took 20 minutes to identify the correct
  on-call person"]
- [Problem]
- [响应问题 — 例如，"花 20 分钟识别正确的值班人"]
- [问题]

### Where We Got Lucky
### 我们幸运之处
- [Factor that reduced impact by chance rather than design — these are hidden
  risks to address]
- [偶然而非设计减少影响的因素 — 这些是需解决的隐藏风险]

---

## Sign-Offs
## 签署

- [ ] Technical Director — Root cause accurate, prevention plan sufficient
- [ ] QA Lead — Test coverage gaps addressed
- [ ] Producer — Timeline and communication reviewed
- [ ] Community Manager — Player communication reviewed
- [ ] 技术总监 — 根本原因准确，预防计划充分
- [ ] QA 主管 — 测试覆盖缺口已解决
- [ ] 制作人 — 时间线与沟通已审查
- [ ] 社群经理 — 玩家沟通已审查

---

*This document is filed in `production/hotfixes/` and linked from the
release notes for the fix version.*
*本文档归档于 `production/hotfixes/` 并从修复版本的发布说明链接。*
