---
title: 一文看懂 KV Cache 和 Prompt Cache 到底差在哪
type: source
status: summarized
sources:
  - ../../raw/sources/%E4%B8%80%E6%96%87%E7%9C%8B%E6%87%82%20KV%20Cache%20%E5%92%8C%20Prompt%20Cache%20%E5%88%B0%E5%BA%95%E5%B7%AE%E5%9C%A8%E5%93%AA.md
source_date: 2026-05-19
source_date_basis: published
published: 2026-05-19
updated: 2026-05-22
---

这是一篇解释 KV Cache 与 Prompt Cache 差异的中文长线程摘录。它对知识库的长期价值在于把 LLM 推理里的 `prefill / decode / KV cache` 与应用侧的 prompt 前缀稳定性、缓存命中率和 API 成本连在一起，补上“上下文工程为什么会直接变成成本工程”的一条证据线。

## 这份资料是什么

- 资料来自 X 用户 `@bozhou_ai` 的长线程，发布日期为 2026-05-19。
- 文章以 DeepSeek 缓存命中/未命中价格差为入口，解释 token 计费、KV Cache、Prompt Cache、DeepSeek Context Caching on Disk，以及 Claude Code 团队围绕 prompt caching 的工程规则。
- 证据边界需要保留：它是解释型二手材料，适合沉淀概念关系和工程启发；涉及 DeepSeek、OpenAI、Anthropic 的具体价格、缓存寿命和实现细节，后续引用时仍应回到官方定价页或技术文档核验。

## 主要观点

1. LLM API 成本按 token 计量，而缓存问题本质上是在区分哪些 token 需要新算、哪些 token 可以复用既有计算结果。
2. KV Cache 发生在单次请求内，保存历史 token 在注意力层里的 K/V 状态，让 decode 阶段不必反复重算前文；它是推理运行时的底层优化，一般不直接改变用户侧 API 计费。
3. Prompt Cache 发生在跨请求的共享前缀上，复用相同 prompt 前缀的 prefill 结果，直接影响缓存读写 token 和输入成本。
4. `prefill` 与 `decode` 是理解两类缓存差异的关键分界：Prompt Cache 主要减少读题阶段的重复计算，KV Cache 主要减少逐 token 生成阶段的重复计算。
5. 对 Agent 和代码助手来说，缓存命中率依赖稳定前缀：静态 system prompt、工具定义和项目规则应靠前且保持确定性，动态时间、用户消息、文件变化和随机字段应放到后部消息通道。
6. 工具列表、模型切换和 system prompt 变动都会破坏 cached prefix；因此工具 defer loading、固定工具顺序、subagent handoff 和 cache 命中率监控都属于上下文工程的一部分。

## 关键内容

### Token 是缓存和计费的共同粒度

- 不同模型的 tokenizer 会把同一段文本切成不同 token 数，因此缓存命中率最终仍要以 API 返回的 usage 或供应商日志为准。
- 文章把 token 计费与算力消耗绑定起来：长 prompt、长输出和重复前缀的成本差异，都要落回 token 级计算。

### KV Cache 解决单次生成里的重复计算

- 自回归生成每产生一个新 token 都需要看见前文；如果不缓存历史 K/V，长输出会不断重算前文。
- KV Cache 保存每层 attention 中已经算出的 K 和 V，让后续 decode 只计算新 token 自己的部分。
- 这条线补强了 [How LLM Inference Works](2026-05-03-how-llm-inference-works.md) 已经记录的判断：KV Cache 提升生成速度，但也把长上下文压力转成显存、批处理容量和延迟问题。

### Prompt Cache 解决跨请求里的重复 prefill

- 文章把 Prompt Cache 解释为跨请求复用相同前缀的 prefill 结果：多个请求有相同 system prompt、工具定义或长文档前缀时，后来的请求可以命中缓存。
- 这与 KV Cache 的差别不只是名字不同，而是作用阶段、作用域和商业后果不同：一个偏底层运行时，一个偏服务商暴露给客户的计费与成本优化机制。
- DeepSeek 的 Context Caching on Disk 被作为特殊样本提出：文章认为其把 prompt 前缀的 prefill 结果放到磁盘层，从而扩大缓存寿命和命中范围；这条应作为后续核验官方材料的线索，而不是当前定论。

### Cache-friendly prompt 是上下文工程问题

- Claude Code 的经验被总结为“静态在前，动态在后”：全局 system prompt 与工具定义、项目规则、会话上下文、对话消息应按稳定性从高到低排列。
- 时间戳、用户变化、文件变化等动态信息更适合放在后续 user message 或 tool result，而不是改写 system prompt。
- 缓存通常绑定模型、工具列表和组织/API key；中途换模型、增减工具、注入非确定性字段，都会导致缓存前缀失效。
- 因此缓存命中率应像 latency、cost 和 uptime 一样被观测，而不是只当 API 账单上的附带字段。

## 纵向线索

- 相比 [How LLM Inference Works](2026-05-03-how-llm-inference-works.md) 从推理链路解释 `prefill / decode / KV cache`，这份资料进一步把缓存推进到 API 产品与应用层 prompt 装配。
- 它也补上了 Agent 工程中一个更实际的约束：上下文放在哪里，不只影响模型是否能读到，也影响前缀是否稳定、缓存是否命中、成本是否可控。

## 横向线索

- 对 [LLM 推理系统](../concepts/llm-inference-systems.md) 而言，这份资料补清 KV Cache 与 Prompt Cache 的作用阶段、作用域和成本边界。
- 对 [Context Engineering](../concepts/context-engineering.md) 而言，它把“前缀稳定性”提升为上下文工程的明确目标。
- 对 [系统约束与训练配方](../concepts/system-and-training-constraints.md) 和 [大模型训练流水线](../concepts/llm-training-pipeline.md) 而言，它说明 serving 侧缓存策略也会成为产品成本和体验的一部分。

## 与知识库的相关性

这份来源让推理系统、上下文工程和 Agent 工具装配之间的关系更清楚：缓存不是单纯的底层优化，而是会反向塑造 system prompt、工具定义、动态消息、subagent handoff 和观测指标的应用架构约束。

## 可靠性判断

- 作为中文解释型材料，文章结构清楚，适合用于建立 KV Cache 与 Prompt Cache 的概念边界。
- 文中包含若干供应商价格、缓存寿命、DeepSeek 磁盘缓存和 Claude Code 工程实践的说法；这些内容在知识库中应作为线索使用，正式引用前需要补官方文档或一手工程材料。
- 文中把工程启发压成“静态在前，动态在后”，可作为上下文工程原则保留，但不应替代具体供应商的缓存规则。

## 受影响页面

- [LLM 推理系统](../concepts/llm-inference-systems.md)
- [Context Engineering](../concepts/context-engineering.md)
- [系统约束与训练配方](../concepts/system-and-training-constraints.md)
- [大模型训练流水线](../concepts/llm-training-pipeline.md)
- [Transformer 架构](../concepts/transformer-architecture.md)

## 开放问题

- 是否需要补入 OpenAI、Anthropic、DeepSeek 的官方 prompt caching / context caching 文档，形成一篇供应商缓存策略 comparison。
- 是否需要把 Claude Code prompt caching 的一手材料单独摄入，以支撑工具列表稳定、defer loading 和 cache 命中率监控这些工程判断。

## 来源

- [原始摘录](../../raw/sources/%E4%B8%80%E6%96%87%E7%9C%8B%E6%87%82%20KV%20Cache%20%E5%92%8C%20Prompt%20Cache%20%E5%88%B0%E5%BA%95%E5%B7%AE%E5%9C%A8%E5%93%AA.md)
- [原文链接](https://x.com/bozhou_ai/status/2056646380152930670?s=46)
