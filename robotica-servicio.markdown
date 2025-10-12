---
layout: default
title: Service Robotics
permalink: /service-robotics/
---

<h1>Service Robotics Posts</h1>
<ul>
  {% for post in site.posts %}
    {% if post.categories contains "service-robotics" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> 
        <span>({{ post.date | date: "%d %b %Y" }})</span>
      </li>
    {% endif %}
  {% endfor %}
</ul>

