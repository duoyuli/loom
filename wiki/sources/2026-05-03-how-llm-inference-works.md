---
title: How LLM Inference Works
type: source
status: summarized
sources:
  - ../../raw/sources/How%20LLM%20Inference%20Works.md
source_date: 2026-05-03
source_date_basis: published
published: 2026-05-03
updated: 2026-05-21
---

这是一篇用第一性原理解释 LLM 推理路径的长线程摘录。它对知识库的长期价值不在于介绍 Transformer 这个名词，而在于把 prompt 到流式输出之间的运行时链路拆成 `tokenize / embed / prefill / decode / KV cache / quantization / serving`，补清“训练架构”和“推理系统”之间的边界。

## 这份资料是什么

- 资料来自 Akshay Pachaar 的 X article，发布日期为 2026-05-03。
- 文章主线是解释一次生成请求从文本进入模型，到首 token、后续 token、KV cache、量化和 serving 框架之间发生了什么。
- 证据边界需要保留：它是面向工程读者的解释型材料，适合沉淀推理系统的概念地图；其中关于 DeepSeek-V4 的前沿判断和具体数字，后续若要引用，应回到技术报告原文核验。

## 主要观点

1. LLM 生成的本质仍是逐 token 预测，但一次请求的性能不能只看“模型有多大”，还要看 prompt 处理、缓存和内存访问模式。
2. 推理分为两个不同瓶颈的阶段：`prefill` 处理完整输入，偏 compute-bound；`decode` 每次只生成一个新 token，偏 memory-bound。
3. KV cache 是长生成可行的关键优化，但它也把长上下文成本转成显存压力、批处理容量和后续 token 延迟问题。
4. 量化、grouped-query attention、sliding window、PagedAttention、continuous batching 和 speculative decoding 都是在围绕推理侧瓶颈做系统优化。
5. 判断一个模型“慢”，应该先问是 `TTFT` 慢还是 `ITL` 慢；长 prompt 与长输出压的是不同系统资源。

## 关键内容

### 从文本到首 token

- 文本先被 tokenizer 切成 token id，再通过 embedding table 转成向量。
- 位置信息通过 RoPE 等机制注入，因为 attention 本身不天然知道顺序。
- 多层 Transformer 通过 self-attention 在 token 间混合信息，再通过前馈网络处理每个 token 的表示。
- 最后一层把末位向量投回 vocabulary 分布并采样，得到第一个输出 token。

### Prefill 与 Decode 是两类任务

- `prefill` 阶段一次性处理全部 prompt token，可以并行计算所有 token 的 Q/K/V 和注意力矩阵，主要吃 GPU 算力吞吐。
- `decode` 阶段每轮只处理一个新 token，但必须读取模型权重和历史 KV cache，算术量小、内存访问重。
- 这解释了为什么首 token 延迟和流式 token 间隔经常不是同一个优化问题。

### KV cache 是优化，也是成本

- 没有 KV cache，生成第 N 个 token 时会反复重算前面所有 token 的 K/V，长生成会极慢。
- 有了 KV cache，系统保存每层历史 K/V，只为新 token 追加缓存；速度大幅提高，但显存随 token 数、层数和 hidden size 增长。
- 因此长上下文不只是“窗口变大”，还会吞掉批处理空间，影响并发、延迟和成本。

### Serving 系统才决定体感性能

- vLLM、TensorRT-LLM、Text Generation Inference 这类 serving 框架会在模型循环外增加 continuous batching、speculative decoding 和更精细的内存管理。
- 这些优化说明推理性能已经是系统工程问题：同一模型在不同 serving 栈上的体感差异，可能来自缓存布局、批处理策略、量化位宽和内存调度。

## 纵向线索

- 相比 [Attention Is All You Need](2017-06-12-attention-is-all-you-need.md) 关注架构如何训练和建模序列，这篇来源关注现代 decoder-only 模型如何被服务出来。
- 它把 Transformer 的后续演化从“架构选择”推进到“运行时系统”：KV cache、PagedAttention、speculative decoding 等都不是原论文核心内容，而是大规模服务中的约束反推。

## 横向线索

- 对 [Transformer 架构](../concepts/transformer-architecture.md) 而言，这篇来源补入推理侧的 prefill/decode、KV cache 和 long-context 成本。
- 对 [系统约束与训练配方](../concepts/system-and-training-constraints.md) 而言，它补清训练系统与推理系统的差别：训练看梯度和吞吐，推理看首 token、token 间隔、显存和并发。
- 对 [大模型训练流水线](../concepts/llm-training-pipeline.md) 而言，它说明部署反馈和 serving 成本会反过来影响模型架构与产品能力边界。

## 与知识库的相关性

这份来源补上当前知识库训练栈里相对薄的一层：模型部署和推理系统。它能防止把所有模型体感差异都归因到训练或后训练，也能解释为什么上下文长度、量化和 serving 框架会成为产品能力的一部分。

## 可靠性判断

- 作为推理系统入门地图，这篇资料结构清晰，适合连接 Transformer、系统约束和训练流水线页面。
- 它仍是解释型二手材料，不替代 vLLM、TensorRT-LLM、PagedAttention 或具体模型技术报告。
- 文中关于前沿架构压缩 KV cache 的说法，应作为后续研究线索，而不是当前知识库里的定论。

## 受影响页面

- [Transformer 架构](../concepts/transformer-architecture.md)
- [系统约束与训练配方](../concepts/system-and-training-constraints.md)
- [大模型训练流水线](../concepts/llm-training-pipeline.md)

## 开放问题

- 是否需要单独建立“LLM 推理系统”概念页，承接 `prefill / decode / KV cache / batching / quantization` 这组长期复用问题。
- 如果继续摄入推理侧资料，优先补 vLLM / PagedAttention、TensorRT-LLM 还是 speculative decoding 的一手材料。

## 来源

- [原始摘录](../../raw/sources/How%20LLM%20Inference%20Works.md)
- [原文链接](https://x.com/akshay_pachaar/article/2050941458614751327)
