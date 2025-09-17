---
layout: page
title: Tools and Information for Network Engineers
description: Tools, calculator, and helpful information for network engineering, web developers, and IT professionals.
---

<div class="home-sections">
  <a href="/articles/" class="section-box-link">
    <div class="section-box">
      <h3>Articles</h3>
      <div class="section-icon articles-icon">📄</div>
      <p>Explore the numerous articles written about: Cisco Firewalls, VPNs, Juniper Firewalls, Electronic devices and much more tech talk.</p>
    </div>
  </a>

  <a href="/tools/" class="section-box-link">
    <div class="section-box">
      <h3>Tools</h3>
      <div class="section-icon tools-icon">🔧</div>
      <p>Explore the tools made exclusively for TunnelsUp. Here you'll find neat tools to help you with your firewall configurations.</p>
    </div>
  </a>

  <a href="/cheatsheets/" class="section-box-link">
    <div class="section-box">
      <h3>Cheat Sheets</h3>
      <div class="section-icon cheatsheets-icon">📋</div>
      <p>View Cheatsheets made by TunnelsUP. Cheatsheets are documents that contain a lot of information on one page.</p>
    </div>
  </a>

  <a href="/videos/" class="section-box-link">
    <div class="section-box">
      <h3>Videos</h3>
      <div class="section-icon videos-icon">🎥</div>
      <p>Explore the various videos that have been made by TunnelsUP. Here you can learn about VPNs and electronic projects.</p>
    </div>
  </a>
</div>

<div class="home-content">
  <div class="recent-articles">
    <h2>Recent Articles</h2>
    <ul>
      {% for post in site.posts limit: 10 %}
        <li class="post">
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </li>
      {% endfor %}
    </ul>
    <h4><a href="/articles/">All Articles &gt;&gt;</a></h4>
  </div>

  <div class="featured-articles">
    <h2>Featured Articles</h2>
    <ul>
      {% assign featured_posts = site.posts | where_exp: "post", "post.categories contains 'featured'" %}
      {% for post in featured_posts limit: 10 %}
        <li class="post">
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </li>
      {% endfor %}
    </ul>
  </div>
</div>
