---
layout: default
title: "Travel Stories | Akash Chatterjee"
description: "Travel experiences, stories and reflections from Akash Chatterjee's adventures around the world. From mountain trails to city escapes."
permalink: /travel/
last_modified_at: 2026-02-15
---

# Travel

<ul class="post-list">
  {% for post in site.categories.travel %}
    <li>
      <span class="post-meta">{{ post.date | date: "%b %d, %Y" }}</span>
      <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title }}</a>
      {% if post.excerpt %}
        <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
      {% endif %}
    </li>
  {% endfor %}
</ul>
