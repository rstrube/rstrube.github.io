---
title: 'Systemd Core Dumps'
tags: ['linux', 'systemd']
date: 2023-01-04
---
# Systemd Core Dumps {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

Systemd manages core dumps for applications that crash.

You can use `coredumpctl` to list and examine core dump files.

## Listing Core Dumps
```shell
# list core dumps (newest first):
coredumpctl -r list
```

## Inspecting a Core Dump
```shell
# inspect a core dump using PID
coredumpctl info {PID}
```

```shell
# output core dump to text file
coredumpctl info {PID} > ~/my_coredump.txt
```

## Cleaning out old Core Dumps
```shell
# manually kick off process to clean all tmp files > 3 days old, including core dumps
systemd-tmpfiles --clean
```

!!! tip

    You can also manually delete core dump files, they are located in: `/var/lib/systemd/coredump`.