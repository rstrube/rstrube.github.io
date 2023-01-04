---
title: 'Using AzCopy to Copy Blobs to Azure'
tags: ['development','azure']
---
# Using AzCopy Command to Copy Blobs to Azure

1. If AzCopy is not installed download and install it from the above link.
1. Once installed, navigate to its directory in cmd prompt (Make sure you run cmd prompt as Admin!)
	*Its location depends on which version you installed x64 or x86*
	`%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy`
	`%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`
1. Once navigated to install directory run the command:
	`AzCopy /Source:<source> /Dest:<destination> [Options]`
	
	Example:
	`AzCopy Source:https://myaccount.blob.core.windows.net/mycontainer  Dest:C:\myfolder /SourceKey:key /S`

	**Note: `/S` parameter is very important, allows it to recursively work through all files in specified  `<source>` copying to a destination that is in azure works as well.**

1. Let the job run, this will take a long time depending on the size of the blobs directory

## Example 1: Copy from File System To Azure Blob Container
Change to the correct directory for AzCopy
`cd 'C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy'`

Copy all blobs from FS (`S:\EPiServer\blobs`) up to Production Container (sramprodblob)
```
./AzCopy /Source:S:\EPiServer\blobs /Dest:https://sramprodblob.blob.core.windows.net/sramprobblob /DestKey:ZyWowd7tK9Dt1zKp7hLTIwshTG6qk5okujwZFJ0nX+Y7uE97uI7SiUkQKCfcYdu/8uaRB+n2CqY0fdeDWBL6Hw== /S
```

## Example 2: Copy from Prod Blob Container to New Dev Blob Container
Change to the correct directory for AzCopy
`cd 'C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy'`

Copy all blobs from Production Container (sramprodblob) to Development Container (blobcontainer-20200303)
```
./AzCopy /Source:https://sramprodblob.blob.core.windows.net/sramprodblob /Dest:https://sramdevblob.blob.core.windows.net/blobcontainer-20200303 /SourceKey:ZyWowd7tK9Dt1zKp7hLTIwshTG6qk5okujwZFJ0nX+Y7uE97uI7SiUkQKCfcYdu/8uaRB+n2CqY0fdeDWBL6Hw== /DestKey:we8tVAovxZwyeBHgWPMFWJUcB3D8Eb1K8ls1Txss1lQy0D2up9QXrusoXGAIDLSWBlXfCm3mWMZraPlmEXXFwA== /S
```

## Example 3: Copy from Stage Blob Container to New Dev Blob Container
Change to the correct directory for AzCopy
`cd 'C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy'`

Copy all blobs from Stage Container (sramstageblob) to Development Container (blobcontainer-20200303)
```shell
./AzCopy /Source:https://sramstageblob.blob.core.windows.net/blobcontainer-20190909 /Dest:https://sramdevblob.blob.core.windows.net/blobcontainer-20200303 /SourceKey:ujwRDGVF02luuNlGMtGN3+5F9KGXR/9Hep/vSdA6aMz6YnRP5r6uhIirAJlxRMvojojVQh0WM1J9mPvJEoTYbg== /DestKey:we8tVAovxZwyeBHgWPMFWJUcB3D8Eb1K8ls1Txss1lQy0D2up9QXrusoXGAIDLSWBlXfCm3mWMZraPlmEXXFwA== /S
```
See also:

* [Microsoft Azure AzCopy Help Article](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy)