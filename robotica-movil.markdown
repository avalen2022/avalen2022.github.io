---
layout: default
title: Robótica Móvil
permalink: /robotica-movil/
---

<h1>Posts de Robótica Móvil</h1>
<ul>
  {% for post in site.posts %}
    {% if post.categories contains "robotica-movil" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> 
        <span>({{ post.date | date: "%d %b %Y" }})</span>
      </li>
    {% endif %}
  {% endfor %}
</ul>

