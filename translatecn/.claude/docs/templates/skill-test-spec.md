# Skill Test Spec: /[skill-name]
# 技能测试规格：/[skill-name]

## Skill Summary
## 技能摘要

[One paragraph: what this skill does, when to use it, what it produces. Include
the primary output artifact, the verdict format it uses, and which pipeline stage
it belongs to.]
[一段：此技能做什么、何时使用、产生什么。包含主要输出工件、
它使用的裁决格式以及它属于哪个流水线阶段。]

---

## Static Assertions (Structural)
## 静态断言（结构）

Verified automatically by `/skill-test static` — no fixture needed.
由 `/skill-test static` 自动验证 — 无需 fixture。

- [ ] Has required frontmatter fields: `name`, `description`, `argument-hint`, `user-invocable`, `allowed-tools`
- [ ] Has ≥2 phase headings (## Phase N or numbered ## sections)
- [ ] Contains verdict keywords: [list the ones expected, e.g., PASS, FAIL, CONCERNS]
- [ ] Contains "May I write" collaborative protocol language (if skill writes files)
- [ ] Has a next-step handoff at the end
- [ ] 有所需 frontmatter 字段：`name`、`description`、`argument-hint`、`user-invocable`、`allowed-tools`
- [ ] 有 ≥2 个阶段标题（## Phase N 或编号 ## 节）
- [ ] 包含裁决关键词：[列出预期，例如，PASS, FAIL, CONCERNS]
- [ ] 包含 "May I write" 协作协议语言（如技能写文件）
- [ ] 末尾有下一步交接

---

## Test Cases
## 测试用例

### Case 1: Happy Path — [short description]
### 用例 1：快乐路径 — [简短描述]

**Fixture:** [Describe the assumed project state. Which files exist? What do they
contain? E.g., "game-concept.md exists with all 8 required sections complete.
systems-index.md exists. All MVP GDDs are present and individually reviewed."]
**Fixture：**[描述假设的项目状态。哪些文件存在？它们包含什么？
例如，"game-concept.md 存在且所有 8 个必需节完整。systems-index.md 存在。
所有 MVP GDD 都存在并已单独审核。"]

**Input:** `/[skill-name] [args]`
**输入：**`/[skill-name] [args]`

**Expected behavior:**
1. [Phase 1 action — what the skill should read or check]
2. [Phase 2 action — what the skill should evaluate]
3. [Phase N action — what the skill should output]
**预期行为：**
1. [阶段 1 动作 — 技能应读或检查什么]
2. [阶段 2 动作 — 技能应评估什么]
3. [阶段 N 动作 — 技能应输出什么]

**Assertions:**
- [ ] Skill reads [specific file] before producing output
- [ ] Output includes verdict keyword [PASS/FAIL/etc.]
- [ ] Output lists [specific content] from the fixture
- [ ] Skill asks for approval before writing any file
**断言：**
- [ ] 技能在产生输出前读 [特定文件]
- [ ] 输出包含裁决关键词 [PASS/FAIL/etc.]
- [ ] 输出列出 fixture 中的 [特定内容]
- [ ] 技能在写任何文件前请求批准

---

### Case 2: Failure Path — [short description, e.g., "Missing required artifact"]
### 用例 2：失败路径 — [简短描述，例如，"缺失所需工件"]

**Fixture:** [Describe the failure state. E.g., "game-concept.md is missing.
No files exist in design/gdd/."]
**Fixture：**[描述失败状态。例如，"game-concept.md 缺失。
design/gdd/ 中无文件。"]

**Input:** `/[skill-name] [args]`
**输入：**`/[skill-name] [args]`

**Expected behavior:**
1. [Phase 1: skill detects missing file]
2. [Phase 2: skill surfaces the gap rather than assuming OK]
3. [Output: FAIL or BLOCKED verdict with specific blocker named]
**预期行为：**
1. [阶段 1：技能检测到缺失文件]
2. [阶段 2：技能揭示缺口而非假设 OK]
3. [输出：FAIL 或 BLOCKED 裁决并命名具体阻碍]

**Assertions:**
- [ ] Skill does NOT output PASS when the fixture is incomplete
- [ ] Skill names the specific missing artifact
- [ ] Skill suggests a remediation action (e.g., "Run /[other-skill]")
- [ ] Skill does not create files to fill in the gap without asking
**断言：**
- [ ] fixture 不完整时技能不输出 PASS
- [ ] 技能命名具体缺失工件
- [ ] 技能建议补救动作（例如，"运行 /[other-skill]"）
- [ ] 技能不在未询问时创建文件填补缺口

---

### Case 3: Edge Case — [short description, e.g., "No argument provided"]
### 用例 3：边界情况 — [简短描述，例如，"未提供参数"]

**Fixture:** [State of project files for this case]
**Fixture：**[此用例的项目文件状态]

**Input:** `/[skill-name]` (no argument)
**输入：**`/[skill-name]`（无参数）

**Expected behavior:**
1. [What the skill should do when invoked without arguments]
**预期行为：**
1. [无参数调用时技能应做什么]

**Assertions:**
- [ ] [assertion]
**断言：**
- [ ] [断言]

---

## Protocol Compliance
## 协议合规

- [ ] Uses "May I write" before all file writes
- [ ] Presents findings or report before asking for write approval
- [ ] Ends with a recommended next step or follow-up skill
- [ ] Never auto-creates files without explicit user approval
- [ ] Does not skip phases or jump straight to a verdict without checking
- [ ] 所有文件写入前使用 "May I write"
- [ ] 请求写入批准前呈现发现或报告
- [ ] 以建议的下一步或后续技能结束
- [ ] 永不在无显式用户批准时自动创建文件
- [ ] 不跳过阶段或不经检查直接跳到裁决

---

## Coverage Notes
## 覆盖备注

[Document what is intentionally NOT tested in this spec and why. Examples:
- "Case 3 (all-mode) is not covered because it runs too many checks to evaluate
  in a single spec — test each sub-mode individually."
- "The database integration path is not covered as it requires a live environment."
- "Edge cases involving corrupted YAML files are deferred to a future spec."]
[记录此规格中故意未测试的内容及原因。示例：
- "用例 3（all-mode）未覆盖因为它运行太多检查无法在单一规格中评估 —
  单独测试每个子模式。"
- "数据库集成路径未覆盖因为它需实时环境。"
- "涉及损坏 YAML 文件的边界情况延后到未来规格。"]
