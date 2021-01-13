---
layout: page
title: OOP
---

<div class="posts">
  {% for post in site.categories.oop %}
  <div class="post">
    <h3 class="post-title">
      <a href="{{ site.url }}{{ post.url }}">
        {{ post.title }}
      </a>
    </h3>

    <span class="post-date">{{ post.date | date_to_string }}</span>
  </div>
  {% endfor %}
</div>