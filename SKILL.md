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

## 进度通知规范

在关键节点向用户输出进度说明，格式统一为纯文本，不用 markdown 列表或标题。

| 时机 | 输出格式 | 示例 |
|---|---|---|
| Phase 0 完成后 | 一句话 | `已识别主题：腾讯，应用腾讯蓝主题。` |
| Phase 2 完成后 | 完整页面清单（见下方规范） | — |
| Phase 3 开始前 | 一句话 | `开始生成 HTML，共 12 张…` |
| Step 3.3 每张写入前 | `→ 当前序号/总数  页面标题` | `→ 3/12  技术架构现状` |
| Phase 4 保存完成后 | （见 Phase 4 输出规范） | — |

**Phase 2 完成后的页面清单格式：**

用两列对齐输出所有幻灯片编号和标题，让用户在等待生成期间已知晓全貌：

```
规划完成，共 12 张：
01 封面          07 PART 3 落地路径
02 目录          08 三阶段推进计划
03 PART 1 背景   09 组织与资源保障
04 现状与问题    10 关键成功指标
05 根因分析      11 总结与展望
06 PART 2 方案   12 Q&A
```

列数根据总页数自适应：≤8 张单列，9-16 张双列，>16 张三列。章节过渡页标题前加 `PART N` 前缀以示区分。

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
  1. 完整读取现有 HTML 文件
  2. 识别原有主题（除非用户明确要求更换，否则保留）
  3. 按用户指示定位需要修改的幻灯片，精确替换对应 `<section>` 块
  4. 新增幻灯片插入到逻辑顺序最合适的位置，不打乱原有结构
  5. 未被点名的幻灯片保持原样，不做"顺手优化"
  6. 修改完成后，执行与 Mode A 相同的质量检查（不溢出、结构完整、logo 路径有效）

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

- 公司缺失 → 从内容、署名、部门名称中推断；仍无法识别则默认蚂蚁集团+支付宝双 Logo
- 受众缺失 → 从内容语气、术语密度、场景词（”汇报””提案””分享””复盘”等）推断；无法推断则默认”个人汇报”
- 页数缺失 → 根据内容信息量推断合理页数（要点数量、章节数、数据密度）；无法推断则默认 10-13 页
- 日期缺失 → 默认今天
- 署名缺失 → 从内容署名、部门信息或当前用户信息推断；无法推断则默认”路人甲”

---

## Phase 2：结构规划

根据收集的内容，自动规划幻灯片结构：

### 演讲稿转 PPT 专项规则

如果输入是”演讲全文 / 发言稿 / 逐字稿 / 长文”，先做”演讲稿 → 演示结构”的转换，不要直接把原文分段贴到页面里。

1. **PPT 是演讲辅助，不是原文上墙** — 禁止逐段照搬；单页文字量必须明显少于讲稿密度；详细解释交给演讲者口头完成。
2. **一页一判断** — 先提炼一个结论句作为标题，再保留 2–4 个支撑点；多个论点拆成多页，允许重组顺序。
3. **留金句，删铺垫** — 保留原话中的数字、判断、类比；删除寒暄、重复、口语化过渡、背景铺垫；引用只取最有代表性的 1 句。
4. **3 秒可读** — 观众应在 3 秒内看懂这页在讲什么；副标题只做提示词，不写成第二段正文；bullet 和卡片描述优先短句，不写完整段落。
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
- **卡片未铺满一行时，容器必须收窄并居中**：按实际卡片数等比缩小容器宽度（`max-width`），同时 `margin:0 auto` 居中，禁止让少量卡片在全宽容器里偏向左侧留大片空白。
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

### 文字润色（自动执行，无需问用户）

结构规划完成后，在写 HTML 前对所有文字执行：

1. **去冗余** — 删除”我们认为””需要指出的是”等无信息量的前缀。
2. **名词化** — “进行分析”→”分析”，”做出决策”→”决策”。
3. **数字化** — 只润色原文中已有数字的表达形式（如"百分之三十七"→"37%"）；**禁止在原文无数据的地方自行编造具体数字**，无数据时保持概括性但有力的表达。
4. **动词有力** — 用”推进””落地””打通””提升””收敛”替代”做””进行””开展”。
5. **层级清晰** — 主标题是结论/判断，副标题是补充说明，内容是支撑。
6. **引用克制** — 原话引用只保留最有代表性的句子；同页不出现多段长引用。

---

## Phase 3：生成 HTML

### Step 3.1：准备输出文件 + 读取主题和组件

**① 复制 shell 模板**
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
  - 例：标题含"安全" → `grep "shield" icons.md` 取 SVG 填入对应图标槽

### Step 3.2：用 Edit 工具填充占位符

复制完 `_base.html` 后，**依次用 Edit 工具替换占位符**，不要重写整个文件。

`_base.html` 已内置蚂蚁集团默认值：双 logo（蚂蚁集团 + 支付宝）、页脚”* 仅限内部交流使用”。**②③④ 仅在用户明确指定不同内容时才需替换，否则保持默认不改。**

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

**③ Logo（仅用户指定非默认 logo 时）**

`#globalLogoGroup` 是唯一的 logo 节点，`position:fixed` 悬停在右上角。每个 logo 槽放两张 img：`.logo-light`（白底页显示）和 `.logo-dark`（深色页显示），CSS 按 `body.on-blue` 自动切换，**全程只改这一处，不在任何 `<section>` 里写 logo**。

```html
<!-- 单 logo -->
<div id=”globalLogoGroup” class=”logo-group-single”>
    <img class=”logo-light” src=”./logos/[brand]-color.png” alt=”[公司]”>
    <img class=”logo-dark”  src=”./logos/[brand]-white.png” alt=”[公司]”>
</div>

<!-- 双 logo（集团 + 子品牌）-->
<div id=”globalLogoGroup” class=”logo-group-dual”>
    <img class=”logo-light” src=”./logos/[集团]-color.png” alt=”[集团]”>
    <img class=”logo-dark”  src=”./logos/[集团]-white.png” alt=”[集团]”>
    <span class=”logo-divider”></span>
    <img class=”logo-light” src=”./logos/[子品牌]-color.png” alt=”[子品牌]”>
    <img class=”logo-dark”  src=”./logos/[子品牌]-white.png” alt=”[子品牌]”>
</div>

<!-- 无白色版时，logo-dark 用彩色版加 filter 转白 -->
<img class=”logo-dark” src=”./logos/[brand]-color.png” alt=”[公司]” style=”filter:brightness(0) invert(1);”>

<!-- 无 logo -->
<div id=”globalLogoGroup” class=”logo-group-single” style=”display:none;”></div>
```

> 禁止在 `<section>` 内写任何 logo 相关代码；禁止在 `#globalLogoGroup` 上手写定位样式，CSS 已统一处理。

**④ 页脚说明（仅用户指定不同文字时）**

默认已内置”* 仅限内部交流使用”。需要更换时：
```
old: * 仅限内部交流使用
new: [用户指定的文字]
```

**⑤ 幻灯片内容（必填）**
```
old: <!-- %%SLIDES%% -->
new: 所有 <section> 幻灯片 HTML
```

**主题应用说明：**
- `.slide-section` 和 `.slide-qa` 在基础 CSS 中使用硬编码渐变，必须用 `!important` 覆盖。
- `--primary-pale` 会自动影响 `info-card` 背景、`agenda-item` hover、`highlight-box` 等，无需逐一覆盖。

### Step 3.2.5：大屏自适应（已内置，无需手动写入）

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

**演讲稿专项密度检查**（输入为演讲稿时，每页生成后执行）：

对照以下标准自检，不达标则继续精简或拆页：

- 这一页，站在 3 米外能在 5 秒内看完吗？→ 不能则继续删
- 卡片 / bullet 描述里有完整段落吗？→ 有则压缩到 1 句话
- 同一页里多个并列模块是否各有分组标题和视觉间隔？→ 没有则补充分组，或在内容量超出屏幕时再拆页
- 标题是结论句还是话题词？→ 必须是结论句（"我们做了 X" 而非 "X 的情况"）

---

## Phase 4：输出

1. **保存** — 将文件保存到当前工作目录。文件名使用英文小写 + 连字符，例如 `antgroup-q1-review.html`、`alibaba-ai-strategy.html`；主题关键词是中文时转为拼音或英译，避免中文文件名造成路径兼容问题。
2. **用浏览器打开** — 执行 `open [文件名].html` 自动在默认浏览器中打开。
3. **执行 Phase 4.1 单体化** — 浏览器打开后，立即执行单体化，将所有外部图片内嵌为 base64，确保文件零依赖、可直接传播。无需询问用户，默认执行。
4. **告知用户：**
   - 文件路径、文件大小（KB）、幻灯片总数、应用的主题名称
   - 导航：方向键 / 空格翻页，点击右侧圆点导航，`F` 全屏
   - 如需修改：直接编辑 HTML 文件，或告知我继续修改

---

## Phase 4.1：单体化（每次必须执行）

将所有外部图片引用替换为 base64 内嵌 Data URI，使 HTML 文件完全自包含，无需 `logos/` 目录，可直接复制传播。

**执行步骤：**

**① 扫描文件中所有外部图片引用**

```bash
grep -o 'src="[^"]*"' [文件名].html | grep -v 'data:' | sort -u
```

**② 对每个本地图片路径，执行 base64 内嵌替换**

```bash
LOGO_B64=$(base64 -i [图片路径] | tr -d '\n') && \
sed -i '' "s|src=\"[图片相对路径]\"|src=\"data:image/png;base64,${LOGO_B64}\"|g" [文件名].html
```

- 图片格式为 `.png` → `data:image/png;base64,`
- 图片格式为 `.jpg`/`.jpeg` → `data:image/jpeg;base64,`
- 图片格式为 `.svg` → `data:image/svg+xml;base64,`
- 多张图片逐一替换，每次替换后验证 `grep -c 'data:image'` 计数递增

**③ 验证无残留外部引用**

```bash
grep -o 'src="[^"]*"' [文件名].html | grep -v 'data:'
```

输出为空则表示所有图片已内嵌。

**④ 告知用户最终结果**

输出一行说明：文件路径 + 文件大小（`wc -c` 换算为 KB）+ 确认无外部依赖。

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
