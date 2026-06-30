# Economy Model: [System Name]
# 经济模型：[系统名称]

*Created: [Date]*
*创建日期：[日期]*
*Owner: economy-designer*
*负责人：经济设计师*
*Status: [Draft / Balanced / Live]*
*状态：[草稿 / 已平衡 / 已上线]*

---

## Overview
## 概览

[What resources, currencies, and exchange systems does this economy cover?
What player behaviors does it incentivize?]
[本经济涵盖哪些资源、货币与兑换系统？它激励玩家的哪些行为？]

---

## Currencies
## 货币

| Currency | Type | Earn Rate | Sink Rate | Cap | Notes |
| ---- | ---- | ---- | ---- | ---- | ---- |
| [Gold] | Soft | [per hour] | [per hour] | [max or none] | [Primary transaction currency] |
| [Gems] | Premium | [per day F2P] | [varies] | [max] | [Premium currency, purchasable] |
| [XP] | Progression | [per action] | [level-up cost] | [none] | [Cannot be traded] |

| 货币 | 类型 | 获取速率 | 消耗速率 | 上限 | 备注 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| [金币] | 软货币 | [每小时] | [每小时] | [上限或无] | [主要交易货币] |
| [宝石] | 高级货币 | [每天 F2P] | [变动] | [上限] | [高级货币，可购买] |
| [经验] | 进度货币 | [每次行动] | [升级消耗] | [无] | [不可交易] |

### Currency Rules
### 货币规则
- [Rule 1 — e.g., "Soft currency has no cap but inflation is controlled via sinks"]
- [规则 1 — 例如 "软货币无上限，但通胀通过消耗口控制"]
- [Rule 2 — e.g., "Premium currency cannot be converted back to real money"]
- [规则 2 — 例如 "高级货币不可兑回真实货币"]
- [Rule 3]
- [规则 3]

---

## Sources (Faucets)
## 来源（产出）

| Source | Currency | Amount | Frequency | Conditions |
| ---- | ---- | ---- | ---- | ---- |
| [Quest completion] | Gold | [50-200] | [per quest] | [Scales with quest difficulty] |
| [Enemy drops] | Gold | [1-10] | [per kill] | [Modified by luck stat] |
| [Daily login] | Gems | [5] | [daily] | [Streak bonus: +1 per consecutive day] |
| [Achievement] | XP | [100-500] | [one-time] | [Per achievement tier] |

| 来源 | 货币 | 数量 | 频率 | 条件 |
| ---- | ---- | ---- | ---- | ---- |
| [任务完成] | 金币 | [50-200] | [每次任务] | [随任务难度缩放] |
| [敌人掉落] | 金币 | [1-10] | [每次击杀] | [受幸运属性修正] |
| [每日登录] | 宝石 | [5] | [每日] | [连签奖励：每连续一天 +1] |
| [成就] | 经验 | [100-500] | [一次性] | [按成就等级] |

---

## Sinks (Drains)
## 消耗口（流出）

| Sink | Currency | Cost | Frequency | Purpose |
| ---- | ---- | ---- | ---- | ---- |
| [Equipment purchase] | Gold | [100-5000] | [as needed] | [Power progression] |
| [Repair costs] | Gold | [10-100] | [per death] | [Death penalty, gold drain] |
| [Cosmetic shop] | Gems | [50-500] | [optional] | [Vanity, premium sink] |
| [Respec] | Gold | [1000] | [rare] | [Build experimentation tax] |

| 消耗口 | 货币 | 成本 | 频率 | 目的 |
| ---- | ---- | ---- | ---- | ---- |
| [装备购买] | 金币 | [100-5000] | [按需] | [力量提升] |
| [修理费用] | 金币 | [10-100] | [每次死亡] | [死亡惩罚、金币流出] |
| [外观商店] | 宝石 | [50-500] | [可选] | [装饰性、高级消耗口] |
| [重置加点] | 金币 | [1000] | [稀有] | [流派试验税] |

---

## Balance Targets
## 平衡目标

| Metric | Target | Rationale |
| ---- | ---- | ---- |
| Time to first meaningful purchase | [X minutes] | [Player should feel spending power early] |
| Hourly gold earn rate (mid-game) | [X gold/hr] | [Based on session length and purchase cadence] |
| Days to max level (F2P) | [X days] | [Enough to retain, not so long it frustrates] |
| Sink-to-source ratio | [0.7-0.9] | [Slight surplus keeps players feeling wealthy] |
| Premium currency F2P earn rate | [X/week] | [Enough to buy something monthly, not everything] |

| 指标 | 目标 | 理由 |
| ---- | ---- | ---- |
| 首次有意义购买的时间 | [X 分钟] | [玩家应尽早感受到购买力] |
| 每小时金币获取率（中期） | [X 金币/小时] | [基于会话时长与购买节奏] |
| F2P 升满级的天数 | [X 天] | [足以留存，又不至于令人沮丧] |
| 消耗-产出比 | [0.7-0.9] | [略有盈余让玩家感到富有] |
| F2P 高级货币获取率 | [X/周] | [足以每月购买一些东西，但非全部] |

---

## Progression Curves
## 进度曲线

### Level XP Requirements
### 等级经验需求
| Level | XP Required | Cumulative XP | Estimated Time |
| ---- | ---- | ---- | ---- |
| 1→2 | [100] | [100] | [10 min] |
| 5→6 | [500] | [1,500] | [2 hrs] |
| 10→11 | [1,500] | [7,500] | [8 hrs] |
| 20→21 | [5,000] | [50,000] | [40 hrs] |

| 等级 | 所需经验 | 累计经验 | 预计时长 |
| ---- | ---- | ---- | ---- |
| 1→2 | [100] | [100] | [10 分钟] |
| 5→6 | [500] | [1,500] | [2 小时] |
| 10→11 | [1,500] | [7,500] | [8 小时] |
| 20→21 | [5,000] | [50,000] | [40 小时] |

*Formula*: `XP(n) = [formula, e.g., 100 * n^1.5]`
*公式*：`XP(n) = [公式，例如 100 * n^1.5]`

### Item Price Scaling
### 物品价格缩放
*Formula*: `Price(tier) = [formula, e.g., base_price * 2^(tier-1)]`
*公式*：`Price(tier) = [公式，例如 base_price * 2^(tier-1)]`

---

## Loot Tables
## 掉落表

### [Drop Source Name]
### [掉落来源名称]
| Item | Rarity | Drop Rate | Pity Timer | Notes |
| ---- | ---- | ---- | ---- | ---- |
| [Common item] | Common | [60%] | [N/A] | [Always useful, never feels bad] |
| [Uncommon item] | Uncommon | [25%] | [N/A] | [Noticeable upgrade] |
| [Rare item] | Rare | [12%] | [10 drops] | [Exciting, build-defining] |
| [Legendary item] | Legendary | [3%] | [30 drops] | [Game-changing, celebration moment] |

| 物品 | 稀有度 | 掉落率 | 保底次数 | 备注 |
| ---- | ---- | ---- | ---- | ---- |
| [普通物品] | 普通 | [60%] | [不适用] | [始终有用，永不令人不悦] |
| [不凡物品] | 不凡 | [25%] | [不适用] | [可察觉的提升] |
| [稀有物品] | 稀有 | [12%] | [10 次掉落] | [令人兴奋，定义流派] |
| [传奇物品] | 传奇 | [3%] | [30 次掉落] | [改变游戏体验，庆祝时刻] |

### Pity System
### 保底系统
[Describe how the pity system works to prevent extreme bad luck streaks.]
[描述保底系统如何运作以防止极端的霉运连续。]

---

## Economy Health Metrics
## 经济健康指标

| Metric | Healthy Range | Warning Threshold | Action if Breached |
| ---- | ---- | ---- | ---- |
| Average player gold | [X-Y at level Z] | [>Y or <X] | [Adjust faucets/sinks] |
| Gold Gini coefficient | [<0.4] | [>0.5] | [Wealth too concentrated] |
| % players hitting currency cap | [<5%] | [>10%] | [Raise cap or add sinks] |
| Premium conversion rate | [2-5%] | [<1% or >10%] | [Rebalance F2P earn rate] |
| Average time between purchases | [X minutes] | [>Y minutes] | [Nothing worth buying] |

| 指标 | 健康区间 | 预警阈值 | 触发后行动 |
| ---- | ---- | ---- | ---- |
| 玩家平均金币 | [Z 级时 X-Y] | [>Y 或 <X] | [调整产出/消耗口] |
| 金币基尼系数 | [<0.4] | [>0.5] | [财富过于集中] |
| 触及货币上限的玩家占比 | [<5%] | [>10%] | [提高上限或增加消耗口] |
| 高级货币转化率 | [2-5%] | [<1% 或 >10%] | [重新平衡 F2P 获取率] |
| 两次购买之间平均时间 | [X 分钟] | [>Y 分钟] | [没有值得购买的东西] |

---

## Ethical Guardrails
## 伦理护栏

- [No pay-to-win: premium currency cannot buy gameplay power advantages]
- [不得付钱取胜：高级货币不可购买游戏玩法上的力量优势]
- [Pity timers on all random drops: guaranteed outcome within X attempts]
- [所有随机掉落均设保底：X 次尝试内保证产出]
- [Transparent drop rates displayed to players]
- [向玩家透明展示掉落率]
- [Spending limits for minor accounts]
- [未成年人账户设置消费上限]
- [No artificial scarcity pressure (FOMO timers) on essential items]
- [对必需品不得施加人为稀缺压力（FOMO 计时）]

---

## Simulation Results
## 模拟结果

[Include results from economy simulations if available: player wealth
distribution over time, sink effectiveness, inflation rate, etc.]
[如可用，附上经济模拟结果：玩家财富随时间的分布、消耗口有效性、通胀率等。]

---

## Dependencies
## 依赖

- Depends on: [combat balance, quest design, crafting system]
- 依赖于：[战斗平衡、任务设计、制作系统]
- Affects: [difficulty curve, player retention, monetization]
- 影响：[难度曲线、玩家留存、商业化]
- Must coordinate with: `game-designer`, `live-ops-designer`, `analytics-engineer`
- 必须协调：`game-designer`、`live-ops-designer`、`analytics-engineer`
