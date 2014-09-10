---
layout: post
title:  "OpenWRT on Ebox3300 part I"
date:   2014-09-08
categories: openwrt
---

Introduction
------------

Some time ago I received as a gift small, little, industrial computer.
It isn't very powerful but it's very power efficient. Especially for a x86 
architecture based system. It runs on 5V and needs less than 2 amps, so it's
about 10W. It's form factor is also appealing.

It was laying around for some time collecting dust, because I haven't got
any use for it (or so I thought ;)). Now the time has come to make
something productive out of it. Also I thought, I will share some of my 
experiences.



The machine
-----------

The machine can be found [here](http://www.compactpc.com.tw/en/product/EBOX-3300A-JSK/ebox-3300a-jsk.html).
I'm going to show some more interesting specs with my comments.
For the specifics look at the vendor page.

Here are the specs:

* VESA mounting support
	- so we can mount it on the TV!,
* Processor: Vortex86DX 1GHz 
	- vendor says it's 600MHz, but I suppose Linux knows better ;),
* Ram: 256MB DDR2,
	- quite enough for my needs,
* 3 USB 2.0 ports,
* Compact Flash slot
	- seen as hard drive - boot possible,
* µSD card slot
	- also seen as hard drive - boot possible,
* 10/100Mbps LAN,
* VGA port,
* twin RS232
	- for those, who fancy old school serial interfaces - including me ;),
	- one of them is debug port, we can even set BIOS setup menu to be accessible
	through it (we don't need a monitor and/or keyboard - hooray!),
	- [null modem](http://en.wikipedia.org/wiki/Null_modem) cable is a must! :)
* internal Mini PCI socket,
* C-Media Electronics based USB audio interface,
	- supported by Linux,
	- aux output and mic input jacks present,
* power supply: 5V/2A
	- power efficiency I love, it takes less power than my old router ;)

So, it's quite capable device in such a small box.


The plan
--------

Recently, my old Netgear router (802.11g and WPA only - sic!) started behaving
bad and generally I had enough of it. It worked for quite some years, but
the upgrade was needed (and I was bored ;)). Then I noticed this little computer in my
equipment box. The power efficiency was great and I wished to play with it
a little. So there came the plan: put a Linux in a box and turn it to some
badass router/server and hack with OpenWRT along the way ;).

My goals:

* replace my old router,
* OpenWRT based distro,
* 802.11n wlan connection,
* lots of capabilities to play with.

So, in the next part I will say a little about the first steps and problems.

