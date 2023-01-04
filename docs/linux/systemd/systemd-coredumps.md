---
title: 'Systemd Coredumps'
tags: ['linux', 'systemd']
---
# Systemd Coredumps
systemd-coredump is used by default to manage coredumps for applications that crash.

You can use the `coredumpctl` to list and examine coredump files.

## Listing Coredumps
To list core dumps (with the newest first):

```
coredumpctl -r list
```

# Inspecting a Coredump
Once you know the PID of the core dump, you can:

```
coredumpctl info {PID}
```

This will print out the information for the core dump.

You can also dump the core dump information to a text file:

```
coredumpctl info {PID} > ~/my_coredump.txt
```

# Cleaning out old Coredumps
You can manually run kick off the systemd-tmpfiles process which cleans out all tmp files > 3 days old:

```
systemd-tmpfiles --clean
```

You can also manually delete coredump files, they are located in: `/var/lib/systemd/coredump`.