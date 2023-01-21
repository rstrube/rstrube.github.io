---
title: 'Systemd Journal Logging'
tags: ['linux', 'systemd']
date: 2023-01-20
---
# Systemd Journal Logging {: .primaryHeading }
<small>Last updated: 2023-01-20</small>
{: .primaryDate }

---

Systemd uses a logging system called "journal" to manage system wide logging.

You can use `journalctl` to view, manage, and remove logs.

## Journal Size
```shell
# view current size of journal
journalctl -b -u systemd-journald
```

## Follow Journal Messages
```shell
# follow journal messages in real-time
journalctl -f
```

## Show Journal Messages from Boot
```shell
# show journal messages from the current boot
journalctl -b
```

```shell
# show journal messages from previous boot
journalctl -b -1 # you can put any number here to go back 'n' boots
```

## Manually Cleaning Journal
```shell
# remove oldest journal entries until disk space utilization falls below 100M
journalctl --vacuum-size=100M
```

```shell
# remove all journal entries older than 2 weeks
journalctl --vacuum-time=2weeks
```

```shell
# remove all journal entries older than 1 day
journalctl --vacuum-time=1days
```

```shell
# remove all journal entries older than 1 minute
journalctl --vacuum-time=1m
```

See also:

* `man journalctl`
* <https://wiki.archlinux.org/title/Systemd/Journal>
