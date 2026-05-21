# 概念索引

本页是 `wiki/concepts/` 的目录入口。概念页负责沉淀主题、方法、框架、争议和长期重复出现的术语边界。

## 方法

- [Agentic Engineering](agentic-engineering.md)：把 AI Agent 嵌入软件工程全链条，并用上下文、规格、验证、知识回写和生产系统分层维持生产级约束。
- [Prompt Engineering](prompt-engineering.md)：把任务表达写成模型可理解、可迭代、可评测的指令资产。
- [Context Engineering](context-engineering.md)：管理 Agent 每步推理真正看到的上下文。
- [信息分层设计](information-layering-design.md)：把资源拆成摘要层、核心层和按需原始层，降低 token 浪费并提高检索质量。
- [Agent Session Management](agent-session-management.md)：在长任务里通过 `rewind / compact / clear / subagent` 主动管理会话边界与历史保留。
- [Agent 记忆系统](agent-memory-systems.md)：管理跨步骤、跨 session 的持久状态写入、存放、检索与演化。
- [Spec-driven Development](spec-driven-development.md)：先写契约，再让 Agent 动手实现。
- [Harness Engineering](harness-engineering.md)：为 Agent 建立约束、反馈与清理体系。
- [Agent 评测](agent-evaluation.md)：为检索、生成、工具行为、长链路一致性和生产健康建立可回归的质量判断体系。

## 知识工作流

- [Agent 学习闭环](agent-learning-loop.md)：采集、提炼、应用三段式知识循环。

## 能力心智

- [Jagged Intelligence](jagged-intelligence.md)：模型能力分布并不平滑，需要通过持续协作建立边界直觉。

## 模型架构

- [循环神经网络（RNN）](recurrent-neural-networks.md)：以递归隐藏状态逐步处理序列的经典骨架，暴露出长程依赖、梯度传播与串行计算之间的张力。
- [Transformer 架构](transformer-architecture.md)：用 self-attention 取代 recurrence 的序列建模骨架，重写并行性、路径长度与训练成本前提。

## 训练栈

- [大模型训练流水线](llm-training-pipeline.md)：把模型能力形成看成预训练到部署反馈的连续链路。
- [数据配方](data-recipe.md)：数据来源、配比、去重与合成数据如何塑造能力分布。
- [系统约束与训练配方](system-and-training-constraints.md)：算力、并行、架构与稳定性如何提前锁定能力边界。
- [后训练与奖励设计](post-training-and-reward-design.md)：解释模型为什么会被推向某种回答风格、偏好和任务行为。
- [Agent 训练](agent-training.md)：描述训练目标如何扩展到工具环境、长轨迹任务和 harness program。

## 相关页面

- [总索引](../index.md)
- [分析索引](../analyses/index.md)
