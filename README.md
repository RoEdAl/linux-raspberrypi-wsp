# ArchLinux kernel package with Cirrus Logic (Wolfson) Audio Card support

* [Wolfson Audio Card for Raspberry Pi B](http://www.element14.com/community/docs/DOC-55903)
* [Cirrus Logic Audio Card for Raspberry Pi B+/A+/2](http://www.element14.com/community/docs/DOC-71261)

----

Modified Linux kernel source: [HiassofT/rpi-linux](https://github.com/HiassofT/rpi-linux/tree/cirrus-4.1.y).

This `PKGBUILD` brings you this kernel to [ArchLinux ARM](http://archlinuxarm.org/platforms/armv6/raspberry-pi) OS. It is based on `PKGBUILD` from [ArchLinux ARM repository](https://github.com/archlinuxarm/PKGBUILDs/raw/master/core/linux-raspberrypi/PKGBUILD).


````
[wolfsound@wolfsound ~]$ aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: sndrpiwsp [snd_rpi_wsp], device 0: WM5102 AiFi wm5102-aif1-0 []
  Subdevices: 0/1
  Subdevice #0: subdevice #0
````

## Installation
1.  Clone this repository.

    ````
    git clone --depth=1 http://github.com/RoEdAl/linux-raspberrypi-wsp.git
    ````

2.  [Build package](https://wiki.archlinux.org/index.php/Makepkg):
  
    ````
    cd linux-raspberrypi-wsp
    makepkg -sL  
    ````

    Compilation takes long time. Consider using `distccd` or/and `ccache`.

3.  Install package:

    ````
    pacman -U linux-raspberrypi-wsp-4.1.6-1-armv6h.pkg.tar.xz
    ````

4.  Add `rpi-cirrus-wm5102` [overlay tree](https://www.raspberrypi.org/documentation/configuration/device-tree.md) to `/boot/options.txt` file:

    ````
    # See /boot/overlays/README for a detailed list and description of additional
    #   overlays and their configuration options.
    #
    dtoverlay=rpi-cirrus-wm5102
    ````

4.  Optionally edit `/etc/modules-load.d/raspberrypi.conf` file to prevent loading of [`snd-bcm2835`](https://wiki.archlinux.org/index.php/Raspberry_Pi#Audio) kernel module:

    ````
    bcm2708-rng
    #snd-bcm2835
    ````
    
5. Reboot:

    ````
    sudo reboot
    ````

## Links

* [Experimental OpenELEC Raspberry Pi test builds with support for Wolfson/Cirrus Logic Audio Card](http://www.horus.com/~hias/tmp/openelec-wolfson/)
  * [Readme](http://www.horus.com/~hias/tmp/openelec-wolfson/00README.txt)
* [Cirrus Logic's modified Linux kernel source](https://github.com/CirrusLogic/rpi-linux)
* [Useful mixer scripts](https://github.com/CirrusLogic/wiki-content)
