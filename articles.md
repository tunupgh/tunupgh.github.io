---
layout: page
title: Articles
permalink: /articles/
---

<div class="article-list">
{% for post in site.posts %}
  <article class="article-item">
    <h3><a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a></h3>
    <p class="article-meta">
      <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time>
      {% if post.categories.size > 0 %}
        • Categories: {{ post.categories | join: ", " }}
      {% endif %}
    </p>
    {% if post.excerpt %}
      <p class="article-excerpt">{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
    {% endif %}
  </article>
{% endfor %}
</div>