---
title: Attention Is All You Need
type: source
status: summarized
sources:
  - ../../raw/sources/2017-06-12-attention-is-all-you-need-link.md
  - ../../raw/assets/Attention%20Is%20All%20You%20Need.pdf
source_date: 2017-06-12
source_date_basis: published
published: 2017-06-12
updated: 2026-04-24
---

这是一篇 Transformer 原始论文的来源页。它最值得长期保留的判断不是“attention 很重要”，而是：把 self-attention 提升为序列建模的主路径后，模型的并行性、依赖路径长度和训练成本结构会一起改变。

## 这份资料是什么

- 论文题为 `Attention Is All You Need`，对应 `arXiv:1706.03762`，正文标注为 `31st Conference on Neural Information Processing Systems (NIPS 2017)`。
- 当前摄入的 PDF 是 `arXiv:1706.03762v7 [cs.CL] 2 Aug 2023` 版本；本页引用其正文内容，但不把后来社区对 Transformer 的全部扩展逆向写回原论文。
- 论文直接提出了 Transformer 架构，并用机器翻译与 constituency parsing 实验支撑其有效性。

## 主要观点

1. 序列转导不一定需要 recurrence 或 convolution；self-attention 可以成为主干结构。
2. Transformer 的核心不是单一注意力层，而是 `multi-head self-attention + position-wise feed-forward + residual + layer norm + positional encoding` 这套整体骨架。
3. 这种架构显著提高了训练并行性，并把长距离依赖之间的最大路径长度压到常数级。
4. 在 WMT 2014 英德、英法翻译任务上，论文报告 Transformer 以更低训练成本达到或超过当时最优结果。
5. 论文还展示了该架构对英语 constituency parsing 的泛化能力，说明它不只适用于单一翻译任务。

## 关键内容

### 架构骨架

- 编码器和解码器各由 `N = 6` 层堆叠而成。
- 每层的核心子层是 multi-head attention 与 position-wise feed-forward network。
- 解码器自注意力使用 mask，以保持自回归生成约束。
- 因为去掉了 recurrence 和 convolution，论文需要显式引入 positional encoding；作者最终采用 sinusoidal positional encoding。

### 技术判断

- 论文把 self-attention、recurrent layer 与 convolution layer 放在统一坐标里比较每层复杂度、最少串行操作数和最大路径长度。
- 它最强的主张不是“attention 更准”，而是“attention 把串行瓶颈改写了”，这让训练吞吐、依赖学习和硬件利用率进入同一个设计问题。
- 多头注意力被用来缓解单头平均化带来的表示分辨率损失，并让不同头在不同子空间、不同位置上分工。

### 实验与消融

- 论文报告 `Transformer (base)` 在 WMT 2014 English-to-German 上达到 `27.3 BLEU`，`Transformer (big)` 达到 `28.4 BLEU`。
- 对 English-to-French，摘要与表 2 写的是 `41.8 BLEU`，但第 6.1 节正文写的是 `41.0 BLEU`；当前来源页保留这处内部冲突，不擅自替论文定稿。
- 消融实验显示：单头注意力明显更差；适当增大模型宽度和 `d_ff` 有帮助；dropout 对避免过拟合很关键；learned positional embedding 与 sinusoidal encoding 在该实验中结果接近。

## 纵向线索

- 这篇论文位于经典 `RNN/CNN seq2seq` 时代与后续 Transformer 主导时代之间的关键断点。
- 论文原始目标仍是 encoder-decoder 式的序列转导，不是今天常见的 decoder-only 大语言模型；后者是沿着同一主干继续演化出来的后续路线。
- 论文已经明确指出全局 self-attention 的 `O(n^2)` 代价，并把 restricted/local attention 留作未来工作，这为后来的长上下文改造埋下了问题意识。

## 横向线索

- 对 [系统约束与训练配方](../concepts/system-and-training-constraints.md) 而言，这是一手证据，说明“架构选择”会直接改变串行瓶颈、路径长度与训练成本。
- 对 [大模型训练流水线](../concepts/llm-training-pipeline.md) 而言，Transformer 更接近“底座形成层”的架构选择，而不是后训练或 Agent 层的改进。
- 对当前知识库而言，它补上了训练栈主题里此前缺少的一篇关键一手架构材料。

## 与知识库的相关性

这份来源最适合沉淀为 [Transformer 架构](../concepts/transformer-architecture.md) 这一概念页，并回写到训练栈相关主题中。它帮助知识库把“模型为什么能扩展到今天这一步”往前追溯到更基础的架构决策。

## 可靠性判断

- 这是高价值一手论文，对 Transformer 原始设计、实验设置和作者当时的技术判断具备直接证据价值。
- 但它不是现代 LLM 全景论文，不应直接拿来解释 instruction following、RLHF、tool use 或长上下文产品体验。
- 当前 PDF 是较新的 arXiv 版本，包含论文正文与再发布信息；若未来需要严格核对某个数值或表述，仍应区分 `NIPS 2017` 版本与 `arXiv v7` 版本是否存在编辑差异。

## 受影响页面

- 新增：`wiki/sources/2017-06-12-attention-is-all-you-need.md`
- 新增：`wiki/concepts/transformer-architecture.md`
- 更新：`wiki/concepts/system-and-training-constraints.md`
- 更新：`wiki/concepts/index.md`
- 更新：`wiki/index.md`
- 更新：`wiki/overview.md`
- 更新：`wiki/log.md`

## 开放问题

- 是否继续把 `self-attention`、`multi-head attention`、`positional encoding` 拆成更细的概念页，还是先保留在 `Transformer 架构` 下。
- 若后续继续摄入长上下文或稀疏注意力论文，应该优先做 `timeline` 还是 `comparison` 类型分析页。
- 论文内部关于 English-to-French `41.8 / 41.0 BLEU` 的冲突，是否需要回查官方勘误或代码仓库进一步厘清。

## 来源

- [资料指针](../../raw/sources/2017-06-12-attention-is-all-you-need-link.md)
- [原始 PDF](../../raw/assets/Attention%20Is%20All%20You%20Need.pdf)
