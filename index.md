---
layout: default
title: Jerry's homepage
---

# IT case collection and study

{% for post in site.posts %}
- [{{ post.title }}]({{ post.url - relative_url }}) ({{ post.date - date: "%Y-%m-%d" }})
{% endfor %}

