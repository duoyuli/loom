# 知识库

当前仓库是一个可持续积累的本地知识库，首要目标是知识的可检索性、可链接性、可复用性，而不是一次性产出单篇回答。

## 使命

知识先沉淀到 `wiki/`，再被回答、研究和写作复用。Agent 负责维护 `wiki/` 的来源映射、页面结构、链接关系和纠错回写；人负责筛选资料、引导问题方向，并决定哪些研究值得继续深挖。

`writing/` 是基于 wiki 的输出层，不是知识沉淀的第一落点。文章可以复用 wiki，但如果某篇文章未来要作为来源进入知识库，必须先整理进 `raw/sources/`，再按普通来源摄入。

## 硬约束

- `raw/` 下的文件不可变，绝不重写原始资料。
- `raw/assets/` 中的资料不能直接对应 `wiki/sources/`；必须先在 `raw/sources/` 有原始载体或资料指针笔记。
- `wiki/sources/index.md` 是来源注册表；新增、迁移、巡检、反查和命名时，一律先以它为准，不靠猜当前文件名。
- 同一对象或概念只有一页：宁可更新已有页面，不新建语义重复页；不同对象或概念宁可拆成互链小页，不堆进单个大页面。
- 保留不确定性、冲突点和证据边界，区分事实、解释和开放问题。新旧结论冲突时，更新原页面并记录冲突点，不绕开旧结论。
- 已发布文章默认视为定稿快照；是否改写、重写或重新入库，由人决定，不自动触发知识库流程。
- 除非信息已在别处完整保留且确实冗余，否则不删除有价值的历史上下文。

## 记账规则

只要发生 `wiki/` 层的**实质变更**，任务收尾时必须做两件事：更新 `wiki/index.md`，并在 `wiki/log.md` 追加一条记录。各工作流不再单独重复这一步。

实质变更指：

- 新增、删除或重构 `wiki/sources/`、`wiki/entities/`、`wiki/concepts/`、`wiki/analyses/` 页面。
- 修正已有结论、增加关键事实或改变判断。
- 重写页面之间的主要链接关系或导航结构。
- 修正来源映射、canonical 路径或关键证据链。

不算实质变更：纯格式调整、错字修正、不影响判断的措辞压缩、仅修改 `writing/` 层页面（`writing/` 只需维护 `writing/index.md`）。

## git 约定

- 每完成一次任务（摄入、纠错、巡检等）做一个 commit，不把多次任务混在一个 commit 里。
- commit message 以任务类型开头，例如 `摄入: 2026-05-30-xxx`、`纠错: 修正 entities/yyy 的成立时间`。

## 目录结构

- `raw/sources/`：不可变的原始资料文件或资料指针笔记。
- `raw/assets/`：原始资料引用的图片、附件等不可变资源。
- `wiki/index.md`：面向内容的页面目录，默认入口。
- `wiki/log.md`：按时间追加的日志。
- `wiki/overview.md`：知识库当前的高层综述。
- `wiki/sources/`：每个资料一页，记录摘要、关键主张、相关性和后续线索。
- `wiki/entities/`：人物、公司、地点、产品、项目、组织等稳定对象。
- `wiki/concepts/`：主题、方法、框架、争议和重复出现的概念。
- `wiki/analyses/`：值得长期保留的回答、对比、时间线、综合判断和备忘。
- `writing/`：专题文章与草稿；`writing/index.md` 只承担写作导航，不承担 wiki 记账职责。

目录内容变多时，优先在目录内新增 `index.md` 作为地图页，而不是新增顶层目录。

## 命名约定

文件名一律 lowercase kebab-case；空格、全角标点和路径不安全字符折叠为连字符。

- 资料文件：`raw/sources/YYYY-MM-DD-short-title.ext`
- 资料指针笔记：`raw/sources/YYYY-MM-DD-short-title-link.md`
- 来源页：`wiki/sources/YYYY-MM-DD-normalized-raw-title.md`（canonical 文件名，必须遵守）
- 专题文章：`writing/YYYY-MM-DD-short-title.md`

### 来源页 canonical 文件名

- `YYYY-MM-DD` 取 `source_date`：优先用资料的真实发布日期；无法确定时，回退到最早不可变原始载体在本仓库的首次 commit 时间（不可依赖文件系统创建时间）。原始内容实际存于 `raw/assets/` 时，以该 asset 的首次 commit 为准，不取后补指针笔记的时间。
- `normalized-raw-title` 直接沿用 `raw/sources/` 原题目语义做规范化：保留原语言，不为生成 slug 额外翻译。原文件名已带前置日期的，先去掉，避免日期写两次。
- 旧路径不符合规范的：先在 `wiki/sources/index.md` 登记并标记 `legacy-path`；只在该来源页被实质更新时才顺手迁到 canonical 路径。

## 关键文件格式

### `wiki/sources/index.md`（来源注册表）

用一张表维护，一份资料一行：

```markdown
| raw_path | 来源页当前路径 | canonical 路径 | source_date 依据 | 状态 |
|---|---|---|---|---|
| raw/sources/2026-05-30-xxx.pdf | wiki/sources/2026-05-30-xxx.md | （同左） | published | ok |
| raw/sources/old-note.md | wiki/sources/old-note.md | wiki/sources/2025-11-02-old-note.md | created | legacy-path |
```

状态取值：`ok`、`legacy-path`、`未摄入`。巡检时以本表对照 `raw/sources/`，检查覆盖率和一对多重复映射。

### `wiki/log.md`

按时间追加，一条记录一行：

```markdown
- 2026-06-10 摄入 | 2026-05-30-xxx.md | 新建来源页，更新 entities/yyy、concepts/zzz；遗留：www 待建页
- 2026-06-11 巡检 | （无实质修正） | 结论：无冲突；空白：aaa 主题缺来源
```

巡检即使没有实质修正，也至少记录结论和后续空白。

## 决策顺序

1. 先判断这次任务是否应沉淀进知识库。
2. 默认先读 `wiki/index.md`；涉及来源时，同时读 `wiki/sources/index.md`。
3. 先找已有页面，再决定是否新建。
4. 先修复可检索性、来源映射和链接，再追求格式完整。
5. 只更新真正受影响的最小页面集合，不机械补全模板。
6. 只有内容具有长期复用价值时，才沉淀到 `wiki/analyses/`。

## 工作流程

### 摄入

最小产物：来源页、受影响页面、三个记账文件（注册表、index、log）。

1. 读 `wiki/index.md` 和 `wiki/sources/index.md`，确认该资料是否已入库、当前路径、是否 legacy-path。
2. 读 `raw/sources/` 中的新文件；原始材料只有 `raw/assets/` 时，先确认已有对应指针笔记，没有则先建。
3. 判定 `source_date` 与 `source_date_basis`，按 canonical 规则创建或更新来源页。
4. 立即在 `wiki/sources/index.md` 登记。
5. 更新受影响的实体页、概念页、综述页和分析页，补充或修正链接。

重点不是机械补模板，而是把来源、判断和链接关系真正沉淀下来。

### 批量摄入

1. 对比 `raw/sources/`、来源注册表与现有 `wiki/sources/` 页面，找出未摄入、缺登记和仍在旧路径的来源。
2. 可为每个未摄入材料起一个 subagent，但 subagent 只负责单条来源及其局部受影响页，**不得并发修改** `wiki/sources/index.md`、`wiki/index.md`、`wiki/log.md` 三个共享文件。
3. 主 agent 统一串行更新三个共享文件；顺带迁移的 legacy path 也由主 agent 统一登记收尾。

### 查询

最小产物：基于当前 wiki 的综合回答。

1. 先读 `wiki/index.md`，再读最相关的链接页面；涉及具体来源时再读注册表和来源页。
2. 基于当前 wiki 综合回答。
3. 只有回答具有长期复用价值时，才回写 `wiki/analyses/`。

### 深度研究

1. 先检查已有实体页、概念页、来源页和分析页，优先复用；只有当前 wiki 不足时才新建分析页。
2. 按横纵分析法组织；默认拆成互链小页，不写一篇过长总报告。
3. 页面分工：`sources/` 只存 `## 纵向线索` / `## 横向线索`，不替代完整分析；`entities/` 承接对象的长期判断；`concepts/` 承接概念的定义、演化和位置；`analyses/` 承接时间线、对比和综合判断，subtype 优先 `timeline` / `comparison` / `synthesis`。

### 写作

1. 先读相关概念页、实体页、分析页和来源页，把 wiki 当证据底座；证据不足时先补 `wiki/`，再写 `writing/`。
2. 文章 frontmatter 用 `derived_from` 列出支撑页面；正文关键判断应能回溯到这些页面，而不是只依赖临时推导。
3. 更新 `writing/index.md`。

### 纠错

1. 明确原结论哪里有误：事实错误、解释偏差还是证据不足。
2. 回溯并优先更新导致错误的原页面，不只补孤立备注。改变既有结论时，写清原说法、修正原因和依据。
3. 修正受影响页面之间的链接与来源引用；必要时补 `## 开放问题`。有长期价值时可在 `wiki/analyses/` 存简短纠错分析。

### 巡检

重点检查：

- 页面之间是否冲突；是否有已被新资料替代但未更新的旧结论。
- 是否有链接薄弱的孤页、应合并的重复信息、被反复提到但未建页的概念、缺少来源引用的页面。
- 注册表是否覆盖全部 `raw/sources/`；是否存在一对多重复映射；新建或刚更新的来源页是否用了 canonical 文件名；旧路径是否已标记 `legacy-path`。
- 是否存在 `raw/assets/` 被来源页直接引用、但缺 `raw/sources/` 载体或指针笔记的旁路。
- 是否存在明显研究空白。

## 页面模板

通用规则：

- frontmatter 字符串值包含 `:`、`#`、`[`、`]`、`{`、`}` 等字符时，显式加引号；不输出空值字段。
- 页面之间的链接统一用相对路径 Markdown 链接（如 `[xxx](../entities/xxx.md)`），不用 wiki 风格 `[[ ]]` 链接。
- 存在明显空白或未解决冲突时，补 `## 开放问题`。
- 各类型页面默认章节按下列顺序，但只写有实质内容的章节，不机械补空标题。

### source 页

```yaml
---
title: "资料标题"
type: source
sources:
  - raw/sources/2026-05-30-xxx.pdf
source_date: 2026-05-30
source_date_basis: published   # published | created
updated: 2026-06-10
---
```

正文：开头一段短摘要（资料是什么、为何存在）；主要观点或观察；为什么与当前知识库有关；可靠性判断；受影响页面；`## 来源`；必要时 `## 纵向线索` / `## 横向线索`。

要求：必须能通过 `sources` 回溯到 `raw/sources/`；原始材料实际在 `raw/assets/` 时，`sources` 同时列指针笔记和对应 asset。

### entity 页

```yaml
---
title: "对象名"
type: entity
updated: 2026-06-10
# 可选：entity_type: person | org | product | project | place
# 可选：sources、status
---
```

正文：摘要段；`## 纵向脉络`；`## 横向位置`；`## 当前判断`；`## 来源`。

### concept 页

```yaml
---
title: "概念名"
type: concept
updated: 2026-06-10
# 可选：sources、status
---
```

正文：摘要段；`## 定义`；`## 纵向脉络`；`## 横向位置`；`## 与其他概念的关系`；`## 来源`。

### analysis 页

```yaml
---
title: "分析标题"
type: analysis
updated: 2026-06-10
# 长期保留时应补：
# subtype: timeline | comparison | synthesis | framework | review | memo
# sources、status
---
```

正文：摘要段；`## 研究问题`；`## 主要判断`；`## 证据与比较`；`## 结论或备忘`；`## 来源`。

subtype 优先用 `timeline`、`comparison`、`synthesis`，只有明显更合适时再用其余三种。

### writing 页

```yaml
---
title: "文章标题"
type: article
status: drafting   # drafting | published | archived
derived_from:
  - wiki/concepts/xxx.md
  - wiki/analyses/yyy.md
updated: 2026-06-10
---
```

正文结构自由，但关键判断必须能回溯到 `derived_from` 列出的 wiki 页面。写作页不进入 `wiki/` 记账流程。

## 何时请人决定

- 资料筛选方向需要人判断时。
- 旧结论与新证据冲突且影响范围较大时。
- 是否值得继续深度研究或升格为文章时。
- 多种合理组织方式并存，且会明显改变知识结构时。
- 是否要把某篇 `writing/` 文章重新整理为 `raw/sources/` 来源时。
