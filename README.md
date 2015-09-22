# fix-ms-mouse-scroll
 Patch to fix scroll wheel problems with certain Microsoft mice in Linux.

# Problem exploration
Some wireless Microsoft mouses have strange behaviour in Linux after working in Microsoft Windows - scroll is too fast. Possible workaround - reinsert mouse receiver to usb (i.e. power off receiver). More information can be found here:
 * [http://sourceforge.net/projects/resetmsmice/](http://sourceforge.net/projects/resetmsmice/)
 * [https://bbs.archlinux.org/viewtopic.php?id=177916](https://bbs.archlinux.org/viewtopic.php?id=177916)
fix-ms-mouse-scroll patch for Linux kernel can fixes this behaviour.

# Supported mouses
 Only Microsoft mouses are supported, so device usb vendor id should be 0x045e.
 Usb devices with following device id supported:
  * 0x07a5:
   ** Microsoft sculpt ergonomic mouse
  * 0x0745 - Not sure, possible following mouses:
   ** Microsoft Wireless Mouse 1000
   ** Microsoft Wireless Optical Desktop 3000
   ** Microsoft Wireless Mobile Mouse 3500
   ** Microsoft Wireless Mobile Mouse 4000
   ** Microsoft Comfort Mouse 4500
   ** Microsoft Wireless Mouse 5000

# Supported Linux kernels
 Patches tested on the following vanilla kernel and/or [gentoo default kernel](https://packages.gentoo.org/package/sys-kernel/gentoo-sources):
  1. 3.3.0 (tested only compilation)
  2. 4.0.5 (tested compilation & really working)
  3. 4.2.0 (tested only compilation)
  4. 4.3.0-rc2 (tested only compilation)

# Choose appropriate patch version
Following name convention is used:
 fix-ms-mouse-scroll-<v>.patch
 where "<v>" - primary kernel version for witch patch is developed.
  * For kernel <3.3.0 try to use "fix-ms-mouse-scroll-3.3.0.patch"
  * For kernel 3.3.0 use "fix-ms-mouse-scroll-3.3.0.patch"
  * For kernel >3.3.0 but <4.0.5 try to use "fix-ms-mouse-scroll-3.3.0.patch" or "fix-ms-mouse-scroll-4.0.5.patch" (choose version with wich there is no error at kernel "build")
  * For kernel >=4.0.5 but <=4.3.0 use "fix-ms-mouse-scroll-4.0.5.patch"
  * For kernel >4.3.0 try to use "fix-ms-mouse-scroll-4.0.5.patch" and look at special notes about such case (or ask me to update patch if kernel build failed)

# How to apply patch
 1. Download appropriate patch (e.g. `/home/unsacrificed/Downloads/fix-ms-mouse-scroll-4.0.5.patch`)
 2. In terminal "cd" to directory with your kernel sources (e.g. `cd /usr/src/linux-4.0.5-gentoo`)
 3. Apply patch with "patch -p1" command (e.g. `patch -p1 < /home/unsacrificed/Downloads/fix-ms-mouse-scroll-4.0.5.patch`)
 4. Rebuild and install your kernel as usual (no special kernel configuration needed)

# How to view usb device vendor id and device id (aka product id)
 1. Find it in kernel log
  Use something like `sudo journalctl -k` or `sudo less /var/log/dmesg`. Example output:
  `usb 1-4: New USB device found, idVendor=045e, idProduct=07a5
  usb 1-4: New USB device strings: Mfr=1, Product=2, SerialNumber=0
  usb 1-4: Product: Microsoft® 2.4GHz Transceiver v9.0
  usb 1-4: Manufacturer: Microsoft`
  where "045e" - vendor id, "07a5" - device id.
 2. Using `lsusb`
  Run `lsusb` to show installed devices. Example output:
  `Bus 001 Device 002: ID 045e:07a5 Microsoft Corp.`
  where "045e" - vendor id, "07a5" - device id.

# Add new mouse support
 If you have Microsoft mouse with scroll bug and mouse isn't supported by this patch do following:
 1. Change "0x07a5" in patch to your mouse device id
 2. Apply modified patch
 3. If modified patch help you to resolve scroll bug - let me know and I extend supported mouses.

# Apply patch to kernel newer then 4.3.0
 Be shure that after apllying patch to such kernels in file "drivers/hid/hid-microsoft.c" MS_VSCROLL define have unique value over the block of MS_ defines. Multiply MS_VSCROLL defined value by 2 while it isn't unique if needed.
 E.g., 4.0.5 has following block:
  `#define MS_HIDINPUT		0x01
   #define MS_ERGONOMY		0x02
   #define MS_PRESENTER		0x04
   #define MS_RDESC		0x08
   #define MS_NOGET		0x10
   #define MS_DUPLICATE_USAGES	0x20
   #define MS_RDESC_3K		0x40`
  so MS_VSCROLL	defined value should be 0x80 (0x40 * 2).