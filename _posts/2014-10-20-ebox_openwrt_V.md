---
layout: post
title:  "OpenWRT on Ebox3300 (V): Networking"
date:   2014-10-20
tags:   openwrt
categories: ebox
---

In this part, I'm going to describe my problems and solutions to the networking
subsystem of the Ebox3300. The goal is to have ethernet controller connected
to the WAN and some kind of WLAN module to be an access point for my computers.
Device I have, has ethernet controller built in, but no WLAN functionality at
all. It has USB 2.0 ports, so it's possible to attach pendrive-style WLAN card.
It also has mini-PCI connector inside, so there are some options to choose from.

The ethernet
------------

As seen in previous parts, the ethernet controller works almost out-of-the-box,
but I noticed a problem (FYI, I'm using OpenWRT's 3.10.49 kernel
version). Sometimes it fails to enumerate after boot. In `dmesg` it throws
something like this:

{% highlight text %}
r6040 0000:00:08.0: failed to register MII bus
r6040: probe of 0000:00:08.0 failed with error -5
{% endhighlight %}

After that, there is no available network interface. Temporary solution was to
reload the `r6040` module (several times in worst case) and it wasn't
acceptable in any way. Then I turned to Google and found a way out.
[Here](https://bugzilla.kernel.org/show_bug.cgi?id=64081) I've found, that not
only me have the same problem and also there is a solution of how to deal with
it. The only thing I've changed is (in Linux kernel of course ;)):

{% highlight diff %}
--- a/drivers/net/ethernet/rdc/r6040.c
+++ b/drivers/net/ethernet/rdc/r6040.c
@@ -144,7 +144,7 @@
 #define MBCR_DEFAULT   0x012A  /* MAC Bus Control Register */
 #define MCAST_MAX      3       /* Max number multicast addresses to filter */

-#define MAC_DEF_TIMEOUT        2048    /* Default MAC read/write operation timeout */
+#define MAC_DEF_TIMEOUT        4096    /* Default MAC read/write operation timeout */

 /* Descriptor status */
 #define DSC_OWNER_MAC  0x8000  /* MAC is the owner of this descriptor */
{% endhighlight %}

After the change, I compiled the module and included it in new image. It's
working flawlessly until now. So I've added another patch (as described in
previous part).

The wireless lan
----------------

First thought that came to me was to use some kind of USB to WiFi adapter.
They are cheap and there are plenty of them. Thankfully, I had lying around
Tp-Link TL-WN722N (based on Atheros chipset), which fulfill my requirements.
I started to prepare needed kernel modules (`kmod-ath9k-htc`). After getting
this module in the image I've inserted it into kernel and plugged in the WiFi
adapter. Unfortunately, it didn't work. Here is the output from `dmesg`:

{% highlight text %}
[190728.490000] usb 1-1: new high-speed USB device number 3 using ehci-pci
[190748.209069] usb 1-1: ath9k_htc: Firmware htc_9271.fw requested
[190748.217621] usbcore: registered new interface driver ath9k_htc
[190748.502075] usb 1-1: ath9k_htc: Transferred FW: htc_9271.fw, size: 51272
[190749.730359] usb 1-1: Service connection timeout for: 256
[190749.736399] ath9k_htc 1-1:1.0: ath9k_htc: Unable to initialize HTC services
[190749.744312] ath9k_htc: Failed to initialize the device
[190750.392761] usb 1-1: ath9k_htc: USB layer deinitialized
{% endhighlight %}

It looked as device had some problem with communication. The same errors were
shown when I've used different USB ports. After searching on the Internet,
I noticed that this adapter has quite big power requirements (for example -
there were many complaints with using it on Raspberry Pi) and I thought, that
this may be the cause. I got my hands on powered USB hub and indeed it worked
perfectly. So, this was the solution, but it was rather ugly. From one, quite
compact, device I had two, additional cables and another power adapter.

It wasn't acceptable solution, so I tried to find something different.
It was obvious - use miniPCI port on-board. Wifi cards for the miniPCI port
are nowadays quite scarce. Especially, I wished to have a 802.11n type card.
After searching different on-line shops in my country, I've found only several
of them, mostly dedicated for MikroTik router boards and for equivalent of 25$
I bought one called **TechnicLAN TMP-9220**. It is based on great Atheros AR9220
chipset and the Linux support is splendid. As a bonus it works on the 5GHz
bandwidth as well. So after plugging that into the miniPCI, connecting _pigtail_
type antenna connector and antenna, I compiled suitable module (`kmod-ath9k`)
and got it working on the device. I haven't noticed any show-stopping problems
with it up to now, so I'm happy.

The configuration of wireless facilities on the OpenWrt distribution is
described on [wiki](http://wiki.openwrt.org/doc/uci/wireless) very well, so
I won't dive into the details here.

Unfortunately, performance is not great (but enough for my needs for now).
I tested it using the `iperf` tool with default settings. Here is the output:

{% highlight text %}
------------------------------------------------------------
Client connecting to 192.168.10.1, TCP port 5001
TCP window size: 85.0 KByte (default)
------------------------------------------------------------
[  4] local 192.168.10.18 port 46962 connected with 192.168.10.1 port 5001
[ ID] Interval       Transfer     Bandwidth
[  4]  0.0- 1.0 sec  2.62 MBytes  22.0 Mbits/sec
[  4]  1.0- 2.0 sec  2.50 MBytes  21.0 Mbits/sec
[  4]  2.0- 3.0 sec  2.50 MBytes  21.0 Mbits/sec
[  4]  3.0- 4.0 sec  2.38 MBytes  19.9 Mbits/sec
[  4]  4.0- 5.0 sec  2.00 MBytes  16.8 Mbits/sec
[  4]  5.0- 6.0 sec  2.38 MBytes  19.9 Mbits/sec
[  4]  6.0- 7.0 sec  2.25 MBytes  18.9 Mbits/sec
[  4]  7.0- 8.0 sec  2.38 MBytes  19.9 Mbits/sec
[  4]  8.0- 9.0 sec  2.25 MBytes  18.9 Mbits/sec
[  4]  9.0-10.0 sec  2.12 MBytes  17.8 Mbits/sec
[  4]  0.0-10.1 sec  23.5 MBytes  19.5 Mbits/sec
{% endhighlight %}

As one can see, average speed is about 20Mbits/s. Adapter is configured as
802.11n, HT20 (150Mbit/s max). Cipher is WPA2/PSK (CCMP/AES). This is quite
disappointing, even more, as I get the same results when I use HT40
configuration (max 300Mbit/s theoretically) and also the 802.11g
(so max 54Mbits/s). Maybe miniPCI port on this device is a bottleneck?
I haven't found a solution yet, but for now it is enough for my everyday use.
I've also tested USB adapter and then I've only got 4-5Mbits/s on average,
so much worse. I'm going to look into this problem in the future.
