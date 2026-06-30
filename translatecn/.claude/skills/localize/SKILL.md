---
name: localize
description: "Full localization pipeline: scan for hardcoded strings, extract and manage string tables, validate translations, generate translator briefings, run cultural/sensitivity review, manage VO localization, test RTL/platform requirements, enforce string freeze, and report coverage."
argument-hint: "[scan|extract|validate|status|brief|cultural-review|vo-pipeline|rtl-check|freeze|qa]"
user-invocable: true
agent: localization-lead
allowed-tools: Read, Glob, Grep, Write, Bash, Task, AskUserQuestion
model: sonnet
---
---
name: localize
description: "完整的本地化流水线：扫描硬编码字符串、提取和管理字符串表、验证翻译、生成译员简报、运行文化/敏感性评审、管理 VO 本地化、测试 RTL/平台要求、强制字符串冻结，并报告覆盖率。"
argument-hint: "[scan|extract|validate|status|brief|cultural-review|vo-pipeline|rtl-check|freeze|qa]"
user-invocable: true
agent: localization-lead
allowed-tools: Read, Glob, Grep, Write, Bash, Task, AskUserQuestion
model: sonnet
---

# Localization Pipeline
# 本地化流水线

Localization is not just translation — it is the full process of making a game
feel native in every language and region. Poor localization breaks immersion,
confuses players, and blocks platform certification. This skill covers the
complete pipeline from string extraction through cultural review, VO recording,
RTL layout testing, and localization QA sign-off.
本地化不仅仅是翻译 — 它是使游戏在每种语言和地区都
感觉原生的完整过程。糟糕的本地化会破坏沉浸感、
使玩家困惑，并阻塞平台认证。此技能涵盖
从字符串提取到文化评审、VO 录制、
RTL 布局测试和本地化 QA 签署的完整流水线。

**Modes:**
- `scan` — Find hardcoded strings and localization anti-patterns (read-only)
- `extract` — Extract strings and generate translation-ready tables
- `validate` — Check translations for completeness, placeholders, and length
- `status` — Coverage matrix across all locales
- `brief` — Generate translator context briefing document for an external team
- `cultural-review` — Flag culturally sensitive content, symbols, colours, idioms
- `vo-pipeline` — Manage voice-over localization: scripts, recording specs, integration
- `rtl-check` — Validate RTL language layout, mirroring, and font support
- `freeze` — Enforce string freeze; lock source strings before translation begins
- `qa` — Run the full localization QA cycle before release
**模式：**
- `scan` — 查找硬编码字符串和本地化反模式（只读）
- `extract` — 提取字符串并生成可翻译的表
- `validate` — 检查翻译的完整度、占位符和长度
- `status` — 跨所有语言环境的覆盖率矩阵
- `brief` — 为外部团队生成译员上下文简报文档
- `cultural-review` — 标记文化敏感内容、符号、颜色、习语
- `vo-pipeline` — 管理配音本地化：脚本、录制规格、集成
- `rtl-check` — 验证 RTL 语言布局、镜像和字体支持
- `freeze` — 强制字符串冻结；在翻译开始之前锁定源字符串
- `qa` — 在发布之前运行完整的本地化 QA 周期

If no subcommand is provided, output usage and stop. Verdict: **FAIL** — missing required subcommand.
如果未提供子命令，输出用法并停止。Verdict: **FAIL** — 缺少必需的子命令。

---

## Phase 2A: Scan Mode
## 阶段 2A：Scan 模式

Search `src/` for hardcoded user-facing strings:
在 `src/` 中搜索硬编码的面向用户的字符串：

- String literals in UI code not wrapped in a localization function (`tr()`, `Tr()`, `NSLocalizedString`, `GetText`, etc.)
- Concatenated strings that should be parameterized
- Strings with positional placeholders (`%s`, `%d`) instead of named ones (`{playerName}`)
- Format strings that mix locale-sensitive data (numbers, dates, currencies) without locale-aware formatting
- UI 代码中未包装在本地化函数（`tr()`、`Tr()`、`NSLocalizedString`、`GetText` 等）中的字符串字面量
- 应该参数化的拼接字符串
- 使用位置占位符（`%s`、`%d`）而非命名占位符（`{playerName}`）的字符串
- 混合语言环境敏感数据（数字、日期、货币）而没有语言环境感知格式化的格式字符串

Search for localization anti-patterns:
搜索本地化反模式：

- Date/time formatting not using locale-aware functions
- Number formatting without locale awareness (`1,000` vs `1.000`)
- Text embedded in images or textures (flag asset files in `assets/`)
- Strings that assume left-to-right text direction (positional layout, string assembly order)
- Gender/plurality assumptions baked into string logic (must use plural forms or gender tokens)
- Hardcoded punctuation (e.g. `"You won!"` — exclamation styles vary by locale)
- 未使用语言环境感知函数的日期/时间格式化
- 没有语言环境感知的数字格式化（`1,000` vs `1.000`）
- 嵌入图像或纹理中的文本（标记 `assets/` 中的资产文件）
- 假设从左到右文本方向的字符串（位置布局、字符串组装顺序）
- 烘焙到字符串逻辑中的性别/复数假设（必须使用复数形式或性别令牌）
- 硬编码的标点符号（例如 `"You won!"` — 感叹号样式因语言环境而异）

Report all findings with file paths and line numbers. This mode is read-only — no files are written.
报告所有发现并附文件路径和行号。此模式是只读的 — 不写入任何文件。

---

## Phase 2B: Extract Mode
## 阶段 2B：Extract 模式

- Scan all source files for localized string references
- Compare against the existing string table in `assets/data/strings/`
- Generate new entries for strings not yet keyed
- Suggest key names following the convention: `[category].[subcategory].[description]`
  - Example: `ui.hud.health_label`, `dialogue.npc.merchant.greeting`, `menu.main.play_button`
- Each new entry must include a `context` field — a translator comment explaining:
  - Where it appears (which screen, which scene)
  - Maximum character length
  - Any placeholder meaning (`{playerName}` = the player's chosen display name)
  - Gender/plurality context if applicable
- 扫描所有源文件查找本地化字符串引用
- 与 `assets/data/strings/` 中现有的字符串表比较
- 为尚未键控的字符串生成新条目
- 按照约定建议键名：`[category].[subcategory].[description]`
  - 示例：`ui.hud.health_label`、`dialogue.npc.merchant.greeting`、`menu.main.play_button`
- 每个新条目必须包含 `context` 字段 — 译员注释说明：
  - 它出现在哪里（哪个屏幕、哪个场景）
  - 最大字符长度
  - 任何占位符含义（`{playerName}` = 玩家选择的显示名称）
  - 适用时的性别/复数上下文

Output a diff of new strings to add to the string table.
输出要添加到字符串表的新字符串差异。

Present the diff to the user. Ask: "May I write these new entries to `assets/data/strings/strings-en.json`?"
向用户呈现差异。询问："May I write these new entries to `assets/data/strings/strings-en.json`?"

If yes, write only the diff (new entries), not a full replacement. Verdict: **COMPLETE** — strings extracted and written.
如果是，仅写入差异（新条目），而非完整替换。Verdict: **COMPLETE** — 字符串已提取并写入。

---

## Phase 2C: Validate Mode
## 阶段 2C：Validate 模式

Read all string table files in `assets/data/strings/`. For each locale, check:
读取 `assets/data/strings/` 中的所有字符串表文件。对每个语言环境，检查：

- **Completeness** — key exists in source (en) but no translation for this locale
- **Placeholder mismatches** — source has `{name}` but translation omits it or adds extras
- **String length violations** — translation exceeds the character limit recorded in the source `context` field
- **Plural form count** — locale requires N plural forms; translation provides fewer
- **Orphaned keys** — translation exists but nothing in `src/` references the key
- **Stale translations** — source string changed after translation was written (flag for re-translation)
- **Encoding** — non-ASCII characters present and font atlas supports them (flag if uncertain)
- **完整度** — 键存在于源（en）中但此语言环境没有翻译
- **占位符不匹配** — 源有 `{name}` 但翻译省略了它或添加了额外的
- **字符串长度违规** — 翻译超过源 `context` 字段中记录的字符限制
- **复数形式计数** — 语言环境需要 N 个复数形式；翻译提供的较少
- **孤立键** — 翻译存在但 `src/` 中没有引用该键的内容
- **过时翻译** — 源字符串在翻译写入后更改（标记以重新翻译）
- **编码** — 存在非 ASCII 字符并且字体图集支持它们（如果不确定则标记）

Report validation results grouped by locale and severity. This mode is read-only — no files are written.
按语言环境和严重度分组报告验证结果。此模式是只读的 — 不写入任何文件。

---

## Phase 2D: Status Mode
## 阶段 2D：Status 模式

- Count total localizable strings in the source table
- Per locale: count translated, untranslated, stale (source changed since translation)
- Generate a coverage matrix:
- 统计源表中的可本地化字符串总数
- 每个语言环境：统计已翻译、未翻译、过时（自翻译以来源已更改）
- 生成覆盖率矩阵：

```markdown
## Localization Status
Generated: [Date]
String freeze: [Active / Not yet called / Lifted]

| Locale | Total | Translated | Missing | Stale | Coverage |
|--------|-------|-----------|---------|-------|----------|
| en (source) | [N] | [N] | 0 | 0 | 100% |
| [locale] | [N] | [N] | [N] | [N] | [X]% |

### Issues
- [N] hardcoded strings found in source code (run /localize scan)
- [N] strings exceeding character limits
- [N] placeholder mismatches
- [N] orphaned keys
- [N] strings added after freeze was called (freeze violations)
```
```markdown
## Localization Status
Generated: [Date]
String freeze: [Active / Not yet called / Lifted]

| Locale | Total | Translated | Missing | Stale | Coverage |
|--------|-------|-----------|---------|-------|----------|
| en (source) | [N] | [N] | 0 | 0 | 100% |
| [locale] | [N] | [N] | [N] | [N] | [X]% |

### Issues
- [N] hardcoded strings found in source code (run /localize scan)
- [N] strings exceeding character limits
- [N] placeholder mismatches
- [N] orphaned keys
- [N] strings added after freeze was called (freeze violations)
```

This mode is read-only — no files are written.
此模式是只读的 — 不写入任何文件。

---

## Phase 2E: Brief Mode
## 阶段 2E：Brief 模式

Generate a translator context briefing document. This document is sent to the
external translation team or localisation vendor alongside the string table export.
生成译员上下文简报文档。此文档与字符串表导出一起
发送给外部翻译团队或本地化供应商。

Read:
- `design/gdd/` — extract game genre, tone, setting, character names
- `assets/data/strings/strings-en.json` — the source string table
- Any existing lore or narrative documents in `design/narrative/`
读取：
- `design/gdd/` — 提取游戏类型、基调、设定、角色名称
- `assets/data/strings/strings-en.json` — 源字符串表
- `design/narrative/` 中任何现有的传说或叙事文档

Generate `production/localization/translator-brief-[locale]-[date].md`:
生成 `production/localization/translator-brief-[locale]-[date].md`：

```markdown
# Translator Brief — [Game Name] — [Locale]

## Game Overview
[2-3 paragraph summary of the game, genre, tone, and audience]

## Tone and Voice
- **Overall tone**: [e.g., "Darkly comic, not slapstick — think Terry Pratchett, not Looney Tunes"]
- **Player address**: [e.g., "Second person, informal. Never formal 'vous' — always 'tu' for French"]
- **Profanity policy**: [e.g., "Mild — PG-13 equivalent. Match intensity to source, do not soften or escalate"]
- **Humour**: [e.g., "Wordplay exists — if a pun cannot translate, invent an equivalent local joke; do not translate literally"]

## Character Glossary
| Name | Role | Personality | Notes |
|------|------|-------------|-------|
| [Name] | [Role] | [Personality] | [Do not translate / transliterate as X] |

## World Glossary
| Term | Meaning | Notes |
|------|---------|-------|
| [Term] | [What it means] | [Keep in English / translate as X] |

## Do Not Translate List
The following must appear verbatim in all locales:
- [Game name]
- [UI terms that match in-engine labels]
- [Brand or trademark names]

## Placeholder Reference
| Placeholder | What it represents | Example |
|-------------|-------------------|---------|
| `{playerName}` | Player's chosen display name | "Shadowblade" |
| `{count}` | Integer quantity | "3" |

## Character Limits
Tight UI fields with hard limits are marked in the string table `context` field.
Where no limit is stated, target ±30% of the English length as a guideline.

## Contact
Direct questions to: [placeholder for user/team contact]
Delivery format: JSON, same schema as strings-en.json
```
```markdown
# Translator Brief — [Game Name] — [Locale]

## Game Overview
[2-3 paragraph summary of the game, genre, tone, and audience]

## Tone and Voice
- **Overall tone**: [e.g., "Darkly comic, not slapstick — think Terry Pratchett, not Looney Tunes"]
- **Player address**: [e.g., "Second person, informal. Never formal 'vous' — always 'tu' for French"]
- **Profanity policy**: [e.g., "Mild — PG-13 equivalent. Match intensity to source, do not soften or escalate"]
- **Humour**: [e.g., "Wordplay exists — if a pun cannot translate, invent an equivalent local joke; do not translate literally"]

## Character Glossary
| Name | Role | Personality | Notes |
|------|------|-------------|-------|
| [Name] | [Role] | [Personality] | [Do not translate / transliterate as X] |

## World Glossary
| Term | Meaning | Notes |
|------|---------|-------|
| [Term] | [What it means] | [Keep in English / translate as X] |

## Do Not Translate List
The following must appear verbatim in all locales:
- [Game name]
- [UI terms that match in-engine labels]
- [Brand or trademark names]

## Placeholder Reference
| Placeholder | What it represents | Example |
|-------------|-------------------|---------|
| `{playerName}` | Player's chosen display name | "Shadowblade" |
| `{count}` | Integer quantity | "3" |

## Character Limits
Tight UI fields with hard limits are marked in the string table `context` field.
Where no limit is stated, target ±30% of the English length as a guideline.

## Contact
Direct questions to: [placeholder for user/team contact]
Delivery format: JSON, same schema as strings-en.json
```

Ask: "May I write this translator brief to `production/localization/translator-brief-[locale]-[date].md`?"
询问："May I write this translator brief to `production/localization/translator-brief-[locale]-[date].md`?"

---

## Phase 2F: Cultural Review Mode
## 阶段 2F：Cultural Review 模式

Spawn `localization-lead` via Task. Ask them to audit the following for cultural sensitivity across the target locales (read from `assets/data/strings/` and `assets/`):
通过 Task 生成 `localization-lead`。要求他们审核以下内容在目标语言环境中的文化敏感性（从 `assets/data/strings/` 和 `assets/` 读取）：

### Content Areas to Review
### 要审核的内容领域

**Symbols and gestures**
- Thumbs up, OK hand, peace sign — meanings vary by region
- Religious or spiritual symbols in art, UI, or audio
- National flags, map representations, disputed territories
**符号和手势**
- 竖大拇指、OK 手势、和平手势 — 含义因地区而异
- 艺术、UI 或音频中的宗教或精神符号
- 国旗、地图表示、有争议的领土

**Colours**
- White (mourning in some Asian cultures), green (political associations in some regions), red (luck vs danger)
- Alert/warning colours that conflict with cultural associations
**颜色**
- 白色（在某些亚洲文化中表示哀悼）、绿色（在某些地区有政治关联）、红色（幸运 vs 危险）
- 与文化关联冲突的警报/警告颜色

**Numbers**
- 4 (death in Japanese/Chinese), 13, 666 — flag use in UI (room numbers, item counts, prices)
**数字**
- 4（日语/汉语中的死亡）、13、666 — 标记在 UI 中的使用（房间号、物品计数、价格）

**Humour and idioms**
- Idioms that translate as offensive in other locales
- Toilet/bodily humour that is inappropriate in some markets (notably Japan, Germany, Middle East)
- Dark humour around topics that are culturally sensitive in specific regions
**幽默和习语**
- 在其他语言环境中翻译为冒犯性的习语
- 在某些市场（特别是日本、德国、中东）不合适的厕所/身体幽默
- 围绕在特定地区文化敏感的话题的黑色幽默

**Violence and content ratings**
- Content that would require ratings changes in DE (Germany), AU (Australia), CN (China), or AE (UAE)
- Blood colour, gore level, drug references — flag all for region-specific asset variants if needed
**暴力和内容分级**
- 在 DE（德国）、AU（澳大利亚）、CN（中国）或 AE（阿联酋）中需要更改分级的内容
- 血液颜色、血腥程度、毒品引用 — 如果需要，全部标记为地区特定资产变体

**Names and representations**
- Character names that are offensive, profane, or carry negative meaning in target locales
- Stereotyped representation of nationalities, religions, or ethnic groups
**名称和表示**
- 在目标语言环境中具有冒犯性、亵渎性或负面含义的角色名称
- 对国籍、宗教或族裔群体的刻板表示

Present findings as a table:
以表格形式呈现发现：

| Finding | Locale(s) Affected | Severity | Recommended Action |
|---------|--------------------|----------|--------------------|
| [Description] | [Locale] | [BLOCKING / ADVISORY / NOTE] | [Change / Flag for review / Accept] |
| 发现 | 受影响的语言环境 | 严重度 | 建议行动 |
|---------|--------------------|----------|--------------------|
| [描述] | [语言环境] | [BLOCKING / ADVISORY / NOTE] | [更改 / 标记评审 / 接受] |

BLOCKING = must fix before shipping that locale. ADVISORY = recommend change. NOTE = informational only.
BLOCKING = 在交付该语言环境之前必须修复。ADVISORY = 建议更改。NOTE = 仅供参考。

Ask: "May I write this cultural review report to `production/localization/cultural-review-[date].md`?"
询问："May I write this cultural review report to `production/localization/cultural-review-[date].md`?"

---

## Phase 2G: VO Pipeline Mode
## 阶段 2G：VO Pipeline 模式

Manage the voice-over localization process. Determine the sub-task from the argument:
管理配音本地化流程。从参数确定子任务：

- `vo-pipeline scan` — identify all dialogue lines that require VO recording
- `vo-pipeline script` — generate recording scripts with director notes
- `vo-pipeline validate` — check that all recorded VO files are present and correctly named
- `vo-pipeline integrate` — verify VO files are correctly referenced in code/assets
- `vo-pipeline scan` — 识别所有需要 VO 录制的对话行
- `vo-pipeline script` — 生成带有导演注释的录制脚本
- `vo-pipeline validate` — 检查所有录制的 VO 文件是否存在且命名正确
- `vo-pipeline integrate` — 验证 VO 文件在代码/资产中被正确引用

### VO Pipeline: Scan
### VO Pipeline：Scan

Read `assets/data/strings/` and `design/narrative/`. Identify:
- All dialogue lines (keys matching `dialogue.*`) with source text
- Lines already recorded (audio file exists in `assets/audio/vo/`)
- Lines not yet recorded
读取 `assets/data/strings/` 和 `design/narrative/`。识别：
- 所有对话行（匹配 `dialogue.*` 的键）及其源文本
- 已录制的行（音频文件存在于 `assets/audio/vo/`）
- 尚未录制的行

Output a recording manifest:
输出录制清单：

```
## VO Recording Manifest — [Date]

| Key | Character | Source Line | Status |
|-----|-----------|-------------|--------|
| dialogue.npc.merchant.greeting | Merchant | "Welcome, traveller." | Recorded |
| dialogue.npc.merchant.haggle | Merchant | "That's my final offer." | Needs recording |
```
```
## VO Recording Manifest — [Date]

| Key | Character | Source Line | Status |
|-----|-----------|-------------|--------|
| dialogue.npc.merchant.greeting | Merchant | "Welcome, traveller." | Recorded |
| dialogue.npc.merchant.haggle | Merchant | "That's my final offer." | Needs recording |
```

### VO Pipeline: Script
### VO Pipeline：Script

Generate a recording script document for each character, grouped by scene. Include:
为每个角色生成录制脚本文档，按场景分组。包括：

- Character name and brief personality note
- Full dialogue line with pronunciation guide for unusual proper nouns
- Emotion/direction note for each line (`[Warm, welcoming]`, `[Annoyed, clipped]`)
- Any lines that are responses in a conversation (provide context: "Player just said X")
- 角色名称和简短的性格注释
- 完整的对话行以及不寻常专有名词的发音指南
- 每行的情感/指导注释（`[Warm, welcoming]`、`[Annoyed, clipped]`）
- 任何是对话中回应的行（提供上下文："Player just said X"）

Ask: "May I write the VO recording scripts to `production/localization/vo-scripts-[locale]-[date].md`?"
询问："May I write the VO recording scripts to `production/localization/vo-scripts-[locale]-[date].md`?"

### VO Pipeline: Validate
### VO Pipeline：Validate

Glob `assets/audio/vo/[locale]/` for all `.wav`/`.ogg` files. Cross-reference against the VO manifest. Report:
- Missing files (line in script, no audio file)
- Extra files (audio file exists, no matching string key)
- Naming convention violations
Glob `assets/audio/vo/[locale]/` 查找所有 `.wav`/`.ogg` 文件。与 VO 清单交叉引用。报告：
- 缺失的文件（脚本中有行，但没有音频文件）
- 多余的文件（音频文件存在，但没有匹配的字符串键）
- 命名约定违规

### VO Pipeline: Integrate
### VO Pipeline：Integrate

Grep `src/` for VO audio references. Verify each referenced path exists in `assets/audio/vo/[locale]/`. Report broken references.
Grep `src/` 查找 VO 音频引用。验证每个引用的路径是否存在于 `assets/audio/vo/[locale]/`。报告损坏的引用。

---

## Phase 2H: RTL Check Mode
## 阶段 2H：RTL Check 模式

Right-to-left languages (Arabic, Hebrew, Persian, Urdu) require layout mirroring beyond
just translating text. This mode validates the implementation.
从右到左的语言（阿拉伯语、希伯来语、波斯语、乌尔都语）需要在
文本翻译之外进行布局镜像。此模式验证实现。

Read `.claude/docs/technical-preferences.md` to determine the engine. Then check:
读取 `.claude/docs/technical-preferences.md` 确定引擎。然后检查：

**Layout mirroring**
- Is RTL layout enabled in the engine? (Godot: `Control.layout_direction`, Unity: `RTL Support` package, Unreal: text direction flags)
- Are all UI containers set to auto-mirror, or are positions hardcoded?
- Do progress bars, health bars, and directional indicators mirror correctly?
**布局镜像**
- 引擎中是否启用了 RTL 布局？（Godot：`Control.layout_direction`，Unity：`RTL Support` 包，Unreal：文本方向标志）
- 所有 UI 容器是否设置为自动镜像，还是位置被硬编码？
- 进度条、生命条和方向指示器是否正确镜像？

**Text rendering**
- Are fonts loaded that support Arabic/Hebrew character sets?
- Is Arabic text rendered with correct ligatures (connected script)?
- Are numbers displayed as Eastern Arabic numerals where required?
**文本渲染**
- 是否加载了支持阿拉伯语/希伯来语字符集的字体？
- 阿拉伯语文本是否以正确的连字（连接脚本）渲染？
- 数字是否在需要时显示为东阿拉伯数字？

**String assembly**
- Are there any string concatenations that assume left-to-right reading order?
- Do `{placeholder}` positions in sentences work correctly when sentence structure is reversed?
**字符串组装**
- 是否有任何假设从左到右阅读顺序的字符串拼接？
- 当句子结构反转时，句子中的 `{placeholder}` 位置是否正确工作？

**Asset review**
- Are there UI icons with directional arrows or asymmetric designs that need mirrored variants?
- Do any text-in-image assets exist that require RTL versions?
**资产评审**
- 是否有带方向箭头或非对称设计的 UI 图标需要镜像变体？
- 是否存在任何需要 RTL 版本的图像内文本资产？

Grep patterns to check:
- Engine-specific RTL flags in scene/prefab files
- Any `HBoxContainer`, `LinearLayout`, `HorizontalBox` nodes — verify layout_direction settings
- String concatenation with `+` near dialogue or UI code
要检查的 Grep 模式：
- 场景/预制件文件中引擎特定的 RTL 标志
- 任何 `HBoxContainer`、`LinearLayout`、`HorizontalBox` 节点 — 验证 layout_direction 设置
- 对话或 UI 代码附近使用 `+` 的字符串拼接

Report findings. Flag BLOCKING issues (content unreadable without fix) vs ADVISORY (cosmetic improvements).
报告发现。将 BLOCKING 问题（不修复内容不可读）与 ADVISORY（外观改进）区分标记。

Ask: "May I write this RTL check report to `production/localization/rtl-check-[date].md`?"
询问："May I write this RTL check report to `production/localization/rtl-check-[date].md`?"

---

## Phase 2I: Freeze Mode
## 阶段 2I：Freeze 模式

String freeze locks the source (English) string table so that translations can proceed
without the source changing under the translators.
字符串冻结锁定源（英语）字符串表，以便翻译可以继续
而不会在译员工作期间源发生变化。

### freeze call
### freeze 调用

Check current freeze status in `production/localization/freeze-status.md` (if it exists).
在 `production/localization/freeze-status.md`（如果存在）中检查当前冻结状态。

If already frozen:
> "String freeze is currently ACTIVE (called [date]). [N] strings have been added or modified since freeze. These are freeze violations — they require re-translation or an approved freeze lift."
如果已冻结：
> "String freeze is currently ACTIVE (called [date]). [N] strings have been added or modified since freeze. These are freeze violations — they require re-translation or an approved freeze lift."

If not frozen, present the pre-freeze checklist:
如果未冻结，呈现冻结前检查清单：

```
Pre-Freeze Checklist
[ ] All planned UI screens are implemented
[ ] All dialogue lines are final (no further narrative revisions planned)
[ ] All system strings (error messages, tutorial text) are complete
[ ] /localize scan shows zero hardcoded strings
[ ] /localize validate shows no placeholder mismatches in source (en)
[ ] Marketing strings (store description, achievements) are final
```
```
Pre-Freeze Checklist
[ ] All planned UI screens are implemented
[ ] All dialogue lines are final (no further narrative revisions planned)
[ ] All system strings (error messages, tutorial text) are complete
[ ] /localize scan shows zero hardcoded strings
[ ] /localize validate shows no placeholder mismatches in source (en)
[ ] Marketing strings (store description, achievements) are final
```

Use `AskUserQuestion`:
- Prompt: "Are all items above confirmed? Calling string freeze locks the source table."
- Options: `[A] Yes — call string freeze now` / `[B] No — I still have strings to add`
使用 `AskUserQuestion`：
- 提示："Are all items above confirmed? Calling string freeze locks the source table."
- 选项：`[A] Yes — call string freeze now` / `[B] No — I still have strings to add`

If [A]: Write `production/localization/freeze-status.md`:
如果 [A]：写入 `production/localization/freeze-status.md`：

```markdown
# String Freeze Status

**Status**: ACTIVE
**Called**: [date]
**Called by**: [user]
**Total strings at freeze**: [N]

## Post-Freeze Changes
[Any strings added or modified after freeze are listed here automatically by /localize extract]
```
```markdown
# String Freeze Status

**Status**: ACTIVE
**Called**: [date]
**Called by**: [user]
**Total strings at freeze**: [N]

## Post-Freeze Changes
[Any strings added or modified after freeze are listed here automatically by /localize extract]
```

### freeze lift
### freeze lift

If argument includes `lift`: update `freeze-status.md` Status to `LIFTED`, record the reason and date. Warn: "Lifting the freeze requires re-translation of all modified strings. Notify the translation team."
如果参数包含 `lift`：将 `freeze-status.md` 的 Status 更新为 `LIFTED`，记录原因和日期。警告："Lifting the freeze requires re-translation of all modified strings. Notify the translation team."

### freeze check (auto-integrated into extract)
### freeze check（自动集成到 extract）

When `extract` mode finds new or modified strings and `freeze-status.md` shows Status: ACTIVE — append the new keys to `## Post-Freeze Changes` and warn:
> "⚠️ String freeze is active. [N] new/modified strings have been added. These are freeze violations. Notify your localization vendor before proceeding."
当 `extract` 模式发现新的或修改的字符串，并且 `freeze-status.md` 显示 Status: ACTIVE — 将新键追加到 `## Post-Freeze Changes` 并警告：
> "⚠️ String freeze is active. [N] new/modified strings have been added. These are freeze violations. Notify your localization vendor before proceeding."

---

## Phase 2J: QA Mode
## 阶段 2J：QA 模式

Localization QA is a dedicated pass that runs after translations are delivered but
before any locale ships. This is not the same as `/validate` (which checks completeness)
— this is a structured playthrough-based quality check.
本地化 QA 是一个专门的检查，在翻译交付之后
但任何语言环境交付之前运行。这与 `/validate`（检查完整度）不同
— 这是一个基于结构化试玩的质量检查。

Spawn `localization-lead` via Task with:
- The target locale(s) to QA
- The list of all screens/flows in the game (from `design/gdd/` or `/content-audit` output)
- The current `/localize validate` report
- The cultural review report (if it exists)
通过 Task 生成 `localization-lead` 并附：
- 要 QA 的目标语言环境
- 游戏中所有屏幕/流程的列表（来自 `design/gdd/` 或 `/content-audit` 输出）
- 当前的 `/localize validate` 报告
- 文化评审报告（如果存在）

Ask the localization-lead to produce a QA plan covering:
要求 localization-lead 生成涵盖以下内容的 QA 计划：

1. **Functional string check** — every string displays in-game without truncation, placeholder errors, or encoding corruption
2. **UI overflow check** — translated strings that exceed UI bounds (even if within character limits, some languages expand)
3. **Contextual accuracy** — a sample of 10% of strings reviewed in-game for translation accuracy and natural phrasing
4. **Cultural review items** — verify all BLOCKING items from the cultural review are resolved
5. **VO sync check** — if VO exists, verify lip sync or subtitle timing is acceptable after translation
6. **Platform cert requirements** — check platform-specific localization requirements (age ratings text, legal notices, ESRB/PEGI/CERO text)
1. **功能字符串检查** — 每个字符串在游戏内显示无截断、占位符错误或编码损坏
2. **UI 溢出检查** — 翻译字符串超出 UI 边界（即使在字符限制内，某些语言也会扩展）
3. **上下文准确性** — 在游戏内审查 10% 的字符串样本以检查翻译准确性和自然措辞
4. **文化评审项** — 验证文化评审中的所有 BLOCKING 项已解决
5. **VO 同步检查** — 如果存在 VO，验证翻译后口型同步或字幕时序可接受
6. **平台认证要求** — 检查平台特定的本地化要求（年龄分级文本、法律声明、ESRB/PEGI/CERO 文本）

Output a QA verdict per locale:
每个语言环境输出一个 QA 结论：

```
## Localization QA Verdict — [Locale]

**Status**: PASS / PASS WITH CONDITIONS / FAIL
**Reviewed by**: localization-lead
**Date**: [date]

### Findings
| ID | Area | Description | Severity | Status |
|----|------|-------------|----------|--------|
| LOC-001 | UI Overflow | "Settings" button text overflows on [Screen] | BLOCKING | Open |
| LOC-002 | Translation | [Key] translation is literal — sounds unnatural | ADVISORY | Open |

### Conditions (if PASS WITH CONDITIONS)
- [Condition 1 — must resolve before ship]

### Sign-Off
[ ] All BLOCKING findings resolved
[ ] Producer approves shipping [Locale]
```
```
## Localization QA Verdict — [Locale]

**Status**: PASS / PASS WITH CONDITIONS / FAIL
**Reviewed by**: localization-lead
**Date**: [date]

### Findings
| ID | Area | Description | Severity | Status |
|----|------|-------------|----------|--------|
| LOC-001 | UI Overflow | "Settings" button text overflows on [Screen] | BLOCKING | Open |
| LOC-002 | Translation | [Key] translation is literal — sounds unnatural | ADVISORY | Open |

### Conditions (if PASS WITH CONDITIONS)
- [Condition 1 — must resolve before ship]

### Sign-Off
[ ] All BLOCKING findings resolved
[ ] Producer approves shipping [Locale]
```

Ask: "May I write this localization QA report to `production/localization/loc-qa-[locale]-[date].md`?"
询问："May I write this localization QA report to `production/localization/loc-qa-[locale]-[date].md`?"

**Gate integration**: The Polish → Release gate requires a PASS or PASS WITH CONDITIONS verdict for every locale being shipped. A FAIL blocks release for that locale only — other locales may still proceed if their QA passes.
**门禁集成**：Polish → Release 门禁要求每个要交付的语言环境获得 PASS 或 PASS WITH CONDITIONS 结论。FAIL 仅阻塞该语言环境的发布 — 其他语言环境如果其 QA 通过仍可继续。

---

## Phase 3: Rules and Next Steps
## 阶段 3：规则和后续步骤

### Rules
### 规则

- English (en) is always the source locale
- Every string table entry must include a `context` field with translator notes, character limits, and placeholder meaning
- Never modify translation files directly — generate diffs for review
- Character limits must be defined per-UI-element and enforced in validate mode
- String freeze must be called before sending strings to translators — never translate a moving target
- RTL support must be designed in from the start — retrofitting RTL layout is expensive
- Cultural review is required for any locale where the game will be sold commercially
- VO scripts must include director notes — raw dialogue lines produce flat recordings
- 英语（en）始终是源语言环境
- 每个字符串表条目必须包含 `context` 字段，附译员注释、字符限制和占位符含义
- 切勿直接修改翻译文件 — 生成差异以供评审
- 字符限制必须按 UI 元素定义并在 validate 模式中强制执行
- 在将字符串发送给译员之前必须调用字符串冻结 — 切勿翻译移动的目标
- RTL 支持必须从一开始就设计进去 — 改造 RTL 布局成本高昂
- 对于游戏将商业化销售的任何语言环境，文化评审是必需的
- VO 脚本必须包含导演注释 — 原始对话行会产生平淡的录制

### Recommended Workflow
### 推荐工作流

```
/localize scan            → find hardcoded strings
/localize extract         → build string table
/localize freeze          → lock source before sending to translators
/localize brief           → generate translator briefing document
[Send to translators]
/localize validate        → check returned translations
/localize cultural-review → flag culturally sensitive content
/localize rtl-check       → if shipping Arabic / Hebrew / Persian
/localize vo-pipeline     → if shipping dubbed VO
/localize qa              → full localization QA pass
```
```
/localize scan            → find hardcoded strings
/localize extract         → build string table
/localize freeze          → lock source before sending to translators
/localize brief           → generate translator briefing document
[Send to translators]
/localize validate        → check returned translations
/localize cultural-review → flag culturally sensitive content
/localize rtl-check       → if shipping Arabic / Hebrew / Persian
/localize vo-pipeline     → if shipping dubbed VO
/localize qa              → full localization QA pass
```

After `qa` returns PASS for all shipping locales, include the QA report path when running `/gate-check release`.
在 `qa` 为所有交付语言环境返回 PASS 之后，在运行 `/gate-check release` 时包含 QA 报告路径。
