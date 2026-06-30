---
name: test-setup
description: "Scaffold the test framework and CI/CD pipeline for the project's engine. Creates the tests/ directory structure, engine-specific test runner configuration, and GitHub Actions workflow. Run once during Technical Setup phase before the first sprint begins."
argument-hint: "[force]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write
model: sonnet
---
---
名称：test-setup
描述："为项目引擎脚手架测试框架和 CI/CD 流水线。创建 tests/ 目录结构、引擎特定的测试运行器配置和 GitHub Actions 工作流。在第一个冲刺开始前的技术设置阶段运行一次。"
argument-hint："[force]"
user-invocable：true
allowed-tools：Read, Glob, Grep, Bash, Write
model：sonnet
---

# Test Setup
# 测试设置

This skill scaffolds the automated testing infrastructure for the project.
It detects the configured engine, generates the appropriate test runner
configuration, creates the standard directory layout, and wires up CI/CD
so tests run on every push.
此技能为项目脚手架自动化测试基础设施。
它检测配置的引擎，生成适当的测试运行器配置，创建标准目录布局，并连接 CI/CD
以便每次推送时运行测试。

Run this once during the Technical Setup phase, before any implementation
begins. A test framework installed at sprint start costs 30 minutes.
A test framework installed at sprint four costs 3 sprints.
在技术设置阶段、任何实施开始前运行一次。冲刺开始时安装的测试框架花费 30 分钟。
第四冲刺时安装的测试框架花费 3 个冲刺。

**Output:** `tests/` directory structure + `.github/workflows/tests.yml`
**输出：** `tests/` 目录结构 + `.github/workflows/tests.yml`

---

## Phase 1: Detect Engine and Existing State
## 阶段 1：检测引擎和现有状态

1. **Read engine config**:
1. **读取引擎配置**：
   - Read `.claude/docs/technical-preferences.md` and extract the `Engine:` value.
   - 读取 `.claude/docs/technical-preferences.md` 并提取 `Engine:` 值。
   - If engine is not configured (`[TO BE CONFIGURED]`), stop:
   - 若引擎未配置（`[TO BE CONFIGURED]`），停止：
     "Engine not configured. Run `/setup-engine` first, then re-run `/test-setup`."
     "引擎未配置。先运行 `/setup-engine`，然后重新运行 `/test-setup`。"

2. **Check for existing test infrastructure**:
2. **检查现有测试基础设施**：
   - Glob `tests/` — does the directory exist?
   - Glob `tests/` —— 目录是否存在？
   - Glob `tests/unit/` and `tests/integration/` — do subdirectories exist?
   - Glob `tests/unit/` 和 `tests/integration/` —— 子目录是否存在？
   - Glob `.github/workflows/` — does a CI workflow file exist?
   - Glob `.github/workflows/` —— CI 工作流文件是否存在？
   - Glob `tests/gdunit4_runner.gd` (Godot) or `tests/EditMode/` (Unity) or
     `Source/Tests/` (Unreal) for engine-specific artifacts.
   - Glob `tests/gdunit4_runner.gd`（Godot）或 `tests/EditMode/`（Unity）或
     `Source/Tests/`（Unreal）以查找引擎特定产物。

3. **Report findings**:
3. **报告发现**：
   - "Engine: [engine]. Test directory: [found / not found]. CI workflow: [found / not found]."
   - "引擎：[engine]。测试目录：[找到 / 未找到]。CI 工作流：[找到 / 未找到]。"
   - If everything already exists AND `force` argument was not passed:
   - 若一切已存在且未传入 `force` 参数：
     "Test infrastructure appears to be in place. Re-run with `/test-setup force`
     to regenerate. Proceeding will not overwrite existing test files."
     "测试基础设施似乎已就位。用 `/test-setup force` 重新运行以重新生成。
     继续不会覆盖现有测试文件。"

If the `force` argument is passed, skip the "already exists" early-exit and
proceed — but still do not overwrite files that already exist at a given path.
Only create files that are missing.
若传入 `force` 参数，跳过"已存在"提前退出并继续 —— 但仍不覆盖给定路径已存在的文件。
仅创建缺失的文件。

---

## Phase 2: Present Plan
## 阶段 2：呈现计划

Based on the engine detected and the existing state, present a plan:
基于检测到的引擎和现有状态，呈现计划：

```
## Test Setup Plan — [Engine]

I will create the following (skipping any that already exist):

tests/
  unit/           — Isolated unit tests for formulas, state, and logic
  integration/    — Cross-system tests and save/load round-trips
  smoke/          — Critical path test list (15-minute manual gate)
  evidence/       — Screenshot and manual test sign-off records
  README.md       — Test framework documentation

[Engine-specific files — see per-engine details below]

.github/workflows/tests.yml  — CI: run tests on every push to main

Estimated time: ~5 minutes to create all files.
```
```
## 测试设置计划 —— [Engine]

我将创建以下内容（跳过任何已存在的）：

tests/
  unit/           —— 公式、状态和逻辑的隔离单元测试
  integration/    —— 跨系统测试和存档/读档往返
  smoke/          —— 关键路径测试列表（15 分钟手动门禁）
  evidence/       —— 截图和手动测试签字记录
  README.md       —— 测试框架文档

[引擎特定文件 —— 见下方各引擎详情]

.github/workflows/tests.yml  —— CI：每次推送到 main 时运行测试

估计时间：创建所有文件约 5 分钟。
```

Ask: "May I create these files? I will not overwrite any test files that
already exist at these paths."
询问："我可以创建这些文件吗？我不会覆盖这些路径上已存在的任何测试文件。"

Do not proceed without approval.
未经批准不要继续。

---

## Phase 3: Create Directory Structure
## 阶段 3：创建目录结构

After approval, create the following files:
批准后，创建以下文件：

### `tests/README.md`

```markdown
# Test Infrastructure

**Engine**: [engine name + version]
**Test Framework**: [GdUnit4 | Unity Test Framework | UE Automation]
**CI**: `.github/workflows/tests.yml`
**Setup date**: [date]

## Directory Layout

```
tests/
  unit/           # Isolated unit tests (formulas, state machines, logic)
  integration/    # Cross-system and save/load tests
  smoke/          # Critical path test list for /smoke-check gate
  evidence/       # Screenshot logs and manual test sign-off records
```

## Running Tests

[Engine-specific command — see below]

## Test Naming

- **Files**: `[system]_[feature]_test.[ext]`
- **Functions**: `test_[scenario]_[expected]`
- **Example**: `combat_damage_test.gd` → `test_base_attack_returns_expected_damage()`

## Story Type → Test Evidence

| Story Type | Required Evidence | Location |
|---|---|---|
| Logic | Automated unit test — must pass | `tests/unit/[system]/` |
| Integration | Integration test OR playtest doc | `tests/integration/[system]/` |
| Visual/Feel | Screenshot + lead sign-off | `tests/evidence/` |
| UI | Manual walkthrough OR interaction test | `tests/evidence/` |
| Config/Data | Smoke check pass | `production/qa/smoke-*.md` |

## CI

Tests run automatically on every push to `main` and on every pull request.
A failed test suite blocks merging.
```
```
# 测试基础设施

**引擎**：[引擎名 + 版本]
**测试框架**：[GdUnit4 | Unity Test Framework | UE Automation]
**CI**：`.github/workflows/tests.yml`
**设置日期**：[日期]

## 目录布局

```
tests/
  unit/           # 隔离单元测试（公式、状态机、逻辑）
  integration/    # 跨系统和存档/读档测试
  smoke/          # /smoke-check 门禁的关键路径测试列表
  evidence/       # 截图日志和手动测试签字记录
```

## 运行测试

[引擎特定命令 —— 见下方]

## 测试命名

- **文件**：`[system]_[feature]_test.[ext]`
- **函数**：`test_[scenario]_[expected]`
- **示例**：`combat_damage_test.gd` → `test_base_attack_returns_expected_damage()`

## 故事类型 → 测试证据

| 故事类型 | 所需证据 | 位置 |
|---|---|---|
| Logic | 自动化单元测试 —— 必须通过 | `tests/unit/[system]/` |
| Integration | 集成测试或试玩文档 | `tests/integration/[system]/` |
| Visual/Feel | 截图 + 主管签字 | `tests/evidence/` |
| UI | 手动走查或交互测试 | `tests/evidence/` |
| Config/Data | 冒烟检查通过 | `production/qa/smoke-*.md` |

## CI

测试在每次推送到 `main` 和每个拉取请求时自动运行。
失败的测试套件阻止合并。
```

### Engine-specific files
### 引擎特定文件

#### Godot 4 (`Engine: Godot`)
#### Godot 4（`Engine: Godot`）

Create `tests/gdunit4_runner.gd`:
创建 `tests/gdunit4_runner.gd`：

```gdscript
# GdUnit4 test runner — invoked by CI and /smoke-check
# Usage: godot --headless --script tests/gdunit4_runner.gd
extends SceneTree

func _init() -> void:
    var runner := load("res://addons/gdunit4/GdUnitRunner.gd")
    if runner == null:
        push_error("GdUnit4 not found. Install via AssetLib or addons/.")
        quit(1)
        return
    var instance = runner.new()
    instance.run_tests()
    quit(0)
```
```gdscript
# GdUnit4 测试运行器 —— 由 CI 和 /smoke-check 调用
# 用法：godot --headless --script tests/gdunit4_runner.gd
extends SceneTree

func _init() -> void:
    var runner := load("res://addons/gdunit4/GdUnitRunner.gd")
    if runner == null:
        push_error("GdUnit4 not found. Install via AssetLib or addons/.")
        quit(1)
        return
    var instance = runner.new()
    instance.run_tests()
    quit(0)
```

Create `tests/unit/.gdignore_placeholder` with content:
创建 `tests/unit/.gdignore_placeholder`，内容为：
`# Unit tests go here — one subdirectory per system (e.g., tests/unit/combat/)`
`# 单元测试放在这里 —— 每个系统一个子目录（例如 tests/unit/combat/）`

Create `tests/integration/.gdignore_placeholder` with content:
创建 `tests/integration/.gdignore_placeholder`，内容为：
`# Integration tests go here — one subdirectory per system`
`# 集成测试放在这里 —— 每个系统一个子目录`

Note in the README: **Installing GdUnit4**
在 README 中注明：**安装 GdUnit4**
```
1. Open Godot → AssetLib → search "GdUnit4" → Download & Install
2. Enable the plugin: Project → Project Settings → Plugins → GdUnit4 ✓
3. Restart the editor
4. Verify: res://addons/gdunit4/ exists
```
```
1. 打开 Godot → AssetLib → 搜索 "GdUnit4" → 下载并安装
2. 启用插件：项目 → 项目设置 → 插件 → GdUnit4 ✓
3. 重启编辑器
4. 验证：res://addons/gdunit4/ 存在
```

#### Unity (`Engine: Unity`)
#### Unity（`Engine: Unity`）

Create `tests/EditMode/` placeholder file `tests/EditMode/README.md`:
创建 `tests/EditMode/` 占位文件 `tests/EditMode/README.md`：
```markdown
# Edit Mode Tests
Unit tests that run without entering Play Mode.
Use for pure logic: formulas, state machines, data validation.
Assembly definition required: `tests/EditMode/EditModeTests.asmdef`
```
```markdown
# 编辑模式测试
不进入播放模式运行的单元测试。
用于纯逻辑：公式、状态机、数据验证。
需要程序集定义：`tests/EditMode/EditModeTests.asmdef`
```

Create `tests/PlayMode/README.md`:
创建 `tests/PlayMode/README.md`：
```markdown
# Play Mode Tests
Integration tests that run in a real game scene.
Use for cross-system interactions, physics, and coroutines.
Assembly definition required: `tests/PlayMode/PlayModeTests.asmdef`
```
```markdown
# 播放模式测试
在真实游戏场景中运行的集成测试。
用于跨系统交互、物理和协程。
需要程序集定义：`tests/PlayMode/PlayModeTests.asmdef`
```

Note in the README: **Enabling Unity Test Framework**
在 README 中注明：**启用 Unity Test Framework**
```
Window → General → Test Runner
(Unity Test Framework is included by default in Unity 2019+)
```
```
Window → General → Test Runner
(Unity Test Framework 默认包含在 Unity 2019+ 中)
```

#### Unreal Engine (`Engine: Unreal` or `Engine: UE5`)
#### Unreal Engine（`Engine: Unreal` 或 `Engine: UE5`）

Create `Source/Tests/README.md`:
创建 `Source/Tests/README.md`：
```markdown
# Unreal Automation Tests
Tests use the UE Automation Testing Framework.
Run via: Session Frontend → Automation → select "MyGame." tests
Or headlessly: UnrealEditor -nullrhi -ExecCmds="Automation RunTests MyGame.; Quit"

Test class naming: F[SystemName]Test
Test category naming: "MyGame.[System].[Feature]"
```
```markdown
# Unreal 自动化测试
测试使用 UE Automation Testing Framework。
通过以下方式运行：Session Frontend → Automation → 选择 "MyGame." 测试
或无头模式：UnrealEditor -nullrhi -ExecCmds="Automation RunTests MyGame.; Quit"

测试类命名：F[SystemName]Test
测试类别命名："MyGame.[System].[Feature]"
```

---

## Phase 4: Create CI/CD Workflow
## 阶段 4：创建 CI/CD 工作流

### Godot 4
### Godot 4

Create `.github/workflows/tests.yml`:
创建 `.github/workflows/tests.yml`：

```yaml
name: Automated Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    name: Run GdUnit4 Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          lfs: true

      - name: Run GdUnit4 Tests
        uses: MikeSchulze/gdUnit4-action@v1
        with:
          godot-version: '[VERSION FROM docs/engine-reference/godot/VERSION.md]'
          paths: |
            tests/unit
            tests/integration
          report-name: test-results

      - name: Upload Test Results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: reports/
```
```yaml
name: Automated Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    name: Run GdUnit4 Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          lfs: true

      - name: Run GdUnit4 Tests
        uses: MikeSchulze/gdUnit4-action@v1
        with:
          godot-version: '[VERSION FROM docs/engine-reference/godot/VERSION.md]'
          paths: |
            tests/unit
            tests/integration
          report-name: test-results

      - name: Upload Test Results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: reports/
```

### Unity
### Unity

Create `.github/workflows/tests.yml`:
创建 `.github/workflows/tests.yml`：

```yaml
name: Automated Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    name: Run Unity Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          lfs: true

      - name: Run Edit Mode Tests
        uses: game-ci/unity-test-runner@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          testMode: editmode
          artifactsPath: test-results/editmode

      - name: Run Play Mode Tests
        uses: game-ci/unity-test-runner@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          testMode: playmode
          artifactsPath: test-results/playmode

      - name: Upload Test Results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: test-results/
```
```yaml
name: Automated Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    name: Run Unity Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          lfs: true

      - name: Run Edit Mode Tests
        uses: game-ci/unity-test-runner@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          testMode: editmode
          artifactsPath: test-results/editmode

      - name: Run Play Mode Tests
        uses: game-ci/unity-test-runner@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          testMode: playmode
          artifactsPath: test-results/playmode

      - name: Upload Test Results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: test-results/
```

Note: Unity CI requires a `UNITY_LICENSE` secret. Add to GitHub repository
secrets before the first CI run.
注意：Unity CI 需要 `UNITY_LICENSE` 密钥。在首次 CI 运行前添加到 GitHub 仓库
密钥。

### Unreal Engine
### Unreal Engine

Create `.github/workflows/tests.yml`:
创建 `.github/workflows/tests.yml`：

```yaml
name: Automated Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    name: Run UE Automation Tests
    runs-on: self-hosted  # UE requires a local runner with the editor installed

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          lfs: true

      - name: Run Automation Tests
        run: |
          "$UE_EDITOR_PATH" "${{ github.workspace }}/[ProjectName].uproject" \
            -nullrhi -nosound \
            -ExecCmds="Automation RunTests MyGame.; Quit" \
            -log -unattended
        shell: bash

      - name: Upload Logs
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-logs
          path: Saved/Logs/
```
```yaml
name: Automated Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    name: Run UE Automation Tests
    runs-on: self-hosted  # UE requires a local runner with the editor installed

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          lfs: true

      - name: Run Automation Tests
        run: |
          "$UE_EDITOR_PATH" "${{ github.workspace }}/[ProjectName].uproject" \
            -nullrhi -nosound \
            -ExecCmds="Automation RunTests MyGame.; Quit" \
            -log -unattended
        shell: bash

      - name: Upload Logs
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-logs
          path: Saved/Logs/
```

Note: UE CI requires a self-hosted runner with Unreal Editor installed.
Set the `UE_EDITOR_PATH` environment variable on the runner.
注意：UE CI 需要安装了 Unreal Editor 的自托管运行器。
在运行器上设置 `UE_EDITOR_PATH` 环境变量。

---

## Phase 5: Create Smoke Test Seed
## 阶段 5：创建冒烟测试种子

Create `tests/smoke/critical-paths.md`:
创建 `tests/smoke/critical-paths.md`：

```markdown
# Smoke Test: Critical Paths

**Purpose**: Run these 10-15 checks in under 15 minutes before any QA hand-off.
**Run via**: `/smoke-check` (which reads this file)
**Update**: Add new entries when new core systems are implemented.

## Core Stability (always run)

1. Game launches to main menu without crash
2. New game / session can be started from the main menu
3. Main menu responds to all inputs without freezing

## Core Mechanic (update per sprint)

<!-- Add the primary mechanic for each sprint here as it is implemented -->
<!-- Example: "Player can move, jump, and the camera follows correctly" -->
4. [Primary mechanic — update when first core system is implemented]

## Data Integrity

5. Save game completes without error (once save system is implemented)
6. Load game restores correct state (once load system is implemented)

## Performance

7. No visible frame rate drops on target hardware (60fps target)
8. No memory growth over 5 minutes of play (once core loop is implemented)
```
```markdown
# 冒烟测试：关键路径

**目的**：在任何 QA 交接前运行这 10-15 项检查，时间不超过 15 分钟。
**运行方式**：`/smoke-check`（读取此文件）
**更新**：实施新核心系统时添加新条目。

## 核心稳定性（始终运行）

1. 游戏启动到主菜单无崩溃
2. 可从主菜单开始新游戏/会话
3. 主菜单响应所有输入无冻结

## 核心机制（每个冲刺更新）

<!-- 在此为每个冲刺实施时添加主要机制 -->
<!-- 示例："玩家可移动、跳跃，且相机正确跟随" -->
4. [主要机制 —— 在首个核心系统实施时更新]

## 数据完整性

5. 存档游戏无错误完成（实施存档系统后）
6. 加载游戏恢复正确状态（实施读档系统后）

## 性能

7. 目标硬件上无可见帧率下降（60fps 目标）
8. 游玩 5 分钟内无内存增长（实施核心循环后）
```

---

## Phase 6: Post-Setup Summary
## 阶段 6：设置后摘要

After writing all files, report:
写入所有文件后，报告：

```
Test infrastructure created for [engine].

Files created:
- tests/README.md
- tests/unit/ (directory)
- tests/integration/ (directory)
- tests/smoke/critical-paths.md
- tests/evidence/ (directory)
[engine-specific files]
- .github/workflows/tests.yml

Next steps:
1. [Engine-specific install step, e.g., "Install GdUnit4 via AssetLib"]
2. Write your first test: create tests/unit/[first-system]/[system]_test.[ext]
3. Run `/qa-plan sprint` before your first sprint to classify stories and set
   test evidence requirements
4. `/smoke-check` before every QA hand-off

Gate note: /gate-check Technical Setup → Pre-Production now requires:
- tests/ directory with unit/ and integration/ subdirectories
- .github/workflows/tests.yml
- At least one example test file
Run /test-setup and write one example test before advancing.

Verdict: **COMPLETE** — test framework scaffolded and CI/CD wired up.
```
```
已为 [engine] 创建测试基础设施。

已创建文件：
- tests/README.md
- tests/unit/（目录）
- tests/integration/（目录）
- tests/smoke/critical-paths.md
- tests/evidence/（目录）
[引擎特定文件]
- .github/workflows/tests.yml

后续步骤：
1. [引擎特定安装步骤，例如"通过 AssetLib 安装 GdUnit4"]
2. 编写您的第一个测试：创建 tests/unit/[first-system]/[system]_test.[ext]
3. 在第一个冲刺前运行 `/qa-plan sprint` 以分类故事并设置
   测试证据要求
4. 每次 QA 交接前运行 `/smoke-check`

门禁说明：/gate-check 技术设置 → 预制作现在要求：
- 带有 unit/ 和 integration/ 子目录的 tests/ 目录
- .github/workflows/tests.yml
- 至少一个示例测试文件
在进入前运行 /test-setup 并编写一个示例测试。

裁定：**COMPLETE** —— 测试框架已脚手架且 CI/CD 已连接。
```

---

## Collaborative Protocol
## 协作协议

- **Never overwrite existing test files** — only create files that are missing.
  If a test runner file exists, leave it as-is.
- **绝不覆盖现有测试文件** —— 仅创建缺失的文件。
  若测试运行器文件存在，保持原样。
- **Always ask before creating files** — Phase 2 requires explicit approval.
- **创建文件前始终询问** —— 阶段 2 需要明确批准。
- **Engine detection is non-negotiable** — if the engine is not configured,
  stop and redirect to `/setup-engine`. Do not guess.
- **引擎检测不可妥协** —— 若引擎未配置，
  停止并重定向到 `/setup-engine`。不要猜测。
- **`force` flag skips the "already exists" early-exit but never overwrites.**
  It means "create any missing files even if the directory already exists."
- **`force` 标志跳过"已存在"提前退出但绝不覆盖。**
  它意味着"即使目录已存在也创建任何缺失的文件。"
- For Unity CI, note that the `UNITY_LICENSE` secret must be configured
  manually. Do not attempt to automate license management.
- 对于 Unity CI，注意 `UNITY_LICENSE` 密钥必须手动配置。不要尝试自动化许可管理。
