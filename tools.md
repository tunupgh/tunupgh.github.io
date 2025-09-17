---
layout: page
title: Tools
permalink: /tools/
---

<div class="tools-grid">
  <a href="/hash-analyzer/" class="tool-card-link">
    <div class="tool-card">
      <h3>Hash Analyzer</h3>
      <div class="tool-icon">🔐</div>
      <p>Tool to identify hash types. Enter a hash to be identified and determine what type of hash it is.</p>
    </div>
  </a>

  <a href="/subnet-calculator/" class="tool-card-link">
    <div class="tool-card">
      <h3>Subnet Calculator</h3>
      <div class="tool-icon">🔢</div>
      <p>The most user-friendly IPv4/IPv6 subnet calculator. Calculate network addresses, broadcast, and host ranges.</p>
    </div>
  </a>

  <a href="/config-cleanup/" class="tool-card-link">
    <div class="tool-card">
      <h3>Config Cleanup</h3>
      <div class="tool-icon">🧹</div>
      <p>Cisco ASA configuration cleanup tool. Analyze configs to find unused ACLs and objects that can be removed.</p>
    </div>
  </a>

  <a href="/show-connections-analyzer/" class="tool-card-link">
    <div class="tool-card">
      <h3>Show Connections Analyzer</h3>
      <div class="tool-icon">🔍</div>
      <p>Analyze Cisco ASA show connections output to identify top talkers and connection patterns.</p>
    </div>
  </a>

  <a href="/checkpoint-packet-capture-creator/" class="tool-card-link">
    <div class="tool-card">
      <h3>Checkpoint Packet Capture Creator</h3>
      <div class="tool-icon">📊</div>
      <p>Generate Checkpoint firewall packet capture commands for network troubleshooting.</p>
    </div>
  </a>

  <a href="/nat-converter/" class="tool-card-link">
    <div class="tool-card">
      <h3>NAT Conversion Tool</h3>
      <div class="tool-icon">🔄</div>
      <p>Convert Cisco ASA 8.2 and under NAT statements to the new ASA 8.3+ format.</p>
    </div>
  </a>

  <a href="/static-nat-creator/" class="tool-card-link">
    <div class="tool-card">
      <h3>Static NAT Creator</h3>
      <div class="tool-icon">📝</div>
      <p>Create static 1-to-1 NAT configuration for Cisco ASA 8.4.2+ code.</p>
    </div>
  </a>
</div>

<div class="tools-articles">
  <h2>Tool-Related Articles</h2>
  <div class="articles-list">
    {% assign tool_posts = site.posts | where_exp: "post", "post.categories contains 'tools'" %}
    {% for post in tool_posts %}
      <article class="tool-article">
        <h4><a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a></h4>
        <p class="article-meta">
          <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time>
        </p>
        {% if post.excerpt %}
          <div class="article-excerpt">{{ post.excerpt | strip_html | truncatewords: 25 }}</div>
        {% endif %}
      </article>
    {% endfor %}
  </div>
</div>