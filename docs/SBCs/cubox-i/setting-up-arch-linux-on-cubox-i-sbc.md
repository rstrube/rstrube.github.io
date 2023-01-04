---
title: 'Setting up Arch Linux on Cubox-i'
tags: ['sbc','linux','arch-linux','cubox']
---
# Setting up Arch Linux on Cubox-i Single-Board Computer
## Micro-SD Card Setup

Plugin the micro-SD card into your main computer.  It should be detected by your OS and assigned a device path (e.g. `/dev/sdb`).  Make sure to replace `/dev/sdX` with the appropriate device when following the subsequent steps.

Unmount the disk if it was auto-mounted

```shell
umount /dev/sdX1
```

Zero out the first few MB of the card to remove any previous U-Boot environment:

	dd if=/dev/zero of=/dev/sdX bs=1M count=4

Start fdisk to partition the SD card:

    fdisk /dev/sdX

At the fdisk prompt, create the new partition:
* Type n, and press ENTER, accepting default values.
* Exit by typing w.

Create the ext4 filesystem:

    mkfs.ext4 /dev/sdX1

Mount the filesystem:

    mkdir root
    mount /dev/sdX1 root

Download and extract the root filesystem (as root, not via sudo):

    wget http://os.archlinuxarm.org/os/ArchLinuxARM-imx6-cubox-latest.tar.gz
    su
    bsdtar -xpf ArchLinuxARM-imx6-cubox-latest.tar.gz -C root

Install the U-Boot bootloader:

    dd if=root/boot/SPL of=/dev/sdX bs=1K seek=1
    dd if=root/boot/u-boot.img of=/dev/sdX bs=1K seek=69

Unmount the partition:

    sync
    umount root

## Initial Boot

Plug the micro-SD card into the Cubox-i (pins facing upwards).
Plug in the 5V 300A power adapter.

The Cubox-i has a serial console that is accessible via the micro-USB connector on it's back.  Plug this in between the Cubox-i and your host system, and run `screen`

	screen /dev/ttyUSB0 115200

You should see something like:

```
Arch Linux 3.14.79-3-ARCH (ttymxc0)

alarm login:
```

*Note: if you need to disconnect, use `CTRL-A, :` then type `quit <enter>`*

If you accidentally close the terminal, you will need to manually reattach to the screen session

	screen -ls
	screen -r {number from ls}

You can login with `alarm/alarm` or `root/root`.  Unfortunately `sudo` is not installed, so you'll need to work as `root` for the time being.

## Setup Networking

Unfortunately the kernel is so old, that automatic network setup via `systemd-networkd` doesn't work correctly.  You'll need temporarily assign a static IP address to get things going.

First you'll have to disable all systemd related network services:

	systemctl stop systemd-networkd
	systemctl disable systemd-networkd
	systemctl stop systemd-resolved
	systemctl disable systemd-resolved
	systemctl stop systemd-timesyncd
	systemctl disable systemd-timesyncd

Now reboot

	reboot

Login as root

To perform these next steps you'll need to know the subnet of your router, and which IPs are usable for manual IP addresses.  In my case, my router uses `192.168.86.xxx`. 
The router itself is on `192.168.86.1` and will act as the default gateway and DNS server.  The router's DHCP IP addresses start at `192.168.86.20`, so I can use `192.168.86.2` through `192.168.86.19`  for a static IPs.

Set your DNS server to your router's IP.  In my case this was `192.168.86.1`.

	rm /etc/resolv.conf
	echo "nameserver 192.168.86.1" > /etc/resolv.conf

Plug in the Cubox-i into your router via an Ethernet cable and then bring up the interface.  I'm choosing to use `192.168.86.19`

	ip link set eth0 up
	ip address add 192.168.86.19/24 broadcast + dev eth0
	ip route add default via 192.168.86.1 dev eth0

Now confirm the IP address and routes

	ip address show
	ip route show

And make sure you have a connection to the internet

	ping www.google.com

Now set your timezone and update the system time

	timedatectl list-timezones
	timedatectl set-timezone America/Denver
	timedatectl set-time "2022-10-29 21:40:00"
	timedatectl

## Setup Pacman

This next step is a bit tricky, as you'll need to login separately via SSH to run a script that creates entropy.  Keep your main connection as `root` via `screen` active!

First log in as `alarm` via ssh:

	ssh alarm@192.168.86.19

The use nano to create a script (`entropy.sh`):

```bash
#!/bin/bash
for i in {1..100000}
do
   echo "Hello $i times"
done
```

Then set permissions on the script so you can run it

	chmod +x entropy.sh

Now in the main connection (via `screen`) run the following command:

    pacman-key --init

In the SSH connection, run the entropy script several times.  Eventually the `pacman-key --init` command should finish.  You can `exit` the SSH connection.

Then continue in the main connection (via `screen`):

    pacman-key --populate archlinuxarm
    pacman -Syu

You should now have everything updated (minus the kernel).  Now reboot

	reboot

## Updating to Modern Kernel (as root)

The kernel that ships with the base image is `linux-imx6` which hasn't been updated in ages.  You'll want to upgrade to `linux-armv7`.

First reconnect your networking:

	ip link set eth0 up
	ip address add 192.168.86.19/24 broadcast + dev eth0
	ip route add default via 192.168.86.1 dev eth0

Now update the kernel:

	pacman -S linux-armv7

You'll be asked to replace the old kernel.

Reboot one final time:

	reboot

## Setup Sudo (as root)

First reconnect your networking:

	ip link set eth0 up
	ip address add 192.168.86.19/24 broadcast + dev eth0
	ip route add default via 192.168.86.1 dev eth0

Now you'll install and configure `sudo` and add the alarm user to the `wheel` group.

	pacman -S sudo
	usermod -aG wheel alarm

Now you'll edit the `/etc/sudoers` file using `visudo`

	visudo

Uncomment the following line:

	# %wheel ALL=(ALL:ALL) ALL

Now you can finally logout as `root`, and continue all the subsequent steps as `alarm`

	exit

Then login as `alarm/alarm`

## Setup Network Manager

Network manager can act as a DHCP client, greatly simplifying networking.  In these next steps you'll install and enable it, and re-enable the `systemd-timesyncd` service.

	sudo pacman -S networkmanager
	sudo systemctl enable NetworkManager.service
	sudo systemctl enable systemd-timesyncd.service

Now after you reboot, you'll get an IP address automatically applied.  It will no longer be the static one you were using earlier, but will be assigned by your router.  Reboot.

	sudo reboot

Login as `alarm/alarm`.  After rebooting, check your IP address:

	ip address show

You should how have an IP address provided via DHCP.  In my case it is `192.168.86.250`.

You no longer need to connect via `screen` as you can connect via SSH now, e.g.

	ssh alarm@alarm
