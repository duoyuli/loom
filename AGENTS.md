# 知识库

这个仓库是一个可持续积累的本地知识库。首要目标是提升知识的可检索性、可链接性、可复用性，而不是一次性产出单篇回答。

## 使命

知识应先沉淀到 `wiki/`，再被回答、研究和写作复用。Agent 负责维护 `wiki/` 的来源映射、页面结构、链接关系和纠错回写；人负责筛选资料、引导问题方向，并决定哪些研究值得继续深挖。

`writing/` 是基于 wiki 的输出层，不是知识沉淀的第一落点。文章可以复用 wiki，但如果某篇文章未来要作为来源进入知识库，必须先整理进 `raw/sources/`，再按普通来源摄入。

## 硬约束

- `raw/` 下的文件不可变，绝不重写原始资料。
- `raw/assets/` 中的资料不能直接绕过 `raw/sources/` 对应 `wiki/sources/`；必须先有 `raw/sources/` 中的原始载体或不可变资料指针笔记。
- `wiki/sources/index.md` 是来源注册表；新增、迁移、巡检、反查和命名时，必须先以它为准，而不是只靠猜当前文件名。
- `wiki/sources/` 的 canonical 文件名必须是 `YYYY-MM-DD-normalized-raw-title.md`。
- 只要 `wiki/` 发生实质变更，必须同步更新 `wiki/index.md` 和 `wiki/log.md`。
- 优先更新已有页面，不优先新建语义重复页。
- 优先维护许多小页和清晰链接，而不是把内容堆进单个大页面。
- 必须保留不确定性、冲突点和证据边界，区分事实、解释和开放问题。
- 新旧结论冲突时，优先更新原页面并记录冲突点，不绕开旧结论。
- 已发布文章默认视为定稿快照；是否改写、重写或重新入库，由人决定，不自动触发知识库流程。

## 决策顺序

1. 先判断这次任务是否应沉淀进知识库。
2. 默认先读 `wiki/index.md`；涉及来源时，同时读 `wiki/sources/index.md`。
3. 先找已有页面，再决定是否新建页面。
4. 先修复可检索性、来源映射和链接，再追求格式完整。
5. 只更新真正受影响的最小页面集合，不机械补全模板。
6. 只有内容具有长期复用价值时，才沉淀到 `wiki/analyses/`。

## 任务类型

- 摄入：入口是 `raw/sources/` 和相关 `raw/assets/`；最小产物是来源页、受影响页面、`wiki/sources/index.md`、`wiki/index.md` 和 `wiki/log.md`。
- 查询：入口是 `wiki/index.md` 与相关页面；最小产物是基于当前 wiki 的综合回答；只有高复用价值的回答才回写分析页并记日志。
- 深度研究：优先复用实体页、概念页、来源页和分析页；需要长期保留时再拆分析页；优先 `timeline / comparison / synthesis`。
- 写作：先把 wiki 当作证据底座；证据不足时先补 wiki，再写 `writing/`。
- 纠错：先回溯并修正导致错误的原页面，再决定是否补分析页；重要纠错应记录结论变化和依据。
- 巡检：重点检查冲突、孤页、缺来源、重复概念和研究空白；只要修正了 `wiki/` 中的实质内容，就同步更新索引与日志。

## 目录结构

- `raw/sources/`：不可变的原始资料文件或资料指针笔记。
- `raw/assets/`：原始资料引用的图片、附件等不可变资源。
- `wiki/index.md`：面向内容的页面目录，默认入口。
- `wiki/log.md`：按时间追加的日志，记录摄入、问答沉淀、巡检、纠错和结构调整。
- `wiki/overview.md`：知识库当前的高层综述。
- `wiki/sources/`：每个资料一页，记录摘要、关键主张、相关性和后续线索。
- `wiki/entities/`：人物、公司、地点、产品、项目、组织等稳定对象。
- `wiki/concepts/`：主题、方法、框架、争议和重复出现的概念。
- `wiki/analyses/`：值得长期保留的回答、对比、时间线、综合判断和备忘。
- `writing/`：专题文章与文章草稿。
- `writing/index.md`：专题文章入口页，只承担写作导航，不承担 wiki 记账职责。

目录内容变多时，优先在目录内新增 `index.md` 作为地图页，而不是新增新的顶层目录。

## 命名约定

- 资料文件：`YYYY-MM-DD-short-title.ext`
- 资料指针笔记：`YYYY-MM-DD-short-title-link.md`
- `wiki/sources/` 中的资料摘要页：`YYYY-MM-DD-normalized-raw-title.md`
- 专题文章：`writing/YYYY-MM-DD-short-title.md`
- 文件名默认使用 lowercase kebab-case；文件内标题尽量清晰。

### `wiki/sources/` 的 canonical 文件名规则

- `YYYY-MM-DD` 使用 `source_date`。
- `source_date` 优先取资料发布日期；只有无法确定发布日期时，才回退到最早不可变原始载体的创建时间。
- 如果原始内容实际存于 `raw/assets/`，fallback 取对应 asset 的创建时间，不取后补 `raw/sources/` 指针笔记的创建时间。
- `normalized-raw-title` 默认直接沿用 `raw/sources/` 原题目语义做规范化：保留原语言，不为生成 slug 额外翻译标题。
- 规范化时，把空格、全角标点和路径不安全字符折叠为连字符。
- 如果 `raw/sources/` 文件名本身已经带前置日期，生成 `normalized-raw-title` 时先去掉这段日期，避免把日期写两次。
- 旧的 `wiki/sources/` 路径如果还不符合规范，先登记到 `wiki/sources/index.md`，并在该页标记为 `legacy-path`；只有在该来源页被实质更新时，才顺手迁到 canonical 路径。

## 实质变更

以下情况通常算 `wiki/` 层的实质变更，必须同步更新 `wiki/index.md` 和 `wiki/log.md`：

- 新增、删除或重构 `wiki/sources/`、`wiki/entities/`、`wiki/concepts/`、`wiki/analyses/` 页面。
- 修正已有结论、增加关键事实或改变判断。
- 重写页面之间的主要链接关系或导航结构。
- 新增具有长期复用价值的问答、对比、时间线或备忘。
- 修正来源映射、canonical 路径或关键证据链，导致检索入口或结论边界发生变化。

以下情况通常不算：

- 纯格式调整、错字修正或不影响判断的措辞压缩。
- 不改变知识结构的局部排版整理。
- 仅修改 `writing/` 层页面。

## 页面默认

frontmatter 不是全局必填，但以下页面类型有最小必填字段；需要稳定命名、巡检和反查的页面，不再视为完全可选。

### source 页面

最小必填字段：

```yaml
---
title:
type: source
sources:
source_date:
source_date_basis: published | created
updated:
---
```

页面默认内容：

- 页面开头一段短摘要，说明这份资料为何存在。
- 资料是什么。
- 主要观点或观察。
- 为什么与当前知识库有关。
- 可靠性判断。
- 受影响页面。
- `## 来源`。
- 必要时补 `## 纵向线索` / `## 横向线索`。

要求：

- 必须能通过 `sources` 回溯到 `raw/sources/`。
- 如果原始材料实际存放在 `raw/assets/`，`sources` 中应同时保留资料指针笔记和对应 asset。

### entity 页面

最小必填字段：

```yaml
---
title:
type: entity
updated:
---
```

可选字段：

```yaml
---
entity_type: person | org | product | project | place
sources:
status:
---
```

页面默认内容：

- 摘要段
- `## 纵向脉络`
- `## 横向位置`
- `## 当前判断`
- `## 来源`
- 必要时 `## 开放问题`

### concept 页面

最小必填字段：

```yaml
---
title:
type: concept
updated:
---
```

可选字段：

```yaml
---
sources:
status:
---
```

页面默认内容：

- 摘要段
- `## 定义`
- `## 纵向脉络`
- `## 横向位置`
- `## 与其他概念的关系`
- `## 来源`
- 必要时 `## 开放问题`

### analysis 页面

最小必填字段：

```yaml
---
title:
type: analysis
updated:
---
```

长期保留的分析页应补：

```yaml
---
subtype: timeline | comparison | synthesis | framework | review | memo
sources:
status:
---
```

页面默认内容：

- 摘要段
- `## 研究问题`
- `## 主要判断`
- `## 证据与比较`
- `## 结论或备忘`
- `## 来源`
- 必要时 `## 开放问题`

深度研究新增分析页时，优先使用 `timeline`、`comparison`、`synthesis`，只有明显更适合时再用 `framework`、`review` 或 `memo`。

### writing 页面

最小必填字段：

```yaml
---
title:
type: article
status: drafting | published | archived
derived_from:
updated:
---
```

要求：

- `derived_from` 中列出支撑它的 wiki 页面。
- 正文中的关键判断应能回溯到这些 wiki 页面，而不是只依赖临时推导。
- 写作页不自动进入 `wiki/` 记账流程。

### 通用要求

- frontmatter 字符串值如果包含 `:`、`#`、`[`、`]`、`{`、`}` 等容易触发 YAML 歧义的字符，应显式加引号。
- 页面之间优先建立链接；能用 wiki 风格链接时可用，否则用普通 Markdown 链接。
- 如果存在明显空白或未解决冲突，可补 `## 开放问题`。

## 工作流程

### 摄入

1. 先读 `wiki/index.md` 和 `wiki/sources/index.md`。
2. 读取 `raw/sources/` 中的新文件；如果原始材料只有 `raw/assets/`，先确认是否已有对应的 `raw/sources/` 指针笔记。
3. 先在来源注册表中确认该资料是否已入库、当前路径是什么、是否存在 legacy path。
4. 判定 `source_date` 与 `source_date_basis`：优先找真实发布日期；找不到时，回退到最早不可变原始载体的创建时间。
5. 在 `wiki/sources/` 中按 canonical 文件名创建或更新对应资料页。
6. 立即更新 `wiki/sources/index.md`，登记 `raw_path`、当前路径、canonical 路径、日期依据与迁移状态。
7. 更新受影响的实体页、概念页、综述页和分析页。
8. 补充或修正相关链接。
9. 更新 `wiki/index.md`。
10. 在 `wiki/log.md` 追加带时间戳的记录。

重点不是机械补模板，而是把来源、判断和链接关系真正沉淀下来。

### 批量摄入

1. 先按知识库流程读 `wiki/index.md` 和 `wiki/sources/index.md`，再对比 `raw/sources/`、来源注册表与现有 `wiki/sources/` 页面，找出未摄入材料、缺登记材料和仍停留在旧路径上的来源页。
2. 可以为每个未摄入材料起一个 subagent，但 subagent 只负责单条来源及其局部受影响页，不得并发修改 `wiki/sources/index.md`、`wiki/index.md`、`wiki/log.md` 这三个共享文件。
3. 主 agent 统一串行更新 `wiki/sources/index.md`、`wiki/index.md` 和 `wiki/log.md`。
4. 如果批量摄入中顺带迁移 legacy path，也由主 agent 统一登记和收尾。

### 查询

1. 先读 `wiki/index.md`。
2. 再读最相关的链接页面；涉及具体来源时，再读 `wiki/sources/index.md` 和对应来源页。
3. 基于当前 wiki 综合回答。
4. 如果回答具有长期价值，再写入 `wiki/analyses/`，并同步更新 `wiki/index.md` 与 `wiki/log.md`。

### 深度研究

1. 先读 `wiki/index.md`，检查已有实体页、概念页、来源页和分析页。
2. 优先复用已有页面；只有当前 wiki 不足时才新建分析页。
3. 按横纵分析法组织研究。
4. 默认拆成许多互链小页，不写一篇过长总报告。
5. 只要新增或实质改写了 `wiki/` 页面，就同步更新 `wiki/index.md`，并在 `wiki/log.md` 追加记录。

页面分工：

- `wiki/sources/`：补 `## 纵向线索` / `## 横向线索`，只存线索，不替代完整分析。
- `wiki/entities/`：承接对象本身的长期判断。
- `wiki/concepts/`：承接稳定概念的定义、演化和位置。
- `wiki/analyses/`：承接需要长期保留的时间线、对比和综合判断。

### 写作

1. 先读 `wiki/index.md`，再读最相关的概念页、实体页、分析页和来源页。
2. 如果 wiki 证据不足，优先补 `wiki/`，再写 `writing/` 草稿或定稿。
3. 在文章 frontmatter 中用 `derived_from` 列出支撑页面。
4. 更新 `writing/index.md`。
5. 只有当写作过程同时引发 `wiki/` 层实质更新时，才同步更新 `wiki/index.md` 和 `wiki/log.md`。

### 纠错

1. 明确原结论哪里有误，区分事实错误、解释偏差或证据不足。
2. 回溯并优先更新导致错误的原页面，而不是只补孤立备注。
3. 如果纠错改变既有结论，要写清原说法、修正原因和依据。
4. 如有长期价值，可在 `wiki/analyses/` 保存简短纠错分析。
5. 修正受影响页面之间的链接与来源引用；必要时补 `## 开放问题`。
6. 只要 `wiki/` 发生实质更新，就同步更新 `wiki/index.md` 并在 `wiki/log.md` 追加记录。

### 巡检

重点检查：

- 页面之间是否冲突。
- 是否有已被新资料替代但尚未更新的旧结论。
- 是否存在链接薄弱的孤立页面。
- 是否有被反复提到但尚未单独建页的概念。
- 是否有应合并的重复信息。
- 是否缺少来源引用。
- `wiki/sources/index.md` 是否覆盖了全部 `raw/sources/`，以及是否存在一份 raw 对多份 source 或一份 source 对多份 raw 的重复映射。
- 新建或刚更新的 `wiki/sources/` 页面是否已经使用规范文件名；旧路径如未迁移，是否已经在来源注册表标记为 `legacy-path`。
- 是否存在 `raw/assets/` 直接被 `wiki/sources/` 引用，但没有对应 `raw/sources/` 原始载体或指针笔记的旁路。
- 是否存在明显研究空白。

巡检结果应记录到 `wiki/log.md`。如果只是纯检查、没有实质修正，也至少应记录结论和后续空白。

## 编辑标准

- 优先保证清晰度、可积累性和交叉引用质量。
- 避免装饰性措辞。
- 除非信息已在别处完整保留且确实冗余，否则不要删除有价值的历史上下文。
- 优先更新现有页面，而不是创建语义高度重复的新页面。
- 专题文章正文结构保持自由，但关键判断必须能回溯到支撑它的 wiki 页面。

## 何时请人决定

- 资料筛选方向需要人判断时。
- 旧结论与新证据冲突且影响范围较大时。
- 是否值得继续深度研究或升格为文章时。
- 多种合理组织方式并存，且会明显改变知识结构时。
- 是否要把某篇 `writing/` 文章重新整理为 `raw/sources/` 来源时。