---
title: 'Remove Lockout on Arch Linux'
tags: ['linux', 'arch-linux']
---
# Remove Lockout on Arch Linux
If you enter in an invalid password three times, your user will be locked out and you will be unable to run `sudo` commands for 10 mins.

To manually unlock the user:

```shell
# Switch to a true root shell
su root

# Remove the lock on the user
faillock --reset --user {username}
```

See also:

* <https://wiki.archlinux.org/title/Security#Enforce_a_delay_after_a_failed_login_attempt>