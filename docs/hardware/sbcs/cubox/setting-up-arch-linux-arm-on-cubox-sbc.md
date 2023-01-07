---
title: 'Setting up Arch Linux ARM on Cubox-i'
tags: ['hardware','sbc','linux','arch linux']
date: 2023-01-04
---
# Setting up Arch Linux ARM on Cubox-i {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Introduction

Unfortunately the Arch Linux ARM image available for the Cubox-i is extremely old.  It ships with kernel 3.14 which has some bugs that prevent systemd networking from functioning correctly.  This complicates the installation process substantially.  This guide will walk you through setting up an up-to-date Arch Linux ARM install for your Cubox-i.

## Micro-SD Card Setup

- [x] Plugin the micro-SD card into your main computer.  It should be detected by your OS and assigned a device path (e.g. `/dev/sdb`).  Make sure to replace `/dev/sdX` with the appropriate device when following the subsequent steps.

```shell
# unmount the sd card if it was auto-mounted
umount /dev/sdX1

# zero out the first few MB of the sd card to remove any previous u-boot environment
dd if=/dev/zero of=/dev/sdX bs=1M count=4

# start fdisk to create a new partition on the sd card
# Type n, and then press ENTER, accepting default values
# Exit by typing w
fdisk /dev/sdX

# create ext4 filesystem
mkfs.ext4 /dev/sdX1

# mount the filesystem
mkdir root-cubox
mount /dev/sdX1 root-cubox

# download and extract the root filesystem (as root, not via sudo)
wget http://os.archlinuxarm.org/os/ArchLinuxARM-imx6-cubox-latest.tar.gz
su
tar -xpf ArchLinuxARM-imx6-cubox-latest.tar.gz -C root-cubox

# exit root shell
exit

# install u-boot bootloader
dd if=root-cubox/boot/SPL of=/dev/sdX bs=1K seek=1
dd if=root-cubox/boot/u-boot.img of=/dev/sdX bs=1K seek=69

# unmount partition
sync
umount root
```

## Initial Boot

- [x] Plug the micro-SD card into the Cubox-i (pins facing upwards).
- [x] Plug in the 5V 300A power adapter.
- [x] The Cubox-i has a serial console that is accessible via the micro-USB connector on it's back.  Plug this in between the Cubox-i and your host system, and run `screen`

```shell
screen /dev/ttyUSB0 115200

# you should see something like
Arch Linux 3.14.79-3-ARCH (ttymxc0)

alarm login:
```

!!! note

    If you need to disconnect, use `CTRL-A, :` then type `quit <enter>`

!!! note

    If you accidentally close the terminal, you will need to manually reattach to the screen session

    ```shell
	screen -ls
	screen -r {number from ls}
    ```

!!! note

    You can login with `alarm/alarm` or `root/root`.  Unfortunately `sudo` is not installed, so you'll need to work as `root` for the time being.

## Setup Networking

Unfortunately the kernel is so old, that automatic network setup via `systemd-networkd` doesn't work correctly.  You'll need temporarily assign a static IP address to get things going.

- [x] Disable all systemd related network services

```shell
systemctl stop systemd-networkd
systemctl disable systemd-networkd
systemctl stop systemd-resolved
systemctl disable systemd-resolved
systemctl stop systemd-timesyncd
systemctl disable systemd-timesyncd
```

- [x] Reboot

```shell
reboot
```

- [x] Login as root

!!! note

    To perform these next steps you'll need to know the subnet of your router, and which IPs are usable for manual IP addresses.  In my case, my router uses `192.168.86.xxx`. 
    The router itself is on `192.168.86.1` and will act as the default gateway and DNS server.  The router's DHCP IP addresses start at `192.168.86.20`, so I can use `192.168.86.2` through `192.168.86.19`  for a static IPs.

- [x] Set your DNS server to your router's IP.  In my case this was `192.168.86.1`:

```shell
rm /etc/resolv.conf
echo "nameserver 192.168.86.1" > /etc/resolv.conf
```

- [x] Plug in the Cubox-i into your router via an Ethernet cable and then bring up the interface.  I'm choosing to use `192.168.86.19`:

```shell
ip link set eth0 up
ip address add 192.168.86.19/24 broadcast + dev eth0
ip route add default via 192.168.86.1 dev eth0
```

- [x] Confirm the IP address and routes:

```shell
ip address show
ip route show
```

- [x] Make sure you have a connection to the internet:

```shell
ping www.google.com
```

- [x] Set your timezone and update the system time:

```shell
timedatectl list-timezones
timedatectl set-timezone America/Denver
timedatectl set-time "2022-10-29 21:40:00"
timedatectl
```

## Setup Pacman

This next step is a bit tricky, as you'll need to login separately via SSH to run a script that creates entropy.  Keep your main connection as `root` via `screen` active!

- [x] Log in as `alarm` via ssh:

```shell
ssh alarm@192.168.86.19
```

- [x] Use nano to create an entropy generating script:

```bash title="entrop.sh"
#!/bin/bash
for i in {1..100000}
do
   echo "Hello $i times"
done
```

- [x] Set permissions on the script so you can run it:

```shell
chmod +x entropy.sh
```

- [x] In the main connection (via `screen`) run the following command to initialize the pacman keys:

```shell
pacman-key --init
```

- [x] In the SSH connection, run the entropy script several times.  Eventually the `pacman-key --init` command should finish.  You can `exit` the SSH connection.

- [x] Continue in the main connection (via `screen`) to populate the pacman keys and update all packages:

```shell
pacman-key --populate archlinuxarm
pacman -Syu
```

- [x] You should now have everything updated (minus the kernel).  Now reboot

## Updating to Modern Kernel (as root)

The kernel that ships with the base image is `linux-imx6` which hasn't been updated in ages.  You'll want to upgrade to `linux-armv7`.

- [x] Reconnect your networking:

```shell
ip link set eth0 up
ip address add 192.168.86.19/24 broadcast + dev eth0
ip route add default via 192.168.86.1 dev eth0
```

- [x] Update the kernel:

```shell
pacman -S linux-armv7
```

- [x] You'll be asked to replace the old kernel.  Reboot one final time.

## Setup Sudo (as root)

- [x] Reconnect your networking:

```shell
ip link set eth0 up
ip address add 192.168.86.19/24 broadcast + dev eth0
ip route add default via 192.168.86.1 dev eth0
```

- [x] Install and configure `sudo` and add the alarm user to the `wheel` group:

```shell
pacman -S sudo
usermod -aG wheel alarm
```

- [x] Edit the `/etc/sudoers` file using `visudo`:

```shell
visudo

# uncomment the following line:
# %wheel ALL=(ALL:ALL) ALL
```	

- [x] Logout as `root`, and continue all the subsequent steps as `alarm`

## Setup Network Manager

Network manager can act as a DHCP client, greatly simplifying networking.

- [x] Install and enable NetworkManager:

```shell
	sudo pacman -S networkmanager
	sudo systemctl enable NetworkManager.service
```

- [x] Enable the timesyncd Service:
```shell
sudo systemctl enable systemd-timesyncd.service
```

- [x] Reboot. You should get an IP address assigned by your router.

- [x] Login as `alarm/alarm`.  Check your IP address:

```shell
ip address show
```

- [x] You should how have an IP address provided via DHCP.  In my case it is `192.168.86.250`.

- [x] You no longer need to connect via `screen` as you can connect via SSH now:

```shell
ssh alarm@alarm
```