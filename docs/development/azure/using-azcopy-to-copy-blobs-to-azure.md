---
title: 'Using AzCopy to Copy Blobs to Azure'
tags: ['development','azure']
date: 2023-01-04
---
# Using AzCopy to Copy Blobs to Azure {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

- [x] If AzCopy is not installed download and install it from [here](https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10).
- [x] Once installed, navigate to its directory in the Command Prompt or Powershell (run as admin!)
```powershell
# location depends on which version you installed x64 or x86
%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
%ProgramFiles%\Microsoft SDKs\Azure\AzCopy
```

- [x] Once navigated to install directory run the command:
```powershell
AzCopy /Source:<source> /Dest:<destination> [Options]
	
# example:
# note: `/S parameter is very important, allows it to recursively work through all files in specified <source> copying to a destination that is in azure works as well.
AzCopy Source:https://myaccount.blob.core.windows.net/mycontainer  Dest:C:\\myfolder /SourceKey:key /S
```

- [x] Let the job run, this will take a long time depending on the size of the blobs directory

!!! example "Example 1: Copy from file system to Azure blob container"

    ```powershell
    # change to the correct directory for AzCopy
    cd 'C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy'

    # copy all blobs from filesystem (`C:\MyFolder\blobs`) to azure blob container (myblobcontainer)
    ./AzCopy /Source:C:\MyFolder\blobs /Dest:https://myblob.blob.core.windows.net/myblobcontainer /DestKey:ZyWowd7tK9Dt1zKp7hLTIwshTG6qk5okujwZFJ0nX+Y7uE97uI7SiUkQKCfcYdu/8uaRB+n2CqY0fdeDWBL6Hw== /S
    ```

!!! example "Example 2: Copy from one blob container another blob container"

    ```powershell
    # change to the correct directory for AzCopy
    cd 'C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy'

    # copy all blobs from blob container (myblobcontainer) to another blob container (myblob2container)
    ./AzCopy /Source:https://mybob.blob.core.windows.net/myblobcontainer /Dest:https://myblob2.blob.core.windows.net/myblob2container /SourceKey:ZyWowd7tK9Dt1zKp7hLTIwshTG6qk5okujwZFJ0nX+Y7uE97uI7SiUkQKCfcYdu/8uaRB+n2CqY0fdeDWBL6Hw== /DestKey:we8tVAovxZwyeBHgWPMFWJUcB3D8Eb1K8ls1Txss1lQy0D2up9QXrusoXGAIDLSWBlXfCm3mWMZraPlmEXXFwA== /S
    ```

See also:

* [Get Started with AzCopy](https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10)