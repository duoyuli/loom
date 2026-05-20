---
title: Hermes 凭什么两个月接棒 OpenClaw？
type: source
status: summarized
sources:
  - ../../raw/sources/Hermes%20%E5%87%AD%E4%BB%80%E4%B9%88%E4%B8%A4%E4%B8%AA%E6%9C%88%E6%8E%A5%E6%A3%92%20OpenClaw%EF%BC%9F.md
source_date: 2026-04-15
source_date_basis: published
published: 2026-04-15
updated: 2026-04-24
---

这是一篇把 Hermes、OpenClaw 与 Claude Code 放在同一条“干预递减”光谱上比较的文章。它对本知识库最有长期价值的判断不是谁更火，而是：许多 Agent 之间的差异不在功能表，而在谁替用户做决定、何时自动写入技能与记忆、以及用多硬的规则来换稳定性。

## 这份资料是什么

作者从 Skills、Memory、上下文压缩与安全机制四个方向，对 Hermes 和 OpenClaw 的默认自动化程度做对比，并顺带把 Claude Code 放进“项目导向记忆”的参考系里。

## 主要观点

1. Hermes 与 OpenClaw 在调度、子 Agent、浏览器、TTS、Vision、图像生成和消息渠道等功能表上高度重合，真正差异主要在自动化默认值而不是功能有无。
2. Hermes 的核心优势被作者归纳为：把 Skill 生成、Skill 进化、记忆写入和风险控制更彻底地前置为系统默认行为，而不是交给用户手动触发。
3. 三家系统的记忆边界并不相同：Claude Code 偏项目隔离，OpenClaw 偏跨项目长期档案，Hermes 偏更主动的反思式写入与可切换后端。
4. 文章把 Hermes 的稳定性来源解释为“把复杂度藏进死规则”：如工具调用阈值、固定轮数 nudge、字符串替换式压缩、冻结记忆快照、正则风险审批、Hook 隔离等。
5. 资料后半段的大量产品判断和版本走势预测，属于作者的市场叙事与工程推断，不宜直接固化为长期事实。

## 关键内容

### Skills：差异不在“有没有”，而在“谁来按下启动键”

- 文章认为 Hermes 与 OpenClaw 都有 Skills，但 OpenClaw 更依赖手动创建、安装和授权，Hermes 则把复杂任务、失败后自救和用户纠正直接转成 Skill 创建触发。
- Hermes 的 Skill 进化不是直接覆盖，而是离线评估后生成 PR，仍保留人类审核这一最后门槛。
- 这篇资料因此把两者差异压成一句话：OpenClaw 提供机制，Hermes 提供默认自动化。

### Memory：作用域、触发时机与检索方式各不相同

- Claude Code 的 auto-memory 被作者描述为项目内、工作导向、带定期整理，但不跨项目记住“你是谁”。
- OpenClaw 的记忆被描述为更偏长期档案：`MEMORY.md`、`USER.md` 等文件跨项目共享，写入时机偏被动，常发生在压缩前的 silent turn 或会话收尾阶段。
- Hermes 则被写成更主动的路线：默认或可选后端、固定轮数反思、全局共享与全文检索，让写入和翻旧账都更积极。
- 文章最值得保留的不是具体产品优劣，而是这条判断：记忆系统差异往往首先体现在“给谁服务、何时写、跨不跨项目、默认读什么”。

### Harness：用硬规则换稳定，而不是把一切交给模型

- 资料把 Hermes 的很多设计解释为“模型判断不可信，那就写成死规则”：工具调用次数、反思频率、错误纠正触发、风险命令审批等都不交给模型自由裁量。
- 文中举的实现样本包括：到达阈值后触发字符串替换式 `ContextCompressor`、记忆快照会话内冻结、正则黑名单式 Smart 审批、以及多数 Hook 只做 `fire-and-forget` 观察不接管主循环。
- 这些机制共同说明，自动化并没有消灭复杂度，只是把复杂度从用户手里挪到了系统默认策略里。

### 产品光谱：从“人来盯”到“系统代做决定”

- 文章尝试把 Claude Code、Codex、OpenClaw、Hermes 放在一条从强人工控制到强自动化代理的光谱上。
- 在这个框架里，Hermes 抢占的不是“能力全面领先”的位置，而是“更少打扰用户”的体验叙事高地。
- 末尾提到的 OpenClaw `Dreaming` 与 `Active Memory`，则被作者解读为它也在往更主动的记忆路线补课。

## 纵向线索

- 这篇资料与 [一文搞懂Hermes：新顶流Agent如何从经验中自我进化](2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md) 形成互补：后者更像内部技能闭环 anatomy，这篇则把 Hermes 放回与 OpenClaw、Claude Code 的比较框架里。
- 它也和 [深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践](2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md) 形成前后呼应：一篇解 OpenClaw 本身，另一篇解释 Hermes 为何在同类机制上显得更“自动”。
- 文中出现的大量近期版本动态、星标与社区风向，更适合作为时间切片线索，而不是沉淀为对象页的永久事实。

## 横向线索

- 对 [OpenClaw](../entities/openclaw.md) 而言，这篇资料帮助把它从“功能集合”重新理解为一种较强文件驱动、较弱默认自动化的产品定位。
- 对 [Hermes Agent](../entities/hermes-agent.md) 而言，它补上了一条关键比较判断：Hermes 的差异更多是自动化边界和系统默认值，而不是工具表断代领先。
- 对 [Agent 记忆系统](../concepts/agent-memory-systems.md) 而言，这篇资料把记忆系统比较推进到写入触发、作用域和检索方式的层面。
- 对 [Harness Engineering](../concepts/harness-engineering.md) 而言，它补了一种更保守的工程立场：当模型不稳定时，宁可用笨规则、冻结快照和硬阈值换确定性。
- 对 [Hermes 与 OpenClaw：默认自动化、记忆作用域与 Harness 取舍](../analyses/hermes-vs-openclaw-default-automation-comparison.md) 而言，这篇资料是当前最直接的比较型证据入口。

## 与知识库的相关性

这份资料适合沉淀到以下长期主题中：

- [Hermes Agent](../entities/hermes-agent.md)
- [OpenClaw](../entities/openclaw.md)
- [Harness Engineering](../concepts/harness-engineering.md)
- [Agent 记忆系统](../concepts/agent-memory-systems.md)

## 可靠性判断

- 这是高信息密度的比较型二次解读，最适合回收“比较维度”和“作者观察到的默认策略差异”。
- 文中混合了功能对比、源码解读、产品判断、社区叙事和未来走势预测，因此需要明显保留证据边界。
- 尤其是星标、近期发布时间线、某些功能是否“默认开启”、以及对 Anthropic/OpenClaw 近期动作的表述，都属于时间敏感信息。
- 与对象页和概念页兼容的部分，主要是那些不依赖短期热度的结构判断：Skill 触发方式、记忆作用域、默认自动化程度和确定性 Harness。

## 受影响页面

- 新增：`wiki/sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md`
- 更新：`wiki/entities/hermes-agent.md`
- 更新：`wiki/entities/openclaw.md`
- 更新：`wiki/concepts/harness-engineering.md`
- 更新：`wiki/concepts/agent-memory-systems.md`

## 开放问题

- 文中提到的 OpenClaw `Dreaming`、`Active Memory` 与 Hermes `v0.7` 后端调整，是否需要继续摄入官方更新日志或仓库提交作为一手证据。
- “默认自动化更强”是否会长期转化为更高留存或更强能力，目前仍是产品叙事，不是知识库结论。
- 当前已可支撑独立 `comparison` 页面，但若要把比较从结构判断推进到更细实现对照，仍缺官方文档、release note 和代码级一手来源。

## 来源

- [原始摘录](../../raw/sources/Hermes%20%E5%87%AD%E4%BB%80%E4%B9%88%E4%B8%A4%E4%B8%AA%E6%9C%88%E6%8E%A5%E6%A3%92%20OpenClaw%EF%BC%9F.md)
- [原文链接](https://mp.weixin.qq.com/s?__biz=Mjc1NjM3MjY2MA==&mid=2691567081&idx=1&sn=818da2e31c305032dbabf37b44c79bec&poc_token=HNFJ4mmjCFgKQwX6e0Y94F-hjY9oPEWMs7yaq5O8)
