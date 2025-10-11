---
layout: default
title: Mobile Robotic
permalink: /mobile-robotic/
---

<h1>Mobile Robotics Posts</h1>
<ul>
  {% for post in site.posts %}
    {% if post.categories contains "mobile-robotic" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> 
        <span>({{ post.date | date: "%d %b %Y" }})</span>
      </li>
    {% endif %}
  {% endfor %}
</ul>

