---
layout: default
title: "Thoughts & Opinions | Akash Chatterjee"
description: "Essays and opinions on AI privacy, startup culture, philosophy, productivity, cognitive development, and the future of technology by Akash Chatterjee."
permalink: /opinions/
last_modified_at: 2026-02-15
---

# Thoughts & Opinions

<ul class="post-list">
  {% for post in site.categories.opinions %}
    <li>
      <span class="post-meta">{{ post.date | date: "%b %d, %Y" }}</span>
      <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title }}</a>
      {% if post.excerpt %}
        <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
      {% endif %}
    </li>
  {% endfor %}
</ul>
