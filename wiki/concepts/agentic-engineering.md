---
title: Agentic Engineering
type: concept
status: active
sources:
  - ../sources/2026-04-23-从第一性原理思考-agentic-engineering.md
updated: 2026-04-24
---

Agentic Engineering 指的是把 AI Agent 作为软件工程协作者嵌入 SDLC，但仍由工程师保留目标定义、约束设定、判断和验收权的一套工程方法。它区别于 `vibe coding` 的关键，不是是否使用 AI 写代码，而是是否把上下文、规格、验证、知识治理和反馈闭环做成可持续的工程系统。

## 定义

这页把 Agentic Engineering 作为一个上位概念处理：它不是单个工具，也不是单次 prompt 技巧，而是在复杂约束下利用 AI Agent 提升软件工程质量与能力边界的方法论。

根据新摄入来源，Agentic Engineering 的目标不是 L1 的“写得更快”本身，而是 L2/L3 的“做得更好”和“做以前做不到的事”：更完整的测试、更严谨的设计评审、系统化知识沉淀、跨模块架构分析，以及新人更快达到团队水准。

## 纵向脉络

### 从 vibe coding 到工程化协作

- `vibe coding` 的价值主要在低约束原型：快速得到能跑结果。
- 生产级软件工程面对的是多维约束：质量、安全、性能、可维护性、团队规范、验证成本。
- Agentic Engineering 因此把“速度”重新放回工程约束空间里，而不是把速度作为唯一目标。

### 三条公理

新来源提出的三条公理很适合作为这个概念的地基：

1. 软件工程是一条意图转化链：模糊意图需要经过需求、设计、代码和运行行为逐步精确化，每一步都会损耗信息。
2. LLM 由上下文决定、输出概率性、工作记忆有限且易失；这让上下文供给、任务拆分和持久化知识成为结构性要求。
3. 人类认知是稀缺资源；AI 时代的瓶颈不再只是写代码速度，而是工程师能否有效审查、判断和决策。

### 六条实践

从这三条公理，来源推导出六组实践：

- [Context Engineering](context-engineering.md)：构建高信噪比的上下文供给系统。
- 基于知识不对称的人机分工：用“AI 是否知道 / 人是否知道”的乔哈里窗决定自动化、上下文注入、知识杠杆或协同探索。
- AI 全链条参与：让 AI 从需求澄清和设计阶段就参与，而不是只在编码末端提速。
- 小任务推进与多层次验证：用分步校验控制错误累积，并用 Review、测试、集成和性能/安全检查覆盖不同层次的正确性。
- Knowledge as Code：把团队共有知识治理成版本化、可 Review、可迭代的 Skills、Rules、Standards 和 Docs。
- Error-Driven Context Refinement：把被纠正的错误回写为持久规则或 Skill，避免下一次会话重复犯错。

## 横向位置

Agentic Engineering 在本知识库中更像“总方法论”，下方连接多个已存在概念：

- [Prompt Engineering](prompt-engineering.md)：最靠近模型的表达资产。
- [Context Engineering](context-engineering.md)：每一步让 Agent 看到什么。
- [Spec-driven Development](spec-driven-development.md)：先把意图、边界和验收标准显式化。
- [Harness Engineering](harness-engineering.md)：把约束、验证、守卫、状态和反馈做成模型外部控制面。
- [Agent Session Management](agent-session-management.md)：管理长任务里的会话边界、历史保留和恢复点。
- [Agent 学习闭环](agent-learning-loop.md)：把协作中出现的新经验沉淀回长期知识资产。

它也可以作为 [Agent 工作流分层框架](../analyses/agentic-workflow-stack.md) 的上位解释：工作空间、协作方法和学习闭环三层，都是为了让 Agentic Engineering 不停留在一次性对话里。

## 与其他概念的关系

- 与 [Harness Engineering](harness-engineering.md)：Harness 更偏模型外部运行控制面；Agentic Engineering 更偏整个 SDLC 中的人机协作范式。
- 与 [Context Engineering](context-engineering.md)：Context 是 Agentic Engineering 的基础设施之一，因为 AI 只能利用它能看到的知识。
- 与 [Spec-driven Development](spec-driven-development.md)：Spec 是降低早期信息损耗、把盲区转为开放区的关键机制。
- 与 [Agent 学习闭环](agent-learning-loop.md)：学习闭环解决方法论如何随项目错误和团队经验继续演进。
- 与 [Jagged Intelligence](jagged-intelligence.md)：模型能力不平滑，解释了为什么 Agentic Engineering 不能简化为“全自动放权”。

## 当前判断

Agentic Engineering 的稳定价值，在于把 AI 编程讨论从“模型能不能写出代码”转成“人机系统如何在复杂约束下可靠交付”。它强调工程师不是退出，而是从具体执行者迁移为目标定义者、上下文策划者、流程编排者和最终判断者。

但也要保留边界：新来源中的六条实践是作者的系统化推导，当前更适合作为方法地图。若要证明某个实践在特定团队、技术栈或规模下的普遍收益，仍需要更多项目级一手证据。

## 开放问题

- `Knowledge as Code` 是否会成为独立于 Agentic Engineering 的稳定概念，仍需更多来源支撑。
- 基于 Skill 的 `agentic-engineering-framework` 是否值得单独建项目实体页，取决于后续是否有仓库、用户案例或版本演进证据。
- Agentic Engineering 与 Harness Engineering 的边界在行业使用中可能继续漂移，需要后续来源校正。

## 来源

- [从第一性原理思考 Agentic Engineering](../sources/2026-04-23-从第一性原理思考-agentic-engineering.md)
