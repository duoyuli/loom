---
title: 系统约束与训练配方
type: concept
status: active
sources:
  - ../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md
  - ../sources/2017-06-12-attention-is-all-you-need.md
  - ../sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md
  - ../sources/2026-05-03-how-llm-inference-works.md
updated: 2026-05-21
---

系统约束与训练配方描述的是：大模型训练不是单机上的抽象优化题，而是被 GPU 数量、显存、并行方式、上下文长度、架构选择、容错与成本强约束的一类分布式系统工程。很多产品侧看到的能力边界，在训练开始前就已被这些约束写死。

## 定义

- 这份来源把系统问题放在与算法、数据同等重要的位置。
- “训练配方”在这里不仅是超参数，还包括模型结构、并行策略、上下文长度目标、精度方案和稳定性保障。

## 这份资料中的关键点

### 能力边界会提前锁死

- 128K context、多模态、single accelerator、量化友好等目标，都会在训练阶段反向约束参数规模、batch size、并行策略和 serving 成本。
- 这些不是发布时再补的 feature，而是训练开始前就要做出的配方决策。

### 架构选择是成本与效果折中

- [Attention Is All You Need](../sources/2017-06-12-attention-is-all-you-need.md) 是一手例子：把 recurrence 换成 self-attention，不只改变模型表达方式，也直接改变最少串行操作数、最大路径长度与训练并行度。
- [A Critical Review of Recurrent Neural Networks for Sequence Learning](../sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md) 则补上另一侧证据：RNN 时代的主流困难，正是时间步串行推进、长程依赖训练困难以及 `BPTT` 跨时传播不稳定。
- MoE、长上下文结构和更深网络设计，解决的是“在固定预算下如何换取更高能力密度或更长序列处理能力”。
- 这类选择通常伴随路由复杂度、负载均衡、基础设施负担和部署约束，不是单纯的研究偏好。

### 稳定训练本身就是能力

- loss spike、静默硬件错误、通信抖动和 checkpoint 回滚，都会影响最终训练成败。
- 来源把 DeepSeek-V3 这类稳定跑完全程的案例，当作实验室级系统能力的体现，而不是普通实现细节。

### 训练系统与推理系统紧密耦合但并不相同

- 训练侧关心梯度、吞吐、并行、checkpoint 和成本。
- 推理侧关心延迟、KV cache、量化和服务稳定性。
- 这条边界有助于避免把“推理部署问题”误解成“训练本身的问题”，反之亦然。
- [How LLM Inference Works](../sources/2026-05-03-how-llm-inference-works.md) 补清了推理侧内部也不是单一瓶颈：`prefill` 更偏 compute-bound，`decode` 更偏 memory-bound，因此 TTFT、ITL、显存、batch size 和量化会分别影响不同体感。

## 与其他概念的关系

- [数据配方](data-recipe.md) 决定喂什么材料，系统约束决定这些材料能以什么规模和方式被训练进去。
- [大模型训练流水线](llm-training-pipeline.md) 用更高层视角把这类约束放进整条链路。
- [Agent 训练](agent-training.md) 则把系统约束延伸到浏览器、终端、沙盒、检索和工具环境的稳定性问题。

## 对知识库的启发

- 以后看模型“为什么没上更长上下文/更低成本/更强工具能力”，不能只从算法解释，往往要先看训练时锁定了哪些资源边界。
- 对知识库自身而言，这提醒我们把“目录结构、链接方式、索引入口”视为约束设计，而不只是排版风格；很多后续可维护性问题是在结构层面提前决定的。

## 证据边界

- 当前页面主要压缩的是来源中的系统视角和案例线索，不单独替代 DeepSeek-V3、Llama 3、Gemma 3 等技术报告。
- Transformer 原始论文与 RNN 综述一起补上了“架构选择会如何改变训练约束”的历史对照，但它们本身不回答 MoE、长上下文工程或现代 serving 系统的全部问题。
- 推理系统来源补的是 serving 视角，不替代 vLLM、TensorRT-LLM、PagedAttention 或具体模型报告；涉及特定优化效果时仍应回到一手资料。
- `muP`、`WSD`、`FP8` 等术语在这篇综述中主要承担“系统配方已成为竞争点”的证据角色，尚未在本库展开成独立概念。

## 开放问题

- 是否需要把“训练稳定性 / rollback / loss spike”继续拆成单独的工程主题。
- 后续若摄入更多训练报告，是否按“系统配方”单独整理 comparison 页更合适。

## 来源

- [你不知道的大模型训练：原理、路径与新实践](../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md)
- [Attention Is All You Need](../sources/2017-06-12-attention-is-all-you-need.md)
- [A Critical Review of Recurrent Neural Networks for Sequence Learning](../sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md)
- [How LLM Inference Works](../sources/2026-05-03-how-llm-inference-works.md)
