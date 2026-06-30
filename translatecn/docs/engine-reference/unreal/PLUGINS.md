# Unreal Engine 5.7 — Optional Plugins & Systems
# Unreal Engine 5.7 — 可选插件与系统

**Last verified:** 2026-02-13
**最后验证：** 2026-02-13

This document indexes **optional plugins and systems** available in Unreal Engine 5.7.
These are NOT part of the core engine but are commonly used for specific game types.

本文档索引了 Unreal Engine 5.7 中可用的**可选插件与系统**。它们**不是**核心引擎的一部分，但常用于特定游戏类型。

---

## How to Use This Guide
## 如何使用本指南

**✅ Detailed Documentation Available** - See `plugins/` directory for comprehensive guides
**🟡 Brief Overview Only** - Links to official docs, use WebSearch for details
**⚠️ Experimental** - May have breaking changes in future versions
**📦 Plugin Required** - Must be enabled in `Edit > Plugins`

**✅ 提供详细文档** - 参见 `plugins/` 目录获取完整指南
**🟡 仅简要概述** - 链接到官方文档，使用 WebSearch 获取详情
**⚠️ 实验性** - 未来版本可能有破坏性变更
**📦 需要插件** - 必须在 `Edit > Plugins` 中启用

---

## Production-Ready Systems (Detailed Docs Available)
## 生产就绪系统（提供详细文档）

### ✅ Gameplay Ability System (GAS)
### ✅ Gameplay Ability System (GAS)
- **Purpose:** Modular ability system (abilities, attributes, effects, cooldowns, costs)
- **When to use:** RPGs, MOBAs, shooters with abilities, any ability-based gameplay
- **Knowledge Gap:** GAS stable since UE4, UE5 improvements post-cutoff
- **Status:** Production-Ready
- **Plugin:** `GameplayAbilities` (built-in, enable in Plugins)
- **Detailed Docs:** [plugins/gameplay-ability-system.md](plugins/gameplay-ability-system.md)
- **Official:** https://docs.unrealengine.com/5.7/en-US/gameplay-ability-system-for-unreal-engine/

- **用途：** 模块化能力系统（能力、属性、效果、冷却、消耗）
- **何时使用：** RPG、MOBA、带能力的射击游戏，任何基于能力的玩法
- **知识缺口：** GAS 自 UE4 起稳定，UE5 的改进在截止日期之后
- **状态：** 生产就绪
- **插件：** `GameplayAbilities`（内置，在 Plugins 中启用）
- **详细文档：** [plugins/gameplay-ability-system.md](plugins/gameplay-ability-system.md)
- **官方：** https://docs.unrealengine.com/5.7/en-US/gameplay-ability-system-for-unreal-engine/

---

### ✅ CommonUI
### ✅ CommonUI
- **Purpose:** Cross-platform UI framework (automatic gamepad/mouse/touch input routing)
- **When to use:** Multi-platform games (console + PC), input-agnostic UI
- **Knowledge Gap:** Production-ready in UE5+, major improvements post-cutoff
- **Status:** Production-Ready
- **Plugin:** `CommonUI` (built-in, enable in Plugins)
- **Detailed Docs:** [plugins/common-ui.md](plugins/common-ui.md)
- **Official:** https://docs.unrealengine.com/5.7/en-US/commonui-plugin-for-advanced-user-interfaces-in-unreal-engine/

- **用途：** 跨平台 UI 框架（自动路由手柄/鼠标/触摸输入）
- **何时使用：** 多平台游戏（主机 + PC）、与输入无关的 UI
- **知识缺口：** UE5+ 生产就绪，主要改进在截止日期之后
- **状态：** 生产就绪
- **插件：** `CommonUI`（内置，在 Plugins 中启用）
- **详细文档：** [plugins/common-ui.md](plugins/common-ui.md)
- **官方：** https://docs.unrealengine.com/5.7/en-US/commonui-plugin-for-advanced-user-interfaces-in-unreal-engine/

---

### ✅ Gameplay Camera System
### ✅ Gameplay Camera System
- **Purpose:** Modular camera management (camera modes, blending, context-aware cameras)
- **When to use:** Games needing dynamic camera behavior (3rd person, aiming, vehicles)
- **Knowledge Gap:** NEW in UE 5.5, completely post-cutoff
- **Status:** ⚠️ Experimental (UE 5.5-5.7)
- **Plugin:** `GameplayCameras` (built-in, enable in Plugins)
- **Detailed Docs:** [plugins/gameplay-camera-system.md](plugins/gameplay-camera-system.md)
- **Official:** https://docs.unrealengine.com/5.7/en-US/gameplay-cameras-in-unreal-engine/

- **用途：** 模块化摄像机管理（摄像机模式、混合、上下文感知摄像机）
- **何时使用：** 需要动态摄像机行为的游戏（第三人称、瞄准、载具）
- **知识缺口：** UE 5.5 新增，完全在截止日期之后
- **状态：** ⚠️ 实验性（UE 5.5-5.7）
- **插件：** `GameplayCameras`（内置，在 Plugins 中启用）
- **详细文档：** [plugins/gameplay-camera-system.md](plugins/gameplay-camera-system.md)
- **官方：** https://docs.unrealengine.com/5.7/en-US/gameplay-cameras-in-unreal-engine/

---

### ✅ PCG (Procedural Content Generation)
### ✅ PCG（程序化内容生成）
- **Purpose:** Node-based procedural world generation (foliage, props, terrain details)
- **When to use:** Open worlds, procedural levels, large-scale environment population
- **Knowledge Gap:** Experimental in UE 5.0-5.6, production-ready in 5.7
- **Status:** Production-Ready (as of UE 5.7)
- **Plugin:** `PCG` (built-in, enable in Plugins)
- **Detailed Docs:** [plugins/pcg.md](plugins/pcg.md)
- **Official:** https://docs.unrealengine.com/5.7/en-US/procedural-content-generation-in-unreal-engine/

- **用途：** 基于节点的程序化世界生成（植被、道具、地形细节）
- **何时使用：** 开放世界、程序化关卡、大规模环境填充
- **知识缺口：** UE 5.0-5.6 中为实验性，5.7 中生产就绪
- **状态：** 生产就绪（自 UE 5.7 起）
- **插件：** `PCG`（内置，在 Plugins 中启用）
- **详细文档：** [plugins/pcg.md](plugins/pcg.md)
- **官方：** https://docs.unrealengine.com/5.7/en-US/procedural-content-generation-in-unreal-engine/

---

## Other Production-Ready Plugins (Brief Overview)
## 其他生产就绪插件（简要概述）

### 🟡 Mass Entity
### 🟡 Mass Entity
- **Purpose:** High-performance ECS for large-scale AI/crowds (10,000+ entities)
- **When to use:** RTS, city simulators, massive crowds, large-scale AI
- **Status:** Production-Ready (UE 5.1+)
- **Plugin:** `MassEntity`, `MassGameplay`, `MassCrowd`
- **Official:** https://docs.unrealengine.com/5.7/en-US/mass-entity-in-unreal-engine/

- **用途：** 面向大规模 AI/人群的高性能 ECS（10,000+ 实体）
- **何时使用：** RTS、城市模拟、大规模人群、大规模 AI
- **状态：** 生产就绪（UE 5.1+）
- **插件：** `MassEntity`、`MassGameplay`、`MassCrowd`
- **官方：** https://docs.unrealengine.com/5.7/en-US/mass-entity-in-unreal-engine/

---

### 🟡 Niagara Fluids
### 🟡 Niagara Fluids
- **Purpose:** GPU fluid simulation (smoke, fire, liquids)
- **When to use:** Realistic fire/smoke effects, water simulation
- **Status:** Experimental → Production-Ready (UE 5.4+)
- **Plugin:** `NiagaraFluids` (built-in)
- **Official:** https://docs.unrealengine.com/5.7/en-US/niagara-fluids-in-unreal-engine/

- **用途：** GPU 流体模拟（烟雾、火焰、液体）
- **何时使用：** 逼真的火焰/烟雾效果、水体模拟
- **状态：** 实验性 → 生产就绪（UE 5.4+）
- **插件：** `NiagaraFluids`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/niagara-fluids-in-unreal-engine/

---

### 🟡 Water Plugin
### 🟡 Water 插件
- **Purpose:** Ocean, river, lake rendering with buoyancy
- **When to use:** Games with water bodies, boats, swimming
- **Status:** Production-Ready (UE 5.0+)
- **Plugin:** `Water` (built-in)
- **Official:** https://docs.unrealengine.com/5.7/en-US/water-system-in-unreal-engine/

- **用途：** 海洋、河流、湖泊渲染，带浮力
- **何时使用：** 包含水体、船只、游泳的游戏
- **状态：** 生产就绪（UE 5.0+）
- **插件：** `Water`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/water-system-in-unreal-engine/

---

### 🟡 Landmass Plugin
### 🟡 Landmass 插件
- **Purpose:** Terrain sculpting and landscape editing
- **When to use:** Large-scale terrain modification, procedural landscapes
- **Status:** Production-Ready
- **Plugin:** `Landmass` (built-in)
- **Official:** https://docs.unrealengine.com/5.7/en-US/landmass-plugin-in-unreal-engine/

- **用途：** 地形雕刻与地表编辑
- **何时使用：** 大规模地形修改、程序化地貌
- **状态：** 生产就绪
- **插件：** `Landmass`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/landmass-plugin-in-unreal-engine/

---

### 🟡 Chaos Destruction
### 🟡 Chaos Destruction
- **Purpose:** Real-time fracture and destruction
- **When to use:** Destructible environments (walls, buildings, objects)
- **Status:** Production-Ready (UE 5.0+)
- **Plugin:** `ChaosDestruction` (built-in)
- **Official:** https://docs.unrealengine.com/5.7/en-US/destruction-in-unreal-engine/

- **用途：** 实时破碎与破坏
- **何时使用：** 可破坏环境（墙壁、建筑、物体）
- **状态：** 生产就绪（UE 5.0+）
- **插件：** `ChaosDestruction`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/destruction-in-unreal-engine/

---

### 🟡 Chaos Vehicle
### 🟡 Chaos Vehicle
- **Purpose:** Advanced vehicle physics (wheeled vehicles, suspension)
- **When to use:** Racing games, vehicle-heavy gameplay
- **Status:** Production-Ready (replaces PhysX Vehicles)
- **Plugin:** `ChaosVehicles` (built-in)
- **Official:** https://docs.unrealengine.com/5.7/en-US/chaos-vehicles-overview-in-unreal-engine/

- **用途：** 高级载具物理（轮式载具、悬挂）
- **何时使用：** 竞速游戏、载具密集型玩法
- **状态：** 生产就绪（取代 PhysX Vehicles）
- **插件：** `ChaosVehicles`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/chaos-vehicles-overview-in-unreal-engine/

---

### 🟡 Geometry Scripting
### 🟡 Geometry Scripting
- **Purpose:** Runtime procedural mesh generation and editing
- **When to use:** Dynamic mesh creation, procedural modeling
- **Status:** Production-Ready (UE 5.1+)
- **Plugin:** `GeometryScripting` (built-in)
- **Official:** https://docs.unrealengine.com/5.7/en-US/geometry-scripting-in-unreal-engine/

- **用途：** 运行时程序化网格生成与编辑
- **何时使用：** 动态网格创建、程序化建模
- **状态：** 生产就绪（UE 5.1+）
- **插件：** `GeometryScripting`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/geometry-scripting-in-unreal-engine/

---

### 🟡 Motion Design Tools
### 🟡 Motion Design 工具
- **Purpose:** Motion graphics, procedural animation, keyframe animation
- **When to use:** UI animations, procedural motion, keyframed sequences
- **Status:** Experimental → Production-Ready (UE 5.4+)
- **Plugin:** `MotionDesign` (built-in)
- **Official:** https://docs.unrealengine.com/5.7/en-US/motion-design-mode-in-unreal-engine/

- **用途：** 动效图形、程序化动画、关键帧动画
- **何时使用：** UI 动画、程序化运动、关键帧序列
- **状态：** 实验性 → 生产就绪（UE 5.4+）
- **插件：** `MotionDesign`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/motion-design-mode-in-unreal-engine/

---

## Experimental Plugins (Use with Caution)
## 实验性插件（谨慎使用）

### ⚠️ AI Assistant (UE 5.7+)
### ⚠️ AI 助手（UE 5.7+）
- **Purpose:** In-editor AI guidance and help
- **Status:** Experimental
- **Plugin:** Enable in UE 5.7 settings
- **Official:** Announced in UE 5.7 release

- **用途：** 编辑器内 AI 指导与帮助
- **状态：** 实验性
- **插件：** 在 UE 5.7 设置中启用
- **官方：** 在 UE 5.7 发布中宣布

---

### ⚠️ OpenXR (VR/AR)
### ⚠️ OpenXR（VR/AR）
- **Purpose:** Cross-platform VR/AR support
- **When to use:** VR/AR games
- **Status:** Production-Ready for VR, Experimental for AR
- **Plugin:** `OpenXR` (built-in)
- **Official:** https://docs.unrealengine.com/5.7/en-US/openxr-in-unreal-engine/

- **用途：** 跨平台 VR/AR 支持
- **何时使用：** VR/AR 游戏
- **状态：** VR 生产就绪，AR 实验性
- **插件：** `OpenXR`（内置）
- **官方：** https://docs.unrealengine.com/5.7/en-US/openxr-in-unreal-engine/

---

### ⚠️ Online Subsystem (EOS, Steam, etc.)
### ⚠️ Online Subsystem（EOS、Steam 等）
- **Purpose:** Platform-agnostic online services (matchmaking, friends, achievements)
- **When to use:** Multiplayer games with online features
- **Status:** Production-Ready
- **Plugin:** `OnlineSubsystem`, `OnlineSubsystemEOS`, `OnlineSubsystemSteam`
- **Official:** https://docs.unrealengine.com/5.7/en-US/online-subsystem-in-unreal-engine/

- **用途：** 与平台无关的在线服务（匹配、好友、成就）
- **何时使用：** 带在线功能的多人游戏
- **状态：** 生产就绪
- **插件：** `OnlineSubsystem`、`OnlineSubsystemEOS`、`OnlineSubsystemSteam`
- **官方：** https://docs.unrealengine.com/5.7/en-US/online-subsystem-in-unreal-engine/

---

## Deprecated Plugins (Avoid for New Projects)
## 弃用插件（新项目避免使用）

### ❌ PhysX Vehicles
### ❌ PhysX Vehicles
- **Deprecated:** Use Chaos Vehicles instead
- **Status:** Legacy, not recommended

- **弃用：** 改用 Chaos Vehicles
- **状态：** 旧版，不推荐

---

### ❌ Old Replication Graph
### ❌ 旧版 Replication Graph
- **Deprecated:** Replaced by Iris (UE 5.1+)
- **Status:** Use Iris for modern networking

- **弃用：** 被 Iris 取代（UE 5.1+）
- **状态：** 现代网络请使用 Iris

---

## On-Demand WebSearch Strategy
## 按需 WebSearch 策略

For plugins NOT listed above, use the following approach when users ask:

对于上面未列出的插件，当用户询问时，使用以下方法：

1. **WebSearch** for latest documentation: `"Unreal Engine 5.7 [plugin name]"`
2. Verify if plugin is:
   - Post-cutoff (beyond May 2025 training data)
   - Experimental vs Production-Ready
   - Still supported in UE 5.7
3. Optionally cache findings in `plugins/[plugin-name].md` for future reference

1. **WebSearch** 查找最新文档：`"Unreal Engine 5.7 [插件名称]"`
2. 验证插件是否：
   - 在截止日期之后（超出 2025 年 5 月训练数据）
   - 实验性还是生产就绪
   - 在 UE 5.7 中仍受支持
3. 可选择将发现缓存到 `plugins/[plugin-name].md` 以供将来参考

---

## Quick Decision Guide
## 快速决策指南

**I need abilities/skills/buffs** → **Gameplay Ability System (GAS)**
**I need cross-platform UI (console + PC)** → **CommonUI**
**I need dynamic cameras** → **Gameplay Camera System**
**I need procedural worlds** → **PCG**
**I need large crowds (1000s of AI)** → **Mass Entity**
**I need destructible environments** → **Chaos Destruction**
**I need vehicles** → **Chaos Vehicles**
**I need water/oceans** → **Water Plugin**
**I need VR/AR** → **OpenXR**

**我需要能力/技能/增益** → **Gameplay Ability System (GAS)**
**我需要跨平台 UI（主机 + PC）** → **CommonUI**
**我需要动态摄像机** → **Gameplay Camera System**
**我需要程序化世界** → **PCG**
**我需要大规模人群（数千 AI）** → **Mass Entity**
**我需要可破坏环境** → **Chaos Destruction**
**我需要载具** → **Chaos Vehicles**
**我需要水体/海洋** → **Water 插件**
**我需要 VR/AR** → **OpenXR**

---

**Last Updated:** 2026-02-13
**Engine Version:** Unreal Engine 5.7
**LLM Knowledge Cutoff:** May 2025

**最后更新：** 2026-02-13
**引擎版本：** Unreal Engine 5.7
**LLM 知识截止：** 2025 年 5 月
