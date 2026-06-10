---
title: Agent 评测
type: concept
status: active
sources:
  - ../sources/2026-05-13-building-an-evaluation-harness-for-production-ai-agents-a-12-metric-framework-from-100-deployments.md
  - ../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md
  - ../sources/2026-06-10-designing-loops-with-fable-5.md
  - ../sources/2026-06-10-memory-architecture-of-github-copilot.md
  - ../sources/2026-06-02-state-of-memory-in-agent-harness.md
updated: 2026-06-10
---

Agent 评测指的是为多步骤、会用工具、会检索和会产生长期状态影响的 AI Agent 建立可复现、可观测、可回归的质量判断体系。它不只回答“最终答案对不对”，还要定位检索、生成、工具选择、工具执行、长链路一致性、成本和延迟分别在哪里失控。

## 定义

在普通软件里，测试通常围绕确定性输入输出、接口契约和运行时健康指标展开；在 Agent 系统里，评测还要处理模型非确定性、上下文质量、工具调用、检索链路和真实用户分布漂移。

因此 Agent 评测至少包含三层：

- 离线评测：用带 ground truth 或人工标注的 benchmark 抓回归。
- 在线评测：对生产流量抽样或持续打分，发现 benchmark 未覆盖的真实分布问题。
- 生产观测：把 trace、成本、延迟、错误类型和工具执行结果接入 dashboard 与 alerting。

## 纵向脉络

早期 Agent 讨论里，评测常被压缩成 `task / trial / grader`：先定义任务，再让 Agent 跑一次 trial，最后由 grader 判断结果。这个模型仍然重要，因为它把“先修评测，再改 Agent”的工程顺序说清楚了。

新摄入的 12 指标框架则把评测继续推进到生产系统：评测对象不再只是 final answer，而是每次检索、每次生成、每次工具调用、每条多步 trace 和每个生产请求的成本与延迟。

## 横向位置

Agent 评测处在 [Harness Engineering](harness-engineering.md)、[Context Engineering](context-engineering.md) 和生产可观测性之间：

- Harness 负责把约束和反馈接入运行时；评测是其中最重要的反馈传感器之一。
- Context Engineering 负责让模型看到高质量信息；检索相关指标负责判断这件事是否真的发生。
- 生产观测负责监控成本、延迟和错误；Agent 评测把这些指标与语义质量和工具行为接起来。

## 指标地图

12 指标框架可以作为当前最小地图：

- 检索质量：`context relevance / context recall / context precision / retrieval latency`
- 生成质量：`answer faithfulness / answer relevance / hallucination rate`
- Agent 行为：`tool selection accuracy / tool execution success / multi-step coherence`
- 生产健康：`cost per query / p99 latency`

这组拆法的价值在于定位失败层级。比如“幻觉”不一定只来自模型胡编，也可能来自上游检索召回不足、上下文排序错误、prompt 鼓励外推或工具结果没有被正确带回 trace。

## 新增视角：Grader、生产 outcome 与 memory benchmark 的边界

- `Designing loops with Fable 5` 把评测放进执行 loop：独立 grader 子 Agent 在独立上下文里判定 rubric 是否满足，能缓解模型对自身输出 self-critique 过宽的问题。
- Copilot Memory 的资料提示，memory 系统也需要生产 outcome，而不仅是 recall 分数：PR merge rate、code review feedback 这类指标虽然口径仍需一手核对，但方向上比“是否想起旧事实”更接近真实效用。
- `State of Memory in Agent Harness` 对 memory benchmark 的批评值得并入本页：很多基准仍是 recall-centric，不能说明记忆是否改善行动、是否能处理 staleness、污染、选择性遗忘和长跨度生产规模。

## 在线与离线

离线评测适合进入 CI/CD：当检索策略、prompt 模板、工具 schema 或 agent logic 发生变化时，用固定样本抓回归。它的前提是有标注集、标准答案或至少有人工确认的相关 chunk。

在线评测适合进入生产监控：真实用户问题通常没有标准答案，因此更依赖 faithfulness、relevance、hallucination、tool success、latency、cost 等 proxy signal。在线评测的重点不是证明系统永远正确，而是尽早发现分布漂移、长尾问题和趋势恶化。

## 当前判断

Agent 评测不应被理解为上线前的最后一道 QA，而是 production harness 的核心组成。没有评测，Harness 只能靠规则限制动作；有了评测，Harness 才能知道哪些规则有效、哪些上下文失真、哪些工具调用正在变坏，以及哪些优化只是把问题转移到了成本或延迟上。

同时要保留边界：LLM-as-judge 和自动指标本身也会漂移、偏置或被 prompt 影响。高风险场景仍需要人工校准、失败样本复核和跨模型裁判，而不是把一个 judge 分数当成最终事实。

## 与其他概念的关系

- [Harness Engineering](harness-engineering.md)：Agent 评测是 Harness 的反馈层与生产 gate。
- [Context Engineering](context-engineering.md)：检索指标直接衡量上下文是否相关、完整、排序正确且足够快。
- [Agentic Engineering](agentic-engineering.md)：Agent 评测把“多层次验证”从开发流程推进到真实生产流量。
- [Agent 训练](agent-training.md)：训练侧 eval、grader 和 reward 会影响模型行为；应用侧评测则约束部署后的系统行为。
- [Agent 记忆系统](agent-memory-systems.md)：memory 评测不能只测召回，还要测记忆是否被验证、是否改善行动、是否避免旧事实污染。

## 开放问题

- 是否需要把 `Trace / 可观测性` 单独拆页，专门沉淀 span schema、事件流、tool call 记录和 replay。
- 哪些评测工具的一手资料最值得优先摄入：Ragas、OpenTelemetry、LangSmith、DeepEval 或 TruLens。
- 对本知识库自身而言，哪些维护动作可以被转成 evaluation harness，例如来源覆盖率、断链、孤页、日志同步和回答回写质量。

## 来源

- [Building an Evaluation Harness for Production AI Agents: A 12-Metric Framework From 100+ Deployments](../sources/2026-05-13-building-an-evaluation-harness-for-production-ai-agents-a-12-metric-framework-from-100-deployments.md)
- [你不知道的 Agent：原理、架构与工程实践](../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md)
- [Designing loops with Fable 5](../sources/2026-06-10-designing-loops-with-fable-5.md)
- [Memory Architecture of GitHub Copilot](../sources/2026-06-10-memory-architecture-of-github-copilot.md)
- [State of Memory in Agent Harness](../sources/2026-06-02-state-of-memory-in-agent-harness.md)
