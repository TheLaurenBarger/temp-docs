# 3D Printing, Part 1: Octoprint Setup

What better use for a Raspberry PI than turning the already epic 3D printer into yet another smart and remotely available device! Here follows the how to.

{{< toc >}}

## Flash OctoPrint onto an SD Card

OctoPrint is now an OS fully supported by Raspberry Pi and is available on their standard Raspberry Pi Imager. Since OctoPrint has done a better job than I can, behold:

[https://octoprint.org/download/](https://octoprint.org/download/)

## Prusa Mini Printer Configuration

Once you’ve finished the flash and connection, set up the printer configuration as follows:

[https://help.prusa3d.com/article/octoprint-configuration-and-install\_2182#launching-and-configuring-octoprint](https://help.prusa3d.com/article/octoprint-configuration-and-install_2182#launching-and-configuring-octoprint)

| **Setting**       | **Prusa Mini Configuration** |
|---------------------|------------------------------|
| Form Factor         | Rectangular                  |
| Origin              | Lower left                   |
| Heated Bed          | Yes                          |
| Width               | 180mm                        |
| Depth               | 180mm                        |
| Height              | 180mm                        |
| Custom Bounding Box | X: 0/180;Y: -3/180;Z: 0/180  |

## OctoPrint static IP

There are two ways to do this. Either by updating the OctoPrint config file, or by setting a DHCP rule on your router. Since I’ve been excited for any reason to click around the RouterOS, I went with option 2, however:

### OctoPrint Config Entry

asdf

### DHCP Entry

Log onto the RouterOS admin console and proceed with the following steps:

1. Navigate to **IP** → **DHCP Server** → **Leases**.
2. Locate the IP address of the Raspberry Pi hosting OctoPrint.
3. Select **Make Static**.

### OctoLapse

[https://all3dp.com/2/octoprint-timelapse-how-to-find-the-best-octolapse-settings/](https://all3dp.com/2/octoprint-timelapse-how-to-find-the-best-octolapse-settings/)

## Conclusion

And presto! Next up, exposing the pi to the outside world so that you can watch prints from everywhere.

## References

1. [https://octoprint.org/download/](https://octoprint.org/download/)
2. [https://github.com/guysoft/OctoPi#how-to-use-it](https://github.com/guysoft/OctoPi#how-to-use-it)
3. [https://help.prusa3d.com/article/octoprint-configuration-and-install\_2182#launching-and-configuring-octoprint](https://help.prusa3d.com/article/octoprint-configuration-and-install_2182#launching-and-configuring-octoprint)
4. [https://forum.mikrotik.com/viewtopic.php?t=147037](https://forum.mikrotik.com/viewtopic.php?t=147037)
5. [https://all3dp.com/2/octoprint-timelapse-how-to-find-the-best-octolapse-settings/](https://all3dp.com/2/octoprint-timelapse-how-to-find-the-best-octolapse-settings/)
