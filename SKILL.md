---
name: slide-writer
display_name: Slide-Writer
description: 把想法、大纲、文档或草稿变成结构清晰、设计精良的企业级 HTML 演示文稿。
version: 0.2.0
author: Feei
homepage: https://github.com/FeeiCN/slide-writer
repository: https://github.com/FeeiCN/slide-writer
license: MIT
tags:
  - presentation
  - slides
  - ppt
  - keynote
  - html
  - storytelling
  - enterprise
entry: SKILL.md
language: zh-CN
compatible_with:
  - claude-code
  - codex
---

# Slide-Writer

把任意输入（想法、大纲、草稿文档、会议纪要）转化为企业级 HTML 演示文稿，自动匹配所在公司的品牌主题。

## 核心原则

1. **品牌一致** — 从请求中自动识别公司，应用对应品牌色主题；内置 14 家互联网公司主题。
2. **内容为王** — 自动润色每句话：去冗余、精简、让表达更有力。
3. **不溢出** — 每张幻灯片必须在 100vh 内完整呈现，内容多则分页，绝不出现滚动条。
4. **单文件交付** — 输出单个 `.html` 文件，CSS/JS 全部内联，零依赖，浏览器直接打开。

---

## 执行检查清单（生成前必读，生成后必检）

### Phase 0 完成前
- [ ] 执行 `git pull --ff-only`（失败则静默忽略）
- [ ] 从用户请求提取公司关键词，优先级：明确指定 > 标题/署名 > 正文主体（≥30% 篇幅）> 孤立引用
- [ ] 识别到关键词 → 记录 `themes/[id].md` 路径；未识别 → 使用默认蚂蚁集团主题

### Phase 1 完成前
- [ ] 一次性补问缺失字段（公司、主题、受众、页数、内容、署名、日期）
- [ ] 页数推断时使用量化标准（详见 Phase 1）；推断结果与内容明显不符时，显式确认后再进入 Phase 2

### Phase 2 完成前
- [ ] 如输入是演讲稿，应用"演讲稿 → PPT 转换"规则（禁止逐段照搬）
- [ ] 内容密度符合对应场景上限（见密度规则表格）
- [ ] 执行 Phase 2.5「布局多样性自检」，验证局部 + 全局约束均满足
- [ ] 必须包含的页面（封面、目录、章节过渡、结尾）均已规划

### Phase 3 完成前
- [ ] `cp _base.html` 完成，填充 ①标题 ②主题样式 ③logo ④页脚 ⑤幻灯片内容
- [ ] 非蚂蚁集团主题：已替换 logo 路径，并验证 logo 文件实际存在
- [ ] 每张幻灯片生成后执行密度自检（演讲稿场景必须，普通场景建议）
- [ ] 所有 info-card / step-card 已包含图标

### Phase 4 完成前
- [ ] Step 4.1 保存文件
- [ ] Step 4.2 兜底扫描：验证无残留外部 `src=` 引用
- [ ] Step 4.3 告知用户：文件路径、大小（KB）、幻灯片总数、主题名称
- [ ] Step 4.4 `open` 打开浏览器

### 禁止项（红线，不可违反）
- ❌ 在 `<section>` 内写任何 logo 代码（logo 只在 `#globalLogoGroup`）
- ❌ `max-width` 写死 px（改用 `clamp()` 或 `calc(Npx * var(--vp-scale, 1))`）
- ❌ 直接编辑 `_base.html` 本身
- ❌ 编造原文无源的具体数字
- ❌ 演讲稿逐段照搬上墙
- ❌ info-card 不加图标
- ❌ 少量卡片在全宽容器里偏左留白（必须收窄居中）
- ❌ 同页出现多段长引用

---

## 进度通知规范

在关键节点向用户输出进度说明，格式统一为纯文本，不用 markdown 列表或标题。

| 时机 | 输出格式 | 示例 |
|---|---|---|
| Phase 1 完成后 | 一句话，确认收集到的关键元数据 | `已收集：腾讯 · 管理层汇报 · 12 页 · 张三 总监` |
| Phase 2 完成后 | 一句话，说明规划页数 | `规划完成，共 12 张，开始生成…` |
| Step 3.3 每张写入前 | `→ 当前序号/总数  页面标题` | `→ 03/12  技术架构现状` |
| Phase 4 Step 4.2 完成后 | 一句话，含文件大小和内嵌结果 | `单体化完成，文件大小 1.2 MB，无外部依赖。` |
| Phase 4 Step 4.3 | （见 Phase 4 输出规范） | — |

> 取消"Phase 3 开始前"的独立通知——Step 3.3 第一张的 `→ 01/12` 已传递相同信息，无需重复。序号统一补零到总数位数（如总数 12 则写 `01`，总数 100 则写 `001`）。

---

## Phase 0：自动更新 + 识别模式 + 主题检测

### 自动更新（第一步，必须执行）

在当前 skill 工作目录执行：

```bash
git pull --ff-only
```

- 成功 → 静默继续，无需告知用户
- 失败（无网络 / 有本地冲突）→ 静默忽略，继续使用当前版本，不打断流程

### 模式识别
- **Mode A：全新制作** — 从主题/大纲/草稿创建。进入 Phase 1。
- **Mode B：增强改稿** — 在现有 HTML 上修改或扩充内容。执行步骤：
  1. **完整读取现有 HTML 文件**，识别所有 `<section>` 块及其 `id`、`class`、标题文本，输出当前幻灯片清单（`N. [标题] [class]`）
  2. **模式判断**：修改范围 < 30%（≤3 张幻灯片）→ 精确改稿；修改范围 ≥ 30% 或新增内容 > 原有内容 → 告知用户建议重新生成（Mode A）
  3. **识别原有主题**（除非用户明确要求更换，否则保留）
  4. **精确定位**：按用户描述（序号、标题关键词、内容特征）定位目标 `<section>`，定位结果向用户确认后再操作
  5. **精确替换**：只修改被点名的 `<section>` 块；新增幻灯片插入到逻辑顺序最合适的位置，不打乱原有结构
  6. **未被点名的幻灯片保持原样**，不做"顺手优化"
  7. **修改后同步检查**：目录页议程项数与实际内容页数是否一致？章节逻辑是否仍然连贯？若不一致，列出问题项询问用户是否同步更新
  8. 执行与 Mode A 相同的质量检查（不溢出、结构完整、logo 路径有效）

### 主题自动检测

**读取 [themes/_index.md](themes/_index.md)**，从用户请求中提取公司关键词，优先级从高到低：
1. 用户请求原文中明确提到的公司名、产品名
2. 署名中的部门/团队名称
3. 内容中出现的公司名

Phase 1 收集到「公司」字段后，如与初步判断不一致，以用户明确填写的为准并更新主题。

**匹配规则：**
- 识别到关键词 → 记录「主题 ID」和对应的 `themes/[id].md` 路径，生成时告知用户（如”已应用阿里巴巴橙色主题”）
- 未识别到 → 默认使用蚂蚁集团+支付宝双 Logo 主题（`themes/ant-group.md`）
- 用户明确指定主题名 → 优先遵从用户指定
- 多品牌冲突、竞品对比场景 → 按 `themes/_index.md`「多品牌冲突处理」规则执行

---

## Phase 1：内容收集

**优先一次性收集以下信息：**

- **公司**（header: “公司”）：所在公司或品牌名称？（用于自动匹配主题色）
- **主题**（header: “主题”）：演讲/汇报的核心主题是什么？
- **受众**（header: “受众”）：管理层 / 团队内部 / 跨部门 / 外部客户？
- **页数**（header: “页数”）：5-8 页 / 10-15 页 / 20 页以上？
- **内容**（header: “内容”）：请直接粘贴大纲、草稿或关键要点。如果只有主题，说明即可。
- **署名**（header: “署名”）：演讲者姓名 + 角色/岗位/Title（用于封面）？
- **日期**（header: “日期”）：演讲日期？（默认今天）

如果运行环境支持 `AskUserQuestion`，一次性补问缺失项；如果不支持，就用一条普通消息补问，或在低风险场景下按默认值直接继续：

- **公司缺失** → 从内容、署名、部门名称中推断；仍无法识别则默认蚂蚁集团+支付宝双 Logo
- **受众缺失** → 按以下规则推断：
  - 含术语密度高且出现”汇报””review””决策””管理层” → 管理层汇报
  - 含”分享””经验””复盘””团队” → 团队内部
  - 含”提案””方案””客户””合作” → 外部客户 / 跨部门
  - 无法判断 → 默认”内部汇报”
- **页数缺失** → 按内容字数量化推断：
  - 内容 < 500 字 → 4–6 页
  - 500–1500 字 → 8–12 页
  - 1500–3000 字 → 12–18 页
  - > 3000 字 → 18 页以上（可分册）
  - 仍无法判断 → 默认 10 页；推断结果与内容明显不符时，进入 Phase 2 前显式确认
- **日期缺失** → 默认今天
- **署名缺失** → 从内容署名、部门信息或当前用户信息推断；无法推断则默认”演讲者”

---

## Phase 2：结构规划

根据收集的内容，自动规划幻灯片结构：

### 演讲稿转 PPT 专项规则

如果输入是”演讲全文 / 发言稿 / 逐字稿 / 长文”，先做”演讲稿 → 演示结构”的转换，不要直接把原文分段贴到页面里。

1. **PPT 是演讲辅助，不是原文上墙** — 禁止逐段照搬；单页文字量必须明显少于讲稿密度；详细解释交给演讲者口头完成。
2. **一页一判断** — 先提炼一个结论句作为标题，再保留 2–4 个支撑点；多个论点拆成多页，允许重组顺序。
3. **留金句，删铺垫** — 保留原话中的数字、判断、类比；删除寒暄、重复、口语化过渡、背景铺垫；引用只取最有代表性的 1 句。
4. **3 秒可读** — 以下任一条满足即视为达标：① 单页总字数 ≤ 80 字（含标题/副标题）；② bullet ≤ 4 条且每条 ≤ 15 字；③ 数字 ≤ 3 个且说明文字 ≤ 30 字；④ 视觉块（图表/卡片/大数字）占页面面积 ≥ 40%。副标题只做提示词，不写成第二段正文；bullet 和卡片描述优先短句，不写完整段落。
5. **第一视角** — 标题、副标题、过渡语优先”我 / 我们 / 今天想讲”；不写成”作者认为””整场发言围绕”等编辑转述口吻。

推荐转换顺序：

1. 先提炼演讲的 3-6 个核心判断
2. 再为每个判断找 2-4 个支撑点
3. 最后只挑少量最有代表性的原话做 quote

不适合保留到 PPT 的内容：

- 开场寒暄
- 重复表达同一观点的不同句子
- 口语化连接词和铺垫
- 大段故事细节
- 没有信息增量的抒情句
- 可以由演讲者口头补充的背景说明

### 视觉化原则

如果页面内容仍然“像一堆文字”，必须继续改写成更强的视觉结构，而不是直接交付。

默认遵守：

1. **先做结构，再放文字** — 优先用数字、卡片、对比、流程、章节页承载内容，不要先写满文字再找地方放。
2. **主视觉节奏清晰** — 一页可以包含多个视觉区块（如大数字 + 卡片列表 + 进度条排名），但各区块之间必须有明确的视觉层级和分组，不能混成一锅粥；内容量超出屏幕时拆页，而不是缩小字号强塞。
3. **把段落改成组件** — 长段优先改写成 `stat-block`、`step-card`、`quote-box`、`agenda-item` 等组件，而不是正文段落。
4. **关键词可扫读** — 数字、判断句必须一眼能扫到；长句只能作为补充。
5. **多横向，少纵向** — 优先并列卡片、对比、矩阵，少用连续多段纵向文字。
6. **每 3–4 页变换节奏** — 穿插章节页、金句页、超大字判断页或轻内容页，避免全稿信息密度完全一致。
7. **先判内容形态，再选版式** — 详见「内容类型 → 页面骨架 / 组件选择规则」。
8. **标题区固定** — 所有内容页默认复用统一的标题区位置；标题和副标题不应因正文密度不同而上下漂移。
9. **SVG 优先，减少文字密度** — 凡能用图形表达的关系（数量对比、趋势、占比、流程、层级、演进），优先用内联 SVG 替代文字描述；禁止用纯数字 + 文字句子描述可视化数据。不强制把已经结构化的卡片/列表改成 SVG。
10. **视觉效果分层叠加** — 重点卡片可叠加渐变背景 + 顶部色条 + 阶段 pill 标签，形成层次感；装饰性径向渐变光圈作为点缀，不要每张卡都加；具体写法见 `components.md`「设计效果规范」章节。

### 必须包含的页面
| 页面类型 | 用途 | Slide Class | 豁免条件 |
|---|---|---|---|
| 封面页 | 主题、副标题、演讲者 + 角色/岗位/Title、日期 | `slide-cover` | 无豁免，必须有 |
| 目录页 | 用 agenda-item 列出所有章节 | `slide-white` | **总页数 ≤ 6 时省略** |
| 章节过渡页 | 每个大章节开始前 | `slide-section` | **该逻辑分组内容页 < 3 时省略**；用户明确要求页数少时，章节页总数不超过内容页数的 1/3 |
| 结尾页 | Q&A 或感谢 | `slide-qa` | 无豁免，必须有 |

> **小型 PPT 规则（用户要求 ≤ 6 页时）：** 封面 + 内容页 + 结尾，不强插目录和章节过渡页。内容页数以用户指定为准，不因结构要求而强行扩充。

### 内容页密度规则

| 内容类型 | 普通场景上限 | 演讲稿场景上限 |
|---|---|---|
| 文字要点 | 10-12 条 bullet | **6-8 条**，每条 ≤ 25 字 |
| 信息卡片（横向列） | 5-6 列 info-card | **4-5 列**，卡片描述 ≤ 50 字 |
| 信息卡片（纵向堆叠） | 单列 ≤ 8 张，描述 ≤ 50 字/张 | 单列 ≤ 6 张，描述 ≤ 35 字/张 |
| 数据统计 | 6-8 个 stat-block | 6 个，配 1 句解读 |
| 对比/流程 | 4-6 个 step-card | **4 个**，描述 ≤ 35 字 |
| 表格 | 最多 10 行 × 6 列 | 最多 8 行 × 5 列 |
| 引用原话 | 1-2 段，80-150 字 | 1 段，**≤ 80 字** |
| 卡片描述正文 | 5-6 句话 | **3-4 句话**，可含完整段落 |

超出限制 → 自动拆成多页，页间用连贯的过渡标题衔接。

补充硬约束：
- **叙述型卡片页禁止 5 列**：凡是卡片内含标题 + 描述正文 + bullet 的解释型页面，默认最多 4 列；5 列只允许用于极短标签卡、纯图标卡或微型统计卡。
- **单页有效内容高度至少占可用区的 55%**：如果页面主体只占上半屏，必须通过 `justify-content:center`、收窄容器、增加第二视觉区块或改成更聚焦的纯视觉页来补齐节奏。
- **禁止“上实下空”**：页面底部连续留白明显大于顶部留白时，视为未完成布局，需要继续调整。

### 内容类型 → 页面骨架 / 组件选择规则

在把每一部分内容写成 HTML 之前，必须先做一次“内容类型判断”，再决定使用哪种布局。优先级是：**页面级骨架 > 成组组件 > 普通 bullet / 段落**。

| 内容形态 | 优先页面骨架 / 组件 | 适用场景 | 避免用法 |
|---|---|---|---|
| 全文总览 / 章节预告 | 分组目录页、`agenda-item` | 目录页、章节总览、分阶段议程 | 直接用普通 bullet 平铺全篇目录 |
| 开场判断 / 问题定义 | 开场钩子页、2×2 信息面板、`highlight-box` + `info-card` | 开场第一页正文、问题陈述、背景变化 | 用整页长段正文解释背景 |
| 单一核心结论 | `highlight-box`、`stat-block`、大数字 + 简短支撑 | 这一页只讲一个明确判断 | 做成密集列表或多主题混页 |
| 核心结论 + 数据支撑 | `insight-panel`（洞察数据面板）| 一句判断句 + 2×2 内嵌数字格 + 补充说明 | 判断句和数字分散在两个独立卡片里 |
| 并列要点 / 三原则 / 三抓手 | `three-col`、`info-card`、`stat-block` | 3 个并列观点、原则、分类 | 把 3 个平级观点写成纵向长段 |
| 双对象对比 / 两个重点方向 | `two-col`、左右图文混排 | A/B 对比、两类对象、两个重点模块 | 用单列顺序写法削弱对比感 |
| 分阶段推进 / 先后逻辑 | `step-card`、双阶段桥接流程、`step-flow-grid` | 路线图、推进步骤、阶段拆解 | 用普通 bullet 描述”第一、第二、第三” |
| 月度 / 季度路线图 | `roadmap-col`（月份路线图）| 按月拆解关键事项、项目推进节奏 | 用普通 step-card 忽略时间轴感 |
| 工作流 / 研发流程 | `flow-item`（流程格，4列×N行）| DevOps 流程、工序铺排、8 步以内横向矩阵 | 用有序列表平铺 8 个步骤 |
| 能力成熟度 / 等级体系 | `level-row`（分级列表，支持高亮当前级）| L0-L5 分级、成熟度模型、能力评分 | 用普通 bullet 描述各等级 |
| 组织支撑 / 能力体系 | `support-board`、`support-card`、`role-card` | 横向能力、机制、专项保障 | 用多段正文描述体系结构 |
| 责任清单 / 人员分工 | `support-mini-card` + `@负责人`署名 | 每项有明确负责人的任务/机制清单 | 用普通 bullet 列名字 |
| 多角色流转 / 跨方协同 | `demand-flow-board` | 多角色参与、需求流转、协作看板 | 用静态列表描述角色流转 |
| 数据结构 / 占比 / 趋势 | `stat-block`、进度条、标签云、表格、SVG 柱状图 / 折线图 / 环形图 | 数据对比、趋势、构成、分布 | 没有图形支撑却只写数字句子 |
| 排名 / 障碍优先级 | `bar-rank`（进度条排名）| 问题排序、投票统计、障碍频次 | 用纯数字列表 |
| 业务线 / 产品集合 / 技术栈 | `tag-cloud`（标签云）| 规模感展示，重要程度用字号区分 | 用等高等大的标签平铺 |
| 产品 / 系统截图 | `browser-mockup`（浏览器截图框）| 网站、后台系统、产品界面截图 | 直接裸放图片无上下文 |
| 引用原话 / 观点摘录 | `quote-box`、`insight` | 保留一句金句或关键原话 | 堆多段长引用 |
| 结尾总结 / 行动主张 | 分组总结页、`agenda-item`、`highlight-box` | 结论页、行动建议、价值回收 | 结尾再次展开大量新信息 |

### 组件选择约束

- 先选页面骨架，再决定骨架内部放什么组件。
- 一页可包含多个视觉区块，但每个区块必须有明确的分组标题或视觉间隔，不要把不同类型的内容混在同一区块里平铺。
- 如果内容天然属于 `support-board`、`demand-flow-board`、双阶段流程这类强结构页面，就不要退回普通卡片页。
- 如果只是 2-4 个短支撑点，优先用现有组件，不要新造大量自定义 HTML。
- **卡片未铺满一行时，容器必须收窄并居中**：按实际卡片数等比缩小容器宽度（`max-width`），同时 `margin:0 auto` 居中，禁止让少量卡片在全宽容器里偏向左侧留大片空白。容器宽度参考：1 张→40%、2 张→60%、3 张→80%、4 张及以上→100%；用 `style="max-width:X%;margin:0 auto;"` 写在 flex/grid 容器上。
- **内容偏少时，整组组件必须垂直居中**：若标题下只有 1 行卡片、1 组对比或 1 个总结框，`slide-body` 需要显式使用 `justify-content:center`，避免全部内容贴在上半屏。
- **单行卡片页优先“收口”**：4 张以内的横向卡片，先缩容器再加内容，不要直接铺满整行；只有需要表达“规模感”时才允许满宽。
- `components.md` 无法覆盖的复合布局（如分级列表 `level-row`、流程格 `flow-item`、进度条排名、同心圆图）允许用自定义 HTML 实现，但需保持样式一致性（沿用已有 CSS 变量和字号体系）。

### 布局多样性约束（强制）

规划完整体结构后，扫描一遍布局序列，同时满足以下局部和全局要求：

**局部约束（连续检查）：**
- 连续 4 页都是 `three-col` / `info-card` 多列 → 中间插入 `stat-block`、`highlight-box` 大字判断页、`step-card` 流程页或章节过渡页
- 连续 4 页都是普通 `styled-list` bullet → 至少 1 页改为卡片、数据或图文骨架
- 连续 3 页都是 `step-card` 流程 → 第 4 页必须换为其他骨架

**全局约束（整稿比例）：**
- 多列卡片页（`three-col` / `info-card`）占全稿内容页比例 ≤ 55%
- 整稿超过 10 页时，必须包含至少 1 页纯视觉页（大数字、金句、SVG 图表等）

**布局多样性检查清单**（生成前对照一遍）：

| 检查项 | 类型 | 要求 |
|---|---|---|
| 最长同类布局连续段 | 局部 | ≤ 3 页 |
| 多列卡片页占比 | 全局 | ≤ 55% |
| 纯视觉页（大数字/图表/金句）| 全局 | ≥ 1 页（超过 10 页时） |
| 章节过渡页间隔 | 局部 | 每 5–8 页内容页穿插 1 页 |

### Phase 2.5：布局多样性自检（规划完成后，进入 Phase 3 前必须执行）

扫描整个规划表，按以下步骤验证并修正：

1. **统计各类型布局的页数**：分别计算 three-col/info-card 多列页、step-card 流程页、styled-list bullet 页、纯视觉页（大数字/金句/SVG）的数量
2. **检查局部连续约束**：
   - 找出最长同类连续段；如果 three-col/info-card ≥ 4 连续 → 在中间插入过渡页或大字判断页
   - step-card ≥ 3 连续 → 第 4 页换其他骨架
   - styled-list ≥ 4 连续 → 至少 1 页改为卡片或图表骨架
3. **检查全局比例约束**：
   - 多列卡片页占内容页比例 > 55% → 替换超出部分为其他布局
   - 总页数 > 10 且无纯视觉页 → 插入至少 1 页（建议放在中段提振节奏）
4. **输出调整后的规划表**（如有修改，在原清单基础上用 `[调整]` 标注改动位置）

---

### 文字润色（自动执行，无需问用户）

结构规划完成后，在写 HTML 前对所有文字执行：

1. **去冗余** — 删除”我们认为””需要指出的是”等无信息量的前缀。
2. **名词化** — “进行分析”→”分析”，”做出决策”→”决策”。
3. **数字化** — 只润色原文中已有数字的表达形式（如"百分之三十七"→"37%"）；**禁止在原文无数据的地方自行编造具体数字**，无数据时保持概括性但有力的表达。
   - ✅ 允许：原文含"50+"→ 保留"50+ 轮回归测试"；原文含"故障率""下降"→ 补"故障率下降 40%"（原文已有数字或量级词）
   - ❌ 禁止：原文"取得了成果"→ 杜撰"节省成本 200 万元"；原文"反馈不错"→ 杜撰"用户满意度 92%"
4. **动词有力** — 用”推进””落地””打通””提升””收敛”替代”做””进行””开展”。
5. **层级清晰** — 主标题是结论/判断，副标题是补充说明，内容是支撑。
6. **引用克制** — 原话引用只保留最有代表性的句子；同页不出现多段长引用。

---

## Phase 3：生成 HTML

### Step 3.1：准备输出文件 + 读取主题和组件

**① 复制 base 模板**
```
cp _base.html [输出文件名].html
```
- `_base.html` 是预构建的引擎壳，含完整 CSS/JS，**禁止直接编辑 `_base.html` 本身**
- 输出文件名使用英文小写 + 连字符，例如 `antgroup-q1-review.html`

**② 并行读取以下两个文件**（同时发起，不要等一个读完再读另一个）

- **`themes/[id].md`**（Phase 0 已确定的公司主题文件，约 30 行）
  - 获取 CSS 变量块、`.slide-section` / `.slide-qa` 覆盖、logo 文件路径
  - 腾讯 / 字节跳动 / 苹果：文件内已包含补充规则，无需额外读取

- **`components.md`**（按需读取，不必全读）
  - Phase 2 结构规划时已确定每页用哪类组件，只需 Grep 提取对应章节
  - 例：只用到 `info-card` 和 `agenda-item` → 只读「信息卡片」和「目录/议程」两节

- **`icons.md`**（用到含图标槽的组件时按需 Grep）
  - 287 个 Feather Icons，按图标名一行一条，Grep 关键词即可定位
  - 涉及图标的组件：`info-card`（card-icon）、`step-card`（step-icon）、`support-card`（support-icon）、`role-card`（role-icon）、`stat-block`（stat-icon 可选）、`goal-card`（goal-icon 可选）
  - **中文主题 → 英文图标名映射参考**（先查此表，再 Grep icons.md）：

    | 中文主题关键词 | 推荐图标名 |
    |---|---|
    | 安全、防护、权限、合规 | `shield`, `lock`, `eye-off` |
    | 数据、分析、统计、报告 | `bar-chart-2`, `pie-chart`, `trending-up` |
    | 用户、客户、团队、人员 | `users`, `user`, `user-check` |
    | 流程、步骤、推进、路径 | `arrow-right`, `git-branch`, `navigation` |
    | 目标、战略、方向、规划 | `target`, `flag`, `compass` |
    | 技术、系统、架构、平台 | `cpu`, `server`, `layers` |
    | 效率、性能、速度、优化 | `zap`, `clock`, `activity` |
    | 成本、金融、收益、预算 | `dollar-sign`, `credit-card`, `trending-down` |
    | 协同、沟通、对接、整合 | `link`, `share-2`, `message-circle` |
    | 风险、问题、告警、异常 | `alert-triangle`, `x-circle`, `bell` |
    | 创新、产品、功能、特性 | `star`, `package`, `box` |
    | 运营、推广、增长、运作 | `trending-up`, `repeat`, `refresh-cw` |

  - 例：标题含"数据安全" → 先查表得 `shield` → `grep "shield" icons.md` 取 SVG 填入对应图标槽

### Step 3.2：用 Edit 工具填充占位符

复制完 `_base.html` 后，**依次用 Edit 工具替换占位符**，不要重写整个文件。

`_base.html` 已内置蚂蚁集团默认值：双 logo（蚂蚁集团 + 支付宝）。

**① 标题（必填）**
```
old: %%TITLE%%
new: [演讲主题] — [演讲者]
```

**② 主题样式覆盖（仅非蚂蚁集团主题，或用户指定其他配色时）**

读 `themes/[id].md` 的 CSS 块，用 Edit 工具替换：
```
old: <!-- %%THEME_STYLE%% -->
new:
<style>
:root { ... }
.slide-section { background: ... !important; }
.slide-qa      { background: ... !important; }
</style>
```
> 蚂蚁集团主题：跳过此步，`<!-- %%THEME_STYLE%% -->` 保留为注释即可。

**③ Logo（非蚂蚁集团主题时必须替换；用户指定非默认 logo 时也需替换）**

`#globalLogoGroup` 是唯一的 logo 节点，`position:fixed` 悬停在右上角。每个 logo 槽放两张 img：`.logo-light`（白底页显示）和 `.logo-dark`（深色页显示），CSS 按 `body.on-blue` 自动切换，**全程只改这一处，不在任何 `<section>` 里写 logo**。

**logo 在写入时立即转为 base64 data URI，不写相对路径。** 这样文件生成完毕即是自包含状态，浏览器打开后 logo 立刻显示，无需等待 Phase 4.1。

**写入前执行（对每个 logo 文件）：**
```python
import base64, mimetypes

def logo_data_uri(path):
    “””将 logo 文件转为 data URI，供直接写入 src 属性”””
    mime = mimetypes.guess_type(path)[0] or 'image/png'
    with open(path, 'rb') as f:
        return f”data:{mime};base64,{base64.b64encode(f.read()).decode()}”

# 示例
color_uri = logo_data_uri('./logos/[brand]-color.png')
white_uri = logo_data_uri('./logos/[brand]-white.png')
```

**Fallback 流程（文件不存在时按优先级选择）：**
1. 只有彩色版，无白色版 → `logo-dark` 用彩色版 data URI + `style=”filter:brightness(0) invert(1);”` 转白
2. 彩色版也不存在 → `#globalLogoGroup` 设为 `display:none`，并在告知用户时说明”未找到 [公司] logo 文件，已隐藏 logo 区域；如需展示 logo，请将文件放入 logos/ 目录”
3. 不可在 logo 区域写公司名称文字作为替代

```html
<!-- 单 logo（src 直接填 data URI，不写相对路径）-->
<div id=”globalLogoGroup” class=”logo-group-single”>
    <img class=”logo-light” src=”data:image/png;base64,...” alt=”[公司]”>
    <img class=”logo-dark”  src=”data:image/png;base64,...” alt=”[公司]”>
</div>

<!-- 双 logo（集团 + 子品牌）-->
<div id=”globalLogoGroup” class=”logo-group-dual”>
    <img class=”logo-light” src=”data:image/png;base64,...” alt=”[集团]”>
    <img class=”logo-dark”  src=”data:image/png;base64,...” alt=”[集团]”>
    <span class=”logo-divider”></span>
    <img class=”logo-light” src=”data:image/png;base64,...” alt=”[子品牌]”>
    <img class=”logo-dark”  src=”data:image/png;base64,...” alt=”[子品牌]”>
</div>

<!-- 无白色版时，logo-dark 用彩色版 data URI 加 filter 转白 -->
<img class=”logo-dark” src=”data:image/png;base64,...” alt=”[公司]” style=”filter:brightness(0) invert(1);”>

<!-- 无 logo（文件缺失时的 Fallback）-->
<div id=”globalLogoGroup” class=”logo-group-single” style=”display:none;”></div>
```

> 禁止在 `<section>` 内写任何 logo 相关代码；禁止在 `#globalLogoGroup` 上手写定位样式，CSS 已统一处理。


### Step 3.3：幻灯片 HTML 结构

每张幻灯片写入前，先输出一行进度：`→ 当前序号/总数  页面标题`，再写 `<section>` 内容。

**封面页：**
```html
<section class="slide slide-cover" id="slide-1">
    <div class="cover-arcs" aria-hidden="true">
        <div class="arc arc-1"></div>
        <div class="arc arc-2"></div>
        <div class="arc arc-3"></div>
    </div>
    <div class="cover-top reveal" style="display:flex;align-items:center;">
        <span style="color:rgba(255,255,255,0.65);font-size:clamp(0.65rem,1.1vw,0.85rem);">[部门名称]</span>
    </div>
    <div class="cover-main">
        <h1 class="cover-title reveal">[主标题]</h1>
        <p class="cover-subtitle reveal">[副标题/部门]</p>
        <p class="reveal" style="font-size:clamp(0.85rem,1.5vw,1.1rem);color:#fff;font-weight:700;">[演讲者姓名｜角色 / 岗位 / Title]</p>
    </div>
    <div class="cover-bottom">
        <div class="cover-date reveal">[日期]</div>
    </div>
</section>
```

**内容页（白色）：**
```html
<section class="slide slide-white" id="slide-N">
    <div class="slide-header center-stack">
        <span class="header-mark"></span>
        <h2 class="header-title">[页面标题（结论/判断句）]</h2>
        <p class="header-sub">[副标题（补充说明）]</p>
    </div>
    <div class="slide-body" style="justify-content:center;">
        <!-- 内容区，使用组件填充 -->
    </div>
</section>
```

**章节过渡页：**
```html
<section class="slide slide-section" id="slide-N">
    <p class="section-num reveal">PART [章节号]</p>
    <h2 class="section-title reveal">[章节标题]</h2>
    <p class="section-desc reveal">[一句话说明这一章节要回答的问题]</p>
</section>
```

**结尾页：**
```html
<section class="slide slide-qa" id="slide-N">
    <h2 class="qa-title reveal">Q&amp;A</h2>
    <p class="qa-sub reveal">[感谢语]</p>
</section>
```

**密度自检**（演讲稿场景每页生成后必须执行；普通场景建议执行）：

对照以下量化标准自检，任一项不达标则精简或拆页：

| 检查项 | 演讲稿上限 | 普通场景上限 | 处理方式 |
|---|---|---|---|
| bullet 条数 | ≤ 8 条 | ≤ 12 条 | 超出则拆页 |
| 每条 bullet 字数 | ≤ 25 字 | ≤ 40 字 | 超出则压缩 |
| 单页总字数（含标题） | ≤ 150 字 | ≤ 250 字 | 超出则拆页 |
| info-card 卡片描述 | ≤ 35 字/张 | ≤ 50 字/张 | 超出则精简 |
| 标题类型 | 必须是结论句 | 必须是结论句 | 话题词改为判断句 |
| 同页并列模块 | 各有分组标题 | 各有分组标题 | 补充分组或拆页 |

**大屏自适应自检（已内置，无需手动写入）**

4K 缩放 CSS（`@media (min-width:1921px)`）和 `updateViewportScale()` JS 已直接内置于 `_base.html`，`cp` 后自动继承，**生成时不需要做任何额外操作**。

写 inline 固定尺寸时仍须遵守以下规范，确保自定义内容在大屏下等比缩放：

| 场景 | 写法 |
|---|---|
| 普通间距、字号 | 用 `clamp()` 或 CSS 变量，自动随 rem 缩放 |
| 固定 px 尺寸（圆点、色条高度、图标盒子） | `calc(Npx * var(--vp-scale, 1))` |
| 容器最大宽度 | `min(calc(XYZpx * var(--vp-scale, 1)), 100%)` |
| `max-width` 直接写死 px | **禁止**，改用上一行写法或 `clamp()` |

内容区宽度约束（所有幻灯片必须遵守）：

```html
<div style="width:min(100%,var(--deck-safe-width));margin:0 auto;">...</div>
```

---

## Phase 4：输出

**Step 4.1：保存**

文件名使用英文小写 + 连字符，例如 `antgroup-q1-review.html`；主题关键词是中文时转为拼音或英译，避免中文文件名造成路径兼容问题。

**Step 4.2：单体化兜底扫描**

Logo 已在 Step 3.2 ③ 写入时转为 base64，此步骤仅扫描遗漏的外部引用并补充内嵌，确保零依赖。正常情况下脚本会快速通过。

```bash
python3 scripts/inline-images.py [文件名].html
```

**Step 4.3：告知用户**

- 文件路径、文件大小（KB）、幻灯片总数、应用的主题名称
- 导航：方向键 / 空格翻页，点击右侧圆点导航，`F` 全屏
- 如需修改：直接编辑 HTML 文件，或告知我继续修改

**Step 4.4：用浏览器打开**

```bash
open [文件名].html
```

---

## 支持文件

| 文件 | 用途 | 何时读取 |
|---|---|---|
| [_base.html](_base.html) | 预构建引擎壳（含完整 CSS/JS），生成时 `cp` 为输出文件再用 Edit 填充 | Phase 3 Step 3.1（必须） |
| [themes/_index.md](themes/_index.md) | 主题识别规则 + 集团/子品牌关键词表 + logo 索引 + 双 logo 规则 | Phase 0 主题检测（只读此一个文件） |
| `themes/[id].md` | 单公司主题文件（~30 行）：CSS 变量 + logo 路径 + 补充规则 | Phase 3 Step 3.1（只读匹配到的那一个） |
| [components.md](components.md) | 所有可用组件的 HTML 片段参考 | Phase 3 按需 Grep 对应章节，不必全读 |
| [icons.md](icons.md) | 287 个 Feather Icons 内联 SVG，用于 info-card / step-card 图标 | 生成含 info-card / step-card 的页面时按需 Grep |
| `logos/[变体]-white.png` | 公司 Logo 白色版（深色幻灯片使用） | Phase 3 Step 3.1（有 logo 时） |
| `logos/[变体]-blue.png` / `logos/[变体]-color.png` | 公司 Logo 彩色版（白色幻灯片使用） | Phase 3 Step 3.1（有 logo 时） |
| [index.html](index.html) | 完整示例文档，含所有组件的真实渲染样例 | 需要查阅组件骨架细节时（只读参考） |

---


## 非目标 / 不处理的事情

- 不直接生成 .pptx 文件，只输出单个 HTML 文件
- 不负责从互联网自动抓取事实内容，除非用户明确提供素材
- 不伪造演讲者身份、职位、部门信息
- 不擅自修改未被点名的现有页面
- 不保证所有品牌主题都与官方品牌规范完全一致
