---
title: OpenClaw
type: entity
entity_type: project
status: active
sources:
  - ../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md
  - ../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md
  - ../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md
updated: 2026-04-17
---

OpenClaw 是当前知识库里第二个值得单独立页的稳定对象，因为它已经不只是“某个开源 Agent 名字”，而是反复被用来承接 Prompt 动态装配、文件驱动工作区、Skill 渐进披露、上下文压缩、记忆管理和 Hook/HITL Harness 的综合实现样本。对本库而言，它的长期价值不是热度，而是把多条抽象概念线收束到同一个运行时对象上。

## 纵向脉络

- 当前知识库最早把 OpenClaw 当作案例收进来的，是 [你不知道的 Agent：原理、架构与工程实践](../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md)：它用 OpenClaw 说明 Agent 主循环、渠道解耦、`Skills + MEMORY.md`、工具与协议如何拼回系统。
- [深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践](../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md) 则把这个对象继续拆细到 `PromptMode`、Markdown 文件注入、`Compaction / Pruning`、双层 Memory 和 Hook 机制。
- [Hermes 凭什么两个月接棒 OpenClaw？](../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md) 又提供了一个横向切面：OpenClaw 与 Hermes 的真正差异，可能不在功能表，而在默认自动化程度、记忆触发时机与 Harness 的硬规则取舍。
- 目前本库关于 OpenClaw 的材料主要来自解释性文章和综述，不是官方设计文档或仓库一手摘录，因此对象判断仍要保留证据边界。

## 横向位置

- 对 [Context Engineering](../concepts/context-engineering.md) 而言，OpenClaw 提供了 `full / minimal / none`、`Skills` 渐进披露、上下文压缩/修剪与 KV Cache 时间窗口治理这组具体样本。
- 对 [Harness Engineering](../concepts/harness-engineering.md) 而言，OpenClaw 把 Hook、`HEARTBEAT.md`、`BOOTSTRAP.md`、Guardrail 与人在环交互做成了比较完整的外层运行环境。
- 对 [信息分层设计](../concepts/information-layering-design.md) 而言，OpenClaw 展示了“工作区规则文件 / 用户画像文件 / 长期记忆 / 每日记忆 / Skill 文件”如何在不同场景下分层暴露。
- 对 [Agent 记忆系统](../concepts/agent-memory-systems.md) 而言，OpenClaw 提供了 `MEMORY.md + daily notes` 的双层实现，以及写入、召回、衰减和按行精读的具体工程线索。
- 对 [Hermes Agent](hermes-agent.md) 而言，OpenClaw 是目前最值得比较的相邻对象，因为两者都把 Skills、Memory 和 Harness 做成系统级能力，但自动化立场不同。
- 相关比较入口见 [Hermes 与 OpenClaw：默认自动化、记忆作用域与 Harness 取舍](../analyses/hermes-vs-openclaw-default-automation-comparison.md)。

## 当前判断

- OpenClaw 最值得长期保留的不是“火出圈”，而是它把 Agent 系统设计的多条线索收束成了一个文件驱动、约束明确的运行时样本。
- 当前最有复用价值的四组对象判断是：`Markdown 文件即运行时配置面`、`Skills 应渐进披露而非全量注入`、`上下文预算需要压缩与修剪并存`、`Memory 应拆分长期核心层与可检索日记层`。
- 新补的 Hermes 对比资料还提示了另一层判断：OpenClaw 不是缺少机制，而是更多把决定权留在用户或显式触发点上，这使它与 Hermes 形成“默认自动化程度不同”的对象对照。
- 需要保留的两层不确定性是：一，现有对象材料几乎都不是官方规范；二，涉及近期产品节奏、社区热度和功能先后关系的判断都高度时间敏感。

## 开放问题

- 是否需要继续直接摄入 OpenClaw 官方仓库、README、记忆系统和 Hook 机制源码，来减少当前对象页对二次解读的依赖。
- OpenClaw 与 Claude Code、Codex CLI、OpenHands 等其他对象，是否也适合沿当前 Hermes / OpenClaw 的比较维度继续扩展成更大的实现谱系。
- `MEMORY.md` 固定注入与每日笔记按需搜索的组合，在更长时间跨度下会不会出现摘要漂移或主记忆过载，目前仍缺少一手证据。

## 来源

- [你不知道的 Agent：原理、架构与工程实践](../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md)
- [深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践](../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md)
- [Hermes 凭什么两个月接棒 OpenClaw？](../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md)
