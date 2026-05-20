---
title: "Systems Engineering: Building Agentic Software That Works系统工程：构建能工作的代理软件"
source: "https://x.com/ashpreetbedi/status/2041568919085854847?s=46"
author:
  - "[[@ashpreetbedi]]"
published: 2026-04-08
created: 2026-04-26
description: "In the early 1940s, Bell Labs was building the national telephone network, the most complex technical system in the world at the time. Milli..."
tags:
  - "clippings"
---
![图像](https://pbs.twimg.com/media/HFUUjU_WwAA4H2P?format=png&name=large)

In the early 1940s, Bell Labs was building the national telephone network, the most complex technical system in the world at the time. Millions of switches, cables, relays, and operators had to work together. The engineers discovered something that would become an 80-year-old lesson: you can't optimize a system by optimizing individual components. The behavior of the whole (call routing, reliability, capacity, cost) emerged from how the parts interacted. They needed a discipline focused on the interactions between components.20 世纪 40 年代初，贝尔实验室正在构建国家电话网络，这是当时世界上最复杂的技术系统。数以百万计的交换机、电缆、中继器和操作员必须协同工作。工程师们发现了一个 80 年来一直存在的教训：通过优化单个部件来优化系统是行不通的。整个系统的行为（呼叫路由、可靠性、容量、成本）源于各个部件如何相互作用。他们需要一种以部件相互作用为重点的纪律。

They called it systems engineering.他们称之为系统工程。

The agentic software ecosystem is repeating the same mistakes that spawned that discipline. The current wave of harness engineering will ask you to use the filesystem for storage and memory, then try to get around its limitations by building a virtualized file system over a database. It'll ask you to use bash as a general-purpose tool, then force you into per-request sandboxes to handle security. These are symptoms of optimizing one part without considering the system as a whole. And we're buying into it without taking a minute to ask if it's the right approach.代理软件生态系统正在重复导致这一学科的错误。当前的利用工程浪潮将要求你使用文件系统作为存储和内存，然后尝试通过在数据库上构建虚拟化文件系统来绕过其局限性。它将要求你使用 bash 作为通用工具，然后强制你使用单次请求沙箱来处理安全问题。这些都是没有考虑整个系统而优化单个部分的症状。我们购买它，没有花一分钟询问是否是正确的做法。

# Software Engineering Is Systems Engineering软件工程就是系统工程

Coding agents have lowered the barrier to writing code, but they haven't lowered the requirements of production software. Software engineering is, and has always been, systems engineering and if you're building agentic software, your system needs to bridge these five layers:编码代理降低了编写代码的门槛，但并没有降低生产软件的要求。软件工程是系统工程，而且一直如此，如果你正在构建代理软件，你的系统需要跨越这五个层：

**1\. Agent Engineering**: Your agent or multi-agent logic and execution flow. **1.代理工程** : 你的代理或多代理逻辑和执行流程。

Model, system instructions, tool configurations, handoffs, context management, observability. This is where you define what your agent does, how it runs and how it responds. Your agent's behavior should be deterministic where possible and observable where it isn't.模型、系统指令、工具配置、手柄、上下文管理、可观察性。这是你定义代理做什么、如何运行以及如何响应的地方。在可能的情况下，代理的行为应该是确定性的，在不是确定性的地方，代理的行为应该是可观察的。

**2\. Data Engineering**: Your agent is only as good as the context it has access to, and context is just data under the hood. Memory, storage, knowledge: all should be managed with data engineering principles. This means well designed schemas, structured querying, databases for fast read/writes, object storage for long-term storage, and pipelines that keep your knowledge and memory up to date. The patterns are decades old. Use them.**2.数据工程** : 你的代理只有它所访问的上下文那么好，而上下文就是数据的内部。内存、存储、知识：所有这些都应该用数据工程原则来管理。这意味着设计良好的模式、结构化的查询、用于快速读/写操作的数据库、用于长期存储的对象存储，以及不断更新你的知识和记忆的管道。这些模式已有几十年的历史。使用它们。

**3\. Security Engineering**: Auth, RBAC, governance, data isolation, audit trails. **3\. 安全工程** : 身份验证、RBAC、治理、数据隔离、审计日志。

Your agent's capabilities are defined by its tools, and those tools should be scoped with JWT-backed permissions. Read-only access IS NOT a prompt instruction, it's a tool configuration. Actions should have approval tiers: reads run freely, writes need user approval, sensitive operations need admin sign-off. Most actions should be logged and queryable for the life of the product. 你的代理的能力是由其工具定义的，而这些工具应该与基于 JWT 的权限进行 scoped。只读访问并不是一个提示指令，而是一个工具配置。操作应该有审批级别：读取是自由的，写入需要用户批准，敏感操作需要管理员签名。大多数操作应该在整个产品生命周期内被记录和查询。

And please, isolate requests. One user's context bleeding into another's is a data breach, not a bug. It has serious consequences and there are laws protecting user data. So filesystem backed memory on a shared sandbox might not be a good idea.请隔离请求。一个用户的数据泄露到另一个用户的数据中，这不就是数据泄露，而是一个 bug。这会造成严重的后果，而且有法律保护用户数据。因此，共享沙盒上的文件系统支持内存可能不是一个好主意。

**4\. Interface Engineering**: How users and other agents reach your agent.

REST API, Slack, MCP server, terminal. In the old world, you had one API and one client. Now you have multiple surfaces, each with its own identity system. A Slack user ID is not your product's user ID. An MCP client authenticating as another agent is not a human user. Interface engineering is about making sure your auth, policies, and access controls hold consistently across every surface your agent is reachable from.

**5\. Infrastructure Engineering**: How you run and scale your software. Containers, cloud deployment, horizontal scaling. Generally called DevOps.

The good news: 95% of this is identical to running any other service. Re-use existing patterns, they'll serve you well. The 5% that's different: agent requests take longer (increase your load balancer timeouts), responses stream (plan for SSE or WebSockets), and the best agents are proactive (scheduled tasks, background execution). None of this is new.

The key unlock for AI engineers is realizing that agentic software is just regular software, with the business logic replaced by agents, and interfaces going from request/response to streaming across multiple surfaces.

Systems engineering is the discipline of making these parts work together, and is the key to building agentic software that works.

When you look at your software from a systems perspective, the right decisions become obvious and you stop debating MCP vs CLI. You give your agent well-scoped tools, not unfettered bash access. You store sessions, memory, and knowledge in a database, not files.

When you design one layer in isolation, you inherit constraints that cascade through the rest of the system and you waste time and resources patching those constraints. When you design from the system's perspective, each layer reinforces the others.

# Systems Engineering in Practice

I can't make a claim this big and not give you working code.

[Dash is an open-source, self-learning data agent](https://github.com/agno-agi/dash). You ask it questions in plain English, it writes SQL, runs it, and tells you what the numbers mean. Simple enough to clone and adapt. Real enough to demonstrate all five layers.

Here's how each layer works in dash:

## Agent Engineering

Dash is a team of agents. A Leader routes requests to two specialists: an Analyst that queries data (read-only) and an Engineer that builds computed assets like views and summary tables. Each specialist gets the same tool types wired to different capabilities. The Analyst's SQL tools connect to a read-only database engine. The Engineer's SQL tools connect to a writable engine scoped to a single schema. Same interface, different permissions, determined by configuration, not prompts. Instructions are assembled at runtime from table metadata and business rules.

## Data Engineering

**Six layers of context, and tools for learning.**

Raw LLMs writing SQL hit a wall fast: schemas lack meaning, types are misleading, tribal knowledge is missing, there's no way to learn from mistakes. Dash solves this with six layers of grounded context:

- Table metadata (schema, columns, relationships)
- Human annotations (metrics, definitions, business rules)
- Query patterns (SQL that is known to work)
- Institutional knowledge (docs, wikis)
- Learnings (error patterns and discovered fixes)
- Runtime context (live schema inspection).

These layers feed two systems:

- The first is curated knowledge: table schemas, validated queries, and business rules loaded into PostgreSQL.
- The second is discovered learnings: error patterns and fixes that the agent saves when it hits problems and recalls on future queries.

The learning loop is simple: the agent runs a query, gets a type error, diagnoses the fix, saves it. Next time it sees a similar column, it gets it right the first time. And when the Engineer creates a new view, it records the schema and example queries into the knowledge base. The Analyst discovers it on the next search and starts using it. Query 100 is better than query 1, not because the model improved, but because the data layer got better.

## Security Engineering

**Enforced by the system, not the prompt.**

Dash in production uses RBAC with JWT verification. Every query is scoped to \`user\_id\`. An eval suite tests these boundaries directly: it prompts the agents to leak credentials, execute destructive SQL, and cross schema boundaries, then verifies they can't.

Security is a system property tested across layers. The Analyst's read-only access is a PostgreSQL connection parameter. The database itself rejects writes regardless of what the model generates. The Engineer can write, but only to a single schema: a query-level guard blocks any operation targeting the source data.

## Interface Engineering

Dash is available as a REST API, a Slack bot, a web UI, and a CLI. Each surface handles identity differently: Slack maps thread timestamps to sessions, the API uses JWT tokens in production. But all four hit the same agents, same tools, same knowledge.

Your auth and access controls need to hold across every surface, because the agent doesn't know which one it's being called from.

## Infrastructure Engineering

Minimal python container. Docker compose for local development. Deploy to your cloud of choice. Streaming via SSE through a standard ASGI server. The 95% that's identical to any other service is identical. The 5% that's different (longer timeouts, streaming, scheduled tasks) is handled with standard tools.

You can clone it, run \`docker compose up\`, and have the entire system.

One command, five layers, a working product thanks to systems engineering.

Here's the link for reference: [https://github.com/agno-agi/dash](https://github.com/agno-agi/dash)