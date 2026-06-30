# Path-Specific Rules
# 路径特定规则

Rules in `.claude/rules/` are automatically enforced when editing files in matching paths:
`.claude/rules/` 中的规则会在编辑匹配路径中的文件时自动执行：

| Rule File | Path Pattern | Enforces |
| 规则文件 | 路径模式 | 强制内容 |
| ---- | ---- | ---- |
| `gameplay-code.md` | `src/gameplay/**` | Data-driven values, delta time, no UI references |
| `gameplay-code.md` | `src/gameplay/**` | 数据驱动值、delta time、无 UI 引用 |
| `engine-code.md` | `src/core/**` | Zero allocs in hot paths, thread safety, API stability |
| `engine-code.md` | `src/core/**` | 热点路径零分配、线程安全、API 稳定性 |
| `ai-code.md` | `src/ai/**` | Performance budgets, debuggability, data-driven params |
| `ai-code.md` | `src/ai/**` | 性能预算、可调试性、数据驱动参数 |
| `network-code.md` | `src/networking/**` | Server-authoritative, versioned messages, security |
| `network-code.md` | `src/networking/**` | 服务器权威、版本化消息、安全性 |
| `ui-code.md` | `src/ui/**` | No game state ownership, localization-ready, accessibility |
| `ui-code.md` | `src/ui/**` | 不持有游戏状态、本地化就绪、无障碍 |
| `design-docs.md` | `design/gdd/**` | Required 8 sections, formula format, edge cases |
| `design-docs.md` | `design/gdd/**` | 必需的 8 个章节、公式格式、边界情况 |
| `narrative.md` | `design/narrative/**` | Lore consistency, character voice, canon levels |
| `narrative.md` | `design/narrative/**` | 设定一致性、角色语调、正史层级 |
| `data-files.md` | `assets/data/**` | JSON validity, naming conventions, schema rules |
| `data-files.md` | `assets/data/**` | JSON 有效性、命名约定、模式规则 |
| `test-standards.md` | `tests/**` | Test naming, coverage requirements, fixture patterns |
| `test-standards.md` | `tests/**` | 测试命名、覆盖率要求、夹具模式 |
| `prototype-code.md` | `prototypes/**` | Relaxed standards, README required, hypothesis documented |
| `prototype-code.md` | `prototypes/**` | 放宽标准、需要 README、记录假设 |
| `shader-code.md` | `assets/shaders/**` | Naming conventions, performance targets, cross-platform rules |
| `shader-code.md` | `assets/shaders/**` | 命名约定、性能目标、跨平台规则 |
