---
title: 一文讲透：Harness Engineering 即控制论！
type: source
status: summarized
sources:
  - ../../raw/sources/%E4%B8%80%E6%96%87%E8%AE%B2%E9%80%8F%EF%BC%9AHarness%20Engineering%20%E5%8D%B3%E6%8E%A7%E5%88%B6%E8%AE%BA%EF%BC%81.md
source_date: 2026-04-17
source_date_basis: published
published: 2026-04-17
updated: 2026-04-24
---

这是一篇把 Harness Engineering 明确映射到控制论框架的解释性文章。它最有长期价值的部分，不是“控制论”这个比喻本身，而是把 Harness 压回到更具体的闭环结构：目标、控制器、被控系统、传感器与反馈。

## 这份资料是什么

- 资料来自微信公众号长文摘录，结合 OpenAI 的 Harness Engineering 文章和控制论框架做二次解释。
- 内容包含作者自己的工程推演，因此更适合作为工作定义和分析框架，而不是严格一手规范。
- 文章覆盖规则沉淀、架构约束、环境设计、反馈闭环和 AI 时代程序员角色变化。

## 主要观点

1. Harness Engineering 可以被理解为软件工程场景中的控制论实现：人类定义目标、设计环境和反馈回路，Agent 执行具体动作。
2. 仓库是 AI 唯一能稳定感知的世界，因此规则、架构约束和团队共识必须显式沉淀为版本化资产。
3. Harness 的作用之一是缩小代码生成的可能性空间，让 Agent 在更窄、更可控的解空间内行动。
4. 验证工具、日志、监控、流水线和业务规则检查，可以统一理解为传感器；没有传感器，就没有真正闭环。
5. 大任务分解和多轮反馈之所以有效，不只是“更容易”，而是在积累控制力。

## 关键内容

### 控制论映射

- 文章把需求视为目标，把 AI 视为控制器，把代码仓库视为被控系统，把 QA 校验工具视为传感器。
- 这套映射的价值在于提醒我们：Agent 出错时，不一定是“模型不够强”，更可能是目标、环境或反馈回路设计不足。

### 显式规则与可能性空间

- 资料延续了“给目录而不是整本说明书”“规则要沉淀到仓库”“务必要有架构约束”等实践判断。
- 作者进一步用“可能性空间”解释为什么框架、规范和任务分解会提高稳定性：它们在持续收缩 Agent 可落到的实现空间。

### 传感器与反馈

- 文章把传感器分成业务无关和业务相关两类：前者是语法、规范、安全、部署类校验，后者是带业务约束的验收逻辑。
- 这有助于把“测试、日志、监控、审查意见回写”为同一套反馈系统，而不是互不相干的附属流程。

## 横向线索

- 对 [Harness Engineering](../concepts/harness-engineering.md) 而言，这篇资料补强了“约束 + 反馈 + 清理”之外的控制回路语言。
- 对 [Spec-driven Development](../concepts/spec-driven-development.md) 而言，它提醒“目标定义”本身就是控制闭环的第一步。

## 受影响页面

- [Harness Engineering](../concepts/harness-engineering.md)
- [Spec-driven Development](../concepts/spec-driven-development.md)

## 来源

- [原始摘录](../../raw/sources/%E4%B8%80%E6%96%87%E8%AE%B2%E9%80%8F%EF%BC%9AHarness%20Engineering%20%E5%8D%B3%E6%8E%A7%E5%88%B6%E8%AE%BA%EF%BC%81.md)
