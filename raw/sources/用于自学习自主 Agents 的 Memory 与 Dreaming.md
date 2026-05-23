---
title: "用于自学习自主 Agents 的 Memory 与 Dreaming"
source: "https://www.bestblogs.dev/video/8bc3725?entry=newsletter_page"
author:
  - "[[Claude]]"
published: 2026-05-21
created: 2026-05-23
description: "Anthropic 的 Ravi 介绍 Memory 与 Dreaming 两个平台原语，帮助自主 agents 保留跨会话知识、协调共享上下文，并在任务外优化记忆。"
tags:
  - "clippings"
---
## Memory and dreaming for self learning agents

Anthropic 的 Ravi 介绍 Memory 与 Dreaming 两个平台原语，帮助自主 agents 保留跨会话知识、协调共享上下文，并在任务外优化记忆。

⭐ 推荐理由Anthropic 为自主 Agent 引入了「记忆」和「梦想」两大原语，其中「记忆」以虚拟文件系统形式管理知识，而「梦想」则作为离线优化循环，全局协调 Agent 的记忆状态。这种结合使 Agent 能够持续学习和改进，例如 Rakuten 实现了首次执行错误率 97% 的下降，显著提升了企业级任务的自动化效率。

[Claude](https://www.bestblogs.dev/videos?sourceid=5f307b71 "查看该来源的更多文章")

05-217 个章节

[查看原文 →](https://www.youtube.com/watch?v=IGo225tfF2I)

### 章节导览

7 个章节

CH.1Introducing Memory and Dreaming00:46

CH.2Evolution of Anthropic Agent Milestones01:09

CH.3Launching Agent Memory Architecture04:10

CH.4Designing Memory as a File System06:17

CH.5Addressing Processing Limitations09:59

CH.6Unveiling the Dreaming Optimization Process10:59

CH.7Live Technical Platform SRE Demonstration15:14

### 📝 实时字幕

00:131

hello Thank you for joining us today I'm excited to kick things off on uh the breakout stage My name

00:201

is Ravi and I lead the API knowledge team within platform at Enthropic And since joining Anthropic last year my

00:291

focus has been creating the building blocks for agents to interact with many forms of knowledge ranging from the context

00:371

window itself to skills files and even content on the web And we recently released two features that I'm most

00:461

excited about Memory and dreaming We now have the building blocks for agents to learn over time and improve from

00:551

one task to the next And I'll talk about why we think memory is important how we designed it and

01:011

we'll close out with dreaming our new frontier memory feature

01:091

There we go But first a quick timeline of milestones that got us here And the important thing is models

01:191

have been improving and agents are capable of completing tasks that take many many hours and are increasingly complex So

01:281

in 2024 we released model context protocol MCP and this gave models access to external tools and data in a

01:371

principled way In 2025 we released Cloud Code and the agent SDK which lowered the barrier to using and building

01:461

agents which as an aside that blows my mind that that was in 2025 It honestly feels like a lifetime

01:531

ago Later that year we launched skills which gave model models a generic abstraction for unlocking and effectively bolting on

02:041

new capabilities to complete specific tasks Last month we released cloud managed agents a platform for reliably running agents that

02:151

takes care of the hard parts Now the important through line here is that agents can do more and they

02:231

can operate over longer and longer time horizons So in 2025 Meter released a study saying the length of tasks

02:331

that agents can complete is doubling every seven months And we're seeing this happen But managing context over long horizon

02:431

tasks is still a work in progress And that's where memory comes in

02:521

Memory lets agents learn It lets agents carry forward learnings from their previous tasks And in the simplest sense imagine

03:021

a set of tasks Task one task two task three and so on The goal is for performance to improve

03:111

from one task to the next In the base case without something like memory performance on each task might be

03:191

similar because every agent is just starting from the same slate In the optimal case performance improves from task one

03:281

to two task two to three and so on That's the goal Learning from task to task but also from

03:361

environment to environment and agent to agent So with memory agents can learn from common strategies and previous mistakes They

03:461

can learn from the tools they have access to or code bases and files And finally they can transfer these

03:521

learnings to and from other agents Think swarms of agents contributing to and maintaining a shared understanding of the organization

04:031

they work in This is the dream

04:101

So we recently launched memory for cloud managed agents and this is a major step towards this vision It gives

04:191

developers a frontier memory system that is built to maximize intelligence out of the box and it supports multi- aent

04:271

systems all with enterprise control and observability

04:351

And we built memory in partnerships with several teams that are using managed agents And the results speak for themselves

04:431

Racketin saw a 97% decrease in first pass errors in agents deployed in production Wise docs reduced common issues using

04:551

cross session memory in their document document verification pipeline And the through line here and the common feedback we get

05:021

is that our memory primitive allows teams to focus on building the product not the infra and all while reaping

05:111

the benefits of increased intelligence that comes along with better memory

05:201

Now you might be thinking is memory really new rightfully so Memory is a concept that's not entirely new but

05:311

our approach for it with agents has greatly evolved and previously we built memory focusing on capabilities in the harness

05:411

So you might be familiar with claw.md for cloud code or dedicated memory tools in the SDKs But one pattern

05:511

we're seeing is that as models improve we really just want to get out of Claude's way similar to what

05:571

we did with skills And skills was a very basic format that was highly flexible And it created endless possibilities

06:061

And the model understood how to operate with it And so with memory we've leaned into that same direction with

06:121

files

06:171

So let's talk about some of the capabilities that we design memory around So right now with the current set

06:241

of models we know a few things Models and claude are great at navigating virtual environments and a file system

06:331

And Claude is also very capable at using familiar tools like bash and GP to read update and organize files

06:411

Opus 4.7 that we launched last month is a state-of-the-art model at file system based memory and it's increasingly capable

06:491

of discerning which context is most important to save for its future self and how it should be structured and

06:591

how it should be represented And so with memory we've modeled it as a file system to quad Again the

07:061

key principle is getting out of cloud's way and letting it use the capabilities it already has that are very

07:131

strong Or as we like to say let it cook This is the dream

07:221

But we've talked about Claude's memory capabilities within the context of a single agent but we want it to work

07:301

across multiple agents that are operating in the same environment at the same time or maybe across environments And this

07:451

they want different scopes So we offer readonly scopes and read write scopes So for example you could have organizationwide

07:551

memory that's readon and it's updated fairly infrequently and it can be accessed by all agents and the same set

08:011

of agents can have access to more granular memory stores that they can read and write freely and so this

08:081

creates a hierarchy and uh allows the memory system to really scale Now to combat right conflicts to make sure

08:161

that one agent isn't clobbering another's rights we employed a optimistic concurrency control model to avoid agents overwriting each other's

08:241

changes

08:281

And last but not least memory needs to work for real production agents This means enterprisegrade controls So version control

08:361

uh creates an audit trail as agents make changes and developers can see how memory evolves over times They can

08:471

even diff between versions and there's attribution to see which agent wrote which part of the memory And I think

08:571

one of the most important pieces is that memory has a standalone API It enables developers to manage their memory

09:051

from anywhere And the reality is teams are building their systems in many different environments So they can use memory

09:121

via these APIs which provide standard credit operations but also more enterprise focused operations like exports and redactions

09:261

Okay So we've covered three key components of a memory architecture One we started with the storage layer which is

09:351

how the data is managed itself and how changes are tracked Next the structure of memory optimizing in a format

09:421

allows Claude to get the most out of it And finally cloud-driven processing for updating the memory Now let's

09:531

stop at that processing point

09:591

agents writing memory as they work is very key to the processing layer Think of it as taking notes while

10:051

you're doing something But as we scaled up this pattern to more complex multi- aent works like uh use cases

10:151

we started seeing some limits across different sessions and we started seeing some common patterns For example agents were prone

10:231

to making many of the same mistakes and they learned from their mistakes independently agents also displayed some of the

10:301

same patterns of inefficiency And the general theme was memory was being updated in a locally optimal way but it

10:391

wasn't globally optimal In some cases there was duplication or fragmentation And so we started thinking really deeply about this

10:491

problem and in the last couple of months we built a feedback loop in the processing layer that combed some

10:581

of these problems

10:591

Now I've said it a couple times but this time I mean it This really

11:051

is available in research preview right now and it can be used with managed agents It's a process that looks

11:121

for patterns and mistakes across agents and sessions and it automatically curates their memory Customers like Harvey saw a six

11:251

times increase in completion rates for their legal benchmark with Dreaming and we're actively seeing other usage of Dreaming and

11:331

we're really excited to see how people are benefiting from it

11:401

A quick overview of how it is process from sessions It's completely decoupled Think of it like a feedback loop

11:501

Agents write memories and dreaming refines and this process repeats And dreaming can be kicked off ad hoc nightly hourly

11:591

or it can even be triggered by events like the end of a session It's all controlled via API So

12:061

it's very flexible Each dreaming run analyzes session transcripts It inspects the existing state of memory and it proposes optimizations

12:161

to the memory in scenarios where sessions were inefficient made mistakes or needed improved guidance And the output is a

12:261

verified better organized snapshot of memories that agents can choose to adopt And dreaming truly enables continuous selfarning It closes

12:391

the loop on memory

12:441

I mentioned outofband the outofband component of dreaming is really really critical creating a process that's decoupled from the underlying

12:541

agent loop has benefits for one architecture makes it useful for multi- aent systems looking at cross session cross agent

13:041

transcripts discerns patterns that a single agent in isolation might struggle to identify There's also benefits to having a dedicated

13:141

dreaming harness It allows for clearer objectives Since dreaming is an independent process there's no risks of agents needing to

13:241

trade off between improving their memory quality or actually just completing their task objective It's clean separation And lastly it

13:331

doesn't add any latency to the agent It's completely removed from the hot pass

13:431

So zooming out we now have a robust memory layer that can be shared across agents and environments instead of

13:511

only within specific tasks or usage We also have dreaming a process that globally optimizes and reconciles memory across agents

14:001

And the result is a capable memory system for organizational memory that is capable of scaling up both the size

14:021

as well as the quality of memory And the way I think about it is sharing memory that's constantly improving

14:111

across agents raises the floor for every agent and dreaming raises it even further

14:211

And if you really explode the size of this capability and you pull it all together memory becomes a huge

14:291

source of knowledge

14:301

models or test time compute where letting models spend some tokens to explore a problem on average produces better outcomes

14:401

With dreaming agents are doing the same thing They're spending some work up front to curate and produce higher quality

14:471

memory and that pays dividends for all downstream agent performance

14:561

We believe that dreaming and memory form the basis of a frontier memory system Memory on the left helps agents

15:031

learn and remember from task to task and dreaming on the right verifies organizes and enriches the memory The way

15:121

I think about it is dreaming is the bridge between memory as we know it today and organization scale memory

15:141

and knowledge Now I'm going to flip over to a demo

15:231

So this uses both dreaming and memory in practice It's an agent platform for SRRES and everyone loves being on

15:321

call right so here we have a system that looks at incoming alerts and pages and for some of them

15:321

it actually uh spins up agents that decide how to triage and fix the issues as they come up

15:371

and it has access to a couple of memory stores One is a readonly orwide knowledge memory store And so

15:461

this contains things like the SLO policy or runbooks and on call mappings information that doesn't change very often but

15:531

is important for every agent And it also has access to read write memory stores that are specific to the

16:011

task at hand Now we can dig into an interesting example here where an agent investigated and found the root

16:081

cause of an alert

16:101

and it put up a fix and it noted in memory You can see the writes It noted in memory

16:141

that a fix was in flight and it was incoming And then the shared memory store can be read by

16:211

uh subsequent sessions And so here we can see that when a similar issue arises the downstream session already knows

16:301

that a fix is in flight and it's able to act based on that information And I really think this

16:391

is just such a cool pattern because you know the I I was once an SR in my career and

16:491

this really uh helps coordinate across all agents and it's really cross- session memory at work Now for running in

16:591

enterprises uh an important piece here is audit logs and history So with memory you can see the full version

17:091

history You can switch between different versions and you can also attribute the rights to specific sessions And there's also

17:181

a precondition here and that's the optimistic concurrency model to make sure that agents aren't clobbering each other's rights Now

17:261

we'll flip over to the cloud console

17:361

One moment There we go So here we see the list of underlying memory stores that we were using in

17:471

that application And so we'll go over to our team SR memory store And you can see exactly the underlying

17:551

files that were populated there And we're going to head over to the dreams tab And we're going to kick

18:021

off a dream And so this can also be done via the API uh but also in the UI And

18:081

we're going to select the team SR memory store and we're going to select a batch of sessions from the

18:151

last seven days So that's about five And we're going to start dreaming As it begins you can see it

18:191

making progress You can look at the dream and see that there are five input sessions And then you'll see

18:261

there's actually an output memory store that's being compiled And you can actually open the dreaming session This is an

18:341

important piece Dreaming itself is built on cloud manage agents So it's a feature for cloud manage agents built on

18:401

cloud manage agents itself You can see that it spins off a series of sub aents to analyze transcripts in

18:461

parallel And it has all the same UX as the rest of manage agents And we'll fast forward to a

18:511

completed dream session And you can see the diffs on the memory store updates And in this example here we

18:581

see that across sessions and across agents there's a a common pattern of an alert triggering 60 seconds after a

19:081

CPU spike And this is a recurring pattern And so it starts to discern that there might be some issue

19:191

with the retry behavior And so it makes a note So this dreaming process makes a note and updates memory

19:261

so that the next agent that sees this pattern can actually similarly updates the triage log in a more holistic

19:331

way rather than just being a wrote log of all the events that happened

19:431

And that's memory and dreaming at work So we'll flip back over to the slides

19:531

and we'll close out So with that demo we saw how we can build a a production agent that uses

19:591

memory and dreaming to self-improve the agents And this year I think is going to be a really big one

20:071

We're going to see agents run for longer and longer time scales days for example and continuously building upon and

20:161

improving their understanding and view of the world around them is very critical to unlocking that capability And I think

20:241

memory systems are going to be a big part of what makes this behavior possible So give it a try

20:321

I'm excited to see what everyone builds with it And I'll be outside if you have more questions Thank you

点击任意行跳转 · 悬停可向 AI 提问

### 内容简介

## Memory and Dreaming for Self-Learning Autonomous Agents

As autonomous agents take on increasingly complex corporate tasks, managing historical execution context over multi-hour and multi-day horizons remains a core engineering bottleneck. Without deep persistent infrastructure, agents approach every new instruction as a blank slate—frequently repeating mistakes, duplicating work, and failing to share localized workspace operational knowledge across broader agent teams.

To bridge this architectural gap, Anthropic developed two infrastructure primitives for cloud-managed agents: **Memory** and **Dreaming**. Together, these systems establish a scalable framework that lets autonomous swarms continuously improve from task to task, elevating organization-wide performance.

---

## The Technical Road to Persistent Memory

Over recent years, the capability landscape for autonomous agents has progressed through distinct architectural phases:

- **2024 (Model Context Protocol - MCP):** Set a principled standard for models to connect to external application databases and data sources.
- **Early 2025 (Agent SDK & Cloud Code):** Substantially lowered developers' barriers to entry for running and instantiating custom code frameworks.
- **Late 2025 (Skills):** Delivered generic system abstractions to easily drop specific tactical capabilities onto base model behaviors.
- **Recent (Cloud Managed Agents):** Established production hosting environments to handle the foundational engineering weight of long-horizon task coordination.

With autonomous task execution lifecycles doubling every seven months, managing long-horizon execution contexts required transitioning from local session memories to globally shared repositories.

---

## Designing Memory as a Virtual File System

Rather than forcing models to conform to restrictive internal memory management APIs, Anthropic's Memory system models knowledge explicitly as a standard virtual file system exposed to the AI model.

Modern large language models like **Opus 4.7** display significant native proficiency when manipulating files and navigating folder paths. By representing past experiences and shared knowledge as standard directories, the agent uses familiar terminal tools like bash and grep to inspect, alter, and organize its past records. This approach eliminates unnecessary software layers and lets the model decide which exact components of a session are worth saving for its future self.

### Enterprise Control Hierarchy and Concurrency

Deploying shared memory configurations across massive company environments creates significant read-write conflict vulnerabilities. Anthropic addresses this challenge with three architectural constraints:

1. **Scoped Hierarchies:** Agents run with access to distinct memory spaces at once—including wide, read-only corporate knowledge bases (containing static SLO policies or runtime guides) alongside localized, highly specific read-write task stores.
2. **Optimistic Concurrency Control (OCC):** Protects information consistency by preventing multiple concurrent agent workers from overwriting or clobbering each other's state changes during simultaneous writes.
3. **Standalone REST APIs:** Enables external engineering teams to easily execute standard CRUD actions, trigger data exports, or perform compliance redactions from external development stacks.

Early enterprise deployments show significant performance jumps. Rakuten noted a **97% drop in first-pass execution errors** for agents running in live production, while Wise Docs eliminated cross-session processing bottlenecks inside its main document verification pipeline.

---

## The Dreaming Primitive: Achieving Global Optimization

While real-time, session-based note-taking helps agents handle ongoing operations, relying solely on local memory updates leads to systemic issues over time. Multi-agent networks frequently encounter memory fragmentation, redundant records, and localized learning loops where independent agents resolve identical mistakes in isolation.

To counteract this fragmentation, the **Dreaming** loop operates as an out-of-band feedback system that reconciles memory states globally.

```
[ Agent Sessions ]  --->  ( Write Local Memories ) 
       ^                                  | 
       |                                  v
[ Refined Snapshot ] <---  [ Dreaming Optimization Loop ]
```

### Core Mechanics of the Dreaming Loop

- **Decoupled Operation:** Dreaming runs out-of-band via API commands—scheduled either hourly, nightly, or at session termination. Because it is completely decoupled from the primary operational flow, it introduces zero runtime latency to live agents.
- **Parallel Analysis:** Built directly on top of cloud-managed agent infrastructure, the Dreaming process initiates parallel sub-agents to process multi-session text transcripts and compare them against current database states.
- **Conflict Resolution:** The loop identifies systemic inefficiencies, isolates recurring error loops, removes data duplications, and proposes clean, verified memory transformations.

The resulting output is a structural memory snapshot that agents can immediately inherit. By migrating from independent learning profiles to a globally unified memory snapshot, the baseline capabilities of the entire agent swarm rise continuously. In real-world trials, legal technology developer Harvey recorded a **six-fold increase in completion rates** across its standardized legal benchmarks using Dreaming-driven optimization loops.

---

## Future Horizon for Self-Learning Agents

As developer reliance shifts from short-horizon automated scripts to truly long-lived organizational workers, robust contextual retention layers become essential infrastructure. By combining active file-based memory stores with background Dreaming optimization routines, development teams can build autonomous systems that steadily master enterprise engineering tasks, continuously closing the loop on software self-learning.

让每天的阅读，从真正适合你的内容开始

基于你关注的来源生成专属早报，越读越懂你

[免费体验](https://www.bestblogs.dev/signup?redirect=%2Fvideo%2F8bc3725)