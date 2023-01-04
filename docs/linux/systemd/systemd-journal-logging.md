---
title: 'Systemd Journal Logging'
tags: ['linux', 'systemd']
---
# Systemd Journal Logging
Systemd uses a logging system called "journal".

## Journal Size
To view the current size of the journal:

```shell
journalctl -b -u systemd-journald
```

## Follow Journal Messages
To follow journal messages in real time:

```shell
journalctl -f
```

## Show Journal Messages from Boot
To show journal messages from the current boot:

```shell
journalctl -b
```

To show journal messages from previous boot:

```shell
journalctl -b -1 # you can put any number here to go back 'n' boots
```

## Manually Cleaning Journal
Remove archived journal files until the disk space they use falls below 100M:

```shell
journalctl --vacuum-size=100M
```

Make all journal files contain no data older than 2 weeks:

```shell
journalctl --vacuum-time=2weeks
```

Make all journal files contain no data older than 1 day:

```shell
journalctl --vacuum-time=1days
```

See also:
* `man journalctl`
* https://wiki.archlinux.org/title/Systemd/Journal
