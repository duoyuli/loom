---
title: 来源索引
type: source-index
status: active
updated: 2026-05-22
---

本页是 `wiki/sources/` 的来源注册表与第一检索入口。它的职责不是重复每个来源页正文，而是显式登记 raw 路径、当前 source 页、canonical 路径、日期依据和迁移状态，避免写作、分析和巡检时只能靠猜文件名。

## 使用约定

- 新增或查找来源时，先看本页，再进入具体 source 页。
- 当前注册表中的 28 条来源都已经迁到 canonical 路径；后续新增来源也应直接以 canonical 路径入库。
- 如果未来再次出现遗留路径，仍用 `migration_status` 显式标记，而不是靠约定猜测。
- `source_date_basis` 仅有两种：`published` 表示已知资料发布日期，`created` 表示发布日期未知，暂用 `raw/sources/` 文件创建时间兜底。

## 来源注册表

| source_date | basis | title | current_wiki_path | canonical_wiki_path | raw_path | migration_status |
| --- | --- | --- | --- | --- | --- | --- |
| 2026-05-21 | published | 当我们谈论 FDE 时，我们在谈论什么？ | [2026-05-21-当我们谈论-fde-时-我们在谈论什么.md](2026-05-21-当我们谈论-fde-时-我们在谈论什么.md) | `wiki/sources/2026-05-21-当我们谈论-fde-时-我们在谈论什么.md` | `raw/sources/当我们谈论 FDE 时，我们在谈论什么？.md` | `canonical` |
| 2026-05-19 | published | 一文看懂 KV Cache 和 Prompt Cache 到底差在哪 | [2026-05-19-一文看懂-kv-cache-和-prompt-cache-到底差在哪.md](2026-05-19-一文看懂-kv-cache-和-prompt-cache-到底差在哪.md) | `wiki/sources/2026-05-19-一文看懂-kv-cache-和-prompt-cache-到底差在哪.md` | `raw/sources/一文看懂 KV Cache 和 Prompt Cache 到底差在哪.md` | `canonical` |
| 2026-05-13 | published | Building an Evaluation Harness for Production AI Agents: A 12-Metric Framework From 100+ Deployments | [2026-05-13-building-an-evaluation-harness-for-production-ai-agents-a-12-metric-framework-from-100-deployments.md](2026-05-13-building-an-evaluation-harness-for-production-ai-agents-a-12-metric-framework-from-100-deployments.md) | `wiki/sources/2026-05-13-building-an-evaluation-harness-for-production-ai-agents-a-12-metric-framework-from-100-deployments.md` | `raw/sources/Building an Evaluation Harness for Production AI Agents A 12-Metric Framework From 100+ Deployments.md` | `canonical` |
| 2026-05-03 | published | How LLM Inference Works | [2026-05-03-how-llm-inference-works.md](2026-05-03-how-llm-inference-works.md) | `wiki/sources/2026-05-03-how-llm-inference-works.md` | `raw/sources/How LLM Inference Works.md` | `canonical` |
| 2026-04-23 | published | 从第一性原理思考 Agentic Engineering | [2026-04-23-从第一性原理思考-agentic-engineering.md](2026-04-23-从第一性原理思考-agentic-engineering.md) | `wiki/sources/2026-04-23-从第一性原理思考-agentic-engineering.md` | `raw/sources/从第一性原理思考 Agentic Engineering.md` | `canonical` |
| 2026-04-21 | published | 从玩具到生产力：用真实项目讲透 AI Agent 的 Harness Engineering | [2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md](2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md) | `wiki/sources/2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md` | `raw/sources/从玩具到生产力：用真实项目讲透 AI Agent 的 Harness Engineering.md` | `canonical` |
| 2026-04-17 | published | 一文讲透：Harness Engineering 即控制论！ | [2026-04-17-一文讲透-harness-engineering-即控制论.md](2026-04-17-一文讲透-harness-engineering-即控制论.md) | `wiki/sources/2026-04-17-一文讲透-harness-engineering-即控制论.md` | `raw/sources/一文讲透：Harness Engineering 即控制论！.md` | `canonical` |
| 2026-04-17 | created | How We Built the Token-Efficient Memory Algorithm | [2026-04-17-how-we-built-the-token-efficient-memory-algorithm.md](2026-04-17-how-we-built-the-token-efficient-memory-algorithm.md) | `wiki/sources/2026-04-17-how-we-built-the-token-efficient-memory-algorithm.md` | `raw/sources/How We Built the Token-Efficient Memory Algorithm.md` | `canonical` |
| 2026-04-16 | published | AI Search：为你的智能体打造的搜索原语 | [2026-04-16-ai-search-为你的智能体打造的搜索原语.md](2026-04-16-ai-search-为你的智能体打造的搜索原语.md) | `wiki/sources/2026-04-16-ai-search-为你的智能体打造的搜索原语.md` | `raw/sources/AI Search：为你的智能体打造的搜索原语.md` | `canonical` |
| 2026-04-16 | created | Prompt 工程实战指南：从 Prompt 硬编码到可控系统 | [2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md](2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md) | `wiki/sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md` | `raw/sources/Prompt 工程实战指南：从 Prompt 硬编码到可控系统.md` | `canonical` |
| 2026-04-16 | created | Using Claude Code: Session Management & 1M Context | [2026-04-16-using-claude-code-session-management-1m-context.md](2026-04-16-using-claude-code-session-management-1m-context.md) | `wiki/sources/2026-04-16-using-claude-code-session-management-1m-context.md` | `raw/sources/Using Claude Code Session Management & 1M Context.md` | `canonical` |
| 2026-04-16 | created | 从《塞尔达传说》到AI Agent：Claude Skills背后的信息分层设计哲学 | [2026-04-16-从塞尔达传说到ai-agent-claude-skills背后的信息分层设计哲学.md](2026-04-16-从塞尔达传说到ai-agent-claude-skills背后的信息分层设计哲学.md) | `wiki/sources/2026-04-16-从塞尔达传说到ai-agent-claude-skills背后的信息分层设计哲学.md` | `raw/sources/从《塞尔达传说》到AI Agent：Claude Skills背后的信息分层设计哲学.md` | `canonical` |
| 2026-04-15 | published | Agent Memory 架构本质 | [2026-04-15-agent-memory-架构本质.md](2026-04-15-agent-memory-架构本质.md) | `wiki/sources/2026-04-15-agent-memory-架构本质.md` | `raw/sources/Agent Memory 架构本质.md` | `canonical` |
| 2026-04-15 | published | 抽丝剥茧：深度解析 Hermes Agent 万字系统提示词（System Prompt）构成 | [2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md](2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md) | `wiki/sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md` | `raw/sources/抽丝剥茧：深度解析 Hermes Agent 万字系统提示词（System Prompt）构成.md` | `canonical` |
| 2026-04-15 | published | 一文搞懂Hermes：新顶流Agent如何从经验中自我进化 | [2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md](2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md) | `wiki/sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md` | `raw/sources/一文搞懂Hermes：新顶流Agent如何从经验中自我进化.md` | `canonical` |
| 2026-04-15 | published | Hermes 凭什么两个月接棒 OpenClaw？ | [2026-04-15-hermes-凭什么两个月接棒-openclaw.md](2026-04-15-hermes-凭什么两个月接棒-openclaw.md) | `wiki/sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md` | `raw/sources/Hermes 凭什么两个月接棒 OpenClaw？.md` | `canonical` |
| 2026-04-13 | published | 深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践 | [2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md](2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md) | `wiki/sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md` | `raw/sources/深度解析 OpenClaw 在 Prompt  Context  Harness 三个维度中的设计哲学与实践.md` | `canonical` |
| 2026-04-12 | published | 浅谈 Agent Memory | [2026-04-12-浅谈-agent-memory.md](2026-04-12-浅谈-agent-memory.md) | `wiki/sources/2026-04-12-浅谈-agent-memory.md` | `raw/sources/浅谈 Agent Memory.md` | `canonical` |
| 2026-04-12 | published | 停止将 AI 记忆视为搜索问题 | [2026-04-12-停止将-ai-记忆视为搜索问题.md](2026-04-12-停止将-ai-记忆视为搜索问题.md) | `wiki/sources/2026-04-12-停止将-ai-记忆视为搜索问题.md` | `raw/sources/停止将 AI 记忆视为搜索问题.md` | `canonical` |
| 2026-04-12 | published | 你的 ReAct 智能体正在浪费 90% 的重试机会——以下是解决方法 | [2026-04-12-你的-react-智能体正在浪费-90-的重试机会-以下是解决方法.md](2026-04-12-你的-react-智能体正在浪费-90-的重试机会-以下是解决方法.md) | `wiki/sources/2026-04-12-你的-react-智能体正在浪费-90-的重试机会-以下是解决方法.md` | `raw/sources/你的 ReAct 智能体正在浪费 90% 的重试机会——以下是解决方法.md` | `canonical` |
| 2026-04-10 | published | 拥抱 AI 这一年：我的工具、实践和思考 | [2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md](2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md) | `wiki/sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md` | `raw/sources/拥抱 AI 这一年：我的工具、实践和思考.md` | `canonical` |
| 2026-04-08 | published | Systems Engineering: Building Agentic Software That Works系统工程：构建能工作的代理软件 | [2026-04-08-systems-engineering-building-agentic-software-that-works系统工程-构建能工作的代理软件.md](2026-04-08-systems-engineering-building-agentic-software-that-works系统工程-构建能工作的代理软件.md) | `wiki/sources/2026-04-08-systems-engineering-building-agentic-software-that-works系统工程-构建能工作的代理软件.md` | `raw/sources/Systems Engineering Building Agentic Software That Works系统工程：构建能工作的代理软件.md` | `canonical` |
| 2026-04-07 | published | 面向“Token 亿万富翁”的极限 Harness Engineering：100 万行代码、每天 10 亿 To... | [2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md](2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md) | `wiki/sources/2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md` | `raw/sources/面向“Token 亿万富翁”的极限 Harness Engineering：100 万行代码、每天 10 亿 To....md` | `canonical` |
| 2026-04-06 | published | The Anatomy of an Agent Harness | [2026-04-06-the-anatomy-of-an-agent-harness.md](2026-04-06-the-anatomy-of-an-agent-harness.md) | `wiki/sources/2026-04-06-the-anatomy-of-an-agent-harness.md` | `raw/sources/The Anatomy of an Agent Harness.md` | `canonical` |
| 2026-04-03 | published | 你不知道的大模型训练：原理、路径与新实践 | [2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md](2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md) | `wiki/sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md` | `raw/sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md` | `canonical` |
| 2026-03-19 | published | 你不知道的 Agent：原理、架构与工程实践 | [2026-03-19-你不知道的-agent-原理-架构与工程实践.md](2026-03-19-你不知道的-agent-原理-架构与工程实践.md) | `wiki/sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md` | `raw/sources/你不知道的 Agent：原理、架构与工程实践.md` | `canonical` |
| 2017-06-12 | published | Attention Is All You Need | [2017-06-12-attention-is-all-you-need.md](2017-06-12-attention-is-all-you-need.md) | `wiki/sources/2017-06-12-attention-is-all-you-need.md` | `raw/sources/2017-06-12-attention-is-all-you-need-link.md` | `canonical` |
| 2015-10-17 | published | A Critical Review of Recurrent Neural Networks for Sequence Learning | [2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md](2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md) | `wiki/sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning.md` | `raw/sources/2015-10-17-a-critical-review-of-recurrent-neural-networks-for-sequence-learning-link.md` | `canonical` |
