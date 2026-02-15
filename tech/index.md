---
layout: default
title: "Software & Tech Articles | Akash Chatterjee"
description: "Technical posts on AWS Lambda, Elasticsearch, Spring Boot, CI/CD pipelines, and software engineering best practices by Akash Chatterjee."
permalink: /tech/
last_modified_at: 2026-02-15
---

# Tech

<ul class="post-list">
  {% for post in site.categories.tech %}
    <li>
      <span class="post-meta">{{ post.date | date: "%b %d, %Y" }}</span>
      <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title }}</a>
      {% if post.excerpt %}
        <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
      {% endif %}
    </li>
  {% endfor %}
</ul>
