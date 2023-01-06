---
title: 'Shell Cheat Sheet'
tags: ['linux','shell','cheat sheet']
date: 2023-01-04
---
# Shell Cheat Sheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Write / Append Text to a File
You can ordinarily write or append text to a file using the `>` or `>>` shell operators:

```shell
# overwrite
echo "Replacement Text" > myFile.txt
```

```shell
# append
echo "Appended Text" >> myFile.text
```

## Write / Append Text to a Protected File
If you need to write/append text to a protected file, use the `tee` command:

```shell
# overwrite protected file
echo "Replacement Text" | sudo tee myFile.txt
```

```shell
# append to protected file
echo "Appended Text" | sudo tee -a myFile.txt
```