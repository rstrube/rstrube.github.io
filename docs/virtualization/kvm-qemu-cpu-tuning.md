---
title: 'KVM QEMU CPU Tuning'
tags: ['virtualization','kvm-qemu']
date: 2023-01-04
---
KVM QEMU CPU Tuning {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

It can be helpful to pin virtual CPUs to specific physical CPU cores.  In addition, AMD EPYC/Ryzen CPUs use CCXs (Core Complexes) that group together several physical CPU cores and share an L3 cache.

There's a performance advantage to grouping virtual CPUs into as few physical CPU cores as possible.  Ideally, these physical CPU cores would also be in the same CCX and share the same L3 cache.

To determine what the CPU topography of your host machine is, you can use the following command:

```shell
lscpu -e
```

Here is an example when running `lscpu -e` on an 8C/16T Ryzen 4800U CPU.

```shell
robert@mini ~> lscpu -e  
CPU NODE SOCKET CORE L1d:L1i:L2:L3 ONLINE    MAXMHZ    MINMHZ      MHZ  
 0    0      0    0 0:0:0:0          yes 1800.0000 1400.0000 1400.000  
 1    0      0    0 0:0:0:0          yes 1800.0000 1400.0000 1400.000  
 2    0      0    1 1:1:1:0          yes 1800.0000 1400.0000 1400.000  
 3    0      0    1 1:1:1:0          yes 1800.0000 1400.0000 1400.000  
 4    0      0    2 2:2:2:0          yes 1800.0000 1400.0000 1397.206  
 5    0      0    2 2:2:2:0          yes 1800.0000 1400.0000 1400.000  
 6    0      0    3 3:3:3:0          yes 1800.0000 1400.0000 1400.000  
 7    0      0    3 3:3:3:0          yes 1800.0000 1400.0000 1400.000  
 8    0      0    4 4:4:4:1          yes 1800.0000 1400.0000 1800.000  
 9    0      0    4 4:4:4:1          yes 1800.0000 1400.0000 1400.000  
10    0      0    5 5:5:5:1          yes 1800.0000 1400.0000 1400.000  
11    0      0    5 5:5:5:1          yes 1800.0000 1400.0000 1400.000  
12    0      0    6 6:6:6:1          yes 1800.0000 1400.0000 1400.000  
13    0      0    6 6:6:6:1          yes 1800.0000 1400.0000 1400.000  
14    0      0    7 7:7:7:1          yes 1800.0000 1400.0000 1400.000  
15    0      0    7 7:7:7:1          yes 1800.0000 1400.0000 1400.000
```

It's important to note the `CORE` column and the `L3` column, which reflect the physical CPU cores, and which L3 cache the physical CPU cores are connected to.  Remember often times "hyper-threading" is utiltized to double the number of CPUs available on a given host.

So in the example above, CPUs 0 and 1 are both located on physical CPU core 0.  They also use L3 cache 0.

In the example above, if I wanted to allocate 8 virtual CPUs to use on the guest VM, the best bet would be to use CPUs 8-15.  This would utilize physical CPU cores 4-7, which are all connected to the same L3 cache (in this case 1).

To do this, you would do the following in your virtual machine XML file:

```xml
  <vcpu placement="static">8</vcpu>
  <cputune>
    <vcpupin vcpu="0" cpuset="8"/>
    <vcpupin vcpu="1" cpuset="9"/>
    <vcpupin vcpu="2" cpuset="10"/>
    <vcpupin vcpu="3" cpuset="11"/>
    <vcpupin vcpu="4" cpuset="12"/>
    <vcpupin vcpu="5" cpuset="13"/>
    <vcpupin vcpu="6" cpuset="14"/>
    <vcpupin vcpu="7" cpuset="15"/>
  </cputune>
```

See also:

* <https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF#CPU_pinning>

*Note: if your host machine is running an AMD EPYC/Ryzen CPU please see: [[kvm-qemu-amd-cpu-configuration]] to perform additional CPU configuration.*