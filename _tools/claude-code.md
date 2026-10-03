---
title: Claude Code
nav_order: 10
card: true
tagline: 命令行里直接改仓库的 Agent，适合「理解整个项目再动手」的改造任务
category: 命令行 Agent
platform: macOS / Linux / Windows
updated: 2026-10
docs: https://docs.claude.com/en/docs/claude-code/overview
---

## 一句话定位

在终端里运行的编码 Agent。它的特点是会主动读多个文件、生成改动计划，
然后直接修改仓库——而不是只给你一段代码让你自己粘贴。

## 适合谁

- 需要在较大仓库里做跨文件改造
- 习惯终端工作流，希望改动直接落在工作区、可以随时 `git diff` 检查
- 愿意为质量接受一定的 token 成本

## 我实际怎么用

**跨文件重构**：改动涉及多个模块时，先让它输出计划，确认后再执行。
计划阶段是最省时间的部分。

**补测试**：给一个已有测试文件作为风格参考，让它按同样的结构补全。

**排查问题**：把完整报错和我已经排除的可能性一起给它。

```bash
# 项目中开始一次会话
claude

# 非交互模式，适合放进脚本
claude -p "把 src/legacy/ 下所有 print 调用替换成 logger"
```

## 注意事项

{: .pitfall }
默认工作模式会直接修改文件。习惯先看一眼改动范围——尤其是它会同时改多个文件的时候。

{: .tip }
把项目约定写进仓库根目录的 `CLAUDE.md`，比每次对话里重复交代有效得多。

{: .note }
安装命令和参数会随版本变化，请以官方文档为准。
