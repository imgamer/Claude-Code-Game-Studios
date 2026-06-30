# Unreal Engine 5.7 — Gameplay Camera System
# Unreal Engine 5.7 — Gameplay Camera System

**Last verified:** 2026-02-13
**Status:** ⚠️ Experimental (introduced in UE 5.5)
**Plugin:** `GameplayCameras` (built-in, enable in Plugins)
**最后验证：** 2026-02-13
**状态：** ⚠️ 实验性（UE 5.5 引入）
**插件：** `GameplayCameras`（内置，在 Plugins 中启用）

---

## Overview
## 概述

**Gameplay Camera System** is a modular camera management framework introduced in UE 5.5.
It replaces traditional camera setups with a flexible, node-based system that handles
camera modes, blending, and context-aware camera behavior.

**Gameplay Camera System** 是 UE 5.5 引入的模块化摄像机管理框架。它用灵活的、基于节点的系统取代了传统摄像机设置，可处理摄像机模式、混合和上下文感知的摄像机行为。

**Use Gameplay Cameras for:**
- Dynamic camera behavior (3rd person, aiming, vehicles, cinematic)
- Context-aware camera switching (combat, exploration, dialogue)
- Smooth camera blending between modes
- Procedural camera motion (camera shake, lag, offset)

**使用 Gameplay Camera 的场景：**
- 动态摄像机行为（第三人称、瞄准、载具、电影镜头）
- 上下文感知的摄像机切换（战斗、探索、对话）
- 模式之间的平滑摄像机混合
- 程序化摄像机运动（摄像机抖动、延迟、偏移）

**⚠️ Warning:** This plugin is experimental in UE 5.5-5.7. Expect API changes in future versions.

**⚠️ 警告：** 此插件在 UE 5.5-5.7 中为实验性。未来版本预计会有 API 变更。

---

## Core Concepts
## 核心概念

### 1. **Camera Rig**
### 1. **Camera Rig**
- Defines camera configuration (position, rotation, FOV, etc.)
- Modular node graph (similar to Material Editor)

- 定义摄像机配置（位置、旋转、FOV 等）
- 模块化节点图（类似于材质编辑器）

### 2. **Camera Director**
### 2. **Camera Director**
- Manages which camera rig is active
- Handles blending between camera rigs

- 管理哪个 Camera Rig 处于活动状态
- 处理 Camera Rig 之间的混合

### 3. **Camera Nodes**
### 3. **Camera Nodes**
- Building blocks for camera behavior:
  - **Position Nodes**: Orbit, Follow, Fixed Position
  - **Rotation Nodes**: Look At, Match Actor Rotation
  - **Modifiers**: Camera Shake, Lag, Offset

- 摄像机行为的构建块：
  - **位置节点**：Orbit、Follow、Fixed Position
  - **旋转节点**：Look At、Match Actor Rotation
  - **修饰符**：Camera Shake、Lag、Offset

---

## Setup
## 设置

### 1. Enable Plugin
### 1. 启用插件

`Edit > Plugins > Gameplay Cameras > Enabled > Restart`

### 2. Add Camera Component
### 2. 添加摄像机组件

```cpp
#include "GameplayCameras/Public/GameplayCameraComponent.h"

UCLASS()
class AMyCharacter : public ACharacter {
    GENERATED_BODY()

public:
    AMyCharacter() {
        // Create camera component
        CameraComponent = CreateDefaultSubobject<UGameplayCameraComponent>(TEXT("GameplayCamera"));
        CameraComponent->SetupAttachment(RootComponent);
    }

protected:
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Camera")
    TObjectPtr<UGameplayCameraComponent> CameraComponent;
};
```

---

## Create Camera Rig
## 创建 Camera Rig

### 1. Create Camera Rig Asset
### 1. 创建 Camera Rig 资产

1. Content Browser > Gameplay > Gameplay Camera Rig
2. Open Camera Rig Editor (node-based graph)

1. Content Browser > Gameplay > Gameplay Camera Rig
2. 打开 Camera Rig 编辑器（基于节点的图）

### 2. Build Camera Rig (Example: Third Person)
### 2. 构建 Camera Rig（示例：第三人称）

**Node Setup:**
**节点设置：**
```
Actor Position (Character)
  ↓
Orbit Node (Orbit around character)
  ↓
Offset Node (Shoulder offset)
  ↓
Look At Node (Look at character)
  ↓
Camera Output
```

---

## Camera Nodes
## 摄像机节点

### Position Nodes
### 位置节点

#### Orbit Node (Third Person)
#### Orbit Node（第三人称）
- Orbits around target actor
- Configure:
  - **Orbit Distance**: Distance from target (e.g., 300 units)
  - **Pitch Range**: Min/Max pitch angles
  - **Yaw Range**: Min/Max yaw angles

- 围绕目标 actor 轨道运动
- 配置：
  - **Orbit Distance**：与目标的距离（例如 300 单位）
  - **Pitch Range**：最小/最大俯仰角
  - **Yaw Range**：最小/最大偏航角

#### Follow Node (Smooth Follow)
#### Follow Node（平滑跟随）
- Follows target with lag
- Configure:
  - **Lag Speed**: How quickly camera catches up
  - **Offset**: Fixed offset from target

- 带延迟地跟随目标
- 配置：
  - **Lag Speed**：摄像机追上的速度
  - **Offset**：相对目标的固定偏移

#### Fixed Position Node
#### Fixed Position Node
- Static camera position in world space

- 世界空间中的静态摄像机位置

---

### Rotation Nodes
### 旋转节点

#### Look At Node
#### Look At Node
- Points camera at target
- Configure:
  - **Target**: Actor or component to look at
  - **Offset**: Look-at offset (e.g., aim at head instead of feet)

- 将摄像机指向目标
- 配置：
  - **Target**：要注视的 actor 或组件
  - **Offset**：注视偏移（例如瞄准头部而非脚部）

#### Match Actor Rotation
#### Match Actor Rotation
- Matches target actor's rotation
- Useful for first-person or vehicle cameras

- 匹配目标 actor 的旋转
- 适用于第一人称或载具摄像机

---

### Modifier Nodes
### 修饰符节点

#### Camera Shake
#### Camera Shake
- Adds procedural shake (e.g., footsteps, explosions)
- Configure:
  - **Shake Pattern**: Perlin noise, sine wave, custom
  - **Amplitude**: Shake strength

- 添加程序化抖动（例如脚步、爆炸）
- 配置：
  - **Shake Pattern**：Perlin 噪声、正弦波、自定义
  - **Amplitude**：抖动强度

#### Camera Lag
#### Camera Lag
- Smooth dampening of camera movement
- Configure:
  - **Lag Speed**: Damping factor (0 = instant, higher = more lag)

- 摄像机运动的平滑阻尼
- 配置：
  - **Lag Speed**：阻尼系数（0 = 即时，越大延迟越多）

#### Offset Node
#### Offset Node
- Static offset from calculated position
- Useful for shoulder camera offset

- 相对计算位置的静态偏移
- 适用于肩部摄像机偏移

---

## Camera Director (Switching Between Rigs)
## Camera Director（在 Rig 之间切换）

### Assign Camera Rig
### 分配 Camera Rig

```cpp
#include "GameplayCameras/Public/GameplayCameraComponent.h"

void AMyCharacter::SetCameraMode(UGameplayCameraRig* NewRig) {
    if (CameraComponent) {
        CameraComponent->SetCameraRig(NewRig);
    }
}
```

### Blend Between Camera Rigs
### 在 Camera Rig 之间混合

```cpp
// Blend to aiming camera over 0.5 seconds
CameraComponent->BlendToCameraRig(AimingCameraRig, 0.5f);
```

---

## Example: Third Person + Aiming
## 示例：第三人称 + 瞄准

### 1. Create Two Camera Rigs
### 1. 创建两个 Camera Rig

**Third Person Rig:**
**第三人称 Rig：**
```
Actor Position → Orbit (distance: 300) → Look At → Output
```

**Aiming Rig:**
**瞄准 Rig：**
```
Actor Position → Orbit (distance: 150) → Offset (shoulder) → Look At → Output
```

### 2. Switch on Aim
### 2. 瞄准时切换

```cpp
UPROPERTY(EditAnywhere, Category = "Camera")
TObjectPtr<UGameplayCameraRig> ThirdPersonRig;

UPROPERTY(EditAnywhere, Category = "Camera")
TObjectPtr<UGameplayCameraRig> AimingRig;

void StartAiming() {
    CameraComponent->BlendToCameraRig(AimingRig, 0.3f); // Blend over 0.3s
}

void StopAiming() {
    CameraComponent->BlendToCameraRig(ThirdPersonRig, 0.3f);
}
```

---

## Common Patterns
## 常见模式

### Over-the-Shoulder Camera
### 越肩摄像机

```
Actor Position
  ↓
Orbit Node (distance: 250, yaw offset: 30°)
  ↓
Offset Node (X: 0, Y: 50, Z: 50) // Shoulder offset
  ↓
Look At Node (target: Character head)
  ↓
Output
```

---

### Vehicle Camera
### 载具摄像机

```
Vehicle Position
  ↓
Follow Node (lag: 0.2)
  ↓
Offset Node (behind vehicle: X: -400, Z: 150)
  ↓
Look At Node (target: Vehicle)
  ↓
Output
```

---

### First Person Camera
### 第一人称摄像机

```
Character Head Socket
  ↓
Match Actor Rotation
  ↓
Output
```

---

## Camera Shake
## 摄像机抖动

### Trigger Camera Shake
### 触发摄像机抖动

```cpp
#include "GameplayCameras/Public/GameplayCameraShake.h"

void TriggerExplosionShake() {
    if (APlayerController* PC = GetWorld()->GetFirstPlayerController()) {
        if (UGameplayCameraComponent* CameraComp = PC->FindComponentByClass<UGameplayCameraComponent>()) {
            CameraComp->PlayCameraShake(ExplosionShakeClass, 1.0f);
        }
    }
}
```

---

## Performance Tips
## 性能提示

- Limit camera shake frequency (don't trigger every frame)
- Use camera lag sparingly (expensive for high lag values)
- Cache camera rig references (don't search every frame)

- 限制摄像机抖动频率（不要每帧触发）
- 谨慎使用摄像机延迟（高延迟值开销大）
- 缓存 Camera Rig 引用（不要每帧搜索）

---

## Debugging
## 调试

### Camera Debug Visualization
### 摄像机调试可视化

```cpp
// Console commands:
// GameplayCameras.Debug 1 - Show active camera rig info
// showdebug camera - Show camera debug info
```

---

## Migration from Legacy Cameras
## 从旧版摄像机迁移

### Old Spring Arm + Camera Component
### 旧版 Spring Arm + Camera Component

```cpp
// ❌ OLD: Spring Arm Component
USpringArmComponent* SpringArm;
UCameraComponent* Camera;

// ✅ NEW: Gameplay Camera Component
UGameplayCameraComponent* CameraComponent;
// Build orbit + look-at rig in Camera Rig asset
```

---

## Limitations (Experimental Status)
## 限制（实验性状态）

- **API Instability**: Expect breaking changes in UE 5.8+
- **Limited Documentation**: Official docs still evolving
- **Blueprint Support**: Primarily C++ focused (Blueprint support improving)
- **Production Risk**: Test thoroughly before shipping

- **API 不稳定性**：预计 UE 5.8+ 会有破坏性变更
- **文档有限**：官方文档仍在完善
- **Blueprint 支持**：主要面向 C++（Blueprint 支持正在改进）
- **生产风险**：发布前需充分测试

---

## Sources
## 来源
- https://docs.unrealengine.com/5.7/en-US/gameplay-cameras-in-unreal-engine/
- UE 5.5+ Release Notes
- UE 5.5+ 发行说明
- **Note:** This system is experimental. Always check latest official docs for API changes.
- **注意：** 此系统为实验性。请始终查看最新官方文档以了解 API 变更。
