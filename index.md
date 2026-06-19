---
layout: default
title: "Akash Chatterjee | AI, Software & Life"
description: "Personal blog by Akash Chatterjee covering AI tools, software engineering, AWS architecture, startup insights, and philosophical thoughts."
last_modified_at: 2026-02-15
---

<div class="cover-photo">
  <img src="/assets/images/cover.jpg" alt="Cover photo">
</div>

<div class="profile-section">
  <img src="/assets/images/profile.jpg" alt="Akash Chatterjee" class="profile-photo">
</div>

<h1>Hi, I'm Akash 🍻</h1>

<div class="social-links">
  <a href="https://www.linkedin.com/in/akashchatterjee/" target="_blank" aria-label="LinkedIn">
    <svg viewBox="0 0 24 24" width="24" height="24">
      <path fill="currentColor" d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433c-1.144 0-2.063-.926-2.063-2.065 0-1.138.92-2.063 2.063-2.063 1.14 0 2.064.925 2.064 2.063 0 1.139-.925 2.065-2.064 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z"/>
    </svg>
  </a>
  <a href="https://x.com/akachatt" target="_blank" aria-label="X (Twitter)">
    <svg viewBox="0 0 24 24" width="24" height="24">
      <path fill="currentColor" d="M18.244 2.25h3.308l-7.227 8.26 8.502 11.24H16.17l-5.214-6.817L4.99 21.75H1.68l7.73-8.835L1.254 2.25H8.08l4.713 6.231zm-1.161 17.52h1.833L7.084 4.126H5.117z"/>
    </svg>
  </a>
</div>

I write about AI, software and random deep thoughts nobody asked for.

---

## Latest Posts

<ul class="post-list">
  {% for post in site.posts limit:5 %}
    <li>
      <span class="post-meta">{{ post.date | date: "%b %d, %Y" }}</span>
      <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title }}</a>
      {% if post.excerpt %}
        <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
      {% endif %}
    </li>
  {% endfor %}
</ul>

[View all posts →](/archive/)
