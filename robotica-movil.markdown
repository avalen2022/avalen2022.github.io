---
layout: default
title: Mobile Robotics
permalink: /mobile-robotics/
---

<h1>Mobile Robotics Posts</h1>
<ul>
  {% for post in site.posts %}
    {% if post.categories contains "mobile-robotics" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> 
        <span>({{ post.date | date: "%d %b %Y" }})</span>
      </li>
    {% endif %}
  {% endfor %}
</ul>

