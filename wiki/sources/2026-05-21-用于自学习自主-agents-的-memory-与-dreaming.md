---
title: 用于自学习自主 Agents 的 Memory 与 Dreaming
type: source
status: summarized
sources:
  - ../../raw/sources/%E7%94%A8%E4%BA%8E%E8%87%AA%E5%AD%A6%E4%B9%A0%E8%87%AA%E4%B8%BB%20Agents%20%E7%9A%84%20Memory%20%E4%B8%8E%20Dreaming.md
source_date: 2026-05-21
source_date_basis: published
published: 2026-05-21
updated: 2026-05-23
---

这是一份 Anthropic 关于 Memory 与 Dreaming 的演讲转录摘录。它对知识库最有价值的部分，不是再次证明 Agent 需要记忆，而是把记忆系统推进到平台原语层：用文件系统式 Memory 承载跨 session、跨 Agent 的共享状态，再用离线 Dreaming 循环整理、验证和优化这些记忆。

## 这份资料是什么

- 资料来自 BestBlogs 对 Anthropic 演讲视频的摘录，原视频标题为 `Memory and dreaming for self learning agents`。
- 演讲者 Ravi 负责 Anthropic API knowledge 团队，主题是 Cloud managed agents 中的 Memory 与 Dreaming。
- 正文带有产品发布和演示性质，适合沉淀 Anthropic 对平台级记忆原语的设计取舍；具体效果数字需要保留供应商自述边界。

## 主要观点

1. Agent 能处理更长任务后，关键瓶颈不再只是接入工具，而是如何把前一次任务的经验带到下一次任务。
2. Anthropic 将 Memory 建模为 Claude 熟悉的文件系统，让模型用已有的文件导航、编辑和组织能力来维护记忆。
3. Memory 支持多 Agent / 多环境共享，并通过只读、读写等作用域控制不同层级的组织知识。
4. 生产级 Memory 需要版本历史、diff、写入归因、导出、删改和乐观并发控制，否则多 Agent 共享很容易互相覆盖或污染。
5. 单个 Agent 在任务中边做边写 memory 往往只是局部最优；跨 session、跨 Agent 的重复错误、低效模式和碎片化记忆需要独立优化循环。
6. Dreaming 是从主 Agent loop 外拆出来的记忆整理过程：分析 session transcripts 和现有 memory，提出更好的组织、去重、纠错和指导性更新。
7. Dreaming 的价值在于把 Memory 从“任务中的笔记”推进到“组织级持续学习层”，但它仍需要可验证输出和 adoption 边界。

## 关键内容

### 文件系统式 Memory

- 演讲把 Memory 设计为虚拟文件系统，而不是只暴露为隐式向量库或专用记忆工具。
- 这个设计的前提是：当前 Claude 系列模型已经擅长在虚拟环境中导航文件、用 familiar tools 读写和组织文本，并能判断哪些上下文值得保存给未来自己。
- 这条路线与 Skills 的设计哲学相似：平台提供简单、可组合的原语，让模型在熟悉结构中发挥能力，而不是把所有策略都写死在 harness 里。

### 多 Agent 共享与企业控制

- Memory 不只服务单个 Agent，也面向同一环境中的多个 Agent 和跨环境协作。
- Anthropic 提供不同作用域：例如组织级 memory 可以只读且低频更新，任务或团队级 memory 可以读写并快速演化。
- 为避免多个 Agent 互相覆盖写入，系统采用乐观并发控制；为支持生产治理，系统还提供版本历史、diff、写入归因、导出和 redaction 等能力。
- 这说明多 Agent 记忆共享的核心不是“大家都能看到同一份文件”，而是共享边界、写入责任、审计和回滚。

### Dreaming：离线记忆优化循环

- 演讲把普通 memory 写入比作“边工作边记笔记”，但指出这种写入在复杂多 Agent 场景里容易产生局部最优：重复错误、重复低效模式、碎片化和冗余。
- Dreaming 是 decoupled feedback loop：Agent 写 memories，Dreaming 在主任务路径之外分析 transcripts 与现有 memory，再生成一个更有组织、更可用的 memory snapshot。
- 它可以 ad hoc、nightly、hourly 或由 session end 等事件触发，并通过 API 控制。
- 由于 Dreaming 不在 hot path 上，它不会增加主 Agent 任务延迟，也不会让执行中 Agent 在“完成任务”和“改善长期记忆”之间做目标折中。

### SRE 演示样本

- 演示中的 SRE 平台把只读组织知识 memory 用于 SLO、runbook 和 on-call mapping，把读写团队 memory 用于具体告警处置状态。
- 当一个 Agent 发现修复已经在路上，后续 Agent 可以通过共享 memory 知道同类问题已有处理动作，从而避免重复调查。
- Dreaming 示例则从过去七天的一批 session 中识别 recurring pattern，例如 CPU spike 后固定时间触发告警，并把这种跨 session 模式写成更高质量的 triage guidance。

## 纵向线索

- 这份资料把已有 [Agent 记忆系统](../concepts/agent-memory-systems.md) 中的“多 Agent 共享”和“生命周期维护”从概念层推进到平台功能层：作用域、并发控制、版本审计、redaction 与 API 都成为 Memory 原语的一部分。
- 相比 Mem0 更强调 token-efficient retrieval 和写入算法，这条 Anthropic 路线更强调模型可操作的文件结构、共享作用域和离线优化循环。
- Dreaming 也把 [Agent 学习闭环](../concepts/agent-learning-loop.md) 中的“错误回写”扩展为后台优化过程：不是只在失败当下写规则，而是从多 session 轨迹中归纳模式，再回写成未来 Agent 可采用的记忆。

## 横向线索

- 对 [Agent 记忆系统](../concepts/agent-memory-systems.md) 而言，这份资料补强了文件系统式记忆、多 Agent 共享边界、乐观并发控制、版本审计和离线 memory curation。
- 对 [Agent 学习闭环](../concepts/agent-learning-loop.md) 而言，Dreaming 提供了一种“任务外学习”的样本：学习不必挤在主执行路径里，而可以由独立 harness 在后台分析、整理和验证。
- 对 [Harness Engineering](../concepts/harness-engineering.md) 而言，Dreaming 本身就是一个独立 harness：它有自己的输入 transcripts、目标函数、并行子 Agent、diff 输出和 adoption 边界。

## 与知识库的相关性

这条来源值得沉淀，是因为它把 Memory 的工程边界从“如何存和找”推进到“如何作为平台原语被治理和持续优化”。它补上了当前知识库在多 Agent 记忆共享实现样本上的空白，也把 Dreaming 作为一种明确的外部学习循环引入：Agent 不是只靠主循环里的 reflection 学习，还可以通过离线、异步、可审计的 memory 优化过程改进未来表现。

## 可靠性判断

- 这是 Anthropic 产品团队的一手演讲摘录，对理解其 Memory / Dreaming 设计语言有直接价值。
- 但资料本身是产品发布场景，Rakuten 错误率下降、Harvey benchmark 完成率提升等数字来自演讲自述，缺少独立复现细节。
- 因此本页保留架构思路、功能边界和设计取舍，不把具体效果数字作为通用行业结论。

## 受影响页面

- [Agent 记忆系统](../concepts/agent-memory-systems.md)
- [Agent 学习闭环](../concepts/agent-learning-loop.md)

## 开放问题

- Dreaming 输出的“verified better organized snapshot”具体如何验证，资料只给出产品演示，没有展开评测协议。
- 当 Dreaming 与人工维护的组织知识冲突时，谁有最终写入权、如何回滚、如何传播失效，仍需要更细的 governance 材料。
- 文件系统式 Memory 与向量/图谱/数据库式 Memory 的组合边界还不清楚，后续需要对照 Anthropic 官方文档、LangMem、Letta 或 ChatGPT Memory。

## 来源

- [原始摘录](../../raw/sources/%E7%94%A8%E4%BA%8E%E8%87%AA%E5%AD%A6%E4%B9%A0%E8%87%AA%E4%B8%BB%20Agents%20%E7%9A%84%20Memory%20%E4%B8%8E%20Dreaming.md)
- [原文链接](https://www.youtube.com/watch?v=IGo225tfF2I)
