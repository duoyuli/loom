---
title: Context Engineering
type: concept
status: active
sources:
  - ../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md
  - ../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md
  - ../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md
  - ../sources/2026-04-16-ai-search-为你的智能体打造的搜索原语.md
  - ../sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md
  - ../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md
  - ../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md
  - ../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md
  - ../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md
  - ../sources/2026-04-17-how-we-built-the-token-efficient-memory-algorithm.md
  - ../sources/2026-04-12-停止将-ai-记忆视为搜索问题.md
  - ../sources/2026-04-12-你的-react-智能体正在浪费-90-的重试机会-以下是解决方法.md
  - ../sources/2026-04-16-using-claude-code-session-management-1m-context.md
  - ../sources/2026-04-16-从塞尔达传说到ai-agent-claude-skills背后的信息分层设计哲学.md
  - ../sources/2026-04-06-the-anatomy-of-an-agent-harness.md
  - ../sources/2026-04-23-从第一性原理思考-agentic-engineering.md
updated: 2026-04-24
---

Context Engineering 关注的不是“提示词写得好不好”，而是 Agent 在每一步推理时实际看到了哪些信息，以及这些信息是否精准、足够且不过载。

## 定义

按照这份资料的表述，Agent 真正接触的是一个完整的信息环境，而不是单独一段 prompt。这个环境包括系统指令、工具定义、对话历史、外部数据、记忆和运行中的工具输出。原文还强调了一个操作性约束：上下文窗口有限，信息又会在长任务中持续膨胀，因此上下文管理必须包含动态裁剪、更新和去冗余。

新摄入的 Hermes 拆解进一步给了一个实现侧样本：一次真实 Agent 会话里的系统提示，往往会同时装入身份设定、记忆规则、冻结记忆快照、用户画像、技能索引、项目级 `AGENTS.md`、平台提示和会话元数据。换言之，Context Engineering 还包含“哪些上下文层被装入、以什么顺序装入、哪些层值得被裁剪”这些运行时装配问题。

另一篇 Agent 架构综述则把这层工程再往外扩一圈：除了系统提示本身，`Prompt Caching`、`Skills` 描述符长度、按需加载规则、压缩保留优先级和文件系统式动态上下文接口，也都属于 Context Engineering 的稳定组成部分。

新摄入的 Hermes `Skills` 拆解则把这件事推进到更细的运行时权衡：为了保护 `Prompt Caching`，完整 Skill 不直接改写 system prompt，而是先以索引常驻，再在命中时通过用户消息注入；与此同时，当前有哪些 Skill 对会话可见，还受工具集、平台和 fallback 条件控制。换言之，Context Engineering 不只是在决定“加载什么文本”，也在决定“哪些资源进入候选空间”和“哪些缓存边界不能被打破”。

Claude Code 的会话管理说明则把这个概念往操作层推进了一步：上下文治理不只发生在“会话开始前装什么”，也发生在每个 turn 结束后的边界决策里。继续、rewind、compact、clear 和 subagent，并不是零散命令，而是决定“下一轮模型该继承多少历史”的上下文编辑操作。

新摄入的 Prompt 工程指南则补上了一个容易被跳过的入口层：很多系统最开始看到的问题，其实并不是“上下文装配”，而是“Prompt 如何从代码字符串变成可编辑模板”。它提醒我们，Context Engineering 往往不是替代 Prompt Engineering，而是在 Prompt 资产化之后，继续处理多场景、多资源和长任务里的信息装载问题。

OpenClaw 的拆解又补上了另一种实现样本：上下文装载不只发生在对话历史层，还发生在 `PromptMode`、Workspace 文件注入、Skill 可见性、`MEMORY.md` 固定装载和记忆工具按需召回这些并行层级中。相比之下，Hermes / OpenClaw 对比资料则说明，不同系统即使功能相似，也会在“压缩是否交给 LLM”“记忆是否会话内刷新”“哪些规则应写成死阈值”上做出不同的上下文工程取舍。

## 这份资料中的定位

- 它被视为从 `Prompt Engineering` 走向更复杂 Agent 协作的第一步。
- 核心问题是上下文窗口有限，而任务运行中的信息会不断膨胀。
- 目标不是“塞更多”，而是让 Agent 在关键时刻看到最相关、最少冗余的信息。
- 原文把它放进 `Prompt -> Context -> Spec -> Harness` 的递进链里，但也明确这不是严格时间线，而是逐层补洞的问题框架。

## 对知识库的启发

- 知识库应优先维护许多小页和清晰索引，而不是单个大文档。
- 来源页、概念页、分析页的分层，本质上是在帮助 Agent 读取更合适的上下文。
- `wiki/index.md` 先读、再顺着链接扩展，是一种明确的上下文裁剪策略。


## 新增视角：Context 是 Agentic Engineering 的第一杠杆

- 新摄入的 Agentic Engineering 资料从 LLM 的上下文决定性、概率性和有限工作记忆出发，给 Context Engineering 提供了一条更上位的推导：团队通常不能即时改变模型能力，但可以改变进入上下文的知识质量、相关性和结构化程度。
- 这份资料把上下文供给拆成三个动作：用 spec 与设计文档持久化意图，用 Rules/Standards/Skills 保存团队私有知识，再用渐进式披露和按需注入控制 token 预算。
- 其中最值得保留的边界是：上下文的价值不取决于代码量，而取决于信噪比和知识结构化程度。复杂系统里，问题常常不是“AI 不适合”，而是关键私有知识没有被转成 AI 可消费的上下文。
- 它也让 Rules 与 Skills 的分工更清楚：极轻量、全局必须遵守的约束适合常驻 Rules；领域规范、最佳实践和排障流程更适合写成按需加载的 Skills。

## 新增视角：外部记忆也是上下文预算工程

- 新摄入的 Mem0 算法说明把“记忆”进一步从抽象名词压到了运行时预算问题：外部记忆的价值，不是让系统永远带着更长历史，而是在固定 token 预算里，把过去信息重新组织成当前最有用的证据。
- 这份资料明确反对把记忆系统缩成 `store / embed / retrieve` 三步，认为写入、检索和跨记忆推理必须一起设计；否则系统容易在 benchmark 上靠更大上下文抬分，却无法在生产里控制成本。
- 其中最值得保留的工程判断是：如果写入阶段用 `UPDATE / DELETE` 覆盖旧状态，就可能直接丢掉时间演化信息；而 `ADD-only` 写入配合 `semantic + keyword + entity` 多信号检索，更接近“如何把历史在预算内装回当前上下文”这一核心问题。
- 这里仍要保留证据边界：这些数值和效果来自 Mem0 自家平台与公开说法，不应直接外推成所有记忆系统都会收敛到同一实现。

## 新增视角：搜索基础设施也是上下文工程

- 新摄入的 Cloudflare AI Search 说明，把“给模型送对信息”进一步压到检索基础设施层：实例创建、作用域隔离、索引更新、混合搜索和跨实例查询，本身就是上下文工程的一部分。
- 这份资料尤其值得保留的是“作用域可编程”这一点：共享知识、单用户历史、单任务上下文，不一定该提前合并成一个总索引，而可以在运行时按需组合。
- 它也提供了一条边界样本：搜索原语可以解决“从哪里取信息、如何把多源结果排好序”，但还不能自动解决记忆的写入、冲突处理、衰减和遗忘。

## 新增视角：压缩、修剪与模式切换也是上下文工程

- OpenClaw 的资料把“如何让旧信息退场”写得更具体了：`full / minimal / none` 三种 `PromptMode` 决定不同场景下该常驻哪些模块，而 `/compact`、自动压缩和工具结果修剪则决定历史和输出如何退出当前工作记忆。
- 其中 `Compaction` 与 `Pruning` 的分工很值得保留：前者用摘要保留过去，后者用规则直接止损；两者都不是附属优化，而是上下文预算治理的主机制。
- 资料还指出，工作区里的 `AGENT.md / SOUL.md / USER.md / TOOLS.md` 等 Markdown 文件会直接进入 system prompt，这说明“长期资源如何被拆成不同文件并在何时加载”也属于上下文工程，而不只是文档整理。
- Hermes / OpenClaw 对比则补了一条更保守的工程判断：为了保护缓存命中与确定性，有的系统宁可用冻结记忆快照和字符串替换式压缩，也不把这类决策继续交给模型。

## 新增视角：上下文管理也会被训练

- 新摄入资料说明，到了 [Agent 训练](agent-training.md) 阶段，上下文管理不再只是运行时技巧。
- `summary`、`retrieval`、`pruning`、`memory update` 等动作，会直接影响奖励信号和长期任务表现。
- 这让 Context Engineering 从“提示词外围工程”进一步变成“能力形成过程的一部分”。

## 新增视角：会话边界也是上下文工程

- 新摄入的 Claude Code 资料把长任务中的每个 turn 都视为一个上下文分叉点：你可以继续当前轨迹，也可以 rewind、compact、clear 或切给 subagent。
- 这说明 Context Engineering 不只是在入口处拼装系统提示和资料，还包含运行中的“历史保留策略”。
- 资料给出的经验判断是：`1M context` 并不意味着应该一直延续同一会话；上下文衰减会先于硬上限出现，且任务切换通常也应伴随会话切换。
- 其中 `rewind` 和 `compact` 尤其值得区分：前者删除失败分支，后者保留连续性但依赖有损摘要；如果摘要发生在会话极长、方向又将变化的时候，最容易产生“坏 compact”。
- 相关概念可见 [Agent Session Management](agent-session-management.md)。

## 新增视角：信息分层是上下文工程的资源骨架

- Claude Skills 这份资料把上下文治理更明确地写成 `LOD-0 / LOD-1 / LOD-2` 三层：先让 Agent 知道资源存在，再给足够开始工作的核心层，最后只在必要时读取原始层。
- 这说明 Context Engineering 不只是“控制当前会话里出现哪些文本”，还包括长期资源应该如何被摘要、命名和分层暴露。
- 资料还强调层级必须与访问工具一起设计：如果下层主要通过 `grep`、SQL 或 API 被调用，那么摘要、标题和原始内容的组织方式都应服务于这些工具。
- Hermes 的实现样本又补上一条约束：索引层和完整内容层不一定进入同一消息层级。为了保护缓存，索引可以常驻 system prompt，而完整 Skill 更适合作为按需消息注入。
- 相关概念可见 [信息分层设计](information-layering-design.md)。

## 新增视角：工具自由度也是上下文边界

- ReAct 重试分析说明，一个常被忽略的上下文决策是：到底让模型自由输出到哪一层。
- 如果把“具体工具名”也交给模型在运行时自由生成，系统就会把不存在的工具当作上下文中的合法候选，从而把错误传播到重试和预算层。
- 这提示我们，Context Engineering 不只是多给信息，也是在设计“哪些选择空间要暴露给模型，哪些选择空间应收回到确定性代码里”。

## 新增视角：最小高信号 token 集合

- 新摄入的 `The Anatomy of an Agent Harness` 把 Context Engineering 的目标进一步压成一句很适合长期复用的话：不是把更长历史塞进窗口，而是找到“最小可能的高信号 token 集合”。
- 这条表述的重要性在于，它把上下文治理从“容量利用率”拉回“信号质量”。长窗口只是预算条件，不自动等于高质量上下文。
- 文中把常见退场机制总结成四类：`compaction`、`observation masking`、`just-in-time retrieval`、`subagent delegation`。它们的共同作用不是制造更漂亮的摘要，而是持续让旧信息以不同方式退出当前工作记忆。
- 这里也应保留证据边界：文中关于上下文腐化和中段信息衰减的数值，主要是对既有研究与实践的综合转述，适合作为问题意识，不宜单独当作定量定论。

## 新增视角：提示装配中的位置信号

- 这篇资料还提醒，上下文管理不只关心“装什么”，也关心“放在哪”。重要信息即使被成功装入，如果落在长上下文的中段，也可能更容易被忽略。
- 因此 `prompt assembly` 不能只理解为模板拼接；它同时也是一种位置信号调度，把系统指令、工具定义、短期历史、记忆和当前任务按优先级重新排布。
- 这使 `Context Engineering` 与 `Prompt Construction` 的边界更清楚了：前者决定哪些信息进入候选集，后者决定这些信息在当前轮如何被装配。

## 与其他概念的关系

- [Agentic Engineering](agentic-engineering.md) 把 Context Engineering 放进更大的 SDLC 协作方法论中，强调上下文质量是团队可控的核心杠杆。
- [Prompt Engineering](prompt-engineering.md) 更偏单轮指令表达和模板资产化。
- [Spec-driven Development](spec-driven-development.md) 试图解决需求契约不清的问题。
- [Harness Engineering](harness-engineering.md) 则进一步把规则和反馈机制工程化。
- [Agent Session Management](agent-session-management.md) 关注长任务里如何在 turn 边界主动编辑和切分上下文。
- [Agent 记忆系统](agent-memory-systems.md) 关注跨步骤、跨 session 的状态该如何写入、存放、检索与保留演化。
- [信息分层设计](information-layering-design.md) 关注长期资源应如何被拆成摘要层、核心层和原始层。
- [Jagged Intelligence](jagged-intelligence.md) 解释了为什么同一个模型会在不同上下文下表现出高度不平滑的能力边界。
- [大模型训练流水线](llm-training-pipeline.md) 提供了更大的背景：上下文控制既是应用层问题，也是训练栈问题。

## 开放问题

- 当前知识库还没有明确的“优先读哪些页面”的策略模板，后续可以补充更细的检索习惯。
- 当来源变多后，如何自动维护“最相关页面集合”仍是待解决问题。

## 来源

- [Prompt 工程实战指南：从 Prompt 硬编码到可控系统](../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md)
- [拥抱 AI 这一年：我的工具、实践和思考](../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md)
- [你不知道的大模型训练：原理、路径与新实践](../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md)
- [抽丝剥茧：深度解析 Hermes Agent 万字系统提示词（System Prompt）构成](../sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md)
- [一文搞懂Hermes：新顶流Agent如何从经验中自我进化](../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md)
- [深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践](../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md)
- [Hermes 凭什么两个月接棒 OpenClaw？](../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md)
- [你不知道的 Agent：原理、架构与工程实践](../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md)
- [How We Built the Token-Efficient Memory Algorithm](../sources/2026-04-17-how-we-built-the-token-efficient-memory-algorithm.md)
- [AI Search：为你的智能体打造的搜索原语](../sources/2026-04-16-ai-search-为你的智能体打造的搜索原语.md)
- [停止将 AI 记忆视为搜索问题](../sources/2026-04-12-停止将-ai-记忆视为搜索问题.md)
- [你的 ReAct 智能体正在浪费 90% 的重试机会——以下是解决方法](../sources/2026-04-12-你的-react-智能体正在浪费-90-的重试机会-以下是解决方法.md)
- [Using Claude Code: Session Management & 1M Context](../sources/2026-04-16-using-claude-code-session-management-1m-context.md)
- [从《塞尔达传说》到AI Agent：Claude Skills背后的信息分层设计哲学](../sources/2026-04-16-从塞尔达传说到ai-agent-claude-skills背后的信息分层设计哲学.md)
- [The Anatomy of an Agent Harness](../sources/2026-04-06-the-anatomy-of-an-agent-harness.md)
- [从第一性原理思考 Agentic Engineering](../sources/2026-04-23-从第一性原理思考-agentic-engineering.md)
