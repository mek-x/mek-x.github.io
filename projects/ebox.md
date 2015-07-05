---
layout:    subpage
title:     EBOX3300 router
---

Description
-----------

This is an EBOX3300 based WiFi router, powered by OpenWRT system. It was a little experiment and
also possibility of using old hardware for something useful.

![EBOX](/images/ebox/ebox3300.jpg)

Associated posts
----------------

{% for post in site.categories.ebox reversed %}
* {{ post.date | date_to_string }} &rArr; [ {{ post.title }} ]({{ post.url }}){% endfor %}

