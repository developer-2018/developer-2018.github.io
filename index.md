---
layout: layout
type: index navigation
title: Easy!developer
navtitle: Home
navpos: 0
---
<h3>TOP PAGE</h3>
<ul>
{% for post in site.posts %}
    <a href="{{ post.url }}">
    <h3>
    {{ post.date | date_to_long_string }} : {{ post.title }}
    </h3>
    </a>
    {{ post.content | strip_html | truncatewords: 10 }}
    <br><br>

{% endfor %}
</ul>