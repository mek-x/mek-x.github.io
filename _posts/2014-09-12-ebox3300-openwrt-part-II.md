---
layout:     post
title:      "OpenWRT on Ebox3300 part II"
date:       2014-09-12
tags:       openwrt
categories: ebox
---

This part will contain getting the OpenWRT distribution and making basic
build.


The OpenWRT
-----------

OpenWRT is linux distibution, which can be used on many routers. It has
package system based on `opkg` (which is quite similar in usage as Debian's
`dpkg`, but intended for embedded systems) and `uci` configuration system.
The specifics can be found on the [OpenWRT homepage](https://openwrt.org/).
Especially, I recommend reading the Documentation section as it provides
good starting point.

I decided to use this distribution on my device, because it's quite mature
project, stable and there are lots of packages available. I could've
used some more popular, general purpose distribution (like Debian), but I
wanted more specialized OS (my device is meant to be router after all ;)),
which is hacker-friendly and can be easily modified and adapted to my needs.
Also the build system is great, based on Makefiles and kconfig (the same as
used in kernel build system).

Ok, so how do we get the OpenWRT? We can just download it from the Download
section of the homepage (for x86 architecture), but it won't work. The first
step is to get the sources from Development page:
[https://dev.openwrt.org/wiki/GetSource](https://dev.openwrt.org/wiki/GetSource)

There are instructions how to get the sources. I recommend getting them by `git`.
It should be quite straightforward, we only need to install this tool. Every
major linux distribution has it in its repositories. For example, in
Archlinux distro (which I use) command to install it, is:

```pacman -S git```

so nothing fancy.

Next we just use:

```git clone git://git.openwrt.org/openwrt.git```

and we have the newest Bleeding Edge sources of OpenWRT in newly created
`openwrt` directory. This project is under active development so there
are many changes introduced every day. We can update our sources with
`git pull` command.

First build
-----------

In this phase, our goal is simple: build the minimal working image for
the device. This will be ramfs image of root file system and kernel, and it
will be booted from µSD card. Why ramfs image? Because it's simple and we
won't have to deal with drivers for storage controllers and more advanced
filesystem. The first build is only for checking if device supports our
distibution in general and to have a nice starting point for porting work.

Ok, first thing we load default configuration:

```make defconfig```

and after that we run the calssic menuconfig:

```make menuconfig```

In the menu we choose `x86` as `Target System`  and `generic` as `Subtarget`.
In `Target Images` we mark `ramdisk` (we may change compression to gzip) and
uncheck everything else. We don't need any other type of image for time being.
That should be enough for minimal system. Exit the menu and save new
configuration. Now we just run `make` (with -j argument, if we want to
build it on multicore systems, for example `make -j 5` on 4-core machine)
and it should hopefully build after some time (depending on computer, but it
can take even an hour). If there are errors, we must check if we got
every dependency satisfied (they are described in documentation).

