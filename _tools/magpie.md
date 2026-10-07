---
title: Magpie
nav_order: 12
nav_exclude: true
card: true
tagline: 用一个入口管理多个 Agent 的模型，让 Claude Code、Codex、Gemini CLI 共用同一套供应商
category: 模型与路由
platform: macOS / Linux / Windows
updated: 2026-10
website: https://usemagpie.ai
repo: https://github.com/yetone/magpie
---

## 一句话定位

Magpie 是一个本地模型管理器：它把机器上已有的 AI Agent 列在同一个界面里，
点一下就能切换模型；同时内置本地网关，让不同 Agent 复用同一个供应商配置和订阅登录。

## 它解决的问题

- 每个 Agent 的模型配置分散在不同文件里，切模型要在多处来回找
- 手里有多个供应商或订阅账号，想让不同 Agent 共用
  （例如让 Codex 用 DeepSeek、Claude Code 用 Kimi）
- 需要统一查看各 Agent 的当前模型、配额和用量

## 核心能力

- **统一管理模型**：支持 Claude Code、Codex、Gemini CLI、OpenCode 等常见 Agent
- **本地网关**：默认监听 `127.0.0.1:3425`，把不同 Agent 的 API 请求转发到指定供应商
- **保护配置文件**：只修改需要变的字段，尽量保留原配置的注释和格式，并原子写入
- **多形态界面**：菜单栏、独立窗口、TUI、浏览器界面和纯 CLI 都能用

## 常用命令

```bash
# 安装（macOS / Linux / Windows）
curl -fsSL https://usemagpie.ai/install.sh | sh

# 查看本机所有 Agent 及其当前模型
magpie ls

# 设置某个 Agent 的模型
magpie claude moonshot/kimi-k2.5
magpie codex deepseek/deepseek-chat

# 添加供应商并刷新模型列表
magpie provider add deepseek sk-...
magpie sync
```

## 我的取舍

{: .tip}
如果你同时在用多个 CLI Agent、多个模型供应商或多个订阅账号，Magpie 能明显减少配置成本。

{: .pitfall}
如果只用一个 Agent、一个官方模型，直接改官方配置更简单；引入一层网关反而会增加排查成本。

{: .note}
Magpie 会修改各 Agent 的配置文件。首次使用前建议先备份 `~/.claude`、`~/.codex` 等目录，或至少确认当前配置可用后再切换。
