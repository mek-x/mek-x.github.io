---
layout: post
title:  "Wireless sensors network project"
date:   2015-05-20
tags:   wireless IoT
categories: wdan
---

Some time ago, I've done a project for my thesis. It was wireless weather data
acquisition network. I've made several sensor nodes with radios, set up some
server with database and created a site, which presented collected data.
It was nice, quite interesting idea, because I've managed to do a mesh
networking using [DSR](http://en.wikipedia.org/wiki/Dynamic_Source_Routing)
protocol and it was a lot of fun. Unfortunately implementation was pretty bad
and whole system was very unstable. It was enough for proving the concept and
doing needed research, yet it wasn't enough to be used as finished product and
since then, it's collecting dust. I've always wanted to do a refresh and
redesign it to be a proper system.


The idea
--------

I think, it would be interesting to rethink it, redesign it, write proper code
and share the knowledge in the process. Lately, I'm also interested in test
driven development techniques for embedded devices and after reading great book
(*Test Driven Development for Embedded C* by James W. Grenning, which I truly
recommend), that inspired me, I'm going to do the whole project using it.
The idea is to make everything basically from scratch and learn quirks of
[IoT](http://en.wikipedia.org/wiki/Internet_of_Things) world.

Requirements
------------

Some top-level requirements for this system:

* wireless sensors (nodes) powered from batteries,
* nodes shall communicate using multi-hop, mesh, networking,
  which shall be self-repairing and self-reconfiguring,
* there shall be a single master station (gateway to standard Ethernet network),
* adding new nodes to network should be as simple as possible,
* nodes should be power efficient,
* sensors should measure at least: temperature, wind speed, wind direction,
  humidity and barometric pressure,
* data shall be passed to server and stored in a database (possibly SQL-based),
* code and protocols used shall be portable and not limited to single
  architecture, platform or radio standard,
* whole system should not need too much manual involvement to operate,
* it would be nice to have some statistical, state and diagnostic data from
  nodes and network to do some research.

Those are the basic requirements I'd like for this system.
In the future, when I will have it running, I will expand it to present
collected data on some Internet site or/and connect it to global
[APRS](http://en.wikipedia.org/wiki/Automatic_Packet_Reporting_System) network,
since I'm licensed ham-radio operator and also it would be fun ;).
