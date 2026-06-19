---
layout: default
title: "Site Map | Akash Chatterjee"
description: "Complete listing of all pages and posts on akashchatterjee.com, covering tech articles, life and more."
permalink: /sitemap/
last_modified_at: 2026-02-15
---

# Site Map

## Main Pages

- [Home](/)
- [All Posts Archive](/archive/)
- [Tech](/tech/)
- [Life](/life/)

## Tech Posts

<ul>
  {% for post in site.categories.tech %}
    <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — <span>{{ post.date | date: "%b %d, %Y" }}</span></li>
  {% endfor %}
</ul>

## Life Posts

<ul>
  {% for post in site.categories.life %}
    <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — <span>{{ post.date | date: "%b %d, %Y" }}</span></li>
  {% endfor %}
</ul>

