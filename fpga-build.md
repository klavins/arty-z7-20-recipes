Block Design
---

I followed the guide at:

   https://yuhei1-horibe.medium.com/zedboard-audio-hardware-design-b19c3a1bf453

with the following changes and observations.

0. Use ARTY Z7-20 instead of Zedboard. 
0. When customizing the Zynq block, I got a warning about skew rates. 
0. When it says to add one more processor sytem reset for the 24MHz clock, that means take the clk_out2 signal and connect it to slowest_sync_clk and the reset to the ext_reset_in pins.
0. When connecting the I2S TX and RX, the pin name seems to be aud_mrst not aud_mreset
0. To make an external connection to the 24 MHz Clock (clk_out2), you can't do "Make External" because it is already connected to other things. So you do "Make Port", make sure it is set to be an output, rename it, and then connect it to clk_out2. 
0. When it says connect s_axi_aclk for the IIC module, I think it means to clk_out1 (the 48 MHz clock).
0. I also named things more nicely than the default. Although a warning: If you rename a port on a block that is already wired, then it will disconnect it. 
0. Once the DRC worked for the block diagram, I made a backup of the entire project. 

Pin Connections
---

After running synthesis for the first time, you see under I/O Ports that the pinds still need to be assigned. According to Digilent, the PMOD connectors are supposed to be used for I2C and I2S:

  https://digilent.com/reference/_media/reference/pmod/pmod-interface-specification-1_3_1.pdf

In particular, for I2C and a 12 pin PMOD connector, we have:

```
  PMOD Header JB
  Pin 3: SCL                T11
  Pin 4: SDA                T10
  Pin 7: GPIO               V16
  Pin 8: GPIO               W16
```

And for the I2S on a 12 pin connector:

```
  MPOD Header JA
  Pin 1: LRCK               Y18 
  Pin 2: DAC Data (Dout)    Y19
  Pin 3: ADC Data (Din)     Y16
  Pin 4: BCK                Y17
  Pin 9: MCLK               W18
```
There is some conflict here, since pins 3 and 4 can't do the same thing. But there are two PMOD connectors on the ARTY Z7. So I figure: use the first one for I2S and the second one for I2C. The pin mappings can be found in the ARTY Z7-20 constraint file:

  https://github.com/Digilent/digilent-xdc/blob/master/Arty-Z7-20-Master.xdc 

Which I used to fill in the above assignments. All these pins are LVCMOS33. 

Final Synthesis and Implmentation
---

After all these ports are connected, re-run Synthesis. 

Then run Implementation. This produces two critical warnings:

- Invalid clock redefinition on a clock tree. The primary clock design_1_i/Clock/inst/clk_in1 is defined downstream of clock clk_fpga_0 and overrides its insertion delay and/or waveform definition

- A primary clock design_1_i/Clock/inst/clk_in1 is created on an inappropriate internal pin design_1_i/Clock/inst/clk_in1. It is not recommended to create a primary clock on a hierarchical pin when its driver pin has a fanout connected to multiple clock pins

There is a discussion of this here:

  https://support.xilinx.com/s/question/0D52E00006hpjYhSAI/timing4-invalid-clock-redefinition-on-a-clock-tree?language=en_US

There is also a document from Zilinx:

  https://docs.xilinx.com/r/2021.2-English/ug906-vivado-design-analysis/Description?tocId=BjW1_g5w6BmSQKl3XSKVaA

which says to

  > Remove the create_clock constraint on the downstream object and allow the propagation of the upstream clock or create a generated clock referencing the upstream primary clock.

I noticed some chats saying to edit the xdc directly. So I found where the create clock is:

```
  cham4.gen/sources_1/bd/design_1/ip/design_1_clk_wiz_0_1/design_1_clk_wiz_0_1.xdc
```

I closed the Synthesized Design windows. I commented out the create clock command! When I reopen the synthesized design and look at Timing constraints: The create clock is gone for clock wizard. Hooray?

Then I re-ran the Implementation and: No more critical Warnings!

Generate and Save the Bitstream
---

The click generate bistream. When that is done, File -> Export -> Export Hardware. Choose "Include Bistream". I called this file "cham4_hardware.xsa". 

THe next step is to make a Linux Kernel. That is described [here](linux-build.md).