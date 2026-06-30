# Unity 6.3 — Navigation Module Reference
# Unity 6.3 — 导航模块参考

**Last verified:** 2026-02-13
**最后验证：** 2026-02-13
**Knowledge Gap:** Unity 6 NavMesh improvements
**知识盲区：** Unity 6 NavMesh 改进

---

## Overview
## 概述

Unity 6 navigation systems:
- **NavMesh**: Built-in pathfinding for AI agents
- **NavMeshComponents**: Package for runtime NavMesh building

Unity 6 导航系统：
- **NavMesh**：为 AI 智能体内置的寻路系统
- **NavMeshComponents**：用于运行时 NavMesh 构建的包

---

## NavMesh Basics
## NavMesh 基础

### Bake Navigation Mesh
### 烘焙导航网格

1. Mark walkable surfaces:
   - Select GameObject (floor/terrain)
   - Inspector > Navigation > Object tab
   - Check "Navigation Static"

1. 标记可行走表面：
   - 选择 GameObject（地板/地形）
   - Inspector > Navigation > Object 标签页
   - 勾选 "Navigation Static"

2. Bake NavMesh:
   - `Window > AI > Navigation`
   - Bake tab
   - Click "Bake"

2. 烘焙 NavMesh：
   - `Window > AI > Navigation`
   - Bake 标签页
   - 点击 "Bake"

3. Configure settings:
   - **Agent Radius**: How wide the agent is (0.5m default)
   - **Agent Height**: How tall the agent is (2m default)
   - **Max Slope**: Maximum walkable slope (45° default)
   - **Step Height**: Maximum climbable step (0.4m default)

3. 配置设置：
   - **Agent Radius**：智能体宽度（默认 0.5m）
   - **Agent Height**：智能体高度（默认 2m）
   - **Max Slope**：最大可行走坡度（默认 45°）
   - **Step Height**：最大可攀爬台阶（默认 0.4m）

---

## NavMeshAgent (AI Movement)
## NavMeshAgent（AI 移动）

### Basic Agent Setup
### 基本智能体设置

```csharp
using UnityEngine;
using UnityEngine.AI;

public class Enemy : MonoBehaviour {
    private NavMeshAgent agent;
    public Transform target;

    void Start() {
        agent = GetComponent<NavMeshAgent>();
    }

    void Update() {
        // ✅ Move to target
        agent.SetDestination(target.position);
    }
}
```

---

### NavMeshAgent Properties
### NavMeshAgent 属性

```csharp
NavMeshAgent agent = GetComponent<NavMeshAgent>();

// Speed
agent.speed = 3.5f;

// Acceleration
agent.acceleration = 8f;

// Stopping distance
agent.stoppingDistance = 2f; // Stop 2m before destination

// Auto-braking (slow down at destination)
agent.autoBraking = true;

// Rotation speed
agent.angularSpeed = 120f; // Degrees per second

// Obstacle avoidance
agent.obstacleAvoidanceType = ObstacleAvoidanceType.HighQualityObstacleAvoidance;
```

---

### Check Path Status
### 检查路径状态

```csharp
void Update() {
    agent.SetDestination(target.position);

    // Check if agent has a path
    if (agent.hasPath) {
        // Check if path is complete
        if (agent.pathStatus == NavMeshPathStatus.PathComplete) {
            Debug.Log("Valid path");
        } else if (agent.pathStatus == NavMeshPathStatus.PathPartial) {
            Debug.Log("Partial path (destination unreachable)");
        } else {
            Debug.Log("Invalid path");
        }
    }

    // Check if agent reached destination
    if (!agent.pathPending && agent.remainingDistance <= agent.stoppingDistance) {
        Debug.Log("Reached destination");
    }
}
```

---

### Calculate Path (Don't Move Yet)
### 计算路径（暂不移动）

```csharp
NavMeshPath path = new NavMeshPath();
agent.CalculatePath(targetPosition, path);

if (path.status == NavMeshPathStatus.PathComplete) {
    // Valid path exists
    agent.SetPath(path); // Apply the path
}
```

---

## NavMesh Areas (Walkable Costs)
## NavMesh 区域（可行走成本）

### Define Areas
### 定义区域
`Window > AI > Navigation > Areas tab`
- **Walkable**: Cost 1 (default)
- **Not Walkable**: Unwalkable
- **Jump**: Cost 2 (prefer other routes)
- **Custom**: Define your own

`Window > AI > Navigation > Areas 标签页`
- **Walkable**：成本 1（默认）
- **Not Walkable**：不可行走
- **Jump**：成本 2（优先选择其他路径）
- **Custom**：自定义

### Assign Area Costs
### 分配区域成本

```csharp
// Prefer shorter paths over low-cost paths
agent.areaMask = NavMesh.AllAreas; // Walk on all areas

// Only walk on "Walkable" area (avoid "Jump")
agent.areaMask = 1 << NavMesh.GetAreaFromName("Walkable");
```

---

## NavMesh Obstacles (Dynamic Obstacles)
## NavMesh 障碍物（动态障碍）

### NavMeshObstacle Component
### NavMeshObstacle 组件

```csharp
// Add: GameObject > Add Component > NavMesh Obstacle

// Carve: Create hole in NavMesh (agents avoid)
// Don't Carve: Agent pushes through (local avoidance)
```

### Dynamic Carving (Moving Obstacles)
### 动态雕刻（移动障碍）

```csharp
NavMeshObstacle obstacle = GetComponent<NavMeshObstacle>();
obstacle.carving = true; // Create dynamic hole in NavMesh
```

---

## Off-Mesh Links (Jumps, Teleports)
## Off-Mesh Links（跳跃、传送）

### Create Off-Mesh Link
### 创建 Off-Mesh Link

1. `GameObject > Create Empty` (at jump start)
2. Add `Off Mesh Link` component
3. Set Start/End transforms
4. Configure:
   - **Bi-Directional**: Can traverse both ways
   - **Cost Override**: Path cost for this link

1. `GameObject > Create Empty`（在跳跃起点）
2. 添加 `Off Mesh Link` 组件
3. 设置 Start/End 变换
4. 配置：
   - **Bi-Directional**：可双向通行
   - **Cost Override**：此链接的路径成本

### Detect Off-Mesh Link Traversal
### 检测 Off-Mesh Link 通行

```csharp
void Update() {
    // Check if agent is on an off-mesh link
    if (agent.isOnOffMeshLink) {
        // Manually traverse (e.g., play jump animation)
        StartCoroutine(TraverseOffMeshLink());
    }
}

IEnumerator TraverseOffMeshLink() {
    OffMeshLinkData data = agent.currentOffMeshLinkData;
    Vector3 startPos = agent.transform.position;
    Vector3 endPos = data.endPos;

    float duration = 0.5f;
    float elapsed = 0f;

    while (elapsed < duration) {
        agent.transform.position = Vector3.Lerp(startPos, endPos, elapsed / duration);
        elapsed += Time.deltaTime;
        yield return null;
    }

    agent.CompleteOffMeshLink(); // Resume normal pathfinding
}
```

---

## NavMeshComponents Package (Runtime Baking)
## NavMeshComponents 包（运行时烘焙）

### Installation
### 安装
1. `Window > Package Manager`
2. Add from Git URL: `com.unity.ai.navigation`

1. `Window > Package Manager`
2. 从 Git URL 添加：`com.unity.ai.navigation`

### Runtime NavMesh Baking
### 运行时 NavMesh 烘焙

```csharp
using Unity.AI.Navigation;

public class NavMeshBuilder : MonoBehaviour {
    public NavMeshSurface surface;

    void Start() {
        // Bake NavMesh at runtime
        surface.BuildNavMesh();
    }

    void UpdateNavMesh() {
        // Update NavMesh after terrain changes
        surface.UpdateNavMesh(surface.navMeshData);
    }
}
```

---

## Common Patterns
## 常见模式

### Patrol Between Waypoints
### 在路径点之间巡逻

```csharp
public Transform[] waypoints;
private int currentWaypoint = 0;

void Update() {
    if (!agent.pathPending && agent.remainingDistance < 0.5f) {
        // Reached waypoint, move to next
        currentWaypoint = (currentWaypoint + 1) % waypoints.Length;
        agent.SetDestination(waypoints[currentWaypoint].position);
    }
}
```

### Chase Player
### 追逐玩家

```csharp
public Transform player;
public float chaseRange = 10f;

void Update() {
    float distance = Vector3.Distance(transform.position, player.position);

    if (distance <= chaseRange) {
        agent.SetDestination(player.position);
    } else {
        agent.ResetPath(); // Stop moving
    }
}
```

### Flee from Player
### 逃离玩家

```csharp
public Transform player;
public float fleeRange = 5f;

void Update() {
    float distance = Vector3.Distance(transform.position, player.position);

    if (distance <= fleeRange) {
        // Run away from player
        Vector3 fleeDirection = transform.position - player.position;
        Vector3 fleeTarget = transform.position + fleeDirection.normalized * 10f;

        agent.SetDestination(fleeTarget);
    }
}
```

---

## Debugging
## 调试

### NavMesh Visualization
### NavMesh 可视化
- `Window > AI > Navigation > Bake tab`
- Check "Show NavMesh" to visualize walkable areas

- `Window > AI > Navigation > Bake 标签页`
- 勾选 "Show NavMesh" 可视化可行走区域

### Agent Path Gizmos
### 智能体路径 Gizmos

```csharp
void OnDrawGizmos() {
    if (agent != null && agent.hasPath) {
        Gizmos.color = Color.green;
        Vector3[] corners = agent.path.corners;

        for (int i = 0; i < corners.Length - 1; i++) {
            Gizmos.DrawLine(corners[i], corners[i + 1]);
        }
    }
}
```

---

## Performance Tips
## 性能提示

- **Limit Obstacle Avoidance Quality**: Use `LowQualityObstacleAvoidance` for distant agents
- **Update Frequency**: Don't call `SetDestination()` every frame if target hasn't moved
- **Area Masks**: Limit walkable areas to reduce pathfinding search space
- **NavMesh Tiles**: Use tiled NavMesh for large worlds (NavMeshComponents package)

- **限制障碍规避质量**：对远处智能体使用 `LowQualityObstacleAvoidance`
- **更新频率**：如果目标未移动，不要每帧调用 `SetDestination()`
- **区域掩码**：限制可行走区域以减少寻路搜索空间
- **NavMesh 切片**：大型世界使用切片 NavMesh（NavMeshComponents 包）

---

## Sources
## 来源
- https://docs.unity3d.com/6000.0/Documentation/Manual/Navigation.html
- https://docs.unity3d.com/Packages/com.unity.ai.navigation@2.0/manual/index.html
