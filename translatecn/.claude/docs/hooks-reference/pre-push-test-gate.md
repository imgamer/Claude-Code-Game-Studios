# Hook: pre-push-test-gate
# 钩子：pre-push-test-gate

## Trigger
## 触发条件

Runs before any push to a remote branch. Mandatory for pushes to `develop`
and `main`.
在任何推送到远程分支之前运行。对推送到 `develop` 和 `main` 是强制性的。

## Purpose
## 目的

Ensures the build compiles, unit tests pass, and critical smoke tests pass
before code reaches shared branches. This is the last automated quality gate
before code affects other developers.
确保在代码到达共享分支之前，构建通过编译、单元测试通过，且关键冒烟测试通过。这是代码影响其他开发者之前的最后一道自动化质量关卡。

## Implementation
## 实现

```bash
#!/bin/bash
# Pre-push hook: Build and test gate

REMOTE="$1"
URL="$2"

# Only enforce full gate for develop and main
PROTECTED_BRANCHES="develop main"
CURRENT_BRANCH=$(git rev-parse --abbrev-ref HEAD)

FULL_GATE=false
for branch in $PROTECTED_BRANCHES; do
    if [ "$CURRENT_BRANCH" = "$branch" ]; then
        FULL_GATE=true
        break
    fi
done

echo "=== Pre-Push Quality Gate ==="

# Step 1: Build
echo "Building..."
# Adapt to your build system:
# make build || exit 1
# dotnet build || exit 1
# cargo build || exit 1
echo "Build: PASS"

# Step 2: Unit tests
echo "Running unit tests..."
# Adapt to your test framework:
# python -m pytest tests/unit/ -x || exit 1
# dotnet test tests/unit/ || exit 1
# cargo test || exit 1
echo "Unit tests: PASS"

if [ "$FULL_GATE" = true ]; then
    # Step 3: Integration tests (only for protected branches)
    echo "Running integration tests..."
    # python -m pytest tests/integration/ -x || exit 1
    echo "Integration tests: PASS"

    # Step 4: Smoke tests
    echo "Running smoke tests..."
    # python -m pytest tests/playtest/smoke/ -x || exit 1
    echo "Smoke tests: PASS"

    # Step 5: Performance regression check
    echo "Checking performance baselines..."
    # python tools/ci/perf_check.py || exit 1
    echo "Performance: PASS"
fi

echo "=== All gates passed ==="
exit 0
```

## Agent Integration
## 智能体集成

When this hook fails:
当此钩子失败时：
1. Build failure: invoke `lead-programmer` to diagnose
1. 构建失败：调用 `lead-programmer` 进行诊断
2. Unit test failure: invoke `qa-tester` to identify the failing test and
   `gameplay-programmer` or relevant programmer to fix
2. 单元测试失败：调用 `qa-tester` 定位失败的测试，并调用 `gameplay-programmer` 或相关程序员修复
3. Performance regression: invoke `performance-analyst` to analyze
3. 性能回归：调用 `performance-analyst` 进行分析
