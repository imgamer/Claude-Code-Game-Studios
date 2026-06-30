---
name: changelog
description: "Auto-generates a changelog from git commits, sprint data, and design documents. Produces both internal and player-facing versions."
argument-hint: "[version|sprint-number]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write
context: |
  !git log --oneline -30 2>/dev/null
  !git tag --list --sort=-v:refname 2>/dev/null | head -5
model: haiku
---
---
name: changelog
description: "从 git 提交、冲刺数据和设计文档自动生成变更日志。同时生成内部版本和面向玩家版本。"
argument-hint: "[version|sprint-number]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write
context: |
  !git log --oneline -30 2>/dev/null
  !git tag --list --sort=-v:refname 2>/dev/null | head -5
model: haiku
---

## Phase 1: Parse Arguments

Read the argument for the target version or sprint number. If a version is given, use the corresponding git tag. If a sprint number is given, use the sprint date range.

Verify the repository is initialized: run `git rev-parse --is-inside-work-tree` to confirm git is available. If not a git repo, inform the user and abort gracefully.

## 第 1 阶段：解析参数

读取目标版本或冲刺编号参数。如给定版本，使用对应 git 标签。如给定冲刺编号，使用冲刺日期范围。

验证仓库是否已初始化：运行 `git rev-parse --is-inside-work-tree` 确认 git 可用。如非 git 仓库，告知用户并优雅中止。

---

## Phase 2: Gather Change Data

Read the git log since the last tag or release:

```
git log --oneline [last-tag]..HEAD
```

If no tags exist, read the full log or a reasonable recent range (last 100 commits).

Read sprint reports from `production/sprints/` for the relevant period to understand planned work and context behind changes.

Read completed design documents from `design/gdd/` for any new features implemented during this period.

## 第 2 阶段：收集变更数据

读取自上次标签或发布以来的 git 日志：

```
git log --oneline [last-tag]..HEAD
```

如无标签，读取完整日志或合理的近期范围（最近 100 次提交）。

读取 `production/sprints/` 中相关时期的冲刺报告以理解计划工作和变更背景。

读取 `design/gdd/` 中此时期实现的任何新功能的已完成设计文档。

---

## Phase 3: Categorize Changes

Categorize every change into one of these categories:

- **New Features**: Entirely new gameplay systems, modes, or content
- **Improvements**: Enhancements to existing features, UX improvements, performance gains
- **Bug Fixes**: Corrections to broken behavior
- **Balance Changes**: Tuning of gameplay values, difficulty, economy
- **Known Issues**: Issues the team is aware of but have not yet resolved
- **Miscellaneous**: Changes that do not fit the above categories, or commits whose messages are too vague to classify confidently

For each commit, check whether the message contains a task ID or story reference
(e.g. `[STORY-123]`, `TR-`, `#NNN`, or similar). Count commits that lack any task reference
and include this count in the Phase 4 Metrics section as: `Commits without task reference: [N]`.

## 第 3 阶段：分类变更

将每个变更分类到以下类别之一：

- **新功能**：全新的游戏系统、模式或内容
- **改进**：对现有功能的增强、UX 改进、性能提升
- **Bug 修复**：对损坏行为的修正
- **平衡变更**：调整游戏数值、难度、经济
- **已知问题**：团队知晓但尚未解决的问题
- **杂项**：不属于上述类别的变更，或提交信息过于模糊无法可靠分类的提交

对每次提交，检查信息是否包含任务 ID 或故事引用
（例如 `[STORY-123]`、`TR-`、`#NNN` 或类似）。统计缺少任何任务引用的提交，
并在第 4 阶段 Metrics 章节中包含此计数：`Commits without task reference: [N]`。

---

## Phase 4: Generate Internal Changelog

```markdown
# Internal Changelog: [Version]
Date: [Date]
Sprint(s): [Sprint numbers covered]
Commits: [Count] ([first-hash]..[last-hash])

## New Features
- [Feature Name] -- [Technical description, affected systems]
  - Commits: [hash1], [hash2]
  - Owner: [who implemented it]
  - Design doc: [link if applicable]

## Improvements
- [Improvement] -- [What changed technically and why]
  - Commits: [hashes]
  - Owner: [who]

## Bug Fixes
- [BUG-ID] [Description of bug and root cause]
  - Fix: [What was changed]
  - Commits: [hashes]
  - Owner: [who]

## Balance Changes
- [What was tuned] -- [Old value -> New value] -- [Design intent]
  - Owner: [who]

## Technical Debt / Refactoring
- [What was cleaned up and why]
  - Commits: [hashes]

## Miscellaneous
- [Change that didn't fit other categories, or vague commit message]
  - Commits: [hashes]

## Known Issues
- [Issue description] -- [Severity] -- [ETA for fix if known]

## Metrics
- Total commits: [N]
- Files changed: [N]
- Lines added: [N]
- Lines removed: [N]
- Commits without task reference: [N]
```

## 第 4 阶段：生成内部变更日志

```markdown
# Internal Changelog: [Version]
Date: [Date]
Sprint(s): [Sprint numbers covered]
Commits: [Count] ([first-hash]..[last-hash])

## New Features
- [Feature Name] -- [Technical description, affected systems]
  - Commits: [hash1], [hash2]
  - Owner: [who implemented it]
  - Design doc: [link if applicable]

## Improvements
- [Improvement] -- [What changed technically and why]
  - Commits: [hashes]
  - Owner: [who]

## Bug Fixes
- [BUG-ID] [Description of bug and root cause]
  - Fix: [What was changed]
  - Commits: [hashes]
  - Owner: [who]

## Balance Changes
- [What was tuned] -- [Old value -> New value] -- [Design intent]
  - Owner: [who]

## Technical Debt / Refactoring
- [What was cleaned up and why]
  - Commits: [hashes]

## Miscellaneous
- [Change that didn't fit other categories, or vague commit message]
  - Commits: [hashes]

## Known Issues
- [Issue description] -- [Severity] -- [ETA for fix if known]

## Metrics
- Total commits: [N]
- Files changed: [N]
- Lines added: [N]
- Lines removed: [N]
- Commits without task reference: [N]
```

---

## Phase 5: Generate Player-Facing Changelog

```markdown
# What is New in [Version]

## New Features
- **[Feature Name]**: [Player-friendly description of what they can now do
  and why it is exciting. Focus on the experience, not the implementation.]

## Improvements
- **[What improved]**: [How this makes the game better for the player.
  Be specific but avoid jargon.]

## Bug Fixes
- Fixed an issue where [describe what the player experienced, not what was
  wrong in the code]
- Fixed [player-visible symptom]

## Balance Changes
- [What changed in player-understandable terms and the design intent.
  Example: "Healing potions now restore 50 HP (up from 30) -- we felt
  players needed more recovery options in late-game encounters."]

## Known Issues
- We are aware of [issue description in player terms] and are working on a
  fix. [Workaround if one exists.]

---
Thank you for playing! Your feedback helps us make the game better.
Report issues at [link].
```

## 第 5 阶段：生成面向玩家变更日志

```markdown
# What is New in [Version]

## New Features
- **[Feature Name]**: [Player-friendly description of what they can now do
  and why it is exciting. Focus on the experience, not the implementation.]

## Improvements
- **[What improved]**: [How this makes the game better for the player.
  Be specific but avoid jargon.]

## Bug Fixes
- Fixed an issue where [describe what the player experienced, not what was
  wrong in the code]
- Fixed [player-visible symptom]

## Balance Changes
- [What changed in player-understandable terms and the design intent.
  Example: "Healing potions now restore 50 HP (up from 30) -- we felt
  players needed more recovery options in late-game encounters."]

## Known Issues
- We are aware of [issue description in player terms] and are working on a
  fix. [Workaround if one exists.]

---
Thank you for playing! Your feedback helps us make the game better.
Report issues at [link].
```

---

## Phase 6: Output

Output both changelogs to the user. The internal changelog is the primary working document. The player-facing changelog is ready for community posting after review.

## 第 6 阶段：输出

向用户输出两个变更日志。内部变更日志是主要工作文档。面向玩家变更日志经评审后即可发布到社区。

---

## Phase 7: Offer File Write

After presenting the changelogs, ask the user:

> "May I write this changelog to `docs/CHANGELOG.md`?
> [A] Yes, append this entry (recommended if the file already exists)
> [B] Yes, overwrite the file entirely
> [C] No — I'll copy it manually"

- Check whether `docs/CHANGELOG.md` exists before asking. If it does, default the
  recommendation to **[A] append**.
- If the user selects [A]: append the new internal changelog entry to the top of
  the existing file (newest entries first).
- If the user selects [B]: overwrite the file with the new changelog.
- If the user selects [C]: stop here without writing.

After a successful write: Verdict: **CHANGELOG WRITTEN** — changelog saved to `docs/CHANGELOG.md`.
If the user declines: Verdict: **COMPLETE** — changelog generated.

## 第 7 阶段：提供文件写入

呈现变更日志后，询问用户：

> "可以将此变更日志写入 `docs/CHANGELOG.md` 吗？
> [A] 是，追加此条目（如文件已存在则推荐）
> [B] 是，完全覆盖文件
> [C] 否 —— 我手动复制"

- 询问前检查 `docs/CHANGELOG.md` 是否存在。如存在，默认
  推荐 **[A] 追加**。
- 如用户选择 [A]：将新内部变更日志条目追加到
  现有文件顶部（最新条目在前）。
- 如用户选择 [B]：用新变更日志覆盖文件。
- 如用户选择 [C]：不写入，到此停止。

成功写入后：判定：**CHANGELOG WRITTEN** —— 变更日志已保存到 `docs/CHANGELOG.md`。
如用户拒绝：判定：**COMPLETE** —— 变更日志已生成。

---

## Phase 7: Next Steps

- Use `/patch-notes [version]` to generate a styled, saved version for public release.
- Use `/release-checklist` before publishing the changelog externally.

### Guidelines

- Never expose internal code references, file paths, or developer names in the player-facing changelog
- Group related changes together rather than listing individual commits
- If a commit message is unclear, check the associated files and sprint data for context
- Balance changes should always include the design reasoning, not just the numbers
- Known issues should be honest — players appreciate transparency
- If the git history is messy (merge commits, reverts, fixup commits), clean up the narrative rather than listing every commit literally

## 第 7 阶段：下一步

- 使用 `/patch-notes [version]` 生成带样式、已保存的公开版本。
- 在外部发布变更日志前使用 `/release-checklist`。

### 指南

- 永远不要在面向玩家变更日志中暴露内部代码引用、文件路径或开发者姓名
- 将相关变更组合在一起而非列出单个提交
- 如提交信息不清晰，检查相关文件和冲刺数据以获取上下文
- 平衡变更应始终包含设计理由，不仅是数字
- 已知问题应诚实 —— 玩家欣赏透明度
- 如 git 历史混乱（合并提交、回滚、修复提交），清理叙述而非逐字列出每个提交
