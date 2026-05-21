---
标题: 总览
类型: 总览
状态: 在维护
标签:
  - 入口
  - 地图
最近更新: 2026-05-21
来源数: 26
---

# 总览

## 一句话概述

这是整个个人知识库的高层综述，用来描述这个库在研究什么、怎么组织、目前缺什么。

## 当前结构

- 来源层：`wiki/sources/`
- 实体层：`wiki/entities/`，由 [实体索引](entities/index.md) 承担目录内导航
- 概念层：`wiki/concepts/`，由 [概念索引](concepts/index.md) 承担目录内导航
- 分析层：`wiki/analyses/`，由 [分析索引](analyses/index.md) 按 `subtype` 分组
- 写作层：`writing/`

## 当前焦点

- 当前第一批内容聚焦于 AI Agent 工作流的长期结构，而不是短期工具测评。
- 新摄入的生产 Agent 评测材料，把此前散落在 Harness 与 Agent 工程里的 `eval / grader / trace / production metrics` 线索收束为 [Agent 评测](concepts/agent-evaluation.md)：离线 benchmark、在线采样、LLM-as-judge、人工校准、工具行为、多步一致性、成本和延迟都应进入同一套 evaluation harness。
- 新摄入的 Agentic Engineering 第一性原理文章，则把此前分散的 `Prompt / Context / Spec / Harness / Skills / Memory / Session` 主题重新收束到一个上位工程方法论：在意图转化链、LLM 概率性和人类认知稀缺三条约束下，用上下文供给、分步验证、知识治理和错误回写让 Agent 进入生产级软件工程。
- 新补摄入的系统工程材料，则把 Agentic Engineering 从协作流程继续推进到生产系统分层：可工作的 agentic software 需要同时处理 Agent、数据、安全、接口和基础设施五层，不能把权限、记忆、身份映射和部署弹性都混进 prompt 或单一 harness。
- 新补摄入的 LLM 推理材料，则把训练栈延伸到 serving 侧：`prefill / decode`、KV cache、量化、continuous batching 和 speculative decoding 共同决定首 token、流式延迟、显存和并发成本。
- 新摄入的 Prompt 工程资料补上了最靠近模型的一层：Prompt 不是一次性技巧，而是会继续演化为模板、评测、路由与 request spec 的工程资产。
- 新摄入资料开始把 Agent 主题进一步压到运行时实现层：一条线看系统提示 anatomy 与上下文预算，另一条线看 loop 外部的协议、状态、评测与 Trace。
- 新摄入的 Hermes `Skills` 拆解，则把“Agent 学习闭环”从抽象方法推进为更细的技能生命周期：创建触发、结构化存储、索引发现、条件激活、按需加载和执行中 patch。
- 新摄入的 OpenClaw 设计拆解，则把 `Prompt / Context / Harness` 三层压到动态 system prompt、Markdown 文件注入、上下文压缩/修剪、双层 Memory 和 Hook/HITL 这些具体结构上。
- 新摄入的 Claude Skills 设计分析，则把当前知识库已经隐含采用的“索引页 + 小页面 + 按需展开”结构，明确写成了“信息分层设计”这一稳定概念。
- 新摄入的 Claude Code 会话管理说明，又把 `Context Engineering` 从提示装配层继续推进到 turn 边界决策层，补入 `rewind / compact / clear / subagent` 这组运行时操作。
- 新摄入的 AI Search 说明，则把检索基础设施压到作用域管理、混合搜索、索引维护和跨实例查询，提醒“搜索原语”本身也是上下文工程的一部分。
- 新摄入的 Mem0 记忆算法说明，则把“Agent 记忆”从抽象层推进到具体工程层，开始围绕写入策略、状态演化保留、多信号检索和 token 预算建立稳定概念页。
- 新补的一篇 Agent Memory 综述，则把记忆主题从单一路线扩成更完整的系统框架，开始覆盖 CoALA 四类记忆、`RAG ≠ Memory`、遗忘机制、治理层与多 Agent 共享。
- 新补的两篇 Memory 资料，又把主题继续压回边界与生命周期：一条线补 `State / Policy / Profile` 边界、四类建模对象和六维记忆单元，另一条线补衰减、矛盾检测、置信度、压缩和过期机制。
- 新补的 Hermes / OpenClaw 对比，则把两类 Agent 的差异从“功能列表”改写成“默认自动化程度、记忆作用域与确定性 Harness 取舍”的比较维度。
- 新摄入的 Harness 与 ReAct 资料，则把 Harness 从“缰绳”继续推进到控制回路、传感器、错误分类、熔断器和确定性工具路由这些更细的可靠性结构。
- 新摄入的 `The Anatomy of an Agent Harness`，则把此前分散在多个页面里的运行时主题重新收束成一张更完整的 Harness 地图：主循环、工具、记忆、上下文治理、状态、守卫、验证与子 Agent，不再只是并列专题，而是同一层非模型基础设施的不同组成。
- 新摄入的生产力 Harness 实践，则把主题继续从架构边界推进到协作协议：三层目标、`checkpoint`、证据验收、`spec / handoff` 回写与动态纠偏，开始让 Harness 不只像概念图，也像一套可操作的 session 节奏。
- 新摄入资料把视角从“如何把 Agent 用好”扩展到“模型为什么会在训练与部署链路里变强”。
- 训练栈主题开始补关键一手材料，范围从后训练与 Agent 机制向更底层的 Transformer 架构决策延伸，也开始把 RNN 时代的序列学习前史补回来。
- 实体层也不再为空，当前已有 [Hermes Agent](entities/hermes-agent.md) 与 [OpenClaw](entities/openclaw.md) 两个对象页，开始形成第一组可比较的 Agent 实现样本。
- 这组对象现在已有单独的 [Hermes 与 OpenClaw：默认自动化、记忆作用域与 Harness 取舍](analyses/hermes-vs-openclaw-default-automation-comparison.md) 比较页，知识库第一次有了可复用的 `comparison` 样板。

已沉淀的稳定主题包括以下页面：

- [Agentic Engineering](concepts/agentic-engineering.md)
- [Harness Engineering](concepts/harness-engineering.md)
- [Agent 评测](concepts/agent-evaluation.md)
- [Prompt Engineering](concepts/prompt-engineering.md)
- [Context Engineering](concepts/context-engineering.md)
- [信息分层设计](concepts/information-layering-design.md)
- [Agent Session Management](concepts/agent-session-management.md)
- [Agent 记忆系统](concepts/agent-memory-systems.md)
- [Spec-driven Development](concepts/spec-driven-development.md)
- [Agent 学习闭环](concepts/agent-learning-loop.md)
- [Jagged Intelligence](concepts/jagged-intelligence.md)
- [循环神经网络（RNN）](concepts/recurrent-neural-networks.md)
- [Transformer 架构](concepts/transformer-architecture.md)
- [大模型训练流水线](concepts/llm-training-pipeline.md)
- [数据配方](concepts/data-recipe.md)
- [系统约束与训练配方](concepts/system-and-training-constraints.md)
- [后训练与奖励设计](concepts/post-training-and-reward-design.md)
- [Agent 训练](concepts/agent-training.md)

分析页目前包括以下页面：

- [Agent 工作流分层框架](analyses/agentic-workflow-stack.md)
- [大模型训练栈三层框架](analyses/llm-training-stack.md)
- [Hermes 与 OpenClaw：默认自动化、记忆作用域与 Harness 取舍](analyses/hermes-vs-openclaw-default-automation-comparison.md)

其中前两页归入 `subtype: framework`，最新一页归入 `subtype: comparison`。这也让针对具体对象的深研第一次不再停留在“应当拆 comparison”，而是真正补出可复用样板。

写作层已建立入口，后续可把成熟主题转化为 `writing/` 中的专题文章。

## 当前空白

- `Knowledge as Code`、`Error-Driven Context Refinement`、agentic software 五层系统工程与生产 evaluation harness 已作为 Agentic Engineering 的关键实践出现，但当前仍缺更多项目级一手案例支撑，是否独立成页需要继续观察。
- 实体层刚出现第一组对象页，但工具、组织、作者和产品的对象谱系仍明显稀薄。
- `Hermes Agent / OpenClaw` 这组对象已经有了正式 comparison 页，但比较仍主要建立在二次解读上，缺少官方仓库与文档级一手证据。
- 外部引用材料开始单独摄入，Transformer 与 RNN 时代各已有一篇关键架构材料，但训练与对齐相关一手资料仍明显不足。
- 训练栈已拆出“数据配方”和“系统约束”，但 `蒸馏与专用化` 仍未独立成页。
- `Jagged Intelligence` 已作为概念立页，但其原始出处和不同模型代际的比较材料仍未单独摄入。
- `Agent Session Management` 已开始有第二条来自真实项目推进的样本，但仍缺 Cursor、Codex CLI、OpenHands 等工具的横向对照。
- `Agent 记忆系统` 已覆盖 CoALA、治理边界、生命周期字段和 Mem0 路线，但 ChatGPT Memory、LangMem、NotebookLM 或更多开源框架的一手对照材料仍明显不足。
- `信息分层设计` 已立页，但目前仍主要由 Claude Skills 这一路材料支撑，缺少代码库导航、数据库摘要、API 设计等更多一手案例。
- 推理系统已开始进入训练栈视野，但 `prefill / decode / KV cache / batching / quantization` 尚未单独沉淀为概念页，也缺少 vLLM、PagedAttention、TensorRT-LLM 等一手材料。
- agentic software 的系统工程视角已补入 Agentic Engineering / Harness / Context / Memory，但尚未整理成稳定 synthesis 页；Dash 是否值得成为实体页也仍需更多材料。
- “搜索原语 / 检索基础设施” 已开始在 `Context Engineering` 下出现稳定问题意识，但尚未单独沉淀为概念页。
- 2026-05-21 已完成一轮全库巡检与补摄入，并继续摄入生产 Agent 评测资料：`raw/sources`、`wiki/sources` 与来源注册表均为 26 条，2 份 `raw/assets` 均由 raw 指针笔记接回来源页；但还没有把本轮使用的结构检查固化为仓库内自动化脚本。
- 还没有已发布的专题文章，写作层目前只有目录页。

## 建议优先级

1. 继续把训练栈综述拆回一手材料，优先补齐 InstructGPT、DeepSeek-R1、DeepSeek-V3、Constitutional AI、Deliberative Alignment 与 Meta-Harness。
2. 围绕当前主题继续补实体页，在 `Hermes Agent / OpenClaw` 这组样板之外，再补关键研究组织、产品或作者，并按“纵向脉络 / 横向位置 / 当前判断”三段结构收敛。
3. 沿当前 Hermes / OpenClaw 比较页继续扩到更多对象，验证“默认自动化程度 / 记忆作用域 / 确定性 Harness”这组维度是否足以支撑更大的对象谱系。
4. 从现有分析页里选一个主题，先补研究底稿，再产出第一篇 `writing/` 文章。
5. 在问答、纠错和新资料摄入过程中持续回写，而不是把 wiki 当成一次性摘要库。

## 相关页面

- [总索引](index.md)
- [更新日志](log.md)
- [专题文章目录](../writing/index.md)
- [全局说明](../README.md)

## 来源

- [从第一性原理思考 Agentic Engineering](sources/2026-04-23-从第一性原理思考-agentic-engineering.md)
- [Building an Evaluation Harness for Production AI Agents: A 12-Metric Framework From 100+ Deployments](sources/2026-05-13-building-an-evaluation-harness-for-production-ai-agents-a-12-metric-framework-from-100-deployments.md)
- [How LLM Inference Works](sources/2026-05-03-how-llm-inference-works.md)
- [Systems Engineering: Building Agentic Software That Works系统工程：构建能工作的代理软件](sources/2026-04-08-systems-engineering-building-agentic-software-that-works系统工程-构建能工作的代理软件.md)
- [A Critical Review of Recurrent Neural Networks for Sequence Learning](sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md)
- [Attention Is All You Need](sources/2017-06-12-attention-is-all-you-need.md)
- [从玩具到生产力：用真实项目讲透 AI Agent 的 Harness Engineering](sources/2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md)
- [Prompt 工程实战指南：从 Prompt 硬编码到可控系统](sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md)
- [Using Claude Code: Session Management & 1M Context](sources/2026-04-16-using-claude-code-session-management-1m-context.md)
- [AI Search：为你的智能体打造的搜索原语](sources/2026-04-16-ai-search-为你的智能体打造的搜索原语.md)
- [How We Built the Token-Efficient Memory Algorithm](sources/2026-04-17-how-we-built-the-token-efficient-memory-algorithm.md)
- [浅谈 Agent Memory](sources/2026-04-12-浅谈-agent-memory.md)
- [Agent Memory 架构本质](sources/2026-04-15-agent-memory-架构本质.md)
- [停止将 AI 记忆视为搜索问题](sources/2026-04-12-停止将-ai-记忆视为搜索问题.md)
- [从《塞尔达传说》到AI Agent：Claude Skills背后的信息分层设计哲学](sources/2026-04-16-从塞尔达传说到ai-agent-claude-skills背后的信息分层设计哲学.md)
- [抽丝剥茧：深度解析 Hermes Agent 万字系统提示词（System Prompt）构成](sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md)
- [一文搞懂Hermes：新顶流Agent如何从经验中自我进化](sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md)
- [深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践](sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md)
- [Hermes 凭什么两个月接棒 OpenClaw？](sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md)
- [一文讲透：Harness Engineering 即控制论！](sources/2026-04-17-一文讲透-harness-engineering-即控制论.md)
- [面向“Token 亿万富翁”的极限 Harness Engineering：100 万行代码、每天 10 亿 To...](sources/2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md)
- [The Anatomy of an Agent Harness](sources/2026-04-06-the-anatomy-of-an-agent-harness.md)
- [你的 ReAct 智能体正在浪费 90% 的重试机会——以下是解决方法](sources/2026-04-12-你的-react-智能体正在浪费-90-的重试机会-以下是解决方法.md)
- [拥抱 AI 这一年：我的工具、实践和思考](sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md)
- [你不知道的 Agent：原理、架构与工程实践](sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md)
- [你不知道的大模型训练：原理、路径与新实践](sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md)
