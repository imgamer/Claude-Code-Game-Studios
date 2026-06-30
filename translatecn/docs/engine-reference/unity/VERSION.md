# Unity Engine — Version Reference
# Unity 引擎 — 版本参考

| Field | Value |
|-------|-------|
| **Engine Version** | Unity 6.3 LTS |
| **Release Date** | December 2025 |
| **Project Pinned** | 2026-02-13 |
| **Last Docs Verified** | 2026-02-13 |
| **LLM Knowledge Cutoff** | May 2025 |

| 字段 | 值 |
|-------|-------|
| **引擎版本** | Unity 6.3 LTS |
| **发布日期** | 2025 年 12 月 |
| **项目锁定版本** | 2026-02-13 |
| **文档最后验证时间** | 2026-02-13 |
| **LLM 知识截止时间** | 2025 年 5 月 |

## Knowledge Gap Warning
## 知识盲区警告

The LLM's training data likely covers Unity up to ~2022 LTS (2022.3). The entire
Unity 6 release series (formerly Unity 2023 Tech Stream) introduced significant
changes that the model does NOT know about. Always cross-reference this directory
before suggesting Unity API calls.

LLM 的训练数据可能仅覆盖 Unity 至约 2022 LTS（2022.3）。整个 Unity 6 发布系列（前身为 Unity 2023 Tech Stream）引入了模型并不知晓的重大变更。在建议 Unity API 调用之前，请务必交叉参考此目录。

## Post-Cutoff Version Timeline
## 知识截止后的版本时间线

| Version | Release | Risk Level | Key Theme |
|---------|---------|------------|-----------|
| 6.0 | Oct 2024 | HIGH | Unity 6 rebrand, new rendering features, Entities 1.3, DOTS improvements |
| 6.1 | Nov 2024 | MEDIUM | Bug fixes, stability improvements |
| 6.2 | Dec 2024 | MEDIUM | Performance optimizations, new input system improvements |
| 6.3 LTS | Dec 2025 | HIGH | First LTS since 6.0, production-ready DOTS, enhanced graphics features |

| 版本 | 发布时间 | 风险等级 | 核心主题 |
|---------|---------|------------|-----------|
| 6.0 | 2024 年 10 月 | 高 | Unity 6 品牌重塑，新渲染特性，Entities 1.3，DOTS 改进 |
| 6.1 | 2024 年 11 月 | 中 | 缺陷修复，稳定性改进 |
| 6.2 | 2024 年 12 月 | 中 | 性能优化，新输入系统改进 |
| 6.3 LTS | 2025 年 12 月 | 高 | 6.0 以来首个 LTS，生产就绪的 DOTS，增强的图形特性 |

## Major Changes from 2022 LTS to Unity 6.3 LTS
## 从 2022 LTS 到 Unity 6.3 LTS 的主要变更

### Breaking Changes
### 破坏性变更

- **Entities/DOTS**: Major API overhaul in Entities 1.0+, complete redesign of ECS patterns
- **Input System**: Legacy Input Manager deprecated, new Input System is default
- **Rendering**: URP/HDRP significant upgrades, SRP Batcher improvements
- **Addressables**: Asset management workflow changes
- **Scripting**: C# 9 support, new API patterns

- **Entities/DOTS**：Entities 1.0+ 中的重大 API 重构，ECS 模式的完整重新设计
- **Input System**：旧版 Input Manager 已弃用，新 Input System 成为默认
- **Rendering**：URP/HDRP 重大升级，SRP Batcher 改进
- **Addressables**：资源管理工作流变更
- **Scripting**：C# 9 支持，新的 API 模式

### New Features (Post-Cutoff)
### 新特性（知识截止后）

- **DOTS**: Production-ready Entity Component System (Entities 1.3+)
- **Graphics**: Enhanced URP/HDRP pipelines, GPU Resident Drawer
- **Multiplayer**: Netcode for GameObjects improvements
- **UI Toolkit**: Production-ready for runtime UI (replaces UGUI for new projects)
- **Async Asset Loading**: Improved Addressables performance
- **Web**: WebGPU support

- **DOTS**：生产就绪的 Entity Component System（Entities 1.3+）
- **Graphics**：增强的 URP/HDRP 管线，GPU Resident Drawer
- **Multiplayer**：Netcode for GameObjects 改进
- **UI Toolkit**：运行时 UI 生产就绪（在新项目中替代 UGUI）
- **Async Asset Loading**：改进的 Addressables 性能
- **Web**：WebGPU 支持

### Deprecated Systems
### 弃用的系统

- **Legacy Input Manager**: Use new Input System package
- **Legacy Particle System**: Use Visual Effect Graph
- **UGUI**: Still supported, but UI Toolkit recommended for new projects
- **Old ECS (GameObjectEntity)**: Replaced by modern DOTS/Entities

- **Legacy Input Manager**：使用新的 Input System 包
- **Legacy Particle System**：使用 Visual Effect Graph
- **UGUI**：仍受支持，但新项目推荐使用 UI Toolkit
- **旧版 ECS (GameObjectEntity)**：被现代 DOTS/Entities 替代

## Verified Sources
## 已验证的来源

- Official docs: https://docs.unity3d.com/6000.0/Documentation/Manual/index.html
- Unity 6 release: https://unity.com/releases/unity-6
- Unity 6.3 LTS announcement: https://unity.com/blog/unity-6-3-lts-is-now-available
- Migration guide: https://docs.unity3d.com/6000.0/Documentation/Manual/upgrade-guides.html
- Unity 6 support: https://unity.com/releases/unity-6/support
- C# API reference: https://docs.unity3d.com/6000.0/Documentation/ScriptReference/index.html

- 官方文档：https://docs.unity3d.com/6000.0/Documentation/Manual/index.html
- Unity 6 发布：https://unity.com/releases/unity-6
- Unity 6.3 LTS 公告：https://unity.com/blog/unity-6-3-lts-is-now-available
- 迁移指南：https://docs.unity3d.com/6000.0/Documentation/Manual/upgrade-guides.html
- Unity 6 支持：https://unity.com/releases/unity-6/support
- C# API 参考：https://docs.unity3d.com/6000.0/Documentation/ScriptReference/index.html
