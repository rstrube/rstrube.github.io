---
title: 'Git Cheat Sheet'
tags: ['development','git','cheat sheet']
date: 2023-01-04
---
# Git Cheat Sheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---
## Push an Existing Repository to an Upstream Remote

```shell
# add an https upstream remote
git remote add origin https://github.com/rstrube/xxx.git

# alternatively add an ssh upstream remote
git remote add origin git@github.com:rstrube/xxx.git

# force move existing branch to main (e.g. rename branch)
git branch -M main

# push a main branch and add upstream tracking reference
git push -u origin main

# alternatively push all branches and add upstream tracking reference(s)
git push -u origin --all
```

## Set Upstream Tracking for Specific Branch

```shell
# add upstream tracking reference for current branch
git branch -u origin/develop

# add upstream tracking reference for specific branch
git branch -u origin/develop develop
```

## Delete all Local Branches Except for Specific Branch
There are times when you would like to clean out most of your local branches, keeping a few specific branches:

```shell
# delete all branches except "main"
git branch | grep -v "main" | xargs git branch -D

# delete all branches except "main" and "develop"
git branch | grep -v "main" | grep -v "develop" | xargs git branch -D

# delete all branches except for "main" and your current branch
git branch | grep -v "main" | grep -v ^* | xargs git branch -D
```

## Completely Reset Local Repository Branch
There are times when you would like to completely reset your local repository branch so it matches an upstream remote *exactly*.

```shell
# refreshes from default upstream remote
git fetch

# optionally you can specify the upstream remote
git fetch origin

# do a hard reset to the latest commit from upstream remote
git reset --hard origin/HEAD

# clean out untracked files
# -x don't use standard .gitignore rules
# -d recurse into untracked directories
# -f force
git clean -xdf
```

## Gitignore a Specific File that was Previously Added to a Repository
If a file is *already* being tracked in a given repository, simply adding it to `.gitignore` won't remove it.

There are two approaches:

**Approach 1:** Separate commits for the deletion of file, and `.gitignore`.

:   !!! warning

        This has the downside of requiring the deletion of a specific file (at least temporarily).  After you've pushed both commits, you'll need to manually restore a local copy of the deleted file.  It will be git ignored moving forward.   

    - [x] Commit a deletion of the file.
    - [x] Commit changes to `.gitignore` to explicitly ignore the file moving forward.

    All future changes to the file, including recreating it again will be ignore.

**Approach 2:** Separate commits for the deletion of the file *from the index*, and `.gitignore`

:   !!! tip

        This is the preferred method, as you don't need to explicitly delete the file.
        
    - [x] Remove the file from the index, and commit.
    ```shell
    # remove the file from the index only (--cached switch)
    git rm --cached myfile
    ```

    - [x] Commit changes to `.gitignore` to explicitly ignore the file moving forward.
    
    All future changes to the file will be ignore.