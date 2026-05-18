---
layout: page
title: Post
---

{% for post in site.posts %}
- [{{ post.title }}]({{ post.url | absolute_url }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}
