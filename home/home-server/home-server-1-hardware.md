# Home Server, Part 1: Hardware

The value of a dedicated home server is unimaginable. My little NUC has paid off for itself in usefulness and sheer joy a hundred times over and I’ve had it for six months. This article is the first of many to chronicle the complete setup process for my home server, starting with, the hardware.

<!-- Table of Contents -->

## Hardware

I went with an Intel NUC for my home server setup. It’s more powerful than a Raspberry PI without being unreasonably expensive. The total components list added up to R4,352 when I bought it. I purchased the nuc itself, some RAM and an SSD for the setup.

1. [Intel NUC NUC7CJYHN Intel Celeron 2.0GHz Dual Core](https://www.wootware.co.za/intel-boxnuc7cjyhn2-nuc-nuc7cjyhn-intel-celeron-2-0ghz-dual-core-intel-uhd-600-graphics-2x-ddr4-so-dimm-mini-pc-ram-hdd-os-not-included.html) - R3,474.00
2. [G.Skill Ripjaws 4GB DDR4](https://www.wootware.co.za/g-skill-f4-2400c16s-4grs-ripjaws-so-dimm-4gb-1x4gb-ddr4-2400mhz-cl16-1-2v-notebook-memory.html) - R329.00
3. [Kingston 240GB SSD](https://www.takealot.com/kingston-240gb-a400-sata3-2-5-ssd/PLID46618461) - R549
4. A minimum 4GB flash drive

Putting everything together was fairly intuitive, especially since I’ve been putting monster PCs together. A little NUC wasn’t too much of a challenge.

> 📝 The Intel notes on getting started with the NUC can be found here: [Intel NUC User Guide](https://www.intel.com/content/dam/support/us/en/documents/intel-nuc/NUC7xJYHN_UserGuide.pdf).

<!-- needs something here -->

> ⚠️ When I first got the NUC, I installed Ubuntu Desktop and had every intention of hooking up the NUC via HDMI to my TV. Ran into an issue with the sound drivers. This is apparently resolved with a BIOS update but I’ll admit… I’m far too lazy to fix this now.

## NUC BIOS settings

There are a few settings we should configure in the NUC BIOS menu.

1. Restart the NUC while holding F2.
2. If you want to force the NUC to open the BIOS menu, Press and hold the power button for three seconds. You will be presented with the power button menu. Select **Enter BIOS Menu**.

At this point you’ll see a summary of your device’s properties. There is only one setting we want to enable in our BIOS menu, thanks to the general state of Eskom (this badger is South African). We want to ensure that the NUC will start up again after any power failure.

1. Open the **Power** menu on the BIOS menu.
2. Locate the **After Power Failure** option and set it to **Power On**.

## Conclusion

And that should be it! Hardware is ready to become a fully operational home server. As a next step, we’ll install the OS and start configuring the functionality of our home server.

<!-- Next Article -->

## References

1. [Intel NUC User Guide](https://www.intel.com/content/dam/support/us/en/documents/intel-nuc/NUC7xJYHN_UserGuide.pdf)
