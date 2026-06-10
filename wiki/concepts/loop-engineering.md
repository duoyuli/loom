---
title: Loop Engineering
type: concept
status: active
sources:
  - ../sources/2026-06-09-loop-engineering.md
  - ../sources/2026-06-10-designing-loops-with-fable-5.md
updated: 2026-06-10
---

Loop Engineering（循环工程）指的是把人从“逐轮提示 Agent 的人”这个位置上替换下来：你不再亲自一轮轮提示编码 Agent，而是设计一个会自己找活、派活、验收、记录并决定下一步的系统，让它去驱动 Agent。它被提出者明确放在 [Harness Engineering](harness-engineering.md) 的上一层。

## 定义

按最早立页来源（Addy Osmani 的 X 长文）的说法，一个 loop 可以理解为一个递归目标：你定义目的，AI 迭代直到完成。它由五个构件加一处记忆组成，作者称 Claude Code 与 Codex 现在都凑齐了这五件：

1. 自动化（Automations）：按计划自行触发，自己做发现和分诊，是让“循环”成为循环而非一次性运行的心跳。
2. Worktree：让并行的多个 Agent 各自在独立 checkout 上工作，不互相踩文件。
3. Skills：把项目知识写下来，避免 Agent 每次冷启动都靠猜重新推导意图。
4. 插件与连接器：基于 MCP 把 Agent 接到 issue tracker、数据库、staging API、Slack 等既有工具上。
5. 子 Agent：把“出主意的”和“检查的”分开，写代码的模型不给自己打分。

第六件是记忆：一个活在单次对话之外、记录“做完了什么、下一步是什么”的磁盘文件或看板。作者用一句话概括其必要性——“Agent 会忘，仓库不会忘”，因为模型在两次运行之间会遗忘，状态必须落在磁盘而不是上下文里。

需要保留的证据边界：这是单一作者的早期判断，作者本人明确表示“仍然很早、我持保留态度”，并强调 token 成本和质量下降（slop）是真实风险。因此本页把 Loop Engineering 当作一个正在成形的工程命名，而不是已被验证的稳定范式。

## 纵向脉络

- 起点是一个观察：过去两年，从编码 Agent 取得产出的方式是“写好提示、给够上下文，你一轮轮地拿着工具”；@steipete 与 Anthropic Claude Code 负责人 @bcherny 把它推到“不该再提示 Agent，而该设计提示 Agent 的循环”。
- 关键转变是“这不再是工具问题”：一年前要循环得自己写一堆 bash 并永久维护，现在自动化、worktree、Skills、连接器、子 Agent 这些能力直接随 Codex / Claude Code 出厂，且两者形状几乎一致。
- 因此 Loop Engineering 的工程动作不是绑定某个工具，而是设计一个“无论坐在哪个工具里都成立”的 loop；具体产品原语（Codex Automations、Claude Code `/loop` `/goal`、hooks、GitHub Actions）只是同一形状的不同实现。

## 横向位置

- 它被作者明确定位为“坐在 Harness 上面一层”：[Harness Engineering](harness-engineering.md) 管单个 Agent 运行其中的环境，Loop Engineering 则在其上跑在定时器上、孵化助手、自我投喂。
- 它与 factory model（构建软件的系统）、long-running agents（磁盘记忆）属于同一作者的同一条线索，是这条线索里最靠近“编排层”的命名。
- 它和 [Agentic Engineering](agentic-engineering.md) 的关系是：后者是把 AI 嵌入整个 SDLC 的总方法论并坚持工程师保留判断权，Loop Engineering 则是其中“自运行编排”这一具体形态——它把人从执行者推向 loop 设计者，但反复强调“Build the loop, stay the engineer”，人不能退化成只按启动键的人。

## 新增视角：Loop 需要可评分目标和独立 verifier

- `Designing loops with Fable 5` 把 loop 的运行时形状压得更细：一个有效 loop 不只是反复调用模型，而是把目标/rubric、外部反馈、日志读取、独立 grader 子 Agent、停止条件和跨 session memory 接在一起。
- 其中最关键的补充是 maker/checker 分离：写代码或做实验的 Agent 不适合给自己打分，独立上下文里的 verifier 更像 loop 的传感器。
- 这让 Loop Engineering 的“递归目标”定义更可操作：目标必须能被环境或 grader 反复检查，否则 loop 只是无人值守地重复提示。

## 与其他概念的关系

- [Harness Engineering](harness-engineering.md)：harness 是被编排的运行环境，loop 是其上的自运行编排层；loop 复用 harness 的工具、状态、守卫和验证，但额外加上节奏、自我触发和跨运行记忆。
- [Agent 学习闭环](agent-learning-loop.md)：Loop Engineering 把“采集—提炼—应用”的闭环外化成一个跑在定时器上的系统，自动化负责发现、子 Agent 负责执行与检查、状态文件负责跨运行记忆。
- [Agent Session Management](agent-session-management.md)：worktree 隔离与子 Agent 编排是 loop 在长任务里管理并行会话与上下文边界的运行时手段；作者也提醒人类 review 带宽才是真正的并行上限（orchestration tax）。
- [Agent 记忆系统](agent-memory-systems.md)：磁盘上的状态/记忆文件是 loop 跨运行延续的前提，对应“记忆必须在上下文之外”这条约束。
- [信息分层设计](information-layering-design.md)：Skills 把项目意图写在外部、按需加载，正是把“意图债”转成可复用上下文资产，让 loop 不必每轮从零重新推导项目。
- [Agent 评测](agent-evaluation.md)：maker/checker 分离与 `/goal` 的“独立小模型判停”，本质上是把验证做成 loop 的内生传感器；但作者强调“done”仍是声明不是证明，最终验证仍在人身上。

## 开放问题

- Loop Engineering 是否会沉淀为稳定术语，还是会与 harness、factory model、agent orchestration 等说法继续混用，取决于后续是否有更多一手实践和工具文档采用这组命名。
- “loop 跑在 harness 之上”的分层在不同团队的真实系统里是否稳定，值得后续用仓库级或产品文档级证据校验。
- 无人值守 loop 下如何度量并约束 token 成本与质量漂移，使 maker/checker 分离真正提供可信验证，而不是把错误也一起自动化——这与 comprehension debt、cognitive surrender 一起构成本概念的主要风险边界。

## 来源

- [Loop Engineering.](../sources/2026-06-09-loop-engineering.md)
- [Designing loops with Fable 5](../sources/2026-06-10-designing-loops-with-fable-5.md)
