# ArchLinux ARM kernel package with driver for Cirrus Logic/Wolfson Audio Card.

Supported devices:

* [Wolfson Audio Card](http://www.element14.com/wolfson) for Raspberry Pi B,
* [Cirrus Logic Audio Card](http://www.element14.com/cirruslogic_ac) for Raspberry Pi B+/A+/2.

----

Modified Linux kernel source: [HiassofT/rpi-linux](http://github.com/HiassofT/rpi-linux/tree/cirrus-4.1.y).

----

This `PKGBUILD` brings you this kernel to [ArchLinux ARM](http://archlinuxarm.org/platforms/armv6/raspberry-pi) OS. It is based on `PKGBUILD` from [ArchLinux ARM repository](http://github.com/archlinuxarm/PKGBUILDs/tree/master/core/linux-raspberrypi).

````
[wolfsound@wolfsound ~]$ uname -a
Linux wolfsound 4.1.16-1-WSP #1 Fri Jan 29 17:01:48 CET 2016 armv6l GNU/Linux
[wolfsound@wolfsound ~]$ aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: sndrpiwsp [snd_rpi_wsp], device 0: WM5102 AiFi wm5102-aif1-0 []
  Subdevices: 0/1
  Subdevice #0: subdevice #0
````

----

# Instalation of prepared packages.

Follow [this link](http://headless.audio) for detailed instructions.

# Build & installation from sources.

1. Clone this repository.

   ````
   git clone --depth=1 http://github.com/RoEdAl/linux-raspberrypi-wsp.git
   ````
1. [Build package](http://wiki.archlinux.org/index.php/Makepkg):
  
   ````
   cd linux-raspberrypi-wsp
   makepkg -sL  
   ````

   Compilation takes long time. Consider using [`distccd`](http://archlinuxarm.org/developers/distcc-cross-compiling) or/and `ccache`.
   You may also compile this package on a PC using [QEMU Chroot](http://wiki.archlinux.org/index.php/Raspberry_Pi#QEMU_chroot).
1. Install kernel package:

   ````
   pacman -U linux-raspberrypi-wsp-4.1.13-1.2-armv6h.pkg.tar.xz
   ````
    
   Optionally install kernel headers package (for developers only):
  
   ````
   pacman -U linux-raspberrypi-wsp-headers-4.1.13-1.2-armv6h.pkg.tar.xz
   ````
1. Enable and configure *Cirrus Logic/Wolfson* audio card in `/boot/options.txt` file:

   ````
   # Configures Cirrus Logic/Wolfson audio card
   dtoverlay=rpi-cirrus-wm5102
   ````

   Optionally enable **mmap** support:

   ````
   # Enables mmap support in the bcm2708-i2s driver
   dtoverlay=i2s-mmap
   ````
1. Enable onboard audio interface in `/boot/options.txt` file if you need it (disabled by default):

   ````
   dtparam=audio=on
   ````
1. Reboot:

   ````
   sudo reboot
   ````

# Links.

* [Experimental OpenELEC Raspberry Pi test builds with support for Wolfson/Cirrus Logic Audio Card](http://www.horus.com/~hias/tmp/openelec-wolfson/)
  * [Readme](http://www.horus.com/~hias/tmp/openelec-wolfson/00README.txt)
* [element14 community - Driver fixes and updates to kernel 3.18.16 and 4.0.5](http://www.element14.com/community/thread/43711/l/driver-fixes-and-updates-to-kernel-31816-and-405?displayFullThread=true)
* [Cirrus Logic's useful mixer scripts](https://github.com/CirrusLogic/wiki-content)
* [Cirrus Logic's modified Linux kernel source](http://github.com/CirrusLogic/rpi-linux)
* [Archphile](http://archphile.org) - Yet Another Archlinux Based Audiophile Distribution for Raspberry Pi, Udoo and Cubox-i
    * [Forum](http://forum.archphile.org)
    * [Repositiories](http://github.com/archphile)
* [Cirrus Logic Audio Card on Raspberry Pi 2 with Debian Jessie](http://stmllr.net/blog/cirrus-logic-audio-card-on-raspberry-pi2-with-debian-jessie)
* [raspberrypi/linux - bcm2708-i2s lacks mmap support](http://github.com/raspberrypi/linux/issues/1004)
