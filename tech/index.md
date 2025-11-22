---
layout: default
title: tech
---

# tech stuff

<ul>
  {% for post in site.categories.tech %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>