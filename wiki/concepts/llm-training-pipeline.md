---
title: 大模型训练流水线
type: concept
status: active
sources:
  - ../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md
  - ../sources/2026-05-03-how-llm-inference-works.md
updated: 2026-05-21
---

大模型训练流水线指的是：模型能力不是由单次预训练独立决定的，而是由预训练、数据工程、系统约束、后训练、评测奖励、Agent 训练和部署反馈共同塑形的一整条链路。

## 定义

这一路线把“模型为什么变强”拆回生产过程来看。参数规模仍重要，但在前沿模型里，用户实际感受到的差异越来越多出现在预训练之后的后半段。

## 主要结构

### 底座形成

- 预训练决定知识压缩、泛化潜力和可被后续激活的空间。
- [数据配方](data-recipe.md) 决定模型更偏向哪些能力，去重、污染控制和数据配比属于能力设计，不只是清洗。
- [系统约束与训练配方](system-and-training-constraints.md) 决定上下文长度、架构选择、并行方式和成本边界。

### 行为塑形

- 指令微调把基础能力转成更可用的回答形式。
- 偏好优化、强化学习和拒绝采样把“好回答”的定义接入训练回路。
- 评测、`grader` 与 `reward` 决定模型会被往哪个方向推。

### 环境耦合

- 到了 [Agent 训练](agent-training.md)，训练目标扩展为长轨迹任务表现，而不只是单轮回答正确率。
- 工具环境、上下文裁剪、检索、记忆更新和 harness program 一起进入优化范围。
- 发布后的蒸馏、专用化和生产流量回灌，说明训练栈在部署后仍持续运行。
- 推理系统则是部署反馈中的另一条硬约束：`prefill / decode`、KV cache、量化、continuous batching 和 speculative decoding 会决定模型能以什么成本、延迟和并发形态被交付给用户。

## 这份资料带来的关键判断

- 解释模型进步时，不能只看参数量和预训练数据规模。
- 用户感知到的“更会用工具”“更听指令”“更稳定”，往往来自后训练、奖励设计和外层 harness。
- 发布版本本身也是产品决策，不一定等于训练曲线最右端的 checkpoint。
- 用户感知到的“更快”也不一定来自模型能力提升；可能来自 serving 栈、KV cache 管理、量化策略或批处理调度。

## 与其他概念的关系

- [数据配方](data-recipe.md) 与 [系统约束与训练配方](system-and-training-constraints.md) 共同解释“底座形成层”到底由哪些决策构成。
- [后训练与奖励设计](post-training-and-reward-design.md) 是这条流水线里最直接塑造用户体验的一段。
- [Agent 训练](agent-training.md) 说明训练目标如何从权重优化扩展到环境与轨迹。
- [Harness Engineering](harness-engineering.md) 和 [Context Engineering](context-engineering.md) 则对应这条流水线在应用层和训练层的外部控制程序。

## 对知识库的启发

- 当前知识库原本偏重 Agent 运行时方法论，这个概念页把这些方法论放回模型供应链的更大背景里。
- 后续摄入新材料时，可以先判断它是在补“底座形成”“行为塑形”还是“环境耦合”哪一层。

## 开放问题

- 后续是否需要把“蒸馏与专用化”继续拆成独立概念页。
- 对开源模型分析时，哪些公开信号足以判断提升主要来自哪一层。

## 来源

- [你不知道的大模型训练：原理、路径与新实践](../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md)
- [How LLM Inference Works](../sources/2026-05-03-how-llm-inference-works.md)
