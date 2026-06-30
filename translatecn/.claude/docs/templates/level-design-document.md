# Level: [Level Name]
# 关卡：[关卡名称]

## Quick Reference
## 快速参考

- **Area/Region**: [Where in the game world]
- **Type**: [Combat / Exploration / Puzzle / Hub / Boss / Mixed]
- **Estimated Play Time**: [X-Y minutes]
- **Difficulty**: [1-10 relative scale]
- **Prerequisite**: [What the player must have done to reach this level]
- **Status**: [Concept | Layout | Graybox | Art Pass | Polish | Final]
- **区域/地区**：[在游戏世界中何处]
- **类型**：[战斗 / 探索 / 解谜 / 枢纽 / Boss / 混合]
- **预估游玩时间**：[X-Y 分钟]
- **难度**：[1-10 相对刻度]
- **前置条件**：[玩家必须完成什么才能到达此关卡]
- **状态**：[概念 | 布局 | 灰盒 | 美术通 | 打磨 | 最终]

## Narrative Context
## 叙事上下文

- **Story Moment**: [Where in the narrative arc does this level occur]
- **Narrative Purpose**: [What story beat this level delivers]
- **Emotional Target**: [What the player should feel during this level]
- **Lore Discoveries**: [What world-building the player can find here]
- **故事时刻**：[此关卡在叙事弧线何处出现]
- **叙事目的**：[此关卡交付什么故事节拍]
- **情感目标**：[玩家在此关卡期间应感受什么]
- **背景设定发现**：[玩家可在此发现什么世界构建]

## Layout
## 布局

### Overview Map
### 概览图

```
[ASCII diagram of the level layout. Use these symbols:]
[S] = Start point
[E] = Exit/end point
[C] = Combat encounter
[P] = Puzzle
[R] = Reward/loot
[!] = Story beat
[?] = Secret/optional
[>] = One-way passage
[=] = Two-way passage
[@] = NPC
[B] = Boss encounter
```

```
[ASCII diagram of the level layout. Use these symbols:]
[S] = Start point
[E] = Exit/end point
[C] = Combat encounter
[P] = Puzzle
[R] = Reward/loot
[!] = Story beat
[?] = Secret/optional
[>] = One-way passage
[=] = Two-way passage
[@] = NPC
[B] = Boss encounter
```

```
[关卡布局 ASCII 图。使用这些符号：]
[S] = 起点
[E] = 出口/终点
[C] = 战斗遭遇
[P] = 解谜
[R] = 奖励/战利品
[!] = 故事节拍
[?] = 秘密/可选
[>] = 单向通道
[=] = 双向通道
[@] = NPC
[B] = Boss 遭遇
```

### Critical Path
### 关键路径

[The mandatory route through the level, step by step.]
[通过关卡必经路线，逐步。]

1. Player enters at [S]
2. [Description of what happens along the path]
3. Player exits at [E]
1. 玩家在 [S] 进入
2. [沿路径发生什么的描述]
3. 玩家在 [E] 退出

### Optional Paths
### 可选路径

| Path | Access Requirement | Reward | Discovery Hint |
|------|-------------------|--------|---------------|
| 路径 | 访问要求 | 奖励 | 发现提示 |
|------|-------------------|--------|---------------|

### Points of Interest
### 兴趣点

| Location | Type | Description | Purpose |
|----------|------|-------------|---------|
| 位置 | 类型 | 描述 | 用途 |
|----------|------|-------------|---------|

## Encounters
## 遭遇

### Combat Encounters
### 战斗遭遇

| ID | Position | Enemy Composition | Difficulty | Arena Notes |
|----|----------|------------------|-----------|-------------|
| E-01 | [Map ref] | [2x Grunt, 1x Ranged] | 3/10 | Open area, cover on flanks |
| E-02 | [Map ref] | [1x Elite, 3x Grunt] | 5/10 | Narrow corridor, no retreat |
| ID | 位置 | 敌人组成 | 难度 | 竞技场备注 |
|----|----------|------------------|-----------|-------------|
| E-01 | [地图引用] | [2x 杂兵，1x 远程] | 3/10 | 开阔区域，侧翼有掩体 |
| E-02 | [地图引用] | [1x 精英，3x 杂兵] | 5/10 | 狭窄走廊，无退路 |

### Non-Combat Encounters
### 非战斗遭遇

| ID | Position | Type | Description | Solution Hint |
|----|----------|------|-------------|---------------|
| ID | 位置 | 类型 | 描述 | 解决提示 |
|----|----------|------|-------------|---------------|

## Pacing Chart
## 节奏图

```
Intensity
10 |                              *
 8 |                         *   * *
 6 |            *  *        * * *   *
 4 |     *  *  * ** *   *  *
 2 | * ** ** *        * * *          *
 0 |S-----------------------------------------E
     [Start]    [Mid]              [Climax] [Exit]
```

```
Intensity
10 |                              *
 8 |                         *   * *
 6 |            *  *        * * *   *
 4 |     *  *  * ** *   *  *
 2 | * ** ** *        * * *          *
 0 |S-----------------------------------------E
     [Start]    [Mid]              [Climax] [Exit]
```

[Describe the intended rhythm: where are the peaks, valleys, rest points?]
[描述预期节奏：峰值、谷值、休息点在何处？]

## Audio Direction
## 音频方向

| Zone/Moment | Music Track | Ambience | Key SFX |
|-------------|------------|----------|---------|
| [Entry] | [Track] | [Ambient sounds] | [Door opening] |
| [Combat] | [Combat music] | [Muted ambience] | [Combat SFX] |
| [Post-combat] | [Calm transition] | [Return to ambience] | |
| 区域/时刻 | 音乐轨道 | 环境 | 关键音效 |
|-------------|------------|----------|---------|
| [入口] | [轨道] | [环境声] | [开门] |
| [战斗] | [战斗音乐] | [静音环境] | [战斗音效] |
| [战斗后] | [平静过渡] | [返回环境] | |

## Visual Direction
## 视觉方向

- **Lighting**: [Key, fill, ambient description]
- **Color Palette**: [Dominant colors and why]
- **Mood Board References**: [Description of visual references]
- **Landmarks**: [Visible navigation aids and their locations]
- **Sight Lines**: [What the player should see from key positions]
- **光照**：[主光、补光、环境描述]
- **配色**：[主色及原因]
- **情绪板参考**：[视觉参考描述]
- **地标**：[可见导航辅助及其位置]
- **视线**：[玩家从关键位置应看到什么]

## Collectibles and Secrets
## 收集品与秘密

| Item | Location | Visibility | Hint | Required For |
|------|----------|-----------|------|-------------|
| 物品 | 位置 | 可见性 | 提示 | 用于 |
|------|----------|-----------|------|-------------|

## Technical Notes
## 技术备注

- **Estimated Object Count**: [N]
- **Streaming Zones**: [Where to break the level for streaming]
- **Performance Concerns**: [Any known heavy areas]
- **Required Systems**: [What game systems are active in this level]
- **预估对象数**：[N]
- **流送区**：[何处为流送划分关卡]
- **性能问题**：[任何已知重载区]
- **所需系统**：[此关卡激活什么游戏系统]
