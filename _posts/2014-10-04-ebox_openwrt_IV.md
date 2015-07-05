---
layout: post
title:  "OpenWRT on Ebox3300 (IV): Adding patches"
date:   2014-10-04
tags:   openwrt
categories: ebox
---

In this part, we will deal with enabling not functioning parts of our
device. We've already noticed, that there are no disks seen and ethernet
controller is also not visible. The second is quite easily resolved by
selecting appropriate options in the OpenWRT menuconfig. However the first
thing is going to be more interesting, as we will be creating patch to the
OpenWRT build tree itself. The solution to these hardware problems I've found
on [this blog](http://hq.pingdynasty.com/blog/2010/10/05/installing-debian-on-ebox-3300/).


The ethernet controller
-----------------------

First, we deal with ethernet controller. The controller on board is based on
the `RDC R6040` device. So we will need r6040 kernel module. It's location
in OpenWRT's menuconfig is `Kernel modules > Network Devices > kmod-r6040`.
We simply select it to build and that's all. When we rebuild our image we will
hopefully get our working ethernet device in `ifconfig` and in `dmesg` we will
get something like this:

{% highlight text %}
[    7.082793] r6040: RDC R6040 NAPI net driver,version 0.28 (07Oct2011)
[    7.096384] libphy: r6040_eth_mii: probed
[    7.101049] r6040 0000:00:08.0: attached PHY driver [Generic PHY] (mii_bus:phy_addr=0000:00:08.0-0:01)
{% endhighlight %}

There is a slight problem with this device (it sometimes fails to enumerate -
at least in my case), but I will get back to it later on.


The IDE controller
------------------

As far as disks are concerned, it's going to be more complicated. According
to blog, which I provided link above, the needed driver is in kernel. However,
driver is not assigned to the specific hardware ID our device uses. So we have
to add this ID to the driver headers in order to correctly identify it.

The correct way to add a patch to OpenWRT build tree is using the tool named
`quilt` (which is great tool to work with patches - used by Linux kernel
maintainers). The procedure is described on
[official OpenWRT wiki](http://wiki.openwrt.org/doc/devel/patches).
We are going to use the procedure described in **Adding or editing kernel
patches** section.

### Adding the kernel patch

Procedure looks like this (of course we need `quilt`, so install it ;)):

* First we need to prepare our kernel tree in order to add changes:

{% highlight sh %}
make target/linux/{clean,prepare} V=s QUILT=1
{% endhighlight %}

* Then we go to build directory and prepare our patch:
    - The `build_dir` is for version of OpenWRT I use. On different version,
      however, it should be similar.
    - Patch name (for kernel patches) must be constructed that way:
      `{platform,generic}/[number]-[name_of_patch].patch`.
      Our patch is of `platform` type.

{% highlight sh %}
cd build_dir/target-i386_i486_uClibc-0.9.33.2/linux-x86_generic/linux-3.10.49/
quilt push -a
quilt new platform/170-rdc_d1011_ids.patch
{% endhighlight %}


* Next we edit the files:

    > Note:
    > `quilt edit` uses editor, which can be set in `~/.quiltrc` as `EDITOR=`
    > variable.

{% highlight sh %}
quilt edit drivers/ata/pata_it821x.c
quilt edit include/linux/pci_ids.h
{% endhighlight %}

* We make our changes visible in patch by using `quilt refresh` command.
* We may see what changes are being applied to the patch using `quilt diff`:

{% highlight diff %}
# quilt diff

--- a/drivers/ata/pata_it821x.c
+++ b/drivers/ata/pata_it821x.c
@@ -957,6 +957,7 @@ static const struct pci_device_id it821x
        { PCI_VDEVICE(ITE, PCI_DEVICE_ID_ITE_8211), },
        { PCI_VDEVICE(ITE, PCI_DEVICE_ID_ITE_8212), },
        { PCI_VDEVICE(RDC, PCI_DEVICE_ID_RDC_D1010), },
+       { PCI_VDEVICE(RDC, PCI_DEVICE_ID_RDC_D1011), },

        { },
 };
--- a/include/linux/pci_ids.h
+++ b/include/linux/pci_ids.h
@@ -2328,6 +2328,7 @@
 #define PCI_DEVICE_ID_RDC_R6060                0x6060
 #define PCI_DEVICE_ID_RDC_R6061                0x6061
 #define PCI_DEVICE_ID_RDC_D1010                0x1010
+#define PCI_DEVICE_ID_RDC_D1011                0x1011

 #define PCI_VENDOR_ID_LENOVO           0x17aa
{% endhighlight %}

As we can see, only those two lines are needed in order to enable our device
in the driver.

* To add our patch to the main OpenWRT buildroot we need to run this command
  from root directory:

{% highlight sh %}
make target/linux/update package/index V=s
{% endhighlight %}

* After that we can run again `make target/linux/{clean,prepare} V=s QUILT=1`
  and see if our changes are added. We may also check, if the patch is in the
  patch dir, in this case: `target/linux/x86/patches-3.10`.

### Adding the device to OpenWRT menuconfig

After the necessary modification we have to enable correct kernel module in
OpenWRT menuconfig, but there is no appropriate option. So we must add one.

It is quite straightforward. The necessary change must be done in the
`package/kernel/linux/modules/block.mk` file. It will add kernel module
in `Kernel modules > Block Devices > kmod-ata-core > kmod-ata-pata-821x`.

This piece of code should be added under this submenu:

{% highlight sh %}
define AddDepends/ata
    SUBMENU:=$(BLOCK_MENU)
    DEPENDS+=kmod-ata-core $(1)
endef
{% endhighlight %}

Here is the lines we should add:

{% highlight diff %}
@@ -116,6 +116,17 @@ endef
 $(eval $(call KernelPackage,ata-nvidia-sata))


+define KernelPackage/ata-pata-821x
+  TITLE:=IT8211/2 PATA support
+  KCONFIG:=CONFIG_PATA_IT821X
+  FILES:=$(LINUX_DIR)/drivers/ata/pata_it821x.ko
+  AUTOLOAD:=$(call AutoLoad,41,pata_it821x,1)
+  $(call AddDepends/ata)
+endef
+
+$(eval $(call KernelPackage,ata-pata-821x))
+
+
 define KernelPackage/ata-pdc202xx-old
   SUBMENU:=$(BLOCK_MENU)
   TITLE:=Older Promise PATA controller support
{% endhighlight %}

After those changes, we should have necessary option available and also
we should have visible disks (as `sda`, `sdb`, etc.) when we rebuild image
with correct module selected.

Let's check with `dmesg`:

{% highlight sh %}
[    0.157099] libata version 3.00 loaded.
[    0.996951] pata_it821x 0000:00:0c.0: setting latency timer to 64
[    1.000347] scsi0 : pata_it821x
[    1.004775] scsi1 : pata_it821x
[    1.009032] ata1: PATA max UDMA/133 cmd 0x1f0 ctl 0x3f6 bmdma 0xef00 irq 14
[    1.016821] ata2: PATA max UDMA/133 cmd 0x170 ctl 0x376 bmdma 0xef08 irq 15
[    1.300693] ata1.00: ATA-6: 2GU2M     RDC SD-IDE HOST CONTROLLER, 01000000, max UDMA/133
[    1.309761] ata1.00: 3921920 sectors, multi 0: LBA
[    1.315323] ata1.00: limited to UDMA/33 due to 40-wire cable
[    1.322155] ata1.00: configured for UDMA/33
[    1.539471] ata2.00: CFA: TOSHIBA THNCF512MQG, 3.00, max PIO4
[    1.545879] ata2.00: 1000944 sectors, multi 1: LBA
[    1.589460] ata2.00: configured for PIO4
{% endhighlight %}

Disks should be visible under `/dev`. My µSD card is seen as `sda` and CF card
as `sdb`. So far, so good.


Summary
-------

After those changes, we should be able to use the ethernet device, as well as
memory cards as IDE drives. In the next part we deal with adding Ebox3300
target to OpenWRT build tree and we will look into networking facilities.

