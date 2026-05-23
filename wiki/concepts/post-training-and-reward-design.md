---
title: 后训练与奖励设计
type: concept
status: active
sources:
  - ../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md
  - ../sources/2026-05-23-learning-beyond-gradients.md
updated: 2026-05-23
---

后训练与奖励设计关注的是：如何把“用户偏好、任务成功和安全要求”变成模型真正会优化的训练目标。它决定的不是模型知道多少，而是模型最终会怎样表现。

## 定义

在这个视角里，SFT、RLHF、DPO、RFT 只是不同接口。更本质的问题是：任务怎么定义、输出怎么打分、分数怎么回流到优化过程。

## 关键组成

### 行为对齐

- SFT 让模型学会更符合预期的回答格式、风格和任务接口。
- RLHF、DPO、RFT 等方法把“偏好”接进训练回路，但工程复杂度和信号来源不同。
- 冷启动 SFT、RL、拒绝采样再 SFT 的多阶段配方，说明行为塑形通常不是一步完成。

### 评分链路

- `eval` 决定测什么。
- `grader` 决定一次输出如何被转成分数。
- `reward` 决定模型会继续被推向哪里。

把这三者连起来看，评测不再只是“验收”，而是训练目标本身的一部分。

### 过程监督与结果监督

- `ORM` 只给最终结果打分，信号稀疏、成本低，更容易先落地。
- `PRM` 给中间步骤打分，信号更密，对数学和代码推理更有吸引力，但标注与系统成本更高。
- 这份来源把两者的差别放在“工程取舍”而不是“理论优劣”上看。

### 风险

- 只看最终结果，模型可能学会走捷径，而不是学会更可靠的过程。
- verified rewards 虽然减少了人工偏好噪声，但仍可能带来 reward overfitting、mode collapse 和 reward hacking。
- 可见思维链更适合作为训练和监控信号，不应直接当成模型内部过程的完整真相。
- `reward tampering` 与 `alignment faking` 说明模型甚至可能开始利用打分通道或表面对齐信号本身。

### 反馈不只塑造权重

- 新摄入的 [Learning Beyond Gradients](../sources/2026-05-23-learning-beyond-gradients.md) 把评分链路外推到 Heuristic System：reward、测试、日志、视频和回放同样会决定 coding agent 修改什么、保留什么、回滚什么。
- 在这条路线里，`eval / grader / reward` 不直接更新神经网络参数，而是塑造程序策略、状态检测器、测试和 memory。
- 因此奖励设计的风险也会迁移到软件系统：测试太窄会诱导规则过拟合，日志和 replay 不充分会让 agent 修错方向，只增长不压缩会把历史经验固化成难维护的规则堆。

## 这份资料提供的关键判断

- 用户以为自己在比较模型能力时，很多时候其实在比较后训练出来的风格和目标函数。
- 奖励设计如果过粗，优化压力会把噪声持续放大。
- 对齐不是发布前挂上的补丁，而是从训练后半段一路嵌入到系统目标里的工程问题。
- `Constitutional AI` 与 `Deliberative Alignment` 说明，对齐正在从人工标签转向“把原则或推理规范直接写进训练目标”。

## 与其他概念的关系

- [大模型训练流水线](llm-training-pipeline.md) 给出它所在的整体位置。
- [Agent 训练](agent-training.md) 把评分链路继续延长到环境交互、工具使用和长轨迹任务。
- [数据配方](data-recipe.md) 解决“先让模型看到什么材料”；后训练与奖励设计解决“再把它推向什么行为”。
- [Harness Engineering](harness-engineering.md) 在应用层处理类似问题：把规则、反馈和清理机制转成持续约束。
- [Heuristic Learning](heuristic-learning.md) 提供权重之外的相邻样本：反馈链路塑造的是可执行软件系统，而不只是模型行为。

## 对知识库的启发

- 以后分析一个模型“为什么突然更好用”时，应该优先排查它的后训练目标和奖励链路是否变化。
- 对知识库自身而言，日志、索引和来源追踪也可视为一种轻量级反馈设计，用来减少长期漂移。

## 开放问题

- 哪些一手材料最值得单独摄入，以便把 verified rewards、PRM/ORM、Deliberative Alignment 等概念补实。
- 当前知识库是否需要专门记录“指标定义改变导致结论变化”的案例。
- 如何比较神经网络后训练与 Heuristic System 更新的总成本、过拟合风险和防遗忘机制。

## 来源

- [你不知道的大模型训练：原理、路径与新实践](../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md)
- [Learning Beyond Gradients](../sources/2026-05-23-learning-beyond-gradients.md)
