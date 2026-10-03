---
title: 工具总览
nav_order: 1
permalink: /tools/
description: 我长期在用的 AI Agent 工具清单。
---

# 工具总览

只收录我自己在真实项目里持续使用的工具。每个工具页都写清楚三件事：
**它是干什么的、我实际怎么用、什么地方会踩坑**。

{: .note }
工具迭代很快，页面里的链接和细节请以官方文档为准。这里的 `updated` 字段是最近一次核对时间。

## 分类

**命令行 Agent**：直接在终端里操作代码仓库，适合脚本化和批量任务。

- [Claude Code]({{ '/tools/claude-code/' | relative_url }})：重度改造仓库时的首选
- [Codex CLI]({{ '/tools/codex-cli/' | relative_url }})：轻量、适合脚本和 CI 场景

**编辑器内 Agent**：在 IDE 里围绕当前文件工作，适合边写边改。

- [Cursor]({{ '/tools/cursor/' | relative_url }})：代码补全和局部改造体验最好

**能力扩展**：把外部系统接进 Agent 的标准协议。

- [MCP 服务器]({{ '/tools/mcp-servers/' | relative_url }})：让 Agent 用上你的数据库、API 和文档

**本地运行**：数据不出本机时使用。

- [本地模型运行]({{ '/tools/local-models/' | relative_url }})：Ollama / LM Studio 的取舍

## 挑选标准

{: .tip }
我判断一个工具是否值得留在工具箱，只看一条：**它是否减少了我要做的判断次数**。功能多但需要频繁纠错的工具，最后都会被放弃。
