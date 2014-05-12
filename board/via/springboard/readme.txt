VIA VAB-600 Springboard
=======================

The Springboard is a VIA Cortex-A9 SoC on Pico-ITX form factor board.
http://www.viaspringboard.com/

This configuration is intended as a base image.


Installation
============

These instructions are based on the available Linux Board Support Package (BSP)
contents for the Springboard.


Compile buildroot
-----------------

The buildroot rootfs is built by:

    make via_springboard_defconfig
    make

The required result is "output/images/rootfs.tar.gz"


Download the Linux Development Package
-----------------------------------------

The Linux Development package is at
http://www.viaspringboard.com/via-vab-600-springboard-software-development-packages.html

It contains resources prepared to flash a new system onto the Springboard (a Debian system),
as well as the kernel source, tool chain, and evaluation guide.

The file is "SpringBoard_Linux_Development_Package_v1.00.00.zip"
Extract the contents of the file somewhere on your system.

This is used for the Linux kernel image (3.0.8, uzImage.bin).


Prepare an SD Card
-------------------

1. Prepare an SD card (at least 2GB), and format with FAT partition.

2. From the "EVK/Update_Package" directory copy "scriptcmd" and the "bspinst/" directory
   ontp the SD card.

3. Remove the existing ".tgz" files from the SD card's "bpinst/packages" directory.

4. Copy buildroot's rootfs.tar.gz to the SD card's "bpinst/packages/rootfs.tgz", make
   sure it has a "tgz" extension! (The rootfs will be installed from here and the stock
   install script appears to only handle ".tgz" extensions correctly at the moment).

5. in "bpinst/bspinst.cfg" find the line:
   "setenv wmt.display.param 4:6:1:1920:1080:60"
   This is the screen setting. If your display is npot 1920x1080, update this line to have
   the correct resolution. Eg. for 1280x1024 display at 75Hz should read:
   "setenv wmt.display.param 4:6:1:1280:1024:75"
   (without the quotes, of course) 

5. Plug in the SD card to the Springboard SD card slot, and power up the board

6. After a few seconds, the automatic update of the eMMC storage should start. Wait
   until that's finished. If the screen is scrambled at that time, just wait ~5 minutes
   just to be super-safe until going to the next step.

7. Press the SD Card to remove it. The Springboard should shut down automatically. Power
   on again to start with the new images. If the screen is still scrabled on this boot,
   check step 5. and update the display settings, and reflash.

8. If everything works well, you should arrive to the login prompt, and can get started.
   Springboard <3 Buildroot!


Advanced
========

If you want to modify the kernel, the Linux Development Package has the whole source code
of the BSP kernel, as well as the toolchain and documentation how to do modifications.
