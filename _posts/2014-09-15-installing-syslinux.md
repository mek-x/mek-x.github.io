---
categories:  bootloader
layout:      post
title:       "Something about Syslinux..."
date:        2014-09-15
---

Syslinux is quite interesting bootloader for x86 based machines. It's not as
fancy as Grub (especially 2.0 version and up), but it's very simple to install
and supports booting kernel from, among others, fat or ext2/3/4 partition.
It's perfect for my project of making Ebox3300 based router. In this post
I'll describe the basic installation and configuration.

Preparing target
----------------

Our target will be standard µSD card. We'll use one partition formatted
as ext2. In my system the card is seen as `/dev/mmcblk0`.
First we need to create partition on it using our favourite tool.
I use `fdisk` (root permissions are probably needed to successfully run this
command). This is sequence of commands to create one partition on the device,
using whole available space and making it bootable:

{% highlight bash %}
fdisk /dev/mmcblk0
o
n
p
[ENTER]
[ENTER]
[ENTER]
a
w
{% endhighlight %}

After successfully creating new partition we must format it with, for example,
`mke2fs` command, which is often part of package named `e2fstools`,
`e2fsprogs` or something similar --- it depends on our linux distribution.
After creating partition, there should be `/dev/mmcblk0p1` device. That's
our partition.

```
mke2fs /dev/mmcblk0p1
```

After successfull execution of this command we now have prepared target device
for our bootloader.

Installing Syslinux
-------------------

[[Project homepage](http://www.syslinux.org/)]

Sometimes there is precompiled package in our distro repositories, but it's
more fun to compile Syslinux ourselves. First we need sources from
[here](https://www.kernel.org/pub/linux/utils/boot/syslinux/). Newest version
is fine (I've used 6.02). For building it we need `perl`, `nasm` assembler,
and of course `gcc` with other standard build tools (like `make` etc.).
We build it by running:

`
make bios
`

If we just run `make` it will build also the EFI version which is not
needed. After, hopefully, successfull build our tool is
`bios/extlinux/extlinux` and there is also
[master boot record](http://en.wikipedia.org/wiki/Master`boot`record)
image: `bios/mbr/mbr.bin` which we'll need. The installation is described
in details [here](http://www.syslinux.org/wiki/index.php/EXTLINUX).
In my system, I do it that way (as root):

{% highlight bash %}
mount /dev/mmcblk0p1 /mnt/media
./bios/extlinux/extlinux --install /mnt/media
cat bios/mbr/mbr.bin > /dev/mmcblk0
{% endhighlight %}

Now we must create syslinux configuration file `extlinux.conf` on the target
partition where we installed syslinux. It's contents for me in Ebox3300
project are:

{% highlight text %}
DEFAULT linux
LABEL linux
    SAY Now booting the kernel...
    KERNEL vmlinuz
    APPEND console=ttyS0,115200n8
{% endhighlight %}

For details look at the Syslinux homepage.

After copying vmlinuz image with kernel and initramfs to the target
partition we will have bootable µSD card for our device.

Summary
-------

This is quick, dirty and basic how-to concerning installing bootloader
manually for our project. We may also use Grub, which is also great
bootloader and can be used, of course.

