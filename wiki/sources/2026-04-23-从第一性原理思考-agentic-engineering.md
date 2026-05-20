---
title: 从第一性原理思考 Agentic Engineering
type: source
status: summarized
sources:
  - ../../raw/sources/%E4%BB%8E%E7%AC%AC%E4%B8%80%E6%80%A7%E5%8E%9F%E7%90%86%E6%80%9D%E8%80%83%20Agentic%20Engineering.md
source_date: 2026-04-23
source_date_basis: published
published: 2026-04-23
updated: 2026-04-24
---

这是一篇把 Agentic Engineering 从“AI 辅助写代码”上移到软件工程全链条方法论的长文。它对知识库最有长期价值的部分，不是提出又一个新名词，而是用“信息损耗、LLM 概率性与工作记忆、人类认知稀缺”三条公理，推导出 `Context / Spec / SDLC / Verification / Knowledge / Self-Refinement` 这组工程实践。

## 这份资料是什么

- 资料来自微信公众号长文摘录，作者为魏依承，发布日期为 2026-04-23。
- 文章主线是用第一性原理推导 Agentic Engineering 的目标、约束和最佳实践，并把方法落到一个基于 Skill 的开源框架 `agentic-engineering-framework`。
- 证据边界需要保留：文中包含作者方法论推导、业界研究引用和自有开源框架介绍；它适合沉淀为工程框架与问题地图，不宜把全部实践直接当成已普遍验证的行业标准。

## 主要观点

1. `vibe coding` 适合原型和低约束任务，但生产级系统需要在速度、质量、安全、可维护性等约束中做优化；这正是 Agentic Engineering 与“让 AI 替你写代码”的分界。
2. 软件工程的核心困境可归纳为信息损耗、知识孤岛、认知成本和重复性劳动；AI 会改善这些问题，也会引入概率性输出、私有知识缺失、审查负担暴涨和验证瓶颈。
3. AI 的价值可分为 L1 加速、L2 增强、L3 解锁；真正需要工程化方法的是 L2/L3，而不是简单脚本或样板代码生成。
4. 文章提出三条公理：软件工程是意图转化链；LLM 由上下文决定、输出概率性且工作记忆有限易失；人类认知是稀缺资源。
5. 从这些公理出发，作者反对五个常见假设：把代码库全喂给 AI、认为 AI 不适合复杂系统、把 AI 价值缩成写代码更快、测试通过即可提交、让 AI 像人一样独立完成整包任务。
6. 对应的六条实践是：Context Engineering、基于知识不对称的人机分工、AI 全链条参与、小任务推进与多层次验证、Knowledge as Code、Error-Driven Context Refinement。
7. Skill 被视为落地载体，因为它能按需加载、版本化、被 Review，并支持从 Workflow、Best Practices、Standards、Docs、Troubleshooting 到 Self-Refinement 的模块化组织。

## 关键内容

### 三条公理与意图转化链

- 文章把软件工程压缩成“人类意图 → 自然语言需求 → 结构化设计 → 形式化代码 → 可执行程序”的意图转化链。
- AI 没有消除这条链，只是改变了链上各环节的成本和损耗率；概率性输出还会引入新的“似是而非”损耗。
- 这解释了为什么需求评审、设计文档、代码审查、测试体系在 AI 时代仍然必要：它们本质上是链路中的校验点，而不是旧时代的仪式。

### Context Engineering：高信噪比而非全量灌入

- 文章把上下文质量视为团队最可控的提效杠杆：模型能力常常是给定常量，而团队能改变的是哪些知识以什么形式被 AI 看到。
- Context Engineering 的目标不是喂入更多代码，而是提供最小、高信号的 token 集合，包括 spec、设计文档、架构约束、编码规范、模块契约、Skills、MCP 等。
- 文章特别强调“按需注入”与“渐进式披露”：Rules 适合极轻量全局约束，Skills 更适合专项知识，完整资源只应在任务触发后逐层展开。

### 人机分工：用乔哈里窗处理知识不对称

- 作者把人类/AI 是否知道某类知识，整理成四个象限：开放区、盲区、潜能区、未知区。
- 开放区适合极致自动化；盲区需要上下文注入；潜能区可以利用 AI 的通用知识补齐人的短板；未知区只能协同探索。
- 这比“AI 能不能做复杂系统”更细：复杂系统不是绝对不能用 AI，而是必须先处理私有知识、任务粒度和校验密度。

### AI 全链条参与与 Spec-First

- 文章反对只在编码阶段使用 AI，因为越早期的信息损耗传播越远、修复代价越高。
- AI 在需求澄清阶段更像引导者，通过提问帮助人把模糊意图显式化；在设计阶段更像协作者，帮助分析权衡；在编码与测试阶段才更像执行者。
- `Spec-First` 在这里不只是文档先行，而是把任务从“AI 猜意图”推向“根据明确规格执行”的机制。

### 小任务推进与多层次验证

- 长链路任务里，AI 每一步的未校验偏差都会成为下一步的输入前提；上下文也会随着任务推进不断退化。
- 因此复杂任务应拆成可独立验证的子任务，每步由 AI 执行、人类校验，通过后再进入下一步。
- 文章也区分了验证层次：意图层靠需求/设计 Review，实施层靠 Code Review，行为层靠自动化测试，系统层靠集成、性能与安全测试。

### Knowledge as Code 与错误驱动的自我改进

- 文章把团队共有知识从“文档”提升为像代码一样治理的对象：Best Practices、Standards、Rules 与 Skills 应该版本化、可 Review、可迭代。
- `Error-Driven Context Refinement` 则处理自下而上的知识生长：AI 被纠正后，应诊断根因、检索现有规则、必要时建议创建或更新 Rule/Skill，避免同类错误在下次会话复发。
- 这与当前知识库的“raw 不可变、wiki 回写、log 记录”流程高度同构：错误和新经验不能停在一次性对话里，必须变成可检索、可复用的上下文。

### 基于 Skill 的框架落地

- 文章把最佳实践映射到 Skills：`Workflow` 驱动 SDLC，`Best Practices` 和 `Standards` 提供通用/私有知识，`Docs` 保存 spec 与设计文档，`Troubleshooting` 处理排障，`Self-Refinement` 负责错误回写。
- Skill 的三层加载机制是 `Metadata → Instructions → Resources`，与本库已有的“索引页 → 概念页/来源页 → 原始资料”信息分层思路一致。
- 文章还给出复杂度自适应流程：简单任务可轻量跳过完整 spec，复杂任务则必须先补 spec、确认设计、加载规范，再分步编码。

## 纵向线索

- 相比此前偏 `Prompt / Context / Spec / Harness` 分层的资料，这篇来源提供了一个更上位的推导框架：先定义工程目标和不可变约束，再解释为什么这些分层实践会同时出现。
- 它也把“程序员角色迁移”从情绪判断压回两条稳定方向：成为会编排 AI 的工程师，或往问题定义和价值判断的上游发展。

## 横向线索

- 对 [Agentic Engineering](../concepts/agentic-engineering.md) 而言，这篇来源可以作为立页依据：它给出目标、边界、公理、实践和 Skill 化落地。
- 对 [Context Engineering](../concepts/context-engineering.md) 而言，它补入“最小高信号 token 集合”的一阶推导、渐进式披露和 Rules/Skills 分工。
- 对 [Spec-driven Development](../concepts/spec-driven-development.md) 而言，它把 Spec-First 放进意图转化链，强调早期意图显式化比后期补救更便宜。
- 对 [Harness Engineering](../concepts/harness-engineering.md) 而言，它补入多层次验证和复杂任务的小步校验理由。
- 对 [Agent 学习闭环](../concepts/agent-learning-loop.md) 而言，它补入 `Knowledge as Code` 与 `Error-Driven Context Refinement`。

## 与知识库的相关性

这份来源与当前知识库高度相关，因为它把已经分散沉淀的 `Prompt / Context / Spec / Harness / Skills / Memory / Session` 等主题重新组织成 Agentic Engineering 的系统方法论。它不是要求新建一套平行术语，而是提供一个上位框架：工程师如何在 LLM 的上下文、概率性和记忆限制下，重新设计 SDLC、知识治理与验证流程。

## 可靠性判断

- 作为方法论来源，它结构完整、与现有多篇 Agent 工程资料高度互证，适合沉淀为概念页和若干现有主题的补充视角。
- 但文章的“一阶公理推导”仍是作者的解释框架，不等同于严格数学证明；其中引用的 GitHub、Anthropic、Lost in the Middle、Addy Osmani 等外部材料也需要在更高要求研究中单独回到原文核验。
- 开源框架 `agentic-engineering-framework` 目前在本库中只作为线索保留，暂不因单一来源直接升格为实体页。

## 受影响页面

- [Agentic Engineering](../concepts/agentic-engineering.md)
- [Context Engineering](../concepts/context-engineering.md)
- [Spec-driven Development](../concepts/spec-driven-development.md)
- [Harness Engineering](../concepts/harness-engineering.md)
- [Agent 学习闭环](../concepts/agent-learning-loop.md)
- [Agent 工作流分层框架](../analyses/agentic-workflow-stack.md)

## 开放问题

- `agentic-engineering-framework` 是否已有足够真实项目证据，值得未来单独作为项目实体摄入。
- `Knowledge as Code` 是否应继续挂在 Agent 学习闭环下，还是后续在更多来源支撑后拆成独立概念页。
- 六条实践与已有 `Prompt -> Context -> Spec -> Harness` 方法链的关系，是否需要进一步整理成一张稳定的 synthesis 分析页。

## 来源

- [原始摘录](../../raw/sources/%E4%BB%8E%E7%AC%AC%E4%B8%80%E6%80%A7%E5%8E%9F%E7%90%86%E6%80%9D%E8%80%83%20Agentic%20Engineering.md)
- [原文链接](https://mp.weixin.qq.com/s?__biz=MzI2NDU4OTExOQ==&mid=2247695386&idx=1&sn=7b4ec46bedf2b841680912d6be029a81&poc_token=HMoD62mjME2kZA478uRdDq4UcTCmiSHduVG2-N22)
