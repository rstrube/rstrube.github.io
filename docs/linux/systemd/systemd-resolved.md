---
title: 'Systemd Resolved'
tags: ['linux', 'systemd', 'dns']
date: 2023-01-04
---
# Systemd Resolved {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

Systemd resolved is a local caching DNS provider that is used by default by NetworkManager.

!!! tip

    Systemd resolved is not enabled by default for Arch Linux installations, to enable it during or post installation run the following:

    ```shell
    sudo systemctl enable --now systemd-resolved.service
    ```
You can use `resolvectl` to manage Systemd resolved.

## Check Status
```shell
# check the status
resolvectl status
```

```shell
# check statistics (cache hits / misses)
resolvectl statistics
```

## Run Queries
```shell
# run a specific query
resolvectl query www.google.com
```
## Flush Cache
```shell
# flush cache
resolvectl flush-caches
```