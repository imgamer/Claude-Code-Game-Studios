# Godot — Breaking Changes
# Godot — 破坏性变更

Last verified: 2026-02-12
最后验证：2026-02-12

Changes between Godot versions, focused on post-LLM-cutoff changes (4.4+).
各 Godot 版本之间的变更，重点关注 LLM 截止之后的变更（4.4+）。

## 4.5 → 4.6 (Jan 2026 — POST-CUTOFF, HIGH RISK)
## 4.5 → 4.6（2026 年 1 月 — 截止后，高风险）

| Subsystem | Change | Details |
|-----------|--------|---------|
| Physics | Jolt is now the DEFAULT 3D physics engine | New projects use Jolt automatically. Existing projects keep their setting. Some HingeJoint3D properties (like `damp`) only work with GodotPhysics. |
| Rendering | Glow processes BEFORE tonemapping | Was after tonemapping. Scenes with glow will look different. Adjust intensity/blend in WorldEnvironment. |
| Rendering | D3D12 default on Windows | Was Vulkan. For better driver compatibility. |
| Rendering | AgX tonemapper new controls | White point and contrast parameters added. |
| Core | Quaternion initializes to identity | Was zero. Unlikely to affect most code but technically breaking. |
| UI | Dual-focus system | Mouse/touch focus now separate from keyboard/gamepad focus. Visual feedback differs by input method. |
| Animation | IK system fully restored | CCDIK, FABRIK, Jacobian IK, Spline IK, TwoBoneIK via SkeletonModifier3D nodes. |
| Editor | New "Modern" theme default | Grayscale replaces blue-tint. Restore: Editor Settings → Interface → Theme → Style: Classic |
| Editor | "Select Mode" keybind changed | New "Select Mode" (v key) prevents accidental transforms. Old mode renamed "Transform Mode" (q key). |
| 2D | TileMapLayer scene tile rotation | Scene tiles can now be rotated like atlas tiles. |
| Localization | CSV plural form support | No longer requires Gettext for plurals. Context columns added. |
| C# | Automatic string extraction | Translation strings auto-extracted from C# code. |
| Plugins | New EditorDock class | Specialized container for plugin docks with layout control. |

| 子系统 | 变更 | 详情 |
|-----------|--------|---------|
| 物理 | Jolt 现在是默认的 3D 物理引擎 | 新项目自动使用 Jolt。现有项目保留其设置。某些 HingeJoint3D 属性（如 `damp`）仅适用于 GodotPhysics。 |
| 渲染 | glow 在色调映射之前处理 | 原本在色调映射之后。带 glow 的场景外观会不同。请在 WorldEnvironment 中调整强度/混合。 |
| 渲染 | Windows 上 D3D12 设为默认 | 原为 Vulkan。旨在获得更好的驱动兼容性。 |
| 渲染 | AgX 色调映射器新增控件 | 增加了白点和对比度参数。 |
| 核心 | Quaternion 初始化为单位值 | 原为零。不太可能影响大多数代码，但技术上属于破坏性变更。 |
| UI | 双焦点系统 | 鼠标/触摸焦点现在与键盘/手柄焦点分离。视觉反馈因输入方式而异。 |
| 动画 | IK 系统完全恢复 | 通过 SkeletonModifier3D 节点提供 CCDIK、FABRIK、Jacobian IK、Spline IK、TwoBoneIK。 |
| 编辑器 | 新的 "Modern" 主题设为默认 | 灰度取代蓝色调。恢复方法：Editor Settings → Interface → Theme → Style: Classic |
| 编辑器 | "Select Mode" 快捷键变更 | 新的 "Select Mode"（v 键）可防止意外变换。旧模式更名为 "Transform Mode"（q 键）。 |
| 2D | TileMapLayer 场景瓦片旋转 | 场景瓦片现在可以像图集瓦片一样旋转。 |
| 本地化 | CSV 复数形式支持 | 不再需要 Gettext 来处理复数。增加了上下文列。 |
| C# | 自动字符串提取 | 翻译字符串会自动从 C# 代码中提取。 |
| 插件 | 新增 EditorDock 类 | 用于插件停靠面板的专用容器，带有布局控制功能。 |

## 4.4 → 4.5 (Late 2025 — POST-CUTOFF, HIGH RISK)
## 4.4 → 4.5（2025 年末 — 截止后，高风险）

| Subsystem | Change | Details |
|-----------|--------|---------|
| GDScript | Variadic arguments added | Functions can accept `...` arbitrary params — new language feature |
| GDScript | `@abstract` decorator | Abstract classes and methods now enforceable |
| GDScript | Script backtracing | Detailed call stacks available even in Release builds |
| Rendering | Stencil buffer support | New capability for advanced visual effects |
| Rendering | SMAA 1x antialiasing | New post-processing AA option |
| Rendering | Shader Baker | Pre-compiles shaders — reportedly 20x faster startup on some demos |
| Rendering | Bent normal maps, specular occlusion | New material features |
| Accessibility | Screen reader support | Control nodes work with accessibility tools via AccessKit |
| Editor | Live translation preview | Test GUI layouts in different languages in-editor |
| Physics | 3D interpolation rearchitected | Moved from RenderingServer to SceneTree. API unchanged but internals differ. |
| Animation | BoneConstraint3D | New: AimModifier3D, CopyTransformModifier3D, ConvertTransformModifier3D |
| Resources | `duplicate_deep()` added | New explicit method for deep duplication of nested resources |
| Navigation | Dedicated 2D navigation server | No longer a proxy to 3D navigation; smaller export for 2D games |
| UI | FoldableContainer node | New accordion-style container for collapsible UI sections |
| UI | Recursive Control behavior | Disable mouse/focus interactions across entire node hierarchies |
| Platform | visionOS export support | New platform target |
| Platform | SDL3 gamepad driver | Delegated gamepad handling to SDL library |
| Platform | Android 16KB page support | Required for Google Play targeting Android 15+ |

| 子系统 | 变更 | 详情 |
|-----------|--------|---------|
| GDScript | 增加可变参数 | 函数可接受 `...` 任意参数 — 新的语言特性 |
| GDScript | `@abstract` 装饰器 | 抽象类和方法现在可强制执行 |
| GDScript | 脚本回溯 | 即使在 Release 构建中也可获得详细的调用栈 |
| 渲染 | 模板缓冲区支持 | 用于高级视觉效果的新功能 |
| 渲染 | SMAA 1x 抗锯齿 | 新的后处理抗锯齿选项 |
| 渲染 | 着色器烘焙器 | 预编译着色器 — 据称在某些示例中启动速度提升 20 倍 |
| 渲染 | 弯曲法线贴图、镜面反射遮蔽 | 新的材质特性 |
| 无障碍 | 屏幕阅读器支持 | Control 节点通过 AccessKit 与无障碍工具协同工作 |
| 编辑器 | 实时翻译预览 | 在编辑器中测试不同语言的 GUI 布局 |
| 物理 | 3D 插值重构 | 从 RenderingServer 移至 SceneTree。API 未变但内部实现不同。 |
| 动画 | BoneConstraint3D | 新增：AimModifier3D、CopyTransformModifier3D、ConvertTransformModifier3D |
| 资源 | 新增 `duplicate_deep()` | 用于嵌套资源深度复制的新显式方法 |
| 导航 | 专用 2D 导航服务器 | 不再是 3D 导航的代理；2D 游戏的导出体积更小 |
| UI | FoldableContainer 节点 | 用于可折叠 UI 区段的新手风琴式容器 |
| UI | 递归 Control 行为 | 可禁用整个节点层级的鼠标/焦点交互 |
| 平台 | visionOS 导出支持 | 新的平台目标 |
| 平台 | SDL3 手柄驱动 | 将手柄处理委托给 SDL 库 |
| 平台 | Android 16KB 页支持 | 面向 Android 15+ 的 Google Play 发布所需 |

## 4.3 → 4.4 (Mid 2025 — NEAR CUTOFF, VERIFY)
## 4.3 → 4.4（2025 年中 — 接近截止，需核实）

| Subsystem | Change | Details |
|-----------|--------|---------|
| Core | `FileAccess.store_*` return `bool` | Was `void`. Methods: `store_8`, `store_16`, `store_32`, `store_64`, `store_buffer`, `store_csv_line`, `store_double`, `store_float`, `store_half`, `store_line`, `store_pascal_string`, `store_real`, `store_string`, `store_var` |
| Core | `OS.execute_with_pipe` | Added optional `blocking` parameter |
| Core | `RegEx.compile/create_from_string` | Added optional `show_error` parameter |
| Rendering | `RenderingDevice.draw_list_begin` | Many parameters removed; `breadcrumb` parameter added |
| Rendering | Shader texture types | Parameter/return types changed from `Texture2D` to `Texture` |
| Particles | `.restart()` method | Added optional `keep_seed` parameter (CPU/GPU 2D/3D) |
| GUI | `RichTextLabel.push_meta` | Added optional `tooltip` parameter |
| GUI | `GraphEdit.connect_node` | Added optional `keep_alive` parameter |

| 子系统 | 变更 | 详情 |
|-----------|--------|---------|
| 核心 | `FileAccess.store_*` 返回 `bool` | 原为 `void`。方法包括：`store_8`、`store_16`、`store_32`、`store_64`、`store_buffer`、`store_csv_line`、`store_double`、`store_float`、`store_half`、`store_line`、`store_pascal_string`、`store_real`、`store_string`、`store_var` |
| 核心 | `OS.execute_with_pipe` | 增加可选 `blocking` 参数 |
| 核心 | `RegEx.compile/create_from_string` | 增加可选 `show_error` 参数 |
| 渲染 | `RenderingDevice.draw_list_begin` | 移除了许多参数；增加了 `breadcrumb` 参数 |
| 渲染 | 着色器纹理类型 | 参数/返回类型从 `Texture2D` 改为 `Texture` |
| 粒子 | `.restart()` 方法 | 增加可选 `keep_seed` 参数（CPU/GPU 2D/3D） |
| GUI | `RichTextLabel.push_meta` | 增加可选 `tooltip` 参数 |
| GUI | `GraphEdit.connect_node` | 增加可选 `keep_alive` 参数 |

## 4.2 → 4.3 (In Training Data — LOW RISK)
## 4.2 → 4.3（在训练数据中 — 低风险）

| Subsystem | Change | Details |
|-----------|--------|---------|
| Animation | `Skeleton3D.add_bone` returns `int32` | Was `void` |
| Animation | `bone_pose_updated` signal | Replaced by `skeleton_updated` |
| TileMap | `TileMapLayer` replaces `TileMap` | One node per layer instead of multi-layer single node |
| Navigation | `NavigationRegion2D` | Removed `avoidance_layers`, `constrain_avoidance` properties |
| Editor | `EditorSceneFormatImporterFBX` | Renamed to `EditorSceneFormatImporterFBX2GLTF` |
| Animation | AnimationMixer base class | AnimationPlayer and AnimationTree now extend AnimationMixer |

| 子系统 | 变更 | 详情 |
|-----------|--------|---------|
| 动画 | `Skeleton3D.add_bone` 返回 `int32` | 原为 `void` |
| 动画 | `bone_pose_updated` 信号 | 被 `skeleton_updated` 取代 |
| TileMap | `TileMapLayer` 取代 `TileMap` | 每层一个节点，而非多层单节点 |
| 导航 | `NavigationRegion2D` | 移除了 `avoidance_layers`、`constrain_avoidance` 属性 |
| 编辑器 | `EditorSceneFormatImporterFBX` | 重命名为 `EditorSceneFormatImporterFBX2GLTF` |
| 动画 | AnimationMixer 基类 | AnimationPlayer 和 AnimationTree 现在继承自 AnimationMixer |
