---
title: "Using Claude Code: Session Management & 1M Context"
type: source
status: summarized
sources:
  - ../../raw/sources/Using%20Claude%20Code%20Session%20Management%20%26%201M%20Context.md
source_date: 2026-04-16
source_date_basis: created
updated: 2026-04-16
---

这是一份围绕 Claude Code 长会话管理的实践说明。它最有长期价值的不是某个 `/` 命令本身，而是把“什么时候继续当前会话、什么时候 rewind、compact、clear 或切给 subagent”明确成上下文治理的操作界面，也提醒了 `1M context` 只是延长了跑道，并没有取消上下文预算和上下文衰减问题。

## 这份资料是什么

资料来自 `x.com` 上一篇面向 Claude Code 用户的说明帖，主题是配合新的 `1 million` 上下文窗口更新，解释 `/usage`、会话管理与 compaction 的关系。正文采用操作建议和示意图的形式，讨论长任务中如何管理上下文窗口。

## 主要观点

1. 长上下文窗口会显著改变工作方式，但不会消除会话治理；上下文越长，越需要主动决定哪些历史该保留、哪些该丢弃。
2. 所谓 `context rot`，是随着上下文膨胀，注意力被更多旧 token 分散、旧但无关的信息开始干扰当前任务；资料声称在 Claude Code 的 `1M` 模型里，这种退化常在约 `300k-400k tokens` 附近开始出现，但高度依赖任务类型，不能当成硬阈值。
3. 每一轮回复结束后都存在一个“分叉点”：继续当前会话、rewind、clear、compact 当前历史，或把下一段工作交给 subagent；这些选项本质上都在管理下一个回合的有效上下文。
4. `rewind` 往往比“在同一会话里纠错”更好，因为它能丢弃失败分支本身，只保留必要的文件读取和新学到的约束。
5. `/compact` 与 `/clear` 的差别，不在于都能减轻上下文，而在于前者依赖模型生成有损摘要，后者依赖人写一个更可控的 handoff brief。
6. “坏 compact” 常出现在会话已经很长、模型处于最迟钝状态时，还要它预测接下来工作会转向哪里；如果下一步任务方向突变，刚才的关键信息就可能被摘要掉。
7. subagent 不是单纯的并行技巧，也是一种上下文隔离机制：把高噪声的中间过程留在子上下文里，只把结论带回父会话。

## 关键内容

### 上下文窗口、compaction 与衰减

- 资料把上下文窗口定义为模型在下一次生成前能同时“看见”的所有内容，包括系统提示、对话、工具调用、工具输出和已读取文件。
- 当窗口逐渐接近上限时，需要把当前任务压缩成更小描述再继续，这里被称作 `compaction`。
- 但问题并不只发生在硬上限附近；作者强调性能会先于上限下降，因此“还没满”不等于“还适合继续塞”。

### 每个 turn 都是会话边界决策

- `Continue`：继续沿用当前轨迹。
- `/rewind`：回到更早的节点，丢弃后续失败分支。
- `/clear`：基于人工整理的 brief 开一个新会话。
- `Compact`：让模型总结已有会话，再在摘要上继续。
- `Subagents`：给下一段高噪声工作一个独立上下文窗口。

资料的核心判断是，这五种操作不是零散命令，而是同一套会话管理策略的不同分支。

### 什么时候应该新开会话

- 原文给出的经验法则是：开始一个新任务时，通常也应开始一个新会话。
- 但它也保留灰区，例如刚做完功能实现立刻写文档时，复用部分上下文可以省掉重新读文件的成本。
- 换言之，是否复用旧会话取决于两件事：已有上下文是否仍有价值，以及该任务对“上下文纯净度”的敏感度是否高于重复读取成本。

### `rewind`、`compact` 与 `clear` 的差异

- `rewind` 更适合纠错，因为它直接删掉错误尝试带来的噪声。
- `compact` 更省力，但摘要由模型决定，天然有损；可以通过附加指令聚焦它保留什么。
- `clear` 更费力，但留下的是人主动筛过的任务简报，更适合目标已经变化或需要强控制的场景。

### subagent 作为上下文隔离器

- 资料建议用一个心智判断：下一段工作的工具输出，之后还会不会再次需要？
- 如果只需要结论，不需要全部中间痕迹，那么交给 subagent 往往更划算。
- 这使 subagent 同时承担并行执行和上下文减噪两个角色。

## 纵向线索

- 这份资料把“长上下文”从容量升级问题，推进成会话生命周期治理问题。
- 其逻辑链条是：上下文窗口变大 -> 单会话可承载更长任务 -> 上下文衰减仍会先出现 -> 因此必须在 turn 边界主动选择保留、回退、压缩、清空或分流。
- 它也补足了现有知识库里 `Context Engineering` 偏抽象的一面：上下文治理不只发生在提示拼装阶段，也发生在长任务进行中的连续会话决策里。

## 横向线索

- 这份资料最直接支撑的是 [Context Engineering](../concepts/context-engineering.md) 的运行时操作面。
- 它也适合拆成更聚焦的 [Agent Session Management](../concepts/agent-session-management.md) 概念页，用来承接 `rewind / compact / clear / subagent` 这组会话边界动作。
- 对 [Harness Engineering](../concepts/harness-engineering.md) 也有侧面价值：如果系统没有提供好的 session 操作，长任务就更容易把失败轨迹和噪声持续堆回主上下文。

## 与知识库的相关性

这份资料值得沉淀，因为它把“上下文管理”从抽象概念拉回到了可执行操作。对本库最有价值的长期主题是：

- [Context Engineering](../concepts/context-engineering.md)
- [Agent Session Management](../concepts/agent-session-management.md)

## 可靠性判断

- 这是一份高相关度的一手产品实践说明，适合理解 Claude Code 团队建议用户如何治理长会话。
- 但它仍是社交媒体形式的经验说明，不是正式规格文档；其中 `300k-400k` 之类数值更像经验区间，而不是跨任务、跨模型都成立的硬规则。
- 资料中的命令语义与交互心智对 Claude Code 高度贴合，但若外推到其他 Agent IDE，还需要补更多工具侧对照材料。

## 受影响页面

- [Context Engineering](../concepts/context-engineering.md)
- [Agent Session Management](../concepts/agent-session-management.md)

## 开放问题

- 当前知识库还没有跨工具对照：Claude Code 的 `rewind / compact / clear`，与其他 Agent IDE 或 CLI 的 session 机制有哪些结构差异。
- 对长任务来说，何时应该优先“保留上下文”而非“保留智能度”，目前仍主要依赖经验判断，缺少更系统的量化依据。

## 来源

- [原始摘录](../../raw/sources/Using%20Claude%20Code%20Session%20Management%20%26%201M%20Context.md)
- [原文链接](https://x.com/trq212/status/2044548257058328723)
