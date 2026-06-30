---
name: architecture-review
description: "Validates completeness and consistency of the project architecture against all GDDs. Builds a traceability matrix mapping every GDD technical requirement to ADRs, identifies coverage gaps, detects cross-ADR conflicts, verifies engine compatibility consistency across all decisions, and produces a PASS/CONCERNS/FAIL verdict. The architecture equivalent of /design-review."
argument-hint: "[focus: full | coverage | consistency | engine | single-gdd path/to/gdd.md]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
agent: technical-director
model: opus
---
---
name: architecture-review
description: "对照所有 GDD 验证项目架构的完整性与一致性。构建可追溯性矩阵，将每个 GDD 技术需求映射到 ADR，识别覆盖缺口，检测跨 ADR 冲突，验证所有决策间的引擎兼容性一致性，并生成 PASS/CONCERNS/FAIL 判定。架构层面的 /design-review 等价物。"
argument-hint: "[focus: full | coverage | consistency | engine | single-gdd path/to/gdd.md]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Write, Task, AskUserQuestion
agent: technical-director
model: opus
---

# Architecture Review

The architecture review validates that the complete body of architectural decisions
covers all game design requirements, is internally consistent, and correctly targets
the project's pinned engine version. It is the quality gate between Technical Setup
and Pre-Production.

**Argument modes:**
- **No argument / `full`**: Full review — all phases
- **`coverage`**: Traceability only — which GDD requirements have no ADR
- **`consistency`**: Cross-ADR conflict detection only
- **`engine`**: Engine compatibility audit only
- **`single-gdd [path]`**: Review architecture coverage for one specific GDD
- **`rtm`**: Requirements Traceability Matrix — extends the standard matrix
  to include story file paths and test file paths; outputs
  `docs/architecture/requirements-traceability.md` with the full
  GDD requirement → ADR → Story → Test chain. Use in Production phase when
  stories and tests exist.

# 架构评审

架构评审验证完整的架构决策集合是否
覆盖所有游戏设计需求、内部一致、并正确定位到
项目锁定的引擎版本。它是技术设置与预生产之间的质量门禁。

**参数模式：**
- **无参数 / `full`**：完整评审 —— 全部阶段
- **`coverage`**：仅可追溯性 —— 哪些 GDD 需求没有 ADR
- **`consistency`**：仅跨 ADR 冲突检测
- **`engine`**：仅引擎兼容性审计
- **`single-gdd [path]`**：评审单个特定 GDD 的架构覆盖
- **`rtm`**：需求可追溯性矩阵 —— 扩展标准矩阵以
  包含故事文件路径和测试文件路径；输出
  `docs/architecture/requirements-traceability.md`，包含完整的
  GDD 需求 → ADR → Story → Test 链。在生产阶段存在
  故事和测试时使用。

---

## Phase 1: Load Everything

### Phase 1a — L0: Summary Scan (fast, low tokens)

Before reading any full document, use Grep to extract `## Summary` sections
from all GDDs and ADRs:

```
Grep pattern="## Summary" glob="design/gdd/*.md" output_mode="content" -A 4
Grep pattern="## Summary" glob="docs/architecture/adr-*.md" output_mode="content" -A 3
```

For `single-gdd [path]` mode: use the target GDD's summary to identify which
ADRs reference the same system (Grep ADRs for the system name), then full-read
only those ADRs. Skip full-reading unrelated GDDs entirely.

For `engine` mode: only full-read ADRs — GDDs are not needed for engine checks.

For `coverage` or `full` mode: proceed to full-read everything below.

### Phase 1b — L1/L2: Full Document Load

Read all inputs appropriate to the mode:

### Design Documents
- All in-scope GDDs in `design/gdd/` — read every file completely
- `design/gdd/systems-index.md` — the authoritative list of systems

### Architecture Documents
- All in-scope ADRs in `docs/architecture/` — read every file completely
- `docs/architecture/architecture.md` if it exists

### Engine Reference
- `docs/engine-reference/[engine]/VERSION.md`
- `docs/engine-reference/[engine]/breaking-changes.md`
- `docs/engine-reference/[engine]/deprecated-apis.md`
- All files in `docs/engine-reference/[engine]/modules/`

### Project Standards
- `.claude/docs/technical-preferences.md`

Report a count: "Loaded [N] GDDs, [M] ADRs, engine: [name + version]."

**Also read `docs/consistency-failures.md`** if it exists. Extract entries with
Domain matching the systems under review (Architecture, Engine, or any GDD domain
being covered). Surface recurring patterns as a "Known conflict-prone areas" note
at the top of the Phase 4 conflict detection output.

## 第 1 阶段：加载全部内容

### 第 1a 阶段 —— L0：摘要扫描（快速，低 token）

在读取任何完整文档之前，使用 Grep 提取所有 GDD 和 ADR 中的 `## Summary` 章节：

```
Grep pattern="## Summary" glob="design/gdd/*.md" output_mode="content" -A 4
Grep pattern="## Summary" glob="docs/architecture/adr-*.md" output_mode="content" -A 3
```

对于 `single-gdd [path]` 模式：使用目标 GDD 的摘要来识别哪些
ADR 引用了同一系统（在 ADR 中 Grep 系统名称），然后仅完整读取
这些 ADR。完全跳过完整读取无关的 GDD。

对于 `engine` 模式：仅完整读取 ADR —— 引擎检查不需要 GDD。

对于 `coverage` 或 `full` 模式：继续完整读取下面的所有内容。

### 第 1b 阶段 —— L1/L2：完整文档加载

读取适合该模式的所有输入：

### 设计文档
- `design/gdd/` 中所有在范围内的 GDD —— 完整读取每个文件
- `design/gdd/systems-index.md` —— 权威的系统清单

### 架构文档
- `docs/architecture/` 中所有在范围内的 ADR —— 完整读取每个文件
- `docs/architecture/architecture.md`（如存在）

### 引擎参考
- `docs/engine-reference/[engine]/VERSION.md`
- `docs/engine-reference/[engine]/breaking-changes.md`
- `docs/engine-reference/[engine]/deprecated-apis.md`
- `docs/engine-reference/[engine]/modules/` 中的所有文件

### 项目标准
- `.claude/docs/technical-preferences.md`

报告计数："Loaded [N] GDDs, [M] ADRs, engine: [name + version]."

**如 `docs/consistency-failures.md` 存在也读取它**。提取 Domain 与
被评审系统匹配的条目（Architecture、Engine，或任何被覆盖的 GDD 领域）。将反复出现的模式作为"已知易冲突区域"备注
置于第 4 阶段冲突检测输出的顶部。

---

## Phase 2: Extract Technical Requirements from Every GDD

### Pre-load the TR Registry

Before extracting any requirements, read `docs/architecture/tr-registry.yaml`
if it exists. Index existing entries by `id` and by normalized `requirement`
text (lowercase, trimmed). This prevents ID renumbering across review runs.

For each requirement you extract, the matching rule is:
1. **Exact/near match** to an existing registry entry for the same system →
   reuse that entry's TR-ID unchanged. Update the `requirement` text in the
   registry only if the GDD wording changed (same intent, clearer phrasing) —
   add a `revised: [date]` field.
2. **No match** → assign a new ID: next available `TR-[system]-NNN` for that
   system, starting from the highest existing sequence + 1.
3. **Ambiguous** (partial match, intent unclear) → ask the user:
   > "Does '[new requirement text]' refer to the same requirement as
   > `TR-[system]-NNN: [existing text]'`, or is it a new requirement?"
   User answers: "Same requirement" (reuse ID) or "New requirement" (new ID).

For any requirement with `status: deprecated` in the registry — skip it.
It was removed from the GDD intentionally.

For each GDD, read it and extract all **technical requirements** — things the
architecture must provide for the system to work. A technical requirement is any
statement that implies a specific architectural decision.

Categories to extract:

| Category | Example |
|----------|---------|
| **Data structures** | "Each entity has health, max health, status effects" → needs a component/data schema |
| **Performance constraints** | "Collision detection must run at 60fps with 200 entities" → physics budget ADR |
| **Engine capability** | "Inverse kinematics for character animation" → IK system ADR |
| **Cross-system communication** | "Damage system notifies UI and audio simultaneously" → event/signal architecture ADR |
| **State persistence** | "Player progress persists between sessions" → save system ADR |
| **Threading/timing** | "AI decisions happen off the main thread" → concurrency ADR |
| **Platform requirements** | "Supports keyboard, gamepad, touch" → input system ADR |

For each GDD, produce a structured list:

```
GDD: [filename]
System: [system name]
Technical Requirements:
  TR-[GDD]-001: [requirement text] → Domain: [Physics/Rendering/etc]
  TR-[GDD]-002: [requirement text] → Domain: [...]
```

This becomes the **requirements baseline** — the complete set of what the
architecture must cover.

## 第 2 阶段：从每个 GDD 中提取技术需求

### 预加载 TR 注册表

在提取任何需求之前，如 `docs/architecture/tr-registry.yaml`
存在则读取它。按 `id` 和规范化后的 `requirement`
文本（小写、去空格）索引现有条目。这能防止评审运行间 ID 重新编号。

对于你提取的每个需求，匹配规则为：
1. **与同一系统现有注册表条目精确/近似匹配** →
   不变地复用该条目的 TR-ID。仅当 GDD 措辞变化（意图相同、表述更清晰）时
   更新注册表中的 `requirement` 文本 ——
   添加 `revised: [date]` 字段。
2. **无匹配** → 分配新 ID：该系统下一个可用 `TR-[system]-NNN`，
   从现有最高序号 + 1 开始。
3. **模糊**（部分匹配，意图不清）→ 询问用户：
   > "'[新需求文本]' 是否指与
   > `TR-[system]-NNN: [现有文本]` 相同的需求，还是新需求？"
   用户回答："Same requirement"（复用 ID）或 "New requirement"（新 ID）。

对于注册表中 `status: deprecated` 的任何需求 —— 跳过它。
它是被有意从 GDD 中移除的。

对于每个 GDD，读取它并提取所有 **技术需求** —— 架构必须
为系统工作提供的东西。技术需求是任何
暗示特定架构决策的陈述。

要提取的类别：

| 类别 | 示例 |
|----------|---------|
| **数据结构** | "每个实体有生命值、最大生命值、状态效果" → 需要组件/数据 schema |
| **性能约束** | "碰撞检测必须在 200 个实体下以 60fps 运行" → 物理预算 ADR |
| **引擎能力** | "角色动画使用逆运动学" → IK 系统 ADR |
| **跨系统通信** | "伤害系统同时通知 UI 和音频" → 事件/信号架构 ADR |
| **状态持久化** | "玩家进度跨会话保存" → 存档系统 ADR |
| **线程/时序** | "AI 决策在主线程外发生" → 并发 ADR |
| **平台需求** | "支持键盘、手柄、触控" → 输入系统 ADR |

对于每个 GDD，生成结构化列表：

```
GDD: [filename]
System: [system name]
Technical Requirements:
  TR-[GDD]-001: [requirement text] → Domain: [Physics/Rendering/etc]
  TR-[GDD]-002: [requirement text] → Domain: [...]
```

这成为 **需求基线** —— 架构必须覆盖的完整集合。

---

## Phase 3: Build the Traceability Matrix

For each technical requirement extracted in Phase 2, search the ADRs:

1. Read every ADR's "GDD Requirements Addressed" section
2. Check if it explicitly references the requirement or its GDD
3. Check if the ADR's decision text implicitly covers the requirement
4. Mark coverage status:

| Status | Meaning |
|--------|---------|
| ✅ **Covered** | An ADR explicitly addresses this requirement |
| ⚠️ **Partial** | An ADR partially covers this, or coverage is ambiguous |
| ❌ **Gap** | No ADR addresses this requirement |

Build the full matrix:

```
## Traceability Matrix

| Requirement ID | GDD | System | Requirement | ADR Coverage | Status |
|---------------|-----|--------|-------------|--------------|--------|
| TR-combat-001 | combat.md | Combat | Hitbox detection < 1 frame | ADR-0003 | ✅ |
| TR-combat-002 | combat.md | Combat | Combo window timing | — | ❌ GAP |
| TR-inventory-001 | inventory.md | Inventory | Persistent item storage | ADR-0005 | ✅ |
```

Count the totals: X covered, Y partial, Z gaps.

## 第 3 阶段：构建可追溯性矩阵

对于第 2 阶段提取的每个技术需求，搜索 ADR：

1. 读取每个 ADR 的 "GDD Requirements Addressed" 章节
2. 检查它是否显式引用该需求或其 GDD
3. 检查 ADR 的决策文本是否隐式覆盖该需求
4. 标记覆盖状态：

| 状态 | 含义 |
|--------|---------|
| ✅ **Covered（已覆盖）** | 某个 ADR 显式处理了此需求 |
| ⚠️ **Partial（部分）** | 某个 ADR 部分覆盖，或覆盖不明确 |
| ❌ **Gap（缺口）** | 没有 ADR 处理此需求 |

构建完整矩阵：

```
## Traceability Matrix

| Requirement ID | GDD | System | Requirement | ADR Coverage | Status |
|---------------|-----|--------|-------------|--------------|--------|
| TR-combat-001 | combat.md | Combat | Hitbox detection < 1 frame | ADR-0003 | ✅ |
| TR-combat-002 | combat.md | Combat | Combo window timing | — | ❌ GAP |
| TR-inventory-001 | inventory.md | Inventory | Persistent item storage | ADR-0005 | ✅ |
```

统计总数：X 个已覆盖，Y 个部分，Z 个缺口。

---

## Phase 3b: Story and Test Linkage (RTM mode only)

*Skip this phase unless the argument is `rtm` or `full` with stories present.*

This phase extends the Phase 3 matrix to include the story that implements
each requirement and the test that verifies it — producing the full
Requirements Traceability Matrix (RTM).

### Step 3b-1 — Load stories

Glob `production/epics/**/*.md` (excluding EPIC.md index files). For each
story file:
- Extract `TR-ID` from the story's Context section
- Extract story file path, title, Status
- Extract `## Test Evidence` section — the stated test file path

### Step 3b-2 — Load test files

Glob `tests/unit/**/*_test.*` and `tests/integration/**/*_test.*`.
Build an index: system → [test file paths].

For each test file path from Step 3b-1, confirm via Glob whether the file
actually exists. Note MISSING if the stated path does not exist.

### Step 3b-3 — Build the extended RTM

For each TR-ID in the Phase 3 matrix, add:
- **Story**: the story file path(s) that reference this TR-ID (may be multiple)
- **Test File**: the test file path stated in the story's Test Evidence section
- **Test Status**: COVERED (test file exists) / MISSING (path stated but not
  found) / NONE (no test path stated, story type may be Visual/Feel/UI) /
  NO STORY (requirement has no story yet — pre-production gap)

Extended matrix format:

```
## Requirements Traceability Matrix (RTM)

| TR-ID | GDD | Requirement | ADR | Story | Test File | Test Status |
|-------|-----|-------------|-----|-------|-----------|-------------|
| TR-combat-001 | combat.md | Hitbox < 1 frame | ADR-0003 | story-001-hitbox.md | tests/unit/combat/hitbox_test.gd | COVERED |
| TR-combat-002 | combat.md | Combo window | — | story-002-combo.md | — | NONE (Visual/Feel) |
| TR-inventory-001 | inventory.md | Persistent storage | ADR-0005 | — | — | NO STORY |
```

RTM coverage summary:
- COVERED: [N] — requirements with ADR + story + passing test
- MISSING test: [N] — story exists but test file not found
- NO STORY: [N] — requirements with ADR but no story yet
- NO ADR: [N] — requirements without architectural coverage (from Phase 3 gaps)
- Full chain complete (COVERED): [N/total] ([%])

## 第 3b 阶段：故事与测试关联（仅 RTM 模式）

*除非参数为 `rtm` 或 `full` 且故事存在，否则跳过此阶段。*

此阶段扩展第 3 阶段矩阵以包含实现每个需求的故事和验证它的测试 —— 生成完整的
需求可追溯性矩阵（RTM）。

### 步骤 3b-1 —— 加载故事

Glob `production/epics/**/*.md`（排除 EPIC.md 索引文件）。对于每个
故事文件：
- 从故事的 Context 章节提取 `TR-ID`
- 提取故事文件路径、标题、Status
- 提取 `## Test Evidence` 章节 —— 所述测试文件路径

### 步骤 3b-2 —— 加载测试文件

Glob `tests/unit/**/*_test.*` 和 `tests/integration/**/*_test.*`。
构建索引：system → [test file paths]。

对于步骤 3b-1 中的每个测试文件路径，通过 Glob 确认文件是否
确实存在。如所述路径不存在则记为 MISSING。

### 步骤 3b-3 —— 构建扩展 RTM

对于第 3 阶段矩阵中的每个 TR-ID，添加：
- **Story**：引用此 TR-ID 的故事文件路径（可能多个）
- **Test File**：故事的 Test Evidence 章节中所述的测试文件路径
- **Test Status**：COVERED（测试文件存在）/ MISSING（路径已述但未找到）
  / NONE（无测试路径，故事类型可能是 Visual/Feel/UI）/
  NO STORY（需求尚无故事 —— 预生产缺口）

扩展矩阵格式：

```
## Requirements Traceability Matrix (RTM)

| TR-ID | GDD | Requirement | ADR | Story | Test File | Test Status |
|-------|-----|-------------|-----|-------|-----------|-------------|
| TR-combat-001 | combat.md | Hitbox < 1 frame | ADR-0003 | story-001-hitbox.md | tests/unit/combat/hitbox_test.gd | COVERED |
| TR-combat-002 | combat.md | Combo window | — | story-002-combo.md | — | NONE (Visual/Feel) |
| TR-inventory-001 | inventory.md | Persistent storage | ADR-0005 | — | — | NO STORY |
```

RTM 覆盖总结：
- COVERED：[N] —— 有 ADR + 故事 + 通过测试的需求
- MISSING test：[N] —— 故事存在但未找到测试文件
- NO STORY：[N] —— 有 ADR 但尚无故事的需求
- NO ADR：[N] —— 无架构覆盖的需求（来自第 3 阶段缺口）
- 完整链条完成（COVERED）：[N/total] ([%])

---

## Phase 4: Cross-ADR Conflict Detection

Compare every ADR against every other ADR to detect contradictions. A conflict
exists when:

- **Data ownership conflict**: Two ADRs claim exclusive ownership of the same data
- **Integration contract conflict**: ADR-A assumes System X has interface Y, but
  ADR-B defines System X with a different interface
- **Performance budget conflict**: ADR-A allocates N ms to physics, ADR-B allocates
  N ms to AI, together they exceed the total frame budget
- **Dependency cycle**: ADR-A says System X initialises before Y; ADR-B says Y
  initialises before X
- **Architecture pattern conflict**: ADR-A uses event-driven communication for a
  subsystem; ADR-B uses direct function calls to the same subsystem
- **State management conflict**: Two ADRs define authority over the same game state
  (e.g. both Combat ADR and Character ADR claim to own the health value)

For each conflict found:

```
## Conflict: [ADR-NNNN] vs [ADR-MMMM]
Type: [Data ownership / Integration / Performance / Dependency / Pattern / State]
ADR-NNNN claims: [...]
ADR-MMMM claims: [...]
Impact: [What breaks if both are implemented as written]
Resolution options:
  1. [Option A]
  2. [Option B]
```

### ADR Dependency Ordering

After conflict detection, analyse the dependency graph across all ADRs:

1. **Collect all `Depends On` fields** from every ADR's "ADR Dependencies" section
2. **Topological sort**: Determine the correct implementation order — ADRs with no
   dependencies come first (Foundation), ADRs that depend on those come next, etc.
3. **Flag unresolved dependencies**: If ADR-A's "Depends On" field references an ADR
   that is still `Proposed` or does not exist, flag it:
   ```
   ⚠️  ADR-0005 depends on ADR-0002 — but ADR-0002 is still Proposed.
       ADR-0005 cannot be safely implemented until ADR-0002 is Accepted.
   ```
4. **Cycle detection**: If ADR-A depends on ADR-B and ADR-B depends on ADR-A (directly
   or transitively), flag it as a `DEPENDENCY CYCLE`:
   ```
   🔴 DEPENDENCY CYCLE: ADR-0003 → ADR-0006 → ADR-0003
      This cycle must be broken before either can be implemented.
   ```
5. **Output recommended implementation order**:
   ```
   ### Recommended ADR Implementation Order (topologically sorted)
   Foundation (no dependencies):
     1. ADR-0001: [title]
     2. ADR-0003: [title]
   Depends on Foundation:
     3. ADR-0002: [title] (requires ADR-0001)
     4. ADR-0005: [title] (requires ADR-0003)
   Feature layer:
     5. ADR-0004: [title] (requires ADR-0002, ADR-0005)
   ```

## 第 4 阶段：跨 ADR 冲突检测

将每个 ADR 与其他每个 ADR 对比以检测矛盾。当出现以下情况时存在冲突：

- **数据所有权冲突**：两个 ADR 声称对同一数据的独占所有权
- **集成契约冲突**：ADR-A 假设系统 X 有接口 Y，但
  ADR-B 用不同接口定义系统 X
- **性能预算冲突**：ADR-A 给物理分配 N ms，ADR-B 给 AI 分配
  N ms，加在一起超过总帧预算
- **依赖循环**：ADR-A 说系统 X 在 Y 之前初始化；ADR-B 说 Y
  在 X 之前初始化
- **架构模式冲突**：ADR-A 对某子系统使用事件驱动通信；
  ADR-B 对同一子系统使用直接函数调用
- **状态管理冲突**：两个 ADR 对同一游戏状态定义权威
  （例如 Combat ADR 和 Character ADR 都声称拥有 health 值）

对于发现的每个冲突：

```
## Conflict: [ADR-NNNN] vs [ADR-MMMM]
Type: [Data ownership / Integration / Performance / Dependency / Pattern / State]
ADR-NNNN claims: [...]
ADR-MMMM claims: [...]
Impact: [What breaks if both are implemented as written]
Resolution options:
  1. [Option A]
  2. [Option B]
```

### ADR 依赖排序

冲突检测后，分析所有 ADR 的依赖图：

1. **收集所有 `Depends On` 字段**，从每个 ADR 的 "ADR Dependencies" 章节
2. **拓扑排序**：确定正确的实现顺序 —— 无依赖的 ADR
   排在前面（Foundation），依赖它们的排在其后，等等。
3. **标记未解决的依赖**：如 ADR-A 的 "Depends On" 字段引用了一个
   仍为 `Proposed` 或不存在的 ADR，标记它：
   ```
   ⚠️  ADR-0005 depends on ADR-0002 — but ADR-0002 is still Proposed.
       ADR-0005 cannot be safely implemented until ADR-0002 is Accepted.
   ```
4. **循环检测**：如 ADR-A 依赖 ADR-B 且 ADR-B 依赖 ADR-A（直接
   或传递），将其标记为 `DEPENDENCY CYCLE`：
   ```
   🔴 DEPENDENCY CYCLE: ADR-0003 → ADR-0006 → ADR-0003
      This cycle must be broken before either can be implemented.
   ```
5. **输出建议实现顺序**：
   ```
   ### Recommended ADR Implementation Order (topologically sorted)
   Foundation (no dependencies):
     1. ADR-0001: [title]
     2. ADR-0003: [title]
   Depends on Foundation:
     3. ADR-0002: [title] (requires ADR-0001)
     4. ADR-0005: [title] (requires ADR-0003)
   Feature layer:
     5. ADR-0004: [title] (requires ADR-0002, ADR-0005)
   ```

---

## Phase 5: Engine Compatibility Cross-Check

Across all ADRs, check for engine consistency:

### Version Consistency
- Do all ADRs that mention an engine version agree on the same version?
- If any ADR was written for an older engine version, flag it as potentially stale

### Post-Cutoff API Consistency
- Collect all "Post-Cutoff APIs Used" fields from all ADRs
- For each, verify against the relevant module reference doc
- Check that no two ADRs make contradictory assumptions about the same post-cutoff API

### Deprecated API Check
- Grep all ADRs for API names listed in `deprecated-apis.md`
- Flag any ADR referencing a deprecated API

### Missing Engine Compatibility Sections
- List all ADRs that are missing the Engine Compatibility section entirely
- These are blind spots — their engine assumptions are unknown

Output format:
```
### Engine Audit Results
Engine: [name + version]
ADRs with Engine Compatibility section: X / Y total

Deprecated API References:
  - ADR-0002: uses [deprecated API] — deprecated since [version]

Stale Version References:
  - ADR-0001: written for [older version] — current project version is [version]

Post-Cutoff API Conflicts:
  - ADR-0004 and ADR-0007 both use [API] with incompatible assumptions
```

## 第 5 阶段：引擎兼容性交叉检查

跨所有 ADR，检查引擎一致性：

### 版本一致性
- 提及引擎版本的所有 ADR 是否就同一版本达成一致？
- 如任何 ADR 是为旧引擎版本编写的，将其标记为可能陈旧

### 截止线后 API 一致性
- 收集所有 ADR 中的 "Post-Cutoff APIs Used" 字段
- 对每个，对照相关模块参考文档验证
- 检查没有两个 ADR 对同一截止线后 API 做出矛盾假设

### 弃用 API 检查
- 在所有 ADR 中 Grep `deprecated-apis.md` 中列出的 API 名称
- 标记任何引用弃用 API 的 ADR

### 缺失引擎兼容性章节
- 列出完全缺失 Engine Compatibility 章节的所有 ADR
- 这些是盲区 —— 它们的引擎假设未知

输出格式：
```
### Engine Audit Results
Engine: [name + version]
ADRs with Engine Compatibility section: X / Y total

Deprecated API References:
  - ADR-0002: uses [deprecated API] — deprecated since [version]

Stale Version References:
  - ADR-0001: written for [older version] — current project version is [version]

Post-Cutoff API Conflicts:
  - ADR-0004 and ADR-0007 both use [API] with incompatible assumptions
```

---

### Engine Specialist Consultation

After completing the engine audit above, spawn the **primary engine specialist** via Task for a domain-expert second opinion:
- Read `.claude/docs/technical-preferences.md` `Engine Specialists` section to get the primary specialist
- If no engine is configured, skip this consultation
- Spawn `subagent_type: [primary specialist]` with: all ADRs that contain engine-specific decisions or `Post-Cutoff APIs Used` fields, the engine reference docs, and the Phase 5 audit findings. Ask them to:
  1. Confirm or challenge each audit finding — specialists may know of engine nuances not captured in the reference docs
  2. Identify engine-specific anti-patterns in the ADRs that the audit may have missed (e.g., using the wrong Godot node type, Unity component coupling, Unreal subsystem misuse)
  3. Flag ADRs that make assumptions about engine behaviour that differ from the actual pinned version

Incorporate additional findings under `### Engine Specialist Findings` in the Phase 5 output. These feed into the final verdict — specialist-identified issues carry the same weight as audit-identified issues.

### 引擎专家咨询

完成上述引擎审计后，通过 Task 派生 **主引擎专家** 以获取领域专家第二意见：
- 读取 `.claude/docs/technical-preferences.md` 的 `Engine Specialists` 章节获取主专家
- 如未配置引擎，跳过此咨询
- 派生 `subagent_type: [primary specialist]`，传入：所有包含引擎特定决策或 `Post-Cutoff APIs Used` 字段的 ADR、引擎参考文档，以及第 5 阶段审计发现。要求他们：
  1. 确认或挑战每条审计发现 —— 专家可能知晓参考文档未捕获的引擎细节
  2. 识别审计可能遗漏的 ADR 中引擎特定的反模式（例如使用了错误的 Godot 节点类型、Unity 组件耦合、Unreal 子系统误用）
  3. 标记对引擎行为的假设与实际锁定版本不同的 ADR

将额外发现整合到第 5 阶段输出的 `### Engine Specialist Findings` 下。这些将汇入最终判定 —— 专家识别的问题与审计识别的问题权重相同。

---

## Phase 5b: Design Revision Flags (Architecture → GDD Feedback)

For each **HIGH RISK engine finding** from Phase 5, check whether any GDD makes an
assumption that the verified engine reality contradicts.

Specific cases to check:

1. **Post-cutoff API behaviour differs from training-data assumptions**: If an ADR
   records a verified API behaviour that differs from the default LLM assumption,
   check all GDDs that reference the related system. Look for design rules written
   around the old (assumed) behaviour.

2. **Known engine limitations in ADRs**: If an ADR records a known engine limitation
   (e.g. "Jolt ignores HingeJoint3D damp", "D3D12 is now the default backend"), check
   GDDs that design mechanics around the affected feature.

3. **Deprecated API conflicts**: If Phase 5 flagged a deprecated API used in an ADR,
   check whether any GDD contains mechanics that assume the deprecated API's behaviour.

For each conflict found, record it in the GDD Revision Flags table:

```
### GDD Revision Flags (Architecture → Design Feedback)
These GDD assumptions conflict with verified engine behaviour or accepted ADRs.
The GDD should be revised before its system enters implementation.

| GDD | Assumption | Reality (from ADR/engine-reference) | Action |
|-----|-----------|--------------------------------------|--------|
| combat.md | "Use HingeJoint3D damp for weapon recoil" | Jolt ignores damp — ADR-0003 | Revise GDD |
```

If no revision flags are found, write: "No GDD revision flags — all GDD assumptions
are consistent with verified engine behaviour."

Before asking, display the proposed change inline — show the current systems-index row for each flagged GDD and the proposed updated row side by side so the user can see exactly what will change.

Then use `AskUserQuestion`:
- "I found [N] GDD revision flag(s). May I update the systems index?"
  - [A] Yes — apply all [N] updates to the systems index now
  - [B] Show me the full diff first, then ask again
  - [C] No — leave the systems index unchanged for now

If [A]: apply the updates. Status field must be exactly `Needs Revision` — no parentheticals
(other skills match that exact string and parentheticals break the match).
If [B]: display the complete proposed systems-index section, then re-ask with `AskUserQuestion`.

## 第 5b 阶段：设计修订标记（架构 → GDD 反馈）

对于第 5 阶段中每个 **HIGH RISK 引擎发现**，检查是否有 GDD 做出了
与已验证引擎现实矛盾的假设。

要检查的具体情况：

1. **截止线后 API 行为与训练数据假设不同**：如某 ADR
   记录的已验证 API 行为与默认 LLM 假设不同，
   检查所有引用相关系统的 GDD。寻找围绕
   旧（假设的）行为编写的设计规则。

2. **ADR 中的已知引擎限制**：如某 ADR 记录了已知引擎限制
   （例如 "Jolt 忽略 HingeJoint3D damp"、"D3D12 现在是默认后端"），检查
   围绕受影响特性设计机制的 GDD。

3. **弃用 API 冲突**：如第 5 阶段标记了 ADR 中使用的弃用 API，
   检查是否有 GDD 包含假设该弃用 API 行为的机制。

对于发现的每个冲突，记录到 GDD Revision Flags 表中：

```
### GDD Revision Flags (Architecture → Design Feedback)
These GDD assumptions conflict with verified engine behaviour or accepted ADRs.
The GDD should be revised before its system enters implementation.

| GDD | Assumption | Reality (from ADR/engine-reference) | Action |
|-----|-----------|--------------------------------------|--------|
| combat.md | "Use HingeJoint3D damp for weapon recoil" | Jolt ignores damp — ADR-0003 | Revise GDD |
```

如未发现修订标记，写入："No GDD revision flags — all GDD assumptions
are consistent with verified engine behaviour."

询问前，内联显示拟议变更 —— 并排显示每个被标记 GDD 的当前 systems-index 行和拟议更新后的行，让用户看到具体将变更什么。

然后使用 `AskUserQuestion`：
- "我发现 [N] 个 GDD 修订标记。可以更新系统索引吗？"
  - [A] 是 —— 现在将所有 [N] 项更新应用到系统索引
  - [B] 先看完整 diff，再询问
  - [C] 否 —— 暂时保持系统索引不变

如选 [A]：应用更新。Status 字段必须完全是 `Needs Revision` —— 无括号
（其他技能匹配该确切字符串，括号会破坏匹配）。
如选 [B]：显示完整的拟议 systems-index 章节，然后用 `AskUserQuestion` 再次询问。

---

## Phase 6: Architecture Document Coverage

If `docs/architecture/architecture.md` exists, validate it against GDDs:

- Does every system from `systems-index.md` appear in the architecture layers?
- Does the data flow section cover all cross-system communication defined in GDDs?
- Do the API boundaries support all integration requirements from GDDs?
- Are there systems in the architecture doc that have no corresponding GDD
  (orphaned architecture)?

## 第 6 阶段：架构文档覆盖

如 `docs/architecture/architecture.md` 存在，对照 GDD 验证它：

- `systems-index.md` 中的每个系统是否都出现在架构层中？
- 数据流章节是否覆盖 GDD 中定义的所有跨系统通信？
- API 边界是否支持 GDD 中的所有集成需求？
- 架构文档中是否存在没有对应 GDD 的系统
  （孤立架构）？

---

## Phase 7: Output the Review Report

```
## Architecture Review Report
Date: [date]
Engine: [name + version]
GDDs Reviewed: [N]
ADRs Reviewed: [M]

---

### Traceability Summary
Total requirements: [N]
✅ Covered: [X]
⚠️ Partial: [Y]
❌ Gaps: [Z]

### Coverage Gaps (no ADR exists)
For each gap:
  ❌ TR-[id]: [GDD] → [system] → [requirement]
     Suggested ADR: "/architecture-decision [suggested title]"
     Domain: [Physics/Rendering/etc]
     Engine Risk: [LOW/MEDIUM/HIGH]

### Cross-ADR Conflicts
[List all conflicts from Phase 4]

### ADR Dependency Order
[Topologically sorted implementation order from Phase 4 — dependency ordering section]
[Unresolved dependencies and cycles if any]

### GDD Revision Flags
[GDD assumptions that conflict with verified engine behaviour — from Phase 5b]
[Or: "None — all GDD assumptions consistent with verified engine behaviour"]

### Engine Compatibility Issues
[List all engine issues from Phase 5]

### Architecture Document Coverage
[List missing systems and orphaned architecture from Phase 6]

---

### Verdict: [PASS / CONCERNS / FAIL]

PASS: All requirements covered, no conflicts, engine consistent
CONCERNS: Some gaps or partial coverage, but no blocking conflicts
FAIL: Critical gaps (Foundation/Core layer requirements uncovered),
      or blocking cross-ADR conflicts detected

### Blocking Issues (must resolve before PASS)
[List items that must be resolved — FAIL verdict only]

### Required ADRs
[Prioritised list of ADRs to create, most foundational first]
```

## 第 7 阶段：输出评审报告

```
## Architecture Review Report
Date: [date]
Engine: [name + version]
GDDs Reviewed: [N]
ADRs Reviewed: [M]

---

### Traceability Summary
Total requirements: [N]
✅ Covered: [X]
⚠️ Partial: [Y]
❌ Gaps: [Z]

### Coverage Gaps (no ADR exists)
For each gap:
  ❌ TR-[id]: [GDD] → [system] → [requirement]
     Suggested ADR: "/architecture-decision [suggested title]"
     Domain: [Physics/Rendering/etc]
     Engine Risk: [LOW/MEDIUM/HIGH]

### Cross-ADR Conflicts
[List all conflicts from Phase 4]

### ADR Dependency Order
[Topologically sorted implementation order from Phase 4 — dependency ordering section]
[Unresolved dependencies and cycles if any]

### GDD Revision Flags
[GDD assumptions that conflict with verified engine behaviour — from Phase 5b]
[Or: "None — all GDD assumptions consistent with verified engine behaviour"]

### Engine Compatibility Issues
[List all engine issues from Phase 5]

### Architecture Document Coverage
[List missing systems and orphaned architecture from Phase 6]

---

### Verdict: [PASS / CONCERNS / FAIL]

PASS: All requirements covered, no conflicts, engine consistent
CONCERNS: Some gaps or partial coverage, but no blocking conflicts
FAIL: Critical gaps (Foundation/Core layer requirements uncovered),
      or blocking cross-ADR conflicts detected

### Blocking Issues (must resolve before PASS)
[List items that must be resolved — FAIL verdict only]

### Required ADRs
[Prioritised list of ADRs to create, most foundational first]
```

---

## Phase 8: Write and Update Traceability Index

Use `AskUserQuestion` for the write approval:
- "Review complete. What would you like to write?"
  - [A] Write all three files (review report + traceability index + TR registry)
  - [B] Write review report only — `docs/architecture/architecture-review-[date].md`
  - [C] Don't write anything yet — I need to review the findings first

### RTM Output (rtm mode only)

For `rtm` mode, use `AskUserQuestion`:
- "May I write the full Requirements Traceability Matrix?"
  - [A] Yes — write to `docs/architecture/requirements-traceability.md`
  - [B] Not yet — show me the full RTM data first, then ask again

RTM file format:

```markdown
# Requirements Traceability Matrix (RTM)

> Last Updated: [date]
> Mode: /architecture-review rtm
> Coverage: [N]% full chain complete (GDD → ADR → Story → Test)

## How to read this matrix

| Column | Meaning |
|--------|---------|
| TR-ID | Stable requirement ID from tr-registry.yaml |
| GDD | Source design document |
| ADR | Architectural decision governing implementation |
| Story | Story file that implements this requirement |
| Test File | Automated test file path |
| Test Status | COVERED / MISSING / NONE / NO STORY |

## Full Traceability Matrix

| TR-ID | GDD | Requirement | ADR | Story | Test File | Status |
|-------|-----|-------------|-----|-------|-----------|--------|
[Full matrix rows from Phase 3b]

## Coverage Summary

| Status | Count | % |
|--------|-------|---|
| COVERED — full chain complete | [N] | [%] |
| MISSING test — story exists, no test | [N] | [%] |
| NO STORY — ADR exists, not yet implemented | [N] | [%] |
| NO ADR — architectural gap | [N] | [%] |
| **Total requirements** | **[N]** | **100%** |

## Uncovered Requirements (Priority Fix List)

Requirements where the full chain is broken, prioritised by layer:

### Foundation layer gaps
[list with suggested action per gap]

### Core layer gaps
[list]

### Feature / Presentation layer gaps
[list — lower priority]

## History

| Date | Full Chain % | Notes |
|------|-------------|-------|
| [date] | [%] | Initial RTM |
```

### TR Registry Update

Also ask: "May I update `docs/architecture/tr-registry.yaml` with new requirement
IDs from this review?"

If yes:
- **Append** any new TR-IDs that weren't in the registry before this review
- **Update** `requirement` text and `revised` date for any entries whose GDD
  wording changed (ID stays the same)
- **Mark** `status: deprecated` for any registry entries whose GDD requirement
  no longer exists (confirm with user before marking deprecated)
- **Never** renumber or delete existing entries
- Update the `last_updated` and `version` fields at the top

This ensures all future story files can reference stable TR-IDs that persist
across every subsequent architecture review.

### Reflexion Log Update

After writing the review report, append any 🔴 CONFLICT entries found in Phase 4
to `docs/consistency-failures.md` (if the file exists):

```markdown
### [YYYY-MM-DD] — /architecture-review — 🔴 CONFLICT
**Domain**: Architecture / [specific domain e.g. State Ownership, Performance]
**Documents involved**: [ADR-NNNN] vs [ADR-MMMM]
**What happened**: [specific conflict — what each ADR claims]
**Resolution**: [how it was or should be resolved]
**Pattern**: [generalised lesson for future ADR authors in this domain]
```

Only append CONFLICT entries — do not log GAP entries (missing ADRs are expected
before the architecture is complete). Do not create the file if missing — only
append when it already exists.

### Session State Update

After writing all approved files, silently append to
`production/session-state/active.md`:

    ## Session Extract — /architecture-review [date]
    - Verdict: [PASS / CONCERNS / FAIL]
    - Requirements: [N] total — [X] covered, [Y] partial, [Z] gaps
    - New TR-IDs registered: [N, or "None"]
    - GDD revision flags: [comma-separated GDD names, or "None"]
    - Top ADR gaps: [top 3 gap titles from the report, or "None"]
    - Report: docs/architecture/architecture-review-[date].md

If `active.md` does not exist, create it with this block as the initial content.
Confirm in conversation: "Session state updated."

The traceability index format:

```markdown
# Architecture Traceability Index
Last Updated: [date]
Engine: [name + version]

## Coverage Summary
- Total requirements: [N]
- Covered: [X] ([%])
- Partial: [Y]
- Gaps: [Z]

## Full Matrix
[Complete traceability matrix from Phase 3]

## Known Gaps
[All ❌ items with suggested ADRs]

## Superseded Requirements
[Requirements whose GDD was changed after the ADR was written]
```

## 第 8 阶段：写入并更新可追溯性索引

使用 `AskUserQuestion` 请求写入批准：
- "评审完成。你想写入什么？"
  - [A] 写入全部三个文件（评审报告 + 可追溯性索引 + TR 注册表）
  - [B] 仅写评审报告 —— `docs/architecture/architecture-review-[date].md`
  - [C] 暂不写入 —— 我需要先评审发现

### RTM 输出（仅 rtm 模式）

对于 `rtm` 模式，使用 `AskUserQuestion`：
- "可以写入完整的需求可追溯性矩阵吗？"
  - [A] 是 —— 写入 `docs/architecture/requirements-traceability.md`
  - [B] 暂不 —— 先给我看完整 RTM 数据，再询问

RTM 文件格式：

```markdown
# Requirements Traceability Matrix (RTM)

> Last Updated: [date]
> Mode: /architecture-review rtm
> Coverage: [N]% full chain complete (GDD → ADR → Story → Test)

## How to read this matrix

| Column | Meaning |
|--------|---------|
| TR-ID | Stable requirement ID from tr-registry.yaml |
| GDD | Source design document |
| ADR | Architectural decision governing implementation |
| Story | Story file that implements this requirement |
| Test File | Automated test file path |
| Test Status | COVERED / MISSING / NONE / NO STORY |

## Full Traceability Matrix

| TR-ID | GDD | Requirement | ADR | Story | Test File | Status |
|-------|-----|-------------|-----|-------|-----------|--------|
[Full matrix rows from Phase 3b]

## Coverage Summary

| Status | Count | % |
|--------|-------|---|
| COVERED — full chain complete | [N] | [%] |
| MISSING test — story exists, no test | [N] | [%] |
| NO STORY — ADR exists, not yet implemented | [N] | [%] |
| NO ADR — architectural gap | [N] | [%] |
| **Total requirements** | **[N]** | **100%** |

## Uncovered Requirements (Priority Fix List)

Requirements where the full chain is broken, prioritised by layer:

### Foundation layer gaps
[list with suggested action per gap]

### Core layer gaps
[list]

### Feature / Presentation layer gaps
[list — lower priority]

## History

| Date | Full Chain % | Notes |
|------|-------------|-------|
| [date] | [%] | Initial RTM |
```

### TR 注册表更新

同时询问："可以用本次评审的新需求 ID 更新 `docs/architecture/tr-registry.yaml` 吗？"

如同意：
- **追加** 本次评审前未在注册表中的任何新 TR-ID
- **更新** GDD 措辞已变更条目的 `requirement` 文本和 `revised` 日期
  （ID 保持不变）
- **标记** `status: deprecated` 用于 GDD 需求
  不再存在的注册表条目（标记为弃用前需与用户确认）
- **永远不要** 重新编号或删除现有条目
- 更新顶部的 `last_updated` 和 `version` 字段

这确保所有未来故事文件能引用跨后续每次架构评审持久存在的稳定 TR-ID。

### 反思日志更新

写入评审报告后，将第 4 阶段中发现的任何 🔴 CONFLICT 条目追加到
`docs/consistency-failures.md`（如该文件存在）：

```markdown
### [YYYY-MM-DD] — /architecture-review — 🔴 CONFLICT
**Domain**: Architecture / [specific domain e.g. State Ownership, Performance]
**Documents involved**: [ADR-NNNN] vs [ADR-MMMM]
**What happened**: [specific conflict — what each ADR claims]
**Resolution**: [how it was or should be resolved]
**Pattern**: [generalised lesson for future ADR authors in this domain]
```

仅追加 CONFLICT 条目 —— 不要记录 GAP 条目（架构完成前缺少 ADR 是预期内的）。如缺失则不要创建该文件 —— 仅在已存在时追加。

### 会话状态更新

写入所有已批准文件后，静默追加到
`production/session-state/active.md`：

    ## Session Extract — /architecture-review [date]
    - Verdict: [PASS / CONCERNS / FAIL]
    - Requirements: [N] total — [X] covered, [Y] partial, [Z] gaps
    - New TR-IDs registered: [N, or "None"]
    - GDD revision flags: [comma-separated GDD names, or "None"]
    - Top ADR gaps: [top 3 gap titles from the report, or "None"]
    - Report: docs/architecture/architecture-review-[date].md

如 `active.md` 不存在，用此区块作为初始内容创建它。
在对话中确认："Session state updated."

可追溯性索引格式：

```markdown
# Architecture Traceability Index
Last Updated: [date]
Engine: [name + version]

## Coverage Summary
- Total requirements: [N]
- Covered: [X] ([%])
- Partial: [Y]
- Gaps: [Z]

## Full Matrix
[Complete traceability matrix from Phase 3]

## Known Gaps
[All ❌ items with suggested ADRs]

## Superseded Requirements
[Requirements whose GDD was changed after the ADR was written]
```

---

## Phase 9: Handoff

After completing the review and writing approved files, present:

1. **Immediate actions**: List the top 3 ADRs to create (highest-impact gaps first,
   Foundation layer before Feature layer)
2. **Pre-gate checklist**: Check whether these exist via Glob and mark each ✅ or ❌:
   - `tests/unit/` and `tests/integration/` directories — if ❌: run `/test-setup`
   - `.github/workflows/tests.yml` — if ❌: run `/test-setup`
   - `design/accessibility-requirements.md` — if ❌: run `/ux-design`
   - `design/ux/interaction-patterns.md` — if ❌: run `/ux-design`
   Present ❌ items as required steps before gate-check. Do not offer `/gate-check`
   as an option if any item is ❌ — offer the missing skill to run instead.
3. **Rerun trigger**: "Re-run `/architecture-review` after each new ADR is written
   to verify coverage improves"

Then close with `AskUserQuestion` tailored to the pre-gate checklist state:
- If ADR gaps remain or any pre-gate item is ❌:
  - "Architecture review complete. What would you like to do next?"
    - [A] Write a missing ADR — open a fresh session and run `/architecture-decision [system]`
    - [B] Run `/test-setup` — required before gate-check (only show if test infrastructure is ❌)
    - [C] Run `/ux-design` — required before gate-check (only show if UX/accessibility files are ❌)
    - [D] Stop here for this session
- If all pre-gate checklist items are ✅ and no blocking ADR gaps remain:
  - "Architecture review complete. All pre-gate items confirmed. What would you like to do next?"
    - [A] Run `/gate-check pre-production`
    - [B] Write a missing ADR — open a fresh session and run `/architecture-decision [system]`
    - [C] Stop here for this session

## 第 9 阶段：交接

完成评审并写入已批准文件后，呈现：

1. **即时行动**：列出要创建的前 3 个 ADR（影响最大的缺口在前，
   Foundation 层在 Feature 层之前）
2. **门禁前清单**：通过 Glob 检查以下是否存在，并标记 ✅ 或 ❌：
   - `tests/unit/` 和 `tests/integration/` 目录 —— 如 ❌：运行 `/test-setup`
   - `.github/workflows/tests.yml` —— 如 ❌：运行 `/test-setup`
   - `design/accessibility-requirements.md` —— 如 ❌：运行 `/ux-design`
   - `design/ux/interaction-patterns.md` —— 如 ❌：运行 `/ux-design`
   将 ❌ 项作为 gate-check 前的必需步骤呈现。如任何项为 ❌，不要提供 `/gate-check`
   作为选项 —— 改为提供要运行的缺失技能。
3. **重新运行触发器**："每写一个新 ADR 后重新运行 `/architecture-review`
   以验证覆盖是否改善"

然后用根据门禁前清单状态定制的 `AskUserQuestion` 收尾：
- 如仍有 ADR 缺口或任何门禁前项为 ❌：
  - "架构评审完成。接下来你想做什么？"
    - [A] 写一个缺失的 ADR —— 开新会话运行 `/architecture-decision [system]`
    - [B] 运行 `/test-setup` —— gate-check 前必需（仅在测试基础设施为 ❌ 时显示）
    - [C] 运行 `/ux-design` —— gate-check 前必需（仅在 UX/可访问性文件为 ❌ 时显示）
    - [D] 本次会话到此为止
- 如所有门禁前清单项均为 ✅ 且无阻塞性 ADR 缺口：
  - "架构评审完成。所有门禁前项已确认。接下来你想做什么？"
    - [A] 运行 `/gate-check pre-production`
    - [B] 写一个缺失的 ADR —— 开新会话运行 `/architecture-decision [system]`
    - [C] 本次会话到此为止

---

## Error Recovery Protocol

If any spawned agent returns BLOCKED, errors, or fails to complete:

1. **Surface immediately**: Report "[AgentName]: BLOCKED — [reason]" before continuing
2. **Assess dependencies**: If the blocked agent's output is required by a later phase, do not proceed past that phase without user input
3. **Offer options** via AskUserQuestion with three choices:
   - Skip this agent and note the gap in the final report
   - Retry with narrower scope (fewer GDDs, single-system focus)
   - Stop here and resolve the blocker first
4. **Always produce a partial report** — output whatever was completed so work is not lost

## 错误恢复协议

如任何派生的智能体返回 BLOCKED、错误或未能完成：

1. **立即呈现**：在继续之前报告 "[AgentName]: BLOCKED — [reason]"
2. **评估依赖**：如被阻塞智能体的输出为后续阶段所需，未经用户输入不要越过该阶段
3. **通过 AskUserQuestion 提供选项**，包含三个选择：
   - 跳过此智能体并在最终报告中注明缺口
   - 以更窄范围重试（更少 GDD，单系统聚焦）
   - 在此停止并先解决阻塞项
4. **始终生成部分报告** —— 输出已完成的任何内容以免工作丢失

---

## Collaborative Protocol

1. **Read silently** — do not narrate every file read
2. **Show the matrix** — present the full traceability matrix before asking for
   anything; let the user see the state
3. **Don't guess** — if a requirement is ambiguous, ask: "Is [X] a technical
   requirement or a design preference?"
4. **Draft before approval** — always show the content that will be written (the
   report, the updated ADR section, the systems-index row) inline in the conversation
   before requesting approval. Never ask to write something the user has not yet seen.
5. **Use `AskUserQuestion` for write approvals** — plain text "May I?" is not
   sufficient. Use the structured tool with labeled options [A]/[B]/[C] so the
   user can choose between "write now", "show full draft first", and "not yet".
   Multi-file changesets must list every file and what changes, then ask once
   with grouped options — not a separate plain-text question per file.
6. **Non-blocking** — the verdict is advisory; the user decides whether to continue
   despite CONCERNS or even FAIL findings

## 协作协议

1. **静默读取** —— 不要叙述每个读取的文件
2. **展示矩阵** —— 在要求任何东西之前展示完整可追溯性矩阵；
   让用户看到状态
3. **不要猜测** —— 如需求不明确，问："[X] 是技术需求
   还是设计偏好？"
4. **批准前起草** —— 始终在请求批准之前在对话中
   内联展示将要写入的内容（报告、更新的 ADR 章节、systems-index 行）。永远不要请求写入用户尚未看到的东西。
5. **使用 `AskUserQuestion` 请求写入批准** —— 纯文本 "可以吗？" 不足。
   使用带标签选项 [A]/[B]/[C] 的结构化工具，以便
   用户在"现在写入"、"先看完整草稿"、"暂不"之间选择。
   多文件变更集必须列出每个文件及变更内容，然后用
   分组选项一次性询问 —— 而非每个文件单独的纯文本问题。
6. **非阻塞** —— 判定是建议性的；用户决定是否在
   CONCERNS 甚至 FAIL 发现下继续
