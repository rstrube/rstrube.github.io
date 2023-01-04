---
title: 'Enabling and Disabling KDE Compositor'
tags: ['linux', 'kde']
---
# Enabling and Disabling KDE Compositor
## Checking if Compositor is Active
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
You can also toggle the compository on/off using `alt+shift+F12`.
