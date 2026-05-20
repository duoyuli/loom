---
title: 信息分层设计
type: concept
status: active
sources:
  - ../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md
  - ../sources/2026-04-16-从塞尔达传说到ai-agent-claude-skills背后的信息分层设计哲学.md
  - ../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md
  - ../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md
updated: 2026-04-17
---

信息分层设计指的是：不要把完整知识一次性塞给 Agent，而是把同一资源拆成不同精度的层级，让 Agent 先发现、再读取核心、最后只在必要时深入原始细节。它既是 token 预算治理方法，也是决策质量治理方法。

## 定义

这套设计通常至少包含两层：元信息和完整内容。面对更大的信息体时，则会扩展成三层或更多层：`LOD-0` 负责发现，`LOD-1` 负责让 Agent 直接开始工作，`LOD-2` 负责承接完整原始信息或精确查询。新摄入的 Claude Skills 资料强调，层级本身并不独立存在，必须和读取工具、查询方式、文件组织一起设计。

Hermes 的 `Skills` 闭环则给出一个更工程化的三层样本：system prompt 里的技能索引负责发现，`skill_view` 加载的完整 `SKILL.md` 负责执行，Skill 引用的参考文档或模板构成更深原始层。文章还补了一个此前页面里较弱的维度：分层不仅决定“读几层”，还决定“谁在当前会话里可见”，因此 `platforms`、`requires_*` 和 `fallback_for_*` 这类条件门控也属于信息暴露设计的一部分。

OpenClaw 的资料又把分层从 Skill 资源扩到整个工作区：`AGENT.md / SOUL.md / USER.md / TOOLS.md` 这些文件分工暴露不同维度的上下文，`MEMORY.md` 与每日记忆又形成“固定注入层 / 按需检索层”的另一组分层。再加上 `full / minimal / none` 三种 `PromptMode`，说明分层不只是一套静态文件结构，也是一套按角色和场景切换可见范围的机制。

## 纵向脉络

- 在 Agent 工程语境里，这个概念常从 `Skills`、长文档、数据库摘要或代码仓库导航中显形。
- 早期做法常是“把更多说明塞进 Prompt”；信息分层设计则把重点转成“先给摘要，再按需展开”。
- 当系统继续复杂化后，这个概念会和 [Context Engineering](context-engineering.md) 汇合，因为上下文管理本质上也在决定哪些层常驻、哪些层按需进入。

## 横向位置

- 它不直接回答任务目标，而是回答“长期知识和资源该如何暴露给 Agent”。
- [Prompt Engineering](prompt-engineering.md) 更偏单次调用的表达结构。
- [Context Engineering](context-engineering.md) 更偏运行时装配与上下文裁剪。
- 信息分层设计进一步下沉到资源组织本身：标题、摘要、核心文档、原始数据和查询入口如何形成分工。

## 与其他概念的关系

- `Skills` 的元数据索引、完整 `SKILL.md` 与被引用扩展文档，是这种设计的典型样本。
- Hermes 的 `技能索引 -> 完整 Skill -> 支撑文件` 路径说明，层级设计还会被缓存设计反向约束：索引适合常驻，完整内容更适合按需注入。
- 文件系统本身可以看作最基础的两层结构：文件名与文件内容。
- OpenClaw 的 `MEMORY.md` 与 `memory/YYYY-MM-DD.md` 说明，记忆系统内部也可以做信息分层：长期核心层适合固定暴露，日记层更适合只在需要时通过检索进入。
- 高质量摘要层需要持续维护，否则就会与底层原始信息漂移；这个同步问题会进入 [Harness Engineering](harness-engineering.md)。
- 当信息层被设计得足够稳定后，Agent 才更容易做按需加载、局部检索和工具调用选择。

## 开放问题

- 当前知识库仍缺少自动检查机制，无法确认索引页和概念页摘要是否与底层页面长期一致。
- 三层架构适用于“大到不能直接读完”的对象，但如何量化这个阈值，仍需要更多案例。

## 来源

- [你不知道的 Agent：原理、架构与工程实践](../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md)
- [从《塞尔达传说》到AI Agent：Claude Skills背后的信息分层设计哲学](../sources/2026-04-16-从塞尔达传说到ai-agent-claude-skills背后的信息分层设计哲学.md)
- [一文搞懂Hermes：新顶流Agent如何从经验中自我进化](../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md)
- [深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践](../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md)
