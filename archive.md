---
layout: default
title: archive
permalink: /archive/
---
# everything
<ul class="post-list">
  {% for post in site.posts %}
    <li>
      <span class="post-meta">{{ post.date | date: "%b %d, %Y" }}</span>
      <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title }}</a>
      {% if post.excerpt %}
        <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
      {% endif %}
    </li>
  {% endfor %}
</ul>
