# Setup Requirements
# 安装要求

This template requires a few tools to be installed for full functionality.
All hooks fail gracefully if tools are missing — nothing will break, but
you'll lose validation features.
此模板需要安装一些工具以获得完整功能。
所有钩子在工具缺失时会优雅失败 — 不会出问题，但
你会失去验证功能。

## Required
## 必需工具

| Tool | Purpose | Install |
| 工具 | 用途 | 安装 |
| ---- | ---- | ---- |
| **Git** | Version control, branch management | [git-scm.com](https://git-scm.com/) |
| **Git** | 版本控制、分支管理 | [git-scm.com](https://git-scm.com/) |
| **Claude Code** | AI agent CLI | `npm install -g @anthropic-ai/claude-code` |
| **Claude Code** | AI 智能体 CLI | `npm install -g @anthropic-ai/claude-code` |

## Recommended
## 推荐工具

| Tool | Used By | Purpose | Install |
| 工具 | 使用方 | 用途 | 安装 |
| ---- | ---- | ---- | ---- |
| **jq** | Hooks (7 of 12) | JSON parsing in commit/push/asset/agent hooks | See below |
| **jq** | 钩子（12 个中的 7 个） | commit/push/asset/agent 钩子中的 JSON 解析 | 见下文 |
| **Python 3** | Hooks (2 of 12) | JSON validation for data files | [python.org](https://www.python.org/) |
| **Python 3** | 钩子（12 个中的 2 个） | 数据文件的 JSON 验证 | [python.org](https://www.python.org/) |
| **Bash** | All hooks | Shell script execution | Included with Git for Windows |
| **Bash** | 所有钩子 | Shell 脚本执行 | Git for Windows 自带 |

### Installing jq
### 安装 jq

**Windows** (any of these):
**Windows**（任选其一）：
```
winget install jqlang.jq
choco install jq
scoop install jq
```

**macOS**:
**macOS**：
```
brew install jq
```

**Linux**:
**Linux**：
```
sudo apt install jq     # Debian/Ubuntu
sudo dnf install jq     # Fedora
sudo pacman -S jq       # Arch
```

## Platform Notes
## 平台说明

### Windows
### Windows
- Git for Windows includes **Git Bash**, which provides the `bash` command
  used by all hooks in `settings.json`
- Git for Windows 包含 **Git Bash**，提供 `settings.json` 中
  所有钩子使用的 `bash` 命令
- Ensure Git Bash is on your PATH (default if installed via the Git installer)
- 确保 Git Bash 在你的 PATH 中（通过 Git 安装器安装时为默认）
- Hooks use `bash .claude/hooks/[name].sh` — this works on Windows because
  Claude Code invokes commands through a shell that can find `bash.exe`
- 钩子使用 `bash .claude/hooks/[name].sh` — 这在 Windows 上可行，因为
  Claude Code 通过能找到 `bash.exe` 的 shell 调用命令

### macOS / Linux
### macOS / Linux
- Bash is available natively
- Bash 原生可用
- Install `jq` via your package manager for full hook support
- 通过包管理器安装 `jq` 以获得完整钩子支持

## Verifying Your Setup
## 验证你的安装

Run these commands to check prerequisites:
运行以下命令检查前置条件：

```bash
git --version          # Should show git version
bash --version         # Should show bash version
jq --version           # Should show jq version (optional)
python3 --version      # Should show python version (optional)
```

## What Happens Without Optional Tools
## 没有可选工具会怎样

| Missing Tool | Effect |
| 缺失工具 | 影响 |
| ---- | ---- |
| **jq** | Commit validation, push protection, asset validation, and agent audit hooks silently skip their checks. Commits and pushes still work. |
| **jq** | 提交验证、推送保护、资源验证和智能体审计钩子静默跳过检查。提交和推送仍可工作。 |
| **Python 3** | JSON data file validation in commit and asset hooks is skipped. Invalid JSON can be committed without warning. |
| **Python 3** | 跳过提交和资源钩子中的 JSON 数据文件验证。无效 JSON 可在不报警告下提交。 |
| **Both** | All hooks still execute without error (exit 0) but provide no validation. You're flying without safety nets. |
| **两者都缺失** | 所有钩子仍无错误执行（exit 0）但不提供验证。你在没有安全网的情况下工作。 |

## Recommended IDE
## 推荐的 IDE

Claude Code works with any editor, but the template is optimized for:
Claude Code 可与任何编辑器配合使用，但本模板针对以下编辑器优化：
- **VS Code** with the Claude Code extension
- **VS Code** 搭配 Claude Code 扩展
- **Cursor** (Claude Code compatible)
- **Cursor**（兼容 Claude Code）
- Terminal-based Claude Code CLI
- 基于终端的 Claude Code CLI
