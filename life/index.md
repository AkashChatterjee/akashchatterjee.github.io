---
layout: default
title: "Life | Akash Chatterjee"
description: "Life & everything else."
permalink: /life/
redirect_from:
  - /opinions/
last_modified_at: 2026-06-19
---

# Life

<ul class="post-list">
  {% for post in site.categories.life %}
    <li>
      <span class="post-meta">{{ post.date | date: "%b %d, %Y" }}</span>
      <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title }}</a>
      {% if post.excerpt %}
        <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
      {% endif %}
    </li>
  {% endfor %}
</ul>
