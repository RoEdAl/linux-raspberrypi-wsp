# Linux kernel package with Cirrus Logic (Wolfson) audio card support

````
[wolfsound@wolfsound ~]$ aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: sndrpiwsp [snd_rpi_wsp], device 0: WM5102 AiFi wm5102-aif1-0 []
  Subdevices: 0/1
  Subdevice #0: subdevice #0
````

## Installation

1.  Add `rpi-cirrus-wm5102` overlay tree to `options.txt` file:

    ````
    # See /boot/overlays/README for a detailed list and description of additional
    #   overlays and their configuration options.
    #
    dtoverlay=rpi-cirrus-wm5102
    ````

2.  Optionally edit `/etc/modules-load.d/raspberrypi.conf` file to prevent loading of `snd-bcm2835` kernel module:

    ````
    bcm2708-rng
    #snd-bcm2835
    ````
