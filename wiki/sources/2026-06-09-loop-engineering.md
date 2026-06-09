---
title: "Loop Engineering."
type: source
status: summarized
sources:
  - ../../raw/sources/Loop%20Engineering..md
source_date: 2026-06-09
source_date_basis: published
created: 2026-06-10
updated: 2026-06-10
---

这是一篇 Addy Osmani 发在 X 上的长文，提出 "Loop Engineering"（循环工程）这组说法：不再亲自提示 Agent，而是设计一套会替你提示 Agent 的系统。它的长期价值不在于宣布提示工程已经过时，而在于把 Harness 之上的编排层命名出来，并把它映射到 Claude Code 与 Codex 已经内置的五个具体原语上。需要保留的证据边界是：这是单一作者的早期判断，作者本人也明确表示“仍然很早、我持保留态度”。

## 这份资料是什么

- 资料是 Addy Osmani 于 2026-06-09 在 X（`https://x.com/addyosmani/status/2064127981161959567`）发布的长贴，并大量回链他本人的 blog 系列（agent harness engineering、factory model、long-running agents、intent debt、comprehension debt、cognitive surrender 等）。
- 文章引用了 @steipete（“你不该再提示编码 Agent，而该设计提示 Agent 的循环”）和 Anthropic Claude Code 负责人 @bcherny（“我不再提示 Claude，而是写循环去提示 Claude，我的工作是写循环”）两句二手转述作为立论起点。
- 核心主张：一个 loop 由五个构件加一处记忆组成，作者称 Claude Code 和 Codex 现在都凑齐了这五件。

## 主要观点

1. Loop Engineering 的定义是“把你自己从提示者的位置上替换掉”：你设计一个能自己找活、派活、验收、记录并决定下一步的系统，让它去戳 Agent，而不是你一轮轮去戳。
2. 一个 loop 可以理解为递归目标：你定义目的，AI 迭代直到完成。
3. 五个构件是：自动化（按计划自动发现与分诊）、worktree（并行隔离）、Skills（写下项目知识）、插件与连接器（接入既有工具）、子 Agent（让出主意的和检查的分开）；第六件是记忆，一个活在单次对话之外、记录“做完了什么、下一步是什么”的磁盘文件或看板。
4. Loop Engineering “坐在 Harness 上面一层”：Harness 是单个 Agent 运行其中的环境，而 loop 在它之上跑在定时器上、孵化小助手、自己喂自己。
5. 这已经不再是“工具”问题：一年前要循环得自己写一堆 bash 并永久维护，现在这些能力直接随产品出厂，且 Codex 与 Claude Code 形状几乎一致，于是该设计的是无论坐在哪个工具里都成立的 loop。
6. Loop 改变了工作，但没有把人删掉；而且随着 loop 变好，三个问题反而更尖锐：验证仍在你身上、理解力会腐化（comprehension debt）、容易陷入“认知投降”（cognitive surrender）；token 成本也必须当心。

## 关键内容

### 五个构件加一处记忆

文章把 loop 拆成可逐项排查的结构，并强调两个产品名字不同但能力等价：

- 自动化是“心跳”：让 loop 成为持续的循环而不是一次性运行。Codex 在 Automations 标签里配置项目、提示、频率和运行位置，找到东西进 Triage，没找到自动归档；Claude Code 用 `/loop`（按节奏重跑）、cron、hooks 和 GitHub Actions 达到同一处。
- `/goal` 是更贴近本文主题的会话内原语：它持续运行直到一个可验证的停止条件成立，且每轮之后由一个独立小模型判断是否完成——写代码的 Agent 不是给自己打分的那个。Codex 也有同名 `/goal`。
- worktree 解决并行碰撞：两个 Agent 同写一个文件等同于两个工程师不打招呼提交同一行；git worktree 给每个 Agent 独立 checkout。
- Skills 让你不必每次重新解释项目：统一是 `SKILL.md` + 可选脚本/引用/资产的文件夹格式；好的描述要“紧而无聊”才能被准确触发。Skill 是创作格式，plugin 是分发方式。
- 插件与连接器（建立在 MCP 上）让 loop 触达真实工具：读 issue tracker、查数据库、打 staging API、往 Slack 发消息，于是 loop 能“开 PR、连 Linear、CI 绿了再 ping 频道”，而不只是说“这是修复方案”。
- 子 Agent 的核心是把“写的人”和“查的人”分开：写代码的模型给自己打分太宽容，换一个指令甚至换一个模型的 Agent 才抓得住前者说服自己接受的问题。

### Loop 与 Harness 的分层

- 作者明确把 loop 放在 harness 的上一层：harness 是“单个 Agent 运行其中的环境”，loop 是“在其上、跑在定时器上、会孵化助手、会自我投喂”的编排系统。
- 这条分层也复用了他此前的 factory model（构建软件的系统）与 long-running agents（模型在两次运行之间会遗忘，所以记忆必须落在磁盘而不是上下文）两条线。

### 一个 loop 的形状与边界

- 典型形状：每天早上自动化跑一次仓库，调用分诊 Skill 读昨天的 CI 失败、未决 issue 和近期提交，写进 markdown 或 Linear；对值得做的发现开隔离 worktree，派一个子 Agent 起草修复、另一个子 Agent 对照项目 Skill 和现有测试评审；连接器开 PR、更新工单；处理不了的进 triage inbox；状态文件是整件事的脊柱。
- 三条会随 loop 变好而更尖锐的限制：验证仍是你的工作（“done” 是声明不是证明）、理解力会随你不读 loop 产出而腐化、舒适姿态往往才是危险姿态（停止持有观点、照单全收即“认知投降”）。
- 作者的总结口号是“Build the loop. Stay the engineer.”——设计 loop 是放大判断力还是放大逃避思考，取决于使用者，同一个动作可得相反结果。

## 纵向线索

- 对 [Loop Engineering](../concepts/loop-engineering.md) 来说，这是当前唯一的立页一手依据，提供了术语、五件套结构和与 Harness 的分层关系。
- 对 [Harness Engineering](../concepts/harness-engineering.md) 来说，它给出了一条“在 harness 之上”的编排层定位，把此前分散的自动化、worktree、子 Agent、记忆收束成同一层。
- 对 [Agent 学习闭环](../concepts/agent-learning-loop.md) 来说，它把“采集—提炼—应用”的闭环外化成一个跑在定时器上的系统：自动化发现工作、子 Agent 执行与检查、状态文件跨运行记忆。

## 横向线索

- 与 [Agent Session Management](../concepts/agent-session-management.md)：worktree 隔离与子 Agent 编排正是长任务里管理并行会话与上下文边界的运行时手段。
- 与 [Agent 记忆系统](../concepts/agent-memory-systems.md)：“Agent 会忘，仓库不会忘”——磁盘上的状态/记忆文件是 loop 跨运行延续的前提。
- 与 [信息分层设计](../concepts/information-layering-design.md)：Skills 把项目意图写在外部、按需加载，正是把意图债（intent debt）转成可复用上下文资产。
- 与 [Agent 评测](../concepts/agent-evaluation.md)：maker/checker 分离与 `/goal` 的“独立模型判停”，本质上是把验证做成 loop 的内生传感器。

## 与知识库的相关性

这篇资料适合新建 [Loop Engineering](../concepts/loop-engineering.md) 概念页，因为它给出了明确术语、可复用的五件套结构、与 Harness 的清晰分层，以及具名出处。它也应回写到 [Harness Engineering](../concepts/harness-engineering.md)、[Agent 学习闭环](../concepts/agent-learning-loop.md) 和 [Agentic Engineering](../concepts/agentic-engineering.md)，说明“编排单个 Agent 的环境”之上还有“编排整套自运行循环”的一层。

## 可靠性判断

- 这是单一作者的观点贴，作者本人明确保留态度（“仍然很早、我持保留态度”），不应当作行业共识。
- 文中大量结论建立在 @steipete、@bcherny 等人的二手转述上，原话语境需回到各自原帖核对。
- 五件套与具体产品功能（Codex Automations、Claude Code `/loop` `/goal`、worktree、hooks）强绑定，这些功能名称和行为可能随版本变化，引用时应标注时间点。
- token 成本、质量下降（slop）和“认知投降”是作者自己点出的边界，应与方法主张一并保留。

## 受影响页面

- [Loop Engineering](../concepts/loop-engineering.md)
- [Harness Engineering](../concepts/harness-engineering.md)
- [Agent 学习闭环](../concepts/agent-learning-loop.md)
- [Agentic Engineering](../concepts/agentic-engineering.md)

## 开放问题

- Loop Engineering 是否会成为稳定术语，取决于后续是否有更多一手实践、工具文档或团队案例采用这组命名，而不只停留在意见领袖的 X 讨论。
- “loop 跑在 harness 之上”这条分层在不同团队的实际系统里是否稳定，还是会与 harness、factory model 等说法继续混用。
- 在无人值守 loop 下如何度量并约束 token 成本与质量漂移，使 maker/checker 分离真正提供可信验证，而不是把错误也自动化。

## 来源

- [原始摘录](../../raw/sources/Loop%20Engineering..md)
- [原文链接](https://x.com/addyosmani/status/2064127981161959567)
