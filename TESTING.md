# Testing Slide-Writer

目标是让 skill 调整后可以快速做三件事：复现生成、打开预览、按清单验收。

## 本地开发说明

在当前仓库开发或调试 skill 时，优先复用本地文件，不要回退到 Claude 全局目录中的旧副本。

## 快速流程

1. 在当前仓库中挑一个测试样例：
   - `examples/antgroup-quarterly-review.md`
   - `examples/alibaba-ai-rollout.md`
   - `examples/revise-existing-deck.md`
2. 让模型基于 `SKILL.md` 生成或修改 HTML，并把结果写到仓库根目录，例如 `test-antgroup.html`。全新测试稿默认从 `template.html` 出发，并在需要组件参考时查阅 `examples/index.html`，不要从旧的 `test-*.html` 继续复制。
3. 先在仓库根目录运行 `./scripts/smoke-test.sh test-antgroup.html`。
4. 再运行 `./scripts/preview.sh`。
5. 浏览器打开 `http://localhost:8000/test-antgroup.html`。
6. 按下面的验收清单检查。

## 推荐测试指令

### 新生成

```text
使用当前目录的 slide-writer skill，基于 examples/antgroup-quarterly-review.md 生成一个单文件 HTML 演示稿，输出到 ./test-antgroup.html。先严格读取本仓库里的 SKILL.md、themes/_index.md、对应的 themes/[id].md、components.md、template.html 和 examples/index.html，再开始生成。所有测试稿都从 template.html 出发，并参考 examples/index.html，不要从已有 test-*.html 派生。
```

### 改现有稿

```text
使用当前目录的 slide-writer skill，读取 ./test-antgroup.html，并根据 examples/revise-existing-deck.md 的要求直接改稿，覆盖原文件。
```

## 静态 Smoke Test

```bash
./scripts/smoke-test.sh test-antgroup.html
```

这个脚本会做轻量检查：

- HTML 中引用的本地 logo 文件是否存在
- 是否保留 `progress-bar`、`nav-dots`、`fullscreenBtn`、`footnote`
- 是否包含封面、目录、章节页、结尾页
- 是否仍然存在明显错误的根目录 logo 路径

## 手工验收清单

- 主题识别正确：能根据关键词命中对应公司主题。
- Logo 路径有效：页面里没有坏图标。
- 页面无滚动条：每一页都完整落在 `100vh` 内。
- 封面署名完整：默认包含“姓名｜角色 / 岗位 / Title”，而不只是名字。
- 层级清晰：标题是结论句，副标题是补充说明。
- 内容页标题区固定：不同内容页的标题和副标题位置保持一致，不因正文多少而上下漂移。
- 内容密度受控：长列表、表格、卡片没有溢出。
- 页面骨架优先：高频页面优先复用模板中已有的骨架（如分组目录、双阶段流程、横向支撑板、流转看板），而不是每页从零拼布局。
- 如果输入是演讲稿：没有把原文逐段搬上 PPT，单页文字明显少于讲稿原文密度。
- 如果输入是演讲稿：每页都是“判断/结论 + 支撑”，而不是大段讲稿摘抄。
- 如果输入是演讲稿：整体更像上台演讲用的 PPT，而不是阅读型资料页。
- 如果输入是演讲稿：副标题、bullet、quote 都足够短，观众能在几秒内抓住重点。
- 如果输入是演讲稿：默认保留第一视角，不要出现明显的编辑转述口吻。
- 页面有明显视觉结构：不是整页连续文字，至少使用数字、卡片、对比、引用、章节页中的一种。
- 连续几页不能都长得一样：应有节奏变化，而不是所有页都是同一种文字卡片。
- 目录、章节页、结尾页都存在。
- 键盘翻页、导航点、进度条仍然可用。
- 浏览器直接打开时样式正常，不依赖构建工具。
- `./scripts/smoke-test.sh` 通过，没有缺失文件或关键结构报错。

## 回归重点

- 改 `themes/_index.md` 或任一 `themes/[id].md` 后，至少跑一个蚂蚁系和一个阿里系样例。
- 改 `components.md` 后，至少检查 `info-card`、`agenda-item`、`step-card`、`stat-block`，以及页面级骨架（如分组目录或支撑板）是否仍然可用。
- 改 `template.html` 或 `examples/index.html` 后，至少检查目录页、内容页、章节页、结尾页和 logo / progress / nav / fullscreen 行为，并确认所有内容页标题区位置一致。
- 改 `SKILL.md` 的生成规则后，至少同时测一次全新生成和一次增强改稿。

## 产物约定

- 建议把临时测试产物命名为 `test-*.html` 放在仓库根目录。
- 如果需要长期保留样例，再手动整理到独立目录，避免污染根目录素材。
