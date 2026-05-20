---
title: Hermes 与 OpenClaw：默认自动化、记忆作用域与 Harness 取舍
type: analysis
subtype: comparison
status: evergreen
sources:
  - ../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md
  - ../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md
  - ../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md
  - ../sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md
  - ../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md
updated: 2026-04-17
---

这页把当前知识库里最成熟的一组对象比较单独收束起来。现有材料反复说明，Hermes 与 OpenClaw 的差异不应先从功能表理解，而应先从“系统替用户预做多少判断”来理解，再顺着这个轴观察 Skills、Memory、上下文压缩和 Harness 的不同默认策略。

## 研究问题

- Hermes 与 OpenClaw 当前最稳定、最值得长期复用的比较维度是什么。
- 哪些差异属于对象级结构差异，哪些只是近期产品节奏、热度或作者叙事。

## 主要判断

- 这组对象最值得保留的主轴不是“谁功能更多”，而是“默认自动化程度”。
- Hermes 更倾向把技能生成、记忆写入、风险控制和经验修补前置成系统默认动作；OpenClaw 更倾向保留显式触发点、文件接口和用户可见配置面。
- 两者都不是“有 / 没有 Skills、Memory、Harness”的差别，而是“这些机制由谁启动、何时启动、以多硬的规则启动”的差别。
- 因此，比较 Hermes 与 OpenClaw 时，最有用的不是列功能矩阵，而是比较 `Skills`、记忆作用域、上下文压缩和确定性约束分别被放在多靠前的位置。

## 证据与比较

### 1. 比较主轴：默认自动化程度，而不是功能表

- [Hermes 凭什么两个月接棒 OpenClaw？](../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md) 明确指出，两者在调度、子 Agent、浏览器、TTS、Vision 等表层能力上高度重合，真正差异主要在默认自动化值。
- 现有对象页也已经隐含同一判断：Hermes 更像把复杂度藏入默认规则，OpenClaw 更像把复杂度留在文件、命令和显式触发点上。
- 这条主轴比短期产品叙事更稳定，因为它能同时解释后续多个子系统差异。

### 2. Skills：都是程序性记忆，但启动方式不同

- Hermes 的 [Skills 自我进化拆解](../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md) 把程序性知识写成一条更主动的生命周期：复杂任务、错误修复和用户纠正都可能直接触发 Skill 创建或 patch。
- OpenClaw 的 [设计拆解](../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md) 则更强调 `Skills` 的渐进披露、文件读取和显式组织；现有材料没有把“何时自动形成新 Skill”写成同等强的默认约束。
- 因而两者都承认“程序性记忆应独立于稳定事实记忆”，但 Hermes 更前置自动蒸馏，OpenClaw 更前置可见配置与人工干预。

### 3. Memory：差异先出现在作用域和触发时机

- 现有来源把 OpenClaw 的记忆写成 `MEMORY.md + daily notes` 双层结构：长期核心层常驻，日记层按需检索、按时间衰减。
- Hermes 的材料则把重点放在“稳定事实 vs Skill”的边界，以及更主动的写入或反思触发。
- 对比来源进一步把差异压到四个更稳定的问题上：给谁服务、何时写、跨不跨项目、默认读什么。
- 这说明记忆系统的首要比较对象不是“后端叫什么”，而是作用域边界与写入门槛。

### 4. Context：都做分层装载，但缓存与压缩策略不同

- Hermes 的系统提示与 Skills 资料都强调：为了保护 `Prompt Caching`，索引层与完整 Skill 层分离，完整 Skill 倾向按需以消息注入，而不是频繁改写 system prompt。
- OpenClaw 的设计则更强调 `PromptMode`、工作区 Markdown 文件注入、`Compaction` 与 `Pruning` 并用，以及通过文件驱动工作区暴露长期上下文资源。
- 两者都在做信息分层，但 Hermes 更强调缓存边界和触发条件，OpenClaw 更强调工作区文件就是运行时配置面。

### 5. Harness：两者都承认外层约束，但确定性写法不同

- Hermes / OpenClaw 对比资料反复强调，Hermes 的很多稳定性来自“把复杂判断写成死规则”：固定轮数反思、字符串替换式压缩、冻结记忆快照、正则审批等。
- OpenClaw 的资料则把 Hook、`HEARTBEAT.md`、`BOOTSTRAP.md`、Guardrail 和人在环显式暴露为运行环境的一部分，让开发者和用户更容易看到干预接口。
- 这不意味着 Hermes “更强 Harness”、OpenClaw “更弱 Harness”，而是两者在“确定性规则藏在系统内部还是暴露在文件与接口层”上有不同立场。

### 6. 证据边界：当前比较仍主要依赖二次解读

- 当前比较已经能稳定沉淀的是结构差异，不是近期热度、星标变化、版本节奏或“谁接棒谁”的市场叙事。
- Hermes 与 OpenClaw 的对象页和比较页目前仍主要依赖解释性文章与源码摘录，而不是官方设计文档、README、release note 或系统性代码审计。
- 因此，本页适合作为“比较地图”，但不应把时间敏感的产品判断固化成长期结论。

## 结论或备忘

- 现阶段最稳的比较结论是：Hermes 与 OpenClaw 代表了两种不同的默认系统立场。
- Hermes 更像“系统多替用户做决定”：更主动地沉淀 Skill、写入记忆、执行 patch，并用更硬的默认规则换体验连续性。
- OpenClaw 更像“把运行时结构显式展开给用户”：通过文件、PromptMode、Hook 和分层记忆让系统更透明、更可干预。
- 这条差异轴对于后续继续比较 Claude Code、Codex CLI、OpenHands 等对象也有复用价值，因为它避免把对象比较退化为功能清单或短期口碑。

## 来源

- [Hermes 凭什么两个月接棒 OpenClaw？](../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md)
- [一文搞懂Hermes：新顶流Agent如何从经验中自我进化](../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md)
- [深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践](../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md)
- [抽丝剥茧：深度解析 Hermes Agent 万字系统提示词（System Prompt）构成](../sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md)
- [你不知道的 Agent：原理、架构与工程实践](../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md)

## 开放问题

- 当前比较仍明显缺少 Hermes 与 OpenClaw 官方仓库、README、release note 和代码路径级的一手对照。
- Claude Code、Codex CLI、OpenHands 等对象是否也适合沿“默认自动化程度 / 记忆作用域 / 确定性 Harness”这组维度补进同一比较谱系。
- 如果后续对象比较继续增多，是否需要再拆出一页更总括的 Agent 实现比较地图。
