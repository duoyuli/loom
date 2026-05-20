---
title: Prompt 工程实战指南：从 Prompt 硬编码到可控系统
type: source
status: summarized
sources:
  - ../../raw/sources/Prompt%20%E5%B7%A5%E7%A8%8B%E5%AE%9E%E6%88%98%E6%8C%87%E5%8D%97%EF%BC%9A%E4%BB%8E%20Prompt%20%E7%A1%AC%E7%BC%96%E7%A0%81%E5%88%B0%E5%8F%AF%E6%8E%A7%E7%B3%BB%E7%BB%9F.md
source_date: 2026-04-16
source_date_basis: created
updated: 2026-04-16
---

这是一篇面向产品落地的 Prompt 工程实践指南。它最值得长期保留的判断不是某条提示词技巧，而是：当 LLM 系统从 demo 走向可维护产品后，Prompt 不应再被看作一段魔法字符串，而应被纳入模板、评测、路由、请求规格和自动优化组成的工程闭环中。

## 这份资料是什么

作者用一个 “YouTube Summary” 小产品作为例子，拆解团队如何从代码里硬编码一条长 Prompt，逐步演化到模板化、golden case 回归、分类路由、`spec` 管理和半自动优化。

## 主要观点

1. Prompt 工程的难点不在“一次写对”，而在围绕高敏感、近似混沌的 LLM 系统建立可迭代、可回滚、可验证的控制机制。
2. Prompt 一旦从代码字符串升级为模板文件和 CLI 资产，就不再只属于工程师，也能被产品、内容等角色直接参与迭代。
3. `golden case + 自动评估` 是 Prompt 系统第一次获得回归安全网的关键步骤，否则每次改动都只是“凭感觉开盲盒”。
4. 多场景产品不适合把所有规则堆进一条超大 Prompt；先做分类，再选对应模板，通常比继续膨胀总 Prompt 更稳。
5. `Prompt + 模型 + 参数` 应绑定成一个最小请求规格（request spec），这样评估、A/B、回滚和自动搜索才有统一单位。
6. 自动优化只有在模板体系、评估指标和 spec 边界都清晰之后才值得做；否则只是把混乱更快地放大。

## 关键内容

### 六阶段演化路径

- 阶段 1：把长 Prompt 直接写死在代码里，先验证“能不能跑”。
- 阶段 2：把 Prompt 抽成模板文件，并提供本地渲染与调用 CLI，让 Prompt 变成独立资产。
- 阶段 3：用 `golden case`、离线回归和线上反馈回流，让 Prompt 变更具备可量化安全网。
- 阶段 4：把超大 Prompt 拆成 “分类 + 多模板路由”，让每次请求只携带和当前场景相关的指令。
- 阶段 5：用 YAML 或类似配置把 `Prompt + 模型 + 参数` 绑成 request spec，作为统一比较与管理单位。
- 阶段 6：在 spec 空间内做 A/B、调参和自动化优化，把 Prompt 调整从纯手工推进到半自动搜索。

### 对 Prompt 工程定义的收缩

- 资料并不否认“写好指令”很重要，但它强调 Prompt Engineering 不能停留在文案技巧。
- 真正稳定的工程对象，至少包括模板边界、变量输入、评估样本、路由策略和回滚单位。
- 这让 Prompt Engineering 成为方法链的入口层，而不是终点。

### Request Spec 的长期价值

- 文中最值得沉淀的新对象，是 `request spec`：一份 YAML 描述一条调用路径使用哪份 Prompt、哪个模型和哪些默认参数。
- 这使“线上到底在跑什么组合”第一次能被明确命名、对比和回滚。
- 资料也明确接受一个现实：不存在一条 Prompt 能自然迁移到所有模型，因此 spec 应天然允许模型级模板分叉。

## 纵向线索

- 文章把 Prompt 系统的成熟过程写成一条从“单字符串原型”到“可控调用单元”的演化链。
- 这条链并不跳过 Prompt，而是先把 Prompt 独立成资产，再逐步外接评测、路由和参数治理。
- 它也把自动优化摆在最后，强调任何搜索都必须建立在已存在的 spec 边界和评估体系之上。

## 横向线索

- 这份资料可以和 [Prompt Engineering](../concepts/prompt-engineering.md)、[Spec-driven Development](../concepts/spec-driven-development.md) 与 [Harness Engineering](../concepts/harness-engineering.md) 对照阅读。
- 其中 `golden case`、回归测试和线上差评回流，适合继续与当前知识库中的 Trace、评测、反馈机制材料互证。
- 文中提到 DSPy 作为自动优化方向的参照，但这里只保留为线索，不把它直接等同于唯一的 spec 框架。

## 与知识库的相关性

这份资料补上了当前知识库里一直缺席但反复被提及的入口概念：`Prompt Engineering`。它同时也把已有的 [Context Engineering](../concepts/context-engineering.md)、[Spec-driven Development](../concepts/spec-driven-development.md) 和 [Harness Engineering](../concepts/harness-engineering.md) 拉回到了更靠近产品落地的演化序列里。

对本库最有价值的稳定主题是：

- [Prompt Engineering](../concepts/prompt-engineering.md)
- [Spec-driven Development](../concepts/spec-driven-development.md)
- [Harness Engineering](../concepts/harness-engineering.md)
- [Context Engineering](../concepts/context-engineering.md)
- [Agent 工作流分层框架](../analyses/agentic-workflow-stack.md)

## 可靠性判断

- 这是一篇高信息密度的工程经验总结，适合沉淀为方法演化和系统设计线索。
- 文中的代码、阶段划分和示例产品主要是教学化表达，不应误读为某个生产系统的完整实现细节。
- 文中的六阶段路径更像高质量工作地图，而不是所有团队都会严格按顺序经历的通用成熟度模型。
- 资料提到 DSPy，但没有深入讨论其约束条件和失败模式；若后续要直接使用，应单独摄入一手材料。

## 受影响页面

- [Prompt Engineering](../concepts/prompt-engineering.md)
- [Spec-driven Development](../concepts/spec-driven-development.md)
- [Harness Engineering](../concepts/harness-engineering.md)
- [Context Engineering](../concepts/context-engineering.md)
- [Agent 工作流分层框架](../analyses/agentic-workflow-stack.md)

## 开放问题

- request spec 是否值得继续拆成独立概念页，还是继续作为 `Spec-driven Development` 的运行时形态维护。
- 当前知识库还没有沉淀一页专门讨论 `golden case / judge / regression` 的评测结构，后续若相关来源继续变多，可能需要单独立页。
- 文中默认的分类路由适合“场景差异明显”的任务，但何时从单模板升级为多模板路由，仍缺少更明确的判断标准。

## 来源

- [原始摘录](../../raw/sources/Prompt%20%E5%B7%A5%E7%A8%8B%E5%AE%9E%E6%88%98%E6%8C%87%E5%8D%97%EF%BC%9A%E4%BB%8E%20Prompt%20%E7%A1%AC%E7%BC%96%E7%A0%81%E5%88%B0%E5%8F%AF%E6%8E%A7%E7%B3%BB%E7%BB%9F.md)
- [原文链接](https://mp.weixin.qq.com/s/a_Jxm5NsErP4RYx8DaxYqQ)
