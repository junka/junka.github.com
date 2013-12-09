---
layout: page
title: ZaHouse!
tagline: 
---
{% include JB/setup %}

## Posts List

I'm going to post my work here.

{% assign posts_collate = site.posts %}
{% include JB/posts_collate %}

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>




