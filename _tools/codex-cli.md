---
title: Codex CLI
nav_order: 11
card: true
tagline: 轻量的命令行 Agent，沙箱和审批机制清晰，适合脚本化与 CI 场景
category: 命令行 Agent
platform: macOS / Linux / Windows
updated: 2026-10
repo: https://github.com/openai/codex
---

## 一句话定位

把编码 Agent 做成命令行工具，可控性放在比较重要的位置：
文件写入范围、命令执行权限都可以显式约束，因此容易嵌入自动化流程。

## 适合谁

- 想把 Agent 跑在 CI、定时任务或内部工具里
- 需要明确控制它能读写哪些路径、能不能执行命令
- 喜欢在终端里工作，但不希望每次都开一个重量级会话

## 我实际怎么用

**沙箱内跑批量任务**：把改动范围限定在一个目录里，让它处理机械性修改，
结束后用 `git diff` 审查。

**自动化流水线**：在脚本里以非交互方式调用，产出补丁或报告，再由人审核。

```bash
# 安装（Node.js 环境）
npm install -g @openai/codex

# 交互式会话
codex
```

## 注意事项

{: .tip }
把「允许写哪些目录」设得比「我觉得需要」更窄一点。限制明确的 Agent 更容易预测，出错时影响也小。

{: .pitfall }
非交互模式适合规则清晰的任务。需要多轮讨论的设计类问题，交互模式反而更快。

{: .note }
具体参数与安装方式请以仓库 README 为准。
