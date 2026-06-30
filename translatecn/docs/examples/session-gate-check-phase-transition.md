# Example Session: /gate-check Phase Transition — Systems Design → Technical Setup
# 示例会话：/gate-check 阶段过渡 —— 系统设计 → 技术设置

**Context:** Solo indie dev, end of week 5. The Systems Design phase feels complete — 6 MVP GDDs written, cross-review done, engine set up. User wants to formally advance to Technical Setup.
**上下文：** 独立开发者，第 5 周末。系统设计阶段感觉已完成——6 个 MVP GDD 已编写，交叉评审已完成，引擎已设置。用户希望正式推进到技术设置。

**Starting State:**
**起始状态：**
- `production/stage.txt` — currently `systems-design`
- `production/stage.txt` —— 当前为 `systems-design`
- `design/gdd/systems-index.md` — 6 MVP systems listed, all marked `Status: GDD Complete`
- `design/gdd/systems-index.md` —— 列出 6 个 MVP 系统，全部标记为 `Status: GDD Complete`
- `design/gdd/` — 6 GDD files present
- `design/gdd/` —— 存在 6 个 GDD 文件
- `design/gdd/gdd-cross-review-2026-03-10.md` — cross-review report, verdict: CONCERNS (1 low-priority note)
- `design/gdd/gdd-cross-review-2026-03-10.md` —— 交叉评审报告，裁决：CONCERNS（1 个低优先级注释）
- Engine configured (Godot 4.6, `/setup-engine` complete)
- 引擎已配置（Godot 4.6，`/setup-engine` 已完成）

**Session Duration:** ~20 minutes
**会话时长：** 约 20 分钟
**Turns:** 7
**回合数：** 7
**Outcome:** Gate passed, `stage.txt` updated to `technical-setup`, clear list of what comes next
**结果：** 关卡通过，`stage.txt` 更新为 `technical-setup`，清晰的下一步列表

---

## Turn 1: User Initiates Gate Check
## 回合 1：用户发起关卡检查

**User:**
**用户：**
> /gate-check

## Turn 2: Agent Detects Current Phase and Runs the Check
## 回合 2：智能体检测当前阶段并运行检查

**Agent (via /gate-check):**
**智能体（通过 /gate-check）：**
> Reading `production/stage.txt` — current phase: **systems-design**
> 阅读 `production/stage.txt` —— 当前阶段：**systems-design**
>
> Running gate: **Systems Design → Technical Setup**
> 运行关卡：**系统设计 → 技术设置**
>
> ---
> **Required Artifacts Checklist:**
> **必需产物清单：**
>
> | Artifact | Requirement | Status |
> | 产物 | 要求 | 状态 |
> |----------|-------------|--------|
> | `design/gdd/systems-index.md` | Exists, all MVP systems listed | ✅ |
> | `design/gdd/systems-index.md` | 存在，列出所有 MVP 系统 | ✅ |
> | Per-system GDDs | All MVP systems have a GDD | ✅ (6/6) |
> | 单系统 GDD | 所有 MVP 系统都有 GDD | ✅ (6/6) |
> | `/design-review` per GDD | Each GDD reviewed | ✅ (6/6 review comments found) |
> | 每个 GDD 的 `/design-review` | 每个 GDD 已评审 | ✅ (找到 6/6 评审注释) |
> | Cross-GDD review report | `design/gdd/gdd-cross-review-*.md` exists | ✅ |
> | 跨 GDD 评审报告 | `design/gdd/gdd-cross-review-*.md` 存在 | ✅ |
> | Cross-review verdict | PASS or CONCERNS (not FAIL) | ✅ CONCERNS |
> | 交叉评审裁决 | PASS 或 CONCERNS（非 FAIL） | ✅ CONCERNS |
> | Engine configured | `production/stage.txt` or CLAUDE.md engine entry | ✅ Godot 4.6 |
> | 引擎已配置 | `production/stage.txt` 或 CLAUDE.md 引擎条目 | ✅ Godot 4.6 |
>
> ---
> **Content Scan:**
> **内容扫描：**
>
> Checking GDD completeness (8 required sections each)...
> 检查 GDD 完整性（每个 8 个必需章节）...
>
> | GDD | Missing Sections |
> | GDD | 缺失章节 |
> |-----|-----------------|
> | movement-system.md | None ✅ |
> | movement-system.md | 无 ✅ |
> | stamina-system.md | None ✅ |
> | stamina-system.md | 无 ✅ |
> | combat-system.md | None ✅ |
> | combat-system.md | 无 ✅ |
> | inventory-system.md | None ✅ |
> | inventory-system.md | 无 ✅ |
> | crafting-system.md | None ✅ |
> | crafting-system.md | 无 ✅ |
> | exploration-system.md | None ✅ |
> | exploration-system.md | 无 ✅ |
>
> ---
> **Cross-Review Concerns (from `gdd-cross-review-2026-03-10.md`):**
> **交叉评审关注点（来自 `gdd-cross-review-2026-03-10.md`）：**
>
> 1. ⚠️ LOW — Crafting and inventory both define "item stack size" independently with different defaults (crafting: 99, inventory: 64). One system should be the authority; the other should reference it. Not blocking — can resolve in ADR.
> 1. ⚠️ 低 —— 制作和库存都独立定义了"物品堆叠大小"且默认值不同（制作：99，库存：64）。一个系统应该是权威；另一个应该引用它。非阻塞——可在 ADR 中解决。
>
> ---
> **Verdict: PASS**
> **裁决：通过**
>
> All required artifacts present. Cross-review concern is LOW severity and can be resolved during Technical Setup via an ADR. This is a normal finding at this stage.
> 所有必需产物存在。交叉评审关注点为低严重性，可在技术设置期间通过 ADR 解决。这是此阶段的正常发现。
>
> **May I advance `production/stage.txt` to `technical-setup`?**
> **我可以将 `production/stage.txt` 推进到 `technical-setup` 吗？**

## Turn 3: User Confirms
## 回合 3：用户确认

**User:**
**用户：**
> Yes, advance it.
> 是的，推进它。

## Turn 4: Agent Updates Stage and Presents Next Steps
## 回合 4：智能体更新阶段并呈现下一步

**Agent:**
**智能体：**
> Updated `production/stage.txt` → `technical-setup`
> 已更新 `production/stage.txt` → `technical-setup`
>
> ---
> **You are now in: Technical Setup**
> **你现在处于：技术设置**
>
> This phase establishes the technical foundation before production coding starts. Your goal is a complete, approved architecture so that every story written in Pre-Production has clear, unambiguous technical guidance.
> 此阶段在生产编码开始之前建立技术基础。你的目标是完整、已批准的架构，以便在生产前阶段编写的每个故事都有清晰、明确的技木指引。
>
> **Required steps this phase (in order):**
> **此阶段的必需步骤（按顺序）：**
>
> 1. **`/create-architecture`** — Author the master architecture document. Covers system decomposition, data flow, layer boundaries, and module responsibilities.
> 1. **`/create-architecture`** —— 编写主架构文档。涵盖系统分解、数据流、层边界和模块职责。
>
> 2. **`/architecture-decision` (one per significant technical choice)** — At minimum:
> 2. **`/architecture-decision`（每个重要技术选择一个）** —— 至少：
>    - Rendering approach (2D camera, viewport, etc.)
>    - 渲染方法（2D 相机、视口等）
>    - Save/load strategy
>    - 保存/加载策略
>    - Audio bus architecture
>    - 音频总线架构
>    - The "item stack size authority" issue from cross-review (crafting vs inventory)
>    - 来自交叉评审的"物品堆叠大小权威"问题（制作 vs 库存）
>
> 3. **`/architecture-review`** — Validates all ADRs for completeness, dependency ordering, and GDD traceability. Produces the TR-registry.
> 3. **`/architecture-review`** —— 验证所有 ADR 的完整性、依赖排序和 GDD 可追溯性。生成 TR-registry。
>
> 4. **`/create-control-manifest`** — Converts Accepted ADRs into a flat programmer rules sheet. This is what implementation agents will consult during coding.
> 4. **`/create-control-manifest`** —— 将已接受 ADR 转换为扁平的程序员规则表。这是实现智能体在编码期间将查阅的内容。
>
> 5. **`/gate-check`** — Technical Setup → Pre-Production gate. Requires: architecture doc, all ADRs Accepted, control manifest, and dependency ordering resolved.
> 5. **`/gate-check`** —— 技术设置 → 生产前阶段关卡。需要：架构文档、所有 ADR 已接受、控制清单和依赖排序已解决。
>
> **Optional but recommended:**
> **可选但推荐：**
> - `/tech-debt` scan if you have any prototype code you plan to carry forward
> - 如果你计划保留任何原型代码，扫描 `/tech-debt`
> - `/setup-engine` re-run if you haven't yet fetched Godot 4.6 API snapshots
> - 如果你尚未获取 Godot 4.6 API 快照，重新运行 `/setup-engine`
>
> **Tip:** Start with `/architecture-decision` for the item stack size conflict. It's a quick win that closes an open question before it creates ambiguity in two ADRs later.
> **提示：** 从物品堆叠大小冲突的 `/architecture-decision` 开始。这是一个快速胜利，在它稍后在两个 ADR 中造成歧义之前关闭一个开放问题。

## Turn 5: User Asks About the Cross-Review Concern
## 回合 5：用户询问交叉评审关注点

**User:**
**用户：**
> What do I actually write in the ADR for the stack size conflict?
> 我应该在堆叠大小冲突的 ADR 中实际写什么？

## Turn 6: Agent Advises
## 回合 6：智能体建议

**Agent:**
**智能体：**
> The ADR decision is: **which system owns the canonical `max_stack_size` value?**
> ADR 决策是：**哪个系统拥有规范的 `max_stack_size` 值？**
>
> Two options to consider:
> 两个选项供考虑：
>
> **Option A: Inventory owns it**
> **选项 A：库存拥有它**
> - `ItemData` resource (owned by inventory) gets `max_stack_size: int = 64`
> - `ItemData` 资源（由库存拥有）获得 `max_stack_size: int = 64`
> - Crafting system reads from `ItemData`, never sets its own value
> - 制作系统从 `ItemData` 读取，从不设置自己的值
> - Pros: inventory is the source of truth for all item properties
> - 优点：库存是所有物品属性的真相来源
> - Cons: crafting team needs to know to look at inventory data
> - 缺点：制作团队需要知道去查看库存数据
>
> **Option B: Item data resource owns it (shared resource)**
> **选项 B：物品数据资源拥有它（共享资源）**
> - `ItemData.max_stack_size` is the authority for both systems
> - `ItemData.max_stack_size` 是两个系统的权威
> - Both crafting and inventory import from `ItemData`
> - 制作和库存都从 `ItemData` 导入
> - Pros: single source, no coupling between crafting and inventory modules
> - 优点：单一来源，制作和库存模块之间无耦合
> - Cons: one more thing in the shared data layer
> - 缺点：共享数据层中又多了一项
>
> Most studios go with B (shared resource) once they have >2 systems touching item data. But either is valid for a solo project.
> 大多数工作室一旦有 >2 个系统触及物品数据，就会选择 B（共享资源）。但对于独立项目，两者都有效。
>
> Run `/architecture-decision` and it will guide you through the ADR format, including the Dependencies section that links this ADR back to the two GDDs.
> 运行 `/architecture-decision`，它将引导你完成 ADR 格式，包括将此 ADR 链接回两个 GDD 的 Dependencies 章节。

## Turn 7: User Wraps Up
## 回合 7：用户收尾

**User:**
**用户：**
> Got it. I'll start with /architecture-decision for this.
> 明白了。我将从此项的 /architecture-decision 开始。

**Agent:**
**智能体：**
> Good starting point. The ADR will document the decision, the reasoning, and the GDD requirements it addresses — so when you write code months from now, the rationale is preserved.
> 好的起点。ADR 将记录决策、理由和它所解决的 GDD 需求——所以当你几个月后编写代码时，理由得以保留。
>
> Your phase gate is logged. Run `/help` at any time during Technical Setup to see your current progress against the required checklist.
> 你的阶段关卡已记录。在技术设置期间随时运行 `/help` 以查看你当前对照必需清单的进度。

---

## What This Example Demonstrates
## 本示例展示了什么

- **Gate check is automated, not manual**: agent reads artifacts and checks them — user doesn't fill out a form
- **关卡检查是自动的，而非手动的**：智能体读取产物并检查它们——用户不填写表单
- **CONCERNS ≠ FAIL**: the cross-review concern is LOW severity and passes the gate. A FAIL would require resolution before advancing.
- **CONCERNS 不等于 FAIL**：交叉评审关注点为低严重性并通过关卡。FAIL 需要在推进之前解决。
- **Stage.txt is the authority**: the status line, `/help`, and all skills read from `production/stage.txt` — updating it here changes what every subsequent skill sees
- **stage.txt 是权威**：状态行、`/help` 和所有技能都从 `production/stage.txt` 读取——在此更新它会改变每个后续技能看到的内容
- **Next steps are phase-specific**: the agent doesn't give a generic "good luck" — it gives the ordered checklist for Technical Setup specifically
- **下一步是特定于阶段的**：智能体不给通用的"祝你好运"——它给出专门针对技术设置的有序清单
- **Gate check surfaces carry-forward work**: the stack size conflict was a cross-review note; the gate check ensures it becomes a concrete ADR rather than getting lost
- **关卡检查呈现遗留工作**：堆叠大小冲突是交叉评审注释；关卡检查确保它成为具体的 ADR 而非丢失
- **One advance per gate**: the user confirmed advancement explicitly. The gate doesn't auto-advance; human confirmation is required.
- **每个关卡一次推进**：用户明确确认推进。关卡不会自动推进；需要人工确认。
