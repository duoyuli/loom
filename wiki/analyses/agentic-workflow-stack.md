---
title: Agent 工作流分层框架
type: analysis
subtype: framework
status: evergreen
sources:
  - ../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md
  - ../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md
  - ../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md
  - ../sources/2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md
  - ../sources/2026-04-23-从第一性原理思考-agentic-engineering.md
  - ../sources/2026-04-08-systems-engineering-building-agentic-software-that-works系统工程-构建能工作的代理软件.md
  - ../sources/2026-05-21-当我们谈论-fde-时-我们在谈论什么.md
  - ../sources/2026-05-18-从0开发大模型的17种agent架构演进详细拆解.md
  - ../sources/2026-05-23-learning-beyond-gradients.md
updated: 2026-05-23
---

这份分析把来源文章压缩成一个更适合长期复用的框架：Agent 系统的长期价值主要来自工作空间、协作方法和知识闭环三层的联动，而不是某个单点工具或某次 prompt 技巧。

## 三层框架

### 1. 工作空间层

目标是让多任务、多 Agent、多机器协作保持低摩擦和可观察。文中的窗口管理、终端工具、状态仪表盘都属于这一层。对知识库更值得保留的不是具体工具名，而是三类稳定诉求：快速切换、跨环境执行、统一状态观察。

### 2. 协作方法层

这一层回答“如何让 Agent 长期稳定工作”：

- 用 [Prompt Engineering](../concepts/prompt-engineering.md) 把最靠近模型的指令层从代码字符串升级成可维护资产。
- 用 [Context Engineering](../concepts/context-engineering.md) 控制它看到什么。
- 用 [Spec-driven Development](../concepts/spec-driven-development.md) 说明它要交付什么。
- 用 [Harness Engineering](../concepts/harness-engineering.md) 限制它不能怎样跑偏。
- 新摄入的 Agent 架构综述把这一层继续细化为控制流设计：每种架构都应能说明新增的 state、router、evaluator、终止条件或副作用闸门。
- 新摄入的 “Token 亿万富翁” 资料则提醒，这一层还必须显式包含 `agent-legible` 代码库、`< 1 分钟` 构建循环、可观测性和 PR 生命周期自动化；否则即使 `Prompt / Context / Spec / Harness` 齐了，系统也会在人类等待和慢内循环处失速。
- 用任务状态、协议、评测与 Trace 把多轮运行时状态外化，避免把复杂性硬塞回主循环。

### 3. 学习闭环层

这一层回答“如何让 Agent 越用越强”：

- 采集外部变化
- 提炼成可复用模式
- 应用回工作空间和任务执行

对应概念见 [Agent 学习闭环](../concepts/agent-learning-loop.md)。新摄入的 [Heuristic Learning](../concepts/heuristic-learning.md) 把这一层进一步落到可执行软件系统：学习结果不只进入文档、Skill 或 memory，也可以进入 policy code、状态检测器、测试、日志、回放和实验记录。

## 这份框架最值得保留的判断

- 易过时的是具体工具和具体模型。
- 相对稳定的是如何构造上下文、契约、约束和反馈回路。
- 新摄入的 Prompt 工程资料说明，这条协作方法链往往不是从 Context 开始，而是先把 Prompt 从“硬编码字符串”升级为模板、评测和 spec 资产，再继续外扩。
- 当系统从单 Agent 走向长任务和多 Agent 时，协议、外部状态和可观测性会成为协作方法层的硬组成部分。
- 当自动化进一步走向多 Agent 编排时，`spec` 也会从“需求契约”升级成“交接对象”，而仓库结构、构建预算和可观测性则会升级成真实的协作前提。
- 如果知识库要为 Agent 服务，就应更多记录“稳定结构”而不是“短期技巧”。
- 原文后半段补充了一个协作层判断：人在面对模型的锯齿状能力分布时，仍需通过持续使用建立边界直觉，见 [Jagged Intelligence](../concepts/jagged-intelligence.md)。
- Heuristic Learning 补充了一个新的稳定结构：当任务反馈清楚且可复现时，Agent 工作流可以把失败样本、回放和测试直接接成程序策略的更新循环，而不只是在对话里产出一次性代码。

## 证据边界

- 这份三层框架主要压缩的是来源中的稳定结构，不覆盖原文所有价值判断。
- 关于 AI Native、早期采用门槛和具体模型体验的讨论，更适合作为时代背景与开放问题，而不是此框架的核心支柱。

## 对本知识库的意义

- 当前知识库已经具备“原始资料不可变 + wiki 持续回写”的基本形态。
- 未来如果继续摄入 AI 相关资料，可以优先判断它落在哪一层，以及是否真的提供了新的稳定结构。
- 新摄入的 Agent 工程综述说明，这个三层框架还可以继续细化：协作方法层内部至少还包括 `loop 简化 / 状态外化 / 工具接口 / 记忆分层 / 多 Agent 协议 / 评测追踪` 六个子主题。


## 新增判断：Agentic Engineering 是三层框架的上位方法论

新摄入的 [从第一性原理思考 Agentic Engineering](../sources/2026-04-23-从第一性原理思考-agentic-engineering.md) 把本页的三层框架重新放进一个更大的工程目标中：在复杂约束下，让 AI Agent 可靠地提升质量并拓展工程师能力边界，而不是只把编码速度拉高。

它对本框架的补强主要有三点：

1. **工作空间层**不只是效率工具，而是为了缓解人类认知稀缺，让工程师把注意力从样板执行转移到目标、约束和判断。
2. **协作方法层**可以用三条公理统一解释：意图转化链要求 Spec 和 Review；LLM 的上下文与概率性要求 Context、Harness 和小任务校验；工作记忆易失要求 Docs as Code 和会话恢复点。
3. **学习闭环层**不只是采集外部知识，也包括从项目错误中反向生长规则与 Skill，即 `Knowledge as Code + Error-Driven Context Refinement`。

因此，后续摄入 Agent 工程材料时，可以先判断它是在补“意图损耗治理”“上下文供给”“验证控制”还是“知识回写”中的哪一环，而不是只按工具名分类。

## 新增判断：三层框架还需要生产系统底座

新摄入的 [Systems Engineering: Building Agentic Software That Works系统工程：构建能工作的代理软件](../sources/2026-04-08-systems-engineering-building-agentic-software-that-works系统工程-构建能工作的代理软件.md) 对本页形成一次校正：`工作空间 / 协作方法 / 学习闭环` 更像 Agent 使用和迭代的上层框架，但生产级 agentic software 还必须有更底层的系统工程支撑。

这条底座至少包括：

- Data Engineering：把上下文、记忆、业务知识和错误学习做成可查询、可更新、可治理的数据层。
- Security Engineering：把权限、隔离、审批和审计做成系统约束，而不是 prompt 建议。
- Interface Engineering：让 REST、Slack、MCP、CLI 等入口共享一致身份和策略语义。
- Infrastructure Engineering：处理长请求、流式响应、后台任务和部署弹性。

因此本框架后续不应只追踪“Agent 如何协作”，还要追踪“支撑 Agent 协作的服务系统是否完整”。

## 新增判断：工作流发现可能需要前线组织角色

新摄入的 [当我们谈论 FDE 时，我们在谈论什么？](../sources/2026-05-21-当我们谈论-fde-时-我们在谈论什么.md) 给本页补了一个组织层校正：工作流层的价值不一定能由产品团队在总部预先定义，尤其是 AI agent 这种新类别，很多 golden case 只能在客户真实环境中被发现。

因此，工作流分层框架还需要容纳 [Forward Deployed Engineer](../concepts/forward-deployed-engineer.md) 这类角色：

- 工作空间层提供前线可用的平台杠杆。
- 协作方法层在现场验证真实流程、权限和集成约束。
- 学习闭环层把现场碎石路回流成平台铺好道路。

这条线提醒：如果现场发现不能进入平台能力，工作流协作会退化成项目交付；如果可以回流，前线部署就可能成为产品学习系统。

## 新增判断：协作方法层要能落到控制流对象

新摄入的 [从0开发大模型的17种Agent架构演进详细拆解](../sources/2026-05-18-从0开发大模型的17种agent架构演进详细拆解.md) 对本页形成一次结构复盘：`Prompt / Context / Spec / Harness` 仍然是协作方法层的高层入口，但真正实现时必须落成更小的控制流对象。

可复用的拆法是：

- `state`：计划、草稿、批注、工具结果、共享黑板、记忆、模拟状态。
- `router`：固定边、条件边、重规划、入口分诊、人工审批。
- `evaluator`：critic、verifier、程序化约束、LLM-as-judge、人类闸门。
- `loop / termination`：反思、ReAct、PEV、自我改进和任务终止条件。

这让分层框架更容易用于复盘具体系统：如果一个 Agent 工作流失控，不应只问 prompt 是否写清楚，而要检查这些控制流对象缺了哪一个、哪个对象还停留在模型口头承诺里。

## 新增判断：学习闭环可以直接维护程序系统

新摄入的 [Learning Beyond Gradients](../sources/2026-05-23-learning-beyond-gradients.md) 对本页形成另一条补强：工作流分层里的“学习闭环层”不只负责知识沉淀，也可以直接维护可执行 Heuristic System。

在这类系统里，工作空间层提供快速实验、回放和记录；协作方法层提供测试、harness、状态读取和重构约束；学习闭环层则把 reward、失败视频、trial summary 和版本 diff 回写为下一轮 policy code。它让“Agent 越用越强”从抽象愿景变成一种可检查的工程循环。

这条线也提醒分层框架要保留成本边界：如果只统计环境交互步数，而不统计 coding agent 的模型调用、日志阅读、工具运行和重构时间，就容易高估 Heuristic Learning 的样本效率。

## 来源

- [Prompt 工程实战指南：从 Prompt 硬编码到可控系统](../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md)
- [拥抱 AI 这一年：我的工具、实践和思考](../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md)
- [面向“Token 亿万富翁”的极限 Harness Engineering：100 万行代码、每天 10 亿 To...](../sources/2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md)
- [你不知道的 Agent：原理、架构与工程实践](../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md)
- [从第一性原理思考 Agentic Engineering](../sources/2026-04-23-从第一性原理思考-agentic-engineering.md)
- [Systems Engineering: Building Agentic Software That Works系统工程：构建能工作的代理软件](../sources/2026-04-08-systems-engineering-building-agentic-software-that-works系统工程-构建能工作的代理软件.md)
- [当我们谈论 FDE 时，我们在谈论什么？](../sources/2026-05-21-当我们谈论-fde-时-我们在谈论什么.md)
- [从0开发大模型的17种Agent架构演进详细拆解](../sources/2026-05-18-从0开发大模型的17种agent架构演进详细拆解.md)
- [Learning Beyond Gradients](../sources/2026-05-23-learning-beyond-gradients.md)
