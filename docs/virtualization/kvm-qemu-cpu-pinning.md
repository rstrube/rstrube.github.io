---
title: 'KVM QEMU CPU Pinning'
tags: ['virtualization','kvm qemu']
date: 2023-01-04
---
# KVM QEMU CPU Pinning {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

It can be helpful to pin virtual CPU cores to specific physical CPU cores.  In addition, AMD EPYC/Ryzen CPUs use CCXs (Core Complexes) that group together several physical CPU cores and share an L3 cache.

There's a performance advantage to grouping virtual CPU cores into as few host physical CPU cores as possible.  Ideally, these host physical CPU cores would also be in the same CCX and share the same L3 cache.

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

!!! note

    The `CORE` column reflects the host physical CPU core.

    The `L3` column reflects which L3 cache the host physical CPU core is connected to.
    
    Remember that often times hyper-threading or SMT is utilized to double the number of host CPU cores available.

In the example above, host CPU cores `0` and `1` are *both* located on host physical CPU core `0`.  These host physical CPU cores also use L3 cache `0`.

If the goal is to allocate 8 virtual CPU cores to use on the guest VM.  The ideal configuration would be to use host CPU cores `8-15`.

* The host CPU cores `8-15` are located on host physical CPU cores `4-7`.
* More importantly all of these host physical CPU cores share the same L3 cache (L3 cache `1` in this case).

In our example to properly pin the virtual CPU cores to the correct host CPU cores we would configure our VM in the following fashion:

```xml title="Virtual Machine XML Configuration"
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

!!! tip

    If your host machine is running an AMD EPYC/Ryzen CPU please see: [[kvm-qemu-amd-cpu-configuration]] to perform additional CPU configuration.

See also:

* <https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF#CPU_pinning>