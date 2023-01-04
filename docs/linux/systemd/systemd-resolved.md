---
title: 'Systemd Resolved'
tags: ['linux', 'systemd', 'dns']
---
# Systemd Resolved
Systemd resolved is a local caching DNS provider that is used by default by NetworkManager.

*Note: it is not enabled by default for Arch Linux installations, to enable it:*

```shell
sudo systemctl enable --now systemd-resolved.service
```

## Check Status
To check the status:

```shell
resolvectl status
```

To check statistics (cache hits / misses):

```shell
resolvectl statistics
```

To run a query:

```shell
# resolvedctl query {hostname}
resolvectl query www.google.com
```

To flush cache:

```shell
resolvectl flush-caches
```