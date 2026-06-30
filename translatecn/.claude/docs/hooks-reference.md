# Active Hooks
# 活动钩子

Hooks are configured in `.claude/settings.json` and fire automatically:
钩子配置在 `.claude/settings.json` 中，并会自动触发：

| Hook | Event | Trigger | Action |
| 钩子 | 事件 | 触发条件 | 动作 |
| ---- | ----- | ------- | ------ |
| `validate-commit.sh` | PreToolUse (Bash) | `git commit` commands | Validates design doc sections, JSON data files, hardcoded values, TODO format |
| `validate-commit.sh` | PreToolUse (Bash) | `git commit` 命令 | 验证设计文档章节、JSON 数据文件、硬编码值、TODO 格式 |
| `validate-push.sh` | PreToolUse (Bash) | `git push` commands | Warns on pushes to protected branches (develop/main) |
| `validate-push.sh` | PreToolUse (Bash) | `git push` 命令 | 在推送到受保护分支（develop/main）时发出警告 |
| `validate-assets.sh` | PostToolUse (Write/Edit) | Asset file changes | Checks naming conventions and JSON validity for files in `assets/` |
| `validate-assets.sh` | PostToolUse (Write/Edit) | 资源文件变更 | 检查 `assets/` 目录下文件的命名约定和 JSON 有效性 |
| `session-start.sh` | SessionStart | Session begins | Loads sprint context, milestone, git activity; detects and previews active session state file for recovery |
| `session-start.sh` | SessionStart | 会话开始 | 加载冲刺上下文、里程碑、git 活动；检测并预览活动会话状态文件以进行恢复 |
| `detect-gaps.sh` | SessionStart | Session begins | Detects fresh projects (suggests /start) and missing documentation when code/prototypes exist, suggests /reverse-document or /project-stage-detect |
| `detect-gaps.sh` | SessionStart | 会话开始 | 检测全新项目（建议使用 /start），当代码/原型存在但文档缺失时建议使用 /reverse-document 或 /project-stage-detect |
| `pre-compact.sh` | PreCompact | Context compression | Dumps session state (active.md, modified files, WIP design docs) into conversation before compaction so it survives summarization |
| `pre-compact.sh` | PreCompact | 上下文压缩 | 在压缩前将会话状态（active.md、已修改的文件、进行中的设计文档）转储到对话中，以便在摘要化后保留下来 |
| `post-compact.sh` | PostCompact | After compaction | Reminds Claude to restore session state from `active.md` checkpoint |
| `post-compact.sh` | PostCompact | 压缩后 | 提醒 Claude 从 `active.md` 检查点恢复会话状态 |
| `notify.sh` | Notification | Notification event | Shows Windows toast notification via PowerShell |
| `notify.sh` | Notification | 通知事件 | 通过 PowerShell 显示 Windows 通知 |
| `session-stop.sh` | Stop | Session ends | Summarizes accomplishments and updates session log |
| `session-stop.sh` | Stop | 会话结束 | 总结完成的工作并更新会话日志 |
| `log-agent.sh` | SubagentStart | Agent spawned | Audit trail start — logs subagent invocation with timestamp |
| `log-agent.sh` | SubagentStart | 智能体生成 | 审计跟踪开始 — 记录子智能体调用及时间戳 |
| `log-agent-stop.sh` | SubagentStop | Agent stops | Audit trail stop — completes subagent record |
| `log-agent-stop.sh` | SubagentStop | 智能体停止 | 审计跟踪结束 — 完成子智能体记录 |
| `validate-skill-change.sh` | PostToolUse (Write/Edit) | Skill file changes | Advises running `/skill-test` after any `.claude/skills/` file is written or edited |
| `validate-skill-change.sh` | PostToolUse (Write/Edit) | 技能文件变更 | 在任何 `.claude/skills/` 文件被写入或编辑后建议运行 `/skill-test` |

Hook reference documentation: `.claude/docs/hooks-reference/`
钩子参考文档：`.claude/docs/hooks-reference/`
Hook input schema documentation: `.claude/docs/hooks-reference/hook-input-schemas.md`
钩子输入模式文档：`.claude/docs/hooks-reference/hook-input-schemas.md`
