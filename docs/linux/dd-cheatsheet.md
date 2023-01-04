---
title: 'DD Cheatsheet'
tags: ['linux','shell','dd','cheatsheet']
---
# DD cheatsheet
## Dump disk to image

```shell
sudo dd if=/dev/sdb | gzip -c > ~/my-disk.image.gz
```