---
title: 'Vim Cheatsheet'
tags: ['development', 'vim', 'cheatsheet']
date: 2023-01-04
---
# Vim Cheatsheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Normal Mode
### Cutting Text
There are various ways to delete text. All of these commands can be prefixed with a **count** value. `d` and `c` commands can accept any motion commands. Only arrow motion examples are shown in this section, many more variations will be discussed later in this chapter.

- `dd` delete the current line
- `2dd` delete the current line and the line below it (total 2 lines)
- `10dd` delete the current line and 9 lines below it (total 10 lines)
- `dk` delete the current line and the line above it
- `3dk` delete the current line and 3 lines above it (total 4 lines)
- `D` delete from the current character to the end of line (same as `d$`, where `$` is a motion command to move to the end of line)
- `x` delete only the current character under the cursor (same as `dl`)
- `cc` delete the current line and change to Insert mode
    - indentation will be preserved depending on the `autoindent` setting
- `4cc` delete the current line and 3 lines below it and change to Insert mode (total 4 lines)
- `C` delete from the current character to the end of line and change to Insert mode
- `s` delete only the character under the cursor and change to Insert mode (same as cl)
- `5s` delete the character under the cursor and 4 characters to its right and change to Insert mode (total 5 characters)
- `S` delete the current line and change to Insert mode (same as cc)
    - indentation will be preserved depending on the `autoindent` setting

### Copying Text
There are various ways to copy text using the **yank** command `y`.

- `yy` copy the current line
    - `Y` also copies the current line
- `y$` copy from the current character to the end of line
- `2yy` copy the current line and the line below it (total 2 lines)
- `10yy` copy the current line and 9 lines below it (total 10 lines)
- `yk` copy the current line and the line above it
- `3yk` copy the current line and 3 lines above it (total 4 lines)

### Pasting Text
The **put** (paste) command `p` is used after cut or copy operations.

- `p` paste the copied content once
    - If the copied text was line based, content is pasted **below** the current line
    - If the copied text was part of a line, content is pasted to the **right** of the cursor
- `P` paste the copied content once
    - If the copied text was line based, content is pasted **above** the current line
    - If the copied text was part of a line, content is pasted to the **left** of the cursor
- `3p` and `3P` paste the copied content three times
- `]p` paste the copied content like `p` command, but changes the indentation level to match the current line
- `[p` paste the copied content like `P` command, but changes the indentation level to match the current line
