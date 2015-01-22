VIA VAB-820/AMOS-820
====================

These settings are aimed to use Buildroot-generated images on an SD card
with a VIA VAB-820 board or AMOS-820 system. Code is based on the original
VIA BSP, which is in turn based on the Freescale Linux 3.0.35 / 4.1.0 BSP.

Configuring and building Buildroot
----------------------------------

First apply the relevant defconfig

  $ make via_imx6_vab820_defconfig

Then you can edit build uptions by

  $ make menuconfig

When happy with the setup run

  $ make

The results of the build should be these files in the default setting:

  output/images
  ├── 6x_bootscript
  ├── rootfs.tar
  ├── rootfs.tar.gz
  ├── u-boot.bin
  └── uImage

Set up your SD card
-------------------

Important: pay attention which partition you ar modifying so you don't
accidentally erase your host computer's system!

In the default setup you need to create 2 partitions on your SD card:
a boot partition (ext2) and a root partition (ext4). For example, if your
SD card is at /dev/sdX, using fdisk, starting from an empty card:

  # fdisk /dev/sdX
  n  (new partition)
  p  (primary partition)
  <return>  (default first sector, should be at least 2048 or larger)
  50M  (50MB size)
  n  (the second partition)
  p  (primary partition type)
  <return>  (default first sector)
  <return>  (use all remaining space)
  p  (print so the partition looks something like this:)
    Device     Boot  Start      End  Sectors  Size Id Type
    /dev/sdX1         2048   104447   102400   50M 83 Linux
    /dev/sdX2       104448 15564799 15460352  7.4G 83 Linux
  w  (save changes)

After this you need to format the the newly created file system:

  # mkfs.ext2 -L boot /dev/sdX1
  # mkfs.ext4 -L rootfs /dev/sdX2

After this the system can be copied onto the card. First copy the u-boot
onto the region of the card before the first partition (starting from the
root directory of buildroot):

  # dd if=output/images/u-boot.bin bs=512 seek=2 skip=2 of=/dev/sdX

Mount the first partition /dev/sdX1, and copy the boot script and kernel:

  # cp output/images/6x_bootscript /mnt/<BOOT-PARTITION>
  # cp output/images/uImage /mnt/<BOOT-PARTITION>

Finally copy the root file system as well onto the mounted /dev/sdX1
rootfs partition:

  # tar xzvf output/images/rootfs.tar.gz -C /mnt/<ROOTFS-PARTITION>

Booting
-------

On the VAB-820 board make sure the jumper J11 (just next to the SD card
slot) is sorted betweek  the two pins towards the center of the board.
This signals to the onboard bootloader to try get the u-boot from the
SD card.

If you haven't modified the u-boot variables (i.e. never run a `saveenv`
in the u-boot console), the SD card should automatically boot.

If you have modified the variables, you might need to run a `destroyenv`
in the u-boot console to clear all the settings and let the just compiled
u-boot and the 6x_bootscript take care of things.

In the future, add your modifications to the defaults at
`board/via/imx6_vab820/6x_bootscript.txt`, and see the `post-build.sh`
in the same directory on how to create a new `6x_bootscript`.
