---
title: "Matt 的新 Skill 实测：并行开发是怎么跑起来的"
date: 2026-10-06 10:00:00 +0800
category: 工作流拆解
tags: [AI Agent, Skills, 并行开发]
lede: "Matt Pocock 的 skills 仓库新增了一个并行开发入口 /implement-spec：把工单当成任务图，把「就绪」的工单分给多个子 Agent，各自在独立 worktree 里按 TDD 实现，最后汇到一个集成分支。"
excerpt: "拆解 Matt Pocock 新 skill /implement-spec 的并行开发流程：任务图、就绪前沿、worktree 并发、集成分支，以及这套做法什么时候值得用。"
---

## 先给结论

Matt Pocock 的 `mattpocock/skills` 仓库里，最近把 **`/implement-spec`** 正式升级成了工程类 Skill。
它做的事情很直接：**把一张工单列表当成任务图，把互不阻塞的工单同时交给多个子 Agent 去写，最后合并到一条集成分支上。**

这就是「并行开发」在这里的真实含义——不是一个人开几个窗口手动切换，而是 harness 替你调度多个子 Agent。

其中的关键取舍是：

- **并行的单位是工单之间的依赖关系**，不是随便切文件。
- **每个子 Agent 有自己独立的 worktree**，互不踩脚。
- **所有结果汇到一条集成分支**，再由 `code-review` 统一收口。

## 它到底在解决什么问题

如果你用 Agent 做过稍微大一点的需求，多半遇到过一个尴尬：

一个 spec 拆成十几张工单，本轮对话一次只能做一张。做完第一张，上下文已经很长；
换到第二张，又得重新建立理解。等到全做完，你花在「切换和复述」上的时间，比写代码还多。

原来的 `/implement` 是**逐张工单顺序实现**的。`/implement-spec` 的思路是换一个维度：
既然工单之间有依赖关系，那就先看清整张图，然后把「当前没人挡着我」的那一批同时开跑。

![并行开发：把任务图里的就绪前沿分给多个子 Agent，各自在 worktree 中实现后汇入集成分支]({{ '/assets/images/agent-parallel-dev.svg' | relative_url }})

## 任务图、前沿和 worktree

要理解它，只需要三个概念。

### 一、工单是任务图，不是步骤列表

`/to-tickets` 会把你已经讨论清楚的方案，拆成一组**带阻塞关系的工单**——
每张工单都会声明「我依赖谁」。于是整批工单构成一张有向图，而不是一条直线。

{: .tip }
拆的时候建议竖切：一张工单穿透数据层、接口和前端，做完就是一小段端到端可验证的功能，而不是「先建表、再写接口、再做页面」这种横切。

### 二、前沿（frontier）：这一刻能开跑的工单

因为工单之间存在阻塞，任何时刻都会有一批**前置工单都已完成的工单**，它们随时可以被认领。

这批工单就叫**前沿**。并行开发的全部调度逻辑，都围绕「找出当前前沿 → 派发 → 完成后再找新前沿」展开。

### 三、worktree：每个子 Agent 一块独立工作区

这是并行能成立的前提。每个 implementer 子 Agent 都在**自己的 git worktree 和分支**上干活，
所以多个 Agent 同时改代码不会互相覆盖。

子 Agent 开工前会先确认自己的 worktree 基于集成分支；做完之后再把手上的活合回集成分支。

## 一次运行是怎么走的

`/implement-spec` 的流程可以概括成六步：

1. **读图**：读取 spec 和工单，理解任务图与依赖关系。
2. **探路（可选）**：派一个探索子 Agent 把相关代码和外部文档摸清楚，笔记存到仓库外的目录，供后续所有子 Agent 复用，让 implementer 专注写代码。
3. **建集成分支**：所有并行结果最终都合到这一条分支上。
4. **并发实现**：每个工单派一个 implementer 子 Agent，各自在独立 worktree 上、按 `tdd`（红-绿-重构）实现。
5. **合并**：某个子 Agent 完成后，由一个 merger 子 Agent 把它的成果合进集成分支；一旦前沿发生变化，就继续派发新的子 Agent。
6. **收口**：全部完成后跑 `code-review`，把发现的问题交给一个 implementer 子 Agent 统一修，最后清理所有 worktree。

{: .note }
父子 Agent 之间**主要靠上下文指针沟通**：指向 spec、工单、探索笔记和提交，而不是把信息复制一遍。这既省 token，也避免同一条信息出现两个版本。

## 为什么这次值得认真看

三个点让这套东西区别于「多开几个终端」。

**画图先于开工。** 并行不是拍脑袋切任务，而是先有一张带依赖的图。图对了，并发才有意义。

**纪律被复用了。** 每个并行分支里跑的仍然是同一套约束——先红后绿的 `/tdd`、单一职责的 `/code-review`。
并行放大的是**已经存在的正确做法**，而不是失控的自由发挥。

**收口是流程的一部分。** 并行本身会制造更多合并点，所以它把「合并 + 代码审查」写进了必经步骤，而不是留给你事后补救。

## 什么时候值得用，什么时候别用

{: .tip }
值得用：需求已经通过 `grill-me` 之类的对话逼清楚、能拆成若干条**互不阻塞**的工单、并且项目有测试作为反馈回路。这种情况下并行能明显缩短墙上时钟时间。

{: .pitfall }
别用：改动很小、任务高度耦合、或者方案本身还没想清楚。这时候强行并行，只会把「没想清楚」放大成更多冲突和更多返工。

还要认清一点：**并行不省成本和 token，只省你的等待时间。** 多个子 Agent 同时跑，算力和额度是成倍消耗的。
它真正适合的，是那种「反正要等很久」的大块可分解工作。

## 怎么开始

```bash
# Claude Code 插件方式（托管、只读、随官方 marketplace 更新）
claude plugins install mattpocock-skills

# 或者把 skill 作为可编辑的文件装进你的项目
npx skills@latest add mattpocock/skills
```

装完先在仓库里跑一次 `/setup-matt-pocock-skills`，它会问你用哪个 issue tracker、triage 用哪些标签、文档放哪里。
之后典型链路是：

```text
/grill-me  → 把需求问清楚
/to-spec   → 整理成 spec
/to-tickets→ 拆成带依赖的任务图
/implement-spec → 并行实现 + 合并 + review
```

## 一句话回顾

- **任务图**：工单之间有阻塞关系，不是一条直线。
- **前沿**：这一刻所有前置都完成、可以立即开跑的工单。
- **worktree 隔离**：每个子 Agent 一块独立工作区，互不覆盖。
- **集成分支 + code-review**：并行开跑，集中收口。

`/implement-spec` 的价值，不在于「让 Agent 变快」，而在于它把**并行开发需要的那套秩序**——
依赖图、工作区隔离、合并纪律、代码审查——做成了可以一键触发的流程。

## 延伸阅读

- GitHub：[mattpocock/skills —— Skills for Real Engineers](https://github.com/mattpocock/skills)
- 站内文章：[AI Agent 是什么？从 Chatbot、Workflow 到 Harness]({{ '/posts/2026/10/ai-agent-harness/' | relative_url }})
- 站内文章：[我把 AI Agent 放进研发流程的四个位置]({{ '/posts/2026/09/agent-workflow/' | relative_url }})
