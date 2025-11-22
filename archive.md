---
layout: default
title: archive
---
# everything
<ul>
  {% for post in site.posts %}
    <li><a href="{{ post.url }}">{{ post.title }}</a> ({{ post.date | date: "%Y" }})</li>
  {% endfor %}
</ul>
