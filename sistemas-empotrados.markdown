---
layout: default
title: Sistemas Empotrados
permalink: /sistemas-empotrados/
---

<h1>Posts de Sistemas Empotrados</h1>
<ul>
  {% for post in site.posts %}
    {% if post.categories contains "sistemas-empotrados" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> 
        <span>({{ post.date | date: "%d %b %Y" }})</span>
      </li>
    {% endif %}
  {% endfor %}
</ul>

