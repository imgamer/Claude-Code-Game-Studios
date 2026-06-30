# Unreal Engine 5.7 — Physics Module Reference
# Unreal Engine 5.7 — 物理模块参考

**Last verified:** 2026-02-13
**Knowledge Gap:** UE 5.7 Chaos Physics improvements
**最后验证：** 2026-02-13
**知识缺口：** UE 5.7 Chaos Physics 改进

---

## Overview
## 概述

UE 5 uses **Chaos Physics** (replaced PhysX in UE 4):
- Better performance
- Destruction support
- Vehicle physics improvements

UE 5 使用 **Chaos Physics**（在 UE 4 中取代了 PhysX）：
- 更好的性能
- 支持破坏
- 载具物理改进

---

## Rigid Body Physics
## 刚体物理

### Enable Physics on Static Mesh
### 在静态网格上启用物理

```cpp
UStaticMeshComponent* MeshComp = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh"));
MeshComp->SetSimulatePhysics(true);
MeshComp->SetEnableGravity(true);
MeshComp->SetMassOverrideInKg(NAME_None, 50.0f); // 50 kg
```

### Apply Forces
### 施加力

```cpp
// Apply impulse (instant velocity change)
MeshComp->AddImpulse(FVector(0, 0, 1000), NAME_None, true);

// Apply force (continuous)
MeshComp->AddForce(FVector(0, 0, 500));

// Apply torque (rotation)
MeshComp->AddTorqueInRadians(FVector(0, 0, 100));
```

---

## Collision
## 碰撞

### Collision Channels
### 碰撞通道

```cpp
// Project Settings > Engine > Collision
// Define custom collision channels and responses

// Set collision in C++
MeshComp->SetCollisionEnabled(ECollisionEnabled::QueryAndPhysics);
MeshComp->SetCollisionObjectType(ECollisionChannel::ECC_Pawn);
MeshComp->SetCollisionResponseToAllChannels(ECR_Block);
MeshComp->SetCollisionResponseToChannel(ECC_Camera, ECR_Ignore);
```

### Collision Events
### 碰撞事件

```cpp
// Enable collision events
MeshComp->SetNotifyRigidBodyCollision(true);

// Bind to OnComponentHit
MeshComp->OnComponentHit.AddDynamic(this, &AMyActor::OnHit);

UFUNCTION()
void AMyActor::OnHit(UPrimitiveComponent* HitComp, AActor* OtherActor,
    UPrimitiveComponent* OtherComp, FVector NormalImpulse, const FHitResult& Hit) {
    UE_LOG(LogTemp, Warning, TEXT("Hit %s"), *OtherActor->GetName());
}
```

### Overlap Events
### 重叠事件

```cpp
// Enable overlap events
MeshComp->SetGenerateOverlapEvents(true);

// Bind to OnComponentBeginOverlap
MeshComp->OnComponentBeginOverlap.AddDynamic(this, &AMyActor::OnOverlapBegin);

UFUNCTION()
void AMyActor::OnOverlapBegin(UPrimitiveComponent* OverlappedComp, AActor* OtherActor,
    UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult) {
    UE_LOG(LogTemp, Warning, TEXT("Overlapped %s"), *OtherActor->GetName());
}
```

---

## Raycasting (Line Traces)
## 射线检测（线追踪）

### Single Line Trace
### 单线追踪

```cpp
FHitResult HitResult;
FVector Start = GetActorLocation();
FVector End = Start + GetActorForwardVector() * 1000.0f;

FCollisionQueryParams QueryParams;
QueryParams.AddIgnoredActor(this);

// Perform trace
bool bHit = GetWorld()->LineTraceSingleByChannel(
    HitResult,
    Start,
    End,
    ECC_Visibility,
    QueryParams
);

if (bHit) {
    UE_LOG(LogTemp, Warning, TEXT("Hit: %s"), *HitResult.GetActor()->GetName());
    DrawDebugLine(GetWorld(), Start, HitResult.Location, FColor::Red, false, 2.0f);
}
```

### Multi Line Trace
### 多线追踪

```cpp
TArray<FHitResult> HitResults;
bool bHit = GetWorld()->LineTraceMultiByChannel(
    HitResults,
    Start,
    End,
    ECC_Visibility,
    QueryParams
);

for (const FHitResult& Hit : HitResults) {
    UE_LOG(LogTemp, Warning, TEXT("Hit: %s"), *Hit.GetActor()->GetName());
}
```

### Sweep (Thick Trace)
### 扫描（粗追踪）

```cpp
FHitResult HitResult;
FCollisionShape Sphere = FCollisionShape::MakeSphere(50.0f);

bool bHit = GetWorld()->SweepSingleByChannel(
    HitResult,
    Start,
    End,
    FQuat::Identity,
    ECC_Visibility,
    Sphere,
    QueryParams
);
```

---

## Character Movement
## 角色移动

### Character Movement Component
### Character Movement Component

```cpp
// Built into ACharacter class
UCharacterMovementComponent* MoveComp = GetCharacterMovement();

// Configure movement
MoveComp->MaxWalkSpeed = 600.0f;
MoveComp->JumpZVelocity = 600.0f;
MoveComp->AirControl = 0.2f;
MoveComp->GravityScale = 1.0f;
MoveComp->bOrientRotationToMovement = true;
```

### Add Movement Input
### 添加移动输入

```cpp
// In Character class
void AMyCharacter::MoveForward(float Value) {
    if (Value != 0.0f) {
        AddMovementInput(GetActorForwardVector(), Value);
    }
}

void AMyCharacter::MoveRight(float Value) {
    if (Value != 0.0f) {
        AddMovementInput(GetActorRightVector(), Value);
    }
}
```

---

## Physical Materials
## 物理材质

### Create Physical Material
### 创建物理材质

1. Content Browser > Right Click > Physics > Physical Material
2. Configure properties:
   - Friction: 0.0 - 1.0
   - Restitution (bounciness): 0.0 - 1.0

1. Content Browser > 右键 > Physics > Physical Material
2. 配置属性：
   - Friction：0.0 - 1.0
   - Restitution（弹性）：0.0 - 1.0

### Assign Physical Material
### 分配物理材质

```cpp
// In static mesh editor: Physics > Phys Material Override
// Or in C++:
MeshComp->SetPhysMaterialOverride(PhysicalMaterial);
```

---

## Constraints (Physics Joints)
## 约束（物理关节）

### Physics Constraint Component
### Physics Constraint Component

```cpp
UPhysicsConstraintComponent* Constraint = CreateDefaultSubobject<UPhysicsConstraintComponent>(TEXT("Constraint"));
Constraint->SetConstrainedComponents(ComponentA, NAME_None, ComponentB, NAME_None);

// Configure constraint
Constraint->SetLinearXLimit(ELinearConstraintMotion::LCM_Limited, 100.0f);
Constraint->SetLinearYLimit(ELinearConstraintMotion::LCM_Locked, 0.0f);
Constraint->SetLinearZLimit(ELinearConstraintMotion::LCM_Free, 0.0f);

Constraint->SetAngularSwing1Limit(EAngularConstraintMotion::ACM_Limited, 45.0f);
```

---

## Destruction (Chaos Destruction)
## 破坏（Chaos Destruction）

### Enable Chaos Destruction
### 启用 Chaos Destruction

```cpp
// Plugin: Enable "Chaos" plugin
// Create Geometry Collection asset for destructible objects
```

### Destroy Geometry Collection
### 销毁 Geometry Collection

```cpp
// Fracture mesh in Chaos editor
// In game, apply damage:
UGeometryCollectionComponent* GeoComp = /* Get component */;
GeoComp->ApplyPhysicsField(/* Field parameters */);
```

---

## Performance Tips
## 性能提示

### Physics Optimization
### 物理优化

```cpp
// Simplify collision shapes (use simple primitives)
MeshComp->SetCollisionEnabled(ECollisionEnabled::NoCollision); // Disable when not needed

// Use Physics Asset for skeletal meshes (simplified collision)
// Don't simulate physics for distant objects

// Reduce physics substeps:
// Project Settings > Engine > Physics > Max Substep Delta Time
```

---

## Debugging
## 调试

### Physics Debug Visualization
### 物理调试可视化

```cpp
// Console commands:
// show collision - Show collision shapes
// p.Chaos.DebugDraw.Enabled 1 - Show Chaos debug
// pxvis collision - Visualize collision

// Draw debug shapes:
DrawDebugSphere(GetWorld(), Location, Radius, 12, FColor::Green, false, 2.0f);
DrawDebugBox(GetWorld(), Location, Extent, FColor::Red, false, 2.0f);
```

---

## Sources
## 来源
- https://docs.unrealengine.com/5.7/en-US/physics-in-unreal-engine/
- https://docs.unrealengine.com/5.7/en-US/chaos-physics-overview-in-unreal-engine/
