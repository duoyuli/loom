---
title: 循环神经网络（RNN）
type: concept
status: active
sources:
  - ../sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md
updated: 2026-04-15
---

循环神经网络指的是：通过把上一步的隐藏状态带入下一步计算，让模型沿时间逐步处理序列的一类架构。它在今天未必还是默认主干，但理解序列建模为什么困难，几乎绕不开 RNN、LSTM、BRNN 和 BPTT 这条历史线。

## 定义

- RNN 用递归隐藏状态表示上下文，因此可以处理可变长度输入和输出。
- 把网络沿时间展开后，它可以被看成跨时间共享参数的深网络，训练通常依赖 `backpropagation through time`。
- `LSTM` 与 `BRNN` 是最关键的两类经典改进：前者主要缓解长程依赖的训练问题，后者允许输出同时利用过去和未来信息。

## 纵向脉络

- 这类架构回应的是固定长度向量模型和浅层滑动窗口方法的局限：它们难以保留更长的上下文。
- 早期简单 RNN 暴露出 vanishing / exploding gradients 问题，导致“理论上能表达、实践上难训练”成为长期主线矛盾。
- `LSTM` 通过门控和较稳定的误差传播路径，把 RNN 从研究性结构推向了更可用的工程骨架。
- 后来的 `seq2seq LSTM` 把编码器和解码器接起来，推动了机器翻译、图像描述等任务的进展。
- [Transformer 架构](transformer-architecture.md) 随后不是简单增强 RNN，而是改写了“序列该如何建模”的主路径：从递归隐藏状态转向全局 self-attention。

## 横向位置

- 相比 [Transformer 架构](transformer-architecture.md)，RNN 的主要代价是时间步之间更强的串行依赖，训练并行性更差。
- 相比 Markov 模型，RNN 用连续隐藏状态而不是离散状态表表示历史，因此更有表达弹性，但训练也更难稳定。
- 对 [系统约束与训练配方](system-and-training-constraints.md) 而言，RNN 是理解“架构选择如何影响并行、路径长度和训练成本”的重要对照组。
- 对 [大模型训练流水线](llm-training-pipeline.md) 而言，它属于更上游的底座架构问题，而不是后训练或 Agent 机制。

## 与其他概念的关系

- [Transformer 架构](transformer-architecture.md)：Transformer 的关键贡献之一，就是把 RNN 长期暴露出的串行瓶颈和长路径问题换成了另一组成本结构。
- [系统约束与训练配方](system-and-training-constraints.md)：RNN 说明模型骨架会直接决定训练吞吐、可并行度和长程依赖学习难度。
- [数据配方](data-recipe.md)：数据决定序列里有什么规律，RNN 决定模型如何把前文压缩进隐藏状态并逐步带到后文。

## 当前判断

- 对现代大语言模型而言，RNN 已不是主流底座，但它仍是理解序列建模史和架构演化逻辑的关键参照。
- 它最值得保留的，不是“过去曾经流行”，而是它把若干至今仍存在的问题暴露得很清楚：长程依赖、状态压缩、训练稳定性和并行性之间存在结构性张力。
- 讨论 Transformer 或更后续架构时，如果不先看清 RNN 的问题设定，很容易把后续突破误读成单一技巧升级，而不是目标函数与计算路径的整体重写。

## 开放问题

- 是否需要把 `LSTM` 从本页拆出，单独承接门控、记忆单元和流式任务优势。
- 如果后续摄入 `Bahdanau attention`、`seq2seq` 原始论文或流式 ASR 资料，应该围绕“RNN 极限”做 comparison 还是做 timeline。

## 来源

- [A Critical Review of Recurrent Neural Networks for Sequence Learning](../sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md)
