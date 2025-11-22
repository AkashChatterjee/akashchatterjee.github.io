---
layout: default
title: travel
---

# travel stuff

<ul>
  {% for post in site.categories.travel %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>