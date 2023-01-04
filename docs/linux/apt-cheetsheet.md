---
title: 'Apt Cheatsheet'
tags: ['linux','apt','cheatsheet']
---
# Apt Cheatsheet
To update the list of available packages:

```shell
$> sudo apt update
```

To upgrade all packages that have updates:

```shell
$> sudo apt upgrade
```

To install an individual package:

```shell
$> sudo apt install <package(s)>
```

To mark a manually installed package as having been "automatically" installed:

```shell
$> sudo apt-mark auto <package(s)>
```

To list installed packages:

```shell
$> sudo apt list --installed
```

To list upgradable packages:

```shell
$> sudo apt list --upgradable
```

To list packages available from a given repo (e.g. see what packages a PPA provides):

```shell
cd /var/lib/apt/lists
grep ^Package: /var/lib/apt/lists/{name of ppa or repo}*_Packages
```
