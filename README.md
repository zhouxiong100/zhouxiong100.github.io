# 知识分享站

基于 Jekyll + [just-the-docs](https://github.com/just-the-docs/just-the-docs) 的静态站点，
用于分享 AI Agent 使用经验与工具。推送到 `main` 后由 GitHub Actions 自动构建并发布到
<https://zhouxiong100.github.io>。

## 目录结构

| 路径 | 作用 |
| --- | --- |
| `_config.yml` | 站点信息、导航、搜索、提示块、永久链接 |
| `index.md` | 首页（Hero、最新文章、工具卡片） |
| `articles/index.md` | 文章归档，按分类分组 |
| `about.md` / `404.md` | 关于页与 404 页 |
| `_posts/` | 文章，文件名用英文 slug + 日期 |
| `_tools/` | 工具页（集合），自动进入侧边栏「工具」分组 |
| `_layouts/post.html` | 文章页布局（日期、分类、标签、上下篇） |
| `_layouts/tool.html` | 工具页布局（一句话定位、链接、类型） |
| `_sass/color_schemes/ink.scss` | 自定义配色变量 |
| `_sass/custom/custom.scss` | 组件级自定义样式 |
| `_includes/` | 页脚、搜索占位、自定义 head 等覆盖 |

## 写一篇新文章

在 `_posts/` 下新建 `YYYY-MM-DD-english-slug.md`：

```yaml
---
title: "文章标题"
date: 2026-10-08 10:00:00 +0800
category: 经验          # 决定归档分组
tags: [提示词, 工作流]     # 可选，显示为标签
lede: "一句话摘要，会显示在列表和文章开头。"
excerpt: "给 RSS 用的摘要。"
---

正文从这里开始。
```

可用提示块（跟在段落后独占一行）：

```markdown
{: .tip }       经验 / 绿色
{: .note }      说明 / 蓝色
{: .warn }      注意 / 黄色
{: .pitfall }   踩坑 / 红色
```

## 加一个工具

在 `_tools/` 下新建 `tool-name.md`：

```yaml
---
title: 工具名
nav_order: 10
tagline: 一句话说明它是干什么的、适合谁
category: 命令行工具
platform: macOS / Windows / Linux
updated: 2026-10
website: https://example.com
docs: https://example.com/docs
repo: https://github.com/example/example
card: true        # 设为 true 会出现在首页「工具箱」
---
```

`nav_order: 1` 是 `_tools/overview.md`（工具总览），新增工具从 10 开始编号即可。

## 本地预览

需要 Ruby 3.x（Windows 用 [RubyInstaller](https://rubyinstaller.org/) 带 DevKit 的版本）：

```bash
bundle install
bundle exec jekyll serve --livereload
```

## 部署

- `push` 到 `main` → 构建并发布
- Pull Request → 只做构建校验，不发布
- 也可以在 Actions 页面手动 `workflow_dispatch`
