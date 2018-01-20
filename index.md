---
layout: layout
type: index navigation
title: Easy!developer
navtitle: Home
navpos: 0
---
<ul>
{% for post in site.posts %}
    <a href="{{ post.url }}">
    <h1>
    {{ post.date | date_to_long_string }} : {{ post.title }}
    </h1>
    {{ post.content | strip_html | truncatewords: 10 }}
    </a>
    <br>

{% endfor %}
</ul>