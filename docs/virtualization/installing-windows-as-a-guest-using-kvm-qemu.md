---
title: 'Installing Windows as a Guest using KVM QEMU'
tags: ['virtualization','kvm qemu','windows']
---
# Installing Windows as a Guest using KVM QEMU
## Prerequisites
* Make sure to download an ISO for Windows.  In this example we are using an ISO for Windows Server 2022.
* Make sure to download the latest version of `virtio-win` ISO from: <https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/>.  This will include the Windows virtio drivers and the Windows guest tools.  As of this writing the latest version is `0.1.217`.

![[Pasted image 20220518164928.png]]

# Creating a new VM
When creating a new virtual machine, make sure to select the appropriate Windows version from the OS dropdown.  This makes ensures that the HyperV enlightenments are enabled.

![[Pasted image 20220518161018.png]]

Make sure to also select the "Customize configuration before install" checkbox so we can fully customize the VMs virtual hardware *before* starting installation.

![[Pasted image 20220518153719.png]]

## Configuring VM to use UEFI Instead of Legacy BIOS
It's highly recommended to setup your new VM with UEFI.  Make sure to select the `x64 OVMF_CODE.fd` firmware.

*Note: if you don't see this option, you will need to install the `edk2-ovmf` package (Arch)*

![[Pasted image 20220518153913.png]]

## Configuring Virtual Disks to use VirtIO
In order to improve overall VM performance, it's highly recommended to setup your virtual disks to use VirtIO.  The main benefits of this are:
1. Increased performance
2. Passthrough ATA TRIM support (for SSDs)

To do this configure the Virtual Disk as follows:

![[Pasted image 20220518160548.png]]

## Configurating Virtual NIC to use VirtIO
It's also recommended to configure your virtual network interface card to use VirtIO.

![[Pasted image 20220518162518.png]]

## Adding Additional CDROM Drive for Driver Disk
Because we are setting up Windows to use VirtIO for both the virtual disk and the NIC we need to add another CDROM for installation.  This CDROM will be used to load the virtio drivers during the installation.  Click the "Add Hardware" button to add a new CDROM to your VM.

![[Pasted image 20220518164421.png]]

The go ahead and configure a new CDROM drive and load it with the `virtio-win` ISO you downloaded previously.

![[Pasted image 20220518165512.png]]

![[Pasted image 20220518165201.png]]

## CPU Tuning
You might wish to consider steps to pin your virtual CPUs to specific cores of your host CPU.  Please see [[kvm-qemu-cpu-tuning]] for more information.  You might also need to perform some additional tweaks for AMD CPUs.  Please see [[kvm-qemu-amd-cpu-configuration]] for more information.

## Configuring QEMU Guest Agent Channel
You'll now need to add a new channel for the QEMU guest agent.  This will allow your host to communicate window resizing to the guest so that it can dynamically adjust resolution. 

To add the new guest agent channel:

1. Click the "Add Hardware" button.
2. Select "Channel"
3. Configure the channel "Name" to be `org.qemu.guest_agent.0`
4. Configure the channel type to be `UNIX socket (unix)`
5. Make sure the "Auto socket" option is checked.

![[Pasted image 20220519103339.png]]

## Installation
After configuring your VM, you can click the "Begin Installation" button.  This should boot the virtual machine as if you were booting off of the Windows installation ISO.

![[Pasted image 20220518162814.png]]

When prompted to select the installation type, make sure to choose "Custom".

![[Pasted image 20220518165923.png]]

Now you'll need to load the `virtio-win` driver via the second CDROM drive you added to your VM.

![[Pasted image 20220518170015.png]]

![[Pasted image 20220518170110.png]]

Make sure to navigate to the `viostor` directory and select the correct subdirectory for your version of windows.  In this case `2k22/amd64` for Windows Server 2022.

![[Pasted image 20220518170428.png]]

After installing the driver you should be able to see the virtual disk available for installation.  You might need to refresh the drive listing for it to fully come online and become available for installation.  Go through the rest of the Windows installation process.

## Post-Installation
After installing windows and logging in, you'll need to complete the rest of the VirtIO driver installation process.  You should still have the `virtio-win` ISO in your second CDROM.  Navigate to it and run the `virtio-win-guest-tools` executable.

![[Pasted image 20220518172831.png]]

*Note: there is currently a bug with `virtio-win-0.1.217` that will cause installation to fail.  You need to tweak the installation options  for "Pvpanic", "Qemufwcfg", and "Qemupciserial" to "Feature will be installed when required."*

![[Pasted image 20220519104236.png]]

## Final Configuration
Shutdown the guest VM and make the following adjustments to enable automatic guest resizing:

`View -> Scale Display -> Auto resize VM with window`

Note if this option is not available it means that either:
* Your guest agent channel was not configured properly
* QEMU guest tools were not installed correctly.

![[Pasted image 20220519104451.png]]

You can also disconnect the ISO from the first CDROM drive, and fully remove the entire second CDROM drive.

![[Pasted image 20220519104909.png]]

![[Pasted image 20220519104957.png]]

## Issues with Maximum Guest Resizing Resolution
You might notice that there is a maximum resolution your guest VM can scale to.  To fix this you'll need to update the video memory for your virtual video card.  This will allow the VM guest to fully display on larger resolution monitors.

To add additional video memory, select the "Video QXL" hardware in your VM configuration and click on the "XML" tab.  Then update `vgamem` from `16384` (16MB) to `65536` (64MB).  This should provide enough video memory to fully display the guest even for high-resolution monitors.

![[Pasted image 20220519102746.png]]