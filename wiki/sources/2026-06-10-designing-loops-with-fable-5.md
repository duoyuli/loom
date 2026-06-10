---
title: Designing loops with Fable 5
type: source
status: summarized
sources:
  - ../../raw/sources/Designing%20loops%20with%20Fable%205.md
source_date: 2026-06-10
source_date_basis: published
created: 2026-06-10
updated: 2026-06-10
---

这是一篇 Anthropic 员工 @rlancemartin 发在 X 上的长帖，用 Claude Fable 5 的两个小实验说明：对这类模型，更有效的使用方式往往不是持续手动提示，而是设计自我纠错、外部评分和跨 session memory 的 loop。它的价值在于给 [Loop Engineering](../concepts/loop-engineering.md) 补了一条来自 Anthropic 运行时产品的近邻证据：`/goal`、Outcomes、独立 grader 子 Agent 和 mounted memory 都可以被读成 loop 的具体传感器与状态层。

## 这份资料是什么

- 资料是 @rlancemartin 于 2026-06-10 在 X 发布的长帖，主题是如何通过 loops 用好 Claude Fable 5。
- 资料包含两个实验样本：Parameter Golf 的长时间 ML engineering loop，以及 Continual Learning Bench 1.0 中基于 SQL 问答的跨 session memory 任务。
- 文中引用 Anthropic 文档里的 `/goal`、Claude Managed Agents Outcomes、Managed Agents memory，以及 Fable 5 prompting guide。

## 主要观点

1. Fable 5 适合放进有环境反馈的自我纠错 loop：模型执行、读取目标或 rubric 反馈、自我修正，再继续直到目标或 rubric 满足。
2. 判断者很重要。作者强调模型对自身输出做 self-critique 有问题，因此 Fable 5 更适合由独立 verifier / grader 子 Agent 在独立上下文里评分。
3. 在 Parameter Golf 这类任务中，loop 的关键不是一次生成，而是不断改训练代码、启动训练、轮询日志、读分数、决定下一轮实验。
4. 对 memory 任务，作者把有效使用记忆拆成 `fail -> investigate -> verify -> distill -> consult`：不是只记失败，而是把失败验证成事实、提炼为可复用规则，并在下一轮先查规则。
5. 结论更偏工程方法：与其直接提示和 steering 模型，不如设计能提供反馈、验证和记忆的 loop，让模型在环境里自我纠错并管理上下文。

## 纵向线索

- 对 [Loop Engineering](../concepts/loop-engineering.md) 来说，这篇资料补出会话内 loop 的更细机制：`/goal` 或 Outcomes 不只是“让模型多跑”，而是把 rubric、独立 grader、停止条件和长时间 sandbox 组合成反馈回路。
- 对 [Agent 学习闭环](../concepts/agent-learning-loop.md) 来说，`fail -> investigate -> verify -> distill -> consult` 是很适合长期保留的学习过程拆分，强调 memory 只有被验证和重新使用才算进入闭环。

## 横向线索

- 与 [Agent 评测](../concepts/agent-evaluation.md)：Outcomes 的 grader 子 Agent 把评测变成 loop 内生机制，且独立上下文能缓解自评偏差。
- 与 [Agent 记忆系统](../concepts/agent-memory-systems.md)：mounted filesystem memory 让跨 session 经验进入下一轮；但资料也显示，记忆条目如果没有调查、验证和规则化，只会停留在失败笔记层。
- 与 [Harness Engineering](../concepts/harness-engineering.md)：rubric、grader、sandbox、日志轮询和停止条件都属于模型外部的 harness 传感器。

## 与知识库的相关性

本资料不需要新建 Fable 5 或 Claude Managed Agents 实体页；它更适合回写既有 [Loop Engineering](../concepts/loop-engineering.md)、[Agent 学习闭环](../concepts/agent-learning-loop.md)、[Agent 评测](../concepts/agent-evaluation.md) 与 [Agent 记忆系统](../concepts/agent-memory-systems.md)。它把“loop 坐在 harness 之上”的抽象判断，补成了 `rubric / grader / sandbox / memory / stop condition` 这一组可操作结构。

## 可靠性判断

- 这是 X 长帖与个人实验分享，不是论文或完整 benchmark 报告；Parameter Golf 与 Continual Learning Bench 的实验设置、样本数和复现实验细节不足，不能把数值当作稳定基准。
- 作者是 Anthropic 员工，文中大量引用 Anthropic 产品与文档，存在产品推广语境。
- 资料价值主要在 loop 设计形状和机制拆分，不在 Fable 5 相对 Opus 4.7 / Sonnet 4.6 的性能结论。

## 受影响页面

- [Loop Engineering](../concepts/loop-engineering.md)
- [Agent 学习闭环](../concepts/agent-learning-loop.md)
- [Agent 评测](../concepts/agent-evaluation.md)
- [Agent 记忆系统](../concepts/agent-memory-systems.md)
- [Harness Engineering](../concepts/harness-engineering.md)

## 开放问题

- Fable 5 的 loop 表现是否能在公开 benchmark 或复现实验中稳定出现，仍需要更多一手材料。
- 独立 grader 子 Agent 能减少 self-critique 偏差，但 grader 自身的偏差、成本和停止条件误判仍需专门评测。
- `fail -> investigate -> verify -> distill -> consult` 能否成为 memory 系统通用评估协议，值得后续用更多产品和任务对照。

## 来源

- [原始摘录](../../raw/sources/Designing%20loops%20with%20Fable%205.md)
- [原文链接](https://x.com/rlancemartin/status/2064397389189071163?s=46)
