---
title: 'Shell Cheatsheet'
tags: ['linux','shell','cheatsheet']
---
# Shell Cheatsheet
## Write / Append Text to a File
You can ordinarily write or append text to a file using the `>` or `>>` shell operators:

To overwrite:

```shell
echo "Replacement Text" > myFile.txt
```

To append:

```shell
echo "Appended Text" >> myFile.text
```

## Write / Append Text to a Protected File
If you need to write/append text to a protected file, use the `tee` command:

To overwrite:

```shell
echo "Replacement Text" | sudo tee myFile.txt
```

To append:

```shell
echo "Appended Text" | sudo tee -a myFile.txt
```