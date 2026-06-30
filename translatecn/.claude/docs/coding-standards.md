# Coding Standards
# 编码标准

- All game code must include doc comments on public APIs
- 所有游戏代码必须在公共 API 上包含文档注释
- Every system must have a corresponding architecture decision record in `docs/architecture/`
- 每个系统必须在 `docs/architecture/` 中有对应的架构决策记录
- Gameplay values must be data-driven (external config), never hardcoded
- 游戏玩法数值必须是数据驱动（外部配置），绝不硬编码
- All public methods must be unit-testable (dependency injection over singletons)
- 所有公共方法必须可单元测试（依赖注入优于单例）
- Commits must reference the relevant design document or task ID
- 提交必须引用相关设计文档或任务 ID
- **Commit messages**: Use Conventional Commits format — `feat:`, `fix:`, `chore:`, `docs:`, `test:`, `refactor:`. Reference the story or task ID in the body (e.g., `Story: EPIC-001-S02`).
- **提交信息**：使用 Conventional Commits 格式 — `feat:`、`fix:`、`chore:`、`docs:`、`test:`、`refactor:`。在正文中引用 story 或任务 ID（例如 `Story: EPIC-001-S02`）。
- **Verification-driven development**: Write tests first when adding gameplay systems.
  For UI changes, verify with screenshots. Compare expected output to actual output
  before marking work complete. Every implementation should have a way to prove it works.
- **验证驱动开发**：添加游戏玩法系统时先写测试。
  UI 变更用截图验证。在标记工作完成前，将预期输出与实际输出进行对比。
  每个实现都应有方法证明其有效。

# Design Document Standards
# 设计文档标准

- All design docs use Markdown
- 所有设计文档使用 Markdown
- Each mechanic has a dedicated document in `design/gdd/`
- 每个机制在 `design/gdd/` 中有专门文档
- Documents must include these 8 required sections:
- 文档必须包含以下 8 个必需章节：
  1. **Overview** -- one-paragraph summary
  1. **Overview** — 一段摘要
  2. **Player Fantasy** -- intended feeling and experience
  2. **Player Fantasy** — 预期的感觉和体验
  3. **Detailed Rules** -- unambiguous mechanics
  3. **Detailed Rules** — 明确无歧义的机制
  4. **Formulas** -- all math defined with variables
  4. **Formulas** — 所有数学公式用变量定义
  5. **Edge Cases** -- unusual situations handled
  5. **Edge Cases** — 处理的特殊情况
  6. **Dependencies** -- other systems listed
  6. **Dependencies** — 列出其他系统
  7. **Tuning Knobs** -- configurable values identified
  7. **Tuning Knobs** — 标识可配置值
  8. **Acceptance Criteria** -- testable success conditions
  8. **Acceptance Criteria** — 可测试的成功条件
- Balance values must link to their source formula or rationale
- 平衡数值必须链接到其源公式或理由

# Testing Standards
# 测试标准

## Test Evidence by Story Type
## 按 story 类型的测试证据

All stories must have appropriate test evidence before they can be marked Done:
所有 story 在标记为 Done 前必须有适当的测试证据：

| Story Type | Required Evidence | Location | Gate Level |
| story 类型 | 必需证据 | 位置 | 门禁级别 |
|---|---|---|---|
| **Logic** (formulas, AI, state machines) | Automated unit test — must pass | `tests/unit/[system]/` | BLOCKING |
| **Logic**（公式、AI、状态机） | 自动化单元测试 — 必须通过 | `tests/unit/[system]/` | BLOCKING |
| **Integration** (multi-system) | Integration test OR documented playtest | `tests/integration/[system]/` | BLOCKING |
| **Integration**（多系统） | 集成测试或有记录的试玩 | `tests/integration/[system]/` | BLOCKING |
| **Visual/Feel** (animation, VFX, feel) | Screenshot + lead sign-off | `production/qa/evidence/` | ADVISORY |
| **Visual/Feel**（动画、VFX、手感） | 截图 + 主管签字 | `production/qa/evidence/` | ADVISORY |
| **UI** (menus, HUD, screens) | Manual walkthrough doc OR interaction test | `production/qa/evidence/` | ADVISORY |
| **UI**（菜单、HUD、屏幕） | 手动走查文档或交互测试 | `production/qa/evidence/` | ADVISORY |
| **Config/Data** (balance tuning) | Smoke check pass | `production/qa/smoke-[date].md` | ADVISORY |
| **Config/Data**（平衡调参） | 冒烟测试通过 | `production/qa/smoke-[date].md` | ADVISORY |

## Automated Test Rules
## 自动化测试规则

- **Naming**: `[system]_[feature]_test.[ext]` for files; `test_[scenario]_[expected]` for functions
- **命名**：文件用 `[system]_[feature]_test.[ext]`；函数用 `test_[scenario]_[expected]`
- **Determinism**: Tests must produce the same result every run — no random seeds, no time-dependent assertions
- **确定性**：测试每次运行必须产生相同结果 — 无随机种子、无时间相关断言
- **Isolation**: Each test sets up and tears down its own state; tests must not depend on execution order
- **隔离性**：每个测试建立并清理自己的状态；测试不得依赖执行顺序
- **No hardcoded data**: Test fixtures use constant files or factory functions, not inline magic numbers
  (exception: boundary value tests where the exact number IS the point)
- **无硬编码数据**：测试夹具使用常量文件或工厂函数，而非内联魔法数字
  （例外：边界值测试中具体数字本身就是测试目的）
- **Independence**: Unit tests do not call external APIs, databases, or file I/O — use dependency injection
- **独立性**：单元测试不调用外部 API、数据库或文件 I/O — 使用依赖注入

## What NOT to Automate
## 不应自动化的内容

- Visual fidelity (shader output, VFX appearance, animation curves)
- 视觉保真度（着色器输出、VFX 外观、动画曲线）
- "Feel" qualities (input responsiveness, perceived weight, timing)
- "手感"特性（输入响应、感知重量、时序）
- Platform-specific rendering (test on target hardware, not headlessly)
- 平台特定渲染（在目标硬件上测试，而非无头模式）
- Full gameplay sessions (covered by playtesting, not automation)
- 完整游戏会话（由试玩覆盖，而非自动化）

## CI/CD Rules
## CI/CD 规则

- Automated test suite runs on every push to main and every PR
- 自动化测试套件在每次推送到 main 和每个 PR 时运行
- No merge if tests fail — tests are a blocking gate in CI
- 测试失败则禁止合并 — 测试是 CI 中的阻塞性门禁
- Never disable or skip failing tests to make CI pass — fix the underlying issue
- 绝不通过禁用或跳过失败测试来让 CI 通过 — 修复根本问题
- Engine-specific CI commands:
- 引擎特定的 CI 命令：
  - **Godot**: `godot --headless --script tests/gdunit4_runner.gd`
  - **Godot**: `godot --headless --script tests/gdunit4_runner.gd`
  - **Unity**: `game-ci/unity-test-runner@v4` (GitHub Actions)
  - **Unity**: `game-ci/unity-test-runner@v4`（GitHub Actions）
  - **Unreal**: headless runner with `-nullrhi` flag
  - **Unreal**: 带 `-nullrhi` 标志的无头运行器
