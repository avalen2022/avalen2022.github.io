---
layout: default
title: Service Robotic
permalink: /Service-Robotic/
---

<h1>Posts de Robótica Móvil</h1>
<ul>
  {% for post in site.posts %}
    {% if post.categories contains "service-robotic" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> 
        <span>({{ post.date | date: "%d %b %Y" }})</span>
      </li>
    {% endif %}
  {% endfor %}
</ul>

