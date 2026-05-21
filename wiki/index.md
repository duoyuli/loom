# 总索引

本页是知识库的总导航。优先从这里进入目录索引或关键入口，再顺着链接进入具体页面阅读。

## 总览

- [总览](overview.md)：知识库当前的高层综述。
- [更新日志](log.md)：按时间顺序记录摄入、结构调整和巡检结果。

## 来源

- [来源索引](sources/index.md)：来源注册表与第一检索入口，统一记录 raw 路径、当前 source 页、canonical 路径、日期依据与迁移状态。
- 当前 `sources/` 已覆盖 Agentic Engineering、Agent 工程、Memory、Harness、Context、训练栈、Transformer 前史、LLM 推理系统和 agentic software 系统工程等主题；Harness 线索也继续从高自动化编排与 `agent-legible` 仓库，扩展到企业工程里的 `spec / handoff / checkpoint / evidence` 这组协作协议。查找具体来源时，优先从来源索引反查，而不是猜文件名。

## 实体

- [实体索引](entities/index.md)：稳定对象页的目录入口，说明何时拆分实体页以及如何与概念页、分析页分工。
- [Hermes Agent](entities/hermes-agent.md)：把系统提示 anatomy、`Skills` 学习闭环、记忆边界和缓存约束收束到同一个真实 Agent 对象上。
- [OpenClaw](entities/openclaw.md)：把文件驱动工作区、`Prompt / Context / Harness` 三层设计、双层 Memory 与 Hook 机制收束到同一个对象页上。

## 概念

- [概念索引](concepts/index.md)：按主题分组的概念与方法目录。
- [Agentic Engineering](concepts/agentic-engineering.md)：把 AI Agent 嵌入软件工程全链条，并用上下文、规格、验证、知识治理和错误回写来维持生产级约束。

## 分析

- [分析索引](analyses/index.md)：按 `subtype` 分组的分析页入口。
- [Hermes 与 OpenClaw：默认自动化、记忆作用域与 Harness 取舍](analyses/hermes-vs-openclaw-default-automation-comparison.md)：当前第一篇对象级 `comparison`，把比较轴从功能表改成默认自动化程度、记忆边界与确定性 Harness。
- 面向对象的深研页默认拆为 `timeline / comparison / synthesis` 三类，而不是新建额外顶层目录。

## 写作

- [专题文章目录](../writing/index.md)：承接从 wiki 研究到成文输出的流程。
- 当前尚无已发布专题文章。

## 维护提醒

- 2026-05-21 巡检发现：`raw/sources` 已增至 25 条，而 `wiki/sources` 与来源注册表仍停留在 23 条。本轮已补摄入 `How LLM Inference Works` 与 `Systems Engineering: Building Agentic Software That Works系统工程：构建能工作的代理软件`，并回写推理系统、系统工程、数据/安全/接口/基础设施边界等线索；修复后 `raw/sources`、`wiki/sources` 与来源注册表均为 25 条。
- 2026-04-24 巡检确认：`raw/sources`、`wiki/sources` 与来源注册表均为 23 条；`raw/assets` 2 份 PDF 均由 raw 指针笔记接回来源页；目录索引覆盖 concepts/entities/analyses 全部页面；wiki 内部 Markdown 链接无断链。本轮顺手统一了旧来源页的 `## 受影响页面` 检索入口。
- 2026-04-24 新摄入 `从第一性原理思考 Agentic Engineering` 后，Agent 主题补出更上位的方法论入口：用意图转化链、LLM 上下文/概率性/记忆约束和人类认知稀缺解释为什么需要 `Context / Spec / Harness / Knowledge as Code / Self-Refinement` 这一整组实践。
- 根索引只承担总导航，不重复目录内正文。
- 当某个目录变大时，优先补目录内 `index.md`，而不是机械拆出新顶层目录。
- 2026-04-19 已完成 `wiki/sources/` 路径收敛；后续新增来源继续沿用 canonical 文件名，`wiki/` 内部 Markdown 链接应保持无断链，`raw/sources/`、`wiki/sources/` 与来源注册表数量应保持一致。
- 2026-04-19 新摄入 `The Anatomy of an Agent Harness` 后，Agent 主题又补出一张更完整的 Harness 地图：不仅继续覆盖 `Prompt -> Context -> Spec -> Harness` 方法链、信息分层、会话边界治理、搜索原语、`Skills` 学习闭环与记忆治理，还把工具、状态、守卫、验证与子 Agent 编排重新收束到“非模型基础设施”这一总框架里。
- 2026-04-19 新摄入 `面向“Token 亿万富翁”的极限 Harness Engineering：100 万行代码、每天 10 亿 To...` 后，Harness 主题又补出一条更激进的高自动化样本：`< 1 分钟` 构建内循环、`agent-legible` 代码库与可观测性、把人工审查移出合并前关键路径，以及 `Symphony + 高保真 spec` 的多 Agent 编排。
- 2026-04-22 新摄入 `从玩具到生产力：用真实项目讲透 AI Agent 的 Harness Engineering` 后，Harness 主题又补出一条更偏企业交付协议的实践线：Harness 管的是非确定性模型如何进入确定性交付链，而 `spec / handoff / checkpoint / evidence` 则是对抗长任务漂移的持续控盘动作。
- 截至 2026-04-24，`raw/sources`、`wiki/sources` 与来源注册表均为 23 条；`raw/assets` 中 2 份 PDF 均有指针笔记、无旁路引用。来源页正文与 frontmatter 里指向 `raw/` 的相对路径已统一为 **URL 编码**（含中文与 `&`、`：` 等），以便 Obsidian 稳定打开；自动化脚本校验前应对路径做 `decode` 再判存在。
