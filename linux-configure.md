Boot up and Log in
---

Put the SD Card into the ARTY. Connect power and then a USB cable from a host computer. On linux, you can use GtkTerm to connect to /dev/ttyUSB1 at 1140200 baud. The user name is `Petalinux` and you'll have to change your password straight away.

Check the logs 
---

```bash
cat /var/log/dmesg
```

It says some good things:

```bash
[    0.254145] Advanced Linux Sound Architecture Driver Initialized.
[    1.372993] xlnx_i2s 43c10000.i2s_receiver: xlnx_i2s_capture DAI registered
[    1.373176] mmc0: SDHCI controller on e0100000.mmc [e0100000.mmc] using ADMA
[    1.378830] xlnx_i2s 43c20000.i2s_transmitter: xlnx_i2s_playback DAI registered
[    1.391933] xlnx_formatter_pcm 43c00000.audio_formatter: sound card device will use DAI link: i2s_transmitter
[    1.401713] xlnx_formatter_pcm 43c00000.audio_formatter: sound card device will use DAI link: i2s_receiver
[    1.410319] xlnx_formatter_pcm 43c00000.audio_formatter: pcm platform device registered
[    1.421454] xlnx_snd_card xlnx_snd_card.0.auto: xlnx-i2s-snd-card-0 registered
[    1.473856] ALSA device list:
[    1.475559]   #0: xlnx-i2s-snd-card-0
```

And `aplay -l` shows:

```
**** List of PLAYBACK Hardware Devices ****
card 0: xlnxi2ssndcard0 [xlnx-i2s-snd-card-0], device 0: (null) snd-soc-dummy-dai-0 []
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

And `arecord -l` shows

```
**** List of CAPTURE Hardware Devices ****
card 0: xlnxi2ssndcard0 [xlnx-i2s-snd-card-0], device 1: (null) snd-soc-dummy-dai-1 []
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

This all seems good. But does it work?
