---
title: 'KVM QEMU AMD CPU Configuration'
tags: ['virtualization','kvm qemu']
date: 2023-01-04
---
# KVM QEMU AMD CPU Configuration {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

There's currently an issue where the CPU feature `topoext` is not exposed from host AMD CPUs to guest VMs.  This affects the ability to use SMT (hyperthreading) on the guest VM.

To work around this issue, you'll need to specifically configure the CPU in the virtual machine XML file:

- [x] Set the CPU mode to `host-passthrough`.
- [x] Make sure to `require` the `topoext` CPU feature.
- [x] Define a CPU topology that aligns with your physical hardware.

In this example our host system has an AMD CPU with 8 physical cores each supporting SMT for a total of 16 host CPU cores.  The goal is to allocate 8 virtual CPU cores to our VM.  Therefore the ideal topology for our VM is to have 4 virtual "physical" CPU cores each with 2 threads for a total of 8 virtual CPU cores.  This aligns and maps well with our host's physical hardware.

```xml title="Virtual Machine XML Configuration File"
  <cpu mode='host-passthrough' check='none'>
    <topology sockets='1' cores='4' threads='2'/>
    <feature policy='require' name='topoext'/>
  </cpu>
```

!!! tip

    You should also consider CPU pinning when thinking about your topology.  Please see: [[kvm-qemu-cpu-pinning]] for more information.

See also:

* <https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF#Improving_performance_on_AMD_CPUs>
* <https://libvirt.org/formatdomain.html#cpu-model-and-topology>
