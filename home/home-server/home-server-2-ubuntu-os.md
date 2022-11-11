# Home Server, Part 2: Ubuntu OS

In the previous article, I set up the hardware for our home server on an Intel NUC. Now that my hardware is prepped, this article will go through the setup for the Ubuntu Server OS which we’ll use for all our hosted services.

<!-- Table of Contents -->

## Install Ubuntu Server

My home server OS of choice is Ubuntu Server. This means we won’t have a frontend and will need to configure everything via terminal. At the time of writing, the most recently released version is [Ubuntu Server 22.04 LTS](https://discourse.ubuntu.com/t/jammy-jellyfish-release-notes/24668).

### Create a bootable drive

In order to install Ubuntu Server onto our NUC we will need to create a bootable drive with the Ubuntu Server ISO.

1. Our first step is to download the ISO file for Ubuntu Server. [https://ubuntu.com/download/server](https://ubuntu.com/download/server)
2. Download your favourite USB flashing software. I typically use Balena Etcher [https://www.balena.io/etcher/](https://www.balena.io/etcher/)
3. Open up your flashing software, select the Ubuntu Server ISO image and select your USB Drive and Flash.

### Installing Ubuntu Server

Our next step is to install Ubuntu onto our NUC. I followed Ubuntu’s standard installation instructions for the majority of this step.

1. Insert your bootable USB drive into your NUC and access the BIOS menu.
    1. If you are not automatically directed to the Ubuntu installation media, the Intel boot menu button is F10. Restart the NUC again and hold down F2.
    2. If you want to force the NUC to open the boot menu, Press and hold the power button for three seconds. You will be presented with the power button menu. Select **Enter Boot Menu**.
2. Complete the installation steps as they appear on your screen. I followed the standard Ubuntu guide for the install [https://ubuntu.com/tutorials/install-ubuntu-server#1-overview](https://ubuntu.com/tutorials/install-ubuntu-server#1-overview).
3. Ensure that you create a user for yourself with a secure password

## Static IP address

Now that Ubuntu Server is installed, I’m going to configure a static IP address for our server so that we always know where it is.

It is possible to configure a static IP address using your routers DHCP settings, but these settings could be erased by a router swap or ISP change, so we’ll be configuring the static IP via Ubuntu Server.

1. **Identify ethernet interface to set the static IP for.**  
    Use the following command to list the available ethernet interfaces:

    ```bash
    ip link
    ```

    This should list the available network interfaces, and should look something like this:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
        link/ether 1c:69:7a:aa:53:72 brd ff:ff:ff:ff:ff:ff
    3: wlo2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ether 14:85:7f:84:de:1f brd ff:ff:ff:ff:ff:ff
    ```

    The name of the ethernet interface in the above list is **eno1**.  
2. **Navigate to the Ubuntu Netplan directory.**  
    Navigate to `/etc/netplan`.

    ```bash
    cd /etc/netplan
    ```

    In this directory there will most likely be multiple different configuration files for the different interfaces available.

    ```bash
    badger@nuc01:/etc/netplan$ ll
    total 16
    drwxr-xr-x   2 root root 4096 May 17 07:07 ./
    drwxr-xr-x 104 root root 4096 Oct 18 06:24 ../
    -rw-------   1 root root   74 May 17 05:48 00-installer-config-wifi.yaml
    -rw-r--r--   1 root root  256 May 17 07:07 00-installer-config.yaml
    ```

    We are going to update the `00-installer-config.yaml` file to set the static IP for our ethernet interface.  
3. **Understanding the Netplan configuration file.**  
    Open the Netplan configuration file to review the contents:

    ```bash
    cat nano 00-installer-config.yaml
    ```

    The file contents should look something like this:

    ```yaml
    network:
      version: 2
      renderer: networkd
      ethernets:
        eno1:
          dhcp4: yes
    ```

    This is the standard format for Netplan configuration files. The configuration starts with a `network` tag followed by the Netplan version and then the device types. The device type can be `ethernets`, `bonds`, `bridges` or `vlans`. This config has also set the `renderer` to the default `networkd` which comes with Ubuntu Server.  

    The configuration also specifies that dhcp4 is enabled for our ethernet interface. This means that the IP address of our ethernet interface is being selected dynamically using the DHCP server. This is the configuration we need to change.  
4. **Configuring a static IP address using the Netplan configuration file.**  
    Open the Netplan configuration file with your text editor of choice:

    ```bash
    sudo nano 00-installer-config.yaml
    ```

    The config that we will set works as follows:  

    * `dhcp4: no`: Disable DHCP IP resolution  
    * `addresses`: The static IPv4 or IPv6 address(s) that will be assigned to the network interface. (192.168.0.10)  
    * `gateway4`: The gateway to use for our selected static IP addresses (Default Gateway: 192.168.0.1)  
    * `nameservers`: The DNS addresses to be used. (Google DNS Servers: 8.8.8.8, 8.8.4.4)  

    My configuration is as follows:

    ```yaml
    network:
      version: 2
      renderer: networkd
      ethernets:
        eno1:
          dhcp4: no
          addresses: [192.168.0.10/24]
          gateway4: 192.168.0.1
          nameservers:
              addresses: [8.8.8.8, 8.8.4.4]
    ```

5. **Save the file and apply changes.**  
    Run the following command to apply the network changes that you’ve made:

    ```bash
    sudo netplan apply
    ```

6. **Verify changes.**  
    Run the following command to review your changes:

    ```bash
    ip addr show dev eno1
    ```

    In the command output I can see that the assigned address is 192.168.0.10:

    ```bash
    2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        link/ether 1c:69:7a:aa:53:72 brd ff:ff:ff:ff:ff:ff
        inet 192.168.0.10/24 brd 192.168.0.255 scope global eno1
           valid_lft forever preferred_lft forever
        inet6 fe80::1e69:7aff:feaa:5372/64 scope link
           valid_lft forever preferred_lft forever
    ```

## Secondary Storage

Our server is going to need some secondary storage to allow us to host a media server. I’m going to just connect a single terabyte hard drive to the NUC and setup automatic volume mounts so that it’s always connected.

1. **Find the device UUID**  
    Once the HDD is connected run the following command to list the connected partitions on the Linux system:

    ```bash
    sudo blkid
    ```

    In the list you should be able to find your HDD details, they should looks something like this:

    ```bash
    badger@nuc01:~$ sudo blkid
    /dev/sda1: UUID="DAED-1AEE" TYPE="vfat" PARTUUID="74c0e0f1-f39b-4a5c-9c7d-5fd8f027235f"
    /dev/sda2: UUID="3d7ecb9d-99cc-469d-a547-32b4a5d89322" TYPE="ext4" PARTUUID="74d53c9c-a465-4b19-ae3e-1b52b07fa2c6"
    /dev/sda3: UUID="BS73dt-byR6-bfrt-X5qT-N6M5-ANUV-jT42Re" TYPE="LVM2_member" PARTUUID="f4459125-cba4-49a4-85c7-bba8fcfdfe87"
    /dev/mapper/ubuntu--vg-ubuntu--lv: UUID="39d0e7aa-dd0d-4d8b-b1fe-d0536d48da4f" TYPE="ext4"
    /dev/sdb1: LABEL="Dwayne the Rock Hard Drive" UUID="8A32AB1232AB01F5" TYPE="ntfs" PARTUUID="85cef983-01"
    ```

    We can see that the device UUID is `8A32AB1232AB01F5` and the device type is `ntfs`. Now that we know the partition name, we can mount the partition on our Ubuntu server.  
2. **Create a mount point**  
    Ubuntu will require a folder to mount the HDD on. We will create the folder /mnt/media for our HDD mount point:

    ```bash
    sudo mkdir /mnt/media
    ```

3. **Automatically mount partition.**  
    Open the file /etc/fstab with your selected text editor.

    ```bash
    sudo nano /etc/fstab
    ```

    Add the following line to the end of the file and save:

    ```bash
    UUID=8A32AB1232AB01F5 /mnt/media ntfs defaults 0 0
    ```

4. **Restart your device**  
    Since the device is now set to automatically connect on start up, a simple restart of the NUC will mount the HDD to your selected partition.

## Conclusion

These are the preliminary steps I followed for the setup of Ubuntu for the home server. Our next steps will include setting up Docker and the beginnings of our home server.

## References

1. [https://www.homeautomationguy.io/home-assistant-tips/installing-docker-home-assistant-and-portainer-on-ubuntu-linux/](https://www.homeautomationguy.io/home-assistant-tips/installing-docker-home-assistant-and-portainer-on-ubuntu-linux/)
2. [https://ubuntu.com/tutorials/install-ubuntu-server#1-overview](https://ubuntu.com/tutorials/install-ubuntu-server#1-overview)
3. [https://linuxize.com/post/how-to-configure-static-ip-address-on-ubuntu-20-04/](https://linuxize.com/post/how-to-configure-static-ip-address-on-ubuntu-20-04/)
4. [https://askubuntu.com/questions/125257/how-do-i-add-an-additional-hard-drive](https://askubuntu.com/questions/125257/how-do-i-add-an-additional-hard-drive)
5. [https://askubuntu.com/questions/46588/how-to-automount-ntfs-partitions](https://askubuntu.com/questions/46588/how-to-automount-ntfs-partitions)
6. [https://linuxhandbook.com/things-to-do-after-installing-linux-server/](https://linuxhandbook.com/things-to-do-after-installing-linux-server/)
