# Godot Engine — Version Reference
# Godot 引擎 — 版本参考

| Field | Value |
|-------|-------|
| **Engine Version** | Godot 4.6 |
| **Release Date** | January 2026 |
| **Project Pinned** | 2026-02-12 |
| **Last Docs Verified** | 2026-02-12 |
| **LLM Knowledge Cutoff** | May 2025 |

| 字段 | 值 |
|-------|-------|
| **引擎版本** | Godot 4.6 |
| **发布日期** | 2026 年 1 月 |
| **项目固定版本** | 2026-02-12 |
| **最后文档验证** | 2026-02-12 |
| **LLM 知识截止** | 2025 年 5 月 |

## Knowledge Gap Warning
## 知识缺口警告

The LLM's training data likely covers Godot up to ~4.3. Versions 4.4, 4.5,
and 4.6 introduced significant changes that the model does NOT know about.
Always cross-reference this directory before suggesting Godot API calls.
LLM 的训练数据可能仅涵盖到约 4.3 的 Godot 版本。版本 4.4、4.5
和 4.6 引入了模型并不知晓的重大变更。
在建议 Godot API 调用之前，务必交叉参考此目录。

## Post-Cutoff Version Timeline
## 截止后的版本时间线

| Version | Release | Risk Level | Key Theme |
|---------|---------|------------|-----------|
| 4.4 | ~Mid 2025 | MEDIUM | Jolt physics option, FileAccess return types, shader texture type changes |
| 4.5 | ~Late 2025 | HIGH | Accessibility (AccessKit), variadic args, @abstract, shader baker, SMAA |
| 4.6 | Jan 2026 | HIGH | Jolt default, glow rework, D3D12 default on Windows, IK restored |

| 版本 | 发布 | 风险等级 | 关键主题 |
|---------|---------|------------|-----------|
| 4.4 | ~2025 年中 | MEDIUM | Jolt 物理选项、FileAccess 返回类型、着色器纹理类型变更 |
| 4.5 | ~2025 年末 | HIGH | 无障碍 (AccessKit)、可变参数、@abstract、着色器烘焙器、SMAA |
| 4.6 | 2026 年 1 月 | HIGH | Jolt 设为默认、glow 重做、Windows 上 D3D12 设为默认、IK 恢复 |

## Verified Sources
## 已验证的来源

- Official docs: https://docs.godotengine.org/en/stable/
- 官方文档：https://docs.godotengine.org/en/stable/
- 4.5→4.6 migration: https://docs.godotengine.org/en/stable/tutorials/migrating/upgrading_to_godot_4.6.html
- 4.5→4.6 迁移：https://docs.godotengine.org/en/stable/tutorials/migrating/upgrading_to_godot_4.6.html
- 4.4→4.5 migration: https://docs.godotengine.org/en/stable/tutorials/migrating/upgrading_to_godot_4.5.html
- 4.4→4.5 迁移：https://docs.godotengine.org/en/stable/tutorials/migrating/upgrading_to_godot_4.5.html
- Changelog: https://github.com/godotengine/godot/blob/master/CHANGELOG.md
- 更新日志：https://github.com/godotengine/godot/blob/master/CHANGELOG.md
- Release notes: https://godotengine.org/releases/4.6/
- 发行说明：https://godotengine.org/releases/4.6/
