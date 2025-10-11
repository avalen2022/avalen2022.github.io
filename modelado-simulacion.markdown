---
layout: default
title: Modelating Y Simulation Robots
permalink: /modelating-simulation/
---

<h1>Modelating And Simulation Robots Posts</h1>
<ul>
  {% for post in site.posts %}
    {% if post.categories contains "modelating-simulation" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> 
        <span>({{ post.date | date: "%d %b %Y" }})</span>
      </li>
    {% endif %}
  {% endfor %}
</ul>

