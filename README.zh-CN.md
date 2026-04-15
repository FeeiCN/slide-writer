<p align="center">
  <img src="examples/slide-writer.png" alt="Slide-Writer" width="200"/>
</p>

# Slide-Writer

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/FeeiCN/slide-writer/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![在线演示](https://img.shields.io/badge/在线演示-feei.cn-blue.svg)](https://feei.cn/slide-writer/examples/)
[![English](https://img.shields.io/badge/Docs-English-blue.svg)](README.md)

> 您只需专注目标、观点与判断，Slide-Writer 负责结构、写作、优化与呈现。

<p align="center">
  <a href="https://feei.cn/slide-writer/examples/">
    <img src="examples/before-after.png" alt="Slide-Writer Demo" width="100%"/>
  </a>
</p>

## 快速开始

> 推荐搭配 **Claude Code** 或 **Codex** 使用，模型建议选择 Claude Sonnet、Claude Opus 或 GPT-4o。

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

**任意输入 → 企业级 PPT**：一句话、大纲、演讲稿、笔记、已有 HTML 均可——Slide-Writer 自动完成重构、改写和演示化排版。

**14 家品牌主题，自动识别**：覆盖蚂蚁、阿里、腾讯、字节等。输入关键词即自动匹配主题、Logo 和配色，无需手动配置。

**单文件交付**：输出一个独立 HTML 文件，CSS、JS、图片全部内置。浏览器直接打开，无需 PowerPoint 或 Keynote。

**自动保持最新**：每次运行自动拉取最新主题、组件和生成规则，无需手动更新。

## 仓库结构

- `SKILL.md`：Skill 定义与执行规则
- `themes/_index.md`：主题识别与 Logo 索引
- `themes/[id].md`：单主题样式与 Logo 规则
- `components.md`：页面组件库
- `template.html`：基础生成壳
- `examples/index.html`：页面骨架演示与完整示例文档
- `examples/`：示例输入与输出
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

---

如果 Slide-Writer 帮你节省了时间，欢迎点个 ⭐ — 让更多人发现这个项目。
