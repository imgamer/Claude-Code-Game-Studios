# review-all-gdds 跨文档审查指南

`/review-all-gdds` 是一个"通读全部游戏设计文档、找文档之间的矛盾、找整体设计问题"的技能。它是所有 skill 里**唯一需要把所有 GDD 同时装进视野**的一个——这决定了它的实现方式、成本结构和独特的价值。

这一篇面向初学者：先建立概念术语表（专业词 + 大白话），再拆命名语义、设计思路、7 个 Phase 的步骤，然后重点讲透两个核心问题——**跨文档一致性是怎么做到的**、**holistic（整体论）设计审查是怎么做到的**，最后用一整节（第八节"深入篇"）拆开支撑这一切的机器：**subagent 的调用机制、上下文隔离原理、切片传递的实现细节**。

---

## 一、先建立直觉：这个技能解决什么问题

### 一个非游戏行业的类比

想象你在写一本长篇小说，一共 15 章，每章 8000 字。你有两位编辑：

- **编辑 A（逐章校对）**：一章一章看。他负责检查"这一章内部是否完整"——人物有没有出场就消失的、情节有没有断裂的。
- **编辑 B（全书通读）**：把 15 章全部读完。他负责检查**只有通读才能发现的问题**——第 3 章说主角身高一米八，第 12 章写他"钻进一米六的管道"没弯腰；第 5 章埋的伏笔到结尾都没回收；更严重的是，**整本书根本没讲清楚主角到底想要什么**。

- 编辑 A 就是 `/design-review`（审查单个 GDD 的内部完整性）
- **编辑 B 就是 `/review-all-gdds`（本篇的主角）**

SKILL.md 原文把这条边界写得非常清楚：

> "This is distinct from `/design-review`, which reviews one GDD for internal completeness. This skill reviews the *relationships* between all GDDs."
> （`/design-review` 审单个 GDD 的内部完整性；本技能审的是**所有 GDD 之间的关系**。）

### 为什么"关系"必须整体审

一个 GDD 单看完美，不代表放在一起没问题。三类问题**只有把所有文档放在一起才能看见**：

1. **文档矛盾**：战斗系统说"最低伤害不低于 1"，某个 Boss 技能说"可以把玩家生命直接扣到 0"——两份文档各自都没错，放在一起就是矛盾。
2. **整体设计缺陷**：每个系统单独看都好玩，但 6 个系统同时要求玩家操作，玩家会疯掉（认知过载）；一个策略比其他所有策略都好，玩家就会只玩它（策略支配）。
3. **边界问题**：系统 A 的输出接系统 B 的输入，单看任何一边都不知道接不接得上。

SKILL.md 里点名的运行时机也印证了这一点——**"Before `/create-architecture` begins (architecture built on inconsistent GDDs inherits those inconsistencies)"**：架构建立在互相矛盾的 GDD 上，就会**继承这些矛盾**。审查是一次"防传染"。

---

## 二、概念术语表（专业词 + 大白话）

| 术语 | 大白话解释 |
|------|-----------|
| **GDD** (Game Design Document) | 一个游戏系统（战斗、背包、任务……）的设计说明书。本项目里每个系统一份，放在 `design/gdd/` 下 |
| **Cross-GDD Consistency**（跨文档一致性） | 文档之间"说法要一致"：你说的公式我这边确实有，你依赖我的地方我确实承认 |
| **Game Design Holism**（设计整体论） | 把所有系统放在一起，用游戏设计理论和玩家心理学审"这个游戏整体好不好" |
| **Pillar**（设计支柱） | 这个游戏最核心的 2-3 条设计理念，如"残酷战斗"。每个系统都应该服务至少一条 |
| **Anti-Pillar**（反支柱） | 明确写下"我们**绝不做**什么"的清单，如"绝不做线性剧情"。防跑偏用的 |
| **Player Fantasy**（玩家幻想） | 这个系统想让玩家**感受到什么**。战斗系统的幻想可能是"每一刀都赚来的" |
| **Entity Registry**（实体注册表） | 一个集中登记簿（`design/registry/entities.yaml`），记着所有 GDD 里定义过的关键数值/公式/物品，以及"归谁管" |
| **Dominant Strategy**（支配策略） | 一个明显最优的玩法，玩家发现后就不玩别的了，游戏变得无聊 |
| **Source / Sink**（资源来源/去向） | 经济学概念：资源怎么获得（source）、怎么消耗（sink）。只有来源没有去向 = 资源越攒越多没处花 |
| **Cognitive Overload**（认知过载） | 同时要玩家操心的事太多（超过 3-4 个），脑子转不过来 |
| **Pillar Drift**（支柱漂移） | 某个系统不服务任何设计支柱——它"在游戏里，但不为游戏的核心服务" |
| **Stale Reference**（失效引用） | A 文档引用了 B 文档的一个机制，但 B 后来改了/根本没写这个机制 |
| **Walkthrough / Scenario**（情景走查） | 像"试玩"一样，模拟玩家在一个具体瞬间会经历什么，看系统链条哪里断 |
| **Verdict**（裁决） | 报告的最终结论，四档：PASS / NOT ASSESSED / CONCERNS / FAIL |

---

## 三、命名语义：名字里藏着的信息

### `review-all-gdds` vs `design-review`

```
/design-review      → review + 单数对象（一份 GDD）→ 审"这一份写全了没"
/review-all-gdds    → all + 复数 GDDs → 审"所有份之间和不和、整体好不好"
```

"all" 这个词就是整个技能的存在理由：**审查对象不是文档本身，是文档之间的关系**——关系至少要两个节点才存在，所以必须 all。

### 两种"审查 pass"的名字语义

SKILL.md 描述里的并列结构：

1. **Cross-GDD Consistency** — "contradictions, stale references, and ownership conflicts **between documents**"（文档**之间**的矛盾、失效引用、所有权冲突）
2. **Game Design Holism** — "issues that **only emerge when you see all systems together**"（只有把所有系统放在一起看才浮现的问题）

一句话记法：
- **一致性 = 编辑校对**（事实核对，对错分明）
- **Holism = 主编通读**（专业判断，好坏分明）

### 四个 focus 参数的语义

```
/review-all-gdds                          → full（默认）：两个 pass 都跑
/review-all-gdds consistency              → 只跑一致性（更快）
/review-all-gdds design-theory            → 只跑设计理论
/review-all-gdds since-last-review         → 只审上次审查报告之后改过的 GDD（用 git 算）
```

这又是那个熟悉的"成本旋钮"思想（和 design-system 的 full/lean/solo 同源）：**全量审查很贵，给用户一个按场景缩减范围的选择**。`since-last-review` 是最精妙的一档——不是靠 AI 判断"哪些改了"，而是靠 `review-scope.sh` 脚本从 git 历史里**确定性地**算出来。

### 为什么 `model: opus`

Frontmatter 里写死了 `model: opus`（最贵的模型）。因为这个技能做的是"多文档综合 + 设计理论判断"——正是模型分层策略里"跨系统综合评审用 Opus"的场景。它不像 dev-story 那样频繁运行，一年跑不了几次，**每次跑就要跑好**。

---

## 四、设计思路：三个核心决策

### 决策 1：两个 pass 分开，是因为"检查方式"完全不同

| | 一致性 pass（Phase 2） | Holism pass（Phase 3） |
|---|---|---|
| 本质 | **事实核对** | **专业判断** |
| 输入 | Dependencies、Detailed Design、Formulas、Tuning Knobs、Acceptance Criteria | Player Fantasy、进度/奖励结构、设计支柱 |
| 检查手段 | 文字比对、表格对照、数值区间核对 | 设计理论（策略支配、经济循环、认知负荷……） |
| 结论性质 | 有明确对错（引用存在或不存在） | 需要设计判断（可能是有意为之） |
| 输出倾向 | Blocking 居多 | Warning/CONCERNS 居多 |

分开的原因很实际：两类检查**读的章节都不一样**，混在一起跑会把不需要的内容装进同一个上下文。所以 SKILL.md 让它们作为**两个并行的 subagent** 同时跑，各自只喂各自需要的"切片"。

### 决策 2：并行 subagent + 内容打包传递

这是本技能最值得学的工程细节。SKILL.md 的 Parallel Execution 段规定：

- Phase 2 和 Phase 3 **互不依赖**（读同样的 GDD，产出独立的报告）→ 派成两个并行的 `game-designer` subagent
- **传内容切片，不传文件路径**。原文的 reasoning：
  > "Paste the sections; the sub-agent has its own context and cannot re-read Phase 1's results, so a path forces a full re-read"
  > （粘贴章节内容。子智能体有自己独立的上下文窗口，访问不到 Phase 1 读过的东西；给它路径就等于逼它把文件整个重读一遍）

这里有三层智慧：

1. **上下文隔离**：全部 GDD 装进主会话会爆掉（一个项目十几份 GDD，每份几千字）。主会话读完、切好片、分发，两个子智能体各拿各的。
2. **按需切片**：Phase 2 拿到的只有它要检查的 5 类章节；Phase 3 拿到的只有 Player Fantasy 和支柱相关。**没有一份内容被装进三个上下文窗口**。
3. **确定性**：不依赖 subagent "自己去读文件"——它可能读错、可能偷懒。主会话直接把要审的文字放进提示词里，审查对象百分百是主会话指定的那部分。

### 决策 3：评审者不裁决

Collaborative Protocol 第 4 条：

> "Don't make design decisions — flag contradictions and options, but never unilaterally decide which GDD is 'right'"
> （别做设计决策——标记矛盾和选项，但绝不单方面裁定哪份 GDD 是"对的"）

skill 找到"战斗系统和 Boss 技能矛盾"之后，**不告诉你该改哪边**——它把两边原文引出来，标红，问你"哪个是权威？"。这又是"AI 提议、人决策"的协作哲学。全部 6 条协议：静默读完再展示 → 先展示全部分析再要行动 → 区分 Blocking 和 advisory → 不替用户决策 → 写文件前先问 → 每个问题必须精确引用 GDD、章节、原文。

---

## 五、步骤拆解：7 个 Phase

### 全景图

```
Phase 1  Load Everything          分层装载：先扫摘要，再按章节精读
        │
        ├────────────┬─────────────┐
        ▼            ▼             │
Phase 2 一致性   Phase 3 Holism    │  两个并行 subagent（game-designer）
（2a-2f）       （3a-3g）          │  各拿各的内容切片
        └────────────┴─────────────┘
                     │
                     ▼
Phase 4  Cross-System Scenarios   情景走查：模拟玩家瞬间
                     │
                     ▼
Phase 5  输出报告                  四档 Verdict + 三级问题分类
                     │
                     ▼
Phase 6  写报告 + 标记 GDD        问权限 → 写文件 → 更新系统索引
                     │
                     ▼
Phase 7  Handoff                  动态生成"下一步"选项
```

### Phase 1：Load Everything（分层装载）

这一步的目标是**花最少的 token，把该装的装齐**。它分了三个层级：

**L0 — Summary Scan（快、省）**：先不读全文，用 Grep 把所有 GDD 的 `## Summary` 段抓出来，生成一个清单给用户看：

```
Found 14 GDDs. Summaries:
  • combat.md — 实时近战，连击与闪避……
  • inventory.md — 重量制背包……
```

**Registry Pre-Load（基线）**：读实体注册表。如果它有内容，一致性问题就有了**预先已知的"权威数值"**可以对照——Phase 2 拿它当冲突基线，比盲读全文快得多。

**L1/L2 — Section Load（按需精读）**：只读"这次审查真正会消费的章节"——不整篇读：

```
Grep pattern="^## (Dependencies|Detailed Rules|Detailed Design|Formulas|
               Tuning Knobs|Acceptance Criteria|Player Fantasy)"
     glob="design/gdd/*.md" output_mode="content" -A 40
```

这一步的设计极其精细，三个细节值得单独讲：

1. **不装用不到的内容**。reference 原文解释了为什么 Overview 和 Edge Cases 不装：Overview 是 Summary 已复述过的叙事，Edge Cases 不喂给任何 checklist 项——装进三个上下文窗口（主会话 + 两个 subagent）纯属浪费。
2. **"Detailed Rules" 和 "Detailed Design" 两个都收**——因为设计规范文档和 GDD 模板对这一节的名字不一致。一个容错，避免漏读。
3. **fail-open 语义**：某份 GDD 一个章节都没匹配到 ≠ 没内容，而是"它早于模板存在、没结构"。这时**升级为整篇读**，绝不让一个系统静默掉出审查集。reference 的原话很漂亮：
   > "the scan narrows the *read*, it never shrinks the *review set*"
   > （扫描只缩小"读"的范围，绝不缩小"审"的范围）

最后还有一个**熔断**：少于 2 份系统 GDD 就直接停——只有一个对象根本谈不上"跨文档"审查。

### Phase 2 + 3：两个并行 pass（下文第六、七节展开）

### Phase 4：Cross-System Scenario Walkthrough（情景走查）

前两个 phase 是**静态分析**——对着文档做比对和判断。Phase 4 是**动态模拟**——像试玩一样走一遍。

先识别 3-5 个"多个系统同时激活"的高危玩家瞬间（战斗+经济重叠、升级发生在战斗中、任务完成触发系统变化、A→B→C 三系统链），然后对每个瞬间显式走五步：

```
1. Trigger          什么玩家操作/游戏事件触发的？
2. Activation order 哪些系统被激活，按什么顺序？
3. Data flow        每个系统输出什么？输出对下一个系统是合法输入吗？
4. Player experience玩家在每一步看到、听到、感受到什么？
5. Failure modes    有没有竞态/反馈环/状态断裂/双重奖励/未定义行为？
```

reference 里给了一个教科书级例子——"玩家在升级阈值上击杀精英怪且任务同时完成"：

```
战斗系统给 100 点击杀 XP
→ 进度系统：跨过升级线 → 升级！属性提升、弹窗
→ 任务系统：击杀数达标 → 任务完成 → 给 500 点任务 XP
→ 进度系统（第二次！）：任务 XP 又跨过一条升级线
⚠️  同一帧内第二次升级——任务系统没检查"是否正在升级"，
   进度系统没有防并发升级的护栏。
   未定义行为：玩家升一级还是两级？弹窗弹两次吗？
```

这种问题**单看任何一份 GDD 都发现不了**——每份文档内部逻辑都是通的，只有把链子连起来走一遍，才看见断口。

### Phase 5：输出报告

报告结构是固定模板，三大块问题（一致性 / 设计问题 / 情景问题）各分 Blocking🔴 / Warning⚠️ / Infoℹ️ 三级，加上一张"GDDs Flagged for Revision"表，最后是 **Verdict**。

**Verdict 的语义和排序是本技能最有思想的部分**，初学者务必记住：

| Verdict | 含义 |
|---------|------|
| **PASS** | 没有 Blocking 也没有 Warning，干净 |
| **NOT ASSESSED** | 有些审查阶段没能跑——**它排在 PASS 之上、CONCERNS/FAIL 之下** |
| **CONCERNS** | 有该解决的 Warning，但没有 Blocking |
| **FAIL** | 存在 Blocking，必须解决才能开始架构 |

**为什么 NOT ASSESSED 比 PASS 还高？** 因为这个技能的全部价值就在于"系统互相对照"——它**特别容易**在只覆盖了一小部分文档的情况下显得很彻底。reference 原话：

> "A cross-review that silently skipped half the systems is indistinguishable from one that found them consistent."
> （静默跳过一半系统的审查，和"审查了、发现全都一致"在结果上无法区分。）

所以触发 NOT ASSESSED 的条件全是"覆盖面受损"：可读的 GDD 少于 2 份、某个 phase 整个没跑（**并行 subagent 返回了一段流利的文字但什么都没写，算 NOT ASSESSED 而不是"没问题"**）、设计支柱未定义（支柱漂移无从"漂"起）、某份 GDD 存在但是空壳。

报告还强制写明 `GDDs reviewed: [N] of [M] present` 和 `Not read:` 清单——**一份 FAIL 的报告也不能让没读的文档"顺带算审过了"**。

### Phase 6：写报告 + 标记 GDD

写文件前两次 `AskUserQuestion`：一次问"可以写报告吗"，一次问"要不要把被标记的 GDD 在系统索引里改成 Needs Revision"。

这里藏着两个精细工程细节：

1. **报告文件名必须是 ISO 8601 日期**（`gdd-cross-review-2026-08-19.md`）。reference 特意强调"这不是风格偏好"——`review-scope.sh` 用 `sort | tail -1` 找上一份报告，**只有 ISO 日期格式下，字典序 = 时间序**。写成 `aug-19-2026` 会导致"上一份审查"选错，改过的 GDD 静默逃过下次的 `since-last-review`。
2. **改系统索引时不许加括号注释**——别的技能按 `"Needs Revision"` **精确字符串匹配**，加个括号就匹配不上了。

最后静默更新会话状态文件 `active.md`——"文件即记忆"的老传统。

### Phase 7：Handoff（动态收尾）

不提供固定的"下一步"选项，而是**按项目现状动态生成**：有 30 秒就能改的小问题 → 提供"当场快修"选项；有被标记的 GDD → 逐个提供 `/design-review`；索引里有 Not Started 的系统 → 提供 `/design-system`；裁决是 PASS/CONCERNS 且两个 pass 都跑了 → 才提供 `/create-architecture` 或 `/gate-check`（**focus 模式只跑了一个 pass，永远不提供进架构的选项**——因为架构需要的是"两个维度都查过"的信心）。

---

## 六、跨文档一致性是怎么做到的（Phase 2 深挖）

Phase 2 有 6 个检查项（2a-2f），本质上是**四种"对账"**。用会计做类比最直观：

### 2a 依赖双向性 = 对账"应收应付"

你说你依赖我（我记你为"应付"），那我的账本上也该记着你依赖我（我记你为"应收"）。两边只记了一边 = 有人账本过期了。每一条 `depends on` 都必须在对面的 `depended on by` 里有镜像。

### 2b 规则矛盾 = 对账"同笔交易两边记的金额要一致"

五个高频矛盾扫描区：**底线/上限规则**（A 说"最低 1"，B 说"可以直接扣到 0"）、**资源所有权**（两份文档都定义同一资源怎么涨）、**状态转换**（角色死亡时 A 描述和 B 描述一致吗）、**时序**（A 以为同帧发生，B 以为异步发生）、**叠加规则**（A 说效果可叠，B 假设不可叠）。

### 2c 失效引用 = 对账"引用的凭证真实存在"

这是最"编辑"味的一项，分两步：

1. **先查 `## Cross-References` 表**——这是每份 GDD 自己声明的"我引用了谁"。表格缺失或还留着模板占位符，**这件事本身就是要报告的问题**（"无法区分'没有依赖'和'没写依赖'"）。
2. **然后无论如何扫正文**——声明的表只抓到作者记得的，正文扫描抓到作者忘了的。

严重度分级体现了成熟工程的克制：

| 情况 | 定级 |
|------|------|
| 引用的机制**根本不存在** | Blocking——架构会继承一条"没人定义过的规则" |
| 引用的机制存在但被设计成另一副样子 | Warning（除非它改变引用方依赖的规则，才升 Blocking） |
| 目标 GDD 还没写，但**系统索引里已规划** | Warning——是"对计划中工作的依赖"，等那份 GDD 写出来再复核 |
| 目标 GDD 没写，索引里**也没有** | Blocking——永远不会有东西来定义它了 |

### 2d 数据与旋钮所有权 = 对账"同一科目不许两个人记账"

两份 GDD 都声称自己拥有影响同一输出的旋钮 → 要么代码里双重生效（bug），要么设计上打架。**所有权必须唯一。**

### 2e 公式兼容 = 对账"上游的付款范围要落在下游的收款范围内"

上游公式输出 5-50，下游公式设计只接收 0-10 → 后期数值溢出，经济崩坏。注意这一项的产出是 **CONCERNS 而非 FAIL**——数值区间错位可能是故意的设计判断，需要人来裁决。

### 2f 验收标准互查 = 对账"两份合同不许同时违约"

A 系统的验收标准说"玩家不会被一击必杀"，B 系统的验收标准说"Boss 一击造成 150% 最大生命"——**两条标准永远无法同时通过测试**。这是最"硬"的一致性问题，因为它直接预言了 QA 阶段必然出现的测试失败。

### 一致性检查的技术底座

三个支撑机制让上面这些"对账"可行：

1. **实体注册表当基线**（Phase 1b）——先读 `entities.yaml`，已知数值就有了权威对照源，Grep 已注册的名字比盲读全文快。
2. **精确引用纪律**——协议第 6 条：每个问题必须引用"哪份 GDD、哪个章节、哪段原文"，禁止模糊警告。
3. **不裁决**——发现矛盾后引出双方原文，交给用户裁定谁是权威。

---

## 七、Holistic 设计审查是怎么做到的（Phase 3 深挖）

Phase 3 的 7 个检查项（3a-3g）用的不是"对账"思维，是**游戏设计理论**。每个检查项都有一条设计理论做支撑，这是它和 Phase 2 的本质区别。逐个讲，每条都配"为什么单个审查发现不了"：

### 3a 进度循环竞争

**理论**：一个游戏应该有一条玩家公认为"游戏主旨"的**主导进度循环**，其他循环都是给它供血的。三条循环平等竞争 → 玩家不知道这游戏玩的是什么，而且**只会优化一条、无视其他**。

**为什么整体才可见**：每份 GDD 单独写"我是核心循环"都很合理。战斗系统是核心、制造系统是主要活动、探索是主驱动——**只有三份放在一起，才发现它们都声称自己是老大，还发同一种 XP**。

### 3b 玩家注意力预算

**理论**：玩家同一时间能主动管理的系统是 3-4 个。数一遍核心循环瞬间同时"要求玩家做决策"的系统数量，超过 4 就标认知过载。

**为什么整体才可见**：每个系统单独设计时都假设"玩家此刻在专心玩我"。

### 3c 支配策略

**理论**：存在一个明显最优解时，其他选择都成了假选项，游戏变得无聊。四个信号：资源垄断、无风险高回报、全维度碾压、存在"明显正确"的选择。

**为什么整体才可见**："远程伤害 80%、零风险；近战伤害 100%、贴脸高风险"——这两条规则**写在同一份战斗 GDD 里**都可能被漏掉，但至少单文档审查有机会抓到；跨系统的支配策略（A 系统产出的资源让 B 系统的玩法全面占优）则只有整体可见。

### 3d 经济循环分析

**理论**：游戏的每种资源都要有来源和去向，两者失衡会出现六种病：

| 症状 | 药方方向 |
|------|----------|
| 只进不出 | 后期资源无限堆积，游戏变简单 |
| 只出不进 | 资源枯竭，系统不可用 |
| 进 >> 出 | 资源失去意义 |
| 出 >> 进 | 永久匮乏，玩家挫败 |
| 正反馈环 | 越强越好赚 → 雪球滚死 |
| 无追赶机制 | 落后加速恶化 → 不可逆 |

**为什么整体才可见**：金币的"来源"（怪物掉落）写在战斗 GDD 里，"去向"（装备、升级）写在经济 GDD 里——**来源和去向几乎注定分家**。单审任何一份都看不到失衡。

### 3e 难度曲线一致性

**理论**：随进度增长的系统必须**方向兼容、速率兼容**。敌方生命随区域指数增长（每区 ×2），玩家伤害随等级线性增长（每级 +10%）→ 第 5 区敌血 32 倍、玩家输出才 1.5 倍，差距无限拉大。

**为什么整体才可见**：一个公式写在战斗 GDD，另一个写在进度 GDD。**"增长曲线的乘除法"从来不在任何一份文档里**——它是两份文档放在一起时才诞生的数学事实。

### 3f 支柱对齐（含反支柱）

**理论**：每个系统应至少服务一条支柱。不服务任何支柱的系统 = "设计层面的范围蔓延"——它在游戏里，但不为游戏的核心服务。反支柱还要反向查：有没有系统做了反支柱明说"绝不做"的事。

**为什么整体才可见**：钓鱼系统的"宁静冥想体验"单独看毫无问题——**只有摆在"残酷战斗/紧张生存/涌现叙事"三条支柱旁边，才发现它哪条都不沾**。

### 3g 玩家幻想连贯性

**理论**：所有系统的 Player Fantasy 应该共同塑造一个"玩家在这个游戏里**是谁**"的一致身份。战斗说"你是冷血的精准杀手"，对话说"你是魅力四射的外交官——暴力永远可以避免"→ 身份精神分裂。

**为什么整体才可见**：每份 GDD 的幻想都只有一句话，合在一起才是完整人格。

### 一句话总结 Holism 的方法论

> **Phase 2 检查"文档说的是不是同一件事"；Phase 3 检查"这些事加起来是不是一个好游戏"。**

---

## 八、深入篇：Subagent 调用机制、上下文隔离与切片传递

第四节"决策 2"只给了结论——并行 subagent、传内容切片不传路径。这一节把那台机器的每个零件拆开：**调用长什么样、隔离为什么成立、切片到底怎么切的**。三层是递进关系：调用是动作，隔离是约束，切片是约束之下的解法。

顺带一个名词澄清：派子智能体的工具在本技能 frontmatter 里叫 `Agent`（`allowed-tools: Read, Glob, Grep, Write, AskUserQuestion, Agent, ...`）。你在其他 skill（如 design-system）里可能见过它叫 `Task`——**同一个工具，两代名字**。看到"派生/spawn/`subagent_type`"就是它在干活。

### 8.1 Subagent 调用机制

#### 调用长什么样

一次 spawn 的骨架：

```
Agent(
  description: "Cross-GDD consistency review",
  subagent_type: "game-designer",                  ← 决定"派谁"
  prompt: "<这个子智能体一生能看到的全部信息>",        ← 决定"它知道什么、干什么"
)
```

两个参数各管一件事，这是理解全部机制的地基：

- **`subagent_type` = 派谁**。它指向一份员工档案 `.claude/agents/game-designer.md`。子智能体启动时，档案里的人设正文、工具白名单、模型配置被装进去，作为它的出厂设置（相当于系统提示词）。
- **`prompt` = 派什么活、给什么料**。这是子智能体整个生命周期的全部信息来源——8.3 节会看到它必须装下四类货物。

所以：**档案文件是"人"，prompt 是"任务包"**。人是复用的（每次 spawn 同一个人），任务包是一次性的。

#### 为什么是 game-designer

SKILL.md 只用一句话解释选它：

> "Spawn both as `game-designer` sub-agents (`subagent_type: game-designer`) — GDD consistency and design-theory review is its domain."
> （GDD 一致性与设计理论审查是 game-designer 的领域。）

这是**领域路由**：审设计文档找设计主管，而不是技术总监或 QA。为什么不派 systems-designer？因为这不是"设计某一个系统"，而是"审查所有系统的设计"——是 game-designer 作为部门主管的职责。和 design-system 的专家路由表是同一条思路：**按任务的领域归属派人，不按"谁有空"**。

#### 并行是怎么实现的

SKILL.md 原话：

> "Spawn both as parallel `Agent` agents **simultaneously** rather than waiting for Phase 2 to complete before starting Phase 3. Collect both results before writing the combined report."
> （同时并行派出两个 Agent，不要等 Phase 2 跑完再启动 Phase 3。收齐两个结果再写合并报告。）

机制层面：**并行不是工具参数，是调用姿势**。主会话在**同一条消息里**发出两个 Agent 工具调用（两个 tool_use 块），两个子智能体才同时开跑。先发一个、等结果、再发一个 = 串行，白扔一半等待时间（串行总时长 A+B，并行总时长 max(A,B)）。

并行有前提：**独立**。SKILL.md 论证过——"they read the same GDD inputs but produce separate reports"（读同样的输入、产出各自独立的报告）。A 不需要 B 的结论，B 不需要 A 的结论，谁也不用等谁。

#### 一次调用的完整生命周期

```
主会话                              子智能体（独立的上下文窗口）
   │
   │  ① 档案装载（game-designer.md → 出厂设置）
   │  ② prompt 注入（任务包 = 它能看到的一切）
   ├───────────── spawn ──────────▶│
   │                                │ ③ 自主工作多轮：
   │      （中间过程一概不可见）        │    思考→调工具→看结果→再思考……
   │                                │
   │◀────── 一条最终消息 ────────────│ ④ 干完，只回这一条
   │
   │  ⑤ 验证返回物（三道验收，见下）
   │  ⑥ 收齐两个结果 → Phase 5 合并报告
```

三个关键点：

1. **子智能体自主跑多轮**。它不是"答一句话"就完——有自己的工具循环。但中间过程主会话**完全看不见**。
2. **返回值只有一条消息**。分析结论/报告文本一次性回传。两个 pass 各回一条，由主会话汇总——这就是为什么 Phase 5（合并报告）必须留在主会话。
3. **返回结果对用户不可见**。用户看不到子智能体说了什么，主会话必须在 Phase 5 把它整合进可见的报告里。

#### 返回值不是"回来了就算数"：三道验收

这是本 skill 对 subagent 最严格的地方——**不信任流利的回复**。

**第一道：验证工件（artifact）**。Error Recovery Protocol 的第一句就是它：

> "First, verify the artifact. If the return contract named a path, check the path exists before treating the phase as done — **a named artifact that is not on disk is a failed phase, however fluent the response reads.**"
> （先验证工件。如果返回契约指定了路径，先确认文件在磁盘上再算这阶段完成——**说好要写的文件不存在，这阶段就是失败，回复再流利也没用。**）

它还点破了一个普通错误处理抓不到的漏洞：子智能体可能"烧完一整个 phase、返回一段听起来很靠谱的开场白，但什么都没写"——这既不是 BLOCKED、不是报错、也不算"没完成"，常规触发器根本不会响。所以验收要**主动查产物**，不能被动等错误。

**第二道：区分"没发现"和"没干活"**。5-report.md 把这条落到了 Verdict 上：

> "A parallel phase that returned nothing has to be distinguished from one that returned no findings; if a spawned agent produced no report, that phase is **NOT ASSESSED, not clean** — an agent can return a fluent sentence, write nothing, and consume a phase."
> （"返回了空"和"返回了'没发现问题'"必须区分。子智能体没产出报告，那个阶段就是 NOT ASSESSED 而不是"干净"——它可能回一句流利的话、什么都不写、就烧掉一个阶段。）

**第三道：BLOCKED 不吞、不弃**。任何子智能体 BLOCKED/出错/没跑完：立刻上报用户、不在它阻塞的依赖上硬走、**永远产出部分报告**（重试范围缩小 = 更少的 GDD / 单系统）。

### 8.2 上下文隔离原理

#### 每个上下文是一间密封的房间

```
┌───────────────────────────────────────────┐
│ 主会话的上下文                              │
│  · SKILL.md + 全部 references              │
│  · Phase 1 装载的所有切片                    │
│  · 和用户的全部对话                          │
│  · （稍后）两个子智能体的返回结果              │
└────────┬──────────────────────┬───────────┘
         │ 只能经 prompt 写入     │ 只能经 prompt 写入
         ▼                      ▼
   ┌──────────────┐      ┌──────────────┐
   │  密封房间 #1   │      │  密封房间 #2   │
   │ 一致性 agent   │      │ Holism agent  │
   │ · 档案(出厂设置)│      │ · 档案         │
   │ · prompt 货物  │      │ · prompt 货物  │
   └──────────────┘      └──────────────┘
        两间房之间：完全不通信，互相不知道对方存在
```

子智能体启动时，房间里**只有两样东西**：出厂设置（档案）和任务包（prompt）。SKILL.md 把这条边界写成铁律：

> "Do not rely on the subagent to re-read these files — **it has its own context window and cannot access Phase 1 results unless they are explicitly passed in the `Agent` prompt**."
> （别指望子智能体自己去重读这些文件——它有自己独立的上下文窗口，**除非显式写进 Agent prompt，否则它接触不到 Phase 1 的任何结果**。）

墙外的东西：Phase 1 花钱装载的切片、主会话与用户的讨论、另一个 subagent 的发现。**没写进 prompt 的，对子智能体来说就不存在。**

#### 隔离不是缺陷，是花钱买来的特性（四大收益）

初学者容易把"子智能体不知道上下文"当成要绕过的麻烦。反了——这套设计主动要这个隔离：

1. **Token 经济**。14 份 GDD 全文塞进一个窗口必爆。两间密封房各装自己的切片，总量才装得下。
2. **确定性/可审计**。审查的对象**就是 prompt 里那段文字**，不多不少。不存在"子智能体自己找文件、找到别的版本/别的章节"的歧义。审了什么，可精确复述。
3. **新鲜视角**。子智能体没看过设计过程的讨论和妥协，只看到眼前章节原文，判断更接近"独立评审"。这和 design-system"创作与审查分离"是同一条原则。
4. **故障隔离**。Phase 3 把自己的窗口塞爆了，Phase 2 的工作毫发无损。一间房失火，烧不到隔壁。

#### 两扇门模型

密封房间只有两扇门：

- **输入门 = prompt**。你写什么，它知道什么。
- **输出门 = 一条最终消息**。它中间干了什么，你一概不知，只拿到结论。

这个不对称结构推导出整个 skill 的纪律：**prompt 必须"自足"**——子智能体不能反问、不能查 Phase 1、看不到用户。所以 8.3 节的四类货物一样不能少，少一样这个 phase 就是缺料开工。

#### 路径传递的陷阱：看起来传了，其实没传

初学者最容易犯的错，SKILL.md 用加粗原文拦：

> "The loaded GDD **content** each phase needs — **not file paths**. Paste the sections; the sub-agent has its own context and cannot re-read Phase 1's results, **so a path forces a full re-read**."
> （传的是内容，**不是文件路径**。粘贴章节原文——子智能体有独立上下文、拿不到 Phase 1 的结果，给路径就等于逼它全文重读。）

为什么传路径是陷阱，拆开三层：

1. **路径不是信息，是"取信息的任务单"**。把 `design/gdd/combat.md` 写进 prompt，子智能体房间里的信息量是零——它还得自己去 Read。而 Phase 1 的装载成果只存在主会话房间里，**子智能体无论如何都拿不到**。
2. **自己去读 = 全文重读**。它不知道哪些章节是审查对象，只能整篇读——Phase 1c 特意不装的 Overview、Edge Cases 全被塞回窗口，**分层装载的省钱设计整段作废**。
3. **确定性丢失**。它可能读错文件、漏读一份、读一半——"审查范围 = 主会话指定的切片"这一保证荡然无存。

一句话：**传路径 = 把"已读好、切好、验证过的料"扔掉，让子智能体重新乱抓一遍。**

### 8.3 切片传递的实现细节

#### 最反直觉的一点：切片不是 spawn 时才切的

初学者的想象：主会话读完所有 GDD → 派任务时从记忆里"摘出"相关部分。**不是。** 切片的定义在 **Phase 1c 的 Grep 模式里就写好了**：

```
Grep pattern="^## (Dependencies|Detailed Rules|Detailed Design|
               Formulas|Tuning Knobs|Acceptance Criteria|
               Player Fantasy)"
     glob="design/gdd/*.md" output_mode="content" -A 40
```

1-load.md 专门强调这不是巧合：

> "**That list is not a guess** — it is exactly the union the Parallel Execution contract below already enumerates."
> （这个清单不是拍脑袋——它就是下文并行执行契约所列需求的**并集**。）

因果链是这样的：

```
checklist 项（2a-2f、3a-3g）决定"审什么"
        ↓
"审什么"决定"需要读哪些章节"（两个 phase 需求的并集）
        ↓
并集被写成 Grep 模式 → 一次性抽出所有 GDD 的对应章节
        ↓
Grep 输出 = 切片原料 → 装载时进主会话，
                      spawn 时按各 phase 需求打包进两个 prompt
```

**一份定义，两次使用**：装载时它是"读清单"，分发时它是"发货清单"。这就是"传切片"零额外成本的原因——料在 Phase 1 就切好了，spawn 只是**打包发货**。

#### 两个包裹里的四类货物

SKILL.md 规定每次 spawn 必传四类东西，一个都不能少：

| 货物 | Phase 2（一致性）拿到 | Phase 3（Holism）拿到 | 为什么必须传 |
|------|----------------------|----------------------|-------------|
| **① 被审内容（切片）** | 每份 GDD 的 Dependencies、Detailed Design/Rules、Formulas、Tuning Knobs、Acceptance Criteria | Player Fantasy、进度/奖励结构、设计支柱 | 审查对象本身。密封房间里没有它就没东西可审 |
| **② 对账基线** | **entities.yaml 全文**（"paste the registry text, not just a file path"） | —（支柱已在 ① 里） | 2c 权威数值、2d 所有权冲突，都要拿注册表当对照底册 |
| **③ 作业指导书** | checklist 2a–2f 全部条目 | checklist 3a–3g 全部条目 | 子智能体**读不到 references/2-consistency.md**——那是主会话的活。不给清单，它就是个有料没任务的设计师 |
| **④ 技术背景** | 引擎名 + 版本（project.yaml 的 `engine.name`、`engine.version`，**逐字段独立解析**，哪个缺了单独去 technical-preferences.md 找）+ `docs/engine-reference/[engine]/VERSION.md` | 同左 | 审查者要知道设计面对哪个引擎哪个版本；VERSION.md 还带着"训练数据缺口"警告 |

两个细节值得放大：

**② 注册表必须粘全文**——SKILL.md 原文强调。注册表是 2d 的对账底册，子智能体要拿它逐项比对所有 Tuning Knobs。半份底册 = 半份审查。

**④ 引擎字段"逐字段独立解析"**——`engine.name` 和 `engine.version` 各查各的，哪个缺就哪个单独走 fallback。不是"有一个没有就整体降级"。这条纪律保证 prompt 里的引擎信息是**尽力拼齐**的。

#### checklist 和切片的对应关系（"并集"的展开）

为什么 Phase 2 拿那 5 类章节、Phase 3 拿那 3 类？对照 checklist 逐项看：

| checklist 项 | 消费的章节 |
|--------------|-----------|
| 2a 依赖双向性 | Dependencies |
| 2b 规则矛盾 | Detailed Design/Rules |
| 2c 失效引用 | Detailed Design/Rules（含 Cross-References 表） |
| 2d 所有权冲突 | Tuning Knobs + 注册表 |
| 2e 公式兼容 | Formulas |
| 2f 验收标准互查 | Acceptance Criteria |
| 3a 进度循环竞争 | 进度/奖励结构 |
| 3b 注意力预算 | Detailed Design/Rules（判断主动/被动） |
| 3c 支配策略 | Detailed Design/Rules |
| 3d 经济循环 | Detailed Design/Rules + Tuning Knobs（来源/去向） |
| 3e 难度曲线 | Formulas + 进度结构 |
| 3f 支柱对齐 | Player Fantasy + 支柱 |
| 3g 幻想连贯性 | Player Fantasy |

（这张表是教学性展开；skill 原文的权威表述就是那两句话——Phase 2 "needs each GDD's Dependencies, Detailed Design/Rules, Formulas, Tuning Knobs and Acceptance Criteria"，Phase 3 "needs Player Fantasy, progression/reward structure and the game pillars"。）

注意 3b/3c/3d 也吃 Detailed Design——所以这个章节**两个 phase 都拿**。"并集"就是这个意思：Grep 模式里每个章节只出现一次，装一次，发给两间房。

#### 明确不装进包裹的内容（负空间设计）

切片清单和 Grep 模式里**没有** Overview 和 Edge Cases。1-load.md 给了双重理由：

> "Overview is narrative restated by the Summary this skill already scanned in Phase 1a, and Edge Cases feeds no checklist item here (`/design-review` owns per-GDD completeness). **Loading them put content in three context windows — this one and both sub-agents' — that no checklist item ever read.**"
> （Overview 是 Phase 1a 已扫过的 Summary 的重复叙述；Edge Cases 不喂给本技能任何 checklist 项（逐 GDD 完整性归 `/design-review` 管）。装它们等于把内容放进**三个上下文窗口**——主会话一个、两个子智能体各一个——而没有任何 checklist 项会读它。）

注意成本算式里的**乘数是 3**：一份没人看的章节，塞进主会话浪费一次、一号房一次、二号房再一次。这就是"负空间设计"——**不传什么和传什么同样是设计决策**，而且浪费被自动放大三倍。

顺带一提 `-A 40`：Grep 每匹配到一个章节标题就向后抓 40 行。这是切片的"厚度"设定——装得下一个典型章节的主体，又不至于退化为全文。

#### 组装出来的一次真实调用（示意）

把 8.1–8.3 串起来，Phase 2 那次 spawn 的完整形态：

```
Agent(
  description: "Cross-GDD consistency review",
  subagent_type: "game-designer",

  prompt: """
  任务：对下列 GDD 章节执行跨文档一致性审查（checklist 2a-2f）。
  每个问题必须引用具体 GDD、章节、原文，并区分 Blocking / Warning。

  ── ① 被审内容（Phase 1c 的 Grep 切片）─────────────
  === combat.md ===
  ## Dependencies
  ……（≤40 行）
  ## Detailed Design
  ……（≤40 行）
  ## Formulas … ## Tuning Knobs … ## Acceptance Criteria …
  === inventory.md ===
  ……（同构）
  === …（共 N 份 GDD）===

  ── ② 对账基线：entities.yaml 全文 ───────────────
  - name: max_player_hp
    value: 100
    source: stats-system
    referenced_by: [combat, boss-abilities]
  ……

  ── ③ 作业指导书：checklist 2a-2f ─────────────────
  2a 依赖双向性：每条 depends on 必须在对面的 depended on by 有镜像……
  2b 规则矛盾：底线/上限、资源所有权、状态转换、时序、叠加……
  2c 失效引用：先查 Cross-References 表，再扫正文……
  2d 所有权冲突：两个 GDD 不许拥有同一个 tuning knob……
  2e 公式兼容：上游输出区间要落在下游输入区间内……
  2f 验收标准互查：两条标准不许无法同时通过……

  ── ④ 技术背景 ───────────────────────────────────
  引擎：Godot 4.7（来自 project.yaml）
  VERSION.md 知识缺口警告：训练数据截止 2025-05，
  4.5-4.7 的 API 未必在训练数据内……
  """,
)
```

Phase 3 的调用同构，只是货物换成：Player Fantasy 切片 + 支柱文本 + checklist 3a-3g + 同样的技术背景。**两个调用在同一条消息里发出**——这才叫并行。

#### 为什么 Phase 4 不派子智能体

顺理成章的疑问：Phase 2/3 都外包了，Phase 4（情景走查）为什么主会话自己干？观察这套机制的逻辑：**走查需要"全部系统同时在场"的全景视野**——识别 3-5 个多系统高危瞬间，得看到所有系统的交互面，而主会话是唯一同时见过全部 Summary、全部切片、支柱和系统索引的房间。把走查外包出去，就得把全景打包过去——而全景恰恰切不动（切了就不是全景）。所以它留在编排者手里。

（严格说，SKILL.md 没有解释这个选择——以上是从各 phase 输入需求推出的。但"能切的外包、切不掉的自持"是这套机制一以贯之的模式：design-system 把可切片的章节分析派出去、把需要全程对话的编排留在主会话，是同一个道理。）

### 8.4 一图流：完整数据流

```
          design/gdd/*.md（磁盘上的 N 份 GDD）
                        │
                        │ Phase 1c：Grep 按 checklist 需求的并集切片
                        ▼
        切片原料（Dependencies / Detailed Design / Formulas /
                 Tuning Knobs / Acceptance Criteria / Player Fantasy）
                        │
        ┌───────────────┴────────────────┐
        │ 按 Phase 2 需求打包             │ 按 Phase 3 需求打包
        ▼                                ▼
  ┌───────────────┐             ┌───────────────┐
  │ prompt #1      │             │ prompt #2      │
  │ ① 5 类章节切片 │             │ ① 幻想/进度/支柱│
  │ ② 注册表全文   │             │ ② （支柱已在①） │
  │ ③ checklist   │             │ ③ checklist   │
  │    2a-2f      │             │    3a-3g      │
  │ ④ 引擎信息     │             │ ④ 引擎信息     │
  └───────┬───────┘             └───────┬───────┘
          │   同一条消息、两个调用、并行开跑   │
          ▼                                ▼
  ┌───────────────┐             ┌───────────────┐
  │ game-designer  │             │ game-designer  │
  │  密封房间 #1    │             │  密封房间 #2    │
  │ （档案+任务包）  │             │ （档案+任务包）  │
  └───────┬───────┘             └───────┬───────┘
          │ 一条最终消息                  │ 一条最终消息
          └───────────────┬──────────────┘
                          ▼
          主会话：先验工件，区分"没发现"vs"没干活"
                          │
                          ▼
          Phase 5：合并两份报告 + 主会话自己的走查结果
```

---

## 九、给初学者的心智模型

把整篇压缩成一张可以带走的心智图：

```
你是一本多作者合著书的总编辑。

15 位作者（15 份 GDD）各写了一章，
每人交稿时都单独过稿了（/design-review）。

现在你要做全书通审（/review-all-gdds）：

第一步 先把所有章节的"摘要"扫一遍，心里建目录（Phase 1）
        ——顺手把"全书关键设定登记簿"拿来对账（registry）

第二步 派两个助理同时开工（并行 subagent）：
        助理 A：事实核查员（Phase 2 一致性）
          对账应收应付、同笔交易金额、凭证真实性、
          科目所有权、上下游金额范围、合同条款冲突
        助理 B：资深主编（Phase 3 Holism）
          通读全书判断：主线清不清楚？读者累不累？
          有没有一条万金油情节？经济（戏份/悬念）平衡吗？
          每章都扣题（支柱）吗？反题（反支柱）没人犯吧？
        ——你只把各助理需要的章节装进他们的公文包，
          不把整本书塞给他们

第三步 你自己再当一次读者，把书"试读"几个关键场景（Phase 4）
        ——静态检查看不出"读到第 12 页突然出戏"这种问题

第四步 出审稿意见：红（必须改）/黄（建议改）/蓝（提一嘴）（Phase 5）
        ——但"这章该听谁的"你说了不算，编辑只标记不给结论

第五步 总结论只有四种（Verdict）：
        PASS 干净
        NOT ASSESSED 有些章没审到——宁可标这个，绝不假装审过
        CONCERNS 有黄条，无红条
        FAIL 有红条，不解决不许进下一工序

第六步 存档、给问题章节贴"待修订"标签（Phase 6）

第七步 按书的现状给作者团队安排下一步（Phase 7 动态 handoff）
```

### 三条最容易踩的认知误区

**误区 1："文档都单独审过了，应该没大问题。"**
单独审过 = 每章内部完整。跨章矛盾、整体设计缺陷、边界断裂，都只在"放在一起"时诞生。这就是为什么 SKILL.md 说架构会在不一致的 GDD 上**继承**不一致——问题不会消失，只会传染。

**误区 2："PASS = 全书没问题。"**
PASS 的前提是"审过的部分没问题"。真正危险的报告是**看起来很厚、实际只覆盖了一半文档**的报告——所以这个技能宁可用 NOT ASSESSED 诚实地说"有些我没审到"，也要和"审了、全干净"严格区分。

**误区 3："AI 审查出矛盾，它会告诉我怎么改。"**
不会。协议明确禁止它裁决"哪份 GDD 是对的"。它做的事是：把两边的原文并排贴出来、标红、告诉你改哪边分别意味着什么。**拍板永远是你的**——因为"战斗系统的底线规则和 Boss 技能哪个更重要"是设计判断，不是事实判断。

---

## 十、小测验

**Q1**：`/design-review` 和 `/review-all-gdds` 的本质区别是什么？

> **A**：对象不同。`/design-review` 审**一份 GDD 的内部完整性**（章节全不全、公式有没有变量表）；`/review-all-gdds` 审**所有 GDD 之间的关系**（矛盾、失效引用、整体设计问题）。前者的最小输入是 1 份 GDD，后者的最小输入是 2 份——少于 2 份直接熔断退出。

**Q2**：为什么 Phase 2 和 Phase 3 要派成两个并行的 subagent，而不是主会话自己顺序跑？

> **A**：三个原因。①两者互不依赖（读同样的 GDD、产出独立报告），并行省时间；②全部 GDD 装进一个主会话上下文会爆掉，两个子智能体各拿各的切片实现了上下文隔离；③两个 pass 消费的章节完全不同（Phase 2 要 Formulas/Tuning Knobs，Phase 3 要 Player Fantasy/支柱），分开装避免任何内容进入一个"没有 checklist 项会读它"的上下文窗口。

**Q3**：为什么给 subagent 传"内容切片"而不是传"文件路径让它自己读"？

> **A**：subagent 有独立的上下文窗口，访问不到主会话 Phase 1 读过的任何东西。给它路径 = 逼它整个重读 = 主会话的分层装载全部白做。直接粘贴切片还保证了**审查对象是确定的**——不依赖子智能体自己找对章节、不漏读。

**Q4**：Verdict 里 NOT ASSESSED 为什么排在 PASS 之上？

> **A**：因为跨文档审查的价值全在"对照"，而它特别容易在只覆盖一小部分文档时显得很彻底——"静默跳过一半系统的审查"和"审了、全都一致"在输出上无法区分。NOT ASSESSED 是诚实声明"有些地方我没看到"，它的威胁模型是"假干净"，比"确认干净"（PASS）要警惕，但比"确认有问题"（CONCERNS/FAIL）轻。

**Q5**：报告文件名为什么必须是 `gdd-cross-review-2026-08-19.md` 这种格式，写成 `aug-19-2026` 会怎样？

> **A**：`review-scope.sh` 用 `sort | tail -1` 找"上一份审查报告"当基线，然后计算 `since-last-review` 的范围。只有 ISO 8601 格式下**字典序 = 时间序**。写成美式日期，`sort` 排出来的"最新"是错的文件，改过的 GDD 会静默逃过下次增量审查。

**Q6**：经济失衡检查（3d）为什么在 Phase 3 而不是 Phase 2？它的"来源"和"去向"明明都是数值检查。

> **A**：Phase 2 是事实核对（有明确对错），Phase 3 是设计判断。数值区间是否错位（2e）可以机械判定；但"金币只进不出是否是问题"取决于设计意图——也许这个游戏后期就是要让玩家富得流油。所以 3d 的产出是 CONCERNS（需要设计判断）而不是 Blocking（有明确对错）。分类标准是**结论需不需要人来裁决**。

**Q7**：为什么说给 subagent 传文件路径是"看起来传了信息，其实没传"？

> **A**：因为路径本身的信息量是零——它只是一张"取货任务单"。子智能体的密封房间里没有 Phase 1 的任何装载成果，给它路径只能逼它自己去整篇 Read：①Phase 1c 精心排除的 Overview、Edge Cases 全被塞回窗口，分层装载的省钱设计白做；②审查范围从"主会话指定的切片"退化成"子智能体自己找到的东西"，确定性丢失。传切片则是把"已经读好、切好、验证过的料"直接发货。

**Q8**：切片清单是什么时候定义的？为什么 1-load.md 说它"不是拍脑袋"？

> **A**：在 Phase 1c 的 Grep 模式里就定义好了——模式里列的章节（Dependencies / Detailed Design / Formulas / Tuning Knobs / Acceptance Criteria / Player Fantasy）就是两个 phase 需求的并集。因果链：checklist 决定"审什么"→"审什么"决定"读什么"→"读什么"同时就是"传什么"。一份定义两次使用：装载时当"读清单"，spawn 时当"发货清单"。原文说 "That list is not a guess — it is exactly the union the Parallel Execution contract already enumerates."

**Q9**：子智能体返回了一段非常流利、听起来很专业的回复，这个 phase 就算完成了吗？

> **A**：不算。三道验收里第一道就是"验证工件"——说好要写的文件不在磁盘上，这阶段就是失败，"however fluent the response reads"（回复写得再流利也没用）。第二道更进一步：如果它什么报告都没产出，那个阶段要标 **NOT ASSESSED** 而不是"干净"——"没干活"必须和"干了但没发现问题"严格区分。这个 skill 的立场是：**流利不是证据，工件才是**。

---

## 十一、动手

1. 打开 [SKILL.md](file:///workspace/.claude/skills/review-all-gdds/SKILL.md)，看 Parallel Execution 段怎么规定"传内容切片、不传路径"——再对照 [1-load.md](file:///workspace/.claude/skills/review-all-gdds/references/1-load.md) 的 Grep 章节装载，体会"装载切片"和"分发切片"是同一份清单的两次使用。
2. 打开 [2-consistency.md](file:///workspace/.claude/skills/review-all-gdds/references/2-consistency.md) 的 2c，数一数失效引用分了几种情况、各自的定级——思考"目标 GDD 未写"为什么按"系统索引有没有规划它"分两档。
3. 打开 [3-design-holism.md](file:///workspace/.claude/skills/review-all-gdds/references/3-design-holism.md)，为 7 个检查项各配一个你自己玩过的游戏的例子——能不能配齐 7 个，是检验你是否真理解每个检查项的标准。
4. 打开 [4-scenarios.md](file:///workspace/.claude/skills/review-all-gdds/references/4-scenarios.md) 的示例走查，注意"同一帧第二次升级"这个问题在静态检查里为什么不可见——然后用同样方法走查一个你熟悉的游戏的"捡起物品 + 背包满 + 任务需要该物品"三系统瞬间。
5. 打开 [5-report.md](file:///workspace/.claude/skills/review-all-gdds/references/5-report.md) 的 NOT ASSESSED 段，找出"一个并行 phase 返回了流利的文字但没写报告"被归为哪一档——这和 [6-write.md](file:///workspace/.claude/skills/review-all-gdds/references/6-write.md) 的 Error Recovery Protocol（"a named artifact that is not on disk is a failed phase"）是同一条原则的两种表述，对比它们。
