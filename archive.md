---
layout:    page
title:     /archive
permalink: /archive/
---

### Look at my older posts:

{% for post in site.posts %}
* {{ post.date | date_to_string }} &rArr; [ {{ post.title }} ]({{ post.url }}){% endfor %}

