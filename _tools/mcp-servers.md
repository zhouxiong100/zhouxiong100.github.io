---
title: MCP 服务器
nav_order: 13
card: true
tagline: 用统一协议把数据库、API、文档接进 Agent，避免为每个客户端重复写适配
category: 能力扩展
platform: 取决于具体 server
updated: 2026-10
website: https://modelcontextprotocol.io
repo: https://github.com/modelcontextprotocol/servers
---

## 一句话定位

MCP 是让 Agent 访问外部能力的开放协议。工具侧实现一个 server，
所有支持 MCP 的客户端就都能使用它——不用为每个客户端分别写插件。

原理和取舍见[这篇文章]({{ '/posts/2026/10/mcp-explained/' | relative_url }})。

## 常见类型

| 类型 | 能做什么 | 例子 |
| --- | --- | --- |
| 文件系统 | 读写指定目录 | 让 Agent 访问项目外部文档 |
| 数据库 | 查询、生成报表 | 只读连接业务库 |
| API 网关 | 调用内部服务 | 查询工单、发布任务 |
| 浏览器 | 抓取页面内容 | 需要登录态的页面 |

## 怎么接入

多数客户端通过一份 JSON 配置接入，通常包含三个要素：

```json
{
  "mcpServers": {
    "example": {
      "command": "npx",
      "args": ["-y", "@example/mcp-server"],
      "env": { "API_KEY": "..." }
    }
  }
}
```

具体的配置文件名和位置各客户端不同，以客户端文档为准。

## 注意事项

{: .pitfall }
工具数量增加会明显拖慢模型的选择速度，也更容易选错。我通常只保留当前任务真正需要的 server。

{: .warn }
server 是凭证的实际持有者。给数据库和 API 授权时按最小权限来，读和写尽量分开。

{: .pitfall }
工具返回的错误信息要写清楚。返回原始堆栈等于让 Agent 无从下手，结构化地说明「哪个参数不合法」它才能自己修正。
