---
title: LLM 推理系统
type: concept
status: active
sources:
  - ../sources/2026-05-03-how-llm-inference-works.md
  - ../sources/2026-05-19-一文看懂-kv-cache-和-prompt-cache-到底差在哪.md
updated: 2026-05-22
---

LLM 推理系统指的是模型权重已经训练完成后，如何把一次用户请求以可控的延迟、成本、显存占用和并发形态服务出来的一整套运行时工程。它包括 `prefill / decode` 分阶段执行、KV Cache、Prompt Cache、批处理、量化、speculative decoding 和 serving 框架等问题。

## 定义

推理系统不是“模型架构”的同义词。Transformer 决定了注意力计算和表示传播的基本骨架，但一次 API 请求的体感速度、首 token 延迟、流式稳定性和输入价格，还会被 serving 栈、缓存布局、批处理调度和供应商计费策略共同塑造。

当前知识库把它放在训练栈与应用工程之间：训练阶段会提前锁定上下文长度、架构和部署约束，而应用层的 prompt 装配、工具定义和上下文顺序又会反过来影响缓存命中率和成本。

## 纵向脉络

- [Attention Is All You Need](../sources/2017-06-12-attention-is-all-you-need.md) 建立了 self-attention 主干，但原始论文关注的是训练与序列建模，并不覆盖现代大模型 serving 的全部问题。
- [How LLM Inference Works](../sources/2026-05-03-how-llm-inference-works.md) 把现代 decoder-only 模型服务过程拆成 tokenization、embedding、prefill、decode、KV Cache、量化和 serving 框架。
- [一文看懂 KV Cache 和 Prompt Cache 到底差在哪](../sources/2026-05-19-一文看懂-kv-cache-和-prompt-cache-到底差在哪.md) 进一步把缓存问题从底层运行时推进到 API 计费和 prompt 前缀稳定性。

## 横向位置

### Prefill 与 Decode 是两类瓶颈

- `prefill` 一次性处理完整输入，可以并行计算 prompt 中各 token 的表示，通常更偏 compute-bound，也决定首 token 前需要付出的读题成本。
- `decode` 每次只生成一个新 token，但要不断读取模型权重和历史 KV Cache，通常更偏 memory-bound，也决定流式输出的 token 间隔。
- 因此“模型慢”需要继续拆成 `TTFT`、`ITL`、长 prompt 成本、长输出成本和并发容量，而不是只看模型名称。

### KV Cache 是单次请求内的生成优化

- KV Cache 保存历史 token 在每层 attention 中的 K/V 状态，避免 decode 阶段反复重算前文。
- 它让长生成变得可行，但也让上下文长度、层数和 hidden size 转成显存压力。
- 这类缓存通常是所有现代 LLM serving 的底层前提，不应简单等同于用户侧账单上的 prompt cache 折扣。

### Prompt Cache 是跨请求的前缀复用

- Prompt Cache 复用相同 prompt 前缀的 prefill 结果，主要影响跨请求共享 system prompt、工具定义、项目规则或长文档前缀时的成本。
- 它的命中边界通常受到模型、组织/API key、工具列表、前缀字节稳定性和缓存寿命约束。
- 对 Agent 应用而言，Prompt Cache 会直接反向塑造 prompt assembly：稳定内容靠前，动态内容靠后；工具 schema、system prompt 和项目规则尽量保持确定性。

### Serving 优化决定产品体感

- continuous batching、PagedAttention、quantization、speculative decoding 和更细的内存调度，都在围绕推理侧瓶颈做系统优化。
- 用户感知到的低延迟或低价格，可能来自模型变小、硬件更强，也可能来自缓存命中、批处理策略、量化和供应商定价策略。

## 与其他概念的关系

- [Transformer 架构](transformer-architecture.md)：提供注意力主干，但不直接解释现代 serving 里的缓存、批处理和计费机制。
- [系统约束与训练配方](system-and-training-constraints.md)：训练时的上下文长度、注意力变体和部署目标，会影响推理时的显存与成本边界。
- [大模型训练流水线](llm-training-pipeline.md)：把推理系统放在部署反馈层，说明模型交付形态也是能力供应链的一部分。
- [Context Engineering](context-engineering.md)：应用侧上下文顺序、工具定义和动态消息会影响 Prompt Cache 命中率，因此上下文工程同时也是成本工程。

## 当前判断

- 推理系统应成为训练栈之外的独立概念页，因为它已经多次影响 Agent 工程、Context Engineering 和产品成本判断。
- KV Cache 与 Prompt Cache 需要严格区分：前者是单次请求内的运行时复用，后者是跨请求前缀复用和商业计费机制。
- 缓存命中率、首 token 延迟、token 间隔和缓存读写 token 应作为可观测指标进入 Agent/LLM 应用的生产监控。

## 开放问题

- 需要补入 vLLM / PagedAttention、TensorRT-LLM、speculative decoding 和供应商 prompt caching 官方文档，避免当前页面长期停留在解释型二手材料上。
- DeepSeek Context Caching on Disk、Anthropic prompt caching、OpenAI prompt caching 的命中规则和计费边界，适合后续整理成 comparison。

## 来源

- [How LLM Inference Works](../sources/2026-05-03-how-llm-inference-works.md)
- [一文看懂 KV Cache 和 Prompt Cache 到底差在哪](../sources/2026-05-19-一文看懂-kv-cache-和-prompt-cache-到底差在哪.md)
