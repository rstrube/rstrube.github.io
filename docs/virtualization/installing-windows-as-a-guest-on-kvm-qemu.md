---
title: 'Installing Windows as a Guest on KVM QEMU'
tags: ['virtualization','kvm qemu','windows']
date: 2023-01-06
---
# Installing Windows as a Guest on KVM QEMU {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Prerequisites

- [x] Make sure to download an ISO for Windows.  In this example we are using an ISO for Windows Server 2022.
- [x] Make sure to download the latest version of `virtio-win` ISO from: <https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/>.  This will include the Windows virtio drivers and the Windows guest tools.

!!! note

    The latest version at the time of this writing is `0.1.225`.  Download whatever the latest version is.  Many of the screenshots contained within might reference an older version of the `virtio-win` ISO.

## Creating a new VM

- [x] When creating a new virtual machine, make sure to select the appropriate Windows version from the OS dropdown.  This makes ensures that the HyperV enlightenments are enabled.

<figure markdown>
  ![[new-vm-windows-version.png]]{: width="500"}
  <figcaption>Selecting correct Windows version</figcaption>
</figure>

- [x] Make sure to also select the **Customize configuration before install** checkbox so we can fully customize the VMs virtual hardware *before* starting installation.

<figure markdown>
  ![[customize-before-install.png]]{: width="500"}
  <figcaption>Selecting "Customize configuration before install" checkbox</figcaption>
</figure>

## Configuring VM to use UEFI Instead of Legacy BIOS

- [x] It's highly recommended to setup your new VM with UEFI.  Make sure to select the `UEFI x86_x64 OVMF_CODE.fd` firmware.

!!! tip

    If you don't see this option, you will need to install your distro's OVMF package.  For Arch Linux the package is `edk2-ovmf`.

<figure markdown>
  ![[ovmf.png]]{: width="500"}
  <figcaption>Selecting OVMF firmware to support UEFI for your VM</figcaption>
</figure>

## Configuring Virtual Disks to use VirtIO

In order to improve overall VM performance, it's highly recommended to setup your virtual disks to use VirtIO.  The main benefits of this are:

* Increased performance
* Passthrough ATA TRIM support (for SSDs)

- [x] To do this configure configure your SATA disk with a **Disk bus** of `VirtIO` and a **Discard mode** of `unmap`.

<figure markdown>
  ![[disk-virtio.png]]{: width="500"}
  <figcaption>Configuring the SATA disk to use VirtIO</figcaption>
</figure>

## Configuring Virtual NIC to use VirtIO

It's also recommended to configure your virtual NIC to use VirtIO.

- [x] To do this configure configure your NIC device with a **Device model* of `VirtIO`.

<figure markdown>
  ![[nic-virtio.png]]{: width="500"}
  <figcaption>Configuring the NIC to use VirtIO</figcaption>
</figure>

## Adding Additional CDROM Drive for Driver Disk

Because we are setting up Windows to use VirtIO for both the virtual disk and the NIC we need to add another CDROM for installation.  This CDROM will be used to load the virtio drivers during the installation.

- [x] Click the **Add Hardware** button to add a new CDROM to your VM.
- [x] Configure a new CDROM drive to load `virtio-win` ISO you downloaded previously.

<figure markdown>
  ![[cdrom-with-iso-1.png]]{: width="500"}
  <figcaption>Adding a new CDROM to the VM</figcaption>
</figure>

<figure markdown>
  ![[cdrom-with-iso-2.png]]{: width="500"}
  <figcaption>Configuring the new CDROM to load the virtio-win ISO</figcaption>
</figure>

## CPU Configuration

Depending on your CPU you might have to perform some additional steps:

* **CPU Pinning**, please see: [[kvm-qemu-cpu-pinning]]
* Additional **AMD CPU Configuration**, please see: [[kvm-qemu-amd-cpu-configuration]]

## Configuring QEMU Guest Agent Channel

To support dynamic resolution adjustments on window resizing, you'll need to add a channel for the QEMU guest agent. 

- [x] Add a new channel for the QEMU guest agent.

1. Click the **Add Hardware** button.
1. Select **Channel**.
1. Configure the channel **Name** to be `org.qemu.guest_agent.0`.
1. Configure the channel type to be `UNIX socket (unix)`.
1. Make sure the **Auto socket** option is checked.

<figure markdown>
  ![[channel-ga.png]]{: width="500"}
  <figcaption>Adding a new channel for the QEMU guest agent</figcaption>
</figure>

## Installation

- [x] After configuring your VM, click the **Begin Installation** button.  This should boot the virtual machine as if you were booting off of the Windows installation ISO.

<figure markdown>
  ![[windows-install-1.png]]{: width="500"}
  <figcaption>Starting Windows installation after booting VM</figcaption>
</figure>

- [x] Choose **Custom** when prompted to select the installation type.

<figure markdown>
  ![[windows-install-2.png]]{: width="500"}
  <figcaption>Selecting "Custom" installation type</figcaption>
</figure>

- [x] Select the **Load driver** option.

<figure markdown>
  ![[windows-install-3.png]]{: width="500"}
  <figcaption>Selecting the "Load driver" option.</figcaption>
</figure>

- [x] Load the VirtIO driver via the second CDROM drive you added to your VM.

<figure markdown>
  ![[windows-install-4.png]]{: width="500"}
  <figcaption>Loading the VirtIO driver from the second CDROM drive.</figcaption>
</figure>

- [x] Navigate to the `viostor` directory and select the correct subdirectory for your specific version of windows.  For this example we are using `2k22/amd64` which is used Windows Server 2022 x86_x64.

<figure markdown>
  ![[windows-install-5.png]]{: width="500"}
  <figcaption>Navigating to the correct driver directory.</figcaption>
</figure>

- [x] After installing the driver you should be able to see the virtual disk available for installation.  You might need to refresh the drive listing for it to fully come online and become available for installation.
- [x] Go through the rest of the Windows installation process normally.

## Post-Installation

After installing windows and logging in, you'll need install the VirtIO guest tools.  You should still have the `virtio-win` ISO in your second CDROM drive.

- [x] Navigate to second CDROM drive and run the `virtio-win-guest-tools` executable.

<figure markdown>
  ![[virtio-guest-tools.png]]{: width="500"}
  <figcaption>Installing the VirtIO guest tools</figcaption>
</figure>

## Final Configuration

- [x] Shutdown the guest VM.
- [x] Configure Virt Viewer to support dynamic resolution adjustment on window resize. Select `View -> Scale Display -> Auto resize VM with window`.

!!! warning

    If this option is not available it means that either:

    * Your QEMU guest agent channel was not configured properly.
    * QEMU guest tools were not installed correctly.

<figure markdown>
  ![[virt-viewer.png]]{: width="500"}
  <figcaption>Configuring virt-viewer for dynamic resolution</figcaption>
</figure>

- [x] Unload the ISO from the second CDROM.

<figure markdown>
  ![[unload-iso.png]]{: width="500"}
  <figcaption>Unloading the virtio-win ISO</figcaption>
</figure>

- [x] Remove the second CDROM entirely from the VM.

<figure markdown>
  ![[remove-cdrom.png]]{: width="500"}
  <figcaption>Removing the second CDROM</figcaption>
</figure>

!!! tip

    You might notice that there is a maximum resolution your guest VM can scale to.  To fix this you'll need to update the video memory for your virtual video card.  This will allow the VM guest to fully display on larger resolution monitors.

    To add additional video memory, select the **Video QXL** hardware in your VM configuration and click on the **XML** tab.  Then update `vgamem` from `16384` (16MB) to `65536` (64MB).  This should provide enough video memory to fully display the guest even for high-resolution monitors.

    <figure markdown>
        ![[vga-ram-adjust.png]]{: width="500"}
        <figcaption>Installing the VirtIO guest tools</figcaption>
    </figure>