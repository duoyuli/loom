---
title: Heuristic Learning
type: concept
status: active
sources:
  - ../sources/2026-05-23-learning-beyond-gradients.md
updated: 2026-05-23
---

Heuristic Learning 指的是：由 coding agent 持续维护一套显式软件系统，让程序策略、状态检测器、测试、日志、回放、memory 和实验记录在反馈中不断更新，而不是只通过梯度下降修改神经网络权重。

## 定义

这页把 Heuristic Learning 作为一种“软件系统学习”概念处理。它不是传统意义上的一次性手写 rule，也不是把 Deep RL 换个名字，而是把可解释代码、外部反馈和持续维护机制接成闭环。

被长期维护的对象可称为 Heuristic System。它至少包含：

- 程序策略：规则、状态机、controller、MPC、宏动作或其他显式 policy。
- 状态表示：检测器、缓存、环境变量、屏幕 CV、RAM 读取或可复现实验状态。
- 反馈入口：reward、testcase、日志、视频、回放、人工反馈。
- 历史与记忆：trial 记录、summary、失败原因、版本 diff、memory。
- 更新机制：coding agent 读取上下文后修改代码、测试、配置或 memory。

单条规则不构成 HS。只有当规则能被反馈、验证、记录、回归和下一轮修改继续照料时，它才进入 Heuristic Learning。

## 纵向脉络

### 从专家系统到可维护 heuristic

传统专家系统和规则系统的问题常常不是规则完全无效，而是维护成本太高：新规则修一个 case，又破坏另一个 case，规则堆积后没人敢删。

coding agent 改变的是这条维护成本曲线。它可以持续读失败日志、看回放、补测试、改策略、写实验记录和重构规则，于是原来不值得长期拥有的 heuristic，可能开始成为可持续资产。

### 从梯度学习到软件系统学习

Deep RL 的更新对象是神经网络参数；HL 的更新对象是软件结构。二者共享状态、动作、反馈、更新这类闭环语言，但回写位置不同：

- Deep RL 把经验压进权重。
- HL 把经验写进代码、测试、日志、replay、memory 和实验协议。

这让 HL 的历史更显式、可读、可删、可回滚，也更容易被工程测试保护；但它也会面临代码腐化、规则过拟合和耦合复杂度上升。

### Continual Learning 的工程化转写

HL 不会自动解决灾难性遗忘。它只是把一部分防遗忘问题转成工程动作：

- 把旧能力固化为回归测试、固定 seed replay、golden trace 和失败视频。
- 把失败方向、版本 diff 和实验 summary 留给下一轮 agent。
- 用重构和压缩把局部补丁折回更简单的表示。

健康的 HS 需要同时吸收反馈和压缩历史；只增长不压缩的系统会变成难以维护的规则堆。

## 横向位置

### 与 Deep RL

HL 和 Deep RL 都在处理状态、动作、反馈和策略更新。差别在于：

- Deep RL 适合高维感知、连续泛化和神经表示学习。
- HL 适合边界清晰、反馈可复现、规则可解释、测试可回归的环境。
- 更现实的路线可能是组合：HL 快速处理在线经验，形成可筛选、可回归的数据，再周期性更新神经网络。

### 与 Harness Engineering

[Harness Engineering](harness-engineering.md) 负责把模型外的约束、验证、状态和反馈做成控制面。HL 可以看成 harness 进一步成为学习对象：测试、日志、replay、golden trace 和 rollback 不只是保护策略，而是策略继续生长的传感器和边界。

### 与 Agent 学习闭环

[Agent 学习闭环](agent-learning-loop.md) 原本更关注知识、Skill 和 memory 如何进入未来工作流。HL 把闭环推进到可执行系统本身：学习结果不只是文档或规则，而是可以直接运行的 policy code、检测器、实验脚本和回归协议。

### 与 Agent 训练

[Agent 训练](agent-training.md) 说明长轨迹能力不只由单轮回答训练决定，还受工具环境、上下文和 harness program 影响。HL 进一步提醒：当 coding agent 能持续改写外层软件系统时，能力形成可能发生在模型权重之外。

### 与后训练与奖励设计

[后训练与奖励设计](post-training-and-reward-design.md) 关注 eval、grader、reward 如何塑造模型行为。HL 中也有类似链路，只是被塑形对象变成了程序系统；reward、日志、视频和测试会决定 coding agent 修改什么、保留什么、回滚什么。

## 当前判断

Heuristic Learning 的稳定价值不是证明“规则优于神经网络”，而是指出一个新的能力形成位置：coding agent 让可解释软件系统也能进入高频反馈、实验记录、回归保护和持续重构的学习循环。

这对 Agentic Engineering 很重要，因为它把“AI 写代码”从生产力问题推进到系统演化问题。未来的 agentic coding 可能不只是交付一次 patch，而是长期维护一套可验证、可压缩、可回放的软件能力。

也要保留边界：HL 的实验结果多来自结构清晰、反馈明确或可模拟环境。复杂感知、开放世界泛化和需要深层表示学习的任务，仍可能主要依赖神经网络，或需要 NN 与 HL 混合。

## 开放问题

- HL 是否会成为行业稳定术语，还是只是一篇文章中的工作命名。
- 如何严格计算 HL 的总样本和计算成本，包括 coding agent 的模型调用、日志阅读、代码生成、视频检查和工具运行。
- HS 的“压缩历史”有没有通用工程模式，例如周期性重构、规则蒸馏、failure taxonomy 或 trace pruning。
- HL 生成的数据如何回灌神经网络，才能避免过拟合到特定 heuristic 或污染后训练分布。
- Montezuma 这类长程规划任务是否需要把 HS 从普通 `policy.py` 扩展成宏动作、状态图、可恢复搜索和长期记忆结构。

## 来源

- [Learning Beyond Gradients](../sources/2026-05-23-learning-beyond-gradients.md)
