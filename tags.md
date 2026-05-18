---
layout: page
title: Tags
---

{% for tag in site.tags %}

## {{ tag[0] }}

{% for post in tag[1] %}

- [{{ post.title }}]({{ post.url | absolute_url }}) <small style="color: #949494; font-size: 0.8em;">{{ post.date | date: "%Y-%m-%d" }}</small>
  {% endfor %}
  {% endfor %}
