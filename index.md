---
layout: page
title: 首页
tagline: Supporting tagline
---
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date: "%Y-%m-%d" }}</span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
