---
layout: default
title: Jerry's homepage
---

# IT case collection and study

## Network
{% for post in site.categories.network %}
- [{{ post.title }}]({{ post.url | relative_url }}) ({{ post.date | date: "%Y-%m-%d" }})
{% endfor %}

## Hardware
{% for post in site.categories.hardware %}
- [{{ post.title }}]({{ post.url | relative_url }}) ({{ post.date | date: "%Y-%m-%d" }})
{% endfor %}
