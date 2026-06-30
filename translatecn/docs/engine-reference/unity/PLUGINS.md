# Unity 6.3 LTS — Optional Packages & Systems
# Unity 6.3 LTS — 可选包与系统

**Last verified:** 2026-02-13
**最后验证：** 2026-02-13

This document indexes **optional packages and systems** available in Unity 6.3 LTS.
These are NOT part of the core engine but are commonly used for specific game types.
本文档索引了 Unity 6.3 LTS 中可用的**可选包与系统**。
它们不是核心引擎的一部分，但常用于特定游戏类型。

---

## How to Use This Guide
## 如何使用本指南

**✅ Detailed Documentation Available** - See `plugins/` directory for comprehensive guides
**🟡 Brief Overview Only** - Links to official docs, use WebSearch for details
**⚠️ Preview** - May have breaking changes in future versions
**📦 Package Required** - Install via Package Manager

**✅ 提供详细文档** - 查看 `plugins/` 目录获取完整指南
**🟡 仅有简要概述** - 链接到官方文档，使用 WebSearch 获取详情
**⚠️ 预览版** - 未来版本可能有破坏性变更
**📦 需要安装包** - 通过 Package Manager 安装

---

## Production-Ready Packages (Detailed Docs Available)
## 生产就绪的包（提供详细文档）

### ✅ Cinemachine
### ✅ Cinemachine

- **Purpose:** Virtual camera system (dynamic cameras, cutscenes, camera blending)
- **When to use:** 3rd person games, cinematics, complex camera behavior
- **Knowledge Gap:** Cinemachine 3.0+ (Unity 6) has major API changes vs 2.x
- **Status:** Production-Ready
- **Package:** `com.unity.cinemachine` (Package Manager)
- **Detailed Docs:** [plugins/cinemachine.md](plugins/cinemachine.md)
- **Official:** https://docs.unity3d.com/Packages/com.unity.cinemachine@3.0/manual/index.html

- **用途：** 虚拟摄像机系统（动态摄像机、过场动画、摄像机混合）
- **何时使用：** 第三人称游戏、电影化场景、复杂摄像机行为
- **知识盲区：** Cinemachine 3.0+（Unity 6）相比 2.x 有重大 API 变更
- **状态：** 生产就绪
- **包：** `com.unity.cinemachine`（Package Manager）
- **详细文档：** [plugins/cinemachine.md](plugins/cinemachine.md)
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.cinemachine@3.0/manual/index.html

---

### ✅ Addressables
### ✅ Addressables

- **Purpose:** Advanced asset management (async loading, remote content, memory control)
- **When to use:** Large projects, DLC, remote content delivery
- **Knowledge Gap:** Unity 6 improvements, better performance
- **Status:** Production-Ready
- **Package:** `com.unity.addressables` (Package Manager)
- **Detailed Docs:** [plugins/addressables.md](plugins/addressables.md)
- **Official:** https://docs.unity3d.com/Packages/com.unity.addressables@2.0/manual/index.html

- **用途：** 高级资源管理（异步加载、远程内容、内存控制）
- **何时使用：** 大型项目、DLC、远程内容分发
- **知识盲区：** Unity 6 改进，更好的性能
- **状态：** 生产就绪
- **包：** `com.unity.addressables`（Package Manager）
- **详细文档：** [plugins/addressables.md](plugins/addressables.md)
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.addressables@2.0/manual/index.html

---

### ✅ DOTS / Entities (ECS)
### ✅ DOTS / Entities (ECS)

- **Purpose:** Data-Oriented Technology Stack (high-performance ECS for massive scale)
- **When to use:** Games with 1000s of entities, RTS, simulations
- **Knowledge Gap:** Entities 1.3+ (Unity 6) is production-ready, major rewrite from 0.x
- **Status:** Production-Ready (as of Unity 6.3 LTS)
- **Package:** `com.unity.entities` (Package Manager)
- **Detailed Docs:** [plugins/dots-entities.md](plugins/dots-entities.md)
- **Official:** https://docs.unity3d.com/Packages/com.unity.entities@1.3/manual/index.html

- **用途：** Data-Oriented Technology Stack（用于超大规模的高性能 ECS）
- **何时使用：** 包含上千实体的游戏、RTS、模拟类
- **知识盲区：** Entities 1.3+（Unity 6）已生产就绪，相比 0.x 有重大重写
- **状态：** 生产就绪（截至 Unity 6.3 LTS）
- **包：** `com.unity.entities`（Package Manager）
- **详细文档：** [plugins/dots-entities.md](plugins/dots-entities.md)
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.entities@1.3/manual/index.html

---

## Other Production-Ready Packages (Brief Overview)
## 其他生产就绪的包（简要概述）

### 🟡 Input System (Already Covered)
### 🟡 Input System（已涵盖）

- **Purpose:** Modern input handling (rebindable, cross-platform)
- **Status:** Production-Ready (default in Unity 6)
- **Package:** `com.unity.inputsystem`
- **Docs:** See [modules/input.md](../modules/input.md)
- **Official:** https://docs.unity3d.com/Packages/com.unity.inputsystem@1.11/manual/index.html

- **用途：** 现代输入处理（可重绑定、跨平台）
- **状态：** 生产就绪（Unity 6 中为默认）
- **包：** `com.unity.inputsystem`
- **文档：** 见 [modules/input.md](../modules/input.md)
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.inputsystem@1.11/manual/index.html

---

### 🟡 UI Toolkit (Already Covered)
### 🟡 UI Toolkit（已涵盖）

- **Purpose:** Modern runtime UI (HTML/CSS-like, performant)
- **Status:** Production-Ready (Unity 6)
- **Package:** Built-in
- **Docs:** See [modules/ui.md](../modules/ui.md)
- **Official:** https://docs.unity3d.com/Packages/com.unity.ui@2.0/manual/index.html

- **用途：** 现代运行时 UI（类 HTML/CSS，高性能）
- **状态：** 生产就绪（Unity 6）
- **包：** 内置
- **文档：** 见 [modules/ui.md](../modules/ui.md)
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.ui@2.0/manual/index.html

---

### 🟡 Visual Effect Graph (VFX Graph)
### 🟡 Visual Effect Graph (VFX Graph)

- **Purpose:** GPU-accelerated particle system (millions of particles)
- **When to use:** Large-scale VFX, fire, smoke, magic, explosions
- **Status:** Production-Ready
- **Package:** `com.unity.visualeffectgraph` (URP/HDRP only)
- **Official:** https://docs.unity3d.com/Packages/com.unity.visualeffectgraph@17.0/manual/index.html

- **用途：** GPU 加速粒子系统（数百万粒子）
- **何时使用：** 大规模 VFX、火焰、烟雾、魔法、爆炸
- **状态：** 生产就绪
- **包：** `com.unity.visualeffectgraph`（仅 URP/HDRP）
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.visualeffectgraph@17.0/manual/index.html

---

### 🟡 Shader Graph
### 🟡 Shader Graph

- **Purpose:** Visual shader editor (node-based shader creation)
- **When to use:** Custom shaders without HLSL coding
- **Status:** Production-Ready
- **Package:** `com.unity.shadergraph` (URP/HDRP)
- **Official:** https://docs.unity3d.com/Packages/com.unity.shadergraph@17.0/manual/index.html

- **用途：** 可视化着色器编辑器（基于节点的着色器创建）
- **何时使用：** 无需 HLSL 编码的自定义着色器
- **状态：** 生产就绪
- **包：** `com.unity.shadergraph`（URP/HDRP）
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.shadergraph@17.0/manual/index.html

---

### 🟡 Timeline
### 🟡 Timeline

- **Purpose:** Cinematic sequencing (cutscenes, scripted events)
- **When to use:** Story-driven games, cinematics, scripted sequences
- **Status:** Production-Ready
- **Package:** `com.unity.timeline` (built-in)
- **Official:** https://docs.unity3d.com/Packages/com.unity.timeline@1.8/manual/index.html

- **用途：** 电影化序列（过场动画、脚本事件）
- **何时使用：** 故事驱动型游戏、电影化场景、脚本化序列
- **状态：** 生产就绪
- **包：** `com.unity.timeline`（内置）
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.timeline@1.8/manual/index.html

---

### 🟡 Animation Rigging
### 🟡 Animation Rigging

- **Purpose:** Runtime IK, procedural animation
- **When to use:** Foot IK, aim offsets, procedural limb placement
- **Status:** Production-Ready (Unity 6)
- **Package:** `com.unity.animation.rigging`
- **Official:** https://docs.unity3d.com/Packages/com.unity.animation.rigging@1.3/manual/index.html

- **用途：** 运行时 IK，程序化动画
- **何时使用：** 足部 IK、瞄准偏移、程序化肢体放置
- **状态：** 生产就绪（Unity 6）
- **包：** `com.unity.animation.rigging`
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.animation.rigging@1.3/manual/index.html

---

### 🟡 ProBuilder
### 🟡 ProBuilder

- **Purpose:** In-editor 3D modeling (level prototyping, greyboxing)
- **When to use:** Rapid prototyping, level blockout
- **Status:** Production-Ready
- **Package:** `com.unity.probuilder`
- **Official:** https://docs.unity3d.com/Packages/com.unity.probuilder@6.0/manual/index.html

- **用途：** 编辑器内 3D 建模（关卡原型设计、灰盒构建）
- **何时使用：** 快速原型设计、关卡布局
- **状态：** 生产就绪
- **包：** `com.unity.probuilder`
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.probuilder@6.0/manual/index.html

---

### 🟡 Netcode for GameObjects
### 🟡 Netcode for GameObjects

- **Purpose:** Official Unity multiplayer networking
- **When to use:** Multiplayer games (client-server architecture)
- **Status:** Production-Ready
- **Package:** `com.unity.netcode.gameobjects`
- **Official:** https://docs-multiplayer.unity3d.com/netcode/current/about/

- **用途：** Unity 官方多人网络游戏
- **何时使用：** 多人游戏（客户端-服务器架构）
- **状态：** 生产就绪
- **包：** `com.unity.netcode.gameobjects`
- **官方文档：** https://docs-multiplayer.unity3d.com/netcode/current/about/

---

### 🟡 Burst Compiler
### 🟡 Burst Compiler

- **Purpose:** LLVM-based compiler for C# Jobs (massive performance boost)
- **When to use:** Performance-critical code, DOTS, Jobs System
- **Status:** Production-Ready
- **Package:** `com.unity.burst` (auto-installed with DOTS)
- **Official:** https://docs.unity3d.com/Packages/com.unity.burst@1.8/manual/index.html

- **用途：** 基于 LLVM 的 C# Jobs 编译器（大幅性能提升）
- **何时使用：** 性能关键代码、DOTS、Jobs System
- **状态：** 生产就绪
- **包：** `com.unity.burst`（随 DOTS 自动安装）
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.burst@1.8/manual/index.html

---

### 🟡 Jobs System
### 🟡 Jobs System

- **Purpose:** Multi-threaded job scheduling (CPU parallelism)
- **When to use:** Performance optimization, parallel processing
- **Status:** Production-Ready
- **Package:** Built-in
- **Official:** https://docs.unity3d.com/Manual/JobSystem.html

- **用途：** 多线程作业调度（CPU 并行）
- **何时使用：** 性能优化、并行处理
- **状态：** 生产就绪
- **包：** 内置
- **官方文档：** https://docs.unity3d.com/Manual/JobSystem.html

---

### 🟡 Mathematics
### 🟡 Mathematics

- **Purpose:** SIMD math library (optimized for Burst)
- **When to use:** DOTS, high-performance math
- **Status:** Production-Ready
- **Package:** `com.unity.mathematics`
- **Official:** https://docs.unity3d.com/Packages/com.unity.mathematics@1.3/manual/index.html

- **用途：** SIMD 数学库（为 Burst 优化）
- **何时使用：** DOTS、高性能数学
- **状态：** 生产就绪
- **包：** `com.unity.mathematics`
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.mathematics@1.3/manual/index.html

---

### 🟡 ML-Agents (Machine Learning)
### 🟡 ML-Agents（机器学习）

- **Purpose:** Train AI with reinforcement learning
- **When to use:** Advanced AI training, procedural behavior
- **Status:** Production-Ready
- **Package:** `com.unity.ml-agents`
- **Official:** https://github.com/Unity-Technologies/ml-agents

- **用途：** 通过强化学习训练 AI
- **何时使用：** 高级 AI 训练、程序化行为
- **状态：** 生产就绪
- **包：** `com.unity.ml-agents`
- **官方文档：** https://github.com/Unity-Technologies/ml-agents

---

### 🟡 Recorder
### 🟡 Recorder

- **Purpose:** Capture gameplay footage, screenshots, animation clips
- **When to use:** Trailers, replays, debug recording
- **Status:** Production-Ready
- **Package:** `com.unity.recorder`
- **Official:** https://docs.unity3d.com/Packages/com.unity.recorder@5.0/manual/index.html

- **用途：** 捕获游戏画面、截图、动画片段
- **何时使用：** 预告片、回放、调试录制
- **状态：** 生产就绪
- **包：** `com.unity.recorder`
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.recorder@5.0/manual/index.html

---

## Preview/Experimental Packages (Use with Caution)
## 预览/实验性包（谨慎使用）

### ⚠️ Splines
### ⚠️ Splines

- **Purpose:** Runtime spline creation and editing
- **When to use:** Roads, paths, procedural content
- **Status:** Production-Ready (Unity 6)
- **Package:** `com.unity.splines`
- **Official:** https://docs.unity3d.com/Packages/com.unity.splines@2.6/manual/index.html

- **用途：** 运行时样条线创建与编辑
- **何时使用：** 道路、路径、程序化内容
- **状态：** 生产就绪（Unity 6）
- **包：** `com.unity.splines`
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.splines@2.6/manual/index.html

---

### ⚠️ Muse (AI Assistant)
### ⚠️ Muse（AI 助手）

- **Purpose:** AI-powered asset creation (textures, sprites, animations)
- **Status:** Preview (Unity 6)
- **Package:** `com.unity.muse.*`
- **Official:** https://unity.com/products/muse

- **用途：** AI 驱动的资源创建（纹理、精灵、动画）
- **状态：** 预览版（Unity 6）
- **包：** `com.unity.muse.*`
- **官方文档：** https://unity.com/products/muse

---

### ⚠️ Sentis (Neural Network Inference)
### ⚠️ Sentis（神经网络推理）

- **Purpose:** Run neural networks in Unity (AI inference)
- **Status:** Preview
- **Package:** `com.unity.sentis`
- **Official:** https://docs.unity3d.com/Packages/com.unity.sentis@2.0/manual/index.html

- **用途：** 在 Unity 中运行神经网络（AI 推理）
- **状态：** 预览版
- **包：** `com.unity.sentis`
- **官方文档：** https://docs.unity3d.com/Packages/com.unity.sentis@2.0/manual/index.html

---

## Deprecated Packages (Avoid for New Projects)
## 已弃用的包（新项目应避免使用）

### ❌ UGUI (Canvas UI)
### ❌ UGUI（Canvas UI）

- **Deprecated:** Still supported, but UI Toolkit recommended
- **Use Instead:** UI Toolkit

- **已弃用：** 仍受支持，但推荐使用 UI Toolkit
- **替代方案：** UI Toolkit

---

### ❌ Legacy Particle System
### ❌ 旧版 Particle System

- **Deprecated:** Use Visual Effect Graph (VFX Graph)
- **Use Instead:** VFX Graph

- **已弃用：** 使用 Visual Effect Graph (VFX Graph)
- **替代方案：** VFX Graph

---

### ❌ Legacy Animation
### ❌ 旧版 Animation

- **Deprecated:** Use Animator (Mecanim)
- **Use Instead:** Animator Controller

- **已弃用：** 使用 Animator (Mecanim)
- **替代方案：** Animator Controller

---

## On-Demand WebSearch Strategy
## 按需 WebSearch 策略

For packages NOT listed above, use the following approach when users ask:
对于上述未列出的包，当用户询问时使用以下方法：

1. **WebSearch** for latest documentation: `"Unity 6.3 [package name]"`
2. Verify if package is:
   - Post-cutoff (beyond May 2025 training data)
   - Preview vs Production-Ready
   - Still supported in Unity 6.3 LTS
3. Optionally cache findings in `plugins/[package-name].md` for future reference

1. 为最新文档进行 **WebSearch**：`"Unity 6.3 [package name]"`
2. 验证包是否：
   - 知识截止后（超出 2025 年 5 月训练数据）
   - 预览版还是生产就绪
   - 在 Unity 6.3 LTS 中仍受支持
3. 可选择将结果缓存到 `plugins/[package-name].md` 以供将来参考

---

## Quick Decision Guide
## 快速决策指南

**I need virtual cameras** → **Cinemachine**
**I need async asset loading / DLC** → **Addressables**
**I need 1000s of entities (RTS, sim)** → **DOTS/Entities**
**I need modern input** → **Input System** (see modules/input.md)
**I need GPU particles** → **Visual Effect Graph**
**I need visual shaders** → **Shader Graph**
**I need cinematics** → **Timeline**
**I need runtime IK** → **Animation Rigging**
**I need level prototyping** → **ProBuilder**
**I need multiplayer** → **Netcode for GameObjects**

**我需要虚拟摄像机** → **Cinemachine**
**我需要异步资源加载 / DLC** → **Addressables**
**我需要上千实体（RTS、模拟）** → **DOTS/Entities**
**我需要现代输入** → **Input System**（见 modules/input.md）
**我需要 GPU 粒子** → **Visual Effect Graph**
**我需要可视化着色器** → **Shader Graph**
**我需要电影化场景** → **Timeline**
**我需要运行时 IK** → **Animation Rigging**
**我需要关卡原型设计** → **ProBuilder**
**我需要多人游戏** → **Netcode for GameObjects**

---

**Last Updated:** 2026-02-13
**最后更新：** 2026-02-13
**Engine Version:** Unity 6.3 LTS
**引擎版本：** Unity 6.3 LTS
**LLM Knowledge Cutoff:** May 2025
**LLM 知识截止：** 2025 年 5 月
