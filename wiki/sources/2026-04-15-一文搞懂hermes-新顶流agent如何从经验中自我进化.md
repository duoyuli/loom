---
title: 一文搞懂Hermes：新顶流Agent如何从经验中自我进化
type: source
status: summarized
sources:
  - ../../raw/sources/%E4%B8%80%E6%96%87%E6%90%9E%E6%87%82Hermes%EF%BC%9A%E6%96%B0%E9%A1%B6%E6%B5%81Agent%E5%A6%82%E4%BD%95%E4%BB%8E%E7%BB%8F%E9%AA%8C%E4%B8%AD%E8%87%AA%E6%88%91%E8%BF%9B%E5%8C%96.md
source_date: 2026-04-15
source_date_basis: published
published: 2026-04-15
updated: 2026-04-24
---

这是一篇围绕 Hermes Agent `Skills` 闭环的源码解读。它最有长期价值的判断不是“某个开源 Agent 很强”，而是：Agent 的程序性知识可以被拆成创建触发、结构化存储、索引发现、条件激活、渐进式加载、执行中修订与分享传播这条连续工程链，而不是停留在“有记忆”这种抽象说法上。

## 这份资料是什么

作者结合 Hermes 代码片段与运行机制，逐段解释 `Skills` 系统如何把复杂任务经验沉淀为可复用文档，并在后续任务中被重新发现、加载、修订与保护。

## 主要观点

1. Hermes 的“学习”主要不是把更多事实塞进记忆，而是把成功做法提炼成可复用 `Skill`，形成偏程序性的长期资产。
2. 一个 Skill 的生命周期可被拆成 `创建 -> 验证 -> 索引 -> 条件激活 -> 按需加载 -> 执行验证 -> patch`，其中每一步都有明确的工程约束。
3. Skill 的加载策略受到上下文预算和 `Prompt Caching` 约束，因此完整 Skill 不常驻系统提示，而是先以索引形式存在，再在命中时按需注入。
4. `Memory` 与 `Skill` 在 Hermes 中承担不同角色：前者保存稳定事实、偏好和环境细节，后者保存“怎么做”的方法、步骤、陷阱与验证。
5. 这篇文章虽然引用了具体代码片段，但仍是二次解读；文中“唯一一个内置闭环自学习机制”之类强结论，更适合作为作者判断而不是知识库事实。

## 关键内容

### Skill 生命周期的七段链路

- 创建触发由系统提示内嵌规则驱动：复杂任务、棘手错误或非平凡工作流完成后，应主动调用 `skill_manage` 创建 Skill。
- 创建过程不是直接落盘，而是经过名称、分类、 frontmatter、大小、重名、原子写入和安全扫描等多道关卡。
- 写入采用 `tempfile + os.replace()` 的原子替换；扫描放在写入后、回滚在失败后，目的是避免 `TOCTOU` 类竞态。
- Skill 格式采用 `YAML frontmatter + Markdown body`，把结构化元数据和可读 SOP 放在同一文件中。

### 发现、可见性与渐进式加载

- Hermes 用两层缓存维护 Skill 索引：进程内 LRU 处理热路径，磁盘快照处理冷启动。
- 索引的核心作用不是直接提供全部说明，而是让系统提示只保留“名称 + 描述”的摘要层。
- 完整 Skill 由 `skill_view(name)` 按需加载；若 Skill 还依赖参考文档、模板或 API 说明，则作为更深一层资料继续按需读取。
- 哪些 Skill 会出现在索引中，还受到 `platforms`、`requires_*`、`fallback_for_*` 等条件控制，这说明分层不仅是“分几层”，也是“当前让谁可见”。

### Prompt Cache 保护与注入策略

- 资料指出 Hermes 不把完整 Skill 直接并入系统提示，而是作为用户消息注入当前消息流。
- 这一做法的核心权衡，是保护 `Prompt Caching`，避免在长任务中因频繁改写 system prompt 而反复支付高昂上下文成本。
- 这也意味着 Skill 内容虽然承担类似系统约束的作用，但其实现层级并非真正的 system prompt，需要依靠激活说明与上层规则共同维持跟随度。

### 自改进与安全边界

- Hermes 要求 Agent 在发现 Skill 缺步骤、命令过时或遗漏陷阱时立即 patch，而不是把错误留到下次。
- Patch 复用了模糊匹配替换逻辑，以降低 `old_string` 轻微格式偏差导致的失败率。
- 成功 patch 后会清理 Skill 索引缓存，但新内容通常在下一个对话才真正成为默认状态，体现的是“最终一致性”而非“当前会话热更新”。
- Skill 生态的安全防护包括 prompt injection 模式检测、路径穿越防护、环境变量依赖检查、90+ 威胁模式扫描、信任等级策略和目录结构限制。

### Skill 与 Memory 的边界

- 按文章中的 Hermes 提示规则，`Memory` 更接近“稳定事实层”，包括用户偏好、环境细节、工具怪癖和长期约定。
- `Skill` 更接近“程序性知识层”，包括步骤、坑点、验证方式和未来可能复用的方法。
- 这条边界对本知识库有直接启发：并不是所有可复用内容都该写进同一种“记忆”容器里。

## 纵向线索

- 与此前的 [Hermes 系统提示拆解](2026-04-15-抽丝剥茧-深度解析-hermes-agent-万字系统提示词-system-prompt-构成.md) 相比，这篇资料把关注点从“系统提示里装了什么”推进到“经验如何进入并改变后续任务执行”。
- 它把 [Agent 学习闭环](../concepts/agent-learning-loop.md) 从抽象的 `采集 / 提炼 / 应用` 压到更具体的运行时链路：什么时候建、怎样存、如何被发现、何时修正。
- 文章把 Voyager 的 `Skill Library` 当作学术前史，也提示后续若要继续深研，应补正式论文或仓库作为更稳的一手参照。

## 横向线索

- 对 [信息分层设计](../concepts/information-layering-design.md) 而言，这篇资料提供了比 Claude Skills 更工程化的样本：索引层、完整 Skill 层、参考资料层再加上“是否可见”的条件门控。
- 对 [Context Engineering](../concepts/context-engineering.md) 而言，它补强了一个关键实现约束：上下文装载策略要与 `Prompt Caching` 和消息层级共同设计。
- 对 [Agent 记忆系统](../concepts/agent-memory-systems.md) 而言，它进一步澄清了“稳定事实”和“可复用方法”不应混存。
- 对 [Hermes Agent](../entities/hermes-agent.md) 而言，这篇资料和系统提示拆解一起构成了当前知识库对该对象的两条主要证据线。

## 与知识库的相关性

这份资料适合沉淀到以下长期主题中：

- [Hermes Agent](../entities/hermes-agent.md)
- [Agent 学习闭环](../concepts/agent-learning-loop.md)
- [信息分层设计](../concepts/information-layering-design.md)
- [Context Engineering](../concepts/context-engineering.md)
- [Agent 记忆系统](../concepts/agent-memory-systems.md)

## 可靠性判断

- 这是一篇高信息密度的工程型二次解读，适合沉淀结构判断和实现线索。
- 文中多次给出函数名、字段名和代码片段，因此对“作者看到的那套 Hermes 实现”有较强参考价值。
- 但它不是 Hermes 官方设计文档，也不是完整代码审计；代码片段被挑选、压缩和解释过，仍可能遗漏边界条件。
- 资料开头明确说明有 AI 辅助写作与优化，因此应把它视为“带源码证据的解释性文章”，而不是逐字等同于一手规范。

## 受影响页面

- 新增：`wiki/sources/2026-04-15-一文搞懂hermes-新顶流agent如何从经验中自我进化.md`
- 新增：`wiki/entities/hermes-agent.md`
- 更新：`wiki/concepts/agent-learning-loop.md`
- 更新：`wiki/concepts/information-layering-design.md`
- 更新：`wiki/concepts/context-engineering.md`
- 更新：`wiki/concepts/agent-memory-systems.md`

## 开放问题

- 是否需要继续摄入 Hermes 官方仓库、`agentskills.io` 规范或 Voyager 原论文，来验证文章中的实现描述和历史定位。
- Skill 的模糊 patch 与正则安全扫描各自的误判成本有多高，目前还缺少直接证据。
- “索引匹配主要依赖 LLM 自己判断相关性”是否会成为大规模 Skill 库的瓶颈，当前仍停留在作者推测层。

## 来源

- [原始摘录](../../raw/sources/%E4%B8%80%E6%96%87%E6%90%9E%E6%87%82Hermes%EF%BC%9A%E6%96%B0%E9%A1%B6%E6%B5%81Agent%E5%A6%82%E4%BD%95%E4%BB%8E%E7%BB%8F%E9%AA%8C%E4%B8%AD%E8%87%AA%E6%88%91%E8%BF%9B%E5%8C%96.md)
- [原文链接](https://mp.weixin.qq.com/s?__biz=MzI2NDU4OTExOQ==&mid=2247695259&idx=1&sn=30ef327bcbbb3e349abd812da627c5f2&poc_token=HGtI4mmjRIzPofBnJ0OrPUp_XvSEit5G0cejRjEO)
