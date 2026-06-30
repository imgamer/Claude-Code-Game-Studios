# Unreal Engine 5.7 — Breaking Changes
# Unreal Engine 5.7 — 破坏性变更

**Last verified:** 2026-02-13
**最后验证：** 2026-02-13

This document tracks breaking API changes and behavioral differences between Unreal Engine 5.3
(likely in model training) and Unreal Engine 5.7 (current version). Organized by risk level.

本文档记录了 Unreal Engine 5.3（可能在模型训练中）与 Unreal Engine 5.7（当前版本）之间的破坏性 API 变更和行为差异。按风险等级组织。

## HIGH RISK — Will Break Existing Code
## 高风险 — 会破坏现有代码

### Substrate Material System (Production-Ready in 5.7)
### Substrate 材质系统（5.7 中生产就绪）
**Versions:** UE 5.5+ (experimental), 5.7 (production-ready)
**版本：** UE 5.5+（实验性），5.7（生产就绪）

Substrate replaces the legacy material system with a modular, physically accurate framework.

Substrate 用模块化、物理精确的框架取代了旧版材质系统。

```cpp
// ❌ OLD: Legacy material nodes (still work but deprecated)
// Standard material graph with Base Color, Metallic, Roughness, etc.

// ✅ NEW: Substrate material layers
// Use Substrate nodes: Substrate Slab, Substrate Blend, etc.
// Modular material authoring with true physical accuracy
```

**Migration:** Enable Substrate in `Project Settings > Engine > Substrate` and rebuild materials using Substrate nodes.

**迁移：** 在 `Project Settings > Engine > Substrate` 中启用 Substrate，并使用 Substrate 节点重新构建材质。

---

### PCG (Procedural Content Generation) API Overhaul
### PCG（程序化内容生成）API 重构
**Versions:** UE 5.7 (production-ready)
**版本：** UE 5.7（生产就绪）

PCG framework reached production-ready status with major API changes.

PCG 框架达到生产就绪状态，伴随重大 API 变更。

```cpp
// ❌ OLD: Experimental PCG API (pre-5.7)
// Old node types, unstable API

// ✅ NEW: Production PCG API (5.7+)
// Use FPCGContext, IPCGElement, new node types
// Stable API, production-ready workflow
```

**Migration:** Follow PCG migration guide in 5.7 docs. Expect significant refactoring for experimental PCG code.

**迁移：** 遵循 5.7 文档中的 PCG 迁移指南。对于实验性 PCG 代码，预计需要大量重构。

---

### Megalights Rendering System
### Megalights 渲染系统
**Versions:** UE 5.5+
**版本：** UE 5.5+

New lighting system supports millions of dynamic lights.

新光照系统支持数百万动态光源。

```cpp
// ❌ OLD: Limited dynamic lights (clustered forward shading)
// Max ~100-200 dynamic lights before performance degrades

// ✅ NEW: Megalights (5.5+)
// Millions of dynamic lights with minimal performance cost
// Enable: Project Settings > Engine > Rendering > Megalights
```

**Migration:** No code changes needed, but lighting behavior may differ. Test scenes after enabling.

**迁移：** 无需代码变更，但光照行为可能有所不同。启用后请测试场景。

---

## MEDIUM RISK — Behavioral Changes
## 中等风险 — 行为变更

### Enhanced Input System (Now Default)
### Enhanced Input 系统（现为默认）
**Versions:** UE 5.1+ (recommended), 5.7 (default)
**版本：** UE 5.1+（推荐），5.7（默认）

Enhanced Input is now the default input system.

Enhanced Input 现在是默认输入系统。

```cpp
// ❌ OLD: Legacy input bindings (deprecated)
InputComponent->BindAction("Jump", IE_Pressed, this, &ACharacter::Jump);

// ✅ NEW: Enhanced Input
SetupPlayerInputComponent(UInputComponent* PlayerInputComponent) {
    UEnhancedInputComponent* EIC = Cast<UEnhancedInputComponent>(PlayerInputComponent);
    EIC->BindAction(JumpAction, ETriggerEvent::Started, this, &ACharacter::Jump);
}
```

**Migration:** Replace legacy input bindings with Enhanced Input actions.

**迁移：** 用 Enhanced Input 动作替换旧版输入绑定。

---

### Nanite Default Enabled
### Nanite 默认启用
**Versions:** UE 5.0+ (optional), 5.7 (encouraged)
**版本：** UE 5.0+（可选），5.7（鼓励）

Nanite virtualized geometry is now the recommended workflow for static meshes.

Nanite 虚拟化几何体现在是静态网格的推荐工作流。

```cpp
// Enable Nanite on static mesh:
// Static Mesh Editor > Details > Nanite Settings > Enable Nanite Support
```

**Migration:** Convert high-poly meshes to Nanite. Test performance on target platforms.

**迁移：** 将高多边形网格转换为 Nanite。在目标平台上测试性能。

---

## LOW RISK — Deprecations (Still Functional)
## 低风险 — 弃用（仍可使用）

### Legacy Material System
### 旧版材质系统
**Status:** Deprecated but supported
**Replacement:** Substrate Material System
**状态：** 已弃用但仍受支持
**替代：** Substrate 材质系统

Legacy materials still work, but Substrate is recommended for new projects.

旧版材质仍可使用，但新项目推荐使用 Substrate。

---

### Old World Partition (UE4 Style)
### 旧版 World Partition（UE4 风格）
**Status:** Deprecated
**Replacement:** World Partition (UE5+)
**状态：** 已弃用
**替代：** World Partition（UE5+）

Use UE5's World Partition system for large worlds.

对于大型世界，使用 UE5 的 World Partition 系统。

---

## Platform-Specific Breaking Changes
## 平台相关的破坏性变更

### Windows
### Windows
- **UE 5.7**: DirectX 12 is now default (was DX11 in older versions)
- Update shaders for DX12 compatibility

- **UE 5.7**：DirectX 12 现为默认（旧版本中为 DX11）
- 更新着色器以兼容 DX12

### macOS
### macOS
- **UE 5.5+**: Metal 3 required (minimum macOS 13)

- **UE 5.5+**：需要 Metal 3（最低 macOS 13）

### Mobile
### 移动端
- **UE 5.7**: Minimum Android API level raised to 26 (Android 8.0)
- Minimum iOS deployment target raised to iOS 14

- **UE 5.7**：最低 Android API 级别提升至 26（Android 8.0）
- 最低 iOS 部署目标提升至 iOS 14

---

## Migration Checklist
## 迁移清单

When upgrading from UE 5.3 to UE 5.7:

从 UE 5.3 升级到 UE 5.7 时：

- [ ] Review Substrate materials (convert if ready for new system)
- [ ] Audit PCG usage (update to production API if using experimental)
- [ ] Test Megalights performance (enable and benchmark)
- [ ] Migrate legacy input to Enhanced Input
- [ ] Convert high-poly meshes to Nanite
- [ ] Update shaders for DX12 (Windows) or Metal 3 (macOS)
- [ ] Verify minimum platform versions (Android 8.0, iOS 14)
- [ ] Test Lumen and Nanite performance on target hardware

- [ ] 审查 Substrate 材质（如准备好迁移到新系统则进行转换）
- [ ] 审查 PCG 使用情况（如使用实验性版本则更新到生产 API）
- [ ] 测试 Megalights 性能（启用并基准测试）
- [ ] 将旧版输入迁移到 Enhanced Input
- [ ] 将高多边形网格转换为 Nanite
- [ ] 更新着色器以适配 DX12（Windows）或 Metal 3（macOS）
- [ ] 验证最低平台版本（Android 8.0、iOS 14）
- [ ] 在目标硬件上测试 Lumen 和 Nanite 性能

---

**Sources:**
**来源：**
- https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-7-release-notes
- https://dev.epicgames.com/documentation/en-us/unreal-engine/upgrading-projects-to-newer-versions-of-unreal-engine
