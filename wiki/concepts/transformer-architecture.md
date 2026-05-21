---
title: Transformer 架构
type: concept
status: active
sources:
  - ../sources/2017-06-12-attention-is-all-you-need.md
  - ../sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md
  - ../sources/2026-05-03-how-llm-inference-works.md
  - ../sources/2026-05-19-一文看懂-kv-cache-和-prompt-cache-到底差在哪.md
updated: 2026-05-22
---

Transformer 架构指的是：用 self-attention 而不是 recurrence 或 convolution 作为序列建模主路径的一类骨架。它最持久的贡献，不是某个局部技巧，而是把“并行性、依赖路径长度与表示交互方式”一起重写了。

## 定义

- 原始 Transformer 是 encoder-decoder 结构。
- 编码器和解码器都由重复堆叠的 attention 子层与前馈子层组成，并配合 residual connection 与 layer normalization。
- 由于没有显式时间步递归，序列顺序需要通过 positional encoding 注入。
- 原论文中的 attention 具体实现是 scaled dot-product attention，并通过 multi-head 机制让不同表示子空间并行分工。

## 纵向脉络

- 它直接回应了 `RNN seq2seq` 的核心瓶颈：训练时必须按时间步串行推进，长距离依赖的信号路径也更长。
- 新摄入的 [A Critical Review of Recurrent Neural Networks for Sequence Learning](../sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md) 补清了这层前史：在 Transformer 之前，序列学习主线长期围绕 `BPTT`、梯度跨时传播和 `LSTM` 门控缓解机制展开。
- 原论文仍把问题表述为 machine translation 和 parsing，不等于今天所有大语言模型的完整形态。
- 后来的 decoder-only、RoPE、KV cache、grouped-query attention、sparse/local attention 等做法，是沿着 Transformer 主干继续演化出来的版本，而不是原论文本身的内容。
- 原始论文已经承认全局 self-attention 的 `O(n^2)` 成本，因此 Transformer 不是“没有代价地更强”，而是用更多全局交互换取更短路径和更高并行度。
- 新摄入的 [How LLM Inference Works](../sources/2026-05-03-how-llm-inference-works.md) 把这条演化从训练架构推进到推理系统：现代 decoder-only 模型在 serving 时还会受到 `prefill / decode` 分阶段瓶颈、KV cache 显存增长和量化策略约束。
- 新摄入的 [一文看懂 KV Cache 和 Prompt Cache 到底差在哪](../sources/2026-05-19-一文看懂-kv-cache-和-prompt-cache-到底差在哪.md) 则提醒：KV Cache 属于 Transformer 推理运行时的 K/V 状态复用，而 Prompt Cache 更接近跨请求的 prefill 结果复用和计费机制，两者不应混为同一个架构特性。

## 横向位置

- 相比 [循环神经网络（RNN）](recurrent-neural-networks.md)，Transformer 把最少串行操作数降到常数级，更适合大规模并行训练。
- 相比 convolution-based sequence models，它用全局 attention 直接连接任意位置，减少长距离依赖需要跨越的层数。
- 对 [大模型训练流水线](llm-training-pipeline.md) 而言，Transformer 属于底座形成层的架构选择；它解释的是模型骨架，不解释后训练目标。
- 对 [后训练与奖励设计](post-training-and-reward-design.md) 和 [Agent 训练](agent-training.md) 而言，Transformer 是更上游的前提条件，而不是这些行为层能力本身。

## 与其他概念的关系

- [系统约束与训练配方](system-and-training-constraints.md)：Transformer 说明架构选择会直接改写训练吞吐、路径长度和扩展边界。
- [LLM 推理系统](llm-inference-systems.md)：承接 Transformer 在服务阶段遇到的 `prefill / decode / KV cache / Prompt Cache` 等运行时约束。
- [大模型训练流水线](llm-training-pipeline.md)：它是底座形成阶段最关键的一类架构决策。
- [数据配方](data-recipe.md)：数据决定学什么，Transformer 决定这些信息如何在序列内部传播和整合。
- [循环神经网络（RNN）](recurrent-neural-networks.md)：RNN 是它最重要的历史对照项，帮助解释“为什么缩短路径与提升并行度会成为骨架级优势”。

## 当前判断

- 讨论现代模型能力时，Transformer 仍是最基础的“骨架级变量”之一。
- 但“用了 Transformer”本身已经不足以解释产品差距；今天很多体感差异更多来自数据、系统、后训练与环境耦合层。
- 因此它在知识库里的作用更像一条基线：提醒我们不要把后续所有进步都误归因到同一个架构名词上。
- 新来源进一步提醒：即使底层同属 Transformer，用户感知的快慢也可能更多来自 serving 栈、缓存布局、批处理和量化，而不是架构名词本身。

## 开放问题

- 什么时候应该把 `self-attention` 单独拆页，而不是继续作为 Transformer 的组成部分来维护。
- 长上下文改造路线更适合沉淀成“Transformer 演化时间线”，还是按不同注意力机制做横向对比。

## 来源

- [Attention Is All You Need](../sources/2017-06-12-attention-is-all-you-need.md)
- [A Critical Review of Recurrent Neural Networks for Sequence Learning](../sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md)
- [How LLM Inference Works](../sources/2026-05-03-how-llm-inference-works.md)
- [一文看懂 KV Cache 和 Prompt Cache 到底差在哪](../sources/2026-05-19-一文看懂-kv-cache-和-prompt-cache-到底差在哪.md)
