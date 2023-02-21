Xilinx Tools
===

To program the FPGAs opr the ARTY Z7 and then build a Linux Image, you need
- Vivado
- Vitis
- Petalinux

These are all available through the Vivado Installer:

  https://www.xilinx.com/support/download.html

Download it and run it from the command line. Choose Vitis, which will install both Vivado and Vitis. Once that installation is done, rerun the installer and choose Petalinux. 

Some gotchas while installing on Ubuntu that I remember are:
- The `/tools` directory needs to exist and be owned by whatever use is doing the installation.
- Installing Petalinux failed at first, but told my why: I needed to `apt install` a few libraries, including 32 bit libraries, ncurses, and a couple of others. Re-running the installer after that worked. 