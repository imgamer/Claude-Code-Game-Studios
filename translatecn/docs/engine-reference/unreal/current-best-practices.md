# Unreal Engine 5.7 — Current Best Practices
# Unreal Engine 5.7 — 当前最佳实践

**Last verified:** 2026-02-13
**最后验证：** 2026-02-13

Modern UE5 patterns that may not be in the LLM's training data.
These are production-ready recommendations as of UE 5.7.

现代 UE5 模式，可能不在 LLM 的训练数据中。这些是截至 UE 5.7 的生产就绪建议。

---

## Project Setup
## 项目设置

### Use UE 5.7 for New Projects
### 新项目使用 UE 5.7
- Latest features: Megalights, production-ready Substrate and PCG
- Better performance and stability

- 最新特性：Megalights、生产就绪的 Substrate 和 PCG
- 更好的性能与稳定性

### Choose the Right Rendering Features
### 选择正确的渲染特性
- **Lumen**: Real-time global illumination (RECOMMENDED for most projects)
- **Nanite**: Virtualized geometry for high-poly meshes (RECOMMENDED for detailed environments)
- **Megalights**: Millions of dynamic lights (RECOMMENDED for complex lighting)
- **Substrate**: Modular material system (RECOMMENDED for new projects)

- **Lumen**：实时全局光照（推荐用于大多数项目）
- **Nanite**：用于高多边形网格的虚拟化几何体（推荐用于精细环境）
- **Megalights**：数百万动态光源（推荐用于复杂光照）
- **Substrate**：模块化材质系统（推荐用于新项目）

---

## C++ Coding
## C++ 编码

### Use Modern C++ Features (C++20 in UE5.7)
### 使用现代 C++ 特性（UE5.7 中的 C++20）

```cpp
// ✅ Use TObjectPtr<T> (UE5 type-safe pointers)
UPROPERTY()
TObjectPtr<UStaticMeshComponent> MeshComp;

// ✅ Structured bindings
if (auto [bSuccess, Value] = TryGetValue(); bSuccess) {
    // Use Value
}

// ✅ Concepts and constraints (C++20)
template<typename T>
concept Damageable = requires(T t, float damage) {
    { t.TakeDamage(damage) } -> std::same_as<void>;
};
```

### Use UPROPERTY() for Garbage Collection
### 使用 UPROPERTY() 进行垃圾回收

```cpp
// ✅ UPROPERTY ensures GC doesn't delete this
UPROPERTY()
TObjectPtr<AActor> MyActor;

// ❌ Raw pointers can become dangling
AActor* MyActor; // Dangerous! May be garbage collected
```

### Use UFUNCTION() for Blueprint Exposure
### 使用 UFUNCTION() 暴露给 Blueprint

```cpp
// ✅ Callable from Blueprint
UFUNCTION(BlueprintCallable, Category="Combat")
void TakeDamage(float Damage);

// ✅ Implementable in Blueprint
UFUNCTION(BlueprintImplementableEvent, Category="Combat")
void OnDeath();
```

---

## Blueprint Best Practices
## Blueprint 最佳实践

### Use Blueprint vs C++
### Blueprint 与 C++ 的取舍

- **C++**: Core gameplay systems, performance-critical code, low-level engine interaction
- **Blueprint**: Rapid prototyping, content creation, data-driven logic, designer workflows

- **C++**：核心玩法系统、性能关键代码、底层引擎交互
- **Blueprint**：快速原型设计、内容创作、数据驱动逻辑、设计师工作流

### Blueprint Performance Tips
### Blueprint 性能提示

```cpp
// ✅ Use Event Tick sparingly (expensive)
// Prefer timers or events

// ✅ Use Blueprint Nativization (Blueprints → C++)
// Project Settings > Packaging > Blueprint Nativization

// ✅ Cache frequently accessed components
// Don't call GetComponent every tick
```

---

## Rendering (UE 5.7)
## 渲染（UE 5.7）

### Use Lumen for Global Illumination
### 使用 Lumen 进行全局光照

```cpp
// Enable: Project Settings > Engine > Rendering > Dynamic Global Illumination Method = Lumen
// Real-time GI, no lightmap baking needed (RECOMMENDED)
```

### Use Nanite for High-Poly Meshes
### 对高多边形网格使用 Nanite

```cpp
// Enable on Static Mesh: Details > Nanite Settings > Enable Nanite Support
// Automatically LODs millions of triangles (RECOMMENDED for detailed meshes)
```

### Use Megalights for Complex Lighting (UE 5.5+)
### 对复杂光照使用 Megalights（UE 5.5+）

```cpp
// Enable: Project Settings > Engine > Rendering > Megalights = Enabled
// Supports millions of dynamic lights with minimal cost
```

### Use Substrate Materials (Production-Ready in 5.7)
### 使用 Substrate 材质（5.7 中生产就绪）

```cpp
// Enable: Project Settings > Engine > Substrate > Enable Substrate
// Modular, physically accurate materials (RECOMMENDED for new projects)
```

---

## Enhanced Input System
## Enhanced Input 系统

### Setup Enhanced Input
### 设置 Enhanced Input

```cpp
// 1. Create Input Action (IA_Jump)
// 2. Create Input Mapping Context (IMC_Default)
// 3. Add mapping: IA_Jump → Space Bar

// C++ Setup:
#include "EnhancedInputComponent.h"
#include "EnhancedInputSubsystems.h"

void AMyCharacter::BeginPlay() {
    Super::BeginPlay();

    if (APlayerController* PC = Cast<APlayerController>(GetController())) {
        if (UEnhancedInputLocalPlayerSubsystem* Subsystem =
            ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(PC->GetLocalPlayer())) {
            Subsystem->AddMappingContext(DefaultMappingContext, 0);
        }
    }
}

void AMyCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent) {
    UEnhancedInputComponent* EIC = Cast<UEnhancedInputComponent>(PlayerInputComponent);
    EIC->BindAction(JumpAction, ETriggerEvent::Started, this, &ACharacter::Jump);
    EIC->BindAction(MoveAction, ETriggerEvent::Triggered, this, &AMyCharacter::Move);
}

void AMyCharacter::Move(const FInputActionValue& Value) {
    FVector2D MoveVector = Value.Get<FVector2D>();
    AddMovementInput(GetActorForwardVector(), MoveVector.Y);
    AddMovementInput(GetActorRightVector(), MoveVector.X);
}
```

---

## Gameplay Ability System (GAS)
## Gameplay Ability System (GAS)

### Use GAS for Complex Gameplay
### 复杂玩法使用 GAS

```cpp
// ✅ Use GAS for: Abilities, buffs, damage calculation, cooldowns
// Modular, scalable, multiplayer-ready

// Install: Enable "Gameplay Abilities" plugin

// Example Ability:
UCLASS()
class UGA_Fireball : public UGameplayAbility {
    GENERATED_BODY()

public:
    virtual void ActivateAbility(...) override {
        // Ability logic
        SpawnFireball();
        CommitAbility(); // Commit cost/cooldown
    }
};
```

---

## World Partition (Large Worlds)
## World Partition（大型世界）

### Use World Partition for Open Worlds
### 开放世界使用 World Partition

```cpp
// Enable: World Settings > Enable World Partition
// Automatically streams world cells based on player location

// Data Layers: Organize content (e.g., "Gameplay", "Audio", "Lighting")
// Runtime Data Layers: Load/unload at runtime
```

---

## Niagara (VFX)
## Niagara（VFX）

### Use Niagara (Not Cascade)
### 使用 Niagara（而非 Cascade）

```cpp
// Create: Content Browser > Right Click > FX > Niagara System
// GPU-accelerated, node-based particle system (RECOMMENDED)

// Spawn particles:
UNiagaraComponent* NiagaraComp = UNiagaraFunctionLibrary::SpawnSystemAtLocation(
    GetWorld(),
    ExplosionSystem,
    GetActorLocation()
);
```

---

## MetaSounds (Audio)
## MetaSounds（音频）

### Use MetaSounds for Procedural Audio
### 程序化音频使用 MetaSounds

```cpp
// Create: Content Browser > Right Click > Sounds > MetaSound Source
// Node-based audio, replaces Sound Cue for complex logic (RECOMMENDED)

// Play MetaSound:
UAudioComponent* AudioComp = UGameplayStatics::SpawnSound2D(
    GetWorld(),
    MetaSoundSource
);
```

---

## Replication (Multiplayer)
## 复制（多人游戏）

### Server-Authoritative Pattern
### 服务器权威模式

```cpp
// ✅ Client sends input, server validates and replicates
UFUNCTION(Server, Reliable)
void Server_Move(FVector Direction);

void AMyCharacter::Server_Move_Implementation(FVector Direction) {
    // Server validates and applies movement
    AddMovementInput(Direction);
}

// ✅ Replicate important state
UPROPERTY(Replicated)
int32 Health;

void AMyCharacter::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const {
    Super::GetLifetimeReplicatedProps(OutLifetimeProps);
    DOREPLIFETIME(AMyCharacter, Health);
}
```

---

## Performance Optimization
## 性能优化

### Use Object Pooling
### 使用对象池

```cpp
// ✅ Reuse objects instead of Spawn/Destroy
TArray<AActor*> ProjectilePool;

AActor* GetPooledProjectile() {
    for (AActor* Proj : ProjectilePool) {
        if (!Proj->IsActive()) {
            Proj->SetActive(true);
            return Proj;
        }
    }
    // Pool exhausted, spawn new
    return SpawnNewProjectile();
}
```

### Use Instanced Static Meshes
### 使用实例化静态网格

```cpp
// ✅ Hierarchical Instanced Static Mesh Component (HISM)
// Render thousands of identical meshes in one draw call
UHierarchicalInstancedStaticMeshComponent* HISM = CreateDefaultSubobject<UHierarchicalInstancedStaticMeshComponent>(TEXT("Trees"));
for (int i = 0; i < 1000; i++) {
    HISM->AddInstance(FTransform(RandomLocation));
}
```

---

## Debugging
## 调试

### Use Logging
### 使用日志

```cpp
// ✅ Structured logging
UE_LOG(LogTemp, Warning, TEXT("Player health: %d"), Health);

// Custom log category
DECLARE_LOG_CATEGORY_EXTERN(LogMyGame, Log, All);
DEFINE_LOG_CATEGORY(LogMyGame);
UE_LOG(LogMyGame, Error, TEXT("Critical error!"));
```

### Use Visual Logger
### 使用 Visual Logger

```cpp
// ✅ Visual debugging
#include "VisualLogger/VisualLogger.h"

UE_VLOG_SEGMENT(this, LogTemp, Log, StartPos, EndPos, FColor::Red, TEXT("Raycast"));
UE_VLOG_LOCATION(this, LogTemp, Log, TargetLocation, 50.f, FColor::Green, TEXT("Target"));
```

---

## Summary: UE 5.7 Recommended Stack
## 总结：UE 5.7 推荐技术栈

| Feature | Use This (2026) | Notes |
|---------|------------------|-------|
| **Lighting** | Lumen + Megalights | Real-time GI, millions of lights |
| **Geometry** | Nanite | High-poly meshes, automatic LOD |
| **Materials** | Substrate | Modular, physically accurate |
| **Input** | Enhanced Input | Rebindable, modular |
| **VFX** | Niagara | GPU-accelerated |
| **Audio** | MetaSounds | Procedural audio |
| **World Streaming** | World Partition | Large open worlds |
| **Gameplay** | Gameplay Ability System | Complex abilities, buffs |

| 特性 | 使用此项（2026） | 说明 |
|---------|------------------|-------|
| **光照** | Lumen + Megalights | 实时 GI、数百万光源 |
| **几何体** | Nanite | 高多边形网格、自动 LOD |
| **材质** | Substrate | 模块化、物理精确 |
| **输入** | Enhanced Input | 可重绑定、模块化 |
| **VFX** | Niagara | GPU 加速 |
| **音频** | MetaSounds | 程序化音频 |
| **世界流送** | World Partition | 大型开放世界 |
| **玩法** | Gameplay Ability System | 复杂能力、增益 |

---

**Sources:**
**来源：**
- https://docs.unrealengine.com/5.7/en-US/
- https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-7-release-notes
