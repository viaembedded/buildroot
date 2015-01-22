VIA VAB-820/AMOS-820
====================

This file documents the Buildroot support for the VIA VAB-820 board and
VIA AMOS-820 system, which are built around a Freescale i.MX6 Quad/Dual SoC.
The kernel and u-boot is based on the official VIA BSP, which is in turn
based on the Freescale Linux 3.0.35 / 4.1.0 BSP.


Configuring and building Buildroot
----------------------------------

First apply the relevant defconfig

  $ make via_imx6_vab820_defconfig

Then you can edit build options by

  $ make menuconfig

When happy with the setup run

  $ make

The result of the build with the default settings should be these files:

  output/images
  ├── 6x_bootscript
  ├── rootfs.tar
  ├── u-boot.bin
  └── uImage


Set up your SD card
-------------------

*Important*: pay attention which partition you are modifying so you don't
accidentally erase the wrong file system, e.g your host computer or your
external storage!

In the default setup you need to create 2 partitions on your SD card:
a boot partition and a root partition. In this guide and in the default settings
the boot partition is ext2, while the root partition is ext4.

You also need to leave space for u-boot at the beginning of the card, before
all the partitions.

For example, if your SD card is at /dev/sdX, using fdisk, and starting from
an empty card, the steps are along these lines:

  # fdisk /dev/sdX
  n  (new partition)
  p  (primary partition)
  <return>  (default first sector, should be at least 1MB from the beginning
             which is 2048 sectors if the sector size is 512KB)
  +50M  (50MB size, as an example)
  n  (the second partition)
  p  (primary partition type)
  <return>  (default first sector)
  <return>  (use all remaining space)
  p  (print so the partition looks something like this:)
    Device     Boot  Start      End  Sectors  Size Id Type
    /dev/sdX1         2048   104447   102400   50M 83 Linux
    /dev/sdX2       104448 15564799 15460352  7.4G 83 Linux
  w  (save changes)

After this you need to format the newly created file system:

  # mkfs.ext2 -L boot /dev/sdX1
  # mkfs.ext4 -L rootfs /dev/sdX2

After this the system can be copied onto the card. First copy the u-boot
onto the region of the card before the first partition (starting from the
root directory of buildroot):

  # dd if=output/images/u-boot.bin bs=512 seek=2 skip=2 of=/dev/sdX

Mount the first partition /dev/sdX1, and copy the boot script and kernel:

  # cp output/images/6x_bootscript /mnt/<BOOT-PARTITION>
  # cp output/images/uImage /mnt/<BOOT-PARTITION>

Finally, copy the root file system onto the mounted (empty) /dev/sdX2
rootfs partition:

  # tar xf output/images/rootfs.tar -C /mnt/<ROOTFS-PARTITION>


Booting
-------

To use the on-card u-boot, you need adjust jumper J11 located just next to
the SD card slot on the VAB-820 board. The correct position for SD card
boot is jumping the two pins on the right, or toward the center of the board.

If you have modified the u-boot variables stored in the SPI flash, you might
need to run a `destroyenv` in the u-boot console to clear all the settings,
and let the u-boot you just compiled, and the 6x_bootscript take care of
booting the board.

In the future, add your modifications to the defaults in
`board/via/imx6_vab820/6x_bootscript.txt`, and see the `post-build.sh`
script in the same directory on how to generate a new `6x_bootscript`.

Please note: in the default buildroot setup the login prompt only shows
over the serial debug line, and does not show on the video out.


eMMC boot
---------

To run the system from the onboard eMMC storage, you can follow similar steps,
but replacing `mmcblk1` with `mmcblk0` in the instructions and scripts, and
working from a system already running on the SD card.
