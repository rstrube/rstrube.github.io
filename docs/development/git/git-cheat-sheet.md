---
title: 'Git Cheat Sheet'
tags: ['development','git','cheat sheet']
date: 2023-01-04
---
# Git Cheat Sheet {: .primaryHeading }
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

## Gitignore a Specific File that was Previously Added to a Repository
If a file is *already* being tracked in a given repository, simply adding it to `.gitignore` won't remove it.

You have two choices:

1. Separate commits for the deletion of file, and `.gitignore`.

:   !!! warning

        This has the downside of requiring the deletion of a specific file (at least temporarily).  After you've pushed both commits, you'll need to manually restore a local copy of the deleted file.  It will be git ignored moving forward.   

    First commit a deletion of the file.
    Second commit changes to `.gitignore` to explicitly ignore the file moving forward.

    All future changes to the file, including recreating it again will be ignore.

2. Separate commits for the index deletion of the file, and `.gitignore`

:   !!! tip

        This is the preferred method, as you don't need to explicitly delete the file.

    First remove the file from the index, and commit.

    ```shell
    # remove the file from the index only (--cached switch)
    git rm --cached myfile
    ```

    Second commit changes to `.gitignore` to explicitly ignore the file moving forward.