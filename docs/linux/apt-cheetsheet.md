---
title: 'Apt Cheatsheet'
tags: ['linux','apt','cheatsheet']
date: 2023-01-04
---
# Apt Cheatsheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---
```shell
# update list of available packages
$> sudo apt update
```

```shell
# upgrade all packages that have updates
$> sudo apt upgrade
```

```shell
# install individual packages(s)
$> sudo apt install <package(s)>
```

```shell
# mark a manually installed package as having been "automatically" installed
$> sudo apt-mark auto <package(s)>
```

```shell
# list installed packages
$> sudo apt list --installed
```

```shell
# list upgradable packages
$> sudo apt list --upgradable
```

```shell
# list packages available from a given repo (e.g. see what packages a PPA provides):
$> cd /var/lib/apt/lists
$> grep ^Package: /var/lib/apt/lists/{name of ppa or repo}*_Packages
```
