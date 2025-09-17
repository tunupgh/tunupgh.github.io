---
layout: page
title: Videos
permalink: /video/
---

<div class="video-list">
{% assign video_posts = site.posts | where_exp: "post", "post.categories contains 'video'" %}
{% for post in video_posts %}
  <article class="video-item">
    <h3><a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a></h3>
    <p class="video-meta">
      <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time>
    </p>
    {% if post.excerpt %}
      <div class="video-excerpt">{{ post.excerpt | strip_html | truncatewords: 50 }}</div>
    {% endif %}
  </article>
{% endfor %}
</div>