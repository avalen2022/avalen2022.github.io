---
layout: default
title: Service Robotics
permalink: /service-robotics/
---

<h1>Posts de Robótica Móvil</h1>
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

