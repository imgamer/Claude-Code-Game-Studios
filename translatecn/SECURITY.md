# Security Policy
# 安全策略

## Supported Versions
## 支持的版本

Only the `main` branch receives security fixes. Forks and older releases are
not supported.
只有 `main` 分支会接收安全修复。派生仓库和旧版本不受支持。

## Reporting a Vulnerability
## 报告漏洞

**Do not report security vulnerabilities through public GitHub issues.**
**不要通过公开的 GitHub issues 报告安全漏洞。**

Use GitHub's private vulnerability reporting instead:
请改用 GitHub 的私密漏洞报告功能：

**[Report a vulnerability →](https://github.com/Donchitos/Claude-Code-Game-Studios/security/advisories/new)**
**[报告漏洞 →](https://github.com/Donchitos/Claude-Code-Game-Studios/security/advisories/new)**

Include as much detail as possible:
请尽可能提供详细信息：

- Description of the vulnerability and what it affects
- 漏洞描述及其影响的范围

- Steps to reproduce
- 复现步骤

- Potential impact and attack scenarios
- 潜在影响和攻击场景

- Any suggested mitigations
- 任何建议的缓解措施

**What to expect:**
**预期流程：**

- Acknowledgment within **48 hours**
- 在 **48 小时**内确认收到

- Status update within **7 days**
- 在 **7 天**内提供状态更新

- Resolution within **90 days** for confirmed vulnerabilities
- 确认的漏洞在 **90 天**内修复

## What Is In Scope
## 范围说明

CCGS is a **local development tool** — it installs shell hooks and coordinates
AI agents that run directly on your machine. Security issues are primarily about
contributed code that executes in users' environments without their awareness.
CCGS 是一个**本地开发工具**——它会安装 shell 钩子并协调直接在你机器上运行的
AI 智能体。安全问题主要涉及在用户毫无察觉的情况下于其环境中执行的贡献代码。

### High Severity
### 高严重性

- Hooks (`.claude/hooks/*.sh`) that execute malicious or undisclosed shell
  commands on user machines
- 在用户机器上执行恶意或未声明的 shell 命令的钩子（`.claude/hooks/*.sh`）

- Skills or agents that exfiltrate environment variables, API keys, or secrets
- 泄露环境变量、API 密钥或机密信息的技能或智能体

- Prompt injection via skill or agent definitions that causes Claude to bypass
  safety measures or take unauthorized destructive actions
- 通过技能或智能体定义进行的提示注入，导致 Claude 绕过安全措施或采取未经授权的破坏性操作

- Contributions that silently alter behavior in ways users cannot audit
- 以用户无法审计的方式静默改变行为的贡献

### Medium Severity
### 中严重性

- Skills that make undisclosed outbound network requests
- 进行未声明的出站网络请求的技能

- Agent definitions that escalate permissions or bypass user confirmation prompts
- 提升权限或绕过用户确认提示的智能体定义

- Hook patterns that behave differently across platforms to conceal behavior
- 在不同平台上表现不一致以掩盖行为的钩子模式

- Skills that write outside their documented scope without an explicit user
  approval step
- 在没有明确用户批准步骤的情况下，写入其文档化范围之外的技能

### Out of Scope
### 不在范围内

- The behavior of Claude or the Claude Code CLI itself
  (report to [Anthropic](https://www.anthropic.com/security))
- Claude 或 Claude Code CLI 本身的行为
  （请向 [Anthropic](https://www.anthropic.com/security) 报告）

- Bugs in the user's Claude Code installation or editor extension
- 用户 Claude Code 安装或编辑器扩展中的错误

- Theoretical vulnerabilities with no realistic attack path
- 没有现实攻击路径的理论性漏洞

- Issues requiring physical access to the user's machine
- 需要物理访问用户机器才能利用的问题

## Security Guidelines for Contributors
## 贡献者安全指南

When contributing hooks, skills, or agents:
在贡献钩子、技能或智能体时：

- **Hooks must be POSIX-compatible** — use `grep -E`, not `grep -P`; avoid
  platform-specific syntax that behaves differently across operating systems
- **钩子必须兼容 POSIX** —— 使用 `grep -E`，不要用 `grep -P`；避免使用在不同操作系统上表现不一致的平台特定语法

- **No silent network calls** from hooks or skills unless explicitly documented
  and opt-in by the user
- **钩子或技能不得进行静默网络调用**，除非已明确文档化并由用户主动选择启用

- **No reading secrets or environment variables** beyond what is minimally
  required and clearly documented in the skill's header
- **不得读取机密信息或环境变量**，仅可读取最低限度所需且已在技能头部明确文档化的内容

- **Skills must not write outside their documented scope** without an explicit
  user confirmation step
- **技能不得写入其文档化范围之外**，除非有明确的用户确认步骤

## Disclosure Policy
## 披露策略

We follow a **90-day coordinated disclosure** timeline:
我们遵循 **90 天协调披露**时间表：

1. You submit the vulnerability privately
1. 你私下提交漏洞

2. We acknowledge within 48 hours
2. 我们在 48 小时内确认收到

3. We confirm and assess severity within 7 days
3. 我们在 7 天内确认并评估严重性

4. We develop and test a fix
4. 我们开发并测试修复方案

5. We notify you before any public disclosure
5. 我们在任何公开披露之前通知你

6. Public disclosure happens after the fix ships, or at 90 days — whichever
   comes first
6. 公开披露在修复发布后或满 90 天时进行——以先到者为准

We credit reporters in release notes unless you prefer to remain anonymous.
除非你希望保持匿名，否则我们会在发布说明中致谢报告者。
