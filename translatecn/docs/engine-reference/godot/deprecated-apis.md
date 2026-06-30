# Godot — Deprecated APIs
# Godot — 弃用的 API

Last verified: 2026-02-12
最后验证：2026-02-12

If an agent suggests any API in the "Deprecated" column, it MUST be replaced
with the "Use Instead" column.
如果智能体建议了 "Deprecated" 列中的任何 API，必须用 "Use Instead" 列替换。

## Nodes & Classes
## 节点与类

| Deprecated | Use Instead | Since | Notes |
|------------|-------------|-------|-------|
| `TileMap` | `TileMapLayer` | 4.3 | One node per layer instead of multi-layer node |
| `VisibilityNotifier2D` | `VisibleOnScreenNotifier2D` | 4.0 | Renamed for clarity |
| `VisibilityNotifier3D` | `VisibleOnScreenNotifier3D` | 4.0 | Renamed for clarity |
| `YSort` | `Node2D.y_sort_enabled` | 4.0 | Property on Node2D, not a separate node |
| `Navigation2D` / `Navigation3D` | `NavigationServer2D` / `NavigationServer3D` | 4.0 | Server-based API |
| `EditorSceneFormatImporterFBX` | `EditorSceneFormatImporterFBX2GLTF` | 4.3 | Renamed |

| 弃用 | 改用 | 自版本 | 说明 |
|------------|-------------|-------|-------|
| `TileMap` | `TileMapLayer` | 4.3 | 每层一个节点，而非多层节点 |
| `VisibilityNotifier2D` | `VisibleOnScreenNotifier2D` | 4.0 | 为清晰而重命名 |
| `VisibilityNotifier3D` | `VisibleOnScreenNotifier3D` | 4.0 | 为清晰而重命名 |
| `YSort` | `Node2D.y_sort_enabled` | 4.0 | Node2D 上的属性，而非单独的节点 |
| `Navigation2D` / `Navigation3D` | `NavigationServer2D` / `NavigationServer3D` | 4.0 | 基于服务器的 API |
| `EditorSceneFormatImporterFBX` | `EditorSceneFormatImporterFBX2GLTF` | 4.3 | 重命名 |

## Methods & Properties
## 方法与属性

| Deprecated | Use Instead | Since | Notes |
|------------|-------------|-------|-------|
| `yield()` | `await signal` | 4.0 | GDScript 2.0 coroutine syntax |
| `connect("signal", obj, "method")` | `signal.connect(callable)` | 4.0 | Callable-based connections |
| `instance()` | `instantiate()` | 4.0 | Renamed |
| `PackedScene.instance()` | `PackedScene.instantiate()` | 4.0 | Renamed |
| `get_world()` | `get_world_3d()` | 4.0 | Explicit 2D/3D split |
| `OS.get_ticks_msec()` | `Time.get_ticks_msec()` | 4.0 | Time singleton preferred |
| `duplicate()` for nested resources | `duplicate_deep()` | 4.5 | Explicit deep copy control |
| `Skeleton3D` signal `bone_pose_updated` | `skeleton_updated` | 4.3 | Renamed |
| `AnimationPlayer.method_call_mode` | `AnimationMixer.callback_mode_method` | 4.3 | Moved to base class |
| `AnimationPlayer.playback_active` | `AnimationMixer.active` | 4.3 | Moved to base class |

| 弃用 | 改用 | 自版本 | 说明 |
|------------|-------------|-------|-------|
| `yield()` | `await signal` | 4.0 | GDScript 2.0 协程语法 |
| `connect("signal", obj, "method")` | `signal.connect(callable)` | 4.0 | 基于 Callable 的连接 |
| `instance()` | `instantiate()` | 4.0 | 重命名 |
| `PackedScene.instance()` | `PackedScene.instantiate()` | 4.0 | 重命名 |
| `get_world()` | `get_world_3d()` | 4.0 | 明确区分 2D/3D |
| `OS.get_ticks_msec()` | `Time.get_ticks_msec()` | 4.0 | 优先使用 Time 单例 |
| 对嵌套资源使用 `duplicate()` | `duplicate_deep()` | 4.5 | 显式控制深度复制 |
| `Skeleton3D` 信号 `bone_pose_updated` | `skeleton_updated` | 4.3 | 重命名 |
| `AnimationPlayer.method_call_mode` | `AnimationMixer.callback_mode_method` | 4.3 | 移至基类 |
| `AnimationPlayer.playback_active` | `AnimationMixer.active` | 4.3 | 移至基类 |

## Patterns (Not Just APIs)
## 模式（不仅仅是 API）

| Deprecated Pattern | Use Instead | Why |
|--------------------|-------------|-----|
| String-based `connect()` | Typed signal connections | Type-safe, refactor-friendly |
| `$NodePath` in `_process()` | `@onready var` cached reference | Performance: path lookup every frame |
| Untyped `Array` / `Dictionary` | `Array[Type]`, typed variables | GDScript compiler optimizations |
| `Texture2D` in shader parameters | `Texture` base type | Changed in 4.4 |
| Manual post-process viewport chains | `Compositor` + `CompositorEffect` | Structured post-processing (4.3+) |
| GodotPhysics3D for new projects | Jolt Physics 3D | Default since 4.6; better stability |

| 弃用的模式 | 改用 | 原因 |
|--------------------|-------------|-----|
| 基于字符串的 `connect()` | 类型化信号连接 | 类型安全、便于重构 |
| 在 `_process()` 中使用 `$NodePath` | `@onready var` 缓存引用 | 性能：每帧都进行路径查找 |
| 无类型的 `Array` / `Dictionary` | `Array[Type]`、类型化变量 | GDScript 编译器优化 |
| 着色器参数中的 `Texture2D` | `Texture` 基类型 | 在 4.4 中变更 |
| 手动后处理视口链 | `Compositor` + `CompositorEffect` | 结构化后处理（4.3+） |
| 新项目使用 GodotPhysics3D | Jolt Physics 3D | 自 4.6 起为默认；稳定性更好 |
