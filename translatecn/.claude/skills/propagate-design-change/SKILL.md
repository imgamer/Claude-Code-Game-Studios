---
name: propagate-design-change
description: "When a GDD is revised, scans all ADRs and the traceability index to identify which architectural decisions are now potentially stale. Produces a change impact report and guides the user through resolution."
argument-hint: "[path/to/changed-gdd.md]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, Task
model: sonnet
agent: technical-director
---
---
name: propagate-design-change
description: "当 GDD 被修订时，扫描所有 ADR 和可追溯性索引，识别哪些架构决策现在可能已过时。生成变更影响报告并引导用户完成解决流程。"
argument-hint: "[path/to/changed-gdd.md]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Bash, Task
model: sonnet
agent: technical-director
---

# Propagate Design Change
# 传播设计变更

When a GDD changes, architectural decisions written against it may no longer be
valid. This skill finds every affected ADR, compares what the ADR assumed against
what the GDD now says, and guides the user through resolution.
当 GDD 发生变更时，针对它编写的架构决策可能不再
有效。此技能查找每个受影响的 ADR，将 ADR 所假设的内容与
GDD 现在所述的内容进行比较，并引导用户完成解决流程。

**Usage:** `/propagate-design-change design/gdd/combat-system.md`
**用法：** `/propagate-design-change design/gdd/combat-system.md`

---

## 1. Validate Argument
## 1. 验证参数

A GDD path argument is **required**. If missing, fail with:
GDD 路径参数是**必需的**。如果缺失，失败并提示：
> "Usage: `/propagate-design-change design/gdd/[system].md`
> Provide the path to the GDD that was changed."
> "用法：`/propagate-design-change design/gdd/[system].md`
> 提供被变更的 GDD 的路径。"

Verify the file exists. If not, fail with:
验证文件存在。如果不存在，失败并提示：
> "[path] not found. Check the path and try again."
> "未找到 [path]。请检查路径后重试。"

---

## 2. Read the Changed GDD
## 2. 读取已变更的 GDD

Read the current GDD in full.
完整读取当前的 GDD。

---

## 3. Read the Previous Version
## 3. 读取先前版本

Run git to get the previous committed version:
运行 git 获取先前提交的版本：

```bash
git show HEAD:design/gdd/[filename].md
```
```bash
git show HEAD:design/gdd/[filename].md
```

If the file has no git history (new file), report:
如果文件没有 git 历史（新文件），报告：
> "No previous version in git — this appears to be a new GDD, not a revision.
> Nothing to propagate."
> "git 中没有先前版本 — 这似乎是一个新的 GDD，而非修订版。
> 没有需要传播的内容。"

If git returns the previous version, do a conceptual diff:
如果 git 返回了先前版本，进行概念性差异比较：
- Identify sections that changed (new rules, removed rules, modified formulas,
  changed acceptance criteria, changed tuning knobs)
- Identify sections that are unchanged
- Produce a change summary:
- 识别已变更的章节（新规则、删除的规则、修改的公式、
  变更的验收标准、变更的调优参数）
- 识别未变更的章节
- 生成变更摘要：

```
## Change Summary: [GDD filename]
Date of revision: [today]

Changed sections:
- [Section name]: [what changed — new rule, removed rule, formula modified, etc.]

Unchanged sections:
- [Section name]

Key changes affecting architecture:
- [Change 1 — likely to affect ADRs]
- [Change 2]
```
```
## Change Summary: [GDD filename]
Date of revision: [today]

Changed sections:
- [Section name]: [what changed — new rule, removed rule, formula modified, etc.]

Unchanged sections:
- [Section name]

Key changes affecting architecture:
- [Change 1 — likely to affect ADRs]
- [Change 2]
```

---

## 4. Load Architecture Inputs
## 4. 加载架构输入

Read all ADRs in `docs/architecture/`:
读取 `docs/architecture/` 中的所有 ADR：
- For each ADR, read the full file
- Extract the "GDD Requirements Addressed" table
- Note which GDD documents and requirement IDs each ADR references
- 对每个 ADR，读取完整文件
- 提取 "GDD Requirements Addressed" 表
- 记录每个 ADR 引用了哪些 GDD 文档和需求 ID

Read `docs/architecture/architecture-traceability.md` if it exists.
如果存在，读取 `docs/architecture/architecture-traceability.md`。

Report: "Loaded [N] ADRs. [M] reference [gdd filename]."
报告："已加载 [N] 个 ADR。其中 [M] 个引用了 [gdd filename]。"

---

## 5. Impact Analysis
## 5. 影响分析

For each ADR that references the changed GDD:
对于每个引用了已变更 GDD 的 ADR：

Compare the ADR's "GDD Requirements Addressed" entries against the changed sections
of the GDD. For each referenced requirement:
将 ADR 的 "GDD Requirements Addressed" 条目与 GDD 中已变更的章节
进行比较。对于每个被引用的需求：

1. **Locate the requirement** in the current GDD — does it still exist?
2. **Compare**: What did the GDD say when the ADR was written vs. what it says now?
3. **Assess the ADR decision**: Is the architectural decision still valid?
1. **在当前 GDD 中定位该需求** — 它是否仍然存在？
2. **比较**：编写 ADR 时 GDD 是怎么说的，与现在所述有何不同？
3. **评估 ADR 决策**：该架构决策是否仍然有效？

Classify each affected ADR as one of:
将每个受影响的 ADR 分类为以下之一：

| Status | Meaning |
|--------|---------|
| ✅ **Still Valid** | The GDD change doesn't affect what this ADR decided |
| ⚠️ **Needs Review** | The GDD change may affect this ADR — human judgment needed |
| 🔴 **Likely Superseded** | The GDD change directly contradicts what this ADR assumed |
| 状态 | 含义 |
|--------|---------|
| ✅ **仍然有效** | GDD 变更不影响此 ADR 所做的决策 |
| ⚠️ **需要审查** | GDD 变更可能影响此 ADR — 需要人工判断 |
| 🔴 **可能已过时** | GDD 变更直接与此 ADR 所假设的内容矛盾 |

For each affected ADR, produce an impact entry:
对于每个受影响的 ADR，生成一个影响条目：

```
### ADR-NNNN: [title]
Status: [Still Valid / Needs Review / Likely Superseded]

What the ADR assumed about this GDD:
  "[relevant quote from the ADR's GDD Requirements Addressed section]"

What the GDD now says:
  "[relevant quote from the current GDD]"

Assessment:
  [Explanation of whether the ADR decision is still valid, and why]

Recommended action:
  [Keep as-is | Review and update | Mark Superseded and write new ADR]
```
```
### ADR-NNNN: [title]
Status: [Still Valid / Needs Review / Likely Superseded]

What the ADR assumed about this GDD:
  "[relevant quote from the ADR's GDD Requirements Addressed section]"

What the GDD now says:
  "[relevant quote from the current GDD]"

Assessment:
  [Explanation of whether the ADR decision is still valid, and why]

Recommended action:
  [Keep as-is | Review and update | Mark Superseded and write new ADR]
```

---

## 6. Present Impact Report
## 6. 呈现影响报告

Present the full impact report to the user before asking for any action. Format:
在要求任何操作之前，向用户呈现完整的影响报告。格式：

```
## Design Change Impact Report
GDD: [filename]
Date: [today]
Changes detected: [N sections changed]
ADRs referencing this GDD: [M]

### Not Affected
[ADRs referencing this GDD whose decisions remain valid]

### Needs Review ([count])
[ADRs that may need updating]

### Likely Superseded ([count])
[ADRs whose assumptions are now contradicted]
```
```
## Design Change Impact Report
GDD: [filename]
Date: [today]
Changes detected: [N sections changed]
ADRs referencing this GDD: [M]

### Not Affected
[ADRs referencing this GDD whose decisions remain valid]

### Needs Review ([count])
[ADRs that may need updating]

### Likely Superseded ([count])
[ADRs whose assumptions are now contradicted]
```

---

## 6b. Director Gate — Technical Impact Review
## 6b. 主管门禁 — 技术影响审查

**Review mode check** — apply before spawning TD-CHANGE-IMPACT:
**审查模式检查** — 在派生 TD-CHANGE-IMPACT 之前应用：
- `solo` → skip. Note: "TD-CHANGE-IMPACT skipped — Solo mode." Proceed to Phase 7.
- `lean` → skip. Note: "TD-CHANGE-IMPACT skipped — Lean mode." Proceed to Phase 7.
- `full` → spawn as normal.
- `solo` → 跳过。备注："TD-CHANGE-IMPACT 已跳过 — 单人模式。"继续至阶段 7。
- `lean` → 跳过。备注："TD-CHANGE-IMPACT 已跳过 — 精简模式。"继续至阶段 7。
- `full` → 正常派生。

Spawn `technical-director` via Task using gate **TD-CHANGE-IMPACT** (`.claude/docs/director-gates.md`).
通过 Task 使用门禁 **TD-CHANGE-IMPACT**（`.claude/docs/director-gates.md`）派生 `technical-director`。

Pass: the full Design Change Impact Report from Phase 6 (change summary, all affected ADRs with their Still Valid / Needs Review / Likely Superseded classifications, and recommended actions).
传递：阶段 6 的完整设计变更影响报告（变更摘要、所有受影响 ADR 及其 "仍然有效 / 需要审查 / 可能已过时" 分类，以及建议操作）。

The technical-director reviews whether:
- The impact classifications are correct (no ADRs under-classified)
- The recommended actions are architecturally sound
- Any cascading effects on other ADRs or systems were missed
technical-director 审查以下内容：
- 影响分类是否正确（没有 ADR 被低估）
- 建议操作在架构上是否合理
- 是否遗漏了对其他 ADR 或系统的任何级联影响

Apply the verdict:
- **APPROVE** → proceed to Phase 7 resolution workflow
- **CONCERNS** → surface the specific ADRs or recommendations flagged; use `AskUserQuestion` with options: `Revise the impact assessment` / `Accept with noted concerns` / `Discuss further`
- **REJECT** → do not proceed to resolution; re-analyze the impact before continuing
应用结论：
- **批准** → 继续至阶段 7 的解决工作流
- **有顾虑** → 突出显示被标记的特定 ADR 或建议；使用 `AskUserQuestion` 提供选项：`修订影响评估` / `接受并记录顾虑` / `进一步讨论`
- **拒绝** → 不继续解决；在继续之前重新分析影响

---

## 7. Resolution Workflow
## 7. 解决工作流

For each ADR marked "Needs Review" or "Likely Superseded", ask the user what to do:
对于每个标记为 "需要审查" 或 "可能已过时" 的 ADR，询问用户如何处理：

Ask for each ADR in turn:
> "ADR-NNNN ([title]) — [status]. What would you like to do?"
> Options:
> - "Mark Superseded (I'll write a new ADR)" — updates ADR status line to `Superseded by: [pending]`
> - "Update in place (minor revision)" — opens the ADR for editing; note what to revise
> - "Keep as-is (the change doesn't actually affect this decision)"
> - "Skip for now (revisit later)"
依次询问每个 ADR：
> "ADR-NNNN（[title]）— [status]。您想如何处理？"
> 选项：
> - "标记为已过时（我会写一个新的 ADR）" — 将 ADR 状态行更新为 `Superseded by: [pending]`
> - "就地更新（小幅修订）" — 打开 ADR 进行编辑；记录需要修订的内容
> - "保持原样（该变更实际上不影响此决策）"
> - "暂时跳过（稍后 revisit）"

For ADRs marked **Superseded**:
- Update the ADR's Status field: `Superseded by ADR-[next number] (pending — see change-impact-[date]-[system].md)`
- Ask: "May I update the status in [ADR filename]?"
对于标记为 **已过时** 的 ADR：
- 更新 ADR 的 Status 字段：`Superseded by ADR-[next number] (pending — see change-impact-[date]-[system].md)`
- 询问："我可以更新 [ADR filename] 中的状态吗？"

---

## 8. Update Traceability Index
## 8. 更新可追溯性索引

If `docs/architecture/architecture-traceability.md` exists:
- Add the changed GDD requirements to the "Superseded Requirements" table:
如果 `docs/architecture/architecture-traceability.md` 存在：
- 将已变更的 GDD 需求添加到 "Superseded Requirements" 表中：

```markdown
## Superseded Requirements
| Date | GDD | Requirement | Changed To | ADRs Affected | Resolution |
|------|-----|-------------|------------|---------------|------------|
| [date] | [gdd] | [old requirement text] | [new requirement text] | ADR-NNNN | [Superseded/Updated/Valid] |
```
```markdown
## Superseded Requirements
| Date | GDD | Requirement | Changed To | ADRs Affected | Resolution |
|------|-----|-------------|------------|---------------|------------|
| [date] | [gdd] | [old requirement text] | [new requirement text] | ADR-NNNN | [Superseded/Updated/Valid] |
```

Ask: "May I update the traceability index?"
询问："我可以更新可追溯性索引吗？"

---

## 9. Output Change Impact Document
## 9. 输出变更影响文档

Ask: "May I write the change impact report to `docs/architecture/change-impact-[date]-[system-slug].md`?"
询问："我可以将变更影响报告写入 `docs/architecture/change-impact-[date]-[system-slug].md` 吗？"

The document contains:
- The change summary from step 3
- The full impact analysis from step 5
- Resolution decisions made in step 7
- List of ADRs that need to be written or updated
该文档包含：
- 步骤 3 的变更摘要
- 步骤 5 的完整影响分析
- 步骤 7 所做的解决决策
- 需要编写或更新的 ADR 列表

If user approved: Verdict: **COMPLETE** — change impact report saved.
If user declined: Verdict: **BLOCKED** — user declined write.
如果用户同意：结论：**已完成** — 变更影响报告已保存。
如果用户拒绝：结论：**已阻塞** — 用户拒绝写入。

---

## 10. Follow-Up Actions
## 10. 后续行动

Based on the resolution decisions, suggest:
基于解决决策，建议：

- **ADRs marked Superseded**: "Run `/architecture-decision [title]` to write the
  replacement ADR. Then re-run `/propagate-design-change` to verify coverage."
- **ADRs to update in place**: List the specific fields to update in each ADR
- **If many ADRs affected**: "Run `/architecture-review` after all ADRs are updated
  to verify the full traceability matrix is still coherent."
- **标记为已过时的 ADR**："运行 `/architecture-decision [title]` 编写
  替代 ADR。然后重新运行 `/propagate-design-change` 验证覆盖范围。"
- **就地更新的 ADR**：列出每个 ADR 中需要更新的具体字段
- **如果许多 ADR 受影响**："在所有 ADR 更新后运行 `/architecture-review`
  以验证完整的可追溯性矩阵仍然连贯。"

---

## Collaborative Protocol
## 协作协议

1. **Read silently** — compute the full impact before presenting anything
2. **Show the full report first** — let the user see the scope before asking for action
3. **Ask per-ADR** — don't batch decisions; each affected ADR may need different treatment
4. **Ask before writing** — always confirm before modifying any file
5. **Non-destructive** — never delete ADR content; only add "Superseded by" notes
1. **静默读取** — 在呈现任何内容之前计算完整影响
2. **先展示完整报告** — 让用户在要求操作之前看到范围
3. **逐个 ADR 询问** — 不要批量决策；每个受影响的 ADR 可能需要不同的处理方式
4. **写入前询问** — 修改任何文件之前始终确认
5. **非破坏性** — 永不删除 ADR 内容；仅添加 "Superseded by" 备注
