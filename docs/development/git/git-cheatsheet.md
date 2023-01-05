---
title: 'Git Cheetsheet'
tags: ['development','git','cheatsheet']
date: 2023-01-04
---
# Git Cheatsheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Delete all Local Branches Except for Specific Branch
There are times when you would like to clean out most of your local branches, keeping a few specific branches:

```shell
# Delete all branches except "main"
git branch | grep -v "main" | xargs git branch -D

# Delete all branches except "main" and "develop"
git branch | grep -v "main" | grep -v "develop" | xargs git branch -D

# Delete all branches except for "main" and your current branch
git branch | grep -v "main" | grep -v ^* | xargs git branch -D
```

## Completely Reset Local Repository Branch
There are times when you would like to completely reset your local repository branch so it matches an upstream remote *exactly*.

```shell
# Refreshes from default upstream remote
git fetch

# Optionally you can specifiy the upstream remote
git fetch origin

# Do a hard reset to the latest commit from upstream remote
git reset --hard origin/HEAD

# Clean out untracked files
# -x don't use standard .gitignore rules
# -d recurse into untracked directories
# -f force
git clean -xdf
```