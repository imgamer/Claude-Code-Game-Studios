# Unity 6.3 LTS — Current Best Practices
# Unity 6.3 LTS — 当前最佳实践

**Last verified:** 2026-02-13
**最后验证：** 2026-02-13

Modern Unity 6 patterns that may not be in the LLM's training data.
These are production-ready recommendations as of Unity 6.3 LTS.
LLM 训练数据中可能没有的现代 Unity 6 模式。
这些是截至 Unity 6.3 LTS 的生产就绪建议。

---

## Project Setup
## 项目设置

### Use Unity 6.3 LTS for Production
### 生产环境使用 Unity 6.3 LTS
- **Tech Stream** (6.4+): Latest features, less stable
- **LTS** (6.3): Production-ready, 2-year support (until Dec 2027)

- **Tech Stream**（6.4+）：最新特性，稳定性较低
- **LTS**（6.3）：生产就绪，2 年支持（至 2027 年 12 月）

### Choose the Right Render Pipeline
### 选择正确的渲染管线
- **URP (Universal)**: Mobile, cross-platform, good performance ✅ Recommended for most games
- **HDRP (High Definition)**: High-end PC/console, photorealistic
- **Built-in**: Deprecated, avoid for new projects

- **URP (Universal)**：移动端、跨平台、性能良好 ✅ 推荐用于大多数游戏
- **HDRP (High Definition)**：高端 PC/主机、照片级真实感
- **Built-in**：已弃用，新项目应避免使用

---

## Scripting
## 脚本编写

### Use C# 9+ Features (Unity 6 Supports C# 9)
### 使用 C# 9+ 特性（Unity 6 支持 C# 9）

```csharp
// ✅ Record types for data
public record PlayerData(string Name, int Level, float Health);

// ✅ Init-only properties
public class Config {
    public string GameMode { get; init; }
}

// ✅ Pattern matching
var result = enemy switch {
    Boss boss => boss.Enrage(),
    Minion minion => minion.Flee(),
    _ => null
};
```

### Async/Await for Asset Loading
### 资源加载使用 Async/Await

```csharp
// ✅ Modern async pattern
public async Task<GameObject> LoadEnemyAsync(string key) {
    var handle = Addressables.LoadAssetAsync<GameObject>(key);
    return await handle.Task;
}
```

### Use Source Generators for Serialization (Unity 6+)
### 使用 Source Generators 进行序列化（Unity 6+）

```csharp
// ✅ Source-generated serialization (faster, less reflection)
[GenerateSerializer]
public partial struct PlayerStats : IComponentData {
    public int Health;
    public int Mana;
}
```

---

## DOTS/ECS (Production-Ready in Unity 6.3 LTS)
## DOTS/ECS（在 Unity 6.3 LTS 中生产就绪）

### Use ISystem (Not ComponentSystem)
### 使用 ISystem（而非 ComponentSystem）

```csharp
// ✅ Modern unmanaged ISystem (Burst-compatible)
public partial struct MovementSystem : ISystem {
    public void OnCreate(ref SystemState state) { }

    public void OnUpdate(ref SystemState state) {
        foreach (var (transform, speed) in
            SystemAPI.Query<RefRW<LocalTransform>, RefRO<MoveSpeed>>()) {
            transform.ValueRW.Position += speed.ValueRO.Value * SystemAPI.Time.DeltaTime;
        }
    }
}
```

### Use IJobEntity for Parallel Jobs
### 为并行作业使用 IJobEntity

```csharp
// ✅ IJobEntity (replaces IJobForEach)
[BurstCompile]
public partial struct DamageJob : IJobEntity {
    public float DeltaTime;

    void Execute(ref Health health, in DamageOverTime dot) {
        health.Value -= dot.DamagePerSecond * DeltaTime;
    }
}

// Schedule it
var job = new DamageJob { DeltaTime = SystemAPI.Time.DeltaTime };
job.ScheduleParallel();
```

---

## Input
## 输入

### Use Input System Package (Not Legacy Input)
### 使用 Input System 包（而非旧版 Input）

```csharp
// ✅ Input Actions (rebindable, cross-platform)
using UnityEngine.InputSystem;

public class PlayerInput : MonoBehaviour {
    private PlayerControls controls;

    void Awake() {
        controls = new PlayerControls();
        controls.Gameplay.Jump.performed += ctx => Jump();
    }

    void OnEnable() => controls.Enable();
    void OnDisable() => controls.Disable();
}
```

Create Input Actions asset in editor, generate C# class via inspector.
在编辑器中创建 Input Actions 资源，通过 inspector 生成 C# 类。

---

## UI
## UI

### Use UI Toolkit for Runtime UI (Production-Ready in Unity 6)
### 为运行时 UI 使用 UI Toolkit（Unity 6 中生产就绪）

```csharp
// ✅ UI Toolkit (replaces UGUI for new projects)
using UnityEngine.UIElements;

public class MainMenu : MonoBehaviour {
    void OnEnable() {
        var root = GetComponent<UIDocument>().rootVisualElement;

        var playButton = root.Q<Button>("play-button");
        playButton.clicked += StartGame;

        var scoreLabel = root.Q<Label>("score");
        scoreLabel.text = $"High Score: {PlayerPrefs.GetInt("HighScore")}";
    }
}
```

**UXML** (UI structure) + **USS** (styling) = HTML/CSS-like workflow.
**UXML**（UI 结构）+ **USS**（样式）= 类 HTML/CSS 的工作流。

---

## Asset Management
## 资源管理

### Use Addressables (Not Resources)
### 使用 Addressables（而非 Resources）

```csharp
// ✅ Addressables (async, memory-efficient)
using UnityEngine.AddressableAssets;

public async Task SpawnEnemyAsync(string enemyKey) {
    var handle = Addressables.InstantiateAsync(enemyKey);
    var enemy = await handle.Task;

    // Cleanup: release when destroyed
    Addressables.ReleaseInstance(enemy);
}
```

**Benefits:** Async loading, remote content delivery, better memory control.
**优势：** 异步加载、远程内容分发、更好的内存控制。

---

## Rendering
## 渲染

### Use RenderGraph API for Custom Passes (URP/HDRP)
### 为自定义通道使用 RenderGraph API（URP/HDRP）

```csharp
// ✅ RenderGraph API (Unity 6+)
public override void RecordRenderGraph(RenderGraph renderGraph, ContextContainer frameData) {
    using (var builder = renderGraph.AddRasterRenderPass<PassData>("My Pass", out var passData)) {
        // Setup pass
        builder.SetRenderFunc((PassData data, RasterGraphContext context) => {
            // Execute commands
        });
    }
}
```

**Replaces:** Old `CommandBuffer.Execute()` pattern.
**替代：** 旧的 `CommandBuffer.Execute()` 模式。

---

## Performance
## 性能

### Use Burst Compiler + Jobs System
### 使用 Burst Compiler + Jobs System

```csharp
// ✅ Burst-compiled job (massive performance gain)
[BurstCompile]
struct ParticleUpdateJob : IJobParallelFor {
    public NativeArray<float3> Positions;
    public NativeArray<float3> Velocities;
    public float DeltaTime;

    public void Execute(int index) {
        Positions[index] += Velocities[index] * DeltaTime;
    }
}

// Schedule
var job = new ParticleUpdateJob {
    Positions = positions,
    Velocities = velocities,
    DeltaTime = Time.deltaTime
};
job.Schedule(positions.Length, 64).Complete();
```

**20-100x faster** than equivalent C# code.
比等效的 C# 代码快 **20-100 倍**。

---

### Use GPU Instancing for Repeated Objects
### 为重复对象使用 GPU Instancing

```csharp
// ✅ GPU Instancing (thousands of objects, minimal draw calls)
Graphics.RenderMeshInstanced(
    new RenderParams(material),
    mesh,
    0,
    matrices // NativeArray<Matrix4x4>
);
```

---

## Memory Management
## 内存管理

### Use NativeContainers (Not Managed Arrays in Jobs)
### 使用 NativeContainers（作业中不要使用托管数组）

```csharp
// ✅ NativeArray (no GC, Burst-compatible)
NativeArray<int> data = new NativeArray<int>(1000, Allocator.TempJob);
// ... use in job
data.Dispose(); // Manual cleanup required

// ✅ Or use using statement
using var data = new NativeArray<int>(1000, Allocator.TempJob);
// Auto-disposed
```

---

## Multiplayer
## 多人游戏

### Use Netcode for GameObjects (Official)
### 使用 Netcode for GameObjects（官方）

```csharp
// ✅ Unity's official netcode
using Unity.Netcode;

public class Player : NetworkBehaviour {
    private NetworkVariable<int> health = new NetworkVariable<int>(100);

    [ServerRpc]
    public void TakeDamageServerRpc(int damage) {
        health.Value -= damage;
    }
}
```

**Replaces:** UNet (deprecated), MLAPI (renamed to Netcode for GameObjects).
**替代：** UNet（已弃用）、MLAPI（已更名为 Netcode for GameObjects）。

---

## Testing
## 测试

### Use Unity Test Framework (NUnit-based)
### 使用 Unity Test Framework（基于 NUnit）

```csharp
// ✅ Play Mode Test
[UnityTest]
public IEnumerator Player_TakesDamage_HealthDecreases() {
    var player = new GameObject().AddComponent<Player>();
    player.Health = 100;

    player.TakeDamage(25);
    yield return null; // Wait one frame

    Assert.AreEqual(75, player.Health);
}
```

---

## Debugging
## 调试

### Use Logging Best Practices
### 使用日志最佳实践

```csharp
// ✅ Structured logging (Unity 6+)
using UnityEngine;

Debug.Log($"Player {playerName} scored {score} points");

// ✅ Conditional compilation for debug code
#if UNITY_EDITOR || DEVELOPMENT_BUILD
    Debug.DrawRay(transform.position, direction, Color.red);
#endif
```

---

## Summary: Unity 6 Tech Stack
## 总结：Unity 6 技术栈

| Feature | Use This (2026) | Avoid This (Legacy) |
|---------|------------------|----------------------|
| **Input** | Input System package | `Input` class |
| **UI** | UI Toolkit | UGUI (Canvas) |
| **ECS** | ISystem + IJobEntity | ComponentSystem |
| **Rendering** | URP + RenderGraph | Built-in pipeline |
| **Assets** | Addressables | Resources |
| **Jobs** | Burst + IJobParallelFor | Coroutines for heavy work |
| **Multiplayer** | Netcode for GameObjects | UNet |

| 特性 | 使用此项（2026） | 避免此项（旧版） |
|---------|------------------|----------------------|
| **Input** | Input System 包 | `Input` 类 |
| **UI** | UI Toolkit | UGUI (Canvas) |
| **ECS** | ISystem + IJobEntity | ComponentSystem |
| **Rendering** | URP + RenderGraph | Built-in 管线 |
| **Assets** | Addressables | Resources |
| **Jobs** | Burst + IJobParallelFor | 用协程处理繁重工作 |
| **Multiplayer** | Netcode for GameObjects | UNet |

---

**Sources:**
**来源：**
- https://docs.unity3d.com/6000.0/Documentation/Manual/BestPracticeGuides.html
- https://docs.unity3d.com/Packages/com.unity.entities@1.3/manual/index.html
- https://docs.unity3d.com/Packages/com.unity.inputsystem@1.11/manual/index.html
