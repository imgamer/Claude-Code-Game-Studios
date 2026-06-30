# Unreal Engine 5.7 — Deprecated APIs
# Unreal Engine 5.7 — 弃用的 API

**Last verified:** 2026-02-13
**最后验证：** 2026-02-13

Quick lookup table for deprecated APIs and their replacements.
Format: **Don't use X** → **Use Y instead**

弃用 API 及其替代方案的快速查询表。
格式：**不要使用 X** → **改用 Y**

---

## Input
## 输入

| Deprecated | Replacement | Notes |
|------------|-------------|-------|
| `InputComponent->BindAction()` | Enhanced Input `BindAction()` | New input system |
| `InputComponent->BindAxis()` | Enhanced Input `BindAxis()` | New input system |
| `PlayerController->GetInputAxisValue()` | Enhanced Input Action Values | New input system |

| 弃用 | 替代 | 说明 |
|------------|-------------|-------|
| `InputComponent->BindAction()` | Enhanced Input `BindAction()` | 新输入系统 |
| `InputComponent->BindAxis()` | Enhanced Input `BindAxis()` | 新输入系统 |
| `PlayerController->GetInputAxisValue()` | Enhanced Input Action Values | 新输入系统 |

**Migration:** Install Enhanced Input plugin, create Input Actions and Input Mapping Contexts.

**迁移：** 安装 Enhanced Input 插件，创建 Input Action 和 Input Mapping Context。

---

## Rendering
## 渲染

| Deprecated | Replacement | Notes |
|------------|-------------|-------|
| Legacy material nodes | Substrate material nodes | Substrate is production-ready in 5.7 |
| Forward shading (default) | Deferred + Lumen | Lumen is default in UE5 |
| Old lighting workflow | Lumen Global Illumination | Real-time GI |

| 弃用 | 替代 | 说明 |
|------------|-------------|-------|
| 旧版材质节点 | Substrate 材质节点 | Substrate 在 5.7 中生产就绪 |
| Forward shading（默认） | Deferred + Lumen | Lumen 是 UE5 默认 |
| 旧版光照工作流 | Lumen 全局光照 | 实时 GI |

---

## World Building
## 世界构建

| Deprecated | Replacement | Notes |
|------------|-------------|-------|
| UE4 World Composition | World Partition (UE5) | Streaming large worlds |
| Level Streaming Volumes | World Partition Data Layers | Better level streaming |

| 弃用 | 替代 | 说明 |
|------------|-------------|-------|
| UE4 World Composition | World Partition (UE5) | 流送大型世界 |
| Level Streaming Volumes | World Partition Data Layers | 更好的关卡流送 |

---

## Animation
## 动画

| Deprecated | Replacement | Notes |
|------------|-------------|-------|
| Old animation retargeting | IK Rig + IK Retargeter | UE5 retargeting system |
| Legacy control rig | Control Rig 2.0 | Production-ready rigging |

| 弃用 | 替代 | 说明 |
|------------|-------------|-------|
| 旧版动画重定向 | IK Rig + IK Retargeter | UE5 重定向系统 |
| 旧版 control rig | Control Rig 2.0 | 生产就绪的绑定 |

---

## Gameplay
## 玩法

| Deprecated | Replacement | Notes |
|------------|-------------|-------|
| `UGameplayStatics::LoadStreamLevel()` | World Partition streaming | Use Data Layers |
| Hardcoded input bindings | Enhanced Input system | Rebindable, modular input |

| 弃用 | 替代 | 说明 |
|------------|-------------|-------|
| `UGameplayStatics::LoadStreamLevel()` | World Partition 流送 | 使用 Data Layers |
| 硬编码输入绑定 | Enhanced Input 系统 | 可重绑定、模块化输入 |

---

## Niagara (VFX)
## Niagara（VFX）

| Deprecated | Replacement | Notes |
|------------|-------------|-------|
| Cascade particle system | Niagara | Cascade is fully deprecated |

| 弃用 | 替代 | 说明 |
|------------|-------------|-------|
| Cascade 粒子系统 | Niagara | Cascade 已完全弃用 |

---

## Audio
## 音频

| Deprecated | Replacement | Notes |
|------------|-------------|-------|
| Old audio mixer | MetaSounds | Procedural audio system |
| Sound Cue (for complex logic) | MetaSounds | More powerful, node-based |

| 弃用 | 替代 | 说明 |
|------------|-------------|-------|
| 旧版音频混音器 | MetaSounds | 程序化音频系统 |
| Sound Cue（用于复杂逻辑） | MetaSounds | 更强大、基于节点 |

---

## Networking
## 网络

| Deprecated | Replacement | Notes |
|------------|-------------|-------|
| `DOREPLIFETIME()` (basic) | `DOREPLIFETIME_CONDITION()` | Conditional replication for optimization |

| 弃用 | 替代 | 说明 |
|------------|-------------|-------|
| `DOREPLIFETIME()`（基础） | `DOREPLIFETIME_CONDITION()` | 条件复制以进行优化 |

---

## C++ Scripting
## C++ 脚本

| Deprecated | Replacement | Notes |
|------------|-------------|-------|
| `TSharedPtr<T>` for UObjects | `TObjectPtr<T>` | UE5 type-safe pointers |
| Manual RTTI checks | `Cast<T>()` / `IsA<T>()` | Type-safe casting |

| 弃用 | 替代 | 说明 |
|------------|-------------|-------|
| 对 UObject 使用 `TSharedPtr<T>` | `TObjectPtr<T>` | UE5 类型安全指针 |
| 手动 RTTI 检查 | `Cast<T>()` / `IsA<T>()` | 类型安全转换 |

---

## Quick Migration Patterns
## 快速迁移模式

### Input Example
### 输入示例
```cpp
// ❌ Deprecated
void AMyCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent) {
    PlayerInputComponent->BindAction("Jump", IE_Pressed, this, &ACharacter::Jump);
}

// ✅ Enhanced Input
#include "EnhancedInputComponent.h"

void AMyCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent) {
    UEnhancedInputComponent* EIC = Cast<UEnhancedInputComponent>(PlayerInputComponent);
    if (EIC) {
        EIC->BindAction(JumpAction, ETriggerEvent::Started, this, &ACharacter::Jump);
    }
}
```

### Material Example
### 材质示例
```cpp
// ❌ Deprecated: Legacy material
// Use standard material graph (still works but not recommended)

// ✅ Substrate Material
// Enable: Project Settings > Engine > Substrate > Enable Substrate
// Use Substrate nodes in material editor
```

### World Partition Example
### World Partition 示例
```cpp
// ❌ Deprecated: Level streaming volumes
// Load/unload levels manually

// ✅ World Partition
// Enable: World Settings > Enable World Partition
// Use Data Layers for streaming
```

### Particle System Example
### 粒子系统示例
```cpp
// ❌ Deprecated: Cascade
UParticleSystemComponent* PSC = CreateDefaultSubobject<UParticleSystemComponent>(TEXT("Particles"));

// ✅ Niagara
UNiagaraComponent* NiagaraComp = CreateDefaultSubobject<UNiagaraComponent>(TEXT("Niagara"));
```

### Audio Example
### 音频示例
```cpp
// ❌ Deprecated: Sound Cue for complex logic
// Use Sound Cue editor nodes

// ✅ MetaSounds
// Create MetaSound Source asset, use node-based audio
```

---

## Summary: UE 5.7 Tech Stack
## 总结：UE 5.7 技术栈

| Feature | Use This (2026) | Avoid This (Legacy) |
|---------|------------------|----------------------|
| **Input** | Enhanced Input | Legacy Input Bindings |
| **Materials** | Substrate | Legacy Material System |
| **Lighting** | Lumen + Megalights | Lightmaps + Limited Lights |
| **Particles** | Niagara | Cascade |
| **Audio** | MetaSounds | Sound Cue (for logic) |
| **World Streaming** | World Partition | World Composition |
| **Animation Retarget** | IK Rig + Retargeter | Old Retargeting |
| **Geometry** | Nanite (high-poly) | Standard Static Mesh LODs |

| 特性 | 使用此项（2026） | 避免使用（旧版） |
|---------|------------------|----------------------|
| **输入** | Enhanced Input | 旧版输入绑定 |
| **材质** | Substrate | 旧版材质系统 |
| **光照** | Lumen + Megalights | 光照贴图 + 有限光源 |
| **粒子** | Niagara | Cascade |
| **音频** | MetaSounds | Sound Cue（用于逻辑） |
| **世界流送** | World Partition | World Composition |
| **动画重定向** | IK Rig + Retargeter | 旧版重定向 |
| **几何体** | Nanite（高多边形） | 标准静态网格 LOD |

---

**Sources:**
**来源：**
- https://docs.unrealengine.com/5.7/en-US/deprecated-and-removed-features/
- https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-7-release-notes
