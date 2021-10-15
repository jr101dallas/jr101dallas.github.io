---
layout: default
title:  "Posts"
---

<div class="sidebar" id="sidebar">
  <nav class="sidebar-nav">
   <h2 class="category-topic">Categories:</h2>
    {% assign sortedCategories = site.categories | sort %}
    {% for category in sortedCategories %}
     {% assign cat4url = category[0] | remove:' ' | downcase %}
     <a class="sidebar-nav-item" href="{{site.baseurl}}/{{cat4url}}">
        {{category[0]}}
     </a>
    {% endfor %}
  </nav>
</div>
<hr>
<div>
{% for post in site.posts %}
  {% assign currentdate = post.date | date: "%B %Y" %}
  {% if currentdate != date %}
    {% unless forloop.first %}</ul>{% endunless %}
    <h2 id="y{{post.date | date: "%B %Y"}}">{{ currentdate }}</h2>
    <ul>
    {% assign date = currentdate %}
  {% endif %}
    <li><a href="{{ post.url }}">{{ post.date | date: "%d" }} {{ post.title }}</a></li>
  {% if forloop.last %}</ul>{% endif %}
{% endfor %}
</div>
