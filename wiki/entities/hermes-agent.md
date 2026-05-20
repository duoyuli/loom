---
title: Hermes Agent
type: entity
entity_type: project
status: active
sources:
  - ../sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md
  - ../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md
  - ../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md
updated: 2026-04-17
---

Hermes Agent 是当前知识库里第一个值得单独立页的稳定对象，因为它已经不止一次作为“真实 Agent 如何组织上下文与经验”的实现样本出现。对本库而言，它的长期价值不是产品热度，而是把 `Skills`、记忆、系统提示、项目约束和缓存边界这些抽象主题压回到一个可追踪的运行时对象上。

## 纵向脉络

- 当前知识库中的第一条证据线，来自 [Hermes 系统提示拆解](../sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md)：它展示 Hermes 如何把身份、记忆规则、记忆快照、技能索引、`AGENTS.md`、平台提示和会话元信息拼成一份真实 system prompt。
- 第二条证据线来自 [Hermes Skills 自我进化拆解](../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md)：它进一步说明 Hermes 不只是在会话开始时装入上下文，还会把复杂任务经验沉淀成 Skill，再通过索引、条件激活、按需加载和 patch 机制持续回写。
- 按该资料的说法，Hermes 对 Voyager `Skill Library` 的学术思路做了更现实的工程化处理，开始面对缓存、并发、安全扫描、路径边界和跨平台条件这些论文里通常不会展开的实现问题。
- 目前本库关于 Hermes 的材料都来自解释性文章和源码摘录，还没有直接摄入官方仓库、设计文档或原始 commit，因此对象判断仍应保留证据边界。

## 横向位置

- 对 [Context Engineering](../concepts/context-engineering.md) 而言，Hermes 是“上下文不只是 prompt，而是可分层装配、按需注入、受缓存边界约束的运行时环境”的具体例子。
- 对 [信息分层设计](../concepts/information-layering-design.md) 而言，Hermes 展示了 `技能索引 -> 完整 Skill -> 支撑文件` 这条更可操作的分层路径。
- 对 [Agent 学习闭环](../concepts/agent-learning-loop.md) 而言，Hermes 把“学习”从泛泛的资料沉淀，压到程序性知识资产的创建、使用和自修复。
- 对 [Agent 记忆系统](../concepts/agent-memory-systems.md) 而言，Hermes 把 `Memory` 与 `Skill` 明确拆成“是什么”和“怎么做”两类持久层。
- 对 [Harness Engineering](../concepts/harness-engineering.md) 而言，Hermes 也提供了安全扫描、回滚、工具条件和提示规则如何共同约束 Agent 的样本，不过这一线当前在知识库里仍未单独展开。
- 对 [OpenClaw](openclaw.md) 而言，Hermes 也是当前最直接的比较对象之一；现有对比材料说明，两者真正差异更集中在默认自动化程度、记忆触发时机和确定性 Harness 的取舍，而不是功能清单本身。
- 相关比较入口见 [Hermes 与 OpenClaw：默认自动化、记忆作用域与 Harness 取舍](../analyses/hermes-vs-openclaw-default-automation-comparison.md)。

## 当前判断

- Hermes 之所以适合成为第一个实体页，是因为它已经把多个概念页连接起来：系统提示 anatomy、信息分层、学习闭环、记忆分层和缓存约束都能在它身上落到具体实现。
- 当前最值得保留的不是“它是不是最强 Agent”，而是三组实现判断：`索引常驻 + 完整内容按需加载`、`Skill patch 应在使用中立即发生`、`Skill 不宜与稳定事实型 Memory 混成同一容器`。
- 新补的 Hermes / OpenClaw 对比还说明，Hermes 的对象差异重点可能不在功能表，而在它把技能生成、记忆写入和风险控制前置成更强的默认自动化规则；这更像“由系统替用户承担多少判断”的重新分配。
- 同时要保留两层不确定性：一是现有材料大多来自二次解释，不是官方规范；二是部分强结论如“唯一一个内置闭环自学习机制”或具体星标热度都不宜直接固化为长期事实。

## 开放问题

- 是否需要继续摄入 Hermes 官方仓库、README、技能格式规范或实际代码路径，来替代当前主要依赖公众号文章的状态。
- Hermes 与 Claude Skills、Codex CLI、OpenHands 等系统在技能组织、记忆边界和缓存策略上的异同，是否值得在现有 Hermes / OpenClaw 比较页之外继续扩成更大的对象谱系。
- 如果后续继续补 Hermes 资料，应该优先围绕对象本身扩展，还是先拆出更稳定的 `procedural memory` 概念页。

## 来源

- [抽丝剥茧：深度解析 Hermes Agent 万字系统提示词（System Prompt）构成](../sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md)
- [一文搞懂Hermes：新顶流Agent如何从经验中自我进化](../sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md)
- [Hermes 凭什么两个月接棒 OpenClaw？](../sources/2026-04-15-hermes-凭什么两个月接棒-openclaw.md)
