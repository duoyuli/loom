---
title: 深度解析 OpenClaw 在 Prompt / Context / Harness 三个维度中的设计哲学与实践
type: source
status: summarized
sources:
  - ../../raw/sources/%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90%20OpenClaw%20%E5%9C%A8%20Prompt%20%20Context%20%20Harness%20%E4%B8%89%E4%B8%AA%E7%BB%B4%E5%BA%A6%E4%B8%AD%E7%9A%84%E8%AE%BE%E8%AE%A1%E5%93%B2%E5%AD%A6%E4%B8%8E%E5%AE%9E%E8%B7%B5.md
source_date: 2026-04-13
source_date_basis: published
published: 2026-04-13
updated: 2026-04-24
---

这是一篇围绕 OpenClaw 源码与运行机制的拆解文章。它对本知识库最有长期价值的判断不是“OpenClaw 很火”，而是把一个可运行 Agent 系统在 `Prompt / Context / Harness` 三层里分别靠什么机制落地，压回到了具体的模块、文件和运行时约束上。

## 这份资料是什么

作者以 OpenClaw 为样本，沿着 system prompt 组装、上下文压缩与记忆管理、Hook 与人在环等外层控制机制，解释一个成熟 Agent 如何在长任务里维持可控性与成本边界。

## 主要观点

1. OpenClaw 的 Prompt Engineering 已不是“写一段 system prompt”，而是 `PromptMode + 工具清单 + 安全准则 + Workspace 文件注入 + 运行时信息` 组成的动态装配系统。
2. OpenClaw 的 Context Engineering 不是单纯“多塞点上下文”，而是通过 `Skills` 渐进披露、对话 `Compaction`、工具结果 `Pruning`、KV Cache 时间窗口治理和双层 Memory 共同维持预算。
3. OpenClaw 的长期记忆不是一个统一大桶，而是拆成每次对话都注入的 `MEMORY.md` 与按需检索的 `memory/YYYY-MM-DD.md`，并配套 `flush / chunk / embedding / BM25 + vector / 时间衰减` 这条完整链路。
4. Harness Engineering 在 OpenClaw 里不是抽象口号，而是 Hook、Guardrail、HEARTBEAT/BOOTSTRAP 规定动作、风险审批和 Human-in-the-loop 这些外层机制的总和。
5. 文中多处给出文件名、函数名和实现描述，但整体仍是解释性二次解读；尤其是“最佳实践”“更符合时间窗口”等结论，适合作为作者判断而不是稳定事实。

## 关键内容

### Prompt：动态组装与文件驱动

- OpenClaw 把 system prompt 拆成 20 多个模块，并用 `full / minimal / none` 三种 `PromptMode` 按场景裁切。
- `AGENT.md`、`SOUL.md`、`IDENTITY.md`、`USER.md`、`TOOLS.md`、`HEARTBEAT.md`、`BOOTSTRAP.md`、`BOOT.md` 共同构成文件驱动的运行时配置面。
- 这套设计把“人格、规则、用户画像、环境说明、启动脚本”从代码硬编码中抽离出来，变成可直接编辑的 Markdown 资产。
- 资料还特别强调了 Prompt 的极简主义写法：短句、硬约束和高密度表达，目的是把更多 token 留给业务上下文而不是解释性废话。

### Context：扩展、压缩、修剪与双层记忆

- `Skills` 采用“先注入名称和描述，再按需读取 `SKILL.md`”的渐进式披露，避免能力扩展直接导致上下文爆炸。
- 对话历史超长时，OpenClaw 通过 `Compaction` 做分块、多阶段摘要、失败回退和标识符保留；工具返回超长时，则通过 `Pruning` 做规则化裁剪。
- 文章把这两者区分得很清楚：`Compaction` 用 LLM 保留历史关键信息，`Pruning` 则直接牺牲部分细节换预算。
- Memory 采用双层设计：`MEMORY.md` 承接高价值、持续性的事实和偏好，`memory/YYYY-MM-DD.md` 承接每日上下文细节；后者只在检索时进入工作记忆。
- 资料给出了一条具体召回链：日记切片、SQLite 存储、嵌入索引、`BM25 + vector` 双路召回、按行精读，以及每日记忆按半衰期衰减。

### Harness：Hooks、规定动作与人在环

- 文章把 Harness 定义为包在模型外部的运行环境与约束系统，核心手段是接口、Hook、Guardrail 与评测/纠偏机制，而不是把所有步骤写死成 Workflow。
- 作者特别区分了 Workflow 与 Harness：前者是人主导的固定路径，后者保留 Agent 的自主规划，但通过外层边界提高成功率。
- OpenClaw 的 Hook 系统横跨工具调用、压缩前后等关键节点，允许开发者在“执行前预防”和“执行后纠偏”之间插入自定义逻辑。
- `HEARTBEAT.md` 与 `BOOTSTRAP.md` 这种文件并不是普通文档，而是把周期巡检、初始化引导等动作显式写进系统约束。
- 人在环同样属于 Harness：高风险操作需要暂停等待用户确认，这不是交互细节，而是最终控制权的结构性设计。

## 纵向线索

- 相比 [你不知道的 Agent：原理、架构与工程实践](2026-03-19-你不知道的-agent-原理-架构与工程实践.md) 这类总纲式综述，这篇资料把 OpenClaw 从“案例”推进成可拆的运行时对象。
- 它也把此前知识库里的 `Prompt -> Context -> Harness` 方法链，从概念层回收到了更具体的实现侧样本。
- 文中多次引用源码路径，说明后续若继续深研 OpenClaw，优先级应转向官方仓库或文档，而不是继续堆叠二次解读。

## 横向线索

- 对 [Context Engineering](../concepts/context-engineering.md) 而言，这篇资料提供了 `PromptMode`、压缩/修剪分工、双层 Memory 和文件注入这组实现样本。
- 对 [Harness Engineering](../concepts/harness-engineering.md) 而言，它补上了 Hook、Workflow vs Harness、`HEARTBEAT.md` 和 Human-in-the-loop 这组更具体的外层约束机制。
- 对 [信息分层设计](../concepts/information-layering-design.md) 而言，`文件名/摘要层 -> 完整 Markdown 文件 -> 搜索或原始细节` 形成了比单纯 Skills 更宽的资源分层样本。
- 对 [Agent 记忆系统](../concepts/agent-memory-systems.md) 而言，这篇资料补了 OpenClaw 的 `MEMORY.md + daily notes` 双层记忆、召回链和时间衰减实现。
- 对 [OpenClaw](../entities/openclaw.md) 而言，这篇是当前知识库里最系统的一条对象证据线。

## 与知识库的相关性

这份资料适合沉淀到以下长期主题中：

- [OpenClaw](../entities/openclaw.md)
- [Context Engineering](../concepts/context-engineering.md)
- [Harness Engineering](../concepts/harness-engineering.md)
- [信息分层设计](../concepts/information-layering-design.md)
- [Agent 记忆系统](../concepts/agent-memory-systems.md)

## 可靠性判断

- 这是一篇高信息密度的工程型二次解读，最大的价值在于把零散实现线索组织成 `Prompt / Context / Harness` 三层框架。
- 文章给出大量文件名、函数名和模块名，因此对“作者读到的那版 OpenClaw”有较强参考价值。
- 但它不是 OpenClaw 官方设计文档，也不是完整源码审计；“最佳实践”“更先进”“更适合当前窗口”等判断都带有作者立场。
- 其中涉及近期版本动态和安全能力增强的表述，也应视为时间敏感信息，后续最好用官方 release note 或仓库变更核对。

## 受影响页面

- 新增：`wiki/sources/2026-04-13-深度解析-openclaw-在-prompt-context-harness-三个维度中的设计哲学与实践.md`
- 新增：`wiki/entities/openclaw.md`
- 更新：`wiki/concepts/context-engineering.md`
- 更新：`wiki/concepts/harness-engineering.md`
- 更新：`wiki/concepts/information-layering-design.md`
- 更新：`wiki/concepts/agent-memory-systems.md`

## 开放问题

- 是否需要继续直接摄入 OpenClaw 官方仓库、README、记忆工具与 Hook 机制的原始实现，来替代当前主要依赖公众号解读的状态。
- `MEMORY.md` 固定注入、日记按需搜索与时间衰减的组合，在更大规模的长期使用里会怎样退化，目前还缺少一手证据。
- Hook 机制与 Skills 市场安全策略的真实误判成本、维护成本和用户心智负担，当前仍停留在经验判断层。

## 来源

- [原始摘录](../../raw/sources/%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90%20OpenClaw%20%E5%9C%A8%20Prompt%20%20Context%20%20Harness%20%E4%B8%89%E4%B8%AA%E7%BB%B4%E5%BA%A6%E4%B8%AD%E7%9A%84%E8%AE%BE%E8%AE%A1%E5%93%B2%E5%AD%A6%E4%B8%8E%E5%AE%9E%E8%B7%B5.md)
- [原文链接](https://mp.weixin.qq.com/s?__biz=MzIzOTU0NTQ0MA==&mid=2247559511&idx=1&sn=64e933b0264e47f0940e693e315e0c82&poc_token=HLRJ4mmjZRGKhzktk5sLb6bf7TX_rToe6h0cLIIy)
