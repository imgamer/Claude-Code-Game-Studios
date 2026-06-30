---
name: patch-notes
description: "Generate player-facing patch notes from git history, sprint data, and internal changelogs. Translates developer language into clear, engaging player communication."
argument-hint: "[version] [--style brief|detailed|full]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash
model: haiku
agent: community-manager
---
---
name: patch-notes
description: "从 git 历史、冲刺数据和内部变更日志生成面向玩家的补丁说明。将开发者语言转换为清晰、有吸引力的玩家沟通内容。"
argument-hint: "[version] [--style brief|detailed|full]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash
model: haiku
agent: community-manager
---

## Phase 1: Parse Arguments
## 阶段 1：解析参数

- `version`: the release version to generate notes for (e.g., `1.2.0`)
- `--style`: output style — `brief` (bullet points), `detailed` (with context), `full` (with developer commentary). Default: `detailed`.
- `version`：要为其生成说明的发布版本（例如 `1.2.0`）
- `--style`：输出样式 — `brief`（要点式）、`detailed`（带上下文）、`full`（带开发者评论）。默认：`detailed`。

If no version is provided, ask the user before proceeding.
如果未提供版本号，在继续之前询问用户。

---

## Phase 2: Gather Change Data
## 阶段 2：收集变更数据

- Read the internal changelog at `production/releases/[version]/changelog.md` if it exists
- Also check `docs/CHANGELOG.md` for the relevant version entry
- Run `git log` between the previous release tag and current tag/HEAD as a fallback
- Read sprint retrospectives in `production/sprints/` for context
- Read any balance change documents in `design/balance/`
- Read bug fix records from QA if available
- 如果存在，读取 `production/releases/[version]/changelog.md` 处的内部变更日志
- 同时检查 `docs/CHANGELOG.md` 中对应版本的条目
- 作为后备，在前一个发布标签与当前标签/HEAD 之间运行 `git log`
- 读取 `production/sprints/` 中的冲刺回顾以获取上下文
- 读取 `design/balance/` 中的任何平衡性变更文档
- 如果可用，读取 QA 的缺陷修复记录

**If no changelog data is available** (neither `production/releases/[version]/changelog.md`
nor a `docs/CHANGELOG.md` entry for this version exists, and git log is empty or unavailable):
**如果没有可用的变更日志数据**（`production/releases/[version]/changelog.md`
不存在，`docs/CHANGELOG.md` 中也没有该版本的条目，且 git log 为空或不可用）：

> "No changelog data found for [version]. Run `/changelog [version]` first to generate the
> internal changelog, then re-run `/patch-notes [version]`."
> "未找到 [version] 的变更日志数据。请先运行 `/changelog [version]` 生成
> 内部变更日志，然后重新运行 `/patch-notes [version]`。"

Verdict: **BLOCKED** — stop here without generating notes.
结论：**已阻塞** — 在此停止，不生成说明。

---

## Phase 2b: Detect Tone Guide and Template
## 阶段 2b：检测语气指南和模板

**Tone guide detection** — before drafting notes, check for writing style guidance:
**语气指南检测** — 在起草说明之前，检查写作风格指南：

1. Check `.claude/docs/technical-preferences.md` for any "tone", "voice", or "style"
   fields or sections.
2. Check `docs/PATCH-NOTES-STYLE.md` if it exists.
3. Check `design/community/tone-guide.md` if it exists.
4. If any source contains tone/voice/style instructions, extract them and apply
   them to the language and framing of the generated notes.
5. If no tone guidance is found anywhere, default to:
   player-friendly, non-technical language; enthusiastic but not hyperbolic;
   focus on what the player experiences, not what the developer changed.
1. 检查 `.claude/docs/technical-preferences.md` 中是否有任何 "tone"、"voice" 或 "style"
   字段或章节。
2. 如果存在，检查 `docs/PATCH-NOTES-STYLE.md`。
3. 如果存在，检查 `design/community/tone-guide.md`。
4. 如果任何来源包含语气/口吻/风格指令，提取它们并应用到
   生成说明的语言和表达框架中。
5. 如果在任何地方都找不到语气指南，默认为：
   对玩家友好、非技术性的语言；热情但不夸张；
   关注玩家的体验，而非开发者的改动。

**Template detection** — check whether a patch notes template exists:
**模板检测** — 检查是否存在补丁说明模板：

1. Glob for `docs/patch-notes-template.md` and `.claude/docs/templates/patch-notes-template.md`.
2. If found at either location, read it and use it as the output structure for Phase 4
   instead of the built-in style templates (Brief / Detailed / Full). Fill in the
   template's sections with the categorized data.
3. If not found, use the built-in style templates as defined in Phase 4.
1. 通过 Glob 查找 `docs/patch-notes-template.md` 和 `.claude/docs/templates/patch-notes-template.md`。
2. 如果在任一位置找到，读取它并将其用作阶段 4 的输出结构，
   而非内置样式模板（Brief / Detailed / Full）。用分类后的数据
   填充模板的各个章节。
3. 如果未找到，使用阶段 4 中定义的内置样式模板。

---

## Phase 3: Categorize and Translate
## 阶段 3：分类和翻译

Categorize all changes into player-facing categories:
将所有变更分类到面向玩家的类别中：

- **New Content**: new features, maps, characters, items, modes
- **Gameplay Changes**: balance adjustments, mechanic changes, progression changes
- **Quality of Life**: UI improvements, convenience features, accessibility
- **Bug Fixes**: grouped by system (combat, UI, networking, etc.)
- **Performance**: optimization improvements players might notice
- **Known Issues**: transparency about unresolved problems
- **新内容**：新功能、地图、角色、物品、模式
- **玩法变更**：平衡性调整、机制变更、进度变更
- **体验优化**：UI 改进、便利功能、可访问性
- **缺陷修复**：按系统分组（战斗、UI、网络等）
- **性能**：玩家可能会注意到的优化改进
- **已知问题**：对未解决问题的透明披露

Translate developer language to player language:
将开发者语言翻译为玩家语言：

- "Refactored damage calculation pipeline" → "Improved hit detection accuracy"
- "Fixed null reference in inventory manager" → "Fixed a crash when opening inventory"
- "Reduced GC allocations in combat loop" → "Improved combat performance"
- Remove purely internal changes that don't affect players
- Preserve specific numbers for balance changes (damage: 50 → 45)
- "重构伤害计算管线" → "提升了命中检测精度"
- "修复库存管理器中的空引用" → "修复了打开库存时的崩溃问题"
- "减少战斗循环中的 GC 分配" → "提升了战斗性能"
- 移除不影响玩家的纯内部变更
- 保留平衡性变更的具体数值（伤害：50 → 45）

---

## Phase 4: Generate Patch Notes
## 阶段 4：生成补丁说明

### Brief Style
### 简洁样式
```markdown
# Patch [Version] — [Title]

**New**
- [Feature 1]
- [Feature 2]

**Changes**
- [Balance/mechanic change with before → after values]

**Fixes**
- [Bug fix 1]
- [Bug fix 2]

**Known Issues**
- [Issue 1]
```
```markdown
# Patch [Version] — [Title]

**New**
- [Feature 1]
- [Feature 2]

**Changes**
- [Balance/mechanic change with before → after values]

**Fixes**
- [Bug fix 1]
- [Bug fix 2]

**Known Issues**
- [Issue 1]
```

### Detailed Style
### 详细样式
```markdown
# Patch [Version] — [Title]
*[Date]*

## Highlights
[1-2 sentence summary of the most exciting changes]

## New Content
### [Feature Name]
[2-3 sentences describing the feature and why players should be excited]

## Gameplay Changes
### Balance
| Change | Before | After | Reason |
| ---- | ---- | ---- | ---- |
| [Item/ability] | [old value] | [new value] | [brief rationale] |

### Mechanics
- **[Change]**: [explanation of what changed and why]

## Quality of Life
- [Improvement with context]

## Bug Fixes
### Combat
- Fixed [description of what players experienced]

### UI
- Fixed [description]

### Networking
- Fixed [description]

## Performance
- [Improvement players will notice]

## Known Issues
- [Issue and workaround if available]
```
```markdown
# Patch [Version] — [Title]
*[Date]*

## Highlights
[1-2 sentence summary of the most exciting changes]

## New Content
### [Feature Name]
[2-3 sentences describing the feature and why players should be excited]

## Gameplay Changes
### Balance
| Change | Before | After | Reason |
| ---- | ---- | ---- | ---- |
| [Item/ability] | [old value] | [new value] | [brief rationale] |

### Mechanics
- **[Change]**: [explanation of what changed and why]

## Quality of Life
- [Improvement with context]

## Bug Fixes
### Combat
- Fixed [description of what players experienced]

### UI
- Fixed [description]

### Networking
- Fixed [description]

## Performance
- [Improvement players will notice]

## Known Issues
- [Issue and workaround if available]
```

### Full Style
### 完整样式
Includes everything from Detailed, plus:
包含详细样式的所有内容，外加：
```markdown
## Developer Commentary
### [Topic]
> [Developer insight into a major change — why it was made, what was considered,
> what the team learned. Written in first-person team voice.]
```
```markdown
## Developer Commentary
### [Topic]
> [Developer insight into a major change — why it was made, what was considered,
> what the team learned. Written in first-person team voice.]
```

---

## Phase 5: Review Output
## 阶段 5：审查输出

Check the generated notes for:
检查生成的说明是否符合：

- No internal jargon (replace technical terms with player-friendly language)
- No references to internal systems, tickets, or sprint numbers
- Balance changes include before/after values
- Bug fixes describe the player experience, not the technical cause
- Tone matches the game's voice (adjust formality based on game style)
- 没有内部行话（用对玩家友好的语言替换技术术语）
- 没有对内部系统、工单或冲刺编号的引用
- 平衡性变更包含前/后数值
- 缺陷修复描述玩家体验，而非技术原因
- 语气与游戏口吻匹配（根据游戏风格调整正式程度）

---

## Phase 6: Save Patch Notes
## 阶段 6：保存补丁说明

Present the completed patch notes to the user along with: a count of changes by category, and any internal changes that were excluded (for review).
将完成的补丁说明呈现给用户，同时附上：按类别统计的变更数量，以及任何被排除的内部变更（供审查）。

Ask: "May I write these patch notes to `docs/patch-notes/[version].md`?"
询问："我可以将这些补丁说明写入 `docs/patch-notes/[version].md` 吗？"

If yes, write the file to `docs/patch-notes/[version].md`, creating the directory
if needed. Also write to `production/releases/[version]/patch-notes.md` as the
internal archive copy.
如果同意，将文件写入 `docs/patch-notes/[version].md`，如有需要则创建目录。
同时将内容写入 `production/releases/[version]/patch-notes.md` 作为
内部存档副本。

---

## Phase 7: Next Steps
## 阶段 7：后续步骤

Verdict: **COMPLETE** — patch notes generated and saved.
结论：**已完成** — 补丁说明已生成并保存。

- Run `/release-checklist` to verify all other release gates are met before publishing.
- Share the patch notes draft with the community-manager for tone review before posting publicly.
- 运行 `/release-checklist` 以在发布前验证所有其他发布门禁均已满足。
- 在公开发布前，将补丁说明草稿分享给 community-manager 进行语气审查。
