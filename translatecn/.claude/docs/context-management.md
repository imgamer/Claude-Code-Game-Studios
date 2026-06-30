# Context Management
# 上下文管理

Context is the most critical resource in a Claude Code session. Manage it actively.
上下文是 Claude Code 会话中最关键的资源。请主动管理。

## File-Backed State (Primary Strategy)
## 文件支撑状态（主要策略）

**The file is the memory, not the conversation.** Conversations are ephemeral and
will be compacted or lost. Files on disk persist across compactions and session crashes.
**文件即记忆，而非对话。** 对话是短暂的，
会被压缩或丢失。磁盘上的文件在压缩和会话崩溃后依然存在。

### Session State File
### 会话状态文件

Maintain `production/session-state/active.md` as a living checkpoint. Update it
after each significant milestone:
维护 `production/session-state/active.md` 作为活动检查点。在每个重要里程碑后更新：

- Design section approved and written to file
- 设计章节获批并写入文件
- Architecture decision made
- 做出架构决策
- Implementation milestone reached
- 达成实现里程碑
- Test results obtained
- 获得测试结果

The state file should contain: current task, progress checklist, key decisions
made, files being worked on, and open questions.
状态文件应包含：当前任务、进度清单、已做出的关键决策、正在处理的文件以及待解决问题。

### Status Line Block (Production+ only)
### 状态行块（仅 Production 及以上阶段）

When the project is in Production, Polish, or Release stage, include a structured
status block in `active.md` that the status line script can parse:
当项目处于 Production、Polish 或 Release 阶段时，在 `active.md` 中包含一个结构化状态块，供状态行脚本解析：

```markdown
<!-- STATUS -->
Epic: Combat System
Feature: Melee Combat
Task: Implement hitbox detection
<!-- /STATUS -->
```

- All three fields (Epic, Feature, Task) are optional — include only what applies
- 全部三个字段（Epic、Feature、Task）均可选 — 仅包含适用项
- Update this block when switching focus areas
- 切换关注区域时更新此块
- The status line displays it as a breadcrumb: `Combat System > Melee Combat > Hitboxes`
- 状态行将其显示为面包屑：`Combat System > Melee Combat > Hitboxes`
- Remove or empty the block when no active work focus exists
- 当不存在活动工作焦点时移除或清空此块

After any disruption (compaction, crash, `/clear`), read the state file first.
在任何中断后（压缩、崩溃、`/clear`），首先读取状态文件。

### Incremental File Writing
### 增量文件写入

When creating multi-section documents (design docs, architecture docs, lore entries):
创建多章节文档时（设计文档、架构文档、设定条目）：

1. Create the file immediately with a skeleton (all section headers, empty bodies)
1. 立即创建带骨架的文件（所有章节标题、空正文）
2. Discuss and draft one section at a time in conversation
2. 在对话中一次讨论并起草一个章节
3. Write each section to the file as soon as it's approved
3. 每个章节一经批准即写入文件
4. Update the session state file after each section
4. 每个章节后更新会话状态文件
5. After writing a section, previous discussion about that section can be safely
   compacted — the decisions are in the file
5. 写入一个章节后，关于该章节的先前讨论可安全
   压缩 — 决策已在文件中

This keeps the context window holding only the *current* section's discussion
(~3-5k tokens) instead of the entire document's conversation history (~30-50k tokens).
这样使上下文窗口仅持有*当前*章节的讨论
（约 3-5k token），而非整个文档的对话历史（约 30-50k token）。

## Proactive Compaction
## 主动压缩

- **Compact proactively** at ~60-70% context usage, not reactively at the limit
- **主动压缩**在约 60-70% 上下文使用率时进行，而非在达到上限时被动进行
- **Use `/clear`** between unrelated tasks, or after 2+ failed correction attempts
- **使用 `/clear`** 在不相关任务之间，或在 2 次以上纠正失败后
- **Natural compaction points:** after writing a section to file, after committing,
  after completing a task, before starting a new topic
- **自然压缩点**：将章节写入文件后、提交后、
  完成任务后、开始新主题前
- **Focused compaction:** `/compact Focus on [current task] — sections 1-3 are
  written to file, working on section 4`
- **聚焦压缩**：`/compact Focus on [current task] — sections 1-3 are
  written to file, working on section 4`

## Context Budgets by Task Type
## 按任务类型的上下文预算

- Light (read/review): ~3k tokens startup
- 轻量（读取/评审）：约 3k token 启动
- Medium (implement feature): ~8k tokens
- 中等（实现功能）：约 8k token
- Heavy (multi-system refactor): ~15k tokens
- 重度（多系统重构）：约 15k token

## Subagent Delegation
## 子智能体委派

Use subagents for research and exploration to keep the main session clean.
Subagents run in their own context window and return only summaries:
使用子智能体进行研究和探索，保持主会话整洁。
子智能体在各自的上下文窗口中运行，仅返回摘要：

- **Use subagents** when investigating across multiple files, exploring unfamiliar code,
  or doing research that would consume >5k tokens of file reads
- **使用子智能体**当跨多个文件调查、探索不熟悉的代码，
  或进行会消耗 >5k token 文件读取的研究时
- **Use direct reads** when you know exactly which 1-2 files to check
- **使用直接读取**当你确切知道要检查哪 1-2 个文件时
- Subagents do not inherit conversation history — provide full context in the prompt
- 子智能体不继承对话历史 — 在提示词中提供完整上下文

## Compaction Instructions
## 压缩说明

When context is compacted, preserve the following in the summary:
当上下文被压缩时，在摘要中保留以下内容：

- Reference to `production/session-state/active.md` (read it to recover state)
- 对 `production/session-state/active.md` 的引用（读取以恢复状态）
- List of files modified in this session and their purpose
- 本次会话中修改的文件列表及其用途
- Any architectural decisions made and their rationale
- 已做出的任何架构决策及其理由
- Active sprint tasks and their current status
- 活动冲刺任务及其当前状态
- Agent invocations and their outcomes (success/failure/blocked)
- 智能体调用及其结果（成功/失败/阻塞）
- Test results (pass/fail counts, specific failures)
- 测试结果（通过/失败计数、具体失败）
- Unresolved blockers or questions awaiting user input
- 未解决的阻塞或等待用户输入的问题
- The current task and what step we are on
- 当前任务以及我们处于哪个步骤
- Which sections of the current document are written to file vs. still in progress
- 当前文档哪些章节已写入文件、哪些仍在进行中

**After compaction:** Read `production/session-state/active.md` and any files being
actively worked on to recover full context. The files contain the decisions; the
conversation history is secondary.
**压缩后**：读取 `production/session-state/active.md` 和任何正在
处理的文件以恢复完整上下文。文件包含决策；对话
历史是次要的。

## Recovery After Session Crash
## 会话崩溃后的恢复

If a session dies ("prompt too long") or you start a new session to continue work:
如果会话终止（"prompt too long"）或你开启新会话以继续工作：

1. The `session-start.sh` hook will detect and preview `active.md` automatically
1. `session-start.sh` 钩子将自动检测并预览 `active.md`
2. Read the full state file for context
2. 读取完整状态文件以获取上下文
3. Read the partially-completed file(s) listed in the state
3. 读取状态中列出的部分完成文件
4. Continue from the next incomplete section or task
4. 从下一个未完成的章节或任务继续
