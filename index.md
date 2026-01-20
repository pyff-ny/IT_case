---
layout: default
title: IT case
---

# Debug: categories

All categories: {{ site.categories | jsonify }}

# Debug: posts
{% for post in site.posts %}
- title={{ post.title }} | categories={{ post.categories }} | tags={{ post.tags }} | url={{ post.url }}
{% endfor %}
