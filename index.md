---
layout: default
title: IT_case
---

# IT_case

{% for post in site.posts %}
- [{{ post.title }}]({{ post.url | relative_url }}) ({{ post.date | date: "%Y-%m-%d" }})
{% endfor %}
