---
layout: default
title: home
---

# Hi, I'm Akash. 👋

I write about AI, software, travel and random deep thoughts nobody asked for.
Always running away from meetings and towards mountains.

## Connect

<!-- LinkedIn -->
<a href="https://www.linkedin.com/in/akashchatterjee/" target="_blank" style="text-decoration: none;">
    <img src="https://cdn.simpleicons.org/linkedin/0077b5" alt="LinkedIn" width="30" height="30" />
</a>

<!-- Twitter / X -->
<a href="https://x.com/akachatt" target="_blank" style="text-decoration: none;">
    <img src="https://cdn.simpleicons.org/x/000000" alt="X (Twitter)" width="30" height="30" />
</a>

## Latest Posts

<ul>
  {% for post in site.posts limit:5 %}
    <li>
      <span style="color: #828282; font-size: 0.9em;">{{ post.date | date: "%b %d, %Y" }}</span>
      <h3>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </h3>
      {% if post.excerpt %}
        <p>{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
      {% endif %}
    </li>
  {% endfor %}
</ul>

[View all posts →](./archive/)