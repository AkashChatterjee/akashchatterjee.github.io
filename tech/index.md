---
layout: default
title: tech
---

# tech stuff

<ul class="post-list">
  {% for post in site.categories.tech %}
    <li>
      <span class="post-meta">{{ post.date | date: "%b %d, %Y" }}</span>
      <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>