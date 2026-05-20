---
title: Prompt Engineering
type: concept
status: active
sources:
  - ../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md
  - ../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md
updated: 2026-04-16
---

Prompt Engineering 关注的是如何把任务、边界和输出要求写成模型一次调用或一类模板能够理解的指令结构。它是与模型交互最靠近的一层，但在系统变复杂后，Prompt 不应再被当作孤立字符串，而应被视为模板、变量、评测和调用规格的一部分。

## 定义

在当前知识库的语境里，Prompt Engineering 的核心对象仍然是“指令表达”本身：角色设定、任务描述、示例、输出格式、局部约束和变量插槽如何影响结果。但新摄入的资料说明，真正稳定的实践不是反复雕刻一条神奇 Prompt，而是把 Prompt 从代码里剥离出来，变成可编辑、可渲染、可评测、可回滚的系统资产。

## 纵向脉络

- 它通常是 Agent 方法链里最早暴露出来的问题层，因为最初系统往往只是“一次调用 + 一段字符串”。
- 当场景开始增多，Prompt 会先演化成模板和变量系统，再进一步外接 `golden case`、路由与 request spec。
- 再往后，Prompt 的局部优化会被吸收到更大的 [Context Engineering](context-engineering.md)、[Spec-driven Development](spec-driven-development.md) 和 [Harness Engineering](harness-engineering.md) 体系中。

## 横向位置

- Prompt Engineering 处理“怎么表达给模型听”。
- [Context Engineering](context-engineering.md) 处理“模型在这一轮到底看到了什么”。
- [Spec-driven Development](spec-driven-development.md) 处理“这条调用或这次任务的契约如何被命名和管理”。
- [Harness Engineering](harness-engineering.md) 处理“即使 Prompt 写得还不错，系统如何继续防回归、防漂移和持续收敛”。

## 与其他概念的关系

- Prompt 是很多系统的入口层，但不再是唯一控制点。
- 多模板路由说明，一个产品往往不是维护“一个 Prompt”，而是维护一组 Prompt 资产。
- request spec 说明 Prompt 不能脱离模型和参数单独比较。
- 信息分层问题则提醒：即使 Prompt 本身写得清楚，如果底层上下文组织杂乱，模型仍会被低质量信息拖垮，见 [信息分层设计](information-layering-design.md)。

## 开放问题

- 当前知识库还没有单独沉淀“Prompt 评测”或“Prompt 路由”的概念页；若相关来源继续增加，后续可能需要拆分。
- 不同模型需要多大程度的 Prompt 分叉，目前仍主要来自经验判断，缺少统一准则。

## 来源

- [拥抱 AI 这一年：我的工具、实践和思考](../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md)
- [Prompt 工程实战指南：从 Prompt 硬编码到可控系统](../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md)
