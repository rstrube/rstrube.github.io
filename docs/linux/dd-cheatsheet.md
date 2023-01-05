---
title: 'DD Cheatsheet'
tags: ['linux','shell','dd','cheatsheet']
date: 2023-01-04
---
# DD Cheatsheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Dump Disk to Compressed Image
```shell
# dump a disk to a compressed disk image
sudo dd if=/dev/sdb | gzip -c > ~/my-disk.image.gz
```