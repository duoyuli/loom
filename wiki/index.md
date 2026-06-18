# 总索引

本页是知识库的总导航。优先从这里进入目录索引或关键入口，再顺着链接进入具体页面阅读。

## 总览

- [总览](overview.md)：知识库当前的高层综述。
- [更新日志](log.md)：按时间顺序记录摄入、结构调整和巡检结果。

## 来源

- [来源索引](sources/index.md)：来源注册表与第一检索入口，统一记录 raw 路径、当前 source 页、canonical 路径、日期依据与迁移状态。
- 当前 `sources/` 已覆盖 Agentic Engineering、Agent 架构控制流、Agent 工程、Memory / Dreaming、Heuristic Learning、Harness、Context、Agent 评测、FDE、训练栈、Transformer 前史、LLM 推理系统、KV/Prompt Cache 和 agentic software 系统工程等主题；Harness 线索也继续从高自动化编排与 `agent-legible` 仓库，扩展到企业工程里的 `spec / handoff / checkpoint / evidence`、生产 evaluation harness、前线产品发现、程序系统学习、Fable 5 自我纠错 loop、Copilot citation-verified memory 与 harness memory 横向综述。查找具体来源时，优先从来源索引反查，而不是猜文件名。

## 实体

- [实体索引](entities/index.md)：稳定对象页的目录入口，说明何时拆分实体页以及如何与概念页、分析页分工。
- [Hermes Agent](entities/hermes-agent.md)：把系统提示 anatomy、`Skills` 学习闭环、记忆边界和缓存约束收束到同一个真实 Agent 对象上。
- [OpenClaw](entities/openclaw.md)：把文件驱动工作区、`Prompt / Context / Harness` 三层设计、双层 Memory 与 Hook 机制收束到同一个对象页上。

## 概念

- [概念索引](concepts/index.md)：按主题分组的概念与方法目录。
- [Agentic Engineering](concepts/agentic-engineering.md)：把 AI Agent 嵌入软件工程全链条，并用上下文、规格、验证、知识治理和错误回写来维持生产级约束。
- [Loop Engineering](concepts/loop-engineering.md)：在 Harness 之上再加一层自运行编排，用自动化、worktree、Skills、连接器、子 Agent 和磁盘记忆设计“替你提示 Agent 的循环”。
- [Agent 评测](concepts/agent-evaluation.md)：把检索、生成、工具行为、长链路一致性、成本和延迟纳入同一套 evaluation harness。
- [Heuristic Learning](concepts/heuristic-learning.md)：把程序策略、状态检测器、测试、日志、回放和 memory 作为 coding agent 可持续更新的学习对象。
- [Forward Deployed Engineer](concepts/forward-deployed-engineer.md)：把客户现场的真实工作流发现回流为平台能力的前线工程角色。
- [LLM 推理系统](concepts/llm-inference-systems.md)：把训练完成后的模型服务拆成 `prefill / decode / KV cache / Prompt Cache / serving` 等运行时约束。

## 分析

- [分析索引](analyses/index.md)：按 `subtype` 分组的分析页入口。
- [Hermes 与 OpenClaw：默认自动化、记忆作用域与 Harness 取舍](analyses/hermes-vs-openclaw-default-automation-comparison.md)：当前第一篇对象级 `comparison`，把比较轴从功能表改成默认自动化程度、记忆边界与确定性 Harness。
- 面向对象的深研页默认拆为 `timeline / comparison / synthesis` 三类，而不是新建额外顶层目录。

## 写作

- [专题文章目录](../writing/index.md)：承接从 wiki 研究到成文输出的流程。
- 当前尚无已发布专题文章。

## 维护提醒

- 2026-06-18 增量巡检：`raw/sources`、`wiki/sources` 与来源注册表仍均为 36 条；70 个 wiki Markdown 页面范围内 882 个内部 Markdown / wiki 链接校验通过；36 个 source 页必需 frontmatter、`## 受影响页面` 与 raw 回溯通过；2 份 `raw/assets` PDF 均由 `raw/sources` 指针笔记接回来源页。本轮修复 [实体索引](entities/index.md) 中“来源索引”误指向总索引锚点的问题，改为直达 [来源注册表](sources/index.md)。
- 2026-06-10 增量巡检与补摄入：巡检发现 3 份未摄入 `raw/sources`，本轮按“遇到未摄入 raw 材料自动摄入”处理，新增 [Designing loops with Fable 5](sources/2026-06-10-designing-loops-with-fable-5.md)、[Memory Architecture of GitHub Copilot](sources/2026-06-10-memory-architecture-of-github-copilot.md)、[State of Memory in Agent Harness](sources/2026-06-02-state-of-memory-in-agent-harness.md) 三个 canonical source 页，来源注册表增至 36 条。回写 [Loop Engineering](concepts/loop-engineering.md)、[Agent 学习闭环](concepts/agent-learning-loop.md)、[Agent 记忆系统](concepts/agent-memory-systems.md)、[Harness Engineering](concepts/harness-engineering.md)、[Agent 评测](concepts/agent-evaluation.md)。证据边界：Fable 5 资料是 Anthropic 员工 X 长帖与小规模实验分享；两篇 Mem0 资料是供应商视角综述，应继续补 GitHub / OpenAI / Anthropic / Hermes / OpenClaw 等一手材料。修复后 `raw/sources`、`wiki/sources` 与来源注册表均为 36 条。
- 2026-06-10 增量巡检与补摄入：巡检发现 iCloud 在本次会话期间陆续把两份新资料同步进仓，`raw/sources` 增至 33 条，而 `wiki/sources` 与来源注册表仍停留在 31 条，缺口为 Addy Osmani 的 `Loop Engineering.`（2026-06-09）与 Claude Code 团队的 `Running an AI-native engineering org`（2026-06-03）。本轮补摄入两份来源：为前者新增 [Loop Engineering](concepts/loop-engineering.md) 概念页并回写 [Harness Engineering](concepts/harness-engineering.md)、[Agent 学习闭环](concepts/agent-learning-loop.md)、[Agentic Engineering](concepts/agentic-engineering.md)；后者折入 [Agentic Engineering](concepts/agentic-engineering.md) 的组织/流程维度，不单独立弱页。修复后 `raw/sources`、`wiki/sources` 与来源注册表均为 33 条，67 个 wiki Markdown 页面范围内的内部链接校验通过。证据边界：两份均为单一作者/单一团队的早期自述（Loop Engineering 作者本人持保留态度，AI-native org 来自 Claude 提供方），已在各自来源页标注，不作稳定共识处理。
- 2026-06-09 增量巡检确认：`raw/sources`、`wiki/sources` 与来源注册表均为 31 条；64 个 wiki Markdown 页面范围内的内部链接校验通过；目录索引覆盖全部 concepts/entities/analyses 内容页；31 个 source 页均有必需 frontmatter、`## 受影响页面` 与可回溯 raw 引用；2 份 `raw/assets` 均由 raw 指针笔记接回来源页。本轮未发现需要立即修正的事实冲突、断链或来源映射错误；后续空白仍集中在 `Knowledge as Code`、`Error-Driven Context Refinement`、搜索原语、Trace / 可观测性、ACI 工具设计和推理系统一手材料。
- 2026-05-23 晚间巡检确认：`raw/sources`、`wiki/sources` 与来源注册表均为 31 条；64 个 wiki Markdown 页面范围内的内部链接校验通过；目录索引覆盖全部 concepts/entities/analyses 内容页；31 个 source 页均有 `## 受影响页面` 与可回溯 raw 引用；2 份 `raw/assets` 均由 raw 指针笔记接回来源页。
- 2026-05-23 新摄入 `Learning Beyond Gradients` 后，新增 [Heuristic Learning](concepts/heuristic-learning.md) 概念页，把 coding agent 维护显式软件系统的现象沉淀为 `policy / state / feedback / trials / replay / memory / update mechanism` 闭环，并回写 Agent 学习闭环、Agent 训练、后训练与奖励设计、Harness Engineering 和 Agentic Engineering；修复后 `raw/sources`、`wiki/sources` 与来源注册表均为 31 条。
- 2026-05-23 复盘巡检确认：`raw/sources`、`wiki/sources` 与来源注册表均为 30 条；目录索引覆盖全部 concepts/entities/analyses 内容页；wiki 内部 Markdown 链接无断链；30 个 source 页均有 `## 受影响页面`；2 份 `raw/assets` 均由 raw 指针笔记接回来源页。
- 2026-05-23 新摄入 `从0开发大模型的17种Agent架构演进详细拆解` 后，Agent 架构主题补入 `state / router / evaluator / loop / termination / dry-run` 这组控制流复盘语言；修复后 `raw/sources`、`wiki/sources` 与来源注册表均为 30 条。
- 2026-05-23 新摄入 `用于自学习自主 Agents 的 Memory 与 Dreaming` 后，Memory 主题补入 Anthropic 的文件系统式 Memory、跨 Agent 共享作用域、乐观并发控制、版本审计与 Dreaming 式离线记忆优化循环；修复后 `raw/sources`、`wiki/sources` 与来源注册表均为 29 条。
- 2026-05-22 新摄入 `一文看懂 KV Cache 和 Prompt Cache 到底差在哪` 后，新增 [LLM 推理系统](concepts/llm-inference-systems.md) 概念页，把此前开放问题里的 `prefill / decode / KV cache / batching / quantization` 推理系统线索升格为独立入口，并补入 Prompt Cache、稳定前缀和缓存命中率对上下文工程与 API 成本的影响；修复后 `raw/sources`、`wiki/sources` 与来源注册表均为 28 条。
- 2026-05-21 巡检发现：`raw/sources` 已增至 25 条，而 `wiki/sources` 与来源注册表仍停留在 23 条。本轮已补摄入 `How LLM Inference Works` 与 `Systems Engineering: Building Agentic Software That Works系统工程：构建能工作的代理软件`，并回写推理系统、系统工程、数据/安全/接口/基础设施边界等线索；修复后 `raw/sources`、`wiki/sources` 与来源注册表均为 25 条。
- 2026-05-21 新摄入 `Building an Evaluation Harness for Production AI Agents: A 12-Metric Framework From 100+ Deployments` 后，新增 [Agent 评测](concepts/agent-evaluation.md) 概念页，把此前散落在 Harness、Context 与 Agent 工程综述中的评测线索收束为离线 benchmark、在线采样、LLM-as-judge、人工校准、trace-level coherence 和生产健康指标。
- 2026-05-21 新摄入 `当我们谈论 FDE 时，我们在谈论什么？` 后，新增 [Forward Deployed Engineer](concepts/forward-deployed-engineer.md) 概念页，把 FDE 从咨询/实施/外包中区分出来，并回写平台产品、现场发现、产物回流和 AI 工作流 golden case 这条组织线。
- 2026-04-24 巡检确认：`raw/sources`、`wiki/sources` 与来源注册表均为 23 条；`raw/assets` 2 份 PDF 均由 raw 指针笔记接回来源页；目录索引覆盖 concepts/entities/analyses 全部页面；wiki 内部 Markdown 链接无断链。本轮顺手统一了旧来源页的 `## 受影响页面` 检索入口。
- 2026-04-24 新摄入 `从第一性原理思考 Agentic Engineering` 后，Agent 主题补出更上位的方法论入口：用意图转化链、LLM 上下文/概率性/记忆约束和人类认知稀缺解释为什么需要 `Context / Spec / Harness / Knowledge as Code / Self-Refinement` 这一整组实践。
- 根索引只承担总导航，不重复目录内正文。
- 当某个目录变大时，优先补目录内 `index.md`，而不是机械拆出新顶层目录。
- 2026-04-19 已完成 `wiki/sources/` 路径收敛；后续新增来源继续沿用 canonical 文件名，`wiki/` 内部 Markdown 链接应保持无断链，`raw/sources/`、`wiki/sources/` 与来源注册表数量应保持一致。
- 2026-04-19 新摄入 `The Anatomy of an Agent Harness` 后，Agent 主题又补出一张更完整的 Harness 地图：不仅继续覆盖 `Prompt -> Context -> Spec -> Harness` 方法链、信息分层、会话边界治理、搜索原语、`Skills` 学习闭环与记忆治理，还把工具、状态、守卫、验证与子 Agent 编排重新收束到“非模型基础设施”这一总框架里。
- 2026-04-19 新摄入 `面向“Token 亿万富翁”的极限 Harness Engineering：100 万行代码、每天 10 亿 To...` 后，Harness 主题又补出一条更激进的高自动化样本：`< 1 分钟` 构建内循环、`agent-legible` 代码库与可观测性、把人工审查移出合并前关键路径，以及 `Symphony + 高保真 spec` 的多 Agent 编排。
- 2026-04-22 新摄入 `从玩具到生产力：用真实项目讲透 AI Agent 的 Harness Engineering` 后，Harness 主题又补出一条更偏企业交付协议的实践线：Harness 管的是非确定性模型如何进入确定性交付链，而 `spec / handoff / checkpoint / evidence` 则是对抗长任务漂移的持续控盘动作。
- 截至 2026-04-24，`raw/sources`、`wiki/sources` 与来源注册表均为 23 条；`raw/assets` 中 2 份 PDF 均有指针笔记、无旁路引用。来源页正文与 frontmatter 里指向 `raw/` 的相对路径已统一为 **URL 编码**（含中文与 `&`、`：` 等），以便 Obsidian 稳定打开；自动化脚本校验前应对路径做 `decode` 再判存在。
