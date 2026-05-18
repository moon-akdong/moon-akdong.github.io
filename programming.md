---
layout: page
title: Programming
---

{% assign posts_by_year = site.categories.programming | group_by_exp: "post", "post.date | date: '%Y'" %}
{% for year in posts_by_year %}

# {{ year.name }}

{% for post in year.items %}

- [{{ post.title }}]({{ post.url | absolute_url }}) <small style="color: #949494; font-size: 0.8em;">{{ post.date | date: "%m.%d" }}</small>
  {% endfor %}
  {% endfor %}
