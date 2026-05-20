---
title: Prompt 工程实战指南：从 Prompt 硬编码到可控系统
source: https://mp.weixin.qq.com/s/a_Jxm5NsErP4RYx8DaxYqQ
author:
  - "[[yan5xu]]"
published: 2025-12-10
created: 2026-04-16
description: 如何围绕 Prompt 建立一套工程化的迭代机制，让它在和真实数据、真实反馈的反复碰撞中“长”出来，同时又尽量可控、可回滚、可验证。
tags:
  - clippings
---
yan5xu *2025年12月10日 10:55*

## 导读：这篇文章写给谁？

**这篇文章适合：**

- • 已经在用 LLM（ChatGPT、Claude 等）做 demo 或小工具的你；
- • 想把“能跑的玩具”升级成 **可维护、可评估、可演进的产品系统** 的工程师 / 技术 PM；
- • 在团队里负责推动 LLM 应用、搭内部平台的同学。

**你可能正面临这些问题：**

- • 一条 Prompt 写在代码里能跑，但一改就“开盲盒”：
- • 同一个输入，多次结果不一样；
	- • 改好一个场景，另一个场景变差。
- • 所有需求都变成“麻烦工程师改一下那条字符串”，  
	PM / 内容同学连措辞都改不了。
- • 没有数据、没有评估、没有回归，  
	每次上线都在赌运气。

---

## 一、误区不在“技巧”，而在“一次写对”的幻觉

很多人在聊 Prompt 工程时，注意力都停留在一件事上：

> 怎么写出一条“足够厉害”的 Prompt：  
> 多堆几个小技巧，多换几种模板，多抠一点文案。

隐含的前提是： **Prompt 是可以一次性写对的** 。  
但在真实的 LLM 应用里，这个前提几乎是错的。

- • 模型对输入极度敏感，同一条 Prompt 换个顺序、换几个词，结果就飘；
- • 同一条 Prompt，在不同模型上效果差异很大；
- • 同一场景，随着你接入更多样的真实数据，好的 Prompt 往往是 **“长出来的”** ——  
	靠一轮轮小改和反馈打磨出来，而不是闭门造车一次成型。

换句话说，你面对的是一个 **近似混沌的系统** ，而不是一个传统意义上“函数确定、写好就完”的组件。

所以真正的难点，不在于：

> “如何一次性写出完美 Prompt？”

而在于：

> **如何围绕 Prompt 建立一套工程化的迭代机制，让它在和真实数据、真实反馈的反复碰撞中“长”出来，同时又尽量可控、可回滚、可验证。**

我想给「Prompt 工程」一个工作定义：

> **Prompt 工程的本质，不只是 Prompt 写作技巧，而是用可度量、可迭代、可验证的工程方法，去管理这种对输入高度敏感、近似混沌的 LLM 系统，使其输出收敛为可预期、可复现的工程结果。**

为了让这件事具体可感，我们不妨看看 **小明所在的一个小团队** ，  
他们从 0 到 1 做一款 **“YouTube Summary”** 产品（输入视频链接 → 输出总结），一路经历：

1. 1\. 原型期：只会在代码里把 Prompt 硬编码成一条长字符串
2. 2\. 规范期：把 Prompt 抽出来变成模板 + CLI
3. 3\. 可度量期：用一批 golden case 做回归评测
4. 4\. 架构期：从“超大 Prompt”拆成“分类 + 各自专用模板”
5. 5\. 规格期：为每种 Prompt+模型+参数组合定义独立的请求规格（spec）
6. 6\. 自适应期：在 spec 之上探索自动化的 Prompt / 参数优化（如结合 DSPy 思路）

你会看到的，不是“六个高级技巧”，而是一条 **从手工调 Prompt，走向系统可控** 的演化路径。

---

## 二、总览：六个阶段在工程上的意义

先给一张“路线图总览表”，方便你对号入座：

| 阶段 | 主要产物 | 关键工程动作 | 直接收益 |
| --- | --- | --- | --- |
| 1 | Prompt 硬编码 | 把长 Prompt 写死在代码里 | 能跑起来，能 demo |
| 2 | 模板 + CLI | Prompt 抽到 `.j2` ，本地可渲染 & 调试 | 非工程同学可参与改 Prompt |
| 3 | golden case + 回归评估 | YAML 描述 case + 自动覆盖评测 | 改 Prompt 有“回归安全网” |
| 4 | 分类 + 多模板 | 先分类，再按类型选模板 | 避免超大 Prompt，场景解耦 |
| 5 | 请求规格（Spec） | 为每种 Prompt+模型+参数组合定义一个 spec | 替换/对比/搜索时有统一的最小工程单元 |
| 6 | 自动化 Prompt / 参数优化 | 在一组 spec 上做搜索、A/B、调参 | 优化从纯手动变为半自动、有据可依 |

---

## 阶段一：Prompt 硬编码——写出第一个能用的版本

### 1.1 这一步要解决的问题

**目标非常简单：先跑起来。**

小明团队只想验证一个问题：

> “能不能用 LLM，把 YouTube 视频自动总结出来？”

此时不谈体系、不讲评估，只要能看到一个“像回事”的总结结果。

### 1.2 第一代实现：在代码里硬编码一条 Prompt

他们做了一个极简原型：

1. 1\. 拉取视频字幕
	transcript
	；
2. 2\. 拼成一条很长的 Prompt；
3. 3\. 调用模型看结果。

看到结果之后，大家很兴奋： **“能总结！还挺像回事。”**

兴奋之后，很快被现实教做人：

- • 同一个视频，多次调用，输出结构和重点都不一样；
- • 教程类视频总结得还行，访谈类视频经常抓不住重点；
- • PM 提出合理需求：“能不能输出统一结构？比如一定要有『核心观点』列表。”

这些需求统统指向一个点： **要改 Prompt。**

而此时，每次改 Prompt 都意味着：

- • 改代码里的字符串；
- • 提 PR、跑 CI、发版。

迭代非常重。

我们把这条 Prompt 从代码里抽出来看，会清楚很多：

```
# prompts/v1_prototype_summary.txt

你是一位拥有 10 年经验的专业内容编辑。

请阅读 <transcript> 标签中的视频字幕，并生成总结。

<example>
输入字幕：大家好，今天我们讲 Python 的变量...
输出总结：
<thinking>视频主要介绍了 Python 基础。</thinking>
核心观点：
- 变量定义
- 命名规范
</example>

<transcript>
{{ transcript }}
</transcript>

要求：
1. 在 <thinking> 标签中分析视频逻辑。
2. 输出 Markdown 格式。
```

调用代码（简化）：

```
# v1_prototype.py

def get_summary(transcript: str) -> str:
    prompt_template = load_file("prompts/v1_prototype_summary.txt")
    prompt = prompt_template.replace("{{ transcript }}", transcript)
    return llm.invoke(prompt)
```

第一代系统的特点：

- • 能跑，验证了“这事可以做”；
- • 高度依赖写这条 Prompt 的那个人；
- • 一切需求最终都变成“麻烦你改一下那条字符串”。

> 如果你现在的系统还在这个阶段，其实一点不丢人，但下一步就该考虑怎么把 Prompt 从代码里“解放”出来。

---

## 阶段二：把 Prompt 抽成模板，并用 CLI 快速执行

### 2.1 这一步要解决的问题

- • 所有角色（PM / 内容 / 增长）都围着那条硬编码字符串转；
- • 每改一个语气、一个词，都得工程师改代码 + 发版；
- • 工程师开始怀疑人生：“我是后端，还是字符串工程师？”

**核心诉求：**  
让 Prompt 成为 **可独立编辑、可本地调试** 的资产，而不是牢牢绑在发布流程里的“硬编码”。

### 2.2 第二代：summary\_v2.j2 + 支持“渲染 + 调用”的 CLI

团队做了两个工程动作：

1. 1\. 把 Prompt 抽象成 **Jinja2 模板文件** ；
2. 2\. 写了一个极简的 **CLI 工具** ，可以：
- • 读模板 + 变量文件
	- • 渲染出完整 Prompt
	- • 调用模型
	- • 把模型结果打印出来

这样 PM / 内容同学能在本地完成“改模板 → 一键看效果”的闭环。

**模板文件：** `prompts/summary_v2.j2`

```
{# prompts/summary_v2.j2 #}

{% include 'common/role_expert.j2' %}

<instruction>
请总结以下视频。
语气风格：{{ tone }}  {# "专业" / "轻松" / "幽默" / "科普" 等 #}
</instruction>

{% if use_example %}
<example>
输入字幕：大家好，今天我们讲 Python 的变量...
输出总结：
<thinking>视频主要介绍了 Python 基础。</thinking>
核心观点：
- 变量定义
- 命名规范
</example>
{% endif %}

<transcript>
{{ transcript }}
</transscript>

<requirements>
1. 先在 <thinking> 标签中分析视频逻辑。
2. 再输出 Markdown 格式的总结。
</requirements>
```

**变量文件：** `examples/summary_input.yml`

```
tone: 轻松
use_example: true
transcript: |
  大家好，今天我们来聊一聊 Python 变量……
```

**CLI 核心逻辑：**

```
# tools/summarize_cli.py（核心逻辑）

from pathlib import Path
from jinja2 import Environment, FileSystemLoader
import yaml
from llm_client import call_llm  # 你自己的封装

def run(template_path: str, vars_path: str, model: str = "gpt-4o-mini"):
    template_path = Path(template_path)
    vars_path = Path(vars_path)

    env = Environment(loader=FileSystemLoader(str(template_path.parent)))
    template = env.get_template(template_path.name)

    vars_dict = yaml.safe_load(vars_path.read_text(encoding="utf-8"))
    prompt = template.render(**vars_dict)

    print("=== Prompt ===")
    print(prompt)
    print("\n=== LLM Output ===")
    print(call_llm(model, prompt))
```

本地使用示例：

```
$ summarize-cli prompts/summary_v2.j2 examples/summary_input.yml --model gpt-4o-mini
```

线上调用就很简单了：

```
# v2_template_based.py

env = Environment(loader=FileSystemLoader("prompts"))

def get_summary_v2(transcript: str,
                   tone: str = "专业",
                   use_example: bool = True) -> str:
    template = env.get_template("summary_v2.j2")
    prompt = template.render(
        transcript=transcript,
        tone=tone,
        use_example=use_example,
    )
    return call_llm("gpt-4o-mini", prompt)
```

**第二代系统带来的变化：**

- • Prompt 从“代码中的硬编码字符串”，变成可独立编辑、预览、执行的文件；
- • PM / 内容同学可以在本地自己改模板，看模型结果；
- • 修改集中体现在 `.j2` 文件，有 Git 版本和 Review；
- • 工程师不再被当成“拼字符串工具人”。

> 很多团队会在这里停很久：能愉快改 Prompt 了，但好不好、有没有回归问题，还是全靠感觉。下一步就是把“感觉”变成“数据”。

---

## 阶段三：用 golden case 做回归评估

### 3.1 这一步要解决的问题

**问题：改 Prompt 像开盲盒。**

- • 为了提升“教程类”视频总结质量，改了一版 Prompt；
- • 上线后，“访谈类”视频开始漏掉关键观点；
- • 用户反馈：“之前挺好，现在反而变差了。”

工程师没法回答：

> “这次改 Prompt，到底是整体变好还是变坏？  
> 是某一类变好，另一类被牺牲了吗？”

没有统一的度量，就没有真正的控制。

### 3.2 最小可用评估体系：golden case + 自动回归

团队开始搭一个最小版本的评估体系：

- • 收集一批覆盖主要场景的 **golden case** ；
- • 对每个 case 写清楚“期望总结中必须出现的要点”；
- • 每次改 Prompt，都在这一批 golden case 上跑一遍。

**golden case 集合：** `data/golden_cases.yml`

```
- id: tutorial_001
  type: tutorial
  input: |
    （这里是教程类视频的字幕文本……）
  expected_points:
    - 解释了 Python 变量的概念
    - 提到了命名规范
    - 区分了局部变量和全局变量

- id: interview_007
  type: interview
  input: |
    （这里是访谈类视频的字幕文本……）
  expected_points:
    - 嘉宾是某领域专家
    - 重点讨论了行业趋势 X
    - 对 Y 问题表达了担忧
```

> 实战经验：
> 
> - • 新场景上线时，强制补 3–5 条代表性 golden case；
> - • golden case 一旦通过评审，改动必须走 Review，而不是随手覆盖。  
> 	这样能避免“评估集自己被悄悄改烂”。

**评估函数伪实现：**

```
# eval.py（伪代码）

def evaluate_coverage(summary: str, expected_points: list[str]) -> float:
    """
    现实项目里主要有两种思路：

    1）规则 / 关键字匹配：
       - 适合结构非常固定、格式要求很死的任务
       - 便宜，但一旦模型换一种说法就容易漏判

    2）用另一个 LLM 做打分（LLM-as-a-judge）：
       - 在我们自己的项目里，基本都是这一种
       - 贵一些，但对多样化表达更鲁棒，也更接近人类主观感觉

    大多数稍微复杂一点的场景，最后都会落到第 2 种。
    返回值可以是 0~1 之间的覆盖率分数。
    """
    ...
```

**回归测试：** `tests/test_summary_regression.py`

```
import yaml
from prompts import get_summary_v2
from eval import evaluate_coverage

def load_golden_cases(path="data/golden_cases.yml"):
    with open(path, "r", encoding="utf-8") as f:
        return yaml.safe_load(f)

def test_summary_quality_regression():
    golden_cases = load_golden_cases()
    for case in golden_cases:
        summary = get_summary_v2(
            transcript=case["input"],
            tone="专业",
            use_example=True,
        )
        score = evaluate_coverage(summary, case["expected_points"])
        assert score > 0.8, f"Case {case['id']} regression, score={score}"
```

上线后，再配合 Trace 系统（LangSmith / LangFuse 等），记录每次版本改动的：

- • Prompt 内容；
- • 模型输出；
- • 评估得分。

这里要区分两件事：

- • golden case + 回归评测，主要解决的是 **线下改 Prompt 的安全性问题** ；
- • 真正上线之后，还需要：
- • 线上采样 + LLM 自动打分（比如每天抽 请求评估）；
	- • 用户投诉 / 差评样本自动回流到 golden case 集合。

这样评估体系才会“活起来”，而不是一包永远不更新的离线样本。

第三代系统的意义：

- • Prompt 更新第一次变成“ **可量化的变更** ”；
- • 回归问题可以在 CI / 测试阶段被拦截，而不是上线几周后才发现；
- • 团队讨论效果不再只是“觉得还行”，而是：
- • “访谈类平均覆盖率从 提升到 。”

---

## 阶段四：从“超大 Prompt”拆成“分类 + 各自模板”

### 4.1 这一步要解决的问题

有了模板和评估体系后，团队会自然想： **“多加点规则，把所有场景都照顾到。”**

结果就是典型的 **Prompt 膨胀（Prompt Bloat）** ，形成了一条 **超大 Prompt** ：

- • Prompt 越写越长，动辄几千 Token；
- • 里面塞满了各种场景的要求，大量指令对当前视频完全不相关；
- • 模型被一堆互相竞争的要求淹没，效果反而变差：
- • 财经视频里出现无关的情绪总结；
	- • Vlog 视频里突然出现奇怪的“伪代码块”。

团队意识到：

> “我们把所有视频类型的总结逻辑，都塞在一条超大 Prompt 里了。  
> 这在一开始方便，现在成了系统可控性的最大敌人。”

### 4.2 升级思路：先分类，再用对应的模板

**总体思路：**

1. 1\. 单独做一次“视频分类”调用：
- • 输入：标题 + 描述；
	- • 输出：一个较粗的类别，例如 `finance / coding / vlog / other` ；
3. 2\. 根据分类结果，选择对应的 Prompt 模板文件：
- • `prompts/summary_finance_v1.j2`
	- • `prompts/summary_coding_v1.j2`
	- • `prompts/summary_vlog_v1.j2`
	- • …

> Trade-off：
> 
> - • 多了一次“分类调用”，有一点 Token 成本和延迟；
> - • 分类错了会导致路由错。  
> 	一般只在 **多场景差异明显** 的产品中使用；单场景的小产品可以先靠一个模板 + 轻量条件判断过渡一阵。

#### 分类请求：prompts/classify\_video.j2

```
{# prompts/classify_video.j2 #}

你是一个内容分类助手，请根据视频的标题和简介，判断视频的主要类型。

可选类型：
- finance：财经 / 商业 / 投资 / 市场分析
- coding：编程教学 / 技术分享 / 代码演示
- vlog：日常 vlog / 旅行 / 生活记录
- other：不属于以上任意主要类型

请只输出一个类型标识：finance / coding / vlog / other。

<title>
{{ title }}
</title>

<description>
{{ description }}
</description>
```

**分类调用代码：** `classifier.py`

```
from jinja2 import Environment, FileSystemLoader
from llm_client import call_llm

env = Environment(loader=FileSystemLoader("prompts"))

def classify_video(meta: dict) -> str:
    template = env.get_template("classify_video.j2")
    prompt = template.render(
        title=meta["title"],
        description=meta["description"],
    )
    result = call_llm("gpt-4o-mini", prompt)
    cleaned = result.strip().lower()
    if cleaned in {"finance", "coding", "vlog", "other"}:
        return cleaned
    return "other"
```

#### 各类型各自的总结模板

`prompts/summary_finance_v1.j2` ：

```
{# prompts/summary_finance_v1.j2 #}

{% include 'common/role_expert.j2' %}

<instruction>
你将阅读一段财经/商业类视频的字幕，并输出总结。
请特别关注数据、指标和风险点。
</instruction>

<transcript>
{{ transcript }}
</transcript>

<requirements>
1. 提取视频中提到的关键财务指标（如收入、利润、增长率），以 Markdown 表格展示。
2. 总结 3–5 个主要观点或结论。
3. 给出提到的风险点和不确定性。
</requirements>
```

`prompts/summary_coding_v1.j2` ：

```
{# prompts/summary_coding_v1.j2 #}

{% include 'common/role_expert.j2' %}

<instruction>
你将阅读一段编程教学视频的字幕，并输出总结。
请关注代码示例和关键概念。
</instruction>

<transcript>
{{ transcript }}
</transcript>

<requirements>
1. 保留关键代码片段，使用 \`\`\` 代码块展示。
2. 区分“概念讲解”和“具体实现步骤”。
3. 标出常见坑点和注意事项。
</requirements>
```

`prompts/summary_vlog_v1.j2` ：

```
{# prompts/summary_vlog_v1.j2 #}

{% include 'common/role_expert.j2' %}

<instruction>
你将阅读一段 vlog 视频的字幕，并输出总结。
请关注故事线和情绪变化。
</instruction>

<transcript>
{{ transcript }}
</transcript>

<requirements>
1. 用 3–5 句话概述视频的主要事件。
2. 标出关键转折点（如冲突、惊喜、情绪高峰）。
3. 用一句话描述视频总体氛围。
</requirements>
```

**总结调用代码：** `v4_routed_summary.py`

```
from jinja2 import Environment, FileSystemLoader
from llm_client import call_llm
from classifier import classify_video

env = Environment(loader=FileSystemLoader("prompts"))

def get_summary_v4(transcript: str, meta: dict) -> str:
    category = classify_video(meta)
    if category == "finance":
        template_name = "summary_finance_v1.j2"
    elif category == "coding":
        template_name = "summary_coding_v1.j2"
    elif category == "vlog":
        template_name = "summary_vlog_v1.j2"
    else:
        template_name = "summary_general_v1.j2"

    template = env.get_template(template_name)
    prompt = template.render(transcript=transcript, meta=meta)
    return call_llm("gpt-4o-mini", prompt)
```

**第四代系统的效果：**

- • 每个请求只携带与该类型相关的指令，Prompt 长度显著下降；
- • 模型不再被一堆无关约束干扰，各类型效果明显提升；
- • 对某一类型的优化，可以在对应模板内局部进行，不再牵一发动全身。

---

## 阶段五：请求规格（Spec）——把 Prompt + 模型 + 参数绑成一个最小单元

### 5.1 这一步要解决的问题

走到这里，小明团队大概已经有：

- • 模板化的 Prompt（`.j2` ）；
- • 分类 + 多模板路由；
- • golden case + 回归评测。

接下来，新的问题是：

- • 同一类能力，会在 **不同模型** 之间迁移、对比；
- • 每个模型都有自己的“脾气”， **不存在一个 Prompt 在所有模型上都表现很好的情况** ；
- • Prompt、模型、参数散落在代码和脑中，很难说清楚：

> “现在线上这条链路，到底在用哪一套组合？”

从 Prompt 工程视角，更合理的做法是：

> 把「Prompt 模板 + 模型 + 一组参数」绑定在一起，  
> 视为一个 **最小的调用规格（Request Spec）** 。

以后讨论效果、评估、做 A/B、做自动化搜索，  
都以这个 **spec** 为单位。

### 5.2 一个 spec 就是一份 YAML

我们约定：

> **一份 YAML 文件，只描述一个 spec。**  
> 一个 spec = Prompt 模板 + 模型 + 默认参数。

比如：财经视频总结，跑在 `gpt-4o-mini` 上的一个规格：

```
# specs/summary_finance__gpt4o_v1.yml

name: summary_finance__gpt4o_v1

prompt: summary_finance_gpt4o_v1.j2   # 用哪份 Prompt 模板

model: gpt-4o-mini                    # 调哪个模型

args:                                 # 默认调用参数（可被覆盖）
  temperature: 0.2
  max_tokens: 2200
```

同样的能力，用 claude 再写一份：

```
# specs/summary_finance__claude_v1.yml

name: summary_finance__claude_v1

prompt: summary_finance_claude_v1.j2
model: claude-3-5-sonnet

args:
  temperature: 0.15
  max_tokens: 2500
```

这里默认接受两件事：

- • **不同模型，用不同 Prompt：**  
	`summary_finance_gpt4o_v1.j2` 和 `summary_finance_claude_v1.j2` 是两份模板，各自针对模型特性调过一轮，不幻想“一条 Prompt 通吃所有模型”。
- • **Prompt、模型、参数一起出现：**  
	换模型 / 换 Prompt，就新建或切换到另一份 spec，而不是在很多地方东拼西凑。

### 5.3 Prompt 变量和参数值，都从外部传入

在这个设计下：

- • spec 只定义“ **长什么样 + 默认值** ”；
- • 这次请求的业务数据、参数覆盖，都从外部传进来。

示例代码：

```
from pathlib import Path
import yaml
from jinja2 import Environment, FileSystemLoader
from llm_client import call_llm

env = Environment(loader=FileSystemLoader("prompts"))

def load_spec(path: str) -> dict:
    return yaml.safe_load(Path(path).read_text(encoding="utf-8"))

def merge_args(spec_args: dict | None, override_args: dict | None) -> dict:
    base = dict(spec_args or {})
    if override_args:
        base.update(override_args)
    return base

def call_with_spec(spec_path: str,
                   prompt_vars: dict,
                   override_args: dict | None = None) -> str:
    spec = load_spec(spec_path)

    # 1. 渲染 Prompt 模板（所有业务上下文都从外面进来）
    template = env.get_template(spec["prompt"])
    prompt = template.render(**prompt_vars)

    # 2. 合并参数：规格默认值 + 本次调用覆盖
    final_args = merge_args(spec.get("args"), override_args)

    # 3. 按规格调用模型
    return call_llm(
        model=spec["model"],
        prompt=prompt,
        **final_args,
    )
```

业务侧用起来可能是这样：

```
summary = call_with_spec(
    spec_path="specs/summary_finance__gpt4o_v1.yml",
    prompt_vars={
        "transcript": transcript,
        "title": meta["title"],
        "channel_name": meta["channel_name"],
        "language": "zh-cn",
        "tone": "专业",
    },
    override_args={
        "temperature": 0.1,  # 这次想更保守
    },
)
```

可以看到：

- • Prompt 模板需要的内容（ `transcript` 、 `title` 、 `tone` …）全在 `prompt_vars` 里，由业务决定；
- • 模型参数有默认值（写在 spec 的 `args` ）；
- • 真正跑的时候，可以按需覆盖一部分参数，不必每次都改 YAML。

### 5.4 这对 Prompt 工程的意义

这一阶段，对 Prompt 工程的直接好处有三个：

1. 1\. **评估 / 实验的粒度统一了**
- • 不再口头说“我试了一下把模型从 A 换成 B”；
	- • 而是清楚地比较：
- • `summary_finance__gpt4o_v1`
		- • vs `summary_finance__claude_v1`
	- • golden case 上的分数、线上表现，全部挂在具体的 spec 名字上，方便追踪和回滚。
3. 2\. **自然接受“每个模型需要专门 Prompt”**
- • 一份 spec 只服务一个模型 + 一份模板 + 一组参数；
	- • 换模型时，顺手就会去新建一份 spec，而不是幻想“复用原 Prompt 见神杀神”。
5. 3\. **为后续自动优化留好了“挂钩点”**
- • 以后做自动化搜索，本质上是在“spec 空间”里探索：
- • 新增一个 spec，把温度调低一点；
		- • 新增一个 spec，用 `gpt-4.1` + 新版模板跑同一批 golden case。
	- • 人定义哪些字段可以调、边界多少，系统负责在这些 spec 之间试验和打分。

一句话概括这个阶段：

> **我们不再零散地谈 Prompt、模型、参数，  
> 而是把三者绑成“请求规格（spec）”这个最小单元，  
> 以后所有评估、对比、搜索，都围绕这个单位来进行。**

---

## 阶段六：在 spec 之上做自动优化（以及 DSPy 思路）

在前五个阶段，所有优化基本靠人：

- • 人写 Prompt 模板；
- • 人选择模型；
- • 人给定参数默认值；
- • 人根据 golden case 和线上反馈改模板、调参数。

当你已经有了：

- • 稳定的模板体系；
- • 明确的评估指标和 golden case；
- • **以 spec 为粒度的调用方案管理方式** ；

这时才有空间考虑： **让系统在一组 spec 上“自动找更好的组合”。**

常见几种玩法：

- • 在多份 spec 之间做 A/B 测试：
- • 同一批请求，一部分走 `summary_finance__gpt4o_v1` ，
	- • 一部分走 `summary_finance__claude_v1` ，
	- • 用 golden case + 线上指标共同决定下一步用哪一个。
- • 在某个 spec 内部做小范围搜索：
- • 固定 Prompt 模板和模型，
	- • 在允许的范围内自动调 `temperature` 、 `max_tokens` 等参数，
	- • 找到更稳、更划算的默认值。
- • 针对表现特别差的样本，半自动地触发：
- • 生成新 Prompt 候选；
	- • 启动一个新的 spec（新模板 + 参数）；
	- • 在离线评估和小流量线上实验里做验证。

如果想再往前走一步，可以看一下像 **DSPy** 这样的框架。  
它做的事情本质上是：

- • 把一次 LLM 调用抽象成一个可优化的「程序规格」（signature / spec）；
- • 人定义好输入输出结构、可调参数、目标指标；
- • 系统在一批数据上，自动搜索更好的 Prompt 结构和参数组合。

你前面用 YAML 定义的：

```
spec = Prompt 模板 + 模型 + 参数
```

本质上就已经是一个 **轻量版的 DSPy 思路** ：

- • 有清晰的调用单元（spec）；
- • 有 golden case 和评估指标；
- • 接下来完全可以在这些 spec 上挂一个“小号的 DSPy 优化器”，  
	让一部分 Prompt / 参数调优工作，从纯手工变成“人设边界、机器帮你找更优解”。

---

## 结语：从 Prompt 硬编码，到设计一个可控系统

回看这几个阶段，你会发现我们做的事情，不是“堆高级 Prompt 技巧”，而是：

1. 1\. **把 Prompt 从代码里剥离出来** ，变成可编辑、可执行的资产；
2. 2\. 让 **非工程角色也能迭代 Prompt** ，而不被工程发布卡住；
3. 3\. 用 golden case 和自动评估，让 Prompt 变更 **可度量、有回归保障** ；
4. 4\. 从“一条超大 Prompt”拆成“分类 + 各自模板”，避免 Prompt 膨胀和 Token 浪费；
5. 5\. 用 **请求规格（spec）** 把 Prompt、模型、参数绑定成一个最小单元，让它们的关系 **可见、可管理、可对比** ；
6. 6\. 在此基础上，才有空间考虑 **在 spec 空间内做自动优化 Prompt 与参数** ，甚至引入像 DSPy 这样的框架。

这才配得上前面那句话：

> **Prompt 工程的本质，不只是 Prompt 写作技巧，而是用可度量、可迭代、可验证的工程方法，去管理这种对输入高度敏感、近似混沌的 LLM 系统，使其输出收敛为可预期、可复现的工程结果。**

如果系统还停留在：

- • 在代码里把 Prompt 硬编码成一条长字符串；
- • 完全靠感觉改 Prompt；
- • 没有 golden case、没有评估、没有明确的请求规格（spec）；

那你面对的，依然是一个 **偶尔惊喜、整体不可控** 的黑箱。

一旦把 Prompt 看成系统的一部分，围绕它搭建结构、工具链和控制回路，  
Prompt 工程就会从“玄学调教”，变成一门可以讲清楚、可以持续演进的工程实践。

---

## 最后：欢迎你在评论区告诉我两件事

如果你看到这里，我非常好奇两件事：

1. 1\. **你现在的 LLM 项目，大致停在哪个阶段（1–6）？**
2. 2\. **在 Prompt 工程 / LLM 应用工程里，你遇到的最大一个坑是什么？**

如果大家有兴趣，我可以挑几个典型问题，单独写一篇后续文章来展开。

继续滑动看下一个

言午

向上滑动看下一个