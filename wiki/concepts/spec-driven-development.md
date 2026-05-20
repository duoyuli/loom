---
title: Spec-driven Development
type: concept
status: active
sources:
  - ../sources/2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md
  - ../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md
  - ../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md
  - ../sources/2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md
  - ../sources/2026-04-23-从第一性原理思考-agentic-engineering.md
updated: 2026-04-24
---

Spec-driven Development 强调先把要做什么写成清晰契约，再让 Agent 动手实现。它要解决的不是模型不会写代码，而是人类的意图往往表达得不够清楚。

## 定义

按照这份资料的描述，`spec` 不必是冗长需求文档，而应该是一份足够清晰的契约，说明目标、边界、约束、不要做什么以及验收标准。新摄入的 Prompt 工程指南则把这个概念往运行时推进了一步：当一条调用路径已经稳定下来，`Prompt + 模型 + 参数` 也可以被绑成一个 request spec，作为评估、回滚和搜索的最小单位。

新摄入的“从玩具到生产力”实践文又把 `spec` 往长任务协作里推进了一层：它不只是实现前的说明书，也是一份最小真相源，用来恢复当前做到哪里、这轮只准做什么、什么暂不处理，以及暂停后下一轮从哪继续。这里的 `spec` 因而更像可持续维护的状态锚点，而不是一次性立项文档。

## 这份资料中的关键判断

- Agent 擅长模式匹配，不擅长“读心”。
- 模糊需求会让 Agent 根据训练分布自行补全意图，从而造成返工。
- 分阶段验收的 gated workflow 虽然看起来更慢，但通常能减少后续修正成本。
- 这份来源还把 GitHub `Spec Kit` 当作一个工作流参照，但这里保留为线索即可，不把它直接等同于唯一正确的 spec 形式。

## 对知识库的启发

- 知识库摄入任务本身就很适合采用 spec 思路，例如明确要求来源页、受影响页面、索引更新和日志追加。
- 对长期维护来说，清晰的页面边界比一次写出“大而全”的总结更重要。
- 后续若让 Agent 进行更复杂的批量摄入或巡检，建议把输出格式、更新范围和验收项写得更显式。

## 新增视角：Request Spec 是契约的运行时形态

- 这份新资料提醒我们，Spec 不一定只存在于“任务还没开始做”的需求文档阶段。
- 当某条 LLM 调用需要长期维护时，`Prompt 模板 + 模型 + 默认参数` 可以下沉成一个 request spec，并获得独立名字。
- 这样，团队讨论的就不再是“试了下换模型”这种口头描述，而是比较两个明确的规格对象在 golden case 与线上表现上的差异。
- 这也自然接受了“不同模型需要不同 Prompt”这一现实，因为 spec 的最小单位本来就不是脱离模型的 Prompt。

## 新增视角：高保真 Spec 也是多 Agent 的交接物

- 新摄入的 “Token 亿万富翁” 资料把 `spec` 推到了比需求文档更强的位置：当 `PRD/spec` 足够细致时，它本身就可以成为多个 coding agents 之间的稳定交接物，而不只是实现前的人类说明书。
- 文中的 “ghost library” 说法值得保留，因为它强调了一种极端情形：复杂系统未必需要共享完整源码，也可能由高保真规范不断被重新生成。
- 这也让 `Spec-driven Development` 与 [Harness Engineering](harness-engineering.md) 的边界更清楚了：Spec 负责界定目标和交接对象，Harness 负责让这些规范在实际编排、验证和 PR 生命周期里可执行。
- 仍要保留证据边界：这种“代码更可抛弃、规范更持久”的判断来自 OpenAI Frontier 实验与 Latent Space 转述，当前更像 frontier 样本，不宜直接当成普遍工程事实。

## 新增视角：`Spec is Truth` 首先是恢复点，而不是大文档

- Aegis 实践强调，长任务最怕的不是“没有文档”，而是任务状态只活在聊天窗口里。一旦上下文腐烂、人类中断或切线程，Agent 就会靠模糊印象续写。
- 因此更有用的 spec 不是更长，而是足够精简却能稳定回答四件事：总目标是什么、当前阶段目标是什么、本轮边界是什么、暂停后下一轮从哪接上。
- 这也解释了为什么文章把 `spec / handoff` 放在一起谈。二者不一定是两类不同对象，更像同一份真相源在“执行前约束”和“执行后恢复”两个时点上的不同用法。

## 新增视角：契约式 gate 让 Spec 从静态文档变成执行协议

- 文章借 `sdd-riper-one-light` 给出了一组更像工程协议的写法：执行前用 `Checkpoint + Restate First` 做前置断言，执行后用 `Reverse Sync` 做后置断言，中间始终要求外部 `spec` 维持不变式。
- 这使 Spec 的作用不再只是“起手把需求说清楚”，而是贯穿整轮执行：先定义本轮只做什么，再在暂停或完成时把真实结果回写，保证下一轮不是从模糊印象继续。
- 从这个角度看，`Spec-driven Development` 真正反对的不是“没写文档”，而是让模型在没有阶段边界、没有证据门槛、没有恢复点的情况下直接展开实现。


## 新增视角：Spec-First 是降低意图转化链损耗

- 新摄入的 Agentic Engineering 资料把软件工程描述成“人类意图 → 自然语言需求 → 结构化设计 → 形式化代码 → 可执行程序”的意图转化链。Spec 的位置因此更清楚：它不是文档仪式，而是尽早捕获意图、约束和验收标准的校验点。
- 这也解释了为什么 AI 不应只在编码阶段介入。若需求澄清和设计阶段的损耗已经进入下游，编码阶段再快也可能只是“高效地做错事”。
- 资料还给出一个分工判断：需求阶段的 AI 更像引导者，通过结构化提问帮助人类把模糊意图显式化；设计阶段更像协作者；到编码和测试阶段才更像执行者。
- 因此 Spec-driven Development 的核心不是强制重流程，而是根据任务复杂度决定 spec 深度：简单局部任务可以轻量处理，跨模块、涉及设计决策的复杂任务则必须先补齐目标、约束和验证方式。

## 与其他概念的关系

- [Agentic Engineering](agentic-engineering.md) 把 Spec-First 放进意图转化链，说明早期规格化是降低后续返工成本的关键。
- [Context Engineering](context-engineering.md) 解决“给 Agent 看什么”。
- [Prompt Engineering](prompt-engineering.md) 解决“怎么把任务表达给模型听”。
- Spec-driven Development 解决“Agent 到底应该交付什么”，以及“哪些调用规格值得被独立命名和管理”。
- [Harness Engineering](harness-engineering.md) 解决“Agent 如何长期保持不偏航”。
- [Jagged Intelligence](jagged-intelligence.md) 说明仅凭“模型看起来很聪明”不足以保证它能自动猜中人的隐含意图。

## 开放问题

- 当前知识库还没有沉淀统一的摄入 spec 模板，后续可根据实际任务再抽象。
- 多 Agent 协作里，什么粒度的 `spec` 才足以替代“共享半成品代码”作为主要交接物，当前仍缺更直接的一手证据。

## 来源

- [拥抱 AI 这一年：我的工具、实践和思考](../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md)
- [从玩具到生产力：用真实项目讲透 AI Agent 的 Harness Engineering](../sources/2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md)
- [Prompt 工程实战指南：从 Prompt 硬编码到可控系统](../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md)
- [面向“Token 亿万富翁”的极限 Harness Engineering：100 万行代码、每天 10 亿 To...](../sources/2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md)
- [从第一性原理思考 Agentic Engineering](../sources/2026-04-23-从第一性原理思考-agentic-engineering.md)
