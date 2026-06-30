# Godot UI — Quick Reference
# Godot UI — 快速参考

Last verified: 2026-02-12 | Engine: Godot 4.6
最后验证：2026-02-12 | 引擎：Godot 4.6

## What Changed Since ~4.3 (LLM Cutoff)
## 自约 4.3（LLM 截止）以来的变更

### 4.6 Changes
### 4.6 变更
- **Dual-focus system**: Mouse/touch focus is now SEPARATE from keyboard/gamepad focus
- **双焦点系统**：鼠标/触摸焦点现在与键盘/手柄焦点分离
  - Visual feedback differs by input method
  - 视觉反馈因输入方式而异
  - Custom focus implementations may need updating
  - 自定义焦点实现可能需要更新
- **TabContainer**: Tab properties editable directly in Inspector
- **TabContainer**：标签页属性可直接在 Inspector 中编辑
- **TileMapLayer scene tile rotation**: Scene tiles can be rotated like atlas tiles
- **TileMapLayer 场景瓦片旋转**：场景瓦片可以像图集瓦片一样旋转

### 4.5 Changes
### 4.5 变更
- **FoldableContainer**: New accordion-style UI node for collapsible sections
- **FoldableContainer**：用于可折叠区段的新手风琴式 UI 节点
- **Recursive Control behavior**: Disable mouse/focus for entire node hierarchies
  with a single property
- **递归 Control 行为**：通过单个属性即可禁用整个节点层级的鼠标/焦点
- **Screen reader support**: Control nodes work with AccessKit
- **屏幕阅读器支持**：Control 节点可与 AccessKit 协同工作
- **Live translation preview**: Test different locales in-editor
- **实时翻译预览**：在编辑器中测试不同语言环境
- **`RichTextLabel.push_meta`**: Added optional `tooltip` parameter (from 4.4)
- **`RichTextLabel.push_meta`**：增加了可选的 `tooltip` 参数（源自 4.4）

### 4.4 Changes
### 4.4 变更
- **`GraphEdit.connect_node`**: Added optional `keep_alive` parameter
- **`GraphEdit.connect_node`**：增加了可选的 `keep_alive` 参数

## Current API Patterns
## 当前 API 模式

### Theme and Style (4.6)
### 主题与样式（4.6）
```gdscript
# Editor uses new "Modern" theme by default
# For game UI, use custom themes as before:
var theme := Theme.new()
theme.set_color(&"font_color", &"Label", Color.WHITE)
theme.set_font_size(&"font_size", &"Label", 24)
```

### Focus Management (4.6 — CHANGED)
### 焦点管理（4.6 — 已变更）
```gdscript
# Keyboard/gamepad focus (grab_focus still works)
func _ready() -> void:
    %StartButton.grab_focus()

# IMPORTANT: In 4.6, mouse hover is separate from keyboard focus
# Both can be active simultaneously on different controls
# Test your UI with BOTH mouse and keyboard/gamepad

# Focus neighbors (unchanged)
%Button1.focus_neighbor_bottom = %Button2.get_path()
%Button1.focus_neighbor_right = %Button3.get_path()
```

### FoldableContainer (4.5 — NEW)
### FoldableContainer（4.5 — 新增）
```gdscript
# Accordion-style collapsible container
# Add as parent of content you want to make collapsible
# Children show/hide when header is clicked
# Configure via editor properties or code
```

### Recursive Disable (4.5 — NEW)
### 递归禁用（4.5 — 新增）
```gdscript
# Disable all mouse/focus interactions for a hierarchy
# Useful for disabling entire menu sections
%SettingsPanel.mouse_filter = Control.MOUSE_FILTER_IGNORE
# In 4.5+, this can propagate recursively to children
```

### Localization-Ready UI (best practice)
### 本地化就绪的 UI（最佳实践）
```gdscript
# Use tr() for all visible strings
label.text = tr("MENU_START_GAME")

# Use auto-wrap for labels (text length varies by language)
label.autowrap_mode = TextServer.AUTOWRAP_WORD_SMART

# Test with live translation preview in editor (4.5+)
```

## Common Mistakes
## 常见错误
- Assuming `grab_focus()` affects mouse focus (keyboard/gamepad only in 4.6)
- 假设 `grab_focus()` 会影响鼠标焦点（在 4.6 中仅影响键盘/手柄）
- Not testing UI with both mouse and gamepad after upgrading to 4.6
- 升级到 4.6 后未同时用鼠标和手柄测试 UI
- Hardcoding strings instead of using `tr()` for localization
- 硬编码字符串而非使用 `tr()` 进行本地化
- Not using `FoldableContainer` for collapsible UI (new in 4.5, cleaner than custom)
- 未使用 `FoldableContainer` 实现可折叠 UI（4.5 中新增，比自定义方案更简洁）
