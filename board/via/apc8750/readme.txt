VIA APC 8750
============

This file documents the Buildroot support for the [VIA APC 8750][1] board,
based on the [community-developed linux-vtwm kernel][2].

The APC 8750 is a neo-ITX form factor, 800MHz ARMv6 board with onboard 
NAND flash storage.


Usage
-----

On boot the onboard u-boot looks for and SD card, and a `scriptcmd` on 
the first, FAT formated partition.

Known working boot uses the load address (`loadaddr`) of `0x01000000` in 
u-boot, but needs `0x00400000` as load address and entry point in the 
generated `uImage`.


Known issues
------------

 * No video output: the community kernel has no VGA/HDMI output support
   as of yet
 * No boot if monitor is not connected: as a quirk of the firmware, the
   board does not boot without a VGA or HDMI connection. This can be
   circumvented by connecting a 120R resistor between the VGA pins 1 & 6,
   as a "dummy VGA dongle".


[1]: http://apc.io/products/8750a/
[2]: https://github.com/linux-wmt/linux-vtwm/
