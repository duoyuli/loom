---
title: Agent 学习闭环
type: concept
status: active
sources:
  - ../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md
  - ../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md
  - ../sources/2026-04-23-从第一性原理思考-agentic-engineering.md
updated: 2026-04-24
---

“让 Agent 替我学习”描述的是一种采集、提炼、应用三段式知识闭环：人不再亲自处理所有一手信息，而是让 Agent 先筛选和压缩，再把结果回灌到未来工作流中。

## 定义

这页更关注“知识如何持续进入工作流”的循环结构，而不是单次调研或单次生成是否成功。闭环是否成立，取决于新资料能否被压缩成可复用规则，并稳定影响后续任务执行。

新摄入的 Hermes `Skills` 资料把这条闭环压得更细：复杂任务或棘手错误会触发 Skill 创建，Skill 以 `YAML + Markdown` 形成可检索 SOP，再通过索引发现、条件激活和 `skill_view` 按需进入当前任务；若执行中发现遗漏步骤或命令过时，Agent 被要求在完成任务前立即 patch。这里的“学习”因此更接近程序性知识资产的持续维护，而不是泛泛的“收集更多资料”。

## 结构

### 采集层

- 每日资讯扫描
- 网页采集与清洗
- 播客转录与分析
- 指定主题深度调研

### 提炼层

- 从新闻、播客、网页中抽取可复用最佳实践
- 去重、评分、筛选高价值模式

### 应用层

- 让提炼结果反哺工作空间配置
- 生成文档、脚本、快捷指令、测试等可执行产物

## 这份资料中的实现特点

- 作者把 `Skills` 和 `multi sub-agent` 作为主要组织方式。
- 闭环的关键不是“收集更多”，而是让新知识能影响后续任务执行。
- 这是一种明显偏向实践收益的学习策略：快速沉淀和验证，比完整理解一切更重要。
- 原文给出的触发背景是“古法学习行不通了”：信息量暴涨、实践质量参差、人工逐条跟进成本过高。
- 人在闭环里并没有消失，而是退到“看提炼结果、做必要反馈与记录”的位置。
- Hermes 的实现样本进一步说明，闭环内部还要区分“稳定事实”和“可复用方法”两类长期状态：前者更接近 Memory，后者更适合 Skill。
- Hermes 里的 patch 成功后不会回改当前会话的 system prompt，而是通过清缓存让新版本从下一次对话起生效，说明学习闭环还会受缓存一致性和上下文边界约束。


## 新增视角：Knowledge as Code 与错误驱动回写

- 新摄入的 Agentic Engineering 资料把学习闭环从“让 Agent 替我学习”推进到更严格的知识治理：团队共有知识应像代码一样被版本化、Review 和迭代，表现为 Rules、Skills、Standards、Best Practices 与 Docs。
- 其中 `Error-Driven Context Refinement` 尤其适合沉淀到本页：当 AI 被纠正后，不应只在当前对话里道歉重试，而应诊断根因，检查现有规则是否覆盖，必要时建议新增或更新 Rule/Skill，防止下次会话重复犯错。
- 这补上了闭环的“自下而上”来源：实践 5 更像资深经验的主动编码，实践 6 则把开发过程里的失败、纠偏和遗漏转成新上下文。
- 对本知识库而言，这与 `wiki/log.md` 和纠错流程同构：重要错误应回溯修正导致错误的页面，而不是只在最终回答里留下临时备注。

## 与知识库的关系

这个仓库可以看作该闭环中的长期记忆层之一：

- `raw/sources/` 保存不可变原始材料。
- `wiki/sources/`、`wiki/concepts/`、`wiki/analyses/` 负责把材料压缩成可复用知识。
- `wiki/index.md` 和页面间链接承担导航作用，帮助后续 Agent 更快重用已沉淀内容。
- [Agent 工作流分层框架](../analyses/agentic-workflow-stack.md) 提供了它在整套个人 AI 工作流中的上位位置。

## 与其他概念的关系

- [Agentic Engineering](agentic-engineering.md) 把学习闭环进一步写成 `Knowledge as Code` 与 `Error-Driven Context Refinement`，强调经验应回写为可执行上下文。
- [Context Engineering](context-engineering.md) 关注每一步该看什么；学习闭环则关注哪些经验值得被长期留下。
- [Spec-driven Development](spec-driven-development.md) 决定交付契约；学习闭环决定哪些新规则会被回写成未来任务的默认约束。
- [Harness Engineering](harness-engineering.md) 负责把反馈与清理机制工程化；学习闭环强调这些反馈应沉淀为长期可复用知识。
- [Jagged Intelligence](jagged-intelligence.md) 解释了为什么人仍需保留筛选、纠偏和边界判断，而不能把闭环误解为全自动代理。

## 风险与限制

- 如果来源筛选不严格，闭环会放大噪音而不是放大知识。
- 如果缺少来源追踪，后续很难回溯结论从何而来。
- 如果只更新新页面而不回写旧页面，知识库会再次碎片化。
- 如果把“快速沉淀”误解为“跳过验证”，闭环会把未验证的时髦说法快速固化进工作流。

## 开放问题

- 哪些采集与提炼动作值得在本仓库外继续自动化。
- 当来源增多后，如何识别已过时或已被更强证据替代的结论。

## 来源

- [拥抱 AI 这一年：我的工具、实践和思考](../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md)
- [一文搞懂Hermes：新顶流Agent如何从经验中自我进化](../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md)
- [从第一性原理思考 Agentic Engineering](../sources/2026-04-23-从第一性原理思考-agentic-engineering.md)
