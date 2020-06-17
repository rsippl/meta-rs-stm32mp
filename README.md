## Motivation

STM32MP1 devices like the [STM32MP157C Discovery Kit](https://www.st.com/en/evaluation-tools/stm32mp157c-dk2.html)
are officially supported by STMicroelectronics through the [OpenSTLinux distribution](https://wiki.st.com/stm32mpu/wiki/OpenSTLinux_distribution), 
a set of OpenEmbedded layers.

The trouble starts when you'd like to create a minimum distribution based on newer software. With OpenSTLinux, the
result is usually bloated and outdated. Newer [Yocto releases](https://wiki.yoctoproject.org/wiki/Releases) support
basically everything you want -- unfortunately, using OpenSTLinux with these is basically impossible.

## Solution: the BSP Layer

The solution is to use the essential part of OpenSTLinux concerning hardware support, i.e. the [BSP layer](https://github.com/STMicroelectronics/meta-st-stm32mp)
together with a reference distribution like [Poky](https://www.yoctoproject.org/docs/current/mega-manual/mega-manual.html#reference-embedded-distribution).
This way, you can provide support for newer Yocto releases, you get rid of the OpenSTLinux bloat and you can rely on
the [Yocto documentation](https://www.yoctoproject.org/docs). 

The aim of this layer is to provide missing pieces like machine definitions and WKS files which help you create
flash images.

Currently, the STM32MP157C-DK2 is supported, adding support for [additional devices](https://github.com/STMicroelectronics/meta-st-stm32mp/tree/zeus/conf/machine)
shouldn't be hard.

## Dependencies

    URI: https://github.com/STMicroelectronics/meta-st-stm32mp
    branch: zeus

If you base your distribution on Poky, your `bblayers.conf` could look like this:

    BBLAYERS ?= " \
      /opt/yocto/workspace/layers/poky/meta \
      /opt/yocto/workspace/layers/poky/meta-poky \
      /opt/yocto/workspace/layers/poky/meta-yocto-bsp \
      /opt/yocto/workspace/layers/meta-openembedded/meta-oe \
      /opt/yocto/workspace/layers/meta-openembedded/meta-python \
      /opt/yocto/workspace/layers/meta-st-stm32mp \
      /opt/yocto/workspace/layers/meta-rs-stm32mp \
      "

## Adjustments in local.conf

Set the machine:

    MACHINE = "stm32mp157c-dk2"

Set file system types in order to generate a wic file which can be written to a SD card with Etcher:

    IMAGE_FSTYPES = "ext4 wic"

**TODO**: wic.gz would be desireable, but doesn't seem to work, it causes errors regarding missing ext4 files.

Accept the EULA to enable all features of the BSP layer (e.g. GPU):

    ACCEPT_EULA_stm32mp157c-dk2 = "1"

Adjust the root partition size (in megabytes):

    STM32MP_ROOTFS_FIXED_SIZE = 256
