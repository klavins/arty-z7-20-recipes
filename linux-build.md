Building a Kernel for the ARTY Z7-20
===

Source the Petalinux Settings
---

```bash
source /tools/Xilinx/PetaLinux/2022.2/tool/settings.sh
```

Initialize an OS Project
---

Change to the project directory for the Vivado hardware. For me, that's `~cham4`. There should be a `.xsa` file in this directory. Then do
```bash
petalinux-create --type project --template zynq --name cham4_os
cd cham4_os
```

Configure the Hardware
---

```bash
petalinux-config --get-hw-description ../
```
This does not take too long and eventually starts a configuration GUI. Do:
- Image Packaging Configuration -> Root filesystem type = EXT4
- Image Packaging Configuration -> Copy final images to tftpboot [DISABLE]

Configure the Kernel
---

```bash
petalinux-config -c kernel
```

This takes a while, but eventually starts a GUI. Add the device drivers for needed for sound under the `Device Drivers` heading. 
- Device Drivers 
- -> Sound card support
- -> Advanced Sound Architecture
- -> ALSA for SoC audio support
    - Audio support for the Xilinx I2S
    - Audio support for the Xilinx audio formatter

All the other device drivers we need, like I2C and GPIOs should be included already. 

Configure the root Filesystem
---

```bash
petalinux-config -c rootfs
```

Under `Petalinux Package Groups` enable
- packagegroup-petalinux > [*] packagegroup-petalinux
- packagegroup-petalinux-audio > [*] packagegroup-petalinux-audio
- packagegroup-petalinux-networking-stack > [*] packagegroup-petalinux-networking-stack
- packagegroup-petalinux-utils > [*] packagegroup-petalinux-utils

Under `Image Features` enable
- [*] package-management

Configure Device Tree
---

Not sure this needs to be done. But apparently you can add lines to 

> project-spec/meta-user/recipes-bsp/device-tree/files/system-user.dtsi 

I am hoping the .xsa file produces whatever we need here. 

Configure u-boot
---

```bash
petalinux-config -c u-boot
```

Takes a while. No special considerations here.

Build the Project
---

```bash
petalinux-build
```

Takes a while. Go do something else. 

It seems that I need to make sure that the kernel flag

```
CONFIG_SND_SOC_XILINX_PL_SND_CARD=y
```

is set. Otherwise there will be no sound card. So I went back to this and did this:

```bash
nano project-spec/meta-user/recipes-kernel/linux/linux-xlnx/user_2023-02-21-01-21-00.cfg 
petalinux-build -x mrproper
petalinux-build
```

You can probably just set that flag before building in the first place. 

Also, there might be a place where you configure this in the menu thingys. 

Package the Project
---

```bash
petalinux-package --boot --fsbl ./images/linux/zynq_fsbl.elf --fpga ./images/linux/system.bit --u-boot
```

Copy Everything to an SD Card
---

The SD Card should have a 2 Gb FAT32 partition called `boot` and the rest of the card should be an EXT4 parition called `root`. To format an SD card, use Gparted. 

Erase any old files with:

```bash
sudo rm -rf /media/eric/boot/*
sudo rm -rf /media/eric/root/*
```

And then copy files with:

```bash
sudo cp images/linux/BOOT.BIN images/linux/image.ub images/linux/boot.scr images/linux/system.dtb /media/eric/boot
sudo tar xf images/linux/rootfs.tar.gz -C /media/eric/root/
sync
```

The last command is important (as is everything else, but this one is easy to forget). 

TODO
===

Configure the sound card driver:

- https://yuhei1-horibe.medium.com/linux-device-driver-for-zedboard-audio-2-2-376888ffb64c
- https://yuhei1-horibe.medium.com/linux-device-driver-for-zedboard-audio-7e2d1efc2941





