---
title: Agent 训练
type: concept
status: active
sources:
  - ../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md
  - ../sources/2026-05-18-从0开发大模型的17种agent架构演进详细拆解.md
  - ../sources/2026-05-23-learning-beyond-gradients.md
updated: 2026-05-23
---

Agent 训练指的是：训练目标不再只关心模型能否给出正确答案，而是关心它能否在工具环境里持续规划、执行、接收反馈并完成长轨迹任务。

## 定义

当模型开始调用浏览器、终端、搜索、检索和记忆系统时，被优化的对象就不再只是模型权重，还包括环境质量、上下文管理和外层控制程序。

## 这份资料中的关键点

### 训练对象变了

- 单轮推理的正确率不够解释 Agent 质量。
- 真正需要优化的是任务分解、工具调用、上下文维护、总结质量和反作弊能力。
- 推理预算分配也进入训练目标，模型不仅要“会想”，还要知道什么时候该想多久。

### Harness 进入训练侧

- harness 在训练中决定 prompt construction、memory update、retrieval policy、context editing 和 tool orchestration。
- 如果环境不稳定、文件系统状态不可复现或工具返回值噪声过大，模型学到的更可能是利用环境漏洞，而不是真能力。
- 这使 harness 从部署细节变成能力形成的一层。

### 运行时架构也会定义训练问题

- 新摄入的 17 种 Agent 架构综述虽然主要讲运行时模式，但它也说明了训练侧为什么不能只奖励最终答案。
- 如果目标系统包含 ReAct、Planning、PEV、Dry-Run 或 Metacognitive 这类控制流，训练和评测就必须覆盖 observation 质量、plan state、verifier 反馈、终止条件、副作用审批和拒绝/升级人类的判断。
- 换言之，长轨迹 Agent 训练不只是让模型“会调用工具”，而是让它在某种 harness 拓扑里学会何时继续、何时回滚、何时搜索、何时拒绝。

### 奖励被拆细

- 最终任务成功只是奖励的一部分。
- 过程质量、并行分解、上下文管理和反作弊约束也要单独计分。
- Meta-Harness 进一步把 harness code 本身变成可被搜索和优化的对象。

### 权重之外也会形成能力

- 新摄入的 Heuristic Learning 资料提醒，长轨迹能力不一定只形成在模型权重里。
- coding agent 可以通过读取 reward、日志、测试失败、视频回放和实验记录，直接修改 policy code、状态检测器、配置、memory 和回归测试。
- 这意味着一部分 “Agent 训练” 可以被理解为外层软件系统的持续优化：被训练或被搜索的对象不是神经网络参数，而是 Heuristic System。
- 这条路线不能替代所有神经网络训练，尤其不擅长复杂感知和长程泛化；但在反馈明确、状态可复现、测试可回归的环境里，它把能力形成位置从模型内部扩展到可维护软件系统。

### 这份来源里的代表性路径

- `Kimi PARL` 把 credit assignment 收束到 orchestrator 层，重点优化并行拆解和关键路径缩短。
- `Cursor Composer 2` 把 summary 质量和真实生产流量重新接回训练回路。
- `Chroma Context-1` 把 context pruning 直接训练成策略。
- `Meta-Harness` 再往前一步，把 harness code 本身变成可被外循环搜索、比较和改写的对象。

## 与其他概念的关系

- [Context Engineering](context-engineering.md) 关注 Agent 每一步真正看到什么；Agent 训练说明这件事本身也能成为训练目标。
- [Harness Engineering](harness-engineering.md) 关注如何在应用层长期约束 Agent；Agent 训练说明同类问题也出现在模型供应链内部。
- [Agentic Engineering](agentic-engineering.md) 和运行时架构资料说明，工程师设计的 `state / router / evaluator` 会反过来定义训练和评测应该观察哪些行为。
- [后训练与奖励设计](post-training-and-reward-design.md) 是它的上游，负责把长轨迹任务表现转成可优化信号。
- [Heuristic Learning](heuristic-learning.md) 是权重之外的相邻路线，把 reward、测试、日志和回放接到 coding agent 对软件系统的直接改写。

## 对知识库的启发

- 当前知识库的“先读索引，再读相关页”本质上就是一种手工 context policy。
- 如果未来让 Agent 更自动地维护本库，可以把页面选择、摘要质量、链接补全和冲突回写视作类似的训练或评测目标。
- 环境质量优先于单点聪明度的判断，对本库也成立：如果索引混乱、链接失真、上下文不稳定，再强的模型也更容易维护漂移。

## 开放问题

- 是否需要为“训练侧 harness”与“应用侧 harness”建立更清晰的边界定义。
- 后续若摄入 Meta-Harness、Cursor Composer 2 或 Chroma Context-1，应该先落到本页还是单独拆分析页。
- 是否需要把运行时架构模式与训练环境设计做成一张对照表，目前还缺少更多一手训练材料。

## 来源

- [你不知道的大模型训练：原理、路径与新实践](../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md)
- [从0开发大模型的17种Agent架构演进详细拆解](../sources/2026-05-18-从0开发大模型的17种agent架构演进详细拆解.md)
- [Learning Beyond Gradients](../sources/2026-05-23-learning-beyond-gradients.md)
