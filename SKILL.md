---
name: slide-writer
display_name: Slide-Writer
description: 把想法、大纲、文档或草稿变成结构清晰、设计精良的企业级 HTML 演示文稿。
version: 0.1.0
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
---

# Slide-Writer

把任意输入（想法、大纲、草稿文档、会议纪要）转化为企业级 HTML 演示文稿，自动匹配所在公司的品牌主题。

## 核心原则

1. **品牌一致** — 从请求中自动识别公司，应用对应品牌色主题；内置 14 家互联网公司主题。
2. **内容为王** — 自动润色每句话：去冗余、精简、让表达更有力。
3. **不溢出** — 每张幻灯片必须在 100vh 内完整呈现，内容多则分页，绝不出现滚动条。
4. **单文件交付** — 输出单个 `.html` 文件，CSS/JS 全部内联，零依赖，浏览器直接打开。

---

## Phase 0：识别模式 + 主题检测

### 模式识别
- **Mode A：全新制作** — 从主题/大纲/草稿创建。进入 Phase 1。
- **Mode B：增强改稿** — 在现有 HTML 上修改或扩充内容。执行步骤：
  1. 完整读取现有 HTML 文件
  2. 识别原有主题（除非用户明确要求更换，否则保留）
  3. 按用户指示定位需要修改的幻灯片，精确替换对应 `<section>` 块
  4. 新增幻灯片插入到逻辑顺序最合适的位置，不打乱原有结构
  5. 未被点名的幻灯片保持原样，不做"顺手优化"
  6. 修改完成后，执行与 Mode A 相同的质量检查（不溢出、结构完整、logo 路径有效）

### 主题自动检测（必须在 Phase 1 之前执行）

**读取 [themes.md](themes.md)**，从以下来源提取公司关键词，自动匹配主题：
1. 用户请求原文（提到的公司名、产品名）
2. 署名中的部门/团队名称
3. 内容中出现的公司名

**匹配规则：**
- 识别到关键词 → 静默应用对应主题，生成时告知用户（如”已应用阿里巴巴橙色主题”）
- 未识别到 → 默认使用蚂蚁集团蓝色主题
- 用户明确指定主题名 → 优先遵从用户指定
- 多品牌冲突、竞品对比场景 → 按 [themes.md](themes.md)「多品牌冲突处理」规则执行

---

## Phase 1：内容收集

**优先一次性收集以下信息：**

- **主题**（header: "主题"）：演讲/汇报的核心主题是什么？
- **受众**（header: "受众"）：管理层 / 团队内部 / 跨部门 / 外部客户？
- **页数**（header: "页数"）：5-8 页 / 10-15 页 / 20 页以上？
- **内容**（header: "内容"）：请直接粘贴大纲、草稿或关键要点。如果只有主题，说明即可。
- **署名**（header: "署名"）：演讲者姓名 + 角色/岗位/Title（用于封面）？
- **日期**（header: "日期"）：演讲日期？（默认今天）

如果运行环境支持 `AskUserQuestion`，一次性补问缺失项；如果不支持，就用一条普通消息补问，或在低风险场景下按默认值直接继续：

- 受众缺失 → 默认“管理层/跨部门汇报”
- 页数缺失 → 默认 5-8 页
- 日期缺失 → 默认今天
- 署名缺失 → 默认留空，但不要伪造职位

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

### 视觉化原则

如果页面内容仍然“像一堆文字”，必须继续改写成更强的视觉结构，而不是直接交付。

默认遵守：

1. **先做结构，再放文字** — 优先用数字、卡片、对比、流程、章节页承载内容，不要先写满文字再找地方放。
2. **一页一种主视觉节奏** — 例如 3 个大数字、2 列对比、1 组 step-card；允许页面留白，不要为了信息完整把屏幕填满。
3. **把段落改成组件** — 长段优先改写成 `stat-block`、`step-card`、`quote-box`、`agenda-item` 等组件，而不是正文段落。
4. **关键词可扫读** — 数字、判断句必须一眼能扫到；长句只能作为补充。
5. **多横向，少纵向** — 优先并列卡片、对比、矩阵，少用连续多段纵向文字。
6. **每 3–4 页变换节奏** — 穿插章节页、金句页、超大字判断页或轻内容页，避免全稿信息密度完全一致。
7. **优先复用页面骨架** — 先从 `components.md` 和 `index.html` 中选择合适的页面级骨架（如统一标题区、分组目录、双阶段流程、横向支撑板、流转看板、开场钩子页），再往骨架里填内容，不要每页都从零拼布局。
8. **标题区固定** — 所有内容页默认复用统一的标题区位置；标题和副标题不应因正文密度不同而上下漂移。
9. **先判内容形态，再选版式** — 每一部分内容在落到页面前，先判断它更像“目录 / 判断 / 对比 / 流程 / 分组能力 / 图文说明 / 数据占比 / 责任结论”中的哪一种，再从 `components.md` 选择最合适的页面骨架或组件组合，不允许跳过这一步直接自由拼版。

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

### 必须包含的页面
| 页面类型 | 用途 | Slide Class |
|---|---|---|
| 封面页 | 主题、副标题、演讲者 + 角色/岗位/Title、日期 | `slide-cover` |
| 目录页 | 用 agenda-item 列出所有章节 | `slide-white` |
| 章节过渡页 | 每个大章节开始前 | `slide-section` |
| 结尾页 | Q&A 或感谢 | `slide-qa` |

### 内容页密度规则

| 内容类型 | 单页最多 |
|---|---|
| 文字要点 | 4-6 条 bullet |
| 演讲稿改写 bullet | 2-4 条，且每条尽量短句 |
| 信息卡片 | 3 列（3 张 info-card）或 2×3 grid |
| 数据统计 | 3-4 个 stat-block |
| 对比/流程 | 2-4 个 step-card |
| 表格 | 最多 5 行 × 4 列 |
| 引用原话 | 1 段，优先控制在 30-50 字内 |

超出限制 → 自动拆成多页，页间用连贯的过渡标题衔接。

### 内容类型 → 页面骨架 / 组件选择规则

在把每一部分内容写成 HTML 之前，必须先做一次“内容类型判断”，再决定使用哪种布局。优先级是：**页面级骨架 > 成组组件 > 普通 bullet / 段落**。

| 内容形态 | 优先页面骨架 / 组件 | 适用场景 | 避免用法 |
|---|---|---|---|
| 全文总览 / 章节预告 | 分组目录页、`agenda-item` | 目录页、章节总览、分阶段议程 | 直接用普通 bullet 平铺全篇目录 |
| 开场判断 / 问题定义 | 开场钩子页、2×2 信息面板、`highlight-box` + `info-card` | 开场第一页正文、问题陈述、背景变化 | 用整页长段正文解释背景 |
| 单一核心结论 | `highlight-box`、`stat-block`、大数字 + 简短支撑 | 这一页只讲一个明确判断 | 做成密集列表或多主题混页 |
| 并列要点 / 三原则 / 三抓手 | `three-col`、`info-card`、`stat-block` | 3 个并列观点、原则、分类 | 把 3 个平级观点写成纵向长段 |
| 双对象对比 / 两个重点方向 | `two-col`、左右图文混排 | A/B 对比、两类对象、两个重点模块 | 用单列顺序写法削弱对比感 |
| 分阶段推进 / 先后逻辑 | `step-card`、双阶段桥接流程、`step-flow-grid` | 路线图、推进步骤、阶段拆解 | 用普通 bullet 描述“第一、第二、第三” |
| 组织支撑 / 能力体系 | `support-board`、`support-card`、`role-card` | 横向能力、机制、专项保障 | 用多段正文描述体系结构 |
| 多角色流转 / 跨方协同 | `demand-flow-board` | 多角色参与、需求流转、协作看板 | 用静态列表描述角色流转 |
| 数据结构 / 占比 / 趋势 | `stat-block`、进度条、标签云、表格、SVG 柱状图 / 折线图 / 环形图 | 数据对比、趋势、构成、分布 | 没有图形支撑却只写数字句子 |
| 引用原话 / 观点摘录 | `quote-box`、`insight` | 保留一句金句或关键原话 | 堆多段长引用 |
| 结尾总结 / 行动主张 | 分组总结页、`agenda-item`、`highlight-box` | 结论页、行动建议、价值回收 | 结尾再次展开大量新信息 |

### 页面选择判断顺序

生成每一页前，按下面顺序判断：

1. 这部分内容是在**总览结构**，还是在讲**一个结论**？
2. 它更像**并列关系**、**对比关系**，还是**阶段关系**？
3. 它是否涉及**多角色协同**、**组织支撑**或**流转过程**？
4. 它的核心信息是否其实是**数字 / 构成 / 趋势**，应该优先视觉化？
5. 如果一个页面可以同时套多个组件，优先选择**最能强化这页主关系**的那个骨架，其它信息降为支撑，而不是混搭所有组件。

### 组件选择约束

- 先选页面骨架，再决定骨架内部放什么组件。
- 一页只保留一种主关系，不要同时把“对比 + 流程 + 目录 + 图表”塞进同一页。
- 如果内容天然属于 `support-board`、`demand-flow-board`、双阶段流程这类强结构页面，就不要退回普通卡片页。
- 如果只是 2-4 个短支撑点，优先用现有组件，不要新造大量自定义 HTML。
- 只有当 `components.md` 现有骨架都不适配时，才允许做轻量自定义布局。

---

## Phase 3：内容润色（自动执行，无需问用户）

演讲稿场景的专项处理规则已在 Phase 2 定义，本阶段覆盖所有输入类型的通用润色。

在生成 HTML 之前，对所有文字进行润色：

1. **去冗余** — 删除”我们认为””需要指出的是”等无信息量的前缀。
2. **名词化** — “进行分析”→”分析”，”做出决策”→”决策”。
3. **数字化** — 有数据的地方补充具体数字；没有数据时保持概括性但有力。
4. **动词有力** — 用”推进””落地””打通””提升””收敛”替代”做””进行””开展”。
5. **层级清晰** — 主标题是结论/判断，副标题是补充说明，内容是支撑。
6. **引用克制** — 原话引用只保留最有代表性的句子；同页不出现多段长引用。

---

## Phase 4：生成 HTML

### Step 4.1：准备输出文件 + 确定主题 + 读取 Logo

**生成流程（必须按顺序执行）：**

1. **复制 shell 模板**：`cp _base.html [输出文件名].html`
   - `_base.html` 是预构建的引擎壳，含完整 CSS/JS，**禁止直接编辑 `_base.html` 本身**
   - 输出文件名使用英文小写 + 连字符，例如 `antgroup-q1-review.html`

2. **读取参考资料**（只读，不复制）：
   - 读取 [themes.md](themes.md) 中已匹配主题的 CSS 变量块
   - 读取 [components.md](components.md) 了解所有可用组件和页面骨架
   - 先为每一部分内容做”内容类型判断”，再从 [components.md](components.md) 里选择最优页面骨架 / 组件组合
   - 如果命中腾讯、字节跳动、苹果主题，额外读取 `themes.md` 中对应的「主题补充规则」

**`_base.html` 占位符说明：**

| 占位符 | 位置 | 替换内容 |
|---|---|---|
| `%%TITLE%%` | `<title>` | 演讲主题 — 演讲者 |
| `<!-- %%THEME_STYLE%% -->` | `</style>` 后 | `<style>` 主题变量覆盖块 |
| `%%LOGO_CLASS%%` | `#globalLogoGroup` 的 class | `logo-group-single` 或 `logo-group-dual` |
| `<!-- %%LOGO_GROUP_CONTENT%% -->` | `#globalLogoGroup` 内部 | logo `<img>` 标签（白底页用） |
| `%%FOOTNOTE%%` | `#footnote` | 页脚说明文字 |
| `<!-- %%SLIDES%% -->` | body 内容区 | 所有 `<section>` 幻灯片 HTML |

**Logo 识别与展示逻辑（必须执行）：**

查阅 [themes.md](themes.md) 中的「子品牌归属表」和「双 Logo 展示规则」：

1. **先查子品牌**：用户请求中是否包含子品牌关键词？
   - 是 → 记录「集团 ID」和「子品牌 ID」
   - 否 → 只记录「集团 ID」，子品牌 ID 为 null

2. **确认 logo 文件是否存在**：查询 themes.md 的「已有 Logo 文件」表
   - 默认使用 themes.md 中记录的真实 PNG 文件名
   - 集团 logo 存在 → 以 themes.md 中记录的 `./logos/...` PNG 文件名为准
   - 子品牌 logo 存在 → 以 themes.md 中记录的 `./logos/...` PNG 文件名为准
   - 待补充 → 对应变量设为 null

3. **按 themes.md「双 Logo 展示规则」中的情况一/二/三/四生成 logo HTML 片段**，后续幻灯片直接复用
4. **统一最终展示尺寸**：不同品牌 PNG 的原始尺寸、宽高比和透明留白都可能不同，最终必须按“视觉高度一致”处理
   - 不以源 PNG 像素大小直接决定最终展示尺寸
   - 统一使用 `height` / `max-height` 控制高度，配合 `width:auto`
   - 必须保留 `object-fit:contain`，禁止拉伸、压扁、裁切
   - 超宽 logo 可以更宽，但高度仍受同一上限约束
   - 分隔线高度要和两侧 logo 的视觉高度匹配
   - 同一份 deck 中，内容页、封面、章节页、结尾页的 logo 要保持一致的视觉基线
   - 默认启用“自动适配”逻辑：根据 logo 的天然宽高比自动微调展示高度和最大宽度，而不是为每个品牌手工写死尺寸
   - 超宽 logo 自动略微降高并收窄最大宽度；偏高或偏方的 logo 可小幅增高
   - 双 logo 场景下，分隔线高度应跟随两侧 logo 的最终显示高度一起更新

### Step 4.2：用 Edit 工具填充占位符

复制完 `_base.html` 后，**依次用 Edit 工具替换 6 个占位符**，不要重写整个文件：

**① 标题**
```
old: %%TITLE%%
new: [演讲主题] — [演讲者]
```

**② 主题样式覆盖**
```
old: <!-- %%THEME_STYLE%% -->
new:
<style>
:root {
    --primary:       [主色];
    --primary-dark:  [深色];
    --primary-light: [浅色];
    --primary-pale:  [极浅色];
    --primary-dim:   [透明色];
    --cover-bg:      [封面渐变];
    --section-bg:    [章节页渐变];
}
.slide-section { background: [渐变] !important; }
.slide-qa      { background: [渐变] !important; }
</style>
```

**③ Logo class**
```
old: %%LOGO_CLASS%%
new: logo-group-single（单 logo）或 logo-group-dual（双 logo）
```

**④ Logo 内容（白底页右上角）**
```
old: <!-- %%LOGO_GROUP_CONTENT%% -->
new: <img src=”./logos/[brand]-color.png” alt=”[公司名]”>
     （双 logo 时加 <span class=”logo-divider”></span> 和第二个 <img>）
```

**⑤ 页脚说明**
```
old: %%FOOTNOTE%%
new: * 仅限内部交流使用（或用户指定的文字）
```

**⑥ 幻灯片内容**
```
old: <!-- %%SLIDES%% -->
new: 所有 <section> 幻灯片 HTML
```

**主题应用说明：**
- `.slide-section` 和 `.slide-qa` 在基础 CSS 中使用硬编码渐变，必须用 `!important` 覆盖。
- `--primary-pale` 会自动影响 `info-card` 背景、`agenda-item` hover、`highlight-box` 等，无需逐一覆盖。
- logo 统一使用 `./logos/...` 相对路径。

**内容页布局原则：**
- 优先选用 `components.md` 中已有的页面级骨架，不要每页从零拼版。
- 每一页先回答”这页最核心的关系是什么”，再选骨架：
  - 总览 / 议程 → 分组目录页或 `agenda-item`
  - 开场判断 / 问题定义 → 开场钩子页或 2×2 信息面板
  - 并列观点 → `three-col` / `info-card`
  - 双对象对比 → `two-col` / 左右图文混排
  - 阶段推进 → `step-card` / 双阶段桥接流程
  - 组织支撑 → `support-board`
  - 多角色流转 → `demand-flow-board`
  - 数据构成 / 趋势 → `stat-block` / SVG 图表 / 表格 / 环形图
  - 结尾总结 → 分组总结页 / `highlight-box`

### Step 4.3：幻灯片 HTML 结构

**封面页：**
```html
<section class="slide slide-cover" id="slide-1">
    <div class="cover-arcs" aria-hidden="true">
        <div class="arc arc-1"></div>
        <div class="arc arc-2"></div>
        <div class="arc arc-3"></div>
    </div>
    <div class="cover-top reveal" style="display:flex;align-items:center;justify-content:space-between;">
        <span style="color:rgba(255,255,255,0.65);font-size:clamp(0.65rem,1.1vw,0.85rem);">[部门名称]</span>
        <!-- 有 Logo 时插入，无则省略整个 logo 容器 -->
        <img src="./logos/[brand]-white.png" alt="[公司名] Logo"
             style="height:clamp(20px,3vh,36px);object-fit:contain;opacity:0.92;">
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

**页面骨架优先级：**
- 目录页优先使用“分组目录页”或 `agenda-item` 列表，而不是普通 bullet。
- 开场第一页正文优先考虑“开场钩子页”或 2×2 信息面板。
- 讲专项体系时优先考虑 `support-board`。
- 讲跨角色协同时优先考虑 `demand-flow-board`。
- 讲两阶段推进逻辑时优先考虑 `step-flow-top + step-flow-bridge + step-flow-bottom`。
- 普通内容页也优先使用统一标题区 + `slide-body`，不要混用多套标题位置。

**章节过渡页：**
```html
<section class="slide slide-section" id="slide-N">
    <!-- 有 Logo 时插入右上角 -->
    <img src="./logos/[brand]-white.png" alt="[公司名] Logo"
         style="position:absolute;top:clamp(1.2rem,2.5vh,2rem);right:clamp(1.5rem,3vw,2.5rem);height:clamp(18px,2.5vh,28px);width:auto;object-fit:contain;opacity:0.7;">
    <p class="section-num reveal">PART [章节号]</p>
    <h2 class="section-title reveal">[章节标题]</h2>
    <p class="section-desc reveal">[一句话说明这一章节要回答的问题]</p>
</section>
```

**结尾页：**
```html
<section class="slide slide-qa" id="slide-N">
    <!-- 有 Logo 时插入右上角 -->
    <img src="./logos/[brand]-white.png" alt="[公司名] Logo"
         style="position:absolute;top:clamp(1.2rem,2.5vh,2rem);right:clamp(1.5rem,3vw,2.5rem);height:clamp(18px,2.5vh,28px);width:auto;object-fit:contain;opacity:0.7;">
    <h2 class="qa-title reveal">Q&amp;A</h2>
    <p class="qa-sub reveal">[感谢语]</p>
</section>
```

---

## Phase 5：输出

1. **保存** — 将文件保存到当前工作目录。文件名使用英文小写 + 连字符，例如 `antgroup-q1-review.html`、`alibaba-ai-strategy.html`；主题关键词是中文时转为拼音或英译，避免中文文件名造成路径兼容问题。
2. **告知用户：**
   - 文件路径、幻灯片总数、应用的主题名称
   - 导航：方向键 / 空格翻页，点击右侧圆点导航，`F` 全屏
   - 如需修改：直接编辑 HTML 文件，或告知我继续修改

---

## 支持文件

| 文件 | 用途 | 何时读取 |
|---|---|---|
| [_base.html](_base.html) | 预构建引擎壳（含完整 CSS/JS），生成时 `cp` 为输出文件再用 Edit 填充 | Phase 4 Step 4.1（必须，替代旧的 index.html 复制流程） |
| [themes.md](themes.md) | 14 家互联网公司品牌主题 CSS 变量定义 + Logo 索引 | Phase 0 主题检测 + Phase 4 生成时 |
| [components.md](components.md) | 所有可用组件的 HTML 片段参考 | Phase 4 生成内容页时 |
| `logos/[变体]-white.png` | 公司 Logo 白色版 PNG（深色幻灯片使用） | Phase 4 Step 4.1（有 logo 时） |
| `logos/[变体]-blue.png` / `logos/[变体]-color.png` | 公司 Logo 彩色版 PNG（白色幻灯片使用） | Phase 4 Step 4.1（有 logo 时） |
| [index.html](index.html) | 完整示例文档，含所有组件的真实渲染样例 | 需要查阅组件骨架细节时（只读参考，不再直接复制） |

---

## 本地开发与测试

在当前仓库开发本 skill 时，优先使用下面这些本地文件作为测试基线：

- 生成 shell：[_base.html](_base.html)（cp 后填充占位符）
- 完整示例参考：[index.html](index.html)（只读）
- 主题库：[themes.md](themes.md)
- 组件库：[components.md](components.md)
- Logo 素材：[logos](logos)
- 仅在需要外部文件时再使用 PNG/AVIF fallback
- 测试样例：[examples](examples)
- 测试说明：[TESTING.md](TESTING.md)

如果用户是在这个仓库里开发或调试 skill，优先复用这些本地文件，不要回退到 Claude 全局目录中的旧副本。

生成本地测试稿时：

- 默认从 [index.html](index.html) 出发
- 只替换主题变量、logo 路径和具体内容
- 不要从已有 `test-*.html` 继续复制
- 生成后优先运行 `./scripts/smoke-test.sh [文件名].html` 做静态检查，再进行浏览器人工验收


## 非目标 / 不处理的事情

- 不直接生成 .pptx 文件，只输出单个 HTML 文件
- 不负责从互联网自动抓取事实内容，除非用户明确提供素材
- 不伪造演讲者身份、职位、部门信息
- 不擅自修改未被点名的现有页面
- 不保证所有品牌主题都与官方品牌规范完全一致
- 不自动设计复杂动画或图表引擎
