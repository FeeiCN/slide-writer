<p align="center">
  <img src="logos/slide-writer.png" alt="Slide-Writer" width="200"/>
</p>

# Slide-Writer

[![Version](https://img.shields.io/badge/version-0.2.0-blue.svg)](https://github.com/FeeiCN/slide-writer/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![在线演示](https://img.shields.io/badge/在线演示-feei.cn-blue.svg)](https://feei.cn/slide-writer/)
[![English](https://img.shields.io/badge/Docs-English-blue.svg)](README.md)

> 您只需专注目标、观点与判断，Slide-Writer 负责结构、写作、优化与呈现。

<p align="center">
  <img src="examples/before-after.png" alt="Slide-Writer Demo" width="100%"/>
</p>

## 快速开始

```bash
# Claude Code
git clone https://github.com/FeeiCN/slide-writer.git ~/.claude/skills/slide-writer

# Codex
git clone https://github.com/FeeiCN/slide-writer.git ~/.agents/skills/slide-writer
```

```text
/slide-writer 帮我生成一个「人为什么要吃饭」的演讲 PPT，使用支付宝风格。
```

```text
/slide-writer 基于演讲稿 examples/tencent-pony-ma.md，生成一个演讲 PPT。
```

```text
/slide-writer 我明天有一个演讲，基于 examples/alibaba-ai-rollout.md 帮我生成一个 PPT。
```

## 核心特性

**简单易用**：无论是输入一句话、大纲、草稿还是演讲稿，都可自动生成一套完整演示文稿。
- 从一个想法或主题生成 PPT
- 从演讲稿转成 PPT
- 从大纲扩展为完整 PPT
- 从笔记、文档、报告转成 PPT
- 优化或调整已有 HTML 演示稿
- 基于同一内容生成不同受众版本

**企业级视觉表达**：专为企业汇报、管理层沟通、部门分享、峰会演讲等正式场景设计。
- 内置 14 家互联网公司品牌主题，支持主题自动识别与切换
- 统一 Logo 展示、配色、排版与视觉规范
- 精确对齐、统一间距、专业视觉节奏

**完整的演示结构**：自动规划章节，拆页，将文档表达转为演示表达。
- 内置封面页、目录页、章节过渡页、结尾页
- 优先复用已沉淀的页面骨架，而非每页从零拼布局

**自动润色与内容重构**：对内容进行演示化改写，让表达更准确、更简练、更适合展示。
- 优化标题层级、要点列表、正文段落
- 自动润色每一句话，提升表达的准确性与力量感

**丰富的页面表达能力**：动画、数据可视化、步骤流程、表格、卡片布局等，无需外部库。
- 柱状图、折线图、环形图（基于内联 SVG）
- 步骤流程图、图文混排、卡片化信息展示

**纯前端单文件交付**：输出单个 HTML 文件，浏览器直接打开，无需 PowerPoint 或 Keynote。
- CSS / JS / 图片全部内置
- 支持键盘翻页、导航点、全屏展示
- 响应式布局，适配不同屏幕与投影分辨率

**自动保持最新**：每次运行时自动拉取最新版本，新主题、新组件无需手动更新。

## 仓库结构

- `SKILL.md`：Skill 定义与执行规则
- `themes.md`：主题与 Logo 规则
- `components.md`：页面组件库
- `index.html`：基础模板 + 页面骨架演示
- `examples/`：示例输入输出
- `TESTING.md`：测试说明

### 快速测试

1. 选一个 [examples](examples) 里的样例作为输入。
2. 让模型基于本仓库里的 `SKILL.md` 生成 `test-*.html` 到仓库根目录。
3. 运行：

```bash
./scripts/preview.sh
```

4. 浏览器打开 `http://localhost:8000/test-xxx.html` 预览。

更完整的测试流程和回归清单见 [TESTING.md](TESTING.md)。
