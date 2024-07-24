# Altera Blaster clone FT245+CPLD improved firmware

In the Altera USB Blaster world, the clones that use an FTDI FT245 chip and a CPLD are widely known as the best design. That's because it is very similar to Altera's original USB Blaster.

I have found a few USB Blaster clones that use this type of setup:

- [Waveshare USB Blaster V2](https://www.waveshare.com/usb-blaster-v2.htm)
- Various USB Blaster products on Amazon and AliExpress that mention FT245+CPLD in the title. Mine says "MODEL KRZV-REV. C"

Both of these clones use an Altera/Intel EPM3064ATC44-10 CPLD. They have different pinouts on their boards though.

I have found that these clones don't work well in Linux. They seem to work fine in Windows for the most part, but they behave erratically in Linux. Occasionally you will get lucky and it will work once, but it fails 90% or more of the time. The JTAG chain debugger in the Altera/Intel software will indicate problems.

## Original design

I found an [open-source implementation of a CPLD bitstream for a USB Blaster](https://ixo-jtag.sourceforge.net/) that interacts with the FT245. It's designed for the EPM7064, but it also works in the EPM3064. I ported this design to run on the Waveshare and generic USB blasters described above, and found that they still suffer from the same problem when running this CPLD design.

## My bug fix

This fork of the original project has a small tweak to the CPLD design. I found that the original design was simultaneously writing all of the output pins and reading the two input pins on the JTAG connector. Delaying the read by a single clock cycle ensures that the CPLD never tries to read back TDO/ASDO at the same time that TCK is being changed.

With this bug fix CPLD bitstream programmed into an affected USB Blaster clone, the problem no longer occurs and it works perfectly. I have noticed occasionally it takes 30 seconds to start working in Linux, but that issue also affects the Terasic USB Blaster so I suspect it may be a problem in general with USB Blasters on Linux and unrelated to the CPLD design.

## Supported devices

- **waveshare.qpf**: project for the Waveshare USB Blaster V2
- **generic_blaster.qpf**: project for generic "FT245+CPLD USB Blaster Download Cable" found on AliExpress (model KRZV-REV. C)

Unfortunately, you need an existing functional USB Blaster in order to program a new bitstream onto these devices, so that limits just how useful this fixed firmware is from a practical standpoint. I'm still sharing it in the hopes that vendors will use it to fix the bug in their products.
