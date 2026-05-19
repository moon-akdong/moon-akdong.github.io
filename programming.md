---
layout: page
title: Programming
---

{% assign posts_by_year = site.categories.programming | group_by_exp: "post", "post.date | date: '%Y'" %}

{% for year in posts_by_year %}

# {{ year.name }}

{% assign year_tags = "" %}
{% for post in year.items %}
{% for tag in post.tags %}
{% unless year_tags contains tag %}
{% if year_tags == "" %}
{% assign year_tags = tag %}
{% else %}
{% assign year_tags = year_tags | append: "," | append: tag %}
{% endif %}
{% endunless %}
{% endfor %}
{% endfor %}
{% assign year_tags_array = year_tags | split: "," | sort_natural %}

{% for tag in year_tags_array %}

## {{ tag }}

{% assign sorted = year.items | sort: "title" %}
{% for post in sorted %}
{% if post.tags contains tag %}

- [{{ post.title }}]({{ post.url | absolute_url }}) <small style="color: #949494; font-size: 0.8em;">{{ post.date | date: "%m.%d" }}</small>
  {% endif %}
  {% endfor %}
  {% endfor %}
  {% endfor %}
