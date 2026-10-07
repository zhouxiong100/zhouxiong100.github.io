---
title: "Answer me with HTML：让 Agent 用一页 HTML 回答复杂问题"
date: 2026-10-07 10:00:00 +0800
category: skill&amp;MCP&amp;工具
tags: [AI Agent, Skills, 可视化]
lede: "一个 agent skill：你问一个复杂问题，Agent 不再甩给你一屏文字，而是产出一页带图、能直接读的 HTML。"
excerpt: "拆解 answer-me-with-html 的核心思路：模型只写内容草稿，布局、配色、画图全部交给 CLI 完成，输出 token 省 6 倍，速度还更快。"
---

## 它解决什么问题

问 Agent 一个复杂问题（比如「讲一下 TCP 三次握手」「梳理这个仓库的模块关系」），
最常见的回答是一大段文字。文字不是不对，是**难读**——
概念之间的关系、多步流程、多方对比，用大段文字表达都很吃力。

![同一个 TCP 问题，左边是纯文字回答，右边是带时序图、状态图和表格的一页 HTML]({{ '/assets/images/text-vs-page.png' | relative_url }})

这个 skill 的思路是：让 Agent 输出一页 HTML。有时序图、状态图、对比表，
打开就能看懂，还能离线保存、直接分享。

## 为什么不直接让模型写 HTML

可以，现在模型写 HTML 不差。但代价是输出 token——
每一行 CSS、每一个包裹用的 `div`、每一个 SVG 坐标，都要模型一个 token 一个 token 敲出来，
而你等的就是输出 token。

这个 skill 的做法是分工：**模型只写内容草稿，其余交给 CLI。**

作者用同一个模型、同一批问题做了对比（3 个主题 × 3 次，取中位数）：

| | 直接让模型写 HTML | 用这个 skill |
| :--- | ---: | ---: |
| 输出 token | 5,341 | **870（省 6.1 倍）** |
| 耗时 | 33 秒 | **12 秒（快 2.8 倍）** |
| 单次成本 | $0.092 | **$0.067（省 27%）** |

![左侧是 9,351 token / 57 秒生成的手写页面，右侧是 899 token / 11 秒由 skill 生成的页面，两者内容相当]({{ '/assets/images/plain-vs-skill.png' | relative_url }})

{: .note }
成本数字和你的运行环境有关：上下文很重的配置下（每次都要重读大量工具和规则），
skill 增加的两次往返可能比省下的 token 更贵。速度优势在任何配置下都成立。

## 工作流程

1. 你照常提问，比如「解释一下 TCP 三次握手」
2. Agent 写一份很短的 Markdown 草稿（内容 + 图表的文本描述）
3. 草稿交给 skill 自带的 CLI，约 50ms 后产出一页完整的 HTML

模型写的草稿大概长这样：

````markdown
---
title: TCP 三次握手
---
## 三次握手 {span=2}
```sequence num
Client -> Server: SYN, seq=x
Server -> Client: SYN+ACK, seq=y, ack=x+1
Client -> Server: ACK, ack=y+1
```
````

布局、面板摆放、主题、流程图坐标（用 dagre 排）、时序图间距，全部由 CLI 计算。
产物是**单个 `.html` 文件**，无 CDN、无外链字体，离线可打开。

![skill 生成的 TCP 三次握手完整页面：标题 + 时序图 + 状态图 + 标志位表格]({{ '/assets/images/tcp-en.png' | relative_url }})

## 什么时候它会出页面

Agent 自己判断：多概念关联、多步流程、多方对比时值得一页；
一行就能答完的问题（「`ls` 怎么显示隐藏文件」）不会触发。
也可以直接说「用 HTML 解释一下」强制触发。

| 你问什么 | 你得到什么 |
| :--- | :--- |
| 「解释 TCP 三次握手」 | 时序图 + 状态图 + 标志位表格 |
| 「这个仓库的模块怎么组织的」 | 目录树 + 调用关系图 |
| 「Redis 还是 Memcached」 | 带 ✓ ✗ 的对比表 + 结论 |
| 「这段代码哪里有问题」 | 逐句标注，问题词和修改建议 |
| 「Kubernetes 是怎么发展来的」 | 关键节点高亮的时间线 |

页面右上角可以切主题和明暗模式、提交你的回复、复制生成页面的 Markdown 源稿。

## 加分项：讲解视频

同一份草稿每段配一句旁白，`am video` 就能生成 3Blue1Brown 风格的讲解页：
图示随旁白逐步出现、相机自动聚焦被点名的节点、同名节点跨场景平滑移动、
单文件离线可播。作者测下来比手写视频页面**省 17.8 倍输出 token、快 11.8 倍**。

![3Blue1Brown 风格讲解页的四帧：标题卡、带高亮的时序图、流程图、对比表]({{ '/assets/images/video-en.png' | relative_url }})

{: .tip }
视频旁白支持 ElevenLabs（设置 `ELEVENLABS_API_KEY`），没有就用系统 TTS，都没有就只出字幕。这是一个可选功能，不配也能用。

## 一些工程细节

- **出错能自愈**：草稿写错了，CLI 返回行号、组件名和一个正确示例，Agent 一轮就能改对
- **引用真实代码**：代码块可以写成 ```` ```ts src=routes.ts lines=18-30 ````，CLI 直接读文件，页面上的代码就是真实代码，带行号和复制按钮
- **写作风格检查**：每次渲染都跑一套改编自 ASD-STE100 的规则（句子长度、空洞动词、模糊数量词），只警告不拦截，可调严格模式
- **三种内置主题**：`blueprint`（工程图纸风）、`shadcn`（干净卡片）、`paper`（长文阅读），默认按内容自动选

## 安装

需要 Node.js 20+，不用 `npm install`，CLI 随 skill 打包。最简单的方式是把这句话发给你的 Agent：

> Install Answer me with HTML: read https://raw.githubusercontent.com/QingYunA/answer-me-with-html/main/INSTALL.md and follow it.

支持 Claude Code、Codex、Cursor、OpenCode 等。也可以一行命令：

```bash
npx skills add QingYunA/answer-me-with-html
```

{: .pitfall }
默认每次生成页面后自动打开浏览器（`open: on`）。如果弹窗打断你，用 `am config set open off` 关掉，或者直接在对话里说「不要自动打开页面」。

## 我的看法

这个 skill 最值得借鉴的不是「输出 HTML」，而是**把确定性的工作从模型手里拿走**。
布局、配色、画图坐标——这些规则明确的活儿，让代码算比让模型猜既快又稳。
模型的上下文和输出 token 留给真正需要判断的部分：内容本身。

这也是它和「直接让模型写 HTML」的本质区别：一个是让模型做它擅长且不擅长的事的混合体，
另一个是明确分工——模型负责内容，工程负责呈现。

## 延伸阅读

- GitHub：[QingYunA/answer-me-with-html](https://github.com/QingYunA/answer-me-with-html)
- 站内文章：[AI Agent 是什么？从 Chatbot、Workflow 到 Harness]({{ '/posts/2026/10/ai-agent-harness/' | relative_url }})
- 站内文章：[Matt 的新 Skill 实测：并行开发是怎么跑起来的]({{ '/posts/2026/10/matt-skills-parallel/' | relative_url }})
