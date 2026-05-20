---
title: 抽丝剥茧：深度解析 Hermes Agent 万字系统提示词（System Prompt）构成
type: source
status: summarized
sources:
  - ../../raw/sources/%E6%8A%BD%E4%B8%9D%E5%89%A5%E8%8C%A7%EF%BC%9A%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90%20Hermes%20Agent%20%E4%B8%87%E5%AD%97%E7%B3%BB%E7%BB%9F%E6%8F%90%E7%A4%BA%E8%AF%8D%EF%BC%88System%20Prompt%EF%BC%89%E6%9E%84%E6%88%90.md
source_date: 2026-04-15
source_date_basis: published
published: 2026-04-15
updated: 2026-04-24
---

这是一篇围绕 Hermes Agent 运行时系统提示的拆解笔记。它最有长期价值的判断不是“某个 Agent 的 prompt 很长”，而是：一个可工作的 Agent 上下文通常是多层拼装物，身份、记忆、技能索引、项目级约束、平台元信息和会话状态都会一起进入系统提示，而这些层本身就是需要被预算、裁剪和治理的运行时结构。

## 这份资料是什么

作者通过导出 Hermes Agent 的完整提示词与结合源码阅读，逆向整理了 Hermes 在一次真实会话里如何构造系统提示，包括 prompt builder 路径、各层内容、字符规模与部分优化策略。

## 主要观点

1. Agent 的系统提示不是单一人格设定，而是由身份、记忆规范、冻结快照、技能索引、项目说明、平台提示和会话元数据共同拼装的上下文包。
2. 长期保留的信息层会显著吞掉 token 预算；在作者案例里，项目级 `AGENTS.md` 几乎占了整个系统提示的一半。
3. `AGENTS.md`、技能目录、memory 使用规范这些“外层程序说明”本身就是 harness 的一部分，不能只考虑内容完整性，还要考虑装载策略与上下文成本。
4. 平台与会话元信息会直接改变 Agent 的行为边界，因此同一个 Agent 在不同渠道、不同线程里并不处于同一上下文条件。
5. 这份拆解更像单个 Hermes 部署实例的工程观察，而不是官方规范；适合用来理解结构，不适合把其中的具体数值直接外推为所有 Agent 的通用事实。

## 关键内容

### 九层系统提示结构

作者把 Hermes 实际运行时系统提示整理为九层：

1. `SOUL.md`：Agent 身份与人格设定。
2. memory 使用指南：告诉模型什么值得写入长期记忆、什么不该写。
3. `MEMORY` 快照：冻结的持久事实。
4. `USER PROFILE` 快照：用户画像与偏好。
5. Skills 索引：按分类列出可加载技能。
6. `AGENTS.md`：项目级开发指南与约束。
7. 会话元数据：时间、模型、provider。
8. 平台提示：如 Telegram 的交互约束。
9. 会话上下文：群组、线程、投递方式等当前环境信息。

这说明 Agent 真正“看到”的并不是一段提示词，而是一整套分层拼接的运行时环境。

### 冻结快照与会话变量

- `MEMORY` 与 `USER PROFILE` 在会话内被视为冻结快照，承担长期偏好和稳定事实。
- 平台提示与会话上下文则是即时变量，会随 Telegram / Discord / 本地终端等运行位置变化。
- 这类分层让“长期记忆”“项目约束”“当前环境”在同一系统提示里叠加，也意味着排查行为差异时不能只盯着最后一轮用户输入。

### Token 预算与裁剪策略

- 作者导出的完整系统提示约为 `36,700 chars`，约 `10K tokens` 量级。
- 其中 `AGENTS.md` 原文约 `20,360 chars`，超出单文件上限后会保留头尾、中间截断。
- 在该案例里，项目级 `AGENTS.md` 是最重的一层，也是作者认为最值得优化的一层。
- 作者给出的经验做法，是通过调整 Hermes 的工作目录或项目提示文件选择，避免每次会话都加载过长的仓库级说明，从而显著降低 token 消耗。

### 技能与工具装载

- 资料显示 Hermes 会把技能目录先压缩成索引，再按需加载完整技能内容。
- 工具层也不是“注册了就全给模型”，而是会根据配置与启用条件筛掉一部分工具集。
- 这类按需发现机制说明，Agent 的能力边界不仅受模型影响，也受运行时装载策略影响。

## 纵向线索

- 这份资料把一次 Agent 会话的上下文形成过程，拆成从人格、记忆、项目约束到平台与线程环境的连续链路。
- 它提供了一个比“Prompt / Context / Harness”更贴近实现层的观察角度：这些抽象层最终会落成具体文件、具体拼装顺序和具体装载预算。
- 从静态人格到动态平台元信息的层层叠加，也说明 Agent 行为不是单点决定，而是多层运行时条件共同塑形的结果。

## 横向线索

- 资料把 `SOUL.md`、`MEMORY`、Skills、`AGENTS.md` 与平台提示放进同一张系统提示地图里，适合和现有的 [Context Engineering](../concepts/context-engineering.md) 对照阅读。
- 它也为 [Harness Engineering](../concepts/harness-engineering.md) 提供了更细的实现样本：项目约束、技能演化规则与记忆使用规范，都会以提示层形式进入运行时。
- 文中对 Hermes 与 OpenClaw 在 `AGENTS.md` 加载方式上的对照，提供了一个“全局规则 vs 项目规则动态装载”的比较切面，但当前仍停留在作者经验层，不宜过度外推。

## 与知识库的相关性

这份资料的长期价值，在于把当前知识库里偏抽象的方法页拉回到一个真实 Agent 的运行时 anatomy。对本库最有价值的稳定主题是：

- [Context Engineering](../concepts/context-engineering.md)
- [Harness Engineering](../concepts/harness-engineering.md)

## 可靠性判断

- 这是一份高信息密度的实践型拆解，适合用来理解 Agent 系统提示的构成方式。
- 它的直接证据主要来自作者导出的 Hermes 提示词和对应源码位置，因此对“这一实例是怎么拼装起来的”有较强参考价值。
- 但它并不是 Hermes 的正式设计文档，也不是跨 Agent 框架的通用规范；其中 token 规模、目录长度和平台配置强烈依赖具体部署环境。
- 文中对优化收益、平台行为和框架优劣的评价，仍带有作者个人工作流与实测环境的偏好色彩。

## 受影响页面

- 新增：`wiki/sources/2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md`
- 更新：`wiki/concepts/context-engineering.md`
- 更新：`wiki/concepts/harness-engineering.md`

## 开放问题

- 是否需要为“系统提示词 anatomy”单独立一个概念页，还是继续留在 `Context / Harness` 两页之下。
- 项目级规则应该更多留在长文档里，还是进一步拆成可按需装载的小页与索引。
- 当知识库本身变大后，是否也需要像 Hermes 一样明确区分“冻结快照”“项目规则”“会话变量”三类上下文。

## 来源

- [原始摘录](../../raw/sources/%E6%8A%BD%E4%B8%9D%E5%89%A5%E8%8C%A7%EF%BC%9A%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90%20Hermes%20Agent%20%E4%B8%87%E5%AD%97%E7%B3%BB%E7%BB%9F%E6%8F%90%E7%A4%BA%E8%AF%8D%EF%BC%88System%20Prompt%EF%BC%89%E6%9E%84%E6%88%90.md)
- [原文链接](https://x.com/LufzzLiz/status/2044258384556556743)
- [文末提到的公众号版本](https://mp.weixin.qq.com/s/gM6mJsH0ay4Z7jkEBjGE0w)
