---
title: State of Memory in Agent Harness
type: source
status: summarized
sources:
  - ../../raw/sources/State%20of%20Memory%20in%20Agent%20Harness.md
source_date: 2026-06-02
source_date_basis: published
created: 2026-06-10
updated: 2026-06-10
---

这是一篇 Mem0 的 In Context 系列长文，横向比较 Claude Code、Anthropic Managed Agents、Codex、GitHub Copilot、OpenClaw、Hermes、Bedrock AgentCore、Windsurf、Devin 等 harness 的 memory 设计。它的长期价值不在于把各产品细节一次定稿，而在于提供了一张“工作记忆 / 外部记忆 / 参数记忆”“bounded local storage / keyword retrieval / harness scope / staleness / isolation”维度图，帮助 [Agent 记忆系统](../concepts/agent-memory-systems.md) 从单点案例进入横向比较。

## 这份资料是什么

- 资料来自 Mem0 于 2026-06-02 发布的 X 长文 / In Context #11。
- 主题是 major agent harnesses 如何处理 memory，以及现有 memory benchmark 的局限。
- 文中覆盖多个产品与论文，并以 Mem0 作为跨 harness 外部 memory layer 的解决方向收束。

## 主要观点

1. Agent memory 至少要区分三层：session 内工作记忆、权重外持久外部记忆、通过梯度进入模型权重的参数记忆。2026 年生产系统基本都停留在外部记忆层。
2. 各 harness 的 memory 方案差异很大：Claude Code 是本地 markdown + 文件名选择；Managed Agents 是 append-only event log + mounted memory store；Codex 是本地 markdown 记忆目录与后处理 consolidation；Copilot 是 citation-verified repo memory；OpenClaw / Hermes / Windsurf / Devin 各自有不同作用域与写入触发。
3. 共同短板集中在五处：存储 bounded and local、检索多为 keyword 或文件名、memory harness-scoped、staleness 处理弱、隔离和跨用户污染风险高。
4. 现有 memory benchmarks 多数仍是 recall-centric，未充分测 memory 是否真正改善行动；LoCoMo 等常见基准甚至可能被简单 grep 近似。
5. 未来 memory 更像基础设施，而不是某个 harness 的附属功能：需要语义检索、跨工具 portability、身份作用域、staleness / forgetting / isolation 机制。

## 纵向线索

- 对 [Agent 记忆系统](../concepts/agent-memory-systems.md) 来说，这篇资料把单一产品案例推成横向维度：memory 的位置、作用域、写入触发、检索方式、过期机制、隔离机制和 benchmark 口径都必须分别记录。
- 它也补强“RAG 不是 Memory”的判断：检索只是读取路径，无法替代写入、失效、遗忘和权限治理。

## 横向线索

- 与 [Harness Engineering](../concepts/harness-engineering.md)：资料直接把 memory 放在 harness 层讨论，说明 context、tool orchestration、agent coordination 和 memory 越来越像同一个 runtime product。
- 与 [Agent 评测](../concepts/agent-evaluation.md)：memory benchmark 的弱点提醒，评测不应只测回忆事实，还要测记忆是否改善任务行动、是否能处理旧事实和污染。
- 与 [Agent 记忆系统](../concepts/agent-memory-systems.md) 的开放问题：ChatGPT Memory、LangMem、NotebookLM 之外，还应补 Codex、Claude Code、Copilot、Hermes、OpenClaw 等 coding harness 的一手 memory 资料。

## 与知识库的相关性

本资料适合回写 [Agent 记忆系统](../concepts/agent-memory-systems.md) 与 [Harness Engineering](../concepts/harness-engineering.md)，但暂不新建“Memory in Agent Harness”分析页：当前材料是 Mem0 的供应商综述，适合作为索引和问题地图；若后续补齐各产品一手文档，再整理成正式横向 comparison 更稳。

## 可靠性判断

- 这是 Mem0 的供应商视角综述，覆盖面广但不可直接替代各产品的一手文档。
- 文中不少产品细节和 benchmark 数字需要回到官方文档或论文原文核对；本页主要保留比较轴和待验证线索。
- 文章最后明显服务于 Mem0 产品定位，关于“外部 memory layer 是答案”的判断应与其他来源对照。

## 受影响页面

- [Agent 记忆系统](../concepts/agent-memory-systems.md)
- [Harness Engineering](../concepts/harness-engineering.md)
- [Agent 评测](../concepts/agent-evaluation.md)

## 开放问题

- 是否基于这篇综述与后续一手来源建立 `agent-harness-memory-comparison` 分析页，取决于是否补齐官方文档与论文证据。
- Codex、Claude Code、Copilot、Hermes、OpenClaw 等 memory 机制目前在本库仍缺系统性一手对照。
- Memory benchmark 应如何从 recall 测试转向 action utility、staleness handling、selective forgetting 和 isolation，需要继续补研究来源。

## 来源

- [原始摘录](../../raw/sources/State%20of%20Memory%20in%20Agent%20Harness.md)
- [原文链接](https://x.com/mem0ai/status/2061822612398014782)
