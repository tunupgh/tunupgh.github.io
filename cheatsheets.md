---
layout: page
title: Cheat Sheets
permalink: /cheatsheets/
---

<div class="cheatsheets-grid">
{% assign cheatsheet_posts = site.posts | where_exp: "post", "post.cheatsheetname" %}
{% for post in cheatsheet_posts %}
  <div class="cheatsheet-card">
    <h4><a href="{{ post.url | relative_url }}">{{ post.cheatsheetname }}</a></h4>
    {% if post.thumbnail %}
      <a href="{{ post.url | relative_url }}" style="text-decoration:none">
        <img src="{{ post.thumbnail }}" alt="{{ post.cheatsheetname }} cheat sheet" width="200px">
      </a>
    {% endif %}
  </div>
{% endfor %}
</div>

## Other Useful Reference Guides

<div class="reference-list">
{% assign reference_posts = site.posts | where_exp: "post", "post.categories contains 'reference'" %}
{% for post in reference_posts %}
  <article class="reference-item">
    <h3><a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a></h3>
    <p class="reference-meta">
      <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time>
    </p>
  </article>
{% endfor %}
</div>