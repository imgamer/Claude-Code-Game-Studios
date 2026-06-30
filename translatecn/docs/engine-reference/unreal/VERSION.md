# Unreal Engine — Version Reference
# Unreal Engine — 版本参考

| Field | Value |
|-------|-------|
| **Engine Version** | Unreal Engine 5.7 |
| **Release Date** | November 2025 |
| **Project Pinned** | 2026-02-13 |
| **Last Docs Verified** | 2026-02-13 |
| **LLM Knowledge Cutoff** | May 2025 |

| 字段 | 值 |
|-------|-------|
| **引擎版本** | Unreal Engine 5.7 |
| **发布日期** | 2025 年 11 月 |
| **项目固定版本** | 2026-02-13 |
| **最后文档验证** | 2026-02-13 |
| **LLM 知识截止** | 2025 年 5 月 |

## Knowledge Gap Warning
## 知识缺口警告

The LLM's training data likely covers Unreal Engine up to ~5.3. Versions 5.4, 5.5,
5.6, and 5.7 introduced significant changes that the model does NOT know about.
Always cross-reference this directory before suggesting Unreal API calls.

LLM 的训练数据可能仅覆盖到约 Unreal Engine 5.3。5.4、5.5、5.6 和 5.7 版本引入了重大变更，模型对此并不了解。在建议调用 Unreal API 之前，务必交叉参考本目录。

## Post-Cutoff Version Timeline
## 截止日期之后的版本时间线

| Version | Release | Risk Level | Key Theme |
|---------|---------|------------|-----------|
| 5.4 | ~Mid 2025 | HIGH | Motion Design tools, animation improvements, PCG enhancements |
| 5.5 | ~Sep 2025 | HIGH | Megalights (millions of lights), animation authoring, MegaCity demo |
| 5.6 | ~Oct 2025 | MEDIUM | Performance optimizations, bug fixes |
| 5.7 | Nov 2025 | HIGH | PCG production-ready, Substrate production-ready, AI assistant |

| 版本 | 发布 | 风险等级 | 核心主题 |
|---------|---------|------------|-----------|
| 5.4 | 约 2025 年中 | 高 | Motion Design 工具、动画改进、PCG 增强 |
| 5.5 | 约 2025 年 9 月 | 高 | Megalights（数百万光源）、动画创作、MegaCity 演示 |
| 5.6 | 约 2025 年 10 月 | 中 | 性能优化、错误修复 |
| 5.7 | 2025 年 11 月 | 高 | PCG 生产就绪、Substrate 生产就绪、AI 助手 |

## Major Changes from UE 5.3 to UE 5.7
## 从 UE 5.3 到 UE 5.7 的重大变更

### Breaking Changes
### 破坏性变更
- **Substrate Material System**: New material framework (replaces legacy materials)
- **PCG (Procedural Content Generation)**: Production-ready, major API changes
- **Megalights**: New lighting system (millions of dynamic lights)
- **Animation Authoring**: New rigging and animation tools
- **AI Assistant**: In-editor AI guidance (experimental)

- **Substrate 材质系统**：新材质框架（取代旧版材质）
- **PCG（程序化内容生成）**：生产就绪，API 有重大变更
- **Megalights**：新光照系统（数百万动态光源）
- **动画创作**：新的绑定与动画工具
- **AI 助手**：编辑器内 AI 指导（实验性）

### New Features (Post-Cutoff)
### 新特性（截止日期之后）
- **Megalights**: Dynamic lighting at massive scale (millions of lights)
- **Substrate Materials**: Production-ready modular material system
- **PCG Framework**: Procedural world generation (production-ready in 5.7)
- **Enhanced Virtual Production**: MetaHuman integration, deeper VP workflows
- **Animation Improvements**: Better rigging, blending, procedural animation
- **AI Assistant**: In-editor AI help (experimental)

- **Megalights**：大规模动态光照（数百万光源）
- **Substrate 材质**：生产就绪的模块化材质系统
- **PCG 框架**：程序化世界生成（5.7 中生产就绪）
- **增强的虚拟制片**：MetaHuman 集成、更深入的 VP 工作流
- **动画改进**：更好的绑定、混合与程序化动画
- **AI 助手**：编辑器内 AI 帮助（实验性）

### Deprecated Systems
### 弃用系统
- **Legacy Material System**: Migrate to Substrate for new projects
- **Old PCG API**: Use new production-ready PCG API (5.7+)

- **旧版材质系统**：新项目请迁移到 Substrate
- **旧版 PCG API**：使用新的生产就绪 PCG API（5.7+）

## Verified Sources
## 已验证的来源

- Official docs: https://docs.unrealengine.com/5.7/
- UE 5.7 release notes: https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-7-release-notes
- What's new in 5.7: https://dev.epicgames.com/documentation/en-us/unreal-engine/whats-new
- UE 5.7 announcement: https://www.unrealengine.com/en-US/news/unreal-engine-5-7-is-now-available
- UE 5.5 blog: https://www.unrealengine.com/en-US/blog/unreal-engine-5-5-is-now-available
- Migration guides: https://docs.unrealengine.com/5.7/en-US/upgrading-projects/

- 官方文档：https://docs.unrealengine.com/5.7/
- UE 5.7 发行说明：https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-7-release-notes
- 5.7 新特性：https://dev.epicgames.com/documentation/en-us/unreal-engine/whats-new
- UE 5.7 公告：https://www.unrealengine.com/en-US/news/unreal-engine-5-7-is-now-available
- UE 5.5 博客：https://www.unrealengine.com/en-US/blog/unreal-engine-5-5-is-now-available
- 迁移指南：https://docs.unrealengine.com/5.7/en-US/upgrading-projects/
