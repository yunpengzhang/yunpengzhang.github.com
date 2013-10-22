---
layout: page
title: Yunpeng and his firends
tagline: Stay Hungry Stay Foolish
---
{% include JB/setup %}
<h2>最新文章</h2>
<hr />

{% for post in site.posts limit: 1  %}
<h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
<div class="list"><time>{{ post.date | date:"%Y年%m月%d日" }}</time></div>
<p>{{ post.content }} <a class="btn" href="{{ post.url }}">查看评论</a></p>
{% endfor %}

<hr />
<h2>以往文章</h2>
<br />

<ul>
  {% for post in site.posts limit: 5 offset: 1 %}
  <li class="list">
    <time>{{ post.date | date:"%Y-%m-%d" }}</time> <a href="{{ post.url }}">{{ post.title }}</a>
  </li>
  {% endfor %}
</ul>

<a class="btn btn-primary" href="/archive.html">更多文章</a>
