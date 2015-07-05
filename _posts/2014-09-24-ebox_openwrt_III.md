---
layout: post
title:  "OpenWRT on Ebox3300 (III): First run"
date:   2014-09-24
tags:   openwrt
categories: ebox
---

In this part we boot the image created using OpenWRT sources. We'll see what is
working out-of-the-box and what is not.

Preparing the boot media
------------------------

First we need to create the bootable microsd card. It can be created, as
I've described in my [previous post](/posts/2014/09/15/installing-syslinux/),
using Syslinux bootloader and ext2 filesystem.

After successful compilation, the binary image of kernel and openwrt ramdisk is
in `bin/x86` directory and it should be named like this:
`openwrt-x86-generic-ramfs.bzImage`. We have to copy it to the memory card
and configure bootloader to load this image. For syslinux, configuration is in
`extlinux.conf` file on the card (if there is no file - create it):

{% highlight text %}
DEFAULT linux
LABEL linux
    SAY Now booting the kernel...
    KERNEL openwrt-x86-generic-ramfs.bzImage
    APPEND console=ttyS0,115200n8
{% endhighlight %}

The important things is, that we want to redirect the console output to the
serial port. This would allow us to debug system without additional monitor and
keyboard attached to the device. We have to use only a null-modem cable
and some kind of USB-RS232 adapter (for modern computers without RS232 ports),
which are cheap and very useful, so I recommend getting one.

Configuring hardware
--------------------

Now we have to mangle some Setup options in Ebox3300's BIOS. Probably during
the first run we would be forced to use the monitor and keyboard in order to
gain access to Setup menu. We may redirect whole BIOS output to the serial port.
Look for this option (`P.O.S.T. Forward To`):

{% highlight text %}
*******************************************************
* South Bridge Chipset Configuration                  *
* *************************************************** *
* P.O.S.T. Forward To            [COM1]               *
*                                                     *
* * Serial/Parallel Port Configuration                *
* * WatchDog Configuration                            *
*                                                     *
*                                                     *
*******************************************************
{% endhighlight %}

This should make our work a way more comfortable.

Another thing is to make our microsd card the main boot device and this should
be straightforward enough.

Running the system
------------------

System should boot with following message (or quite similar):

{% highlight text %}
SYSLINUX 5.01 EDD 0x513c6c21 Copyright (C) 1994-2013 H. Peter Anvin et al
Now booting the kernel...
Loading vmlinuz... ok

[    0.000000] Linux version 3.10.49 (mek@hekate) (gcc version 4.8.3 (OpenWrt/Linaro GCC 4.8-2014.04 r42201) ) #2 Mon Sep 15 20:01:07 CEST 2014
[    0.000000] CPU: vendor_id 'Vortex86 SoC' unknown, using generic init.
[    0.000000] CPU: Your system may be unstable.
[    0.000000] e820: BIOS-provided physical RAM map:
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff] usable
[    0.000000] BIOS-e820: [mem 0x000000000009fc00-0x000000000009ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000000e4000-0x00000000000fffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000000100000-0x000000000fffffff] usable
[    0.000000] BIOS-e820: [mem 0x00000000ff000000-0x00000000ffffffff] reserved
[    0.000000] Notice: NX (Execute Disable) protection missing in CPU!
[    0.000000] DMI not present or invalid.
 ...
{% endhighlight %}

And after a moment, we should get those messages:

{% highlight text %}
...
[    2.141312] Freeing unused kernel memory: 2068k freed
procd: Console is alive
[    2.190026] pps_core: LinuxPPS API ver. 1 registered
[    2.195558] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
[    2.207402] PTP clock support registered
procd: - preinit -
Press the [f] key and hit [enter] to enter failsafe mode
Press the [1], [2], [3] or [4] key and hit [enter] to select the debug level
[    2.322413] usb 3-2: new full-speed USB device number 2 using ohci_hcd
procd: - early -
procd: - ubus -
procd: - init -
Please press Enter to activate this console.
...
{% endhighlight %}

After pressing ENTER we have our OpenWRT welcome screen:

{% highlight text %}
BusyBox v1.22.1 (2014-09-15 19:54:59 CEST) built-in shell (ash)
Enter 'help' for a list of built-in commands.

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 CHAOS CALMER (Bleeding Edge, r42201)
 -----------------------------------------------------
  * 1 1/2 oz Gin            Shake with a glassful
  * 1/4 oz Triple Sec       of broken ice and pour
  * 3/4 oz Lime Juice       unstrained into a goblet.
  * 1 1/2 oz Orange Juice
  * 1 tsp. Grenadine Syrup
 -----------------------------------------------------
root@OpenWrt:/#
{% endhighlight %}

Congratulations! We have a working OS.

The problems
------------

After a while of inspecting system internals I've run into several problems:

1. __No disks visible!__
   No `/dev/sda1`, `/dev/mmcblk0p1` or anything similar. It means we can't
   mount any storage.
2. __No network device!__
   Output of `ifconfig` leaves us only with loopback interface and this makes
   one rather lousy router:

{% highlight text %}
root@OpenWrt:/# ifconfig
lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:2208 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2208 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:150816 (147.2 KiB)  TX bytes:150816 (147.2 KiB)
root@OpenWrt:/#
{% endhighlight %}

So, what can we do? After searching the Internet I've found solution, but this
will be covered in the next part.
