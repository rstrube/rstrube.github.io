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
## File Descriptors
File descriptors are numbers that uniquely identify open files in an OS.  With Linux, the first three file descriptors have special meaning:

| Descriptor | Description |
| ---------- | ----------- |
| 0          | STDIN       |
| 1          | STDOUT      |
| 2          | STDERR      |                                                                                     |

When running an program or script via a shell, you can use the redirect operator (e.g. `>`) to redirect the `STDOUT` output to a file:

```shell
# redirect "Hello World" to file test.txt rather than display it in the shell
echo "Hello World" > test.txt
```
This could be used to redirect *all* `STDOUT` to a file (this would ordinarily be displayed in the shell):

```shell
# redirect all STDOUT that my-program produces to file output.log
./my-program > output.log
```
There are times when you'll run a program or script (with redirection) and *still see* output in the shell.  This is because `STDERR` is still being output to the shell.

To have *all* output go to a file you'll need to perform two redirects:

1. Redirect `STDOUT` > file
2. Redirect `STDERR` > `STDOUT`

```shell
# redirect all STDOUT and STDERR that my-program produces to file output.log
./my-program > output.log 2>&1
```
The `2>&1` tells the shell to redirect `STDERR` (file descriptor 2) to `STDOUT` (file descriptor 1).  The reason you need a `&` is because otherwise the shell will create a file named `1` and redirect `STDERR` output to that file.

!!! warning
    The order of the two parameters is very important.  You first need to define your redirection for STDOUT *before* defining your direction for STDERR to STDOUT!

If you would like to silence *all* output entirely do the following:

```shell
# silence all STDOUT and STDERR that my-program produces
./my-program >/dev/null 2>&1
```
This will redirect all `STDOUT` output my-program produces to the "null sink", basically a black hole for any output.  And it will redirect all `STDERR` output to `STDOUT`.

!!! warning
    The order of the two parameters is very important.  You first need to define your redirection for `STDOUT` *before* defining your direction for `STDERR` to `STDOUT`!

## exec Explained
There are two main uses for `exec`:

1. To run an executable or script and replace the current shell process.
1. To redirect file descriptors.

### Using exec to Run an Executable or Script
What's the difference between running a command with exec vs. running it directly?  When running a command with `exec` a new child process is created (with a new PID) that *replaces* the current (parent) shell process.

```shell
# executes program as child process of shell, shell will continue to run
./my-program

# executes program as child process of shell, this process replaces the current shell process
exec ./my-program
```

!!! note
    If you are running exec on a script, the current shell is replaced by the interpreter defined in the shebang of the script (e.g. `#!/bin/bash`).
    
### Using exec to Redirect File Descriptors

!!! tip
    It's important to understand the basics of [file descriptors](#file-descriptors) to understand how `exec` can be used to perform redirection.

To redirect file descriptor output, run exec *without* the name of a program or script.  This uses the file descriptor syntax covered previously.

```shell
# redirects all future STDOUT from shell to shell.log
exec 1>shell.log

# "Hello World" will be directed to shell.log
echo "Hello World"
```

!!! warning
    Using `exec` for file descriptor redirection will only work on POSIX compliant shells.  This will not work on `fish` shell.