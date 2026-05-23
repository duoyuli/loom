---
title: Learning Beyond Gradients
type: source
status: summarized
sources:
  - ../../raw/sources/Learning%20Beyond%20Gradients.md
source_date: 2026-05-23
source_date_basis: created
created: 2026-05-23
updated: 2026-05-23
---

这是一篇由 Jiayi Weng 撰写的 blog post，提出 Heuristic Learning / Heuristic System 这组概念。它的长期价值不在于宣称手写规则全面替代神经网络，而在于说明 coding agent 让一类过去维护成本过高的 heuristic 系统重新变得值得长期拥有。

## 这份资料是什么

- 资料来自作者个人博客，并附带 `learning-beyond-gradients` artifact 仓库。
- 文章以 EnvPool、Atari、MuJoCo 和 VizDoom 实验为样本，观察 coding agent 不更新神经网络权重，而是通过改代码、加测试、看日志、看回放和写实验记录，让显式程序策略持续变强。
- 原始摘录没有给出精确发布日期，BibTeX 只写到 `2026-05`；本页暂用 raw 文件创建日作为 `source_date`。

## 主要观点

1. Continual Learning 不一定只能通过神经网络权重更新来讨论；也可以看一个可持续迭代的软件系统如何吸收反馈、防止遗忘并压缩历史。
2. Heuristic Learning 的更新对象不是模型参数，而是程序策略、状态检测器、测试、配置、memory、实验记录和回放机制。
3. coding agent 改变的是 heuristic 的维护成本曲线：过去很快腐化的规则系统，如果有测试、日志、回放和持续重构，就可能成为长期可拥有的 Heuristic System。
4. Heuristic System 的核心不是一条 rule，而是 `policy / state representation / feedback / trials / replay / memory / update mechanism` 组成的闭环。
5. HL 可以把旧能力固化为回归测试、固定 seed replay、golden trace、失败视频和版本 diff，从而把灾难性遗忘问题部分转成工程治理问题。
6. HL 仍有边界：复杂感知、长程泛化和高维表示仍可能需要神经网络；更现实的方向是让 HL 处理在线经验，再把可训练、可回归、可筛选的数据周期性回灌给神经网络。

## 关键内容

### Heuristic Learning / Heuristic System

文章把 Heuristic Learning 定义为一种由 coding agent 驱动的软件系统更新过程：

- 主体由程序代码构成，可以是规则、状态机、controller、MPC 或宏动作。
- 反馈可以来自 reward、testcase、日志、视频、回放和人类反馈。
- 更新不通过反向传播，而是直接修改代码、测试、配置、状态读取或 memory。
- 长期被维护的对象是 Heuristic System，而不是孤立的 `policy.py`。

这组定义把“手写规则”从一次性补丁重新放进了学习系统语境：只有当规则、反馈、历史和下一轮更新全部接起来，才构成 HL。

### 实验样本

- Breakout：从接球策略开始，通过动作探测、状态读取、落点预测、卡住循环检测、回归测试和视频回放，逐步到达 `864` 满分。
- Ant / HalfCheetah：用 CPG / PD、姿态反馈、接触信息和短视窗 residual MPC，进入常见 Deep RL 结果量级。
- VizDoom：用 cv2 / NumPy 屏幕 CV 和公开 game variables 构造闭环控制，不训练神经网络。
- Atari57：批量跑 `57 x 2 x 3` 条 coding-agent 搜索轨迹，说明这套方法不只是单点漂亮案例，但不同游戏差异明显。
- Montezuma：暴露表达力边界，普通反应式 `if else` 不足以承接可恢复搜索状态、宏动作和长期 memory。

这些实验更适合作为“现象证据”和 artifact 入口，不应直接外推为 HL 已全面优于 Deep RL。

### 防遗忘和压缩历史

文章最值得回写知识库的一点，是把 Continual Learning 改写成软件系统维护问题：

- 新规则可能修复新失败，也可能破坏旧能力。
- 新 memory 可能把 agent 带到错误方向。
- 新测试太窄会诱导策略钻空子。
- 只增长不压缩的 HS 会让耦合复杂度持续上升，直到超过 agent 能维护的范围。

因此健康的 HS 至少需要两类动作：吸收反馈，以及把局部补丁压缩回更简单、更可维护的表示。

### 耦合复杂度

文章提出一个有用判断：HS 的难度不能按代码行数衡量，而要看 coding agent 一次更新必须同时照顾多少相互牵连的状态、规则、测试、反馈和历史。

降低耦合复杂度的工程因素包括模块边界、接口稳定性、测试覆盖、日志观测性、回滚成本和状态可复现性；提高 agent 有效维护能力的因素则包括模型能力、上下文长度、memory 质量、工具质量和迭代速度。

## 纵向线索

- 对 [Agent 学习闭环](../concepts/agent-learning-loop.md) 来说，这篇资料把“学习”从知识采集与 Skill 回写推进到可执行软件系统本身的持续改写。
- 对 [Agent 训练](../concepts/agent-training.md) 来说，它提供了一条非梯度路线：长轨迹任务的能力形成不只发生在模型权重里，也可能发生在 harness、policy code、测试和实验系统里。
- 对 [后训练与奖励设计](../concepts/post-training-and-reward-design.md) 来说，它把 reward / eval / grader 的思路外推到 coding agent 维护的程序系统，并强调反馈通道自身会影响系统学到什么。

## 横向线索

- 与 [Harness Engineering](../concepts/harness-engineering.md)：HL 里的测试、日志、replay、golden trace、失败视频和回滚机制，本质上是一套可持续运行的 harness。
- 与 [Agentic Engineering](../concepts/agentic-engineering.md)：HL 把 AI 编程从“更快写代码”推进到“哪些代码值得长期拥有”，强调人机系统如何维护可解释、可验证、可压缩的软件能力。
- 与 [Agent 评测](../concepts/agent-evaluation.md)：HL 的每轮更新都依赖可复现 trial、summary、video、seed 和 score；这说明评测不是事后验收，而是系统学习的传感器。

## 与知识库的相关性

这篇资料适合新建 [Heuristic Learning](../concepts/heuristic-learning.md) 概念页，因为它给出了明确术语、对象边界和实验线索。它也应回写到现有 Agent 学习闭环、Agent 训练、后训练/奖励、Harness 和工作流分层框架，说明“学习对象”可以从模型权重扩展到可维护软件系统。

## 可靠性判断

- 这是作者个人观点与实验报告，实验 artifact 较完整，适合作为新概念和现象线索。
- 文中许多实验由 coding agent 生成和总结，应优先把它当作可复现实验入口，而不是严格 peer-reviewed 结论。
- Atari57 与 Deep RL baseline 的比较口径需要谨慎：文章比较的是环境交互步数，不折算 coding agent 读日志、写代码、看视频和运行工具的总计算成本。
- 作者自己也保留了边界：HL 不能解决所有神经网络擅长的问题，复杂感知和长程泛化仍可能需要 NN 与 HL 结合。

## 受影响页面

- [Heuristic Learning](../concepts/heuristic-learning.md)
- [Agent 学习闭环](../concepts/agent-learning-loop.md)
- [Agent 训练](../concepts/agent-training.md)
- [后训练与奖励设计](../concepts/post-training-and-reward-design.md)
- [Harness Engineering](../concepts/harness-engineering.md)
- [Agent 工作流分层框架](../analyses/agentic-workflow-stack.md)

## 开放问题

- HL 是否会成为稳定术语，需要看后续是否有更多一手实验、论文或工程系统采用这组命名。
- 如何定义 HL 的总成本：环境步数、模型调用、人工提示、工具运行、视频检查和重构时间应如何一起计入。
- HL 生成的在线经验如何转成可训练数据，并避免周期性更新神经网络时发生分布污染或 reward hacking。
- Heuristic System 的压缩、重构和防腐化是否需要单独的工程模式库。

## 来源

- [原始摘录](../../raw/sources/Learning%20Beyond%20Gradients.md)
- [原文链接](https://trinkle23897.github.io/learning-beyond-gradients/)
- [artifact 仓库](https://github.com/Trinkle23897/learning-beyond-gradients)
