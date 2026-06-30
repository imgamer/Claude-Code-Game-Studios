# Godot Networking — Quick Reference
# Godot 网络 — 快速参考

Last verified: 2026-02-12 | Engine: Godot 4.6
最后验证：2026-02-12 | 引擎：Godot 4.6

## What Changed Since ~4.3 (LLM Cutoff)
## 自约 4.3（LLM 截止）以来的变更

### 4.6 Changes
### 4.6 变更
- **Networking section in breaking changes**: See the official migration guide for
  specifics at the 4.5→4.6 level
- **破坏性变更中的网络部分**：有关 4.5→4.6 级别的具体内容，请参阅官方迁移指南

### 4.5 Changes
### 4.5 变更
- **No major networking API breaks** — core multiplayer API remains stable
- **没有重大的网络 API 破坏性变更** — 核心多人 API 保持稳定

## Current API Patterns
## 当前 API 模式

### High-Level Multiplayer
### 高级多人游戏
```gdscript
# Server
func host_game(port: int = 9999) -> void:
    var peer := ENetMultiplayerPeer.new()
    peer.create_server(port)
    multiplayer.multiplayer_peer = peer
    multiplayer.peer_connected.connect(_on_peer_connected)
    multiplayer.peer_disconnected.connect(_on_peer_disconnected)

# Client
func join_game(address: String, port: int = 9999) -> void:
    var peer := ENetMultiplayerPeer.new()
    peer.create_client(address, port)
    multiplayer.multiplayer_peer = peer
```

### RPCs
### RPC
```gdscript
# Server-authoritative pattern
@rpc("any_peer", "call_local", "reliable")
func request_action(action_data: Dictionary) -> void:
    if not multiplayer.is_server():
        return
    # Validate on server, then broadcast
    _execute_action.rpc(action_data)

@rpc("authority", "call_local", "reliable")
func _execute_action(action_data: Dictionary) -> void:
    # All peers execute the validated action
    pass
```

### MultiplayerSpawner and MultiplayerSynchronizer
### MultiplayerSpawner 与 MultiplayerSynchronizer
```gdscript
# Use MultiplayerSpawner for automatic node replication
# Use MultiplayerSynchronizer for property synchronization

# MultiplayerSynchronizer setup:
# 1. Add as child of the node to sync
# 2. Configure replication properties in editor
# 3. Set visibility filters for relevancy
```

### SceneMultiplayer Configuration
### SceneMultiplayer 配置
```gdscript
func _ready() -> void:
    var scene_mp := multiplayer as SceneMultiplayer
    scene_mp.auth_callback = _authenticate_peer
    scene_mp.server_relay = false  # Direct peer connections

func _authenticate_peer(id: int, data: PackedByteArray) -> void:
    # Custom authentication logic
    pass
```

## Common Mistakes
## 常见错误
- Not using `"any_peer"` for client-to-server RPCs (defaults to authority only)
- 客户端到服务器的 RPC 未使用 `"any_peer"`（默认仅为 authority）
- Trusting client data without server-side validation
- 未经服务器端验证就信任客户端数据
- Using `"unreliable"` for game state changes (use for position updates only)
- 对游戏状态变更使用 `"unreliable"`（仅应用于位置更新）
- Not setting multiplayer authority (`set_multiplayer_authority()`) on spawned nodes
- 未在生成的节点上设置多人权限（`set_multiplayer_authority()`）
