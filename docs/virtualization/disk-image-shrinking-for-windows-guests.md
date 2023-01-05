---
title: 'Disk Image Shrinking for Windows Guests'
tags: ['virtualization','kvm-qemu','windows']
date: 2023-01-04
---
# Disk Image Shrinking for Windows Guests {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

!!! tip

    Before embarking on shrinking your VM disk image first perform a general cleanup of your Windows VM.

## Windows Cleanup
### Windows SxS Cleanup
The Windows SxS maintains an archive of windows components that have been upgraded.  Over time, this can consume a large amount of disk space.  You can run some commands to immediately wipe out all but the most recent windows components.

Using an elevated Command Prompt or Powershell, execute the following:

```powershell
Dism.exe /online /Cleanup-Image /StartComponentCleanup
```

See also:

* <https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/clean-up-the-winsxs-folder>

### Windows Update Cache Cleanup
1. Launch Disk Cleanup
2. Click the "Cleanup system files button"
3. Cleanup up any files related to Windows Update.
4. Stop the "Windows Update" service
5. Delete all files inside the `C:\Windows\SoftwareDistribution` directory.

## Confirm TRIM Is Enabled
Run the following: `fsutil behavior query DisableDeleteNotify`, if the result is `0` TRIM is enabled.

## Zero Out the Drive Using sdelete
1. Download sdelete from [here](https://docs.microsoft.com/en-us/sysinternals/downloads/sdelete)
2. Zero out the free space of the drive: `.\sdelete.exe -z c:`

## Shrink Disk Image
1. Make sure the Windows VM is shutdown.
2. Run the following commands:

```shell
# make backup of original disk image
cp disk.qcow2 disk.qcow2.bak

# convert the disk from qcow2 -> qcow2
# zeroed out locations will be removed from the disk image
qemu-img convert -O qcow2 -p {disk.qcow2.bak} {disk.qcow2}
```