---
layout: default
title: 首页
nav_order: 1
description: AI Agent 实践经验，以及 skill、MCP 和工具分享。
---

<div class="hero">
  <p class="hero-eyebrow">实践经验 · skill&amp;MCP&amp;工具</p>
  <h1 class="hero-title">把 AI Agent 用进真实的工作流</h1>
  <p class="hero-sub">
    这里记录我在日常研发中使用 AI Agent 的经验，以及 skill、MCP
    和工具的真实使用边界。
  </p>
  <div class="hero-actions">
    <a class="btn btn-primary" href="{{ '/articles/' | relative_url }}">浏览全部文章</a>
    <a class="btn" href="{{ '/tools/' | relative_url }}">看看工具</a>
  </div>
</div>

## 最新文章
{: .no_toc }

{%- assign latest = site.posts | sort: "date" | reverse %}
<div class="post-grid">
{%- for post in latest %}
  {%- assign cat = post.categories | first %}
  <a class="post-card" href="{{ post.url | relative_url }}">
    <p class="post-meta">
      <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y-%m-%d" }}</time>
      {%- if cat %}<span class="sep">·</span><span class="tag">{{ cat }}</span>{% endif %}
    </p>
    <span class="post-card-title">{{ post.title }}</span>
    <span class="post-card-excerpt">{{ post.lede | default: post.excerpt | strip_html | truncate: 88 }}</span>
  </a>
{%- endfor %}
</div>

[查看全部文章 →]({{ '/articles/' | relative_url }})

## skill&MCP&工具
{: .no_toc }

挑选标准只有一条：我自己会在真实项目里持续用它。

{%- assign cards = site.tools | where: "card", true | sort: "nav_order" %}
<div class="card-grid">
{%- for tool in cards %}
  <a class="card" href="{{ tool.url | relative_url }}">
    <span class="card-title">{{ tool.title }}</span>
    <span class="card-desc">{{ tool.tagline }}</span>
  </a>
{%- endfor %}
</div>
