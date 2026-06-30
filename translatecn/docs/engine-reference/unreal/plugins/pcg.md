# Unreal Engine 5.7 — PCG (Procedural Content Generation)
# Unreal Engine 5.7 — PCG（程序化内容生成）

**Last verified:** 2026-02-13
**Status:** Production-Ready (as of UE 5.7)
**Plugin:** `PCG` (built-in, enable in Plugins)
**最后验证：** 2026-02-13
**状态：** 生产就绪（自 UE 5.7 起）
**插件：** `PCG`（内置，在 Plugins 中启用）

---

## Overview
## 概述

**Procedural Content Generation (PCG)** is Unreal's node-based framework for generating
procedural content at massive scale. It's designed for populating large open worlds with
foliage, rocks, props, buildings, and other environmental detail.

**Procedural Content Generation (PCG)** 是 Unreal 基于节点的框架，用于大规模生成程序化内容。它专为用植被、岩石、道具、建筑和其他环境细节填充大型开放世界而设计。

**Use PCG for:**
- Procedural foliage placement (trees, grass, rocks)
- Biome-based environment generation
- Road/path generation
- Building/structure placement
- World detail population (props, clutter)

**使用 PCG 的场景：**
- 程序化植被放置（树木、草、岩石）
- 基于生物群落的环境生成
- 道路/路径生成
- 建筑/结构放置
- 世界细节填充（道具、杂物）

**DON'T use PCG for:**
- Gameplay logic (use Blueprints/C++)
- One-off manual placement (use editor tools)

**不要使用 PCG 的场景：**
- 玩法逻辑（使用 Blueprints/C++）
- 一次性手动放置（使用编辑器工具）

**⚠️ Note:** PCG was experimental in UE 5.0-5.6, became production-ready in UE 5.7.

**⚠️ 注意：** PCG 在 UE 5.0-5.6 中为实验性，在 UE 5.7 中成为生产就绪。

---

## Core Concepts
## 核心概念

### 1. **PCG Graph**
### 1. **PCG Graph**
- Node-based graph (similar to Material Editor)
- Defines generation rules

- 基于节点的图（类似于材质编辑器）
- 定义生成规则

### 2. **PCG Component**
### 2. **PCG Component**
- Placed in level, executes PCG Graph
- Generates content in defined volume

- 放置在关卡中，执行 PCG Graph
- 在定义的体积内生成内容

### 3. **PCG Data**
### 3. **PCG Data**
- Point data (positions, rotations, scales)
- Spline data (paths, roads, rivers)
- Volume data (density, biome masks)

- 点数据（位置、旋转、缩放）
- 样条数据（路径、道路、河流）
- 体积数据（密度、生物群落遮罩）

### 4. **Nodes**
### 4. **节点**
- **Samplers**: Generate points (Grid, Poisson, Surface)
- **Filters**: Remove points based on rules (Density, Tag, Bounds)
- **Modifiers**: Transform points (Offset, Rotate, Scale)
- **Spawners**: Instantiate meshes/actors at points

- **Samplers**：生成点（Grid、Poisson、Surface）
- **Filters**：根据规则移除点（Density、Tag、Bounds）
- **Modifiers**：变换点（Offset、Rotate、Scale）
- **Spawners**：在点处实例化网格/actor

---

## Setup
## 设置

### 1. Enable Plugin
### 1. 启用插件

`Edit > Plugins > PCG > Enabled > Restart`

### 2. Create PCG Volume
### 2. 创建 PCG Volume

1. Place Actors > Volumes > PCG Volume
2. Scale volume to desired generation area

1. Place Actors > Volumes > PCG Volume
2. 将体积缩放到所需的生成区域

### 3. Create PCG Graph
### 3. 创建 PCG Graph

1. Content Browser > PCG > PCG Graph
2. Open PCG Graph Editor

1. Content Browser > PCG > PCG Graph
2. 打开 PCG Graph 编辑器

---

## Basic Workflow
## 基本工作流

### Example: Forest Generation
### 示例：森林生成

#### 1. Create PCG Graph
#### 1. 创建 PCG Graph

**Node Setup:**
**节点设置：**
```
Input (Volume)
  ↓
Surface Sampler (sample volume surface, points per m²: 0.5)
  ↓
Density Filter (use texture mask or noise)
  ↓
Static Mesh Spawner (tree meshes)
  ↓
Output
```

#### 2. Assign Graph to Volume
#### 2. 将 Graph 分配给 Volume

1. Select PCG Volume
2. Details Panel > PCG Component > Graph = Your PCG Graph
3. Click "Generate" button

1. 选择 PCG Volume
2. Details Panel > PCG Component > Graph = 你的 PCG Graph
3. 点击 "Generate" 按钮

---

## Key Node Types
## 关键节点类型

### Samplers (Point Generation)
### Samplers（点生成）

#### Grid Sampler
#### Grid Sampler
- Regular grid of points
- Configure:
  - **Grid Size**: Distance between points
  - **Offset**: Random offset per point

- 规则的点网格
- 配置：
  - **Grid Size**：点之间的距离
  - **Offset**：每个点的随机偏移

#### Poisson Disk Sampler
#### Poisson Disk Sampler
- Random points with minimum distance
- Configure:
  - **Points Per m²**: Density
  - **Min Distance**: Spacing between points

- 具有最小距离的随机点
- 配置：
  - **Points Per m²**：密度
  - **Min Distance**：点之间的间距

#### Surface Sampler
#### Surface Sampler
- Points on mesh surfaces or landscape
- Configure:
  - **Points Per m²**: Density
  - **Surface Only**: Only surface, not volume

- 网格表面或地表上的点
- 配置：
  - **Points Per m²**：密度
  - **Surface Only**：仅表面，不包括体积

---

### Filters (Point Removal)
### Filters（点移除）

#### Density Filter
#### Density Filter
- Remove points based on density value
- Input: Texture or noise
- Use for: Biome masks, clearings, paths

- 根据密度值移除点
- 输入：纹理或噪声
- 用途：生物群落遮罩、空地、路径

#### Tag Filter
#### Tag Filter
- Filter points by tag
- Use for: Conditional spawning

- 按标签过滤点
- 用途：条件生成

#### Bounds Filter
#### Bounds Filter
- Keep only points within bounds
- Use for: Limiting generation to specific areas

- 仅保留边界内的点
- 用途：将生成限制在特定区域

---

### Modifiers (Point Transformation)
### Modifiers（点变换）

#### Rotate
#### Rotate
- Randomize point rotation
- Configure:
  - **Min/Max Rotation**: Rotation range per axis

- 随机化点旋转
- 配置：
  - **Min/Max Rotation**：每个轴的旋转范围

#### Scale
#### Scale
- Randomize point scale
- Configure:
  - **Min/Max Scale**: Scale range

- 随机化点缩放
- 配置：
  - **Min/Max Scale**：缩放范围

#### Project to Ground
#### Project to Ground
- Snap points to landscape surface

- 将点吸附到地表

---

### Spawners (Mesh/Actor Instantiation)
### Spawners（网格/Actor 实例化）

#### Static Mesh Spawner
#### Static Mesh Spawner
- Spawn static meshes at points
- Configure:
  - **Mesh List**: Array of meshes (random selection)
  - **Culling Distance**: LOD/culling settings

- 在点处生成静态网格
- 配置：
  - **Mesh List**：网格数组（随机选择）
  - **Culling Distance**：LOD/剔除设置

#### Actor Spawner
#### Actor Spawner
- Spawn Blueprint actors at points
- Use for: Gameplay actors, interactive objects

- 在点处生成 Blueprint actor
- 用途：玩法 actor、交互对象

---

## Data Sources
## 数据源

### Landscape
### 地表
- Use landscape as input for sampling
- Automatically projects to landscape height

- 使用地表作为采样输入
- 自动投影到地表高度

### Splines
### 样条
- Generate content along splines (roads, rivers, paths)
- Example: Trees along path

- 沿样条生成内容（道路、河流、路径）
- 示例：沿路径的树木

### Textures
### 纹理
- Use textures as density masks
- Paint biomes, clearings, areas

- 使用纹理作为密度遮罩
- 绘制生物群落、空地、区域

---

## Biome Example (Mixed Forest)
## 生物群落示例（混合森林）

### Graph Setup
### Graph 设置

```
Input (Landscape)
  ↓
Surface Sampler (density: 1.0)
  ↓
┌─────────────────┬─────────────────┐
│ Tree Biome      │ Rock Biome      │
│ (density > 0.5) │ (density < 0.5) │
├─────────────────┼─────────────────┤
│ Tree Spawner    │ Rock Spawner    │
└─────────────────┴─────────────────┘
  ↓
Merge
  ↓
Output
```

---

## Spline-Based Generation (Road with Trees)
## 基于样条的生成（带树木的道路）

### 1. Create PCG Graph
### 1. 创建 PCG Graph

```
Spline Input
  ↓
Spline Sampler (sample along spline)
  ↓
Offset (offset from spline path)
  ↓
Tree Spawner
  ↓
Output
```

### 2. Add Spline Component to PCG Volume
### 2. 向 PCG Volume 添加 Spline Component

1. PCG Volume > Add Component > Spline
2. Draw spline path
3. PCG Graph reads spline data

1. PCG Volume > Add Component > Spline
2. 绘制样条路径
3. PCG Graph 读取样条数据

---

## Runtime Generation
## 运行时生成

### Trigger Generation from C++
### 从 C++ 触发生成

```cpp
#include "PCGComponent.h"

UPCGComponent* PCGComp = /* Get PCG Component */;
PCGComp->Generate(); // Execute PCG graph
```

### Stream Generation (Large Worlds)
### 流式生成（大型世界）

- PCG automatically streams with World Partition
- Only generates content in loaded cells

- PCG 自动随 World Partition 流送
- 仅在已加载的区块中生成内容

---

## Performance
## 性能

### Optimization Tips
### 优化提示

- Use **culling distance** on spawned meshes (LOD)
- Limit **density** (fewer points = better performance)
- Use **Hierarchical Instanced Static Meshes (HISM)** for repeated meshes
- Enable **streaming** for large worlds

- 对生成的网格使用 **culling distance**（LOD）
- 限制 **density**（点越少 = 性能越好）
- 对重复网格使用 **Hierarchical Instanced Static Meshes (HISM)**
- 为大型世界启用 **streaming**

### Debug Performance
### 调试性能

```cpp
// Console commands:
// pcg.graph.debug 1 - Show PCG debug info
// stat pcg - Show PCG performance stats
```

---

## Common Patterns
## 常见模式

### Forest with Clearings
### 带空地的森林

```
Surface Sampler
  ↓
Density Filter (noise texture with clearings)
  ↓
Tree Spawner (pine, oak, birch)
```

---

### Rocks on Steep Slopes
### 陡坡上的岩石

```
Landscape Input
  ↓
Surface Sampler
  ↓
Slope Filter (angle > 30°)
  ↓
Rock Spawner
```

---

### Props Along Road
### 沿道路的道具

```
Spline Input (road spline)
  ↓
Spline Sampler
  ↓
Offset (side of road)
  ↓
Street Light Spawner
```

---

## Debugging
## 调试

### PCG Debug Visualization
### PCG 调试可视化

```cpp
// Console commands:
// pcg.debug.display 1 - Show points and generation bounds
// pcg.debug.colormode points - Color-code points
```

### Graph Debugging
### Graph 调试

- PCG Graph Editor > Debug > Show Debug Points
- Visualize points at each node in the graph

- PCG Graph Editor > Debug > Show Debug Points
- 可视化图中每个节点的点

---

## Migration from UE 5.6 (Experimental) to 5.7 (Production)
## 从 UE 5.6（实验性）迁移到 5.7（生产就绪）

### API Changes
### API 变更

```cpp
// ❌ OLD (5.6 experimental API):
// Some nodes renamed, API unstable

// ✅ NEW (5.7 production API):
// Stable node types, documented API
```

**Migration:** Rebuild PCG graphs using stable 5.7 nodes. Test thoroughly.

**迁移：** 使用稳定的 5.7 节点重建 PCG graph。充分测试。

---

## Limitations
## 限制

- **Not for gameplay logic**: Use Blueprints/C++ for game rules
- **Large graphs can be slow**: Optimize with filters and density reduction
- **Runtime generation overhead**: Pre-generate when possible

- **不适用于玩法逻辑**：游戏规则使用 Blueprints/C++
- **大型 graph 可能较慢**：通过过滤器和降低密度进行优化
- **运行时生成开销**：尽可能预先生成

---

## Sources
## 来源
- https://docs.unrealengine.com/5.7/en-US/procedural-content-generation-in-unreal-engine/
- https://docs.unrealengine.com/5.7/en-US/pcg-quick-start-in-unreal-engine/
- UE 5.7 Release Notes (PCG Production-Ready announcement)
- UE 5.7 发行说明（PCG 生产就绪公告）
