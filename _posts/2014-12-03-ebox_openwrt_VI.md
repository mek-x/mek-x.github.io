---
layout: post
title:  "OpenWRT on Ebox3300 (VI): Adding target to the build system"
date:   2014-12-03
tags: openwrt
categories: ebox
---

This is the last part of the quick overview showing how to put and run OpenWRT
on an exemplary device. I'm going to describe how to add this device as
build system target in order to use it as any other. This is rather short
part, because it's only about adding several files to the OpenWRT's sources
tree.


Adding target dir
-----------------

This is quite simple. We need to add a target directory in `target/linux/x86/`
subdir. In my case it's `ebox3300` dir. In this directory we need to have
a target makefile and default kernel config. We may also have some platform
specific files. I recommend looking at the other targets, because they are
perfect examples.

The target makefile in my case looks like that:

{% highlight text %}
BOARDNAME:=EBOX-3300A
FEATURES:=ext4 pci usb
DEFAULT_PACKAGES += kmod-ata-core \
                    kmod-r6040 \
                    kmod-usb-core kmod-usb-ohci kmod-usb2

define Target/Description
    Build firmware images for DMP Electronics EBOX3300 mini pc
endef
{% endhighlight %}

As we can see, there are bunch of variables and target description. I think
they are pretty self-explanatory. For details, please check documentation,
which can be built (sources are in the `docs` dir, in latex format).

Other file, which is needed, is default linux kernel config. Here is config
I've used:

{% highlight text %}
# CONFIG_3C515 is not set
# CONFIG_AC3200 is not set
CONFIG_ACPI=y
CONFIG_ACPI_AC=y
CONFIG_ACPI_BATTERY=y
CONFIG_ACPI_BLACKLIST_YEAR=0
CONFIG_ACPI_BUTTON=y
# CONFIG_ACPI_CMPC is not set
# CONFIG_ACPI_CONTAINER is not set
# CONFIG_ACPI_CUSTOM_DSDT is not set
# CONFIG_ACPI_DEBUG is not set
# CONFIG_ACPI_DOCK is not set
# CONFIG_ACPI_EC_DEBUGFS is not set
# CONFIG_ACPI_FAN is not set
CONFIG_ACPI_I2C=y
# CONFIG_ACPI_INITRD_TABLE_OVERRIDE is not set
# CONFIG_ACPI_PCI_SLOT is not set
CONFIG_ACPI_PROCESSOR=y
# CONFIG_ACPI_PROCESSOR_AGGREGATOR is not set
# CONFIG_ACPI_PROCFS is not set
# CONFIG_ACPI_PROCFS_POWER is not set
# CONFIG_ACPI_PROC_EVENT is not set
# CONFIG_ACPI_SBS is not set
CONFIG_ACPI_THERMAL=y
CONFIG_ACPI_VIDEO=y
# CONFIG_ACPI_WMI is not set
CONFIG_AGP=y
# CONFIG_AGP_ALI is not set
# CONFIG_AGP_AMD is not set
# CONFIG_AGP_AMD64 is not set
# CONFIG_AGP_ATI is not set
# CONFIG_AGP_EFFICEON is not set
CONFIG_AGP_INTEL=y
# CONFIG_AGP_NVIDIA is not set
# CONFIG_AGP_SIS is not set
# CONFIG_AGP_SWORKS is not set
# CONFIG_AGP_VIA is not set
# CONFIG_APPLE_GMUX is not set
# CONFIG_APRICOT is not set
# CONFIG_ASUS_LAPTOP is not set
# CONFIG_AT1700 is not set
# CONFIG_BACKLIGHT_ADP8860 is not set
# CONFIG_BACKLIGHT_ADP8870 is not set
# CONFIG_BACKLIGHT_APPLE is not set
CONFIG_BACKLIGHT_CLASS_DEVICE=y
CONFIG_BACKLIGHT_GENERIC=y
CONFIG_BACKLIGHT_LCD_SUPPORT=y
# CONFIG_BACKLIGHT_SAHARA is not set
CONFIG_BLK_DEV_SR=y
# CONFIG_BLK_DEV_SR_VENDOR is not set
# CONFIG_BLK_DEV_XD is not set
CONFIG_CHROMEOS_LAPTOP=n
CONFIG_CONSOLE_TRANSLATIONS=y
CONFIG_CPU_IDLE_GOV_MENU=y
# CONFIG_DEPCA is not set
CONFIG_DMA_SHARED_BUFFER=y
CONFIG_DMI=y
# CONFIG_DMIID is not set
# CONFIG_DMI_SYSFS is not set
CONFIG_DRM=y
# CONFIG_DRM_AST is not set
# CONFIG_DRM_CIRRUS_QEMU is not set
# CONFIG_DRM_GMA500 is not set
# CONFIG_DRM_I2C_CH7006 is not set
# CONFIG_DRM_I2C_NXP_TDA998X is not set
# CONFIG_DRM_I2C_SIL164 is not set
# CONFIG_DRM_I810 is not set
CONFIG_DRM_I915=y
CONFIG_DRM_I915_KMS=y
CONFIG_DRM_KMS_HELPER=y
# CONFIG_DRM_LOAD_EDID_FIRMWARE is not set
# CONFIG_DRM_MGA is not set
# CONFIG_DRM_MGAG200 is not set
# CONFIG_DRM_NOUVEAU is not set
# CONFIG_DRM_QXL is not set
# CONFIG_DRM_R128 is not set
# CONFIG_DRM_RADEON is not set
# CONFIG_DRM_SAVAGE is not set
# CONFIG_DRM_SIS is not set
# CONFIG_DRM_TDFX is not set
# CONFIG_DRM_UDL is not set
# CONFIG_DRM_VIA is not set
# CONFIG_DRM_VMWGFX is not set
CONFIG_DUMMY_CONSOLE=y
# CONFIG_EFI is not set
# CONFIG_EISA is not set
# CONFIG_EL1 is not set
# CONFIG_EL16 is not set
# CONFIG_EL2 is not set
# CONFIG_EL3 is not set
# CONFIG_ELPLUS is not set
CONFIG_FB=y
CONFIG_FB_CFB_COPYAREA=y
CONFIG_FB_CFB_FILLRECT=y
CONFIG_FB_CFB_IMAGEBLIT=y
# CONFIG_FB_I810 is not set
# CONFIG_FB_VESA is not set
# CONFIG_FB_WMT_GE_ROPS is not set
# CONFIG_FONTS is not set
CONFIG_FONT_8x16=y
CONFIG_FONT_8x8=y
CONFIG_FRAMEBUFFER_CONSOLE=y
CONFIG_FRAMEBUFFER_CONSOLE_DETECT_PRIMARY=y
# CONFIG_FRAMEBUFFER_CONSOLE_ROTATION is not set
# CONFIG_FUJITSU_LAPTOP is not set
# CONFIG_GEOS is not set
CONFIG_HID=y
CONFIG_HID_BATTERY_STRENGTH=y
CONFIG_HPET=y
CONFIG_HPET_MMAP=y
# CONFIG_HP_ACCEL is not set
CONFIG_HW_CONSOLE=y
CONFIG_I2C=y
CONFIG_I2C_ALGOBIT=y
CONFIG_I2C_BOARDINFO=y
CONFIG_INPUT=y
CONFIG_INPUT_KEYBOARD=y
CONFIG_INPUT_MOUSE=y
CONFIG_INPUT_MOUSEDEV=y
CONFIG_INPUT_MOUSEDEV_PSAUX=y
CONFIG_INPUT_MOUSEDEV_SCREEN_X=1024
CONFIG_INPUT_MOUSEDEV_SCREEN_Y=768
CONFIG_INTEL_IDLE=y
# CONFIG_INTEL_IPS is not set
# CONFIG_INTEL_MENLOW is not set
CONFIG_ISA=y
CONFIG_ISAPNP=y
CONFIG_ISO9660_FS=y
# CONFIG_JOLIET is not set
CONFIG_KEYBOARD_ATKBD=y
# CONFIG_LANCE is not set
# CONFIG_LCD_CLASS_DEVICE is not set
# CONFIG_LEDS_CLEVO_MAIL is not set
# CONFIG_MDA_CONSOLE is not set
# CONFIG_MIXCOMWD is not set
# CONFIG_MOUSE_BCM5974 is not set
# CONFIG_MOUSE_CYAPA is not set
CONFIG_MOUSE_PS2=y
CONFIG_MOUSE_PS2_ALPS=y
# CONFIG_MOUSE_PS2_CYPRESS is not set
# CONFIG_MOUSE_PS2_ELANTECH is not set
CONFIG_MOUSE_PS2_LIFEBOOK=y
CONFIG_MOUSE_PS2_LOGIPS2PP=y
CONFIG_MOUSE_PS2_SYNAPTICS=y
# CONFIG_MOUSE_PS2_TOUCHKIT is not set
CONFIG_MOUSE_PS2_TRACKPOINT=y
# CONFIG_MOUSE_SERIAL is not set
# CONFIG_MOUSE_VSXXXAA is not set
# CONFIG_NET_VENDOR_RACAL is not set
CONFIG_NLS=y
CONFIG_NO_HZ=y
# CONFIG_PANASONIC_LAPTOP is not set
CONFIG_PATA_AMD=y
CONFIG_PATA_IT821X=y
CONFIG_PATA_LEGACY=y
CONFIG_PATA_MPIIX=y
CONFIG_PATA_OLDPIIX=y
CONFIG_PATA_PLATFORM=y
CONFIG_PATA_SC1200=y
CONFIG_PATA_VIA=y
CONFIG_PCIEAER=y
CONFIG_PCIEPORTBUS=y
CONFIG_PCI_IOAPIC=y
CONFIG_PCI_LABEL=y
CONFIG_PCI_MMCONFIG=y
# CONFIG_PCWATCHDOG is not set
CONFIG_PNP=y
CONFIG_PNPACPI=y
# CONFIG_PNPBIOS is not set
CONFIG_PNP_DEBUG_MESSAGES=y
# CONFIG_PVPANIC is not set
CONFIG_SATA_AHCI=y
CONFIG_SCHED_HRTICK=y
# CONFIG_SCx200_ACB is not set
CONFIG_SERIAL_8250_PNP=y
# CONFIG_THINKPAD_ACPI is not set
# CONFIG_TOPSTAR_LAPTOP is not set
# CONFIG_TOSHIBA_BT_RFKILL is not set
CONFIG_USB=y
CONFIG_USB_COMMON=y
CONFIG_USB_EHCI_HCD=y
# CONFIG_USB_EHCI_HCD_PLATFORM is not set
CONFIG_USB_EHCI_PCI=y
# CONFIG_USB_OHCI_BIG_ENDIAN_DESC is not set
# CONFIG_USB_OHCI_BIG_ENDIAN_MMIO is not set
CONFIG_USB_OHCI_HCD=y
# CONFIG_USB_OHCI_HCD_PLATFORM is not set
CONFIG_USB_STORAGE=y
CONFIG_USB_UHCI_HCD=y
CONFIG_VGACON_SOFT_SCROLLBACK=y
CONFIG_VGACON_SOFT_SCROLLBACK_SIZE=64
CONFIG_VGA_CONSOLE=y
CONFIG_VIDEO_OUTPUT_CONTROL=y
CONFIG_VT=y
CONFIG_VT_CONSOLE=y
# CONFIG_VT_HW_CONSOLE_BINDING is not set
# CONFIG_WDT is not set
# CONFIG_X86_ACPI_CPUFREQ is not set
# CONFIG_X86_E_POWERSAVER is not set
# CONFIG_X86_INTEL_LPSS is not set
# CONFIG_X86_LONGHAUL is not set
# CONFIG_X86_PCC_CPUFREQ is not set
CONFIG_X86_PM_TIMER=y
# CONFIG_XO15_EBOOK is not set
# CONFIG_ZISOFS is not set
{% endhighlight %}

File is named `config-3.10`. Name is important, because config is identified
by that name. We can have several configs for different kernel versions.
In this case, I've used just the 3.10 kernel version.

Important change from the default config for other x86 based platforms is
`CONFIG_PATA_IT821X=y`. This driver must be compiled in kernel, rather than
built as module, because the default for x86 targets is to mount
the main file system straight from the disk. This is simplest implementation.
The target can be configured to boot with initial ramdisk, rather than mount root
file system from disk. In that case, this option wouldn't be needed.

Modifying platform makefile
---------------------------

Now, we have to modify the main platform makefile which is
`target/linux/x86/Makefile`. We just need to add `ebox3300` subtarget. The name
is just a target directory name. Here is the diff:

{% highlight diff %}
diff --git a/target/linux/x86/Makefile b/target/linux/x86/Makefile
index 13f15c0..cc8ad8b 100644
--- a/target/linux/x86/Makefile
+++ b/target/linux/x86/Makefile
@@ -10,8 +10,8 @@  ARCH:=i386
 BOARD:=x86
  BOARDNAME:=x86
   FEATURES:=squashfs ext4 vdi vmdk pcmcia targz
   -SUBTARGETS=generic olpc xen_domu ep80579 net5501 kvm_guest geos alix2 thincan \
   -       rdc
   +SUBTARGETS=generic olpc xen_domu ebox3300 ep80579 net5501 kvm_guest geos alix2 \
   +       thincan rdc

    KERNEL_PATCHVER:=3.10
{% endhighlight %}

This is it. We may "dirclean" our source tree (`make dirclean`) and run
menuconfig. There should be our new EBOX3300 subtarget, when we select x86
target system. From this point, we may use this build system as easy as for any
other target.

Summing up
----------

As for the device, I've been using it for about three months now and
haven't run into any problems. System is perfectly stable and works as good
as any off-the-shelf router. Even better, it's fully customizable and has
enormous potential. For example, I've established permanent OpenVPN tunnel
connecting my two remote networks. My future plans include of making this
system as an weather data acquisition device and I know it's easily capable
of that task.
