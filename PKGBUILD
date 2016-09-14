#
# Linux kernel Cirrus Logic soundcard support
# Patches taken from: http://github.com/HiassofT/rpi-linux/tree/cirrus-4.4.y
#
# Maintainer: Dave Higham <pepedog@archlinuxarm.org>
# Maintainer: Kevin Mihelich <kevin@archlinuxarm.org>
# Maintainer: Oleg Rakhmanov <oleg@archlinuxarm.org>

# NOTE: Packages replace linux-raspberrypi-latest, remove if that package comes back

buildarch=20

pkgbase=linux-raspberrypi-wsp
_commit=dd9188011786fb62a7960922f31e8e086fb2009b
_cfg_commit=78b04515b74667268e27ae1456e02cc9870d571a
_srcname=linux-${_commit}
_kernelname=${pkgbase#linux}
_desc="Raspberry Pi (Cirrus Logic)"
pkgver=4.4.20
pkgrel=2
bfqver=v7r11
arch=('armv6h' 'armv7h')
url="http://www.kernel.org/"
license=('GPL2')
makedepends=('xmlto' 'docbook-xsl' 'kmod' 'inetutils' 'bc' 'git')
options=('!strip')

source=("http://github.com/raspberrypi/linux/archive/${_commit}.tar.gz"
	'wsp-patches::git+https://github.com/RoEdAl/linux-raspberrypi-wsp-patches.git#branch=cirrus-4.4.y'
	"git+https://github.com/sfjro/aufs4-standalone.git#branch=aufs${pkgver%.*}"
        "bfq1.patch::ftp://teambelgium.net/bfq/patches/${pkgver%.*}.0-${bfqver}/0001-block-cgroups-kconfig-build-bits-for-BFQ-${bfqver}-${pkgver%.*}.0.patch"
        "bfq2.patch::ftp://teambelgium.net/bfq/patches/${pkgver%.*}.0-${bfqver}/0002-block-introduce-the-BFQ-${bfqver}-I-O-sched-for-${pkgver%.*}.0.patch"
        "bfq3.patch::ftp://teambelgium.net/bfq/patches/${pkgver%.*}.0-${bfqver}/0003-block-bfq-add-Early-Queue-Merge-EQM-to-BFQ-${bfqver}-for.patch"
	'config.txt'
        "http://github.com/archlinuxarm/PKGBUILDs/raw/${_cfg_commit}/core/linux-raspberrypi/cmdline.txt"
        'cirrus.conf'
        'raspberrypi.conf')
source_armv6h=("config-armv6h::http://github.com/archlinuxarm/PKGBUILDs/raw/${_cfg_commit}/core/linux-raspberrypi/config.v6"
               'config-armv6h.patch' )
source_armv7h=("config-armv7h::http://github.com/archlinuxarm/PKGBUILDs/raw/${_cfg_commit}/core/linux-raspberrypi/config.v7"
               'config-armv7h.patch'
	       'https://archlinuxarm.org/builder/src/brcmfmac43430-sdio.bin' 'https://archlinuxarm.org/builder/src/brcmfmac43430-sdio.txt')

md5sums=('240ca2ec07612f0d1ff0000a69b5d86d'
         'SKIP'
         'SKIP'
         'c1d7fcfe88edb658375089c0a9cc1811'
         '953133d5e387de2ad79ac0ae5c27cb6b'
         'f0387e673975e9f2a5e05136948edece'
         '42cae552a8aca187560b60a8fc771811'
         '60bc3624123c183305677097bcd56212'
         '71bc3a50eb404709ff78f393aed3d0e8'
         '4511272ed4336120645b68e74f75cb92')
md5sums_armv6h=('a5e85ed9120c9dcb14844c6602836f23'
                '46304bd952f160509480fc0420c765e6')
md5sums_armv7h=('db60becb81cadd664b2bab2dbbd0b335'
                'ea4ca2a0cac5e7491ebd320a90572564'
                '4a410ab9a1eefe82e158d36df02b3589'
                '8c3cb6d8f0609b43f09d083b4006ec5a')

prepare() {
  cd "${srcdir}/${_srcname}"

  msg2 'AUFS patches'
  cp -ru "${srcdir}/aufs4-standalone/Documentation" .
  cp -ru "${srcdir}/aufs4-standalone/fs" .
  cp -ru "${srcdir}/aufs4-standalone/include/uapi/linux/aufs_type.h" ./include/linux
  cp -ru "${srcdir}/aufs4-standalone/include/uapi/linux/aufs_type.h" ./include/uapi/linux

  patch -Np1 -i ../aufs4-standalone/aufs4-kbuild.patch
  patch -Np1 -i ../aufs4-standalone/aufs4-base.patch
  patch -Np1 -i ../aufs4-standalone/aufs4-mmap.patch
  patch -Np1 -i ../aufs4-standalone/aufs4-standalone.patch

  msg2 'BFQ patches'
  patch -Np1 -i "${srcdir}/bfq1.patch"
  patch -Np1 -i "${srcdir}/bfq2.patch"
  patch -Np1 -i "${srcdir}/bfq3.patch"

  msg2 'WSP patch'
  patch -tNp1 --unified -i "${srcdir}/wsp-patches/squashed/0001-Add-support-to-Cirrus-Logic-Wolfson-sound-card.patch"

  msg2 'Configuration'
  cat "${srcdir}/config-${CARCH}" > ./.config
  patch "./.config" < "${srcdir}/config-${CARCH}.patch"

  msg2 'Makefile'
  # add pkgrel to extraversion
  sed -ri "s|^(EXTRAVERSION =)(.*)|\1 \2-${pkgrel}|" Makefile

  # don't run depmod on 'make install'. We'll do this ourselves in packaging
  sed -i '2iexit 0' scripts/depmod.sh
  
  if [[ $CARCH == 'armv7h' ]]; then
    msg2 'Preparing firmware'
    mkdir firmware/brcm
    cp ../brcmfmac43430-sdio.{bin,txt} firmware/brcm
  fi
}

build() {
  cd "${srcdir}/${_srcname}"

  # get kernel version
  make prepare

  # load configuration
  # Configure the kernel. Replace the line below with one of your choice.
  #make menuconfig # CLI menu for configuration
  #make nconfig # new CLI menu for configuration
  #make xconfig # X-based configuration
  #make oldconfig # using old config from previous kernel version
  #make bcmrpi_defconfig # using RPi defconfig
  # ... or manually edit .config

  # Copy back our configuration (use with new kernel version)
  #cp ./.config ../${pkgver}.config

  ####################
  # stop here
  # this is useful to configure the kernel
  #msg "Stopping build"
  #return 1
  ####################

  #yes "" | make config

  msg "Building!"
  make ${MAKEFLAGS} zImage modules dtbs
}

_package() {
  pkgdesc="The Linux Kernel and modules - ${_desc}"
  depends=('coreutils' 'linux-firmware' 'module-init-tools>=3.16')
  optdepends=('crda: to set the correct wireless channels of your country')
  provides=('kernel26' "linux=${pkgver}" 'aufs_friendly')
  conflicts=('kernel26' 'linux')
  install=${pkgname}.install
  backup=('boot/config.txt' 'boot/cmdline.txt' 'etc/modules-load.d/raspberrypi.conf')
  replaces=('linux-raspberrypi-latest','linux-raspberrypi')

  cd "${srcdir}/${_srcname}"

  KARCH=arm

  # get kernel version
  _kernver="$(make kernelrelease)"

  mkdir -p "${pkgdir}"/{lib/modules,lib/firmware,etc/modprobe.d,etc/modules-load.d}
  make INSTALL_MOD_PATH="${pkgdir}" modules_install
  make INSTALL_DTBS_PATH="${pkgdir}/boot" dtbs_install

  [[ $CARCH == "armv6h" ]] && perl scripts/mkknlimg --dtok arch/$KARCH/boot/zImage "${pkgdir}/boot/kernel.img"
  [[ $CARCH == "armv7h" ]] && perl scripts/mkknlimg --dtok arch/$KARCH/boot/zImage "${pkgdir}/boot/kernel7.img"
  cp arch/$KARCH/boot/dts/overlays/README "${pkgdir}/boot/overlays"

  # set correct depmod command for install
  sed \
    -e  "s/KERNEL_VERSION=.*/KERNEL_VERSION=${_kernver}/g" \
    -i "${startdir}/${pkgname}.install"

  # remove build and source links
  rm -f "${pkgdir}"/lib/modules/${_kernver}/{source,build}
  # remove the firmware
  rm -rf "${pkgdir}/lib/firmware"

  # xz all modules to save space
  _ncores="$(getconf _NPROCESSORS_ONLN)"
  find "${pkgdir}" -name '*.ko' |xargs -P ${_ncores} -n 1 xz -C crc32 -6e

  # make room for external modules
  ln -s "../extramodules-${pkgver}${_kernelname}" "${pkgdir}/lib/modules/${_kernver}/extramodules"
  # add real version for building modules and running depmod from post_install/upgrade
  mkdir -p "${pkgdir}/lib/modules/extramodules-${pkgver}${_kernelname}"
  echo "${_kernver}" > "${pkgdir}/lib/modules/extramodules-${pkgver}${_kernelname}/version"

  # Now we call depmod...
  depmod -b "$pkgdir" -F System.map "$_kernver"

  # move module tree /lib -> /usr/lib
  mkdir -p "${pkgdir}/usr"
  mv "$pkgdir/lib" "$pkgdir/usr"

  # install boot files
  install -m644 ../config.txt ../cmdline.txt "${pkgdir}/boot"
  
  # install modprobe.d files
  install -m644 ../cirrus.conf "${pkgdir}/etc/modprobe.d"

  # replace /etc/modules-load.d/raspberry.conf
  install -m644 ../raspberrypi.conf "${pkgdir}/etc/modules-load.d"
}

_package-headers() {
  pkgdesc="Header files and scripts for building modules for linux kernel - ${_desc}"
  provides=("linux-headers=${pkgver}")
  conflicts=('linux-headers')
  replaces=('linux-raspberrypi-latest-headers','linux-raspberrypi-headers')

  install -dm755 "${pkgdir}/usr/lib/modules/${_kernver}"

  cd "${srcdir}/${_srcname}"
  install -D -m644 Makefile \
    "${pkgdir}/usr/lib/modules/${_kernver}/build/Makefile"
  install -D -m644 kernel/Makefile \
    "${pkgdir}/usr/lib/modules/${_kernver}/build/kernel/Makefile"
  install -D -m644 .config \
    "${pkgdir}/usr/lib/modules/${_kernver}/build/.config"

  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/include"

  for i in acpi asm-generic config crypto drm generated keys linux math-emu \
    media net pcmcia scsi sound trace uapi video xen; do
    cp -a include/${i} "${pkgdir}/usr/lib/modules/${_kernver}/build/include/"
  done

  [[ $CARCH == "armv6h" ]] && MACH="mach-bcm2708"
  [[ $CARCH == "armv7h" ]] && MACH="mach-bcm2709"

  # copy arch includes for external modules
  mkdir -p ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH
  cp -a arch/$KARCH/include ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH/
  mkdir -p ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH/$MACH
  cp -a arch/$KARCH/$MACH/include ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH/$MACH/

  # copy files necessary for later builds, like nvidia and vmware
  cp Module.symvers "${pkgdir}/usr/lib/modules/${_kernver}/build"
  cp -a scripts "${pkgdir}/usr/lib/modules/${_kernver}/build"

  # fix permissions on scripts dir
  chmod og-w -R "${pkgdir}/usr/lib/modules/${_kernver}/build/scripts"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/.tmp_versions"

  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/arch/${KARCH}/kernel"

  cp arch/${KARCH}/Makefile "${pkgdir}/usr/lib/modules/${_kernver}/build/arch/${KARCH}/"

  cp arch/${KARCH}/kernel/asm-offsets.s "${pkgdir}/usr/lib/modules/${_kernver}/build/arch/${KARCH}/kernel/"

  # add docbook makefile
  install -D -m644 Documentation/DocBook/Makefile \
    "${pkgdir}/usr/lib/modules/${_kernver}/build/Documentation/DocBook/Makefile"

  # add dm headers
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/md"
  cp drivers/md/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/md"

  # add inotify.h
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/include/linux"
  cp include/linux/inotify.h "${pkgdir}/usr/lib/modules/${_kernver}/build/include/linux/"

  # add wireless headers
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/net/mac80211/"
  cp net/mac80211/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/net/mac80211/"

  # add dvb headers for external modules
  # in reference to:
  # http://bugs.archlinux.org/task/9912
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-core"
  cp drivers/media/dvb-core/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-core/"
  # and...
  # http://bugs.archlinux.org/task/11194
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/include/config/dvb/"
  cp include/config/dvb/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/include/config/dvb/"

  # add dvb headers for http://mcentral.de/hg/~mrec/em28xx-new
  # in reference to:
  # http://bugs.archlinux.org/task/13146
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-frontends/"
  cp drivers/media/dvb-frontends/lgdt330x.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-frontends/"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/i2c/"
  cp drivers/media/i2c/msp3400-driver.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/i2c/"

  # add dvb headers
  # in reference to:
  # http://bugs.archlinux.org/task/20402
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/usb/dvb-usb"
  cp drivers/media/usb/dvb-usb/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/usb/dvb-usb/"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-frontends"
  cp drivers/media/dvb-frontends/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-frontends/"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/tuners"
  cp drivers/media/tuners/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/tuners/"

  # add xfs and shmem for aufs building
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/fs/xfs"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/mm"

  # copy in Kconfig files
  for i in $(find . -name "Kconfig*"); do
    mkdir -p "${pkgdir}"/usr/lib/modules/${_kernver}/build/`echo ${i} | sed 's|/Kconfig.*||'`
    cp ${i} "${pkgdir}/usr/lib/modules/${_kernver}/build/${i}"
  done

  chown -R root.root "${pkgdir}/usr/lib/modules/${_kernver}/build"
  find "${pkgdir}/usr/lib/modules/${_kernver}/build" -type d -exec chmod 755 {} \;

  # strip scripts directory
  find "${pkgdir}/usr/lib/modules/${_kernver}/build/scripts" -type f -perm -u+w 2>/dev/null | while read binary ; do
    case "$(file -bi "${binary}")" in
      *application/x-sharedlib*) # Libraries (.so)
        /usr/bin/strip ${STRIP_SHARED} "${binary}";;
      *application/x-archive*) # Libraries (.a)
        /usr/bin/strip ${STRIP_STATIC} "${binary}";;
      *application/x-executable*) # Binaries
        /usr/bin/strip ${STRIP_BINARIES} "${binary}";;
    esac
  done

  # remove unneeded architectures
  rm -rf "${pkgdir}"/usr/lib/modules/${_kernver}/build/arch/{alpha,arc,arm26,arm64,avr32,blackfin,c6x,cris,frv,h8300,hexagon,ia64,m32r,m68k,m68knommu,metag,mips,microblaze,mn10300,openrisc,parisc,powerpc,ppc,s390,score,sh,sh64,sparc,sparc64,tile,unicore32,um,v850,x86,xtensa}
}

pkgname=("${pkgbase}" "${pkgbase}-headers")
for _p in ${pkgname[@]}; do
  eval "package_${_p}() {
    _package${_p#${pkgbase}}
  }"
done
