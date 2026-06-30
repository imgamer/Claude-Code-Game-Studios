# Test Evidence: [Story Title]
# 测试证据：[故事标题]

> **Story**: `[path to story file]`
> **Story Type**: [Visual/Feel | UI]
> **Date**: [date]
> **Tester**: [who performed the test]
> **Build / Commit**: [version or git hash]
> **故事**：`[path to story file]`
> **故事类型**：[视觉/手感 | UI]
> **日期**：[date]
> **测试者**：[执行测试的人]
> **构建 / 提交**：[version or git hash]

---

## What Was Tested
## 测试内容

[One paragraph describing the feature or behaviour that was validated. Include
the acceptance criteria numbers from the story that this evidence covers.]
[一段描述被验证的功能或行为。包含此证据覆盖的故事中的验收标准编号。]

**Acceptance criteria covered**: [AC-1, AC-2, AC-3]
**覆盖的验收标准**：[AC-1, AC-2, AC-3]

---

## Acceptance Criteria Results
## 验收标准结果

| # | Criterion (from story) | Result | Notes |
|---|----------------------|--------|-------|
| AC-1 | [exact criterion text] | PASS / FAIL | [any observations] |
| AC-2 | [exact criterion text] | PASS / FAIL | |
| AC-3 | [exact criterion text] | PASS / FAIL | |
| # | 标准（来自故事） | 结果 | 备注 |
|---|----------------------|--------|-------|
| AC-1 | [标准原文] | 通过 / 失败 | [任何观察] |
| AC-2 | [标准原文] | 通过 / 失败 | |
| AC-3 | [标准原文] | 通过 / 失败 | |

---

## Screenshots / Video
## 截图 / 视频

List all captured evidence below. Store files in the same directory as this
document or in `production/qa/evidence/[story-slug]/`.
在下面列出所有捕获的证据。将文件存储在此文档所在目录或 `production/qa/evidence/[story-slug]/` 中。

| # | Filename | What It Shows | Acceptance Criterion |
|---|----------|--------------|----------------------|
| 1 | `[filename.png]` | [brief description of what is visible] | AC-1 |
| 2 | `[filename.png]` | | AC-2 |
| # | 文件名 | 显示内容 | 验收标准 |
|---|----------|--------------|----------------------|
| 1 | `[filename.png]` | [可见内容的简要描述] | AC-1 |
| 2 | `[filename.png]` | | AC-2 |

*If video: note the timestamp and what it demonstrates.*
*如果是视频：记录时间戳和它演示的内容。*

---

## Test Conditions
## 测试条件

- **Game state at start**: [e.g., "fresh save, player at level 1, no items"]
- **Platform / hardware**: [e.g., "Windows 11, GTX 1080, 1080p"]
- **Framerate during test**: [e.g., "stable 60fps" or "~45fps — within budget"]
- **Any special setup required**: [e.g., "dev menu used to trigger specific state"]
- **开始时的游戏状态**：[例如，"新存档，玩家 1 级，无物品"]
- **平台 / 硬件**：[例如，"Windows 11, GTX 1080, 1080p"]
- **测试期间帧率**：[例如，"稳定 60fps" 或 "~45fps — 在预算内"]
- **任何所需特殊设置**：[例如，"使用开发菜单触发特定状态"]

---

## Observations
## 观察

[Anything noteworthy that didn't cause a FAIL but should be recorded. Examples:
minor visual jitter, frame dip under load, behaviour that technically passes
but felt slightly off. These become candidates for polish work.]
[任何未导致失败但应记录的值得注意的事项。示例：轻微视觉抖动、
负载下帧率下降、技术上通过但感觉略不对的行为。这些成为打磨工作的候选。]

- [Observation 1]
- [Observation 2]
- [观察 1]
- [观察 2]

If nothing notable: *No significant observations.*
如无值得注意的内容：*无重要观察。*

---

## Sign-Off
## 签署

All roles must sign off before the story can be marked COMPLETE via `/story-done`.
Visual/Feel stories require the designer or art-lead sign-off. UI stories require
the UX lead or designer sign-off.
所有角色必须签署，故事才能通过 `/story-done` 标记为完成。
视觉/手感故事需要设计师或美术主管签署。UI 故事需要 UX 主管或设计师签署。

**Solo developers**: all sign-offs may be by the same person in each role. The
intent is that someone deliberately reviews the evidence before marking complete —
not that three separate people must participate.
**独立开发者**：所有签署可由同一人担任每个角色。意图是有人在标记完成前有意审查证据 —
而非必须三人分别参与。

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Developer (implemented) | | | [ ] Approved |
| Designer / Art Lead / UX Lead | | | [ ] Approved |
| QA Lead | | | [ ] Approved |
| 角色 | 姓名 | 日期 | 签名 |
|------|------|------|-----------|
| 开发者（实现者） | | | [ ] 已批准 |
| 设计师 / 美术主管 / UX 主管 | | | [ ] 已批准 |
| QA 主管 | | | [ ] 已批准 |

**Any sign-off can be marked "Deferred — [reason]"** if the person is
unavailable. Deferred sign-offs must be resolved before the story advances
past the sprint review.
**任何签署都可标记为"延后 — [原因]"**（如该人不可用）。延后签署必须在故事
越过冲刺审查之前解决。

---

*Template: `.claude/docs/templates/test-evidence.md`*
*Used for: Visual/Feel and UI story type evidence records*
*Location: `production/qa/evidence/[story-slug]-evidence.md`*
*模板：`.claude/docs/templates/test-evidence.md`*
*用于：视觉/手感与 UI 故事类型证据记录*
*位置：`production/qa/evidence/[story-slug]-evidence.md`*
