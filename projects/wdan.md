---
layout:    subpage
title:     Wireless Data Acquisition Network
---

Description
-----------

Wireless network of nodes for designed for weather and other data acquisition and
publishing it to wider audience. This is still work in progress.

Associated posts
----------------

{% for post in site.categories.wdan reversed %}
* {{ post.date | date_to_string }} &rArr; [ {{ post.title }} ]({{ post.url }}){% endfor %}

