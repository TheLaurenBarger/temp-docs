We switched our ISP and had a fun week or so without internet. To save ourselves from the unknown horrors of experiencing the universe for more than a few hours without the relief of a screen, we connected the NUC external hard drive to the Xbox and watched anime. Of course, Xbox is Microsoft. And Microsoft does not play nicely with Linux.

{{< toc >}}

Symptoms.
=========

The HDD connected to your Linux machine is mounted as a read-only file system.

1. Services interacting with the device will report read only file system errors, eg, Deluge logs:
    
    ```java
    chown: changing ownership of '/downloads/complete/sonarr': Read-only file system
    chown: changing ownership of '/downloads/complete': Read-only file system
    chown: changing ownership of '/downloads/incomplete/[SubsPlease] Kingdom S4 - 09 (720p) [2DD3756A].mkv': Read-only file system
    chown: changing ownership of '/downloads/incomplete/[SubsPlease] One Piece - 1023 (720p) [0B854FE9].mkv': Read-only file system
    chown: changing ownership of '/downloads/incomplete': Read-only file system
    chown: changing ownership of '/downloads': Read-only file system
    ```
2. Unmounting and remounting the drive returns the following error:
    
    ```java
    badger@nuc01:~$ sudo mount /dev/sdb1 /mnt/media/
    The disk contains an unclean file system (0, 0).
    Metadata kept in Windows cache, refused to mount.
    Falling back to read-only mount because the NTFS partition is in an
    unsafe state. Please resume and shutdown Windows fully (no hibernation
    or fast restarting.)
    Could not mount read-write, trying read-only
    ```
    

Cause.
======

The HDD is an NTFS formatted disk. NTFS is a Microsoft file system. The error here is being caused by a the HDD not being properly ejected from a Microsoft OS. This can either be caused by a Windows machine hibernating instead of shutting down, or removing the hard drive without properly disconnecting it.

The result is that the Windows system is saved in a hibernated state on the HDD. This is saved in a file called hiberfil.sys which tells any other OS that Windows is hibernated on this file system and is intending to be resumed. Any other OS which connects to this drive will mount it as a read-only file system since Windows has reported that the drive is still in use by a Windows OS.

How to fix.
===========

The fix is simple is thankfully simple. The drive just needs to be properly ejected from a Windows machine and then remounted on Linux:

1. Unmount the drive from your Linux OS
2. Connect the drive to a Windows machine.
3. If prompted, choose to repair the drive.
4. Properly eject the drive using the drawer icon.
5. Reconnect the drive to your Linux machine
    

Unmounting and remounting HDD on Linux.
---------------------------------------

To list the available disks connected to your linux system use the following command:

```java
lsblk
```

```java
badger@nuc01:/opt$ lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0    62M  1 loop /snap/core20/1611
loop1                       7:1    0    47M  1 loop /snap/snapd/16292
loop2                       7:2    0  67.8M  1 loop /snap/lxd/22753
loop3                       7:3    0    48M  1 loop /snap/snapd/16778
loop4                       7:4    0  63.2M  1 loop /snap/core20/1623
loop5                       7:5    0  67.2M  1 loop /snap/lxd/21835
sda                         8:0    0 223.6G  0 disk 
├─sda1                      8:1    0   1.1G  0 part /boot/efi
├─sda2                      8:2    0     2G  0 part /boot
└─sda3                      8:3    0 220.5G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   100G  0 lvm  /
sdb                         8:16   0   1.8T  0 disk 
└─sdb1                      8:17   0   1.8T  0 part /mnt/media
```

![](images/icons/grey_arrow_down.png)Click here to expand...

```java
lsblk -f
```

```java
badger@nuc01:/opt$ lsblk -f
NAME                      FSTYPE      LABEL                      UUID                                   FSAVAIL FSUSE% MOUNTPOINT
loop0                     squashfs                                                                            0   100% /snap/core20/1611
loop1                     squashfs                                                                            0   100% /snap/snapd/16292
loop2                     squashfs                                                                            0   100% /snap/lxd/22753
loop3                     squashfs                                                                            0   100% /snap/snapd/16778
loop4                     squashfs                                                                            0   100% /snap/core20/1623
loop5                     squashfs                                                                            0   100% /snap/lxd/21835
sda                                                                                                                    
├─sda1                    vfat                                   DAED-1AEE                                   1G     0% /boot/efi
├─sda2                    ext4                                   3d7ecb9d-99cc-469d-a547-32b4a5d89322      1.6G    11% /boot
└─sda3                    LVM2_member                            BS73dt-byR6-bfrt-X5qT-N6M5-ANUV-jT42Re                
  └─ubuntu--vg-ubuntu--lv ext4                                   39d0e7aa-dd0d-4d8b-b1fe-d0536d48da4f     75.7G    18% /
sdb                                                                                                                    
└─sdb1                    ntfs        Dwayne the Rock Hard Drive 8A32AB1232AB01F5
```

The last entry here shows that the drive sdb/sdb1 is currently mounted on the directory /mnt/media.

To unmount the drive use the following command:

```java
sudo umount /mnt/media
```

```java
badger@nuc01:/opt$ sudo umount /mnt/media
badger@nuc01:/opt$ lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0    62M  1 loop /snap/core20/1611
loop1                       7:1    0    47M  1 loop /snap/snapd/16292
loop2                       7:2    0  67.8M  1 loop /snap/lxd/22753
loop3                       7:3    0    48M  1 loop /snap/snapd/16778
loop4                       7:4    0  63.2M  1 loop /snap/core20/1623
loop5                       7:5    0  67.2M  1 loop /snap/lxd/21835
sda                         8:0    0 223.6G  0 disk 
├─sda1                      8:1    0   1.1G  0 part /boot/efi
├─sda2                      8:2    0     2G  0 part /boot
└─sda3                      8:3    0 220.5G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   100G  0 lvm  /
sdb                         8:16   0   1.8T  0 disk 
└─sdb1                      8:17   0   1.8T  0 part
```

At this point the drive is no longer mounted on the Linux OS. You can remove the drive and connect it to a Windows machine, to repair and properly eject the drive.

To remount the device, connect it to the Linux machine and confirm the drive path:

```java
badger@nuc01:/opt$ lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0    62M  1 loop /snap/core20/1611
loop1                       7:1    0    47M  1 loop /snap/snapd/16292
loop2                       7:2    0  67.8M  1 loop /snap/lxd/22753
loop3                       7:3    0    48M  1 loop /snap/snapd/16778
loop4                       7:4    0  63.2M  1 loop /snap/core20/1623
loop5                       7:5    0  67.2M  1 loop /snap/lxd/21835
sda                         8:0    0 223.6G  0 disk 
├─sda1                      8:1    0   1.1G  0 part /boot/efi
├─sda2                      8:2    0     2G  0 part /boot
└─sda3                      8:3    0 220.5G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   100G  0 lvm  /
sdb                         8:16   0   1.8T  0 disk 
└─sdb1                      8:17   0   1.8T  0 part
```

Once you’ve confirmed the drive path (sdb/sdb1), mount the drive to the desired mount folder (/mnt/media) using the following command:

```java
sudo mount /dev/sdb1 /mnt/media/
```

## Conclusion

There you have it! An interesting problem caused by the nuances of having different operating systems interacting with the same storage. Thankfully an easy fix as well!

## References

1. [https://www.tomshardware.com/how-to/mount-drives-linux](https://www.tomshardware.com/how-to/mount-drives-linux)
2. [https://askubuntu.com/questions/1200201/external-hard-drive-set-to-read-only-on-ubuntu-18-04](https://askubuntu.com/questions/1200201/external-hard-drive-set-to-read-only-on-ubuntu-18-04)
3. [https://www.technipages.com/windows-10-goes-into-hibernation-when-shut-down](https://www.technipages.com/windows-10-goes-into-hibernation-when-shut-down)
4. [https://www.atera.com/blog/ntfs-vs-fat-what-do-i-need-to-know-as-an-it-service-provider/](https://www.atera.com/blog/ntfs-vs-fat-what-do-i-need-to-know-as-an-it-service-provider/)
5. [https://askubuntu.com/questions/145902/unable-to-mount-windows-ntfs-filesystem-due-to-hibernation](https://askubuntu.com/questions/145902/unable-to-mount-windows-ntfs-filesystem-due-to-hibernation)