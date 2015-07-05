---
layout: post
title:  "Wireless sensors network project - high-level design"
date:   2015-07-05
tags:   wireless networks MQTT DSR IoT
categories: wdan
---

Main idea is to create this project from scratch and use it to gain some
interesting knowledge. Also from this moment I'm starting to call this project
**WDAN** (Wireless Data Acquisition Network), because project should have some
name ;). Here are some thoughts about the stack.


Routing protocol
----------------

As a routing protocol I want to implement
[DSR](https://en.wikipedia.org/wiki/Dynamic_Source_Routing), because it's
rather easy and quite flexible. Thanks to the automatic management, it's perfect
for my needs and gives possibility of additional experimentation. It shouldn't
be too heavy, so it can be run on simple hardware. Also I've already know it,
since I've done project with simplified version. So this is my choice for
routing layer of the network stack.

High level protocols
----------------------

As a higher layer of the stack, I want to use
[MQTT](https://en.wikipedia.org/wiki/MQTT),
since it is also designed for use in telemetry systems. It uses
publish-subscribe model. It's data-centric, which is ideal for my use
and gives additional flexibility and resistance to failures, because I can have
several nodes collecting the same type of data and if one fails, system doesn't
need to be reconfigured to the other node address. Nodes will be responsible
to push their data to the gateway/server/broker, so no requests from server will
be needed.

In this case, I have to use MQTT-SN variant of the protocol, since it doesn't
have to be implemented on the top of TCP/IP stack.

Data will be coded using [JSON](https://en.wikipedia.org/wiki/JSON),
since it's well standarized and nice to encode and decode.

Low level protocols
-------------------

As a MAC layer I will use some variant of
[TDMA](https://en.wikipedia.org/wiki/Time_division_multiple_access) based
channel access method for my radio stack. It has to be low-energy aware and
prevent collisions. To use it well I also need some way to synchronize clocks
of the devices, because they have to be used to wake-up the node, when there is
window to communicate.

I also need some kind of error correction/detection to
reinforce the network. So as a simple way to do that, I can use CRC, but it
would be perfect to use more interesting coding, such as
[the Hamming Coding](http://michael.dipperstein.com/hamming/index.html). Thanks
to this I can correct single-bit errors. This will help to keep network more
reliable.

The stack
---------

So this is general look of the stack I wish to implement:

![proto_stack](/images/wdan/Proto_stack.png)

*Radio* are the drivers for radio modules I would use.

Overview of the whole network
-----------------------------

This is my depiction of the whole network stack:

![high_level_view](/images/wdan/high_level_view.png)

Nodes are connected using wireless communication, so not every node has direct
contact with another node. All of them are pushing data to MQTT-SN/MQTT gateway,
which is connected to standard TCP/IP network and main MQTT broker.
Other services are subscribing for data interesting from their point of view on
the main broker.
