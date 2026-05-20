---
title: Agent Session Management
type: concept
status: active
sources:
  - ../sources/2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md
  - ../sources/2026-04-16-using-claude-code-session-management-1m-context.md
updated: 2026-04-22
---

Agent Session Management 指的是在长任务过程中，主动管理“下一轮推理还应继承多少历史”的一组操作，包括继续当前会话、rewind 失败分支、compact 历史、clear 后重开，以及把噪声大的工作切给 subagent。它是 [Context Engineering](context-engineering.md) 在会话边界上的操作化切面。

## 定义

如果说 Context Engineering 关注 Agent 实际看到了什么，那么 Session Management 更具体地关注：在每个 turn 结束后，哪些历史应继续留在激活上下文里，哪些历史应被丢弃、压缩、改写或隔离。

这一定义目前主要由 Claude Code 这类长会话编码工具提供操作样本。新摄入的 Aegis 实践则补了一条更偏协议的样本：会话管理不只是在窗口快满时决定 `rewind / compact / clear`，也包括每次恢复任务时先读 `spec / handoff`、先重述总目标与阶段目标、先做 `checkpoint` 再执行。证据边界要保留：本页抽出的结构可跨工具复用，但具体命令、阈值和默认行为仍强烈依赖产品实现。

## 纵向脉络

- 最朴素的做法是持续在同一会话里追加消息，但这样会把所有尝试、失败路径和工具输出一起累积。
- `rewind` 代表一种更细粒度的回退：保留仍有价值的读取结果，删除错误尝试本身，让会话从更干净的分叉点重新开始。
- `compact` 代表在不中断任务的前提下，把完整历史换成摘要；它保留连续性，但会引入摘要有损问题。
- `clear` 代表完全重开会话，再用一个简短 handoff brief 继承必要信息；它牺牲自动连续性，换来更高的上下文控制权。
- `subagent` 则把部分工作放进独立上下文窗口，让父会话只接收结论，避免高噪声中间过程污染主线。

这些动作串起来，构成了一条从“延续历史”到“主动切断历史”的连续谱系。

## 横向位置

- 对长上下文模型来说，Session Management 不是窗口见底时才需要考虑的补救措施，而是长任务中的持续治理动作。
- 它处理的核心张力，是“复用已有上下文的效率”与“保持上下文纯净、避免智能度退化”之间的权衡。
- Claude Code 这份资料给出的经验是：`1M context` 只是延长跑道，不意味着应该默认把所有相关工作塞进同一会话；当任务切换时，通常也应切换会话。

## 新增视角：恢复动作应从外部真相源开始

- 新摄入的企业实践资料提醒，很多长任务失败不是因为窗口不够大，而是因为恢复任务时直接沿着聊天印象续写，没有先从 `spec / handoff` 这种外部真相源重建当前状态。
- 因而更稳的恢复动作不是“继续说下去”，而是先明确三件事：已经完成什么、当前阶段只收敛什么、这一轮接下来最多做哪几个动作。
- 这让 Session Management 从“保留多少历史”进一步扩展到“恢复时先认哪份历史算数”。聊天记录不再天然是真相源，`spec / handoff` 可能才是。

## 新增视角：执行前 checkpoint 与阶段验收也是会话边界治理

- 文章里的一个关键判断是，长任务往往不是一开始就跑偏，而是做着做着慢慢偏。所以会话边界不只存在于 `clear` 或 `compact` 那一刻，也存在于每轮开始执行前和准备结束时。
- 执行前要求一次 `checkpoint`，明确当前理解、下一步动作、风险和验证方式，本质上是在给本轮会话立边界。
- 收尾时要求区分“这轮最小收敛是否完成”和“全局目标是否完成”，再把结果回写到 `spec / handoff`，则是在给下一轮会话留下干净恢复点。
- 从这个角度看，Session Management 不是纯粹的上下文窗口技巧，而是与 [Harness Engineering](harness-engineering.md) 紧密耦合的协作协议。

## 与其他概念的关系

- [Context Engineering](context-engineering.md) 关注上下文的整体装配与裁剪，Session Management 则聚焦会话边界操作。
- [Harness Engineering](harness-engineering.md) 关心系统如何给 Agent 提供稳定约束；良好的 session 操作，本身就是约束长任务噪声扩散的一种 harness。
- [Spec-driven Development](spec-driven-development.md) 更关注任务契约；当任务目标变化时，Session Management 决定是沿用原契约轨迹，还是重开一个更清晰的执行上下文。

## 来源

- [从玩具到生产力：用真实项目讲透 AI Agent 的 Harness Engineering](../sources/2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md)
- [Using Claude Code: Session Management & 1M Context](../sources/2026-04-16-using-claude-code-session-management-1m-context.md)

## 开放问题

- 当前知识库还缺少跨工具对照，尚不能判断不同 Agent IDE 在 `rewind / compact / clear / subagent` 上是否会收敛到同一套稳定心智模型。
- 当任务是“高度相关但智能敏感度较低”的邻接工作时，何时该留在同一会话，何时该开新会话，仍主要依赖经验启发式。
