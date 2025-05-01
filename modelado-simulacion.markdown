---
layout: default
title: Modelado Y Simulación de Robots
permalink: /modelado-simulacion/
---

<h1>Posts de Modelado Y Simulación de Robots</h1>
<ul>
  {% for post in site.posts %}
    {% if post.categories contains "modelado-simulacion" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> 
        <span>({{ post.date | date: "%d %b %Y" }})</span>
      </li>
    {% endif %}
  {% endfor %}
</ul>

