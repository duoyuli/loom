---
title: 大模型训练栈三层框架
type: analysis
subtype: framework
status: evergreen
sources:
  - ../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md
updated: 2026-04-15
---

这份分析把来源文章压缩成一个更适合长期复用的判断：今天前沿模型的差距，越来越不是“底模本身强多少”的单变量问题，而是底座形成、行为塑形、环境耦合三层一起作用的结果。

## 三层框架

### 1. 底座形成层

这一层包括预训练、[数据配方](../concepts/data-recipe.md) 和 [系统约束与训练配方](../concepts/system-and-training-constraints.md)，负责决定模型能学到什么、能承受多大上下文和多高成本。

### 2. 行为塑形层

这一层包括指令微调、偏好优化、拒绝采样、评测、`grader` 与 `reward`。它决定用户实际感受到的风格、可靠性和任务表现。

### 3. 环境耦合层

这一层把模型带入工具环境和长轨迹任务。上下文裁剪、记忆更新、检索策略、环境稳定性和 harness program 都属于这里。

## 这份框架最值得保留的判断

- 模型“突然变强”时，先别默认是底模升级，也可能是后训练目标、奖励链路或 harness 变了。
- 评测不是纯观察层，而是训练目标的组成部分；测什么、怎么打分，最终就会把模型推向哪里。
- Agent 时代，模型外层程序已经不只是包装层，而是能力形成的一层。
- 发布后的蒸馏、专用化和 checkpoint 选择，说明产品上线也属于训练链路的后段，而不是完全独立的收尾动作。

## 与现有知识库的连接

- 现有的 [Context Engineering](../concepts/context-engineering.md) 和 [Harness Engineering](../concepts/harness-engineering.md) 原本偏重应用侧实践。
- 这份框架说明，它们并不只是“如何把现成模型用好”，也在逐步进入模型训练和产品演化的核心回路。
- 对本知识库来说，这意味着后续可把 Agent 工作流研究与训练栈研究接成一条更完整的线。

## 证据边界

- 这份三层框架压缩的是长期可复用结构，不逐条承接原文中的所有模型案例和数值。
- 对具体论文、技术报告和产品案例的事实依赖，仍应单独回到对应一手来源页。

## 对后续摄入的指引

- 遇到新资料时，先判断它主要在解释哪一层。
- 如果资料讨论的是论文或配方细节，优先补概念页。
- 如果资料试图跨多层做趋势判断，优先补分析页而不是继续堆在来源摘要里。

## 来源

- [你不知道的大模型训练：原理、路径与新实践](../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md)
