---
title: 'Enabling and Disabling KDE Compositor'
tags: ['linux', 'kde']
date: 2023-01-04
---
# Enabling and Disabling KDE Compositor {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Check if Compositor is Active
```shell
qdbus org.kde.KWin /Compositor org.kde.kwin.Compositing.active
```

## Disabling Compositor
```shell
qdbus org.kde.KWin /Compositor org.kde.kwin.Compositing.suspend
```

## Enabling Compositor
```
qdbus org.kde.KWin /Compositor org.kde.kwin.Compositing.resume
```

## Keyboard Shortcut
You can also toggle the compositor on/off using `alt+shift+F12`.
