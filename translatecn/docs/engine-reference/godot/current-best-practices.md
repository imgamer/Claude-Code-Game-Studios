# Godot — Current Best Practices
# Godot — 当前最佳实践

Last verified: 2026-02-12 | Engine: Godot 4.6
最后验证：2026-02-12 | 引擎：Godot 4.6

Practices that are **new or changed** since the model's training data (~4.3).
This supplements (not replaces) the agent's built-in knowledge.
自模型训练数据（约 4.3）以来**新增或变更**的实践。
本文件是对智能体内置知识的补充（而非替代）。

## GDScript (4.5+)
## GDScript（4.5+）

- **Variadic arguments**: Functions can accept arbitrary parameter counts
- **可变参数**：函数可接受任意数量的参数
  ```gdscript
  func log_values(prefix: String, values: Variant...) -> void:
      for v in values:
          print(prefix, ": ", v)
  ```

- **Abstract classes and methods**: Use `@abstract` to enforce inheritance
- **抽象类和方法**：使用 `@abstract` 强制继承
  ```gdscript
  @abstract
  class_name BaseEnemy extends CharacterBody3D

  @abstract
  func get_attack_pattern() -> Array[Attack]:
      pass  # Subclasses MUST override
  ```

- **Script backtracing**: Detailed call stacks available even in Release builds
- **脚本回溯**：即使在 Release 构建中也可获得详细的调用栈

## Physics (4.6)
## 物理（4.6）

- **Jolt Physics is the default 3D engine** for new projects
- **Jolt Physics 是新项目的默认 3D 引擎**
  - Better determinism and stability than GodotPhysics3D
  - 比 GodotPhysics3D 有更好的确定性和稳定性
  - Some HingeJoint3D properties (`damp`) only work with GodotPhysics
  - 某些 HingeJoint3D 属性（`damp`）仅适用于 GodotPhysics
  - Switch: Project Settings → Physics → 3D → Physics Engine
  - 切换方式：Project Settings → Physics → 3D → Physics Engine
  - 2D physics unchanged (still Godot Physics 2D)
  - 2D 物理未变（仍为 Godot Physics 2D）

## Rendering (4.6)
## 渲染（4.6）

- **D3D12 is the default backend on Windows** (was Vulkan) — for better driver compatibility
- **D3D12 是 Windows 上的默认后端**（原为 Vulkan）— 以获得更好的驱动兼容性
- **Glow now processes before tonemapping** with screen blending mode — existing glow setups may look different
- **glow 现在在色调映射之前处理**，采用屏幕混合模式 — 现有的 glow 设置外观可能不同
- **SSR overhauled** — significant improvement in realism, stability, and performance
- **SSR 重做** — 在真实感、稳定性和性能方面有显著提升
- **AgX tonemapper** — new white point and contrast controls
- **AgX 色调映射器** — 新增白点和对比度控件

## Rendering (4.5)
## 渲染（4.5）

- **Shader Baker**: Pre-compile shaders to eliminate startup hitching
- **着色器烘焙器**：预编译着色器以消除启动卡顿
- **SMAA 1x**: New AA option — sharper than FXAA, cheaper than TAA
- **SMAA 1x**：新的抗锯齿选项 — 比 FXAA 更锐利，比 TAA 更省开销
- **Stencil buffer**: Available for advanced masking/portal effects
- **模板缓冲区**：可用于高级遮罩/传送门效果
- **Bent normal maps**: Directional occlusion in normal map textures
- **弯曲法线贴图**：法线贴图纹理中的方向性遮蔽
- **Specular occlusion**: Ambient occlusion now affects reflections
- **镜面反射遮蔽**：环境光遮蔽现在会影响反射

## Accessibility (4.5+)
## 无障碍（4.5+）

- **Screen reader support**: Control nodes integrate with accessibility tools via AccessKit
- **屏幕阅读器支持**：Control 节点通过 AccessKit 与无障碍工具集成
- **Live translation preview**: Test GUI layouts in different languages directly in-editor
- **实时翻译预览**：直接在编辑器中测试不同语言的 GUI 布局
- **FoldableContainer**: New accordion-style UI node for collapsible sections
- **FoldableContainer**：用于可折叠区段的新手风琴式 UI 节点
- **Recursive Control disable**: Disable mouse/focus interactions for entire node hierarchies with a single property
- **递归 Control 禁用**：通过单个属性为整个节点层级禁用鼠标/焦点交互

## Animation (4.5+)
## 动画（4.5+）

- **BoneConstraint3D**: Bind bones to other bones with modifiers
- **BoneConstraint3D**：使用修饰器将骨骼绑定到其他骨骼
  - AimModifier3D, CopyTransformModifier3D, ConvertTransformModifier3D
  - AimModifier3D、CopyTransformModifier3D、ConvertTransformModifier3D

## Animation (4.6)
## 动画（4.6）

- **IK system fully restored**: Complete inverse kinematics reintroduced for 3D
- **IK 系统完全恢复**：为 3D 重新引入完整的逆向运动学
  - Available modifiers: CCDIK, FABRIK, Jacobian IK, Spline IK, TwoBoneIK
  - 可用修饰器：CCDIK、FABRIK、Jacobian IK、Spline IK、TwoBoneIK
  - Applied via `SkeletonModifier3D` nodes
  - 通过 `SkeletonModifier3D` 节点应用

## Resources (4.5+)
## 资源（4.5+）

- **`duplicate_deep()`**: Explicit deep duplication for nested resource trees
- **`duplicate_deep()`**：用于嵌套资源树的显式深度复制
  - Old `duplicate()` behavior retained for backward compatibility
  - 旧的 `duplicate()` 行为保留以向后兼容
  - Use `duplicate_deep()` when you need per-instance copies of nested resources
  - 当需要嵌套资源的逐实例副本时使用 `duplicate_deep()`

## Navigation (4.5+)
## 导航（4.5+）

- **Dedicated 2D navigation server**: No longer proxied through 3D NavigationServer
- **专用 2D 导航服务器**：不再通过 3D NavigationServer 代理
  - Reduces export binary size for 2D-only games
  - 减少 2D 专用游戏的导出二进制体积

## UI (4.6)
## UI（4.6）

- **Dual-focus system**: Mouse/touch focus is now separate from keyboard/gamepad focus
- **双焦点系统**：鼠标/触摸焦点现在与键盘/手柄焦点分离
  - Visual feedback differs depending on input method
  - 视觉反馈因输入方式而异
  - Consider this when designing custom focus behavior
  - 设计自定义焦点行为时请考虑这一点

## Editor Workflow (4.6)
## 编辑器工作流（4.6）

- Flexible dock drag-and-drop with blue outline preview (including bottom panel)
- 灵活的停靠面板拖放，带有蓝色轮廓预览（包括底部面板）
- Most panels support floating windows (except Debugger)
- 大多数面板支持浮动窗口（调试器除外）
- New keyboard shortcuts: Alt+O (Output), Alt+S (Shader)
- 新的键盘快捷键：Alt+O（Output）、Alt+S（Shader）
- Export variable auto-generation: drag resource from FileSystem into script editor
- 导出变量自动生成：从 FileSystem 拖动资源到脚本编辑器
- Live preview in Quick Open dialog when "Live Preview" enabled
- 启用 "Live Preview" 后，Quick Open 对话框中提供实时预览
- New "Select Mode" (v key) prevents accidental transforms; old mode renamed "Transform Mode" (q key)
- 新的 "Select Mode"（v 键）可防止意外变换；旧模式更名为 "Transform Mode"（q 键）

## Tooling
## 工具链

- **ripgrep has no `gdscript` type**: `*.gd` is registered under `gap` (GAP programming language).
- **ripgrep 没有 `gdscript` 类型**：`*.gd` 注册在 `gap`（GAP 编程语言）下。
  `rg --type gdscript` is a hard error — the search never executes.
  `rg --type gdscript` 会产生硬错误 — 搜索根本不会执行。
  Always use `rg --glob "*.gd"` (shell) or `glob: "*.gd"` (Grep tool) to filter GDScript files.
  始终使用 `rg --glob "*.gd"`（shell）或 `glob: "*.gd"`（Grep 工具）来筛选 GDScript 文件。

## Platform (4.5+)
## 平台（4.5+）

- **visionOS export**: First new platform since open-sourcing (windowed app mode)
- **visionOS 导出**：自开源以来的首个新平台（窗口化应用模式）
- **SDL3 gamepad driver**: Better cross-platform gamepad support
- **SDL3 手柄驱动**：更好的跨平台手柄支持
- **Android**: Edge-to-edge display, camera feed access, 16KB page support (Android 15+)
- **Android**：边缘到边缘显示、摄像头画面访问、16KB 页支持（Android 15+）
- **Linux**: Wayland subwindow support for multi-window capability
- **Linux**：Wayland 子窗口支持，以实现多窗口能力
