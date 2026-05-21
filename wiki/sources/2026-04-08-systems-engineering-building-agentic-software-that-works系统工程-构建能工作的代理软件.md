---
title: "Systems Engineering: Building Agentic Software That Works系统工程：构建能工作的代理软件"
type: source
status: summarized
sources:
  - ../../raw/sources/Systems%20Engineering%20Building%20Agentic%20Software%20That%20Works%E7%B3%BB%E7%BB%9F%E5%B7%A5%E7%A8%8B%EF%BC%9A%E6%9E%84%E5%BB%BA%E8%83%BD%E5%B7%A5%E4%BD%9C%E7%9A%84%E4%BB%A3%E7%90%86%E8%BD%AF%E4%BB%B6.md
source_date: 2026-04-08
source_date_basis: published
published: 2026-04-08
updated: 2026-05-21
---

这是一篇把 agentic software 重新放回系统工程视角的长线程摘录。它对知识库的长期价值，是把“Agent 工程”从单个 harness、工具或 prompt 讨论，扩展为跨 Agent、数据、安全、接口和基础设施五层协同的生产系统问题。

## 这份资料是什么

- 资料来自 Ashpreet Bedi 的 X thread，发布日期为 2026-04-08。
- 文章用 Bell Labs 电话网络引出系统工程，再批评当前 agentic software 生态容易局部优化：用文件系统做记忆、用 bash 做万能工具、再用沙箱和虚拟文件系统弥补系统性问题。
- 文末用开源数据 Agent 项目 Dash 作为案例，说明五层系统如何同时落地。

## 主要观点

1. Agentic software 仍然是软件工程，不能因为业务逻辑换成 Agent，就绕过数据、安全、接口和基础设施工程。
2. 生产级系统需要同时跨越五层：`Agent Engineering / Data Engineering / Security Engineering / Interface Engineering / Infrastructure Engineering`。
3. 很多 Harness 争论其实是系统边界问题：文件系统记忆、无限 bash、单请求沙箱等做法如果孤立优化，会把约束转嫁给其他层。
4. Agent 的权限不应靠 prompt 声明，而应靠工具配置、数据库权限、RBAC、JWT、审批分级和审计日志强制执行。
5. 多入口系统必须统一身份与权限语义；Slack、REST、Web UI、CLI、MCP client 进入同一 Agent 时，不能让访问控制随入口漂移。

## 关键内容

### 五层系统工程

- `Agent Engineering`：模型、系统指令、工具配置、handoff、上下文管理和可观测性，定义 Agent 做什么、如何运行、如何响应。
- `Data Engineering`：记忆、存储、知识和上下文都应被当成数据管理问题，使用 schema、结构化查询、数据库、对象存储和更新管道。
- `Security Engineering`：认证、RBAC、治理、数据隔离和审计日志必须由系统强制，不应只写进提示词。
- `Interface Engineering`：REST、Slack、MCP、终端等入口有不同身份系统，必须收敛到一致的策略和权限模型。
- `Infrastructure Engineering`：大部分仍是普通服务工程，但 agent 请求更长、响应常流式、任务可能后台执行，需要相应调整超时、SSE/WebSocket 和调度。

### Dash 案例

- Dash 被描述为一个开源、自学习的数据 Agent，用户用自然语言提问，系统生成 SQL、运行查询并解释结果。
- 它把 Agent 拆成 Leader、Analyst 和 Engineer：Analyst 只连只读数据库，Engineer 只能写入受限 schema；同类 SQL 工具通过配置获得不同权限。
- 数据层提供六类上下文：表元数据、人类注释、查询模式、机构知识、错误学习、运行时 schema 检查。
- 学习闭环不是模型权重变强，而是数据层保存错误模式、修复方式、新视图 schema 和示例查询，让下一次检索更好。

### 安全与接口不是附加层

- 文章特别强调：只读访问不是 prompt instruction，而是工具和数据库连接配置。
- 写操作、敏感操作和跨 schema 行为应由审批、guard 和数据库自身阻断。
- Eval suite 也应直接测试越权、破坏性 SQL、跨 schema 泄漏等边界，而不是只看回答质量。
- 多入口调用同一 Agent 时，Slack 用户、产品用户和 MCP agent 身份不能混同。

## 纵向线索

- 相比只讨论 `Prompt / Context / Harness` 的资料，这篇来源把 agentic software 推回更传统也更严格的软件系统工程脉络：接口、数据、安全和基础设施不是 Agent 外围，而是 Agent 是否能生产化的前提。
- 它也与 Agentic Engineering 的第一性原理来源形成互补：前者偏人机协作与知识治理，本文偏生产系统的层级边界与权限落地。

## 横向线索

- 对 [Agentic Engineering](../concepts/agentic-engineering.md) 而言，这篇来源补入更偏生产架构的五层模型，防止把方法论只理解成协作流程。
- 对 [Harness Engineering](../concepts/harness-engineering.md) 而言，它强调 Harness 的边界不能吞掉数据、安全、接口和基础设施职责；这些层之间需要显式分工。
- 对 [Agent 记忆系统](../concepts/agent-memory-systems.md) 而言，它提供了“记忆就是数据工程”的路线：schema、数据库、对象存储、查询管道和学习记录。
- 对 [Context Engineering](../concepts/context-engineering.md) 而言，它把上下文来源从 prompt 文件扩展到元数据、业务规则、历史查询、机构知识和运行时检查。

## 与知识库的相关性

这份来源直接补强当前知识库的一个空白：过去很多页面已经在讨论 Agent 的 prompt、context、harness、memory 和 session，但还缺一个把这些能力放进生产软件系统的五层视角。它适合先回写到现有概念页，而不是立刻新建“系统工程”弱页。

## 可靠性判断

- 作为工程观点和案例来源，文章的分层清晰，适合作为 Agentic Engineering 与 Harness Engineering 的补充视角。
- 它包含作者对 Dash 的案例介绍和生产架构主张，后续若要依赖 Dash 的具体实现，应单独摄入 GitHub 仓库或官方文档。
- “文件系统记忆不适合共享沙箱”等判断具有明确工程方向，但仍需根据具体部署隔离、权限模型和数据生命周期判断，不应机械套用。

## 受影响页面

- [Agentic Engineering](../concepts/agentic-engineering.md)
- [Harness Engineering](../concepts/harness-engineering.md)
- [Agent 记忆系统](../concepts/agent-memory-systems.md)
- [Context Engineering](../concepts/context-engineering.md)
- [Agent 工作流分层框架](../analyses/agentic-workflow-stack.md)

## 开放问题

- 是否需要在更多来源支撑后，把 `Agent / Data / Security / Interface / Infrastructure` 五层模型整理成 synthesis 分析页。
- Dash 是否值得作为独立项目实体摄入，取决于后续是否需要围绕数据 Agent 做对象级比较。

## 来源

- [原始摘录](../../raw/sources/Systems%20Engineering%20Building%20Agentic%20Software%20That%20Works%E7%B3%BB%E7%BB%9F%E5%B7%A5%E7%A8%8B%EF%BC%9A%E6%9E%84%E5%BB%BA%E8%83%BD%E5%B7%A5%E4%BD%9C%E7%9A%84%E4%BB%A3%E7%90%86%E8%BD%AF%E4%BB%B6.md)
- [原文链接](https://x.com/ashpreetbedi/status/2041568919085854847?s=46)
