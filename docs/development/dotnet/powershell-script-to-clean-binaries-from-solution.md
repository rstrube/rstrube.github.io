---
title: 'Powershell Script to Clean all Binaries from VS Solution'
tags: ['development','dotnet']
---
# Powershell Script to Clean all Binaries from VS Solution
Run this from Powershell within the solution directory to purge out all binaries (this greatly reduces size of solution).

```powershell
Get-ChildItem -inc bin,obj -rec | Remove-Item -rec -force
```