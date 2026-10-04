---
title: 工具总览
nav_order: 1
permalink: /tools/
description: 我长期在用的 AI Agent 工具清单。
---

# 工具总览

当前只保留我持续使用、且已经形成稳定工作流的工具。每个工具页写清楚三件事：
**它是干什么的、我实际怎么用、什么地方会踩坑**。

{: .note }
工具迭代很快，页面里的链接和细节请以官方文档为准。这里的 `updated` 字段是最近一次核对时间。

## 分类

**命令行 Agent**：直接在终端里操作代码仓库，适合脚本化和批量任务。

- [Codex CLI]({{ '/tools/codex-cli/' | relative_url }})：轻量、适合脚本和 CI 场景

**模型与路由**：统一管理多个 Agent 的模型和供应商配置。

- [Magpie]({{ '/tools/magpie/' | relative_url }})：一个入口切换 Claude Code、Codex、Gemini CLI 的模型

## 挑选标准

{: .tip }
我判断一个工具是否值得留在工具箱，只看一条：**它是否减少了我要做的判断次数**。功能多但需要频繁纠错的工具，最后都会被放弃。
