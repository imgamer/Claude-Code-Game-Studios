---
name: security-audit
description: "Audit the game for security vulnerabilities: save tampering, cheat vectors, network exploits, data exposure, and input validation gaps. Produces a prioritised security report with remediation guidance. Run before any public release or multiplayer launch."
argument-hint: "[full | network | save | input | quick]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, Task
model: sonnet
agent: security-engineer
---
---
name: security-audit
description: "审计游戏的安全漏洞：存档篡改、作弊向量、网络攻击、数据暴露和输入验证缺口。生成优先级排序的安全报告并附修复指导。在任何公开发布或多人上线前运行。"
argument-hint: "[full | network | save | input | quick]"
user-invocable: true
allowed-tools: Read, Glob, Grep, Bash, Write, Task
model: sonnet
agent: security-engineer
---

# Security Audit

Security is not optional for any shipped game. Even single-player games have
save tampering vectors. Multiplayer games have cheat surfaces, data exposure
risks, and denial-of-service potential. This skill systematically audits the
codebase for the most common game security failures and produces a prioritised
remediation plan.

**Run this skill:**
- Before any public release (required for the Polish → Release gate)
- Before enabling any online/multiplayer feature
- After implementing any system that reads from disk or network
- When a security-related bug is reported

**Output:** `production/security/security-audit-[date].md`

# 安全审计

安全对任何已发布游戏都不是可选的。即使是单机游戏也有
存档篡改向量。多人游戏有作弊面、数据暴露
风险和拒绝服务潜力。此技能系统审计
代码库中最常见的游戏安全失败并生成优先级排序的
修复计划。

**运行此技能：**
- 在任何公开发布前（打磨 → 发布门禁要求）
- 在启用任何在线/多人功能前
- 在实现任何从磁盘或网络读取的系统后
- 当报告安全相关 Bug 时

**输出：** `production/security/security-audit-[date].md`

---

## Phase 1: Parse Arguments and Scope

**Modes:**
- `full` — all categories (recommended before release)
- `network` — network/multiplayer only
- `save` — save file and serialization only
- `input` — input validation and injection only
- `quick` — high-severity checks only (fastest, for iterative use)
- No argument — run `full`

Read `.claude/docs/technical-preferences.md` to determine:
- Engine and language (affects which patterns to search for)
- Target platforms (affects which attack surfaces apply)
- Whether multiplayer/networking is in scope

## 第 1 阶段：解析参数和范围

**模式：**
- `full` —— 所有类别（发布前推荐）
- `network` —— 仅网络/多人
- `save` —— 仅存档文件和序列化
- `input` —— 仅输入验证和注入
- `quick` —— 仅高严重性检查（最快，用于迭代）
- 无参数 —— 运行 `full`

读取 `.claude/docs/technical-preferences.md` 以确定：
- 引擎和语言（影响要搜索哪些模式）
- 目标平台（影响应用哪些攻击面）
- 多人/网络是否在范围内

---

## Phase 2: Spawn Security Engineer

Spawn `security-engineer` via Task. Pass:
- The audit scope/mode
- Engine and language from technical preferences
- A manifest of all source directories: `src/`, `assets/data/`, any config files

The security-engineer runs the audit across 6 categories (see Phase 3). Collect their full findings before proceeding.

## 第 2 阶段：派生安全工程师

通过 Task 派生 `security-engineer`。传递：
- 审计范围/模式
- 来自技术偏好的引擎和语言
- 所有源目录的清单：`src/`、`assets/data/`、任何配置文件

安全工程师跨 6 个类别运行审计（见第 3 阶段）。继续前收集其完整发现。

---

## Phase 3: Audit Categories

The security-engineer evaluates each of the following. Skip categories not applicable to the project scope.

### Category 1: Save File and Serialization Security
- Are save files validated before loading? (no blind deserialization)
- Are save file paths constructed from user input? (path traversal risk)
- Are save files checksummed or signed? (tamper detection)
- Does the game trust numeric values from save files without bounds checking?
- Are there any eval() or dynamic code execution calls near save loading?

Grep patterns: `File.open`, `load`, `deserialize`, `JSON.parse`, `from_json`, `read_file` — check each for validation.

### Category 2: Network and Multiplayer Security (skip if single-player only)
- Is game state authoritative on the server, or does the client dictate outcomes?
- Are incoming network packets validated for size, type, and value range?
- Are player positions and state changes validated server-side?
- Is there rate limiting on any network calls?
- Are authentication tokens handled correctly (never sent in plaintext)?
- Does the game expose any debug endpoints in release builds?

Grep for: `recv`, `receive`, `PacketPeer`, `socket`, `NetworkedMultiplayerPeer`, `rpc`, `rpc_id` — check each call site for validation.

### Category 3: Input Validation
- Are any player-supplied strings used in file paths? (path traversal)
- Are any player-supplied strings logged without sanitization? (log injection)
- Are numeric inputs (e.g., item quantities, character stats) bounds-checked before use?
- Are achievement/stat values checked before being written to any backend?

Grep for: `get_input`, `Input.get_`, `input_map`, user-facing text fields — check validation.

### Category 4: Data Exposure
- Are any API keys, credentials, or secrets hardcoded in `src/` or `assets/`?
- Are debug symbols or verbose error messages included in release builds?
- Does the game log sensitive player data to disk or console?
- Are any internal file paths or system information exposed to players?

Grep for: `api_key`, `secret`, `password`, `token`, `private_key`, `DEBUG`, `print(` in release-facing code.

### Category 5: Cheat and Anti-Tamper Vectors
- Are gameplay-critical values stored only in memory, not in easily-editable files?
- Are any critical game progression flags (e.g., "has paid for DLC") validated server-side?
- Is there any protection against memory editing tools (Cheat Engine, etc.) for multiplayer?
- Are leaderboard/score submissions validated before acceptance?

Note: Client-side anti-cheat is largely unenforceable. Focus on server-side validation for anything competitive or monetised.

### Category 6: Dependency and Supply Chain
- Are any third-party plugins or libraries used? List them.
- Do any plugins have known CVEs in the version being used?
- Are plugin sources verified (official marketplace, reviewed repository)?

Glob for: `addons/`, `plugins/`, `third_party/`, `vendor/` — list all external dependencies.

## 第 3 阶段：审计类别

安全工程师评估以下每一项。跳过不适用于项目范围的类别。

### 类别 1：存档文件和序列化安全
- 存档文件加载前是否验证？（无盲反序列化）
- 存档文件路径是否由用户输入构造？（路径遍历风险）
- 存档文件是否校验和或签名？（篡改检测）
- 游戏是否信任存档文件中的数值而不做边界检查？
- 加载存档附近是否有 eval() 或动态代码执行调用？

Grep 模式：`File.open`、`load`、`deserialize`、`JSON.parse`、`from_json`、`read_file` —— 检查每个的验证。

### 类别 2：网络和多人安全（如仅单机则跳过）
- 游戏状态是否在服务器端权威，还是客户端决定结果？
- 传入网络包是否验证大小、类型和值范围？
- 玩家位置和状态变更是否在服务器端验证？
- 任何网络调用是否有限速？
- 认证令牌是否正确处理（从不以明文发送）？
- 游戏在发布构建中是否暴露任何调试端点？

Grep：`recv`、`receive`、`PacketPeer`、`socket`、`NetworkedMultiplayerPeer`、`rpc`、`rpc_id` —— 检查每个调用点的验证。

### 类别 3：输入验证
- 任何玩家提供的字符串是否用于文件路径？（路径遍历）
- 任何玩家提供的字符串是否未净化即记录？（日志注入）
- 数值输入（例如物品数量、角色属性）使用前是否边界检查？
- 成就/统计值写入任何后端前是否检查？

Grep：`get_input`、`Input.get_`、`input_map`、面向用户的文本字段 —— 检查验证。

### 类别 4：数据暴露
- `src/` 或 `assets/` 中是否有硬编码的 API 密钥、凭证或密钥？
- 发布构建是否包含调试符号或详细错误信息？
- 游戏是否将敏感玩家数据记录到磁盘或控制台？
- 任何内部文件路径或系统信息是否暴露给玩家？

Grep：`api_key`、`secret`、`password`、`token`、`private_key`、`DEBUG`、面向发布代码中的 `print(`。

### 类别 5：作弊和防篡改向量
- 游戏关键数值是否仅存储在内存中，而非易编辑的文件中？
- 任何关键游戏进度标记（例如"已付费 DLC"）是否在服务器端验证？
- 多人游戏是否有任何针对内存编辑工具（Cheat Engine 等）的保护？
- 排行榜/分数提交是否在接受前验证？

注意：客户端反作弊很大程度上无法强制执行。专注于任何竞争性或货币化内容的服务器端验证。

### 类别 6：依赖和供应链
- 是否使用任何第三方插件或库？列出它们。
- 任何插件在使用版本中是否有已知 CVE？
- 插件来源是否验证（官方市场、已审查的仓库）？

Glob：`addons/`、`plugins/`、`third_party/`、`vendor/` —— 列出所有外部依赖。

---

## Phase 4: Classify Findings

For each finding, assign:

**Severity:**
| Level | Definition |
|-------|-----------|
| **CRITICAL** | Remote code execution, data breach, or trivially-exploitable cheat that breaks multiplayer integrity |
| **HIGH** | Save tampering that bypasses progression, credential exposure, or server-side authority bypass |
| **MEDIUM** | Client-side cheat enablement, information disclosure, or input validation gap with limited impact |
| **LOW** | Defence-in-depth improvement — hardening that reduces attack surface but no direct exploit exists |

**Status:** Open / Accepted Risk / Out of Scope

## 第 4 阶段：分类发现

对每个发现，分配：

**严重性：**
| 级别 | 定义 |
|-------|-----------|
| **CRITICAL** | 远程代码执行、数据泄露，或轻易可利用的破坏多人完整性的作弊 |
| **HIGH** | 绕过进度的存档篡改、凭证暴露，或服务器端权威绕过 |
| **MEDIUM** | 客户端作弊启用、信息泄露，或影响有限的输入验证缺口 |
| **LOW** | 纵深防御改进 —— 减少攻击面但无直接可利用漏洞的加固 |

**状态：** Open / Accepted Risk / Out of Scope（开放 / 接受风险 / 超出范围）

---

## Phase 5: Generate Report

```markdown
# Security Audit Report

**Date**: [date]
**Scope**: [full | network | save | input | quick]
**Engine**: [engine + version]
**Audited by**: security-engineer via /security-audit
**Files scanned**: [N source files, N config files]

---

## Executive Summary

| Severity | Count | Must Fix Before Release |
|----------|-------|------------------------|
| CRITICAL | [N] | Yes — all |
| HIGH | [N] | Yes — all |
| MEDIUM | [N] | Recommended |
| LOW | [N] | Optional |

**Release recommendation**: [CLEAR TO SHIP / FIX CRITICALS FIRST / DO NOT SHIP]

---

## CRITICAL Findings

### SEC-001: [Title]
**Category**: [Save / Network / Input / Data / Cheat / Dependency]
**File**: `[path]` line [N]
**Description**: [What the vulnerability is]
**Attack scenario**: [How a malicious user would exploit it]
**Remediation**: [Specific code change or pattern to apply]
**Effort**: [Low / Medium / High]

[repeat per finding]

---

## HIGH Findings

[same format]

---

## MEDIUM Findings

[same format]

---

## LOW Findings

[same format]

---

## Accepted Risk

[Any findings explicitly accepted by the team with rationale]

---

## Dependency Inventory

| Plugin / Library | Version | Source | Known CVEs |
|-----------------|---------|--------|------------|
| [name] | [version] | [source] | [none / CVE-XXXX-NNNN] |

---

## Remediation Priority Order

1. [SEC-NNN] — [1-line description] — Est. effort: [Low/Medium/High]
2. ...

---

## Re-Audit Trigger

Run `/security-audit` again after remediating any CRITICAL or HIGH findings.
The Polish → Release gate requires this report with no open CRITICAL or HIGH items.
```

## 第 5 阶段：生成报告

```markdown
# Security Audit Report

**Date**: [date]
**Scope**: [full | network | save | input | quick]
**Engine**: [engine + version]
**Audited by**: security-engineer via /security-audit
**Files scanned**: [N source files, N config files]

---

## Executive Summary

| Severity | Count | Must Fix Before Release |
|----------|-------|------------------------|
| CRITICAL | [N] | Yes — all |
| HIGH | [N] | Yes — all |
| MEDIUM | [N] | Recommended |
| LOW | [N] | Optional |

**Release recommendation**: [CLEAR TO SHIP / FIX CRITICALS FIRST / DO NOT SHIP]

---

## CRITICAL Findings

### SEC-001: [Title]
**Category**: [Save / Network / Input / Data / Cheat / Dependency]
**File**: `[path]` line [N]
**Description**: [What the vulnerability is]
**Attack scenario**: [How a malicious user would exploit it]
**Remediation**: [Specific code change or pattern to apply]
**Effort**: [Low / Medium / High]

[repeat per finding]

---

## HIGH Findings

[same format]

---

## MEDIUM Findings

[same format]

---

## LOW Findings

[same format]

---

## Accepted Risk

[Any findings explicitly accepted by the team with rationale]

---

## Dependency Inventory

| Plugin / Library | Version | Source | Known CVEs |
|-----------------|---------|--------|------------|
| [name] | [version] | [source] | [none / CVE-XXXX-NNNN] |

---

## Remediation Priority Order

1. [SEC-NNN] — [1-line description] — Est. effort: [Low/Medium/High]
2. ...

---

## Re-Audit Trigger

Run `/security-audit` again after remediating any CRITICAL or HIGH findings.
The Polish → Release gate requires this report with no open CRITICAL or HIGH items.
```

---

## Phase 6: Write Report

Present the report summary (executive summary + CRITICAL/HIGH findings only) in conversation.

Ask: "May I write the full security audit report to `production/security/security-audit-[date].md`?"

Write only after approval.

## 第 6 阶段：写入报告

在对话中呈现报告摘要（执行摘要 + 仅 CRITICAL/HIGH 发现）。

询问："可以将完整安全审计报告写入 `production/security/security-audit-[date].md` 吗？"

仅在批准后写入。

---

## Phase 7: Gate Integration

This report is a required artifact for the **Polish → Release gate**.

After remediating findings, re-run: `/security-audit quick` to confirm CRITICAL/HIGH items are resolved before running `/gate-check release`.

If CRITICAL findings exist:
> "⛔ CRITICAL security findings must be resolved before any public release. Do not proceed to `/launch-checklist` until these are addressed."

If no CRITICAL/HIGH findings:
> "✅ No blocking security findings. Report written to `production/security/`. Include this path when running `/gate-check release`."

## 第 7 阶段：门禁集成

此报告是 **打磨 → 发布门禁** 的必需产出物。

修复发现后，重新运行：`/security-audit quick` 以在运行 `/gate-check release` 前确认 CRITICAL/HIGH 项已解决。

如存在 CRITICAL 发现：
> "⛔ CRITICAL 安全发现必须在任何公开发布前解决。在这些问题解决前不要进行 `/launch-checklist`。"

如无 CRITICAL/HIGH 发现：
> "✅ 无阻塞性安全发现。报告已写入 `production/security/`。运行 `/gate-check release` 时包含此路径。"

---

## Collaborative Protocol

- **Never assume a pattern is safe** — flag it and let the user decide
- **Accepted risk is a valid outcome** — some LOW findings are acceptable trade-offs for a solo team; document the decision
- **Multiplayer games have a higher bar** — any HIGH finding in a multiplayer context should be treated as CRITICAL
- **This is not a penetration test** — this audit covers common patterns; a real pentest by a human security professional is recommended before any competitive or monetised multiplayer launch

## 协作协议

- **永远不要假设模式是安全的** —— 标记它并让用户决定
- **接受风险是有效的结果** —— 对独立团队而言某些 LOW 发现是可接受的权衡；记录决策
- **多人游戏有更高门槛** —— 多人上下文中的任何 HIGH 发现已作为 CRITICAL 处理
- **这不是渗透测试** —— 此审计覆盖常见模式；在任何竞争性或货币化多人上线前建议由人类安全专业人员进行真实渗透测试
