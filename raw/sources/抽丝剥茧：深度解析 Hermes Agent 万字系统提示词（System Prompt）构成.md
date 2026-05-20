---
title: "抽丝剥茧：深度解析 Hermes Agent 万字系统提示词（System Prompt）构成"
source: "https://x.com/LufzzLiz/status/2044258384556556743"
author:
  - "[[@LufzzLiz]]"
published: 2026-04-15
created: 2026-04-15
description: "没想到吧，Hermes agent 也可能有万字的系统提示词，且看岚叔带你完整拆解。同时教你一招降低50% tokens 的小妙招本文依然是实践操作类文章、欢迎兄弟们大力支持～0、准备阶段如果你需要自己上手实践一下，可以看下岚叔的操作方式。也很简单，要想完整分享，就需要完整的将H..."
tags:
  - "clippings"
---
![图像](https://pbs.twimg.com/media/HF6paiXa4AAvElB?format=jpg&name=large)

> 没想到吧，Hermes agent 也可能有万字的系统提示词，且看岚叔带你完整拆解。同时教你一招降低50% tokens 的小妙招

> 本文依然是实践操作类文章、欢迎兄弟们大力支持～

# 0、准备阶段

如果你需要自己上手实践一下，可以看下岚叔的操作方式。

也很简单，要想完整分享，就需要完整的将Hermes agent 发给模型的提示词全量导出。这里使用岚叔自己开发的神器model-box: [https://github.com/cclank/modelbox](https://github.com/cclank/modelbox) ，可以导出各种agent 的提示词

安装部署直接让你的hermes 照着项目[README.md](https://github.com/cclank/modelbox/blob/main/README.md) 文档做即可，注意选择 mock 模式启动即可

使用方式：

如图，启动后，我们使用/models 切换到modelbox即可

![图像](https://pbs.twimg.com/media/HF6XnOVasAASzLE?format=jpg&name=large)

长期关注过岚叔的都知道，这个是我们debug 出完整系统提示词的神器，之前我们做了openclaw 完整系统提示词解读，可见：

当然了，这一步是选做的，如果你不想手到分享，直接看岚叔后续内容即可

# 一、系统提示词组成结构

首先我们剥开源码可以看见：Prompt Builder 位于 agent/prompt\_builder.py，负责**组装系统提示**——身份定义、平台提示、技能索引、上下文文件。所有函数无状态，由 AIAgent.\_build\_system\_prompt() 调用拼接各模块。

我们整理如下图所示：

![图像](https://pbs.twimg.com/media/HF6YDS-asAAXjlH?format=jpg&name=large)

当然我们导出真实的提示词可以看到：

![图像](https://pbs.twimg.com/media/HF6Y2EMasAIOIqv?format=jpg&name=large)

总结如下：

```text
┌─────────────────────────────────────────────┐
│ 1. SOUL.md — Agent 身份/人格                   │
│ 2. 记忆使用指南 — Memory 工具使用规范            │
│ 3. MEMORY — 持久化记忆快照（冻结）              │
│ 4. USER PROFILE — 用户画像快照（冻结）          │
│ 5. Skills 索引 — 全量技能列表                   │
│ 6. AGENTS.md — 项目级开发指南                   │
│ 7. 会话元数据 — 时间/模型/Provider              │
│ 8. 平台提示 — Telegram 特定行为                 │
│ 9. 会话上下文 — 来源/群组/投递选项              │
└─────────────────────────────────────────────┘
```

总字符数：**~36,700 chars**（~10K tokens），看到这我有点震惊，居然和openclaw 一样都过万了？！

我们接着一层层看，找到内鬼🐶

## 第一层 SOUL.md — Agent 身份（~500 chars）

> 来自 ~/.hermes/SOUL.md，定义 Agent 人格

这个文件是我们自己定义的,可以看到就在开头：

![图像](https://pbs.twimg.com/media/HF6Zn05asAMpB0u?format=jpg&name=large)

## 第 2 层：Memory 使用指南（~600 chars）

硬编码在 \_build\_system\_prompt() 中，告诉模型**如何使用 memory 工具**：

- 用 memory 工具保存持久事实（用户偏好、环境细节、工具特性）
- **不要**保存任务进度、完成日志（用 session\_search 回忆）
- 发现非平凡工作流 → 保存为 Skill
- 使用中发现 Skill 过时 → 立即用 skill\_manage(action='patch') 修复

## 第3层： MEMORY 快照（~3,725/4,000 chars，93% 满）

> 看过岚叔上篇文章的就知道，咱们调整成4000 chars 了，看来快满了。。。

> **冻结快照**，会话内不变。包含 7 个主题板块：

默认profile路径：~/.hermes/memories/MEMORY.md

![图像](https://pbs.twimg.com/media/HF6awDjaYAAXVeQ?format=png&name=large)

可以看到是一些关于岚叔的关键信息，最后Agent world 亮了，是我之前给Hermes 测试人格，挂的coze 社区的api ，没想到key 保存只MEMORY了，这个不太友好，需要优化！

## 第4层：USER PROFILE 快照（~682/1,375 chars，49%）

也是我们自定义的文件,每个人不一样

默认profile路径：~/.hermes/memories/USER.md

也是冻结快照，描述**用户是谁**：

- 当前模型配置概览
- **Wiki 维护要求**：极高精度，必须源码验证，不允许编造
- **GitHub 排版偏好**：纯 Markdown，禁止 Obsidian wikilinks，禁止 HTML 居中标签

## 第5层: Skills 索引（~5,000 chars）

震惊了吧，skill 索引居然 5000 chars

完整技能分类列表，涵盖 **~80+ 个 Skill**，按分类组织：

> 每个只展示名称 + 描述前缀（截断），模型需要时调用 skill\_view(name) 加载完整内容。

这个是岚叔的新机器，基本都是官方的skill，还是很丰富的

![图像](https://pbs.twimg.com/media/HF6bnGEbQAALjsD?format=png&name=large)

## 第6层： AGENTS.md — 项目开发指南（~20,300 chars，被截断到 14K+4K）

4K + 4K 代表啥：

> AGENTS.md 原文（20,360 chars） ├── 前 14,000 chars → 保留（项目结构、核心 API、工具开发指南等重要内容） ├── 中间 2,360 chars → 丢弃，替换为截断提示 └── 后 4,000 chars → 保留（Known Pitfalls、测试指南等收尾内容）

- 单文件上限：**20,000 chars**
- 截断比例：头部 **70%**（14K），尾部 **30%**（无显式定义，即剩余部分约 4-6K）
- AGENTS.md 有 20,360 chars，超了 360 chars，所以中间被砍了一小段

这样设计的原因是：文件开头通常最重要（目录结构、核心 API），结尾也有价值（注意事项、测试），中间往往是细节可以牺牲。 与模型看内容一样。。

可以看到，这是 hermes-agent 仓库根目录的 AGENTS.md，占了系统提示的**近一半**。这里实际是有问题的，Hermes 可以不带这一堆内容的。

内容包括：

1. **项目结构**：完整目录树 + 文件职责说明
2. **文件依赖链**：registry → tools → model\_tools → run\_agent
3. **AIAgent 类**：构造参数、chat() 和 run\_conversation() 接口、核心循环伪代码
4. **CLI 架构**：Rich/prompt\_toolkit、KawaiiSpinner、Slash Command Registry（CommandDef）
5. **添加工具的三步指南**：tools/your\_tool.py → model\_tools.py → [toolsets.py](https://toolsets.py/)
6. **添加配置**：DEFAULT\_CONFIG + OPTIONAL\_ENV\_VARS + 三种配置加载器
7. **Skin/Theme 系统**：完整的皮肤自定义 API
8. **Known Pitfalls**：6 个已知坑（硬编码路径、simple\_term\_menu Bug、ANSI 转义泄漏等）
9. **测试指南**：pytest 命令

**岚叔认为如果你不是不是开发 Hermes，或者做Hermes的升级维护啥，那么这将近5k的token可以不用的**

最佳方式： 针对不同 agent 配置TERMINAL CWD 路径

![图像](https://pbs.twimg.com/media/HF6b52NbIAArkOp?format=jpg&name=large)

告诉hermes： 请将主agent cwd 配置为～ ，然后重启\`

补充说明：关于这个路径你可以自己选择哈，当然你也可以自定义AGENTS.md，没必要把那一大坨都加进去～

项目文件（四选一) ： 按优先级选择其一： .hermes.md → AGENTS.md → CLAUDE.md → .cursorrules

> hermes 的AGENTS.md 与 OpenClaw 的区别很大，前者是项目级别，OpenClaw 是全局的 个人认为这个 hermes 做的更好，相当于实现了AGENTS.md 的动态加载 OpenClaw 就不行，怎么都得加载AGENT.md 等相关文件，不够灵活

![图像](https://pbs.twimg.com/media/HF6b9fna0AAzsAX?format=jpg&name=large)

直接看效果： 瞬间少了一半（当然你需要新开session ，重新加载你的上下文）

![图像](https://pbs.twimg.com/media/HF6cJeCasAMGSlg?format=jpg&name=large)

这样我们日常每次对话都可以省5k token 同时也满足了 Hermes 的高效运维 👍

## 第7层：会话元数据（~200 chars）

```text
Conversation started: Monday, April 13, 2026 09:58 PM
Model: qwen3.6-plus
Provider: alibaba
```

加上模型自我认知指令："When asked what model you are, always answer based on this information"

## 第8层：平台提示--Telegram（~200 chars）

这一层叫平台提示：比如岚叔用的telegram ，会传telegram 相关提示，如果是discord 就传discord 的，这块Hermes 做的很好，不同平台的定制化告诉了模型。

- 不使用 Markdown（Telegram 不渲染）
- 媒体文件投递方式：MEDIA:/path/to/file
- 图片 URL 用 !\[alt\](url) 会自动作为原生照片发送

> 我怎么感觉telegram 实际是支持md渲染的呀。。。

## 第9层： 会话上下文元信息（~400 chars）

系统提示词最后一段：告诉模型"你现在在哪"：

- **来源**：Telegram 群 "爱马仕小分队"，thread 205
- **会话类型**：多用户线程，消息带 \[sender name\] 前缀
- **已连接平台**：local + telegram
- **Home Channel**：telegram Home (ID: xxx')
- **定时任务投递选项**：origin / local / telegram

![图像](https://pbs.twimg.com/media/HF6cecla0AAmw8m?format=jpg&name=large)

## 系统提示词之后的内容

**1\. 对话消息**

这里是我们与hermes 每轮对话内容，都会放到这里

**2.**工具列表（30个）：

![图像](https://pbs.twimg.com/media/HF6dJkoaUAArfxm?format=png&name=large)

这 30 个工具**就是按需加载后的结果**，Hermes 总共注册了 **51 个工具**，但会话只加载了 **30 个**。

缺少的工具包括：

```text
| 未加载                    | 原因                |
| ---------------------- | ----------------- |
| \`mcp_*\`（3 个）           | 没配 MCP server     |
| \`rl_*\`（10 个）           | RL 训练工具集，未启用      |
| \`homeassistant_*\`（4 个） | 未配 Home Assistant |
| \`image_generation\`     | 未配图像生成 API Key    |
| \`mixture_of_agents\`    | MoA 工具集未启用        |
| \`send_message\`         | 可能当前平台配置未启用       |
```

**筛选机制**在 model\_tools.py 的 \_discover\_tools() 里，通过 check\_fn（检查 API Key 是否存在）和 enabled\_toolsets/disabled\_toolsets 来决定最终加载哪些工具。

3\. 统计信息如下：

4.13未优化前

![图像](https://pbs.twimg.com/media/HF6dbD4asAIokH9?format=png&name=large)

4.15 优化后：

![图像](https://pbs.twimg.com/media/HF6dxW8asAARr7C?format=jpg&name=large)

可以看到，是不是提示词少了一大办，恭喜🎉

# 后记

本文通过导出全量Hermes 提示词发现，里面有一些玄机：

比如skill，Hermes 是有很好的skill 自进化逻辑，但是也会造成skill 泛滥的问题，这个估计后面得需要考虑优化。

比如AGENTS.md 文件加载问题，如果你的Hermes 是默认启动的，那么很可能会加载进 ~/.hermes/hermes-agent/AGENTS.md ,这个文件太大了，大到都被截断了。。足足有5k token 之多，需要按岚叔的方式优化下。当然，省事的话可以直接缩减这个文件也可以，但是要注意后续可能会因为更新被覆盖

感谢大家看到这里，希望对您有所帮助，起三连支持～

💗

公众号版本，可参考：[https://mp.weixin.qq.com/s/gM6mJsH0ay4Z7jkEBjGE0w](https://mp.weixin.qq.com/s/gM6mJsH0ay4Z7jkEBjGE0w)