---
title: "AI Search：为你的智能体打造的搜索原语"
source: "https://www.bestblogs.dev/article/0ad4ddf2?entry=resource_card&from=%2Fexplore%3Ftype%3Darticle%26page%3D6"
author:
  - "[[Anni Wang]]"
published: 2026-04-16
created: 2026-04-17
description: "Cloudflare AI Search 是一个专为 AI 智能体设计的全托管、即插即用搜索服务，具备混合搜索、内置存储/索引、动态实例创建和跨实例查询功能，旨在简化智能体 RAG 工作流。"
tags:
  - "clippings"
---
92

⭐ 精选内容

## AI Search: the search primitive for your agents

Cloudflare AI Search 是一个专为 AI 智能体设计的全托管、即插即用搜索服务，具备混合搜索、内置存储/索引、动态实例创建和跨实例查询功能，旨在简化智能体 RAG 工作流。

[The Cloudflare Blog](https://www.bestblogs.dev/articles?sourceid=903ae3 "查看该来源的更多文章")

昨天2526 字 (约 11 分钟)

[查看原文 →](https://blog.cloudflare.com/ai-search-agent-primitive/)

### 摘要

本文介绍了 Cloudflare AI Search，这是一个专为 AI 智能体构建的综合性搜索服务。它解决了智能体从各种数据源（如代码仓库、支持工单、客户历史记录）中检索相关信息这一核心需求，同时抽象掉了管理向量数据库、索引管道和关键词搜索融合的复杂性。主要功能包括混合搜索（语义向量 + BM25 关键词）、每个实例内置的存储和向量索引、通过新的 `ai_search_namespaces` 绑定在运行时动态创建和管理搜索实例的能力，以及跨实例搜索。文章提供了一个构建客户支持智能体的详细实践示例，该智能体可以搜索共享的产品文档和每个客户的解决历史记录。文章还解释了底层的检索管道、分词器和融合方法的配置选项，以及公开测试版的新定价模型。

### 主要内容

- 1\. AI Search 抽象了为智能体构建搜索的基础设施复杂性。
	它提供了一个全托管服务，包含内置存储、向量索引和混合搜索，无需为每个智能体上下文单独设置和维护 R2、Vectorize 和融合逻辑等独立系统。
- 2\. 通过命名空间和运行时实例创建，实现了动态的、按智能体的搜索上下文。
	新的 \`ai\_search\_namespaces\` 绑定允许开发者从 Worker 中以编程方式创建、删除和管理隔离的搜索实例（例如，按客户、按智能体），从而实现可扩展的多租户智能体架构。
- 3\. 混合搜索结合了向量搜索的意图理解能力和 BM25 的术语精确性。
	并行运行两种检索方法并融合结果（例如，使用 Reciprocal Rank Fusion），可以解决每种方法的弱点，确保智能体找到既语义相关又包含特定关键术语的文档。
- 4\. 跨实例搜索允许智能体同时查询多个数据孤岛。
	智能体可以在多个实例（例如，共享知识库和每个客户的历史记录）上执行一次搜索，接收一个统一的、经过排序的结果列表，无需手动合并，从而简化了智能体的工具逻辑。

Every [agent](https://www.cloudflare.com/learning/ai/what-is-agentic-ai/) needs search: Coding agents search millions of files across repos. Support agents search customer tickets and internal docs. Even an agent's memory, its ability to recall past interactions, is fundamentally a search problem. The use cases are different, but the underlying problem is the same: get the right information to the model at the right time.

If you're building search yourself, you need a vector index, an indexing pipeline that parses and chunks your documents, and something to keep the index up to date when your data changes. If you also need keyword search, that's a separate index and fusion logic on top. And if each of your agents needs its own searchable context, you're setting all of that up per agent.

[AI Search](https://developers.cloudflare.com/ai-search/) (formerly [AutoRAG](https://blog.cloudflare.com/introducing-autorag-on-cloudflare/)) is the plug-and-play search primitive you need. You can dynamically create instances, give it your data, and search — from a Worker, the Agents SDK, or Wrangler CLI. Here's what we're shipping:

- **Hybrid search**. Enable both semantic and keyword matching in the same query. Vector search and BM25 run in parallel and results are fused. (The search on our blog is now powered by AI Search. *Try the magnifying glass icon to the top right.*)
- **Built-in storage and index.** New instances come with their own storage and vector index. Upload files directly to an instance via API and they're indexed. No R2 buckets to set up, no external data sources to connect first. The new `ai_search_namespaces` binding lets you create and delete instances at runtime from your Worker, so you can spin up one per agent, per customer, or per language without redeployment.

You can now also attach metadata to documents and use it to boost rankings at query time, and query across multiple instances in a single call.

Now, let's look at what this means in practice.

## In action: Customer Support Agent

Let's walk through a support agent that searches for two kinds of knowledge: shared product docs, and per-customer history like past resolutions. The product docs are too large to fit in a context window, and each customer's history grows with every resolved issue, so the agent needs retrieval to find what's relevant.

Here's what that looks like with AI Search and the [Agents SDK](https://developers.cloudflare.com/agents). Start by scaffolding a project:

```shell
npm create cloudflare@latest -- --template cloudflare/agents-starter
```

First, bind an AI Search namespace to your Worker:

```typescript
// wrangler.jsonc 
{
  "ai_search_namespaces": [
    { "binding": "SUPPORT_KB", "namespace": "support" }
  ],
  "ai": { "binding": "AI" },
  "durable_objects": {
    "bindings": [
      { "name": "SupportAgent", "class_name": "SupportAgent" }
    ]
  }
}
```

Let's say your shared product documentation lives in an R2 bucket called `product-doc`. You can create a one-off AI Search instance (named `product-knowledge`) backed by the bucket on the Cloudflare Dashboard within the `support` namespace:

![BLOG-3240 2](https://image.jido.dev/20260416142038_BLOG-3240_2.png)

BLOG-3240 2

That's your shared knowledge base, the docs every agent can reference.

When a customer comes back with a new issue, knowing what's already been tried saves everyone time. You can track this by creating an AI Search instance per customer. After each resolved issue, the agent saves a summary of what went wrong and how it was fixed. Over time, this builds up a searchable log of past resolutions. You can create instances dynamically using the namespace binding:

```typescript
// create a per-customer instance when they first show up 
await env.SUPPORT_KB.create({
  id: \`customer-${customerId}\`,
  index_method:{ keyword: true, vector: true }
});
```

Each instance gets its own built-in storage and vector index — powered by [R2](https://www.cloudflare.com/developer-platform/products/r2/) and [Vectorize](https://www.cloudflare.com/developer-platform/products/vectorize/). The instance starts empty and accumulates context over time. Next time the customer comes back, all of it is searchable.

Here's what the namespace looks like after a few customers:

```
namespace: "support"
├── product-knowledge     (R2 as source, shared across all agents)
├── customer-abc123       (managed storage, per-customer)
├── customer-def456       (managed storage, per-customer)
└── customer-ghi789       (managed storage, per-customer)
```

Now the agent itself. It extends `AIChatAgent` from the Agents SDK and defines two tools. We're using [Kimi K2.5](https://blog.cloudflare.com/workers-ai-large-models/) as the LLM via [Workers AI](https://www.cloudflare.com/developer-platform/products/workers-ai/). The model decides when to call the tools based on the conversation:

```typescript
import { AIChatAgent, type OnChatMessageOptions } from "@cloudflare/ai-chat";
import { createWorkersAI } from "workers-ai-provider";
import { streamText, convertToModelMessages, tool, stepCountIs } from "ai";
import { routeAgentRequest } from "agents";
import { z } from "zod";

export class SupportAgent extends AIChatAgent<Env> {
  async onChatMessage(_onFinish: unknown, options?: OnChatMessageOptions) {
    // the client passes customerId in the request body
    // via the Agent SDK's sendMessage({ body: { customerId } })
    const customerId = options?.body?.customerId;

    // create a per-customer instance when they first show up.
    // each instance gets its own storage and vector index.
    if (customerId) {
      try {
        await this.env.SUPPORT_KB.create({
          id: \`customer-${customerId}\`,
          index_method: { keyword: true, vector: true }
        });
      } catch {
        // instance already exists
      }
    }

    const workersai = createWorkersAI({ binding: this.env.AI });

    const result = streamText({
      model: workersai("@cf/moonshotai/kimi-k2.5"),
      system: \`You are a support agent. Use search_knowledge_base
        to find relevant docs before answering. Search results
        include both product docs and this customer's past
        resolutions — use them to avoid repeating failed fixes
        and to recognize recurring issues. When the issue is
        resolved, call save_resolution before responding.\`,
      // this.messages is the full conversation history, automatically
      // persisted by AIChatAgent across reconnects
      messages: await convertToModelMessages(this.messages),
      tools: {
        // tool 1: search across shared product docs AND this
        // customer's past resolutions in a single call
        search_knowledge_base: tool({
          description: "Search product docs and customer history",
          inputSchema: z.object({
            query: z.string().describe("The search query"),
          }),
          execute: async ({ query }) => {
            // always search product docs;
            // include customer history if available
            const instances = ["product-knowledge"];
            if (customerId) {
              instances.push(\`customer-${customerId}\`);
            }
            return await this.env.SUPPORT_KB.search({
              query: query,
              ai_search_options: {
                // surface recent docs over older ones
                boost_by: [
                  { field: "timestamp", direction: "desc" }
                ],
                // search across both instances at once
                instance_ids: instances
              }
            });
          }
        }),

        // tool 2: after resolving an issue, the agent saves a
        // summary so future agents have full context
        save_resolution: tool({
          description:
            "Save a resolution summary after solving a customer's issue",
          inputSchema: z.object({
            filename: z.string().describe(
              "Short descriptive filename, e.g. 'billing-fix.md'"
            ),
            content: z.string().describe(
              "What the problem was, what caused it, and how it was resolved"
            ),
          }),
          execute: async ({ filename, content }) => {
            if (!customerId) return { error: "No customer ID" };
            const instance = this.env.SUPPORT_KB.get(
              \`customer-${customerId}\`
            );
            // uploadAndPoll waits until indexing is complete,
            // so the resolution is searchable before the next query
            const item = await instance.items.uploadAndPoll(
              filename, content
            );
            return { saved: true, filename, status: item.status };
          }
        }),
      },
      // cap agentic tool-use loops at 10 steps
      stopWhen: stepCountIs(10),
      abortSignal: options?.abortSignal,
    });

    return result.toUIMessageStreamResponse();
  }
}

// route requests to the SupportAgent durable object
export default {
  async fetch(request: Request, env: Env) {
    return (
      (await routeAgentRequest(request, env)) ||
      new Response("Not found", { status: 404 })
    );
  }
} satisfies ExportedHandler<Env>;
```

With this, the model decides when to search and when to save. When it searches, it queries `product-knowledge` and this customer's past resolutions together. When the issue is resolved, it saves a summary that's immediately searchable in future conversations.

## How AI Search finds what you're looking for

Under the hood, AI Search runs a multi-step retrieval pipeline, in which every step is configurable.

Until now, AI Search only offered vector search. Vector search is great at understanding intent, but it can lose specifics. In a query "ERR\_CONNECTION\_REFUSED timeout," the embedding captures the broad concept of connection failures. But the user isn't looking for general networking docs. They're looking for the specific document that mentions “ERR\_CONNECTION\_REFUSED”. Vector search might return results about troubleshooting without ever surfacing the page that contains that exact error string.

Keyword search fills that gap. AI Search now supports BM25, one of the most widely used retrieval scoring functions. BM25 scores documents by how often your query terms appear, how rare those terms are across the entire corpus, and how long the document is. It rewards matches on specific terms, penalizes common filler words, and normalizes for document length. When you search "ERR\_CONNECTION\_REFUSED timeout", BM25 finds documents that actually contain "ERR\_CONNECTION\_REFUSED" as a term. However, BM25 may miss a page about “troubleshooting network connections” even though it may be describing the same problem. That's where vector search shines, and why you need both.

When you enable hybrid search, it runs vector and BM25 in parallel, fuses the results, and optionally reranks them:

![BLOG-3240 3](https://image.jido.dev/20260416142038_BLOG-3240_3.png)

BLOG-3240 3

Let's take a look at the new configurations for BM25, and how they come together.

1. **Tokenizer** controls how your documents are broken into matchable terms at index time. Porter stemmer (option: `porter`) stems words so "running" matches "run." Trigram (option: `trigram`) matches character substrings so "conf" matches "configuration." You can use porter for natural language content like docs, and trigram for code where partial matches matter.
2. **Keyword match mode** controls which documents are candidates for BM25 scoring at query time. `AND` requires all query terms to appear in a document, OR includes anything with at least one match.
3. **Fusion** controls how vector and keyword results are combined into the final list of results during query time. Reciprocal rank fusion (option: `rrf`) merges by rank position rather than score, which avoids comparing two incompatible scoring scales, whereas max fusion (option: `max`) takes the higher score.
4. **(Optional) Reranking** adds a cross-encoder pass that re-scores results by evaluating the query and document together as a pair. It can help catch cases where a result has the right terms but isn't answering the question.

Every option has a sane default when omitted. You have the flexibility to configure what matters whenever you create a new instance:

```javascript
const instance = await env.AI_SEARCH.create({
  id: "my-instance",
  index_method: { keyword: true, vector: true },
  indexing_options: {
    keyword_tokenizer: "porter"
  },
  retrieval_options: {
    keyword_match_mode: "or"
  },
  fusion_method: "rrf",
  reranking: true,
  reranking_model: "@cf/baai/bge-reranker-base"
});
```

### Boost relevance: surface what matters

Retrieval gets you relevant results, but relevance alone isn't always enough. For example, in a news search, an article from last week and an article from three years ago might both be semantically relevant to "election results," but most users probably want the recent one. Boosting lets you layer business logic on top of retrieval by nudging rankings based on document metadata.

You can boost on timestamp (built in on every item) or any [custom metadata field](https://developers.cloudflare.com/ai-search/configuration/indexing/metadata/) you define.

```javascript
// boost high priority docs
const results = await instance.search({
  query: "deployment guide",
  ai_search_options: {
    boost_by: [
      { field: "timestamp", direction: "desc" }
    ]
  }
});
```

### Cross-instance search: query across boundaries

In the support agent example, product documentation and customer resolution history live in separate instances by design. But when the agent is answering a question, it needs context from both places at once. Without cross-instance search, you'd make two separate calls and merge the results yourself.

The namespace binding exposes a `search()` method that handles this for you. Pass an array of instance names and get one ranked list back:

```javascript
const results = await env.SUPPORT_KB.search({
  query: "billing error",
  ai_search_options: {
    instance_ids: ["product-knowledge", "customer-abc123"]
  }
});
```

Results are merged and ranked across instances. The agent doesn't need to know or care that shared docs and customer resolution history live in separate places.

## How AI Search instances work

So far we've covered how AI Search finds the right results. Now let's look at how you can create and manage your search instances.

If you used AI Search before this release, you know the setup: create an R2 bucket, link it to an AI Search instance, AI search generates a service API token for you, and you manage the Vectorize index that gets provisioned on your account. Uploading an object requires you to write to R2 and then wait for a sync job to run to have the object indexed.

New instances created now work differently. When you call `create()`, the instance comes with its own storage and vector index built-in. You can upload a file, the file is sent to index immediately, and you can poll for indexing status all with one `uploadAndpoll()` API. Once completed, you can search the instance immediately, and there are no external dependencies to wire together.

```javascript
const instance = env.AI_SEARCH.get("my-instance");

// upload and wait for indexing to complete
const item = await instance.items.uploadAndPoll("faq.md", content, {
  metadata: { category: "onboarding" }
});
console.log(item.status); // "completed"

// immediately search after indexing is completed
const results = await instance.search({
  // alternative way to pass in users' query other than using parameter query 
  messages: [{ role: "user", content: "onboarding guide" }],
});
```

Each instance can also connect to one external data source (an R2 bucket or a website) and run on a sync schedule. It can exist alongside the provided built-in storage. In the support agent example, `product-knowledge` is backed by an R2 bucket for shared documentation, while each customer's instance uses built-in storage for context uploaded on the fly.

### Namespaces: create search instances at runtime

The `ai_search_namespaces` is a new binding you can leverage to dynamically create search instances at runtime. It replaces the previous `env.AI.autorag()` API, which accessed AI Search through the `AI` binding. The old bindings will continue to work using [Workers compatibility dates](https://developers.cloudflare.com/workers/configuration/compatibility-dates/).

```javascript
// wrangler.jsonc 
{
  "ai_search_namespaces": [
    { "binding": "AI_SEARCH", "namespace": "example" },
  ]
}
```

The namespace binding gives you APIs like `create()`, `delete()`, `list()`, and `search()` at the namespace level. If you’re creating instances dynamically (e.g. per agent, per customer, per tenant), this is the binding to use.

```javascript
// create an instance 
const instance = await env.AI_SEARCH.create({
  id: "my-instance"
});

// delete an instance and all its indexed data
await env.AI_SEARCH.delete("old-instance");
```

### Pricing for new instances

New instances created as of today will get built-in storage and a vector index automatically.

These instances are free to use while AI Search is in open beta with the limits listed below. When using the website as a data source, website crawling using [Browser Run (formerly Browser Rendering)](https://developers.cloudflare.com/browser-rendering/) is also now a built-in service, meaning that you won’t be billed for it separately. After beta, the goal is to provide unified pricing for AI Search as a single service, rather than billing separately for each underlying component. Workers AI and [AI Gateway](https://www.cloudflare.com/developer-platform/products/ai-gateway/) usage will continue to be billed separately.

We'll give at least 30 days notice and communicate pricing details before any billing begins.

| **Limit** | **Workers Free** | **Workers Paid** |
| --- | --- | --- |
| AI Search instances per account | 100 | 5,000 |
| Files per instance | 100,000 | 1M or 500K for hybrid search |
| Max file size | 4MB | 4MB |
| Queries per month | 20,000 | Unlimited |
| Maximum pages crawled per day | 500 | Unlimited |

*What about existing instances?*

If you created instances before this release, they continue to work exactly as they do today. Your R2 buckets, Vectorize indexes, and Browser Run usage remain on your account and are billed as before. We'll share migration details for existing instances soon.

## Get started today

Search is one of the most fundamental things an agent can do. With AI Search, you don't have to build the infrastructure to make it happen. Create an instance, give it your data, and let your agents search it.

Get started today by running this command to create your first instance:

```shell
npx wrangler ai-search create my-search
```

Check out the [docs](https://developers.cloudflare.com/ai-search/) and come tell us what you're building on the [Cloudflare Developer Discord](https://discord.cloudflare.com/).

![BLOG-3240 4](https://image.jido.dev/20260416142038_BLOG-3240_4.png)

BLOG-3240 4