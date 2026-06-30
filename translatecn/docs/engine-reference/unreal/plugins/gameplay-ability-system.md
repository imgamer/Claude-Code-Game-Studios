# Unreal Engine 5.7 — Gameplay Ability System (GAS)
# Unreal Engine 5.7 — Gameplay Ability System (GAS)

**Last verified:** 2026-02-13
**Status:** Production-Ready
**Plugin:** `GameplayAbilities` (built-in, enable in Plugins)
**最后验证：** 2026-02-13
**状态：** 生产就绪
**插件：** `GameplayAbilities`（内置，在 Plugins 中启用）

---

## Overview
## 概述

**Gameplay Ability System (GAS)** is a modular framework for building abilities, attributes,
effects, and gameplay mechanics. It's the standard for RPGs, MOBAs, shooters with abilities,
and any game with complex ability systems.

**Gameplay Ability System (GAS)** 是一个模块化框架，用于构建能力、属性、效果和玩法机制。它是 RPG、MOBA、带能力的射击游戏以及任何具有复杂能力系统的游戏的标准。

**Use GAS for:**
- Character abilities (spells, skills, attacks)
- Attributes (health, mana, stamina, stats)
- Buffs/debuffs (temporary effects)
- Cooldowns and costs
- Damage calculation
- Multiplayer-ready ability replication

**使用 GAS 的场景：**
- 角色能力（法术、技能、攻击）
- 属性（生命、法力、耐力、属性值）
- 增益/减益（临时效果）
- 冷却与消耗
- 伤害计算
- 多人游戏就绪的能力复制

---

## Core Concepts
## 核心概念

### 1. **Ability System Component** (ASC)
### 1. **Ability System Component** (ASC)
- The main component that owns abilities, attributes, and effects
- Added to Characters or PlayerStates

- 拥有能力、属性和效果的主要组件
- 添加到 Character 或 PlayerState 上

### 2. **Gameplay Abilities**
### 2. **Gameplay Abilities**
- Individual skills/actions (fireball, heal, dash, etc.)
- Activated, committed (cost/cooldown), and can be cancelled

- 单个技能/动作（火球、治疗、冲刺等）
- 可被激活、提交（消耗/冷却），并可以取消

### 3. **Attributes & Attribute Sets**
### 3. **Attributes 与 Attribute Sets**
- Stats that can be modified (Health, Mana, Stamina, Strength, etc.)
- Stored in Attribute Sets

- 可修改的属性值（Health、Mana、Stamina、Strength 等）
- 存储在 Attribute Set 中

### 4. **Gameplay Effects**
### 4. **Gameplay Effects**
- Modify attributes (damage, healing, buffs, debuffs)
- Can be instant, duration-based, or infinite

- 修改属性（伤害、治疗、增益、减益）
- 可以是即时、基于持续时间或无限

### 5. **Gameplay Tags**
### 5. **Gameplay Tags**
- Hierarchical tags for ability logic (e.g., `Ability.Attack.Melee`, `Status.Stunned`)

- 用于能力逻辑的分层标签（例如 `Ability.Attack.Melee`、`Status.Stunned`）

---

## Setup
## 设置

### 1. Enable Plugin
### 1. 启用插件

`Edit > Plugins > Gameplay Abilities > Enabled > Restart`

### 2. Add Ability System Component
### 2. 添加 Ability System Component

```cpp
#include "AbilitySystemComponent.h"
#include "AttributeSet.h"

UCLASS()
class AMyCharacter : public ACharacter {
    GENERATED_BODY()

public:
    AMyCharacter() {
        // Create ASC
        AbilitySystemComponent = CreateDefaultSubobject<UAbilitySystemComponent>(TEXT("AbilitySystem"));
        AbilitySystemComponent->SetIsReplicated(true);
        AbilitySystemComponent->SetReplicationMode(EGameplayEffectReplicationMode::Mixed);

        // Create Attribute Set
        AttributeSet = CreateDefaultSubobject<UMyAttributeSet>(TEXT("AttributeSet"));
    }

protected:
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Abilities")
    TObjectPtr<UAbilitySystemComponent> AbilitySystemComponent;

    UPROPERTY()
    TObjectPtr<const UAttributeSet> AttributeSet;
};
```

### 3. Initialize ASC (Important for Multiplayer)
### 3. 初始化 ASC（对多人游戏很重要）

```cpp
void AMyCharacter::PossessedBy(AController* NewController) {
    Super::PossessedBy(NewController);

    // Server: Initialize ASC
    if (AbilitySystemComponent) {
        AbilitySystemComponent->InitAbilityActorInfo(this, this);
        GiveDefaultAbilities();
    }
}

void AMyCharacter::OnRep_PlayerState() {
    Super::OnRep_PlayerState();

    // Client: Initialize ASC
    if (AbilitySystemComponent) {
        AbilitySystemComponent->InitAbilityActorInfo(this, this);
    }
}
```

---

## Attributes & Attribute Sets
## 属性与 Attribute Set

### Create Attribute Set
### 创建 Attribute Set

```cpp
#include "AttributeSet.h"
#include "AbilitySystemComponent.h"

UCLASS()
class UMyAttributeSet : public UAttributeSet {
    GENERATED_BODY()

public:
    UMyAttributeSet();

    // Health
    UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Health)
    FGameplayAttributeData Health;
    ATTRIBUTE_ACCESSORS(UMyAttributeSet, Health)

    UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_MaxHealth)
    FGameplayAttributeData MaxHealth;
    ATTRIBUTE_ACCESSORS(UMyAttributeSet, MaxHealth)

    // Mana
    UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Mana)
    FGameplayAttributeData Mana;
    ATTRIBUTE_ACCESSORS(UMyAttributeSet, Mana)

    virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const override;

protected:
    UFUNCTION()
    virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);

    UFUNCTION()
    virtual void OnRep_MaxHealth(const FGameplayAttributeData& OldMaxHealth);

    UFUNCTION()
    virtual void OnRep_Mana(const FGameplayAttributeData& OldMana);
};
```

### Implement Attribute Set
### 实现 Attribute Set

```cpp
#include "Net/UnrealNetwork.h"

UMyAttributeSet::UMyAttributeSet() {
    // Default values
    Health = 100.0f;
    MaxHealth = 100.0f;
    Mana = 50.0f;
}

void UMyAttributeSet::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const {
    Super::GetLifetimeReplicatedProps(OutLifetimeProps);

    DOREPLIFETIME_CONDITION_NOTIFY(UMyAttributeSet, Health, COND_None, REPNOTIFY_Always);
    DOREPLIFETIME_CONDITION_NOTIFY(UMyAttributeSet, MaxHealth, COND_None, REPNOTIFY_Always);
    DOREPLIFETIME_CONDITION_NOTIFY(UMyAttributeSet, Mana, COND_None, REPNOTIFY_Always);
}

void UMyAttributeSet::OnRep_Health(const FGameplayAttributeData& OldHealth) {
    GAMEPLAYATTRIBUTE_REPNOTIFY(UMyAttributeSet, Health, OldHealth);
}

// Implement other OnRep functions similarly...
```

---

## Gameplay Abilities
## Gameplay Ability

### Create Gameplay Ability
### 创建 Gameplay Ability

```cpp
#include "Abilities/GameplayAbility.h"

UCLASS()
class UGA_Fireball : public UGameplayAbility {
    GENERATED_BODY()

public:
    UGA_Fireball() {
        // Ability config
        InstancingPolicy = EGameplayAbilityInstancingPolicy::InstancedPerActor;
        NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::ServerInitiated;

        // Tags
        AbilityTags.AddTag(FGameplayTag::RequestGameplayTag(FName("Ability.Attack.Fireball")));
    }

    virtual void ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo,
        const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData) override {

        if (!CommitAbility(Handle, ActorInfo, ActivationInfo)) {
            // Failed to commit (not enough mana, on cooldown, etc.)
            EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
            return;
        }

        // Spawn fireball projectile
        SpawnFireball();

        // End ability
        EndAbility(Handle, ActorInfo, ActivationInfo, true, false);
    }

    void SpawnFireball() {
        // Spawn fireball logic
    }
};
```

### Grant Abilities to Character
### 将能力授予角色

```cpp
void AMyCharacter::GiveDefaultAbilities() {
    if (!HasAuthority() || !AbilitySystemComponent) return;

    // Grant abilities
    AbilitySystemComponent->GiveAbility(FGameplayAbilitySpec(UGA_Fireball::StaticClass(), 1, INDEX_NONE, this));
    AbilitySystemComponent->GiveAbility(FGameplayAbilitySpec(UGA_Heal::StaticClass(), 1, INDEX_NONE, this));
}
```

### Activate Ability
### 激活能力

```cpp
// Activate by class
AbilitySystemComponent->TryActivateAbilityByClass(UGA_Fireball::StaticClass());

// Activate by tag
FGameplayTagContainer TagContainer;
TagContainer.AddTag(FGameplayTag::RequestGameplayTag(FName("Ability.Attack.Fireball")));
AbilitySystemComponent->TryActivateAbilitiesByTag(TagContainer);
```

---

## Gameplay Effects
## Gameplay Effect

### Create Gameplay Effect (Damage)
### 创建 Gameplay Effect（伤害）

```cpp
// Create Blueprint: Content Browser > Gameplay > Gameplay Effect

// OR in C++:
UCLASS()
class UGE_Damage : public UGameplayEffect {
    GENERATED_BODY()

public:
    UGE_Damage() {
        // Instant damage
        DurationPolicy = EGameplayEffectDurationType::Instant;

        // Modifier: Reduce Health
        FGameplayModifierInfo ModifierInfo;
        ModifierInfo.Attribute = UMyAttributeSet::GetHealthAttribute();
        ModifierInfo.ModifierOp = EGameplayModOp::Additive;
        ModifierInfo.ModifierMagnitude = FScalableFloat(-25.0f); // -25 health

        Modifiers.Add(ModifierInfo);
    }
};
```

### Apply Gameplay Effect
### 应用 Gameplay Effect

```cpp
// Apply damage to target
if (UAbilitySystemComponent* TargetASC = UAbilitySystemBlueprintLibrary::GetAbilitySystemComponent(Target)) {
    FGameplayEffectContextHandle EffectContext = AbilitySystemComponent->MakeEffectContext();
    EffectContext.AddSourceObject(this);

    FGameplayEffectSpecHandle SpecHandle = AbilitySystemComponent->MakeOutgoingSpec(
        UGE_Damage::StaticClass(), 1, EffectContext);

    if (SpecHandle.IsValid()) {
        AbilitySystemComponent->ApplyGameplayEffectSpecToTarget(*SpecHandle.Data.Get(), TargetASC);
    }
}
```

---

## Gameplay Tags
## Gameplay Tag

### Define Tags
### 定义标签

`Project Settings > Project > Gameplay Tags > Gameplay Tag List`

Example hierarchy:
```
Ability
  ├─ Ability.Attack
  │   ├─ Ability.Attack.Melee
  │   └─ Ability.Attack.Ranged
  ├─ Ability.Defend
  └─ Ability.Utility

Status
  ├─ Status.Stunned
  ├─ Status.Invulnerable
  └─ Status.Silenced
```

示例层级：
```
Ability
  ├─ Ability.Attack
  │   ├─ Ability.Attack.Melee
  │   └─ Ability.Attack.Ranged
  ├─ Ability.Defend
  └─ Ability.Utility

Status
  ├─ Status.Stunned
  ├─ Status.Invulnerable
  └─ Status.Silenced
```

### Use Tags in Abilities
### 在能力中使用标签

```cpp
UCLASS()
class UGA_MeleeAttack : public UGameplayAbility {
    GENERATED_BODY()

public:
    UGA_MeleeAttack() {
        // This ability has these tags
        AbilityTags.AddTag(FGameplayTag::RequestGameplayTag(FName("Ability.Attack.Melee")));

        // Block these tags while active
        BlockAbilitiesWithTag.AddTag(FGameplayTag::RequestGameplayTag(FName("Ability.Attack")));

        // Cancel these abilities when activated
        CancelAbilitiesWithTag.AddTag(FGameplayTag::RequestGameplayTag(FName("Ability.Defend")));

        // Can't activate if target has these tags
        ActivationBlockedTags.AddTag(FGameplayTag::RequestGameplayTag(FName("Status.Stunned")));
    }
};
```

---

## Cooldowns & Costs
## 冷却与消耗

### Add Cooldown
### 添加冷却

```cpp
// In Ability Blueprint or C++:
// Create Gameplay Effect with Duration = Cooldown time
// Assign to Ability > Cooldown Gameplay Effect Class
```

### Add Cost (Mana)
### 添加消耗（法力）

```cpp
// Create Gameplay Effect that reduces Mana
// Assign to Ability > Cost Gameplay Effect Class
```

---

## Common Patterns
## 常见模式

### Get Current Attribute Value
### 获取当前属性值

```cpp
float CurrentHealth = AbilitySystemComponent->GetNumericAttribute(UMyAttributeSet::GetHealthAttribute());
```

### Listen for Attribute Changes
### 监听属性变化

```cpp
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(UMyAttributeSet::GetHealthAttribute())
    .AddUObject(this, &AMyCharacter::OnHealthChanged);

void AMyCharacter::OnHealthChanged(const FOnAttributeChangeData& Data) {
    UE_LOG(LogTemp, Warning, TEXT("Health: %f"), Data.NewValue);
}
```

---

## Sources
## 来源
- https://docs.unrealengine.com/5.7/en-US/gameplay-ability-system-for-unreal-engine/
- https://github.com/tranek/GASDocumentation (community guide)
- https://github.com/tranek/GASDocumentation（社区指南）
