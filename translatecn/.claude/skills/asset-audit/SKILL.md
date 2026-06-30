---
name: asset-audit
description: "Audits game assets for compliance with naming conventions, file size budgets, format standards, and pipeline requirements. Identifies orphaned assets, missing references, and standard violations."
argument-hint: "[category|all]"
user-invocable: true
allowed-tools: Read, Glob, Grep
model: sonnet
# Read-only diagnostic skill — no specialist agent delegation needed
---
---
名称：asset-audit
描述："审计游戏资产的命名约定、文件大小预算、格式标准和管线需求的合规性。识别孤立资产、缺失引用和标准违规。"
argument-hint："[类别|all]"
user-invocable：true
allowed-tools：Read, Glob, Grep
model：sonnet
# Read-only diagnostic skill — no specialist agent delegation needed
---

## Phase 1: Read Standards
## 阶段 1：读取标准

Read the art bible or asset standards from the relevant design docs and the CLAUDE.md naming conventions.
从相关设计文档和 CLAUDE.md 命名约定中读取美术圣经或资产标准。

---

## Phase 2: Scan Asset Directories
## 阶段 2：扫描资产目录

Scan the target asset directory using Glob:
使用 Glob 扫描目标资产目录：

- `assets/art/**/*` for art assets
- `assets/audio/**/*` for audio assets
- `assets/vfx/**/*` for VFX assets
- `assets/shaders/**/*` for shaders
- `assets/data/**/*` for data files
- `assets/art/**/*` 用于美术资产
- `assets/audio/**/*` 用于音频资产
- `assets/vfx/**/*` 用于 VFX 资产
- `assets/shaders/**/*` 用于着色器
- `assets/data/**/*` 用于数据文件

---

## Phase 3: Run Compliance Checks
## 阶段 3：运行合规性检查

**Naming conventions:**
- Art: `[category]_[name]_[variant]_[size].[ext]`
- Audio: `[category]_[context]_[name]_[variant].[ext]`
- All files must be lowercase with underscores
**命名约定：**
- 美术：`[category]_[name]_[variant]_[size].[ext]`
- 音频：`[category]_[context]_[name]_[variant].[ext]`
- 所有文件必须为小写加下划线

**File standards:**
- Textures: Power-of-two dimensions, correct format (PNG for UI, compressed for 3D), within size budget
- Audio: Correct sample rate, format (OGG for SFX, OGG/MP3 for music), within duration limits
- Data: Valid JSON/YAML, schema-compliant
**文件标准：**
- 纹理：2 的幂次方尺寸，正确格式（UI 用 PNG，3D 用压缩格式），在大小预算内
- 音频：正确采样率，格式（SFX 用 OGG，音乐用 OGG/MP3），在时长限制内
- 数据：有效的 JSON/YAML，符合 schema

**Orphaned assets:** Search code for references to each asset file. Flag any with no references.
**孤立资产：** 在代码中搜索对每个资产文件的引用。标记任何无引用的文件。

**Missing assets:** Search code for asset references and verify the files exist.
**缺失资产：** 在代码中搜索资产引用并验证文件是否存在。

---

## Phase 4: Output Audit Report
## 阶段 4：输出审计报告

```markdown
# Asset Audit Report -- [Category] -- [Date]

## Summary
- **Total assets scanned**: [N]
- **Naming violations**: [N]
- **Size violations**: [N]
- **Format violations**: [N]
- **Orphaned assets**: [N]
- **Missing assets**: [N]
- **Overall health**: [CLEAN / MINOR ISSUES / NEEDS ATTENTION]

## Naming Violations
| File | Expected Pattern | Issue |
|------|-----------------|-------|

## Size Violations
| File | Budget | Actual | Overage |
|------|--------|--------|---------|

## Format Violations
| File | Expected Format | Actual Format |
|------|----------------|---------------|

## Orphaned Assets (no code references found)
| File | Last Modified | Size | Recommendation |
|------|-------------|------|---------------|

## Missing Assets (referenced but not found)
| Reference Location | Expected Path |
|-------------------|---------------|

## Recommendations
[Prioritized list of fixes]

## Verdict: [COMPLIANT / WARNINGS / NON-COMPLIANT]
```

This skill is read-only — it produces a report but does not write files.
此技能是只读的 — 它产生报告但不写入文件。

---

## Phase 5: Next Steps
## 阶段 5：后续步骤

- Fix naming violations using the patterns defined in CLAUDE.md.
- Delete confirmed orphaned assets after manual review.
- Run `/content-audit` to cross-check asset counts against GDD-specified requirements.
- 使用 CLAUDE.md 中定义的模式修复命名违规。
- 手动审查后删除确认的孤立资产。
- 运行 `/content-audit` 将资产计数与 GDD 指定的需求交叉核对。
