# 更新日志

## [2026-05-21 00:00] 摄入 | Building an Evaluation Harness for Production AI Agents

- 新增：`wiki/sources/2026-05-13-building-an-evaluation-harness-for-production-ai-agents-a-12-metric-framework-from-100-deployments.md`
- 新增：`wiki/concepts/agent-evaluation.md`
- 更新：`wiki/sources/index.md`，来源注册表增至 26 条
- 更新：`wiki/concepts/harness-engineering.md`、`wiki/concepts/agentic-engineering.md`、`wiki/concepts/context-engineering.md`，补入 production evaluation harness、离线/在线评测、LLM-as-judge、工具行为、多步一致性、成本和延迟指标
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次把此前多次出现但未成页的 `Agent 评测` 升格为概念页；`Trace / 可观测性` 暂不单独开页，先作为后续开放问题保留。

## [2026-05-21 00:00] 巡检与补摄入 | 来源注册表漂移修复

- 巡检发现：`raw/sources` 为 25 条，`wiki/sources` 与 `wiki/sources/index.md` 仍为 23 条，缺口为 `How LLM Inference Works` 与 `Systems Engineering: Building Agentic Software That Works系统工程：构建能工作的代理软件`
- 新增：`wiki/sources/2026-05-03-how-llm-inference-works.md`
- 新增：`wiki/sources/2026-04-08-systems-engineering-building-agentic-software-that-works系统工程-构建能工作的代理软件.md`
- 更新：`wiki/sources/index.md`，来源注册表增至 25 条
- 更新：`wiki/concepts/transformer-architecture.md`、`wiki/concepts/system-and-training-constraints.md`、`wiki/concepts/llm-training-pipeline.md`，补入 `prefill / decode / KV cache / quantization / serving` 推理系统线索
- 更新：`wiki/concepts/agentic-engineering.md`、`wiki/concepts/harness-engineering.md`、`wiki/concepts/context-engineering.md`、`wiki/concepts/agent-memory-systems.md`、`wiki/analyses/agentic-workflow-stack.md`，补入 agentic software 五层系统工程与数据/安全/接口/基础设施边界
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：修复后 `raw/sources`、`wiki/sources` 与来源注册表均为 25 条；本轮仍保留“自动化结构检查脚本”作为后续空白

## [2026-04-24 19:43] 巡检 | 全库结构、来源映射与链接一致性

- 检查：`raw/sources` 23 条、`wiki/sources` 来源页 23 条、`wiki/sources/index.md` 注册表 23 条，未发现漏登记、重复映射或非 canonical 来源路径。
- 检查：`raw/assets` 2 份 PDF 均有 `raw/sources` 指针笔记，来源页 frontmatter 同时保留指针笔记与 asset，未发现资产旁路引用。
- 检查：`concepts / entities / analyses` 目录索引覆盖全部页面；页面 frontmatter 中 `title / type / updated` 与 analysis `subtype` 均通过检查。
- 检查：对 `wiki/` 内 Markdown 相对链接做 URL decode 后校验，未发现断链。
- 修正：将 6 个旧来源页的 `## 本次摄入带来的页面变化` 统一为 `## 受影响页面`，并为 5 个较早来源页补入 `## 受影响页面`，方便按来源反查受影响概念页。
- 后续空白：`Knowledge as Code / Error-Driven Context Refinement` 仍主要由单篇 Agentic Engineering 来源支撑；`搜索原语 / 检索基础设施`、`Trace / 可观测性`、`ACI 工具设计` 仍值得后续独立补一手材料；本轮巡检脚本尚未固化为仓库内维护工具。

## [2026-04-24 13:55] 摄入 | 从第一性原理思考 Agentic Engineering

- 新增：`wiki/sources/2026-04-23-从第一性原理思考-agentic-engineering.md`
- 新增：`wiki/concepts/agentic-engineering.md`
- 更新：`wiki/sources/index.md`
- 更新：`wiki/concepts/context-engineering.md`、`wiki/concepts/spec-driven-development.md`、`wiki/concepts/harness-engineering.md`、`wiki/concepts/agent-learning-loop.md`
- 更新：`wiki/analyses/agentic-workflow-stack.md`、`wiki/analyses/index.md`
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次把新资料压成 Agentic Engineering 上位概念，而不是为 `agentic-engineering-framework` 单独开弱实体页；现有 `Context / Spec / Harness / 学习闭环` 页面分别补入最小高信号上下文、意图转化链、分步多层验证、`Knowledge as Code + Error-Driven Context Refinement`。`raw/sources`、`wiki/sources` 与来源注册表同步为 23 条。

## [2026-04-22 12:39] 摄入 | 从玩具到生产力：用真实项目讲透 AI Agent 的 Harness Engineering

- 新增：`wiki/sources/2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md`
- 更新：`wiki/sources/index.md`
- 更新：`wiki/concepts/harness-engineering.md`、`wiki/concepts/spec-driven-development.md`、`wiki/concepts/agent-session-management.md`
- 更新：`wiki/index.md`、`wiki/overview.md`
- 备注：本次没有为 `Aegis`、`sdd-riper-one-light` 或“程序员角色迁移”单独开弱页，而是把新资料压回现有方法链；`Harness Engineering` 补入“治理非确定性系统”的边界、伪 Harness / 劣质 Harness 区分与持续控盘节奏，`Spec-driven Development` 补入 `Spec is Truth`、契约式 gate 与 `Reverse Sync`，`Agent Session Management` 则补入 `spec / handoff` 恢复、执行前 `checkpoint` 和阶段验收这组会话边界协议。

## [2026-04-19 11:47] 摄入 | 面向“Token 亿万富翁”的极限 Harness Engineering

- 新增：`wiki/sources/2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md`
- 更新：`wiki/sources/index.md`
- 更新：`wiki/concepts/harness-engineering.md`、`wiki/concepts/spec-driven-development.md`
- 更新：`wiki/analyses/agentic-workflow-stack.md`
- 更新：`wiki/index.md`
- 备注：本次把新资料压回现有方法链，而不是为 `Symphony`、`Frontier` 或 `agent-legible` 单独开弱页；`Harness Engineering` 补入 `< 1 分钟` 构建循环、`agent-legible` 仓库、可观测性和“把人类移出合并前关键路径”的更激进样本，`Spec-driven Development` 补入 `ghost library / 高保真 spec` 作为多 Agent 编排交接物，`Agent 工作流分层框架` 则补入高自动化协作方法层的运行前提。

## [2026-04-19] 结构 | Obsidian 可跳转的 raw 链接（全文 URL 编码）

- 更新：全部 `wiki/sources/*` 中指向 `../../raw/sources/`、`../../raw/assets/` 的 Markdown 链接与 frontmatter `sources` 列表，对路径做 **单次** `percent-encode`（空格、`&`、`：`、中文标点与文件名中的 `%` 等均编码），避免 Obsidian 点击「原始摘录 / 原始 PDF / 资料指针」失效；曾误二次编码的条目已用「先反复 `unquote` 再 `quote`」归一。
- 新增：`## 来源` + `[原始摘录](...)` 正文块（此前仅有 YAML、无正文链接的 5 篇）：ReAct 重试、AI Search 搜索原语、Agent Memory 架构本质、停止将记忆视为搜索问题、Harness 控制论讲透。
- 检查：对 `wiki/` 内指向 `.md`/`.pdf` 的相对链接在 URL 解码后做存在性校验，目标均为真实文件。

## [2026-04-19 纠错] Obsidian 跳转 | 带空格 raw 路径保留 URL 编码

- 更正：`wiki/sources/2026-04-06-the-anatomy-of-an-agent-harness.md` 中「原始摘录」恢复为 `The%20Anatomy%20of%20an%20Agent%20Harness.md`。此前改为字面空格后，Obsidian 无法从来源页跳回 `raw/sources/` 下该文件。
- 备注：自动化校验本地链接时应对路径做 URL 解码再 `exists`；编辑器跳转与脚本不必强行统一为一种写法。

## [2026-04-19 巡检] 例行巡检 | 映射、资产旁路与链接一致性

- 备注：指向 `raw/` 下带空格文件名的 Markdown 链接，在 Obsidian 中更稳妥的写法是 URL 编码（`%20`）；自动化校验应对路径 `decode` 后再判存在。Harness 摘录与 RNN 原始 PDF 链接均已按此策略保留编码形式。
- 检查：`raw/sources/`（20）、`wiki/sources/` 来源页（20，不含 `index.md`）、来源注册表行数（20）一致；未发现一份 raw 对多份 source 或漏登记
- 检查：`raw/assets/` 现存 2 份 PDF，均有对应 `raw/sources/` 指针笔记，来源页 `sources` frontmatter 可双向回溯
- 检查：`wiki/concepts/index.md` 已覆盖当前全部 16 张概念页；`wiki/analyses/index.md` 与 2 张已存在分析页一致
- 检查：对 `wiki/` 内相对链接做存在性校验（含 URL 解码）共 455 处，目标均存在
- 结论：无未解决的结构性断链或注册表漂移；已登记证据边界仍包括 [The Anatomy of an Agent Harness](sources/2026-04-06-the-anatomy-of-an-agent-harness.md) 的「12 组件声称 vs 正文 11 条」与 [Attention Is All You Need](sources/2017-06-12-attention-is-all-you-need.md) 的 BLEU 数值内部差异
- 后续空白（与此前判断一致、仍值得单独升格或补一手证据的）：`搜索原语 / 检索基础设施` 独立概念页、`ACI 工具设计`、`Trace / 可观测性`；Hermes/OpenClaw 比较轴向更多 Harness 厚度的系统外推时仍缺仓库级证据

## [2026-04-19 00:47] 结构修复 | wiki/sources 完整迁移到 canonical 路径

- 更新：`wiki/sources/index.md`
- 更新：19 篇 `wiki/sources/` 来源页路径，统一迁到 canonical 文件名；其余 1 篇已在规范路径上
- 更新：`wiki/concepts/`、`wiki/entities/`、`wiki/analyses/`、`wiki/overview.md`、`wiki/log.md` 内全部 source 入链，回写到新路径
- 更新：`wiki/index.md`
- 检查：当前 `wiki/` 内部 Markdown 链接无断链；`raw/sources/`、`wiki/sources/` 与来源注册表均为 20 条；来源注册表 20 条记录已全部改为 `canonical`
- 备注：此前“命名协议已建立但 legacy path 仍占多数”的阶段已结束。后续新增来源应直接落在 canonical 路径；如果未来再出现临时旧路径，也必须在同次实质更新中一并迁正。

## [2026-04-19 00:33] 巡检 | 来源注册表一致性与结构状态

- 更新：`wiki/sources/index.md`
- 更新：`wiki/index.md`
- 检查：当前 `wiki/` 内部 Markdown 链接无断链；`raw/sources/`、`wiki/sources/` 与来源注册表均为 20 条，未发现漏登记来源或重复映射
- 结论：本轮唯一硬错误是 `你不知道的 Agent：原理、架构与工程实践` 在来源注册表中的 `source_date` 漂移为 `2026-04-15`，现已回写为与来源页一致的 `2026-03-19`
- 备注：当前主要结构问题已从断链和漏页收敛为 legacy source 路径仍占多数，以及若干高频主题尚未升格为独立概念页；其中最明显的研究空白仍是 `搜索原语 / 检索基础设施`、`ACI 工具设计` 与 `Trace / 可观测性`

## [2026-04-19 00:21] 结构收敛 | 来源命名协议与注册表

- 新增：`wiki/sources/index.md`
- 新增：`raw/sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning-link.md`
- 新增：`raw/sources/2017-06-12-attention-is-all-you-need-link.md`
- 更新：`AGENTS.md`、`README.md`
- 更新：`wiki/index.md`
- 更新：20 篇 `wiki/sources/` 来源页，统一补入 `source_date` / `source_date_basis`，并把两份仅存于 `raw/assets/` 的论文来源接回 `raw/sources/` 指针笔记
- 备注：本次不强制迁移现有 legacy source 路径，而是先把新协议收敛到 `AGENTS.md`，再通过来源注册表显式记录 `raw_path / current_wiki_path / canonical_wiki_path / migration_status`。后续新增来源必须直接使用规范文件名；旧来源页只有在发生实质更新时才顺手迁到 canonical 路径。

## [2026-04-19 00:04] 摄入 | The Anatomy of an Agent Harness

- 新增：`wiki/sources/2026-04-06-the-anatomy-of-an-agent-harness.md`
- 更新：`wiki/concepts/harness-engineering.md`、`wiki/concepts/context-engineering.md`、`wiki/concepts/agent-memory-systems.md`
- 更新：`wiki/index.md`、`wiki/overview.md`
- 备注：本次摄入没有新开弱支撑概念页，而是把材料压回现有主题：`Harness Engineering` 补入“非模型基础设施”边界、组件检查表、Harness 厚度与模型共演化；`Context Engineering` 补入“最小高信号 token 集合”、上下文腐化与历史退场策略；`Agent 记忆系统` 补入“分层记忆只是提示线索、行动前仍应核验真实状态”的谨慎原则。同时保留了原文“声称 12 个组件但正文只列出 11 个”的冲突点，避免把综述型清单误当成定稿 taxonomy。

## [2026-04-17 23:30] 巡检 | 回收 Hermes/OpenClaw 比较入口

- 新增：`wiki/analyses/hermes-vs-openclaw-default-automation-comparison.md`
- 更新：`wiki/analyses/index.md`
- 更新：`wiki/entities/hermes-agent.md`、`wiki/entities/openclaw.md`
- 更新：`wiki/sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md`
- 更新：`wiki/sources/2017-06-12-attention-is-all-you-need.md`、`wiki/sources/2026-04-17-how-we-built-the-token-efficient-memory-algorithm.md`
- 更新：`wiki/index.md`、`wiki/overview.md`
- 备注：本次巡检确认当前 `raw/` 与 `wiki/sources/` 映射完整、无漏索引页面；主要结构问题不再是缺页，而是同一比较意图分散在实体页与来源页的开放问题里。已把 `Hermes Agent / OpenClaw` 这组重复出现的比较需求回收到首个对象级 `comparison` 分析页，并把空的 `Comparison` 目录真正补成可复用入口，同时修正两处指向原始资料的本地断链；当前剩余空白转向补一手仓库/文档证据，以及验证这组比较维度能否扩展到更多 Agent 对象。

## [2026-04-17 23:20] 批量摄入 | 搜索原语、Memory 治理与 Harness 可靠性

- 新增：`wiki/sources/2026-04-16-ai-search-为你的智能体打造的搜索原语.md`
- 新增：`wiki/sources/2026-04-15-agent-memory-架构本质.md`
- 新增：`wiki/sources/2026-04-12-停止将-ai-记忆视为搜索问题.md`
- 新增：`wiki/sources/2026-04-17-一文讲透-harness-engineering-即控制论.md`
- 新增：`wiki/sources/2026-04-12-你的-react-智能体正在浪费-90-的重试机会-以下是解决方法.md`
- 更新：`wiki/concepts/context-engineering.md`、`wiki/concepts/agent-memory-systems.md`、`wiki/concepts/harness-engineering.md`
- 更新：`wiki/index.md`、`wiki/overview.md`
- 备注：本次批量摄入把新到材料压回三个已有主题而不是新开弱支撑概念页：`Context Engineering` 补入搜索原语与工具自由度边界，`Agent 记忆系统` 补入 `State / Policy / Profile` 边界、四类建模对象、生命周期字段与矛盾/过期治理，`Harness Engineering` 补入控制回路、传感器、错误分类、熔断器与确定性工具路由

## [2026-04-17 22:59] 继续摄入 | OpenClaw 设计拆解 与 Hermes/OpenClaw 对比

- 新增：`wiki/sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md`
- 新增：`wiki/sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md`
- 新增：`wiki/entities/openclaw.md`
- 更新：`wiki/entities/hermes-agent.md`
- 更新：`wiki/concepts/context-engineering.md`、`wiki/concepts/harness-engineering.md`、`wiki/concepts/information-layering-design.md`、`wiki/concepts/agent-memory-systems.md`
- 更新：`wiki/entities/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次摄入把 OpenClaw 从综述里的案例提升为第二个实体页，并补入 `PromptMode`、Markdown 文件注入、`Compaction / Pruning`、双层 Memory、Hook/HITL Harness 这组更具体的实现样本；同时用 Hermes/OpenClaw 对比把对象差异压回到默认自动化程度、记忆触发和确定性规则取舍，而不是功能表叙事

## [2026-04-17 22:57] 摄入 | Hermes Skills 自我进化拆解

- 新增：`wiki/sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md`
- 新增：`wiki/entities/hermes-agent.md`
- 更新：`wiki/concepts/agent-learning-loop.md`、`wiki/concepts/information-layering-design.md`、`wiki/concepts/context-engineering.md`、`wiki/concepts/agent-memory-systems.md`
- 更新：`wiki/entities/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次摄入把 Hermes 从“系统提示 anatomy 样本”推进为首个实体页，并把 `Skills` 机制沉淀为程序性知识资产的闭环：创建触发、条件激活、渐进式加载、`Prompt Caching` 保护与执行中 patch

## [2026-04-17 22:54] 摄入 | 浅谈 Agent Memory

- 新增：`wiki/sources/2026-04-12-浅谈-agent-memory.md`
- 更新：`wiki/concepts/agent-memory-systems.md`
- 更新：`wiki/index.md`
- 备注：本次摄入把 Agent Memory 主题从此前偏工程实现的写入算法与检索结构，扩展到 CoALA 四类记忆、`RAG ≠ Memory`、写入/整理/读取生命周期、`Raw vs Derived` 张力、遗忘机制、五种架构哲学、治理层与多 Agent 共享；当前空白转向不同记忆系统实现样本之间的横向对照，以及遗忘与共享边界的具体工程协议

## [2026-04-17 15:17] 摄入 | How We Built the Token-Efficient Memory Algorithm

- 新增：`wiki/sources/2026-04-17-how-we-built-the-token-efficient-memory-algorithm.md`
- 新增：`wiki/concepts/agent-memory-systems.md`
- 更新：`wiki/concepts/context-engineering.md`
- 更新：`wiki/sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md`
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次摄入把“Agent 记忆系统”从此前综述里的待拆主题推进成独立概念页，并用 Mem0 的记忆算法说明补入 `ADD-only` 写入、多信号检索、agent-generated facts 与 token 预算这组更具体的工程问题；当前空白转向跨框架记忆系统的横向对照

## [2026-04-16 13:43] 摄入 | Prompt 工程指南 与 Claude Skills 信息分层设计

- 新增：`wiki/sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md`
- 新增：`wiki/sources/2026-04-16-从塞尔达传说到ai-agent-claude-skills背后的信息分层设计哲学.md`
- 新增：`wiki/concepts/prompt-engineering.md`
- 新增：`wiki/concepts/information-layering-design.md`
- 更新：`wiki/concepts/context-engineering.md`、`wiki/concepts/spec-driven-development.md`、`wiki/concepts/harness-engineering.md`
- 更新：`wiki/analyses/agentic-workflow-stack.md`
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次并行摄入补上了缺失的 `Prompt Engineering` 入口页，并把 Claude Skills 背后的 `LOD + 按需加载` 组织原则沉淀为“信息分层设计”；当前 Agent 主题已覆盖从 Prompt 资产化到上下文分层与回归治理的一整条方法链

## [2026-04-16 13:35] 摄入 | Using Claude Code: Session Management & 1M Context

- 新增：`wiki/sources/2026-04-16-using-claude-code-session-management-1m-context.md`
- 新增：`wiki/concepts/agent-session-management.md`
- 更新：`wiki/concepts/context-engineering.md`
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次摄入把 Claude Code 的 `1M context` 使用建议沉淀为“会话边界治理”主题，补入 `rewind / compact / clear / subagent` 这一组运行时上下文操作，并明确长上下文不会消除上下文衰减与任务切换带来的会话管理问题

## [2026-04-15 23:28] 摄入 | Hermes 系统提示拆解 与 Agent 工程综述

- 新增：`wiki/sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md`
- 新增：`wiki/sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md`
- 更新：`wiki/concepts/context-engineering.md`、`wiki/concepts/harness-engineering.md`
- 更新：`wiki/analyses/agentic-workflow-stack.md`
- 更新：`wiki/index.md`、`wiki/overview.md`
- 备注：本次并行摄入一篇真实 Agent 系统提示 anatomy 拆解和一篇 Agent 工程综述，把当前知识库的 Agent 主题从方法论继续压到运行时实现层，补入了上下文装配、任务状态外化、协议、评测与 Trace 等结构线索

## [2026-04-15 21:43] 摄入 | A Critical Review of Recurrent Neural Networks for Sequence Learning

- 新增：`wiki/sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md`
- 新增：`wiki/concepts/recurrent-neural-networks.md`
- 更新：`wiki/concepts/transformer-architecture.md`、`wiki/concepts/system-and-training-constraints.md`
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次摄入补上 Transformer 之前的序列学习前史，把 `RNN / LSTM / BRNN / BPTT` 作为独立概念落库，同时明确其历史价值主要在于解释长程依赖、串行瓶颈与训练稳定性，而不是直接解释现代 LLM 产品体验

## [2026-04-15 13:03] 巡检 | 链接覆盖与结构回写

- 检查：确认当前 `wiki/` 页面不存在真实断链，也没有漏出索引体系的页面
- 更新：`wiki/concepts/agent-learning-loop.md`，补入 `## 定义` 与 `## 与其他概念的关系`，加强与方法链、能力边界和分析页的互链
- 更新：`wiki/index.md`、`wiki/overview.md`，回写本轮巡检结论与当前维护空白
- 备注：当前主要问题已从“缺页或断链”转向“弱互链页面仍需持续补强”；自动化巡检脚本仍是明显空白

## [2026-04-15 13:00] 摄入 | Attention Is All You Need

- 新增：`wiki/sources/2017-06-12-attention-is-all-you-need.md`
- 新增：`wiki/concepts/transformer-architecture.md`
- 更新：`wiki/concepts/system-and-training-constraints.md`
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次摄入补入 Transformer 原始论文，把“架构选择会改变并行性、路径长度与训练成本”的一手证据回写到训练栈主题中；同时保留了论文内部 `41.8 / 41.0 BLEU` 的数值冲突

## [2026-04-15 12:54] 结构纠偏 | 实体索引改为准入说明页

- 更新：`wiki/entities/index.md`，去掉预先展开的空分类，改为记录当前状态、实体页准入条件与目录分工
- 更新：`wiki/index.md`，调整“实体索引”描述，使其与当前目录现状一致
- 备注：当前还没有需要独立承接的稳定对象，实体目录应先作为轻量地图页，而不是预设空壳分类

## [2026-04-15 12:53] 继续摄入 | 你不知道的大模型训练：原理、路径与新实践

- 新增：`wiki/concepts/data-recipe.md`、`wiki/concepts/system-and-training-constraints.md`
- 更新：`wiki/sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md`
- 更新：`wiki/concepts/llm-training-pipeline.md`、`wiki/concepts/post-training-and-reward-design.md`、`wiki/concepts/agent-training.md`
- 更新：`wiki/analyses/llm-training-stack.md`
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次继续摄入把训练前的“数据配方 / 系统约束”从总纲中拆开，并补清评分链路风险与发布后持续塑形的结构

## [2026-04-15 12:49] 重摄入 | 拥抱 AI 这一年：我的工具、实践和思考

- 更新：`wiki/sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md`，补充工作空间诉求、方法链递进关系、后半段反思与证据边界
- 新增：`wiki/concepts/jagged-intelligence.md`
- 更新：`wiki/concepts/context-engineering.md`、`wiki/concepts/harness-engineering.md`、`wiki/concepts/spec-driven-development.md`、`wiki/concepts/agent-learning-loop.md`
- 更新：`wiki/analyses/agentic-workflow-stack.md`
- 更新：`wiki/concepts/index.md`、`wiki/index.md`、`wiki/overview.md`
- 备注：本次重摄入重点回收原文后半段关于能力边界与协作心智的长期价值，并把二手转述与一手经验的证据边界写清

## [2026-04-14 23:58] 纠偏 | 横纵分析法回收到 AGENTS

- 更新：`AGENTS.md`，明确横纵分析法属于维护流程记忆，不属于知识库内容页
- 更新：`wiki/index.md`、`wiki/overview.md`、`wiki/concepts/index.md`、`wiki/entities/index.md`、`wiki/analyses/index.md`
- 删除：`wiki/concepts/horizontal-and-vertical-analysis.md`
- 备注：避免把研究协议误读为概念内容；对象深研流程统一以 `AGENTS.md` 为准

## [2026-04-14 23:43] 结构升级 | 接入横纵分析法

- 新增：`wiki/entities/index.md`、`wiki/concepts/index.md`、`wiki/analyses/index.md`
- 新增：`wiki/concepts/horizontal-and-vertical-analysis.md`
- 更新：`AGENTS.md`，补充横纵分析法协议、`entity_type` / `subtype` 字段与目录内索引规则
- 更新：`wiki/index.md`、`wiki/overview.md`
- 更新：`wiki/analyses/agentic-workflow-stack.md`、`wiki/analyses/llm-training-stack.md`，回填 `subtype: framework`
- 更新：两篇来源页，补充 `## 纵向线索` 与 `## 横向线索`
- 备注：目录结构保持 `sources / entities / concepts / analyses` 不变，深研协议改为通过来源线索、实体页三段结构和 `timeline / comparison / synthesis` 三类分析页承接

## [2026-04-14 22:36] 摄入 | 你不知道的大模型训练：原理、路径与新实践

- 新增：`wiki/sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md`
- 新增：`wiki/concepts/llm-training-pipeline.md`
- 新增：`wiki/concepts/post-training-and-reward-design.md`
- 新增：`wiki/concepts/agent-training.md`
- 新增：`wiki/analyses/llm-training-stack.md`
- 更新：`wiki/concepts/harness-engineering.md`、`wiki/concepts/context-engineering.md`
- 更新：`wiki/index.md`、`wiki/overview.md`
- 备注：把新来源沉淀为“训练流水线、后训练与奖励、Agent 训练”三组核心概念，并回写到现有 Harness / Context 主题中

## [2026-04-14 21:30] 摄入 | 拥抱 AI 这一年：我的工具、实践和思考

- 新增：`wiki/sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md`
- 新增：`wiki/concepts/harness-engineering.md`
- 新增：`wiki/concepts/context-engineering.md`
- 新增：`wiki/concepts/spec-driven-development.md`
- 新增：`wiki/concepts/agent-learning-loop.md`
- 新增：`wiki/analyses/agentic-workflow-stack.md`
- 更新：`wiki/index.md`、`wiki/overview.md`
- 备注：将原文沉淀为“Agent 工作流分层框架”，并拆出 Harness、Context、Spec 与学习闭环四个可复用概念页

## [2026-04-14 21:30] 规范对齐 | 采用标准 AGENTS

- 更新：根目录 `AGENTS.md` 替换为统一版“知识库操作规范”
- 删除：旧的 `wiki/people/`、`wiki/projects/`、`wiki/qa/`、`wiki/terms/`、`wiki/topics/`、`wiki/overview/` 空目录
- 校对：当前目录结构与规则文档统一为 `raw/sources`、`raw/assets`、`wiki/sources`、`wiki/entities`、`wiki/concepts`、`wiki/analyses`

## [2026-04-14 21:20] 目录重构 | 对齐最终结构

- 移动：`index.md` 到 `wiki/index.md`
- 移动：`log.md` 到 `wiki/log.md`
- 移动：`wiki/overview/knowledge-map.md` 到 `wiki/overview.md`
- 新增：`wiki/entities/`、`wiki/concepts/`、`wiki/analyses/`
- 更新：`README.md`、`AGENTS.md`、`wiki/index.md`、`wiki/overview.md` 以匹配新结构
- 备注：知识库目录统一为 `sources / entities / concepts / analyses`

## [2026-04-14 21:10] 结构收敛 | 只保留根目录说明

- 删除：所有子目录 `README.md`
- 删除：`templates/` 目录及模板文件
- 更新：将目录说明与页面约定收拢到根目录 `README.md`
- 更新：修正 `index.md` 与 `wiki/overview/knowledge-map.md` 中的引用
- 备注：目录保留 `raw/` 与 `wiki/` 分类结构，但不再为每个子目录单独写说明页

## [2026-04-14 20:55] 初始化 | 中文个人知识库骨架

- 新增：`AGENTS.md`、`README.md`、`index.md`、`log.md`
- 新增：`raw/`、`wiki/`、`templates/` 基础目录与说明页
- 新增：来源、主题、人物、问答等页面模板
- 备注：本次初始化参考 Karpathy 的 “LLM Wiki” 模式，改写为中文个人知识库工作流
