---
title: 'DD Cheat Sheet'
tags: ['linux','shell','dd','cheat sheet']
date: 2023-01-04
---
# DD Cheat Sheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Dump Disk to Compressed Image
```shell
# dump a disk to a compressed disk image
sudo dd if=/dev/sdb | gzip -c > ~/my-disk.image.gz
```