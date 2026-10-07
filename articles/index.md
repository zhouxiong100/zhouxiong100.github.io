---
layout: default
title: 文章
nav_order: 2
permalink: /articles/
description: 按分类浏览全部 AI Agent 文章。
---

# 文章

按分类归档，每个分类内按时间从新到旧排列。

{%- assign groups = site.posts | group_by_exp: "post", "post.categories | first" | sort: "name" %}
{%- for group in groups %}
{%- assign items = group.items | sort: "date" | reverse %}

## {{ group.name | default: "未分类" }}
{: .no_toc }

<ul class="post-list">
{%- for post in items %}
  <li class="post-list-item">
    <p class="post-meta">
      <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y-%m-%d" }}</time>
      {%- for tag in post.tags %}<span class="sep">·</span><span class="tag">{{ tag }}</span>{% endfor %}
    </p>
    <a class="post-list-title" href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <p class="post-list-excerpt">{{ post.lede | default: post.excerpt | strip_html | truncate: 96 }}</p>
  </li>
{%- endfor %}
</ul>
{%- endfor %}

---

订阅：[RSS]({{ '/feed.xml' | relative_url }})
