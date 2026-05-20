---
title: 从《塞尔达传说》到AI Agent：Claude Skills背后的信息分层设计哲学
type: source
status: summarized
sources:
  - ../../raw/sources/%E4%BB%8E%E3%80%8A%E5%A1%9E%E5%B0%94%E8%BE%BE%E4%BC%A0%E8%AF%B4%E3%80%8B%E5%88%B0AI%20Agent%EF%BC%9AClaude%20Skills%E8%83%8C%E5%90%8E%E7%9A%84%E4%BF%A1%E6%81%AF%E5%88%86%E5%B1%82%E8%AE%BE%E8%AE%A1%E5%93%B2%E5%AD%A6.md
source_date: 2026-04-16
source_date_basis: created
updated: 2026-04-16
---

这是一篇借《塞尔达传说》的 `LOD + 按需加载` 类比 Claude Skills 的设计分析。它最有长期价值的判断不是“Markdown Skill 很优雅”，而是：当 Agent 面对大量能力、数据或文档时，应优先看元信息，再按需读取核心层，最后只在必要时深入原始层；信息层级和访问工具必须一起设计，才能真正提升 token 效率与决策质量。

## 这份资料是什么

作者围绕 Claude Skills 的目录结构与 `SKILL.md` 组织方式，提出一个“信息分层三层架构”解释：`name + description` 是全局摘要层，完整 `SKILL.md` 是核心层，被引用的扩展文档与原始资源则构成按需原始层。

## 主要观点

1. Claude Skills 的核心创新不只是“用 Markdown 写工作流”，而是把信息发现、核心说明和深层细节分层组织起来。
2. `LOD-0 / LOD-1 / LOD-2` 三层分别解决“知道它存在”“足够开始工作”“只在需要时深挖”三件不同问题。
3. 信息分层不应脱离访问工具讨论；`grep`、SQL、API、文件读取等工具会反过来决定信息该如何组织。
4. 不是所有对象都值得强行做三层；较小的信息体通常天然就是“元信息 + 完整内容”的两层系统。
5. 高质量摘要层并非免费，它需要前期构建成本与后续同步维护成本，否则会出现摘要与原始事实漂移。
6. 这套架构可以递归嵌套：一个高层资源的原始层，对更细粒度对象来说又可能是新的三层系统。

## 关键内容

### 三层信息架构

- `LOD-0`：摘要层，提供名称、一句话描述、少量元信息，帮助 Agent 低成本发现相关资源。
- `LOD-1`：核心层，提供足以完成大部分常规任务的说明、参数、样例或统计摘要。
- `LOD-2`：原始层，保留完整细节，只在当前层信息不够时通过工具精准读取。

### 两层与三层的边界

- 资料特别强调并非所有信息都必须拆成三层。
- 对可一次读完的小文件、小文档或代码片段来说，“文件名 + 文件内容”通常已足够。
- 只有当信息体大到必须依赖过滤、查询或聚合才能有效使用时，单独维护高质量核心层才真正划算。

### 工具耦合是设计的一半

- 如果主要查询工具是 SQL，就应优先让原始层保持结构化表形态。
- 如果主要查询工具是 `grep`，则信息的行协议和标题组织会显著影响检索效率。
- 因此分层设计不是纯内容工作，也是一种“为工具设计信息接口”的工程问题。

## 纵向线索

- 文章把游戏里的 `LOD` 与按需加载，翻译成 Agent 时代的信息组织原则。
- 这条线从 Claude Skills 出发，但明显可扩展到数据表、API 文档、代码仓库和企业知识系统。
- 资料最后还提出“分形”视角，说明信息分层不是单层平铺，而是可以在不同粒度上重复出现。

## 横向线索

- 这份资料可以和 [Context Engineering](../concepts/context-engineering.md) 对照阅读，因为它把“给 Agent 看什么”进一步写成了可执行的信息装载骨架。
- 也适合和 [Prompt Engineering](../concepts/prompt-engineering.md) 形成分工：Prompt 更偏如何表达任务，信息分层设计更偏如何组织长期上下文资源。
- 与此前 Agent 架构综述中提到的 `Skills` 描述符、按需加载和文件系统式动态上下文接口，存在明显互证关系。

## 与知识库的相关性

这份资料把当前知识库已经采用的“索引页 + 目录页 + 小页面 + 按链接扩展”结构，第一次明确写成了一个可复用概念： [信息分层设计](../concepts/information-layering-design.md)。它也让现有的 [Context Engineering](../concepts/context-engineering.md) 不再只停留在“装配信息”，而能进一步讨论“信息层级如何被组织、发现和按需读取”。

对本库最有价值的稳定主题是：

- [信息分层设计](../concepts/information-layering-design.md)
- [Context Engineering](../concepts/context-engineering.md)
- [Harness Engineering](../concepts/harness-engineering.md)

## 可靠性判断

- 这是一篇解释型、类比驱动的实践分析，适合沉淀为信息架构原则。
- 文中的 `95% token` 节省等数值更像观点强化与经验性表述，本页不把它当成跨系统通用的定量事实。
- 文章大量使用《塞尔达传说》的比喻来增强直觉，这有助于理解，但不应替代对 Claude Skills 具体实现和限制条件的独立验证。

## 受影响页面

- [信息分层设计](../concepts/information-layering-design.md)
- [Context Engineering](../concepts/context-engineering.md)
- [Harness Engineering](../concepts/harness-engineering.md)

## 开放问题

- Claude Skills 之外，Cursor、Codex CLI、OpenHands 等系统是否也在收敛到相似的信息分层骨架。
- 当前知识库虽然已采用多层页面结构，但还缺少明确的自动化机制去检查 `LOD-0 / LOD-1` 是否与底层内容漂移。
- “什么时候值得从两层升级到三层”仍需要更多一手案例支撑。

## 来源

- [原始摘录](../../raw/sources/%E4%BB%8E%E3%80%8A%E5%A1%9E%E5%B0%94%E8%BE%BE%E4%BC%A0%E8%AF%B4%E3%80%8B%E5%88%B0AI%20Agent%EF%BC%9AClaude%20Skills%E8%83%8C%E5%90%8E%E7%9A%84%E4%BF%A1%E6%81%AF%E5%88%86%E5%B1%82%E8%AE%BE%E8%AE%A1%E5%93%B2%E5%AD%A6.md)
- [原文链接](https://mp.weixin.qq.com/s/F9BtGpZNQxLkalg62ayBXw)
