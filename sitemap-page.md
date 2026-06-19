---
layout: default
title: "Site Map | Akash Chatterjee"
description: "Complete listing of all pages and posts on akashchatterjee.com, covering tech articles, opinions and more."
permalink: /sitemap/
last_modified_at: 2026-02-15
---

# Site Map

## Main Pages

- [Home](/)
- [All Posts Archive](/archive/)
- [Tech](/tech/)
- [Opinions](/opinions/)

## Tech Posts

<ul>
  {% for post in site.categories.tech %}
    <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — <span>{{ post.date | date: "%b %d, %Y" }}</span></li>
  {% endfor %}
</ul>

## Opinion Posts

<ul>
  {% for post in site.categories.opinions %}
    <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — <span>{{ post.date | date: "%b %d, %Y" }}</span></li>
  {% endfor %}
</ul>

