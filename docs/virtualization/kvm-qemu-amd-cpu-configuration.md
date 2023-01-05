---
title: 'KVM QEMU AMD CPU Configuration'
tags: ['virtualization','kvm-qemu']
date: 2023-01-04
---
# KVM QEMU AMD CPU Configuration {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

There's currently an issue where the CPU feature `topoext` is not exposed from AMD CPUs to the guest VM.  This affects the ability to use hyperthreading (SMT) on the guest VM.

To work around this issue, you'll need to specifically configure the CPU in the virtual machine XML file.

Specifically you'll need to use the `host-passthrough` mode, and explicitly require the `topoext` CPU feature.  In addition, it would be helpful to align the CPU topology.  In this case I've already pinned 8 virtual CPUs to specific host CPUs, (see [[kvm-qemu-cpu-tuning]]).  I've aligned the topology so that to the guest VM, there are 4 virtual physical CPU cores, each one capable of running 2 threads.

```xml
  <cpu mode='host-passthrough' check='none'>
    <topology sockets='1' cores='4' threads='2'/>
    <feature policy='require' name='topoext'/>
  </cpu>
```

See also:

* <https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF#Improving_performance_on_AMD_CPUs>
* <https://libvirt.org/formatdomain.html#cpu-model-and-topology>
