---
title: Harness Engineering
type: concept
status: active
sources:
  - ../sources/2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md
  - ../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md
  - ../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md
  - ../sources/2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md
  - ../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md
  - ../sources/2026-04-17-一文讲透-harness-engineering-即控制论.md
  - ../sources/2026-04-12-你的-react-智能体正在浪费-90-的重试机会-以下是解决方法.md
  - ../sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md
  - ../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md
  - ../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md
  - ../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md
  - ../sources/2026-04-06-the-anatomy-of-an-agent-harness.md
  - ../sources/2026-04-23-从第一性原理思考-agentic-engineering.md
updated: 2026-04-24
---

Harness Engineering 指的是为 Agent 搭建一套可执行的约束体系，让模型输出被规则、测试、反馈和清理流程持续塑形，而不是只靠提示词或人工兜底。

## 定义

新摄入的 `The Anatomy of an Agent Harness` 又把这个概念的边界说得更明确了一层：Harness 不是“prompt 外面再包一点胶水代码”，而是包在模型外的完整非模型基础设施，包括主循环、工具、记忆、上下文治理、状态持久化、错误恢复、守卫和验证。这里也要保留一个有用区分：`agent` 更像用户看到的目标导向行为，`harness` 则是产出这种行为的机制总和。

这份资料把 Harness 比作缰绳：模型像马，能力很强，但如果缺少缰绳，就会在高吞吐下快速复制坏模式、突破架构边界并积累技术债。

OpenClaw 的资料把这件事进一步写实：Harness 不只是“有规则”，而是一个包在模型外部的运行环境，里面包含 Hook、Guardrail、启动与心跳规定动作、审批暂停点，以及把 Human-in-the-loop 保留为最终接管权的接口。Hermes / OpenClaw 对比则补上一条更保守的工程立场: 当模型判断不稳时，很多关键条件宁可写成硬阈值、黑名单或冻结策略，也不要继续交给模型自由裁量。

新摄入的“从玩具到生产力”实践文又把这个概念压得更尖锐了一层：传统软件工程主要治理确定性系统，Harness Engineering 主要治理“非确定性模型如何进入确定性交付链”。因此它真正管理的不只是规则列表，而是 `spec / handoff / checkpoint / test / log / approval` 这类持续控盘动作。

## 这份资料提供的关键点

这篇来源把 Harness 的实践拆成约束、文档、反馈、清理四层。这里需要保留证据边界：四层拆法是作者对 OpenAI 经验的转述和压缩，适合当工作定义使用，但若后续要论证具体实践细节，仍应回到 OpenAI 原文。

### 约束层

- 架构规则不应只写在文档里，而应尽量转成 `linter`、结构化测试或其他机械化检查。
- “值得写进文档的规则，通常也值得被自动执行”是本文最重要的判断之一。
- OpenClaw 的实践还提醒我们，Harness 不等于 Workflow。Workflow 是人预先写死路径，Harness 则保留 Agent 的自主规划权，但在关键节点加上外层约束、校验和熔断。

### 文档层

- `AGENTS.md` 不适合承载全部细则，更适合做目录和导航。
- 长文档会稀释重要性；高价值规则需要被拆到结构化页面，而不是堆在一个总说明中。
- Hermes 拆解给出了一条更具体的工程提醒：项目级 `AGENTS.md` 不只是“说明文档”，而是可能直接进入运行时系统提示；如果它过长、装载策略过粗，就会大量占用 token 预算并稀释真正关键的约束。
- 因此 harness 不只是写规则，还包括决定哪些规则常驻、哪些规则按需加载，以及如何控制规则文件的装载粒度。

### 反馈层

- Agent 出错后，不应默认“重试一次”，而应把错误转成可复用的能力修补。
- 每次失败都应促成仓库级改进，而不是一次性人工补丁。
- 新摄入的 Agent 架构综述把这层进一步写实：`task / trial / grader`、完整 trace、事件流和外部状态记录，都属于 harness，而不是事后附加的观察层。
- 新摄入的 Prompt 工程资料则把这一层拉回到最常见的 LLM 产品形态：`golden case`、回归测试、线上采样和差评样本回流，本质上都是 Prompt 系统的 harness，而不是“评测做完再说”的附属物。

### 清理层

- AI 产出会持续制造“泥浆”，需要后台扫描、偏差检查和小步修复。
- 与其定期大扫除，不如把清理变成高频的小额偿还。

## 新增视角：`agent-legible` 仓库与 1 分钟内循环

- 新摄入的 “Token 亿万富翁” 资料把 Harness 继续从“规则与守卫”往仓库底层推进了一步：构建系统、可观测性、Markdown tracker、quality score 和技能文档，本身都要为了 Agent 可读性重新组织。
- 其中最值得保留的工程阈值是 `< 1 分钟` 构建内循环。它不只是体验优化，而是一个运行时预算：一旦超时，就说明 build graph、任务拆分或仓库结构还需要继续收敛。
- 这让我们更清楚地看到，Harness 不只约束 Agent 的动作边界，也在决定 Agent 每一轮等待什么、看到什么、能否快速验证什么。

## 新增视角：Harness 管的是非确定性，而不是普通工程 hygiene

- 新摄入的企业实践资料给出了一句很适合长期保留的区分：传统软件工程更多是在防人类“手滑”写出 bug，Harness Engineering 则是在防非确定性模型把同一目标走向错误的执行路径。
- 这也是为什么“Prompt 是指令，Harness 是约束”值得继续保留。长 Prompt、道德劝说和工具名单都还停留在模型脑内，而真正的 Harness 必须写在模型外部，能在执行前后物理阻断越界。
- 一旦任务进入企业工程环境，问题也会跟着改变：比起“这句话怎么说更像高手”，更关键的是有没有真相源、审批点、日志链路、测试回路和恢复点。

## 新增视角：先分清伪 Harness、劣质 Harness 和持续控盘

- 这篇来源把两个常见误区区分得很清楚：`5000` 字 `DO NOT` 和“给 20 个 API 让模型自己挑”属于伪 Harness，因为它们没有提供模型外的硬边界。
- 另一类误区是劣质 Harness，例如暴力死循环重试或强制巨型文档流。它们确实加了控制面，但控制方式本身会制造新的噪声、预算浪费和偏航。
- 相对更稳的样板是前置验证、最小真相源和执行前 `checkpoint`。它们的共同点不是“更复杂”，而是把阶段目标、风险和证据要求显式外置出来。

## 新增视角：把人类移出合并前关键路径

- 这份资料给出了一种更激进的默认立场：当 token 足够便宜、Agent 足够并行时，最稀缺的资源不再是生成成本，而是人类同步注意力。
- 在这个视角下，Harness 的目标不再只是“辅助人工 review”，而是尽量用自动检查、可观测性、Agent 自审与合并后修正，把人工逐行审查从关键路径上挪开。
- 这里仍要保留证据边界：这是通过 Latent Space 整理的 OpenAI Frontier 实践样本，适合当 frontier 方向的参考，不应直接外推成所有团队都已可采用的稳定基线。

## 新增视角：Hook、规定动作与人在环

- OpenClaw 的资料把 Harness 拆成更容易落地的对象：`before_tool_call` 这类 Hook 用于执行前预防，`before_compaction / after_compaction` 这类 Hook 用于关键状态切换时观察或后处理。
- `HEARTBEAT.md`、`BOOTSTRAP.md` 和类似启动脚本的文件并不是普通说明文档，而是把“定期检查什么”“初始化必须做什么”写成系统规定动作。
- 人在环也不只是审批 UI，而是 Harness 的结构组成：当系统暂停等待确认时，表示最终控制权仍由人保留，而不是模型独占。

## 新增视角：Harness 是持续控盘，而不是起手一次性下指令

- 新摄入的 Aegis 实践把这件事写得很具体：长任务最常见的失败，不是一开始就错，而是做着做着慢慢偏。所以 Harness 的关键动作必须持续发生，而不是开场说完总目标就放任执行。
- 更可复用的协作节奏是：先用 `spec / handoff` 恢复外部真相源，再压当前轮的最小目标；执行前要求一次 `checkpoint`，明确当前理解、下一步动作、风险和验证方式；执行后只接受测试、日志、接口回包等外部证据，而不接受“我觉得好了”。
- 这也让“阶段完成不等于全局完成”第一次变成可操作的门禁，而不是口头提醒。Harness 在这里不再只是限制动作边界，也在持续重写当前轮次到底算什么、完成到哪一步才允许继续。

## 新增视角：有时越笨的规则越像 Harness

- Hermes / OpenClaw 对比资料给出了一种现实工程判断：在超长上下文和多轮任务里，越“聪明”的全权统管方案越容易翻车，反而是字符串替换压缩、固定轮数反思、正则审批和冻结快照这种笨办法更稳定。
- 这说明 Harness 的目标不是让模型显得更自由，而是让系统在真实预算、真实风险和真实噪声里更可控。
- 当我们把复杂度从用户手里拿走时，它并没有消失，而是被重写进默认规则、阈值和回退路径里；这正是 Harness 需要被单独设计和审计的原因。

## 新增视角：Harness 也是训练对象

- 新摄入资料把 harness 放到了训练链路里，而不只是在运行时兜底。
- 在 Agent 训练阶段，`prompt construction`、`retrieval policy`、`memory update`、`context editing`、`tool orchestration` 都属于 harness 的职责。
- 如果环境不稳定、工具返回噪声大或状态不可复现，模型会先学会利用漏洞，而不是学会能力本身。
- 这意味着 harness 已经不仅是“如何把模型用好”，也是“模型最终会形成什么能力”的一层。

## 新增视角：Prompt 系统的最小 harness

- 新资料提供了一个更贴近日常产品迭代的最小样板：先把 Prompt 抽成模板，再用 `golden case + judge` 做离线回归，最后把线上失败样本不断回流。
- 这样一来，Prompt 变更第一次有了回归安全网，也第一次能被 CI 或固定评测流程拦截。
- 资料还说明，多模板路由和 request spec 不是孤立技巧；它们只有接入评测、回滚和反馈之后，才真正进入 harness 体系。

## 新增视角：Harness 是一张职责清单，而不只是一个总称

- 新摄入资料给出了一份很适合工程排查的组件清单：`orchestration loop / tools / memory / context management / prompt construction / output parsing / state management / error handling / guardrails / verification loops / subagent orchestration`。
- 这份清单的价值，不在于提供最终 taxonomy，而在于把原本容易混作一团的职责拆开。例如 `verification` 不应被吞进普通错误处理，`context management` 也不等于 `memory`。
- 仍需保留一个证据边界：原文声称有“12 个组件”，但正文只实际列出了 11 个。这说明它更适合被当成综述性的检查表，而不是严格标准。

## 新增视角：Harness 也是控制回路

- 新摄入的“控制论”资料把 Harness 明确映射成 `目标 -> 控制器 -> 被控系统 -> 传感器 -> 反馈` 这一闭环。
- 这套语言的价值，不只是多一个比喻，而是让很多分散做法重新归位：需求是目标，仓库规则是环境约束，测试和监控是传感器，Agent 则是执行具体控制动作的控制器。
- 它也提醒，任务拆分和多轮修正之所以有效，是因为它们在积累控制力，而不只是“把 prompt 说得更细”。

## 新增视角：错误分类、熔断与确定性路由

- ReAct 重试分析提供了一组更细粒度的 Harness 手段：错误分类、单工具熔断器、确定性工具路由。
- 这组手段的核心思想是：不可重试错误不能和可重试错误共用同一套预算治理逻辑；否则系统会把重试浪费在永远不会成功的问题上。
- 其中最值得保留的判断是，某些可靠性问题不应继续交给模型“多试几次”，而应收回到代码层用更硬的结构解决。

## 新增视角：Harness 厚度本身就是设计决策

- `The Anatomy of an Agent Harness` 把很多框架差异进一步压成了七个设计选择：单 Agent 还是多 Agent、`ReAct` 还是 `plan-and-execute`、上下文窗口治理策略、验证回路设计、权限架构、工具暴露范围，以及 Harness 到底要写多厚。
- 其中最值得保留的一点是“厚度”意识：Harness 不是越厚越先进。很多外层逻辑只是暂时替模型补洞；如果模型升级后仍必须继续堆厚，说明设计可能没有抓住长期稳定结构。
- 同一篇资料还补出一个很重要的共演化判断：模型会逐渐适应其训练时所在的 Harness，Harness 也会随着模型能力提升而被不断删薄。对知识库而言，这提醒我们在讨论具体实现时，不能把某个版本的外层程序误当成永久形态。


## 新增视角：多层次验证是 Agentic Engineering 的可靠性骨架

- 新摄入的 Agentic Engineering 资料把 Harness 的必要性重新推回三条约束：LLM 输出有概率性，长任务会让错误累积和上下文退化，人类一次性审查大型未校验变更的认知成本过高。
- 因此 Harness 不只是在动作边界上加 guardrail，也包括把任务拆成可独立验证的小步，并在每步之后设置人类或自动化校验。任务约束越密集，步长越应该短。
- 这份资料还把验证拆成四层：意图层的需求/设计 Review，实施层的 Code Review，行为层的自动化测试，系统层的集成、性能与安全测试。测试通过只是其中一层，不能替代架构一致性、非功能性约束和设计意图检查。
- 这与既有 Harness 主题互补：此前更强调模型外部控制面和持续控盘，这次补上“为什么不能整包放权、为什么不能只看测试绿灯”的第一性原理解释。

## 与其他概念的关系

- [Agentic Engineering](agentic-engineering.md) 把 Harness 视为复杂约束下可靠协作的必要组成，尤其用于控制概率性输出和错误累积。
- [Context Engineering](context-engineering.md) 关注“Agent 看到了什么”。
- [Prompt Engineering](prompt-engineering.md) 关注“最靠近模型的表达层如何组织”。
- [Spec-driven Development](spec-driven-development.md) 关注“Agent 到底要做什么”。
- [Agent Session Management](agent-session-management.md) 关注“何时该切断、恢复或重置当前会话轨迹”；`spec / handoff / checkpoint` 则是 Harness 维持这些切换不漂移的外部锚点。
- Harness Engineering 继续往前一步，关注“即使 Agent 理解了意图，也如何长期不跑偏”。
- [Jagged Intelligence](jagged-intelligence.md) 解释了为什么不能把局部高表现误读成稳定可靠的全局能力，这正是 harness 需要存在的原因之一。
- [Agent 训练](agent-training.md) 说明类似问题也发生在训练侧，而不只是部署侧。
- [大模型训练流水线](llm-training-pipeline.md) 提供了更大的背景：外层程序本身正在成为训练栈的一部分。

## 对本知识库的启发

- 当前知识库已经采用“索引页 + 小页面”的结构，这与“给 Agent 一张地图”的思路一致。
- 现阶段仍缺少更机械化的维护约束，例如链接检查、来源字段校验、页面孤立性巡检等。
- `wiki/log.md` 可以视为反馈层的一部分，用来记录这类结构性改进何时发生。

## 开放问题

- 是否需要为 `wiki/` 引入自动检查，防止出现孤立页面、缺失来源或失效链接。
- 哪些知识库规则值得固化为脚本或 lint 规则，而不是继续依赖人工审阅。

## 来源

- [Prompt 工程实战指南：从 Prompt 硬编码到可控系统](../sources/2026-04-16-prompt-工程实战指南-从-prompt-硬编码到可控系统.md)
- [从玩具到生产力：用真实项目讲透 AI Agent 的 Harness Engineering](../sources/2026-04-21-从玩具到生产力-用真实项目讲透-ai-agent-的-harness-engineering.md)
- [拥抱 AI 这一年：我的工具、实践和思考](../sources/2026-04-10-拥抱-ai-这一年-我的工具-实践和思考.md)
- [面向“Token 亿万富翁”的极限 Harness Engineering：100 万行代码、每天 10 亿 To...](../sources/2026-04-07-面向-token-亿万富翁-的极限-harness-engineering-100-万行代码-每天-10-亿-to.md)
- [你不知道的大模型训练：原理、路径与新实践](../sources/2026-04-03-you-dont-know-llm-training-principles-paths-new-practices.md)
- [一文讲透：Harness Engineering 即控制论！](../sources/2026-04-17-一文讲透-harness-engineering-即控制论.md)
- [你的 ReAct 智能体正在浪费 90% 的重试机会——以下是解决方法](../sources/2026-04-12-你的-react-智能体正在浪费-90-的重试机会-以下是解决方法.md)
- [抽丝剥茧：深度解析 Hermes Agent 万字系统提示词（System Prompt）构成](../sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md)
- [深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践](../sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md)
- [Hermes 凭什么两个月接棒 OpenClaw？](../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md)
- [你不知道的 Agent：原理、架构与工程实践](../sources/2026-03-19-你不知道的-agent-原理-架构与工程实践.md)
- [The Anatomy of an Agent Harness](../sources/2026-04-06-the-anatomy-of-an-agent-harness.md)
- [从第一性原理思考 Agentic Engineering](../sources/2026-04-23-从第一性原理思考-agentic-engineering.md)
