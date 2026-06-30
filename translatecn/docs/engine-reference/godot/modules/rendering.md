# Godot Rendering — Quick Reference
# Godot 渲染 — 快速参考

Last verified: 2026-02-12 | Engine: Godot 4.6
最后验证：2026-02-12 | 引擎：Godot 4.6

## What Changed Since ~4.3 (LLM Cutoff)
## 自约 4.3（LLM 截止）以来的变更

### 4.6 Changes
### 4.6 变更
- **D3D12 is the default rendering backend on Windows** (was Vulkan)
- **D3D12 是 Windows 上的默认渲染后端**（原为 Vulkan）
- **Glow processes before tonemapping** (was after) — uses screen blending mode
- **glow 在色调映射之前处理**（原为之后）— 采用屏幕混合模式
- **AgX tonemapper**: new white point and contrast controls
- **AgX 色调映射器**：新增白点和对比度控件
- **SSR overhauled**: better realism, visual stability, and performance
- **SSR 重做**：更好的真实感、视觉稳定性和性能

### 4.5 Changes
### 4.5 变更
- **Shader Baker**: Pre-compiles shaders to reduce startup time
- **着色器烘焙器**：预编译着色器以减少启动时间
- **SMAA 1x**: New anti-aliasing option (sharper than FXAA, cheaper than TAA)
- **SMAA 1x**：新的抗锯齿选项（比 FXAA 更锐利，比 TAA 更省开销）
- **Stencil buffer support**: Enables selective geometry masking/portal effects
- **模板缓冲区支持**：可实现选择性几何体遮罩/传送门效果
- **Bent normal maps**: Directional occlusion encoded in normal map textures
- **弯曲法线贴图**：在法线贴图纹理中编码方向性遮蔽
- **Specular occlusion**: Ambient occlusion now correctly affects reflections
- **镜面反射遮蔽**：环境光遮蔽现在能正确影响反射

### 4.4 Changes
### 4.4 变更
- **`RenderingDevice.draw_list_begin`**: Many parameters removed; optional `breadcrumb` added
- **`RenderingDevice.draw_list_begin`**：移除了许多参数；增加了可选的 `breadcrumb`
- **Shader texture types**: Changed from `Texture2D` to `Texture` base type
- **着色器纹理类型**：从 `Texture2D` 改为 `Texture` 基类型
- **Particles `.restart()`**: Added optional `keep_seed` parameter
- **粒子 `.restart()`**：增加了可选的 `keep_seed` 参数

### 4.3 Changes (in training data)
### 4.3 变更（在训练数据中）
- **Compositor node**: `Compositor` + `CompositorEffect` for post-processing chains
- **Compositor 节点**：`Compositor` + `CompositorEffect` 用于后处理链

## Current API Patterns
## 当前 API 模式

### Post-Processing (4.3+)
### 后处理（4.3+）
```gdscript
# Use Compositor node — NOT manual viewport shader chains
# Add Compositor as child of WorldEnvironment or Camera3D
# Create CompositorEffect resources for each post-process step
```

### Anti-Aliasing Options (4.6)
### 抗锯齿选项（4.6）
```
Project Settings → Rendering → Anti Aliasing:
- MSAA 2D/3D: Hardware MSAA (quality but expensive)
- Screen Space AA: FXAA (fast, blurry) or SMAA (sharp, moderate cost)  # SMAA new in 4.5
- TAA: Temporal (best quality, ghosting on fast motion)
```

### Rendering Backend Selection (4.6)
### 渲染后端选择（4.6）
```
Project Settings → Rendering → Renderer:
- Forward+ (default): Full featured, desktop-focused
- Mobile: Optimized for mobile/low-end, limited features
- Compatibility: OpenGL 3.3 / WebGL 2, broadest hardware support

Windows default backend: D3D12 (was Vulkan pre-4.6)
```

## Common Mistakes
## 常见错误
- Assuming Vulkan is the default backend on Windows (D3D12 since 4.6)
- 假设 Vulkan 是 Windows 上的默认后端（自 4.6 起为 D3D12）
- Using manual viewport chains instead of Compositor for post-processing
- 使用手动视口链而非 Compositor 进行后处理
- Using `Texture2D` in shader uniform types (use `Texture` since 4.4)
- 在着色器 uniform 类型中使用 `Texture2D`（自 4.4 起应使用 `Texture`）
- Not using Shader Baker for projects with many shader variants
- 对于有大量着色器变体的项目未使用着色器烘焙器
