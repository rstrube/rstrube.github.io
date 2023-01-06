---
title: '.NET Core CLI Cheat Sheet'
tags: ['development','dotnet', 'cheat sheet']
date: 2023-01-04
---
# .NET Core CLI Cheat Sheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Creating a new solution

```shell
# create a sln file with name of the current folder
dotnet new sln
```

!!! example

    If you run `dotnet new sln` in `/home/robert/Projects/MyProject`, the name of the `sln` file would be `MyProject.sln`.

## Creating a new project
```shell
# create a .csproj file with name of the current folder
dotnet new <template>
```

!!! example

    If you run `dotnet new console` in `/home/robert/Projects/MyProject/MyProject.Exec`, dotnet will create a new console project with a filename of `MyProject.Exec.csproj` at that location.

!!! note

    You can list out template choices via `dotnet new -l`

    ```shell
    Template Name                                 Short Name          Language    Tags                            
    --------------------------------------------  ------------------  ----------  --------------------------------
    ASP.NET Core Empty                            web                 [C#],F#     Web/Empty                       
    ASP.NET Core gRPC Service                     grpc                [C#]        Web/gRPC                        
    ASP.NET Core Web API                          webapi              [C#],F#     Web/WebAPI                      
    ASP.NET Core Web App                          webapp,razor        [C#]        Web/MVC/Razor Pages             
    ASP.NET Core Web App (Model-View-Controller)  mvc                 [C#],F#     Web/MVC                         
    ASP.NET Core with Angular                     angular             [C#]        Web/MVC/SPA                     
    ASP.NET Core with React.js                    react               [C#]        Web/MVC/SPA                     
    Blazor Server App                             blazorserver        [C#]        Web/Blazor                      
    Blazor Server App Empty                       blazorserver-empty  [C#]        Web/Blazor/Empty                
    Blazor WebAssembly App                        blazorwasm          [C#]        Web/Blazor/WebAssembly/PWA      
    Blazor WebAssembly App Empty                  blazorwasm-empty    [C#]        Web/Blazor/WebAssembly/PWA/Empty
    Class Library                                 classlib            [C#],F#,VB  Common/Library                  
    Console App                                   console             [C#],F#,VB  Common/Console                  
    dotnet gitignore file                         gitignore                       Config                          
    Dotnet local tool manifest file               tool-manifest                   Config                          
    EditorConfig file                             editorconfig                    Config                          
    global.json file                              globaljson                      Config                          
    MSTest Test Project                           mstest              [C#],F#,VB  Test/MSTest                     
    MVC ViewImports                               viewimports         [C#]        Web/ASP.NET                     
    MVC ViewStart                                 viewstart           [C#]        Web/ASP.NET                     
    NuGet Config                                  nugetconfig                     Config                          
    NUnit 3 Test Item                             nunit-test          [C#],F#,VB  Test/NUnit                      
    NUnit 3 Test Project                          nunit               [C#],F#,VB  Test/NUnit                      
    Protocol Buffer File                          proto                           Web/gRPC                        
    Razor Class Library                           razorclasslib       [C#]        Web/Razor/Library               
    Razor Component                               razorcomponent      [C#]        Web/ASP.NET                     
    Razor Page                                    page                [C#]        Web/ASP.NET                     
    Solution File                                 sln,solution                    Solution                        
    Web Config                                    webconfig                       Config                          
    Worker Service                                worker              [C#],F#     Common/Worker/Web               
    xUnit Test Project                            xunit               [C#],F#,VB  Test/xUnit                      
    ```

    Some common `<template>` values are:

    * `console`: Console application
    * `classlib`: Class library
    * `gitignore`: Standard `.gitignore` file
    * `web`: Empty web project
    * `mvc`: ASP.NET Core MVC Web App
    * `webapp`: ASP.NET Core Web App that uses Razor Pages
    * `webapi`: ASP.NET Core Web API App

    !!! warning
        Razor Pages are an evolution of WebForms, and therefore comes with many of the disadvantages.

## Adding a Project to a Solution
```shell
# add a project to the solution in the current directory
dotnet sln add <path to csproj file>
```

!!! example

    If you are currently in the `/home/robert/Projects/MyProject` directory which contains `MyProject.sln`, the command `dotnet sln add ./MyProject.Exec/MyProject.Exec.csproj` will add that project to the solution.

## Cleaning a Solution
```shell
# run from solution directory to clean entire solution
# run from project directory to only clean that specific project
dotnet clean
```

## Building a Solution
```shell
# run from solution directory to build entire solution
# run from project directory to only build that specific project
dotnet build
```

## Restore Nuget Packages
```shell
# run from solution directory to restore nuget packages for all projects in the solution
# run from project directory to only restore nuget packages for that specific project
dotnet restore
```

!!! note

    This isn't typically required anymore as `dotnet build` and `dotnet run` will automatically restore nuget packages when required.

## Add a Nuget Package to Project
```shell
# run from project directory to add nuget package reference to that specific project
dotnet add package <package name>
```

!!! example

    ```shell
    # add the latest version of a package:
    dotnet add package Newtonsoft.Json

    #add a specific version of a package to a project:
    dotnet add package Microsoft.Azure.DocumentDB.Core -v 1.0.0
    ```

## List Nuget Packages in a Project
```shell
# run from solution directory to list nuget packages for all projects in the solution
# run from project directory to list nuget packages for that specific project
dotnet list package
```

###  List Outdated Packages
```shell
# run from solution directory to list outdated nuget packages for all projects in the solution
# run from project directory to list outdated nuget packages for that specific project
dotnet list package --outdated
```

## Remove a Nuget Package from a Project
```shell
# run from project directory to remove a nuget package reference from that specific project
dotnet add package <package name>
```

!!! example

    ```shell
    # remove a package from a project:
    dotnet remove package Newtonsoft.Json
    ```

## Add Reference to Another Project
```shell
# add reference to another project to a specific project
dotnet add reference <path to .csproj>
```

## List References
```shell
# list all references for a specific project
dotnet list reference
```

## Remove a Reference to Another Project
```shell
# remove a reference to another project from a specific project
dotnet remove <path to .csproj>
```

## Run a Project
```shell
# run from project directory to start an application (console app, web app, etc.)
dotnet run
```

!!! tip "Passing in Arguments"

    If you need to pass in arguments when running the application using `dotet run`, you'll need to use the following syntax:

    ```shell
    # dotnet run <any dotnet CLI args> -- <application args>
    # e.g. 'val1' and 'val2' are args[0] and args[1] passed into console application's
    # private static int Main(string[] args)
    dotnet run -- val1 val2
    ```

## Publish a Project
There are two main types of application publishing in:

1. Self-contained deployments (SCD)
2. Framework-dependent executables (FDE)

!!! note

    FDEs are now the default in .NET Core 3.0 and up.

### RIDS (Runtime Identifier)
When publishing your application you have the option of targeting a particular runtime environment (OS, CPU architecture, etc.).

!!! tip

    If you do not specify an RID, .NET will attempt to using one matching the host machine you are publishing from.

Below are same common values:

#### Windows RIDs
Only common values are listed. For the latest and complete version, see the [runtime.json](https://github.com/dotnet/runtime/blob/main/src/libraries/Microsoft.NETCore.Platforms/src/runtime.json) file in the dotnet/runtime repository.

Windows, not version-specific
:   * `win-x64`
    * `win-x86`
    * `win-arm`
    * `win-arm64`

Windows 7 / Windows Server 2008 R2
:   * `win7-x64`
    * `win7-x86`

Windows 8.1 / Windows Server 2012 R2
:   * `win81-x64`
    * `win81-x86`
    * `win81-arm`

Windows 11 / Windows Server 2022 / Windows 10 / Windows Server 2016
:   * `win10-x64`
    * `win10-x86`
    * `win10-arm`
    * `win10-arm64`

!!! note

    There are no win11 RIDs, use win10 RIDs for Windows 11.

#### Linux RIDs
Only common values are listed. For the latest and complete version, see the runtime.json file in the dotnet/runtime repository. Devices running a distribution not listed below may work with one of the not-distribution-specific RIDs. For example, Raspberry Pi devices running a Linux distribution not listed can be targeted with linux-arm.

Linux, not distribution-specific
:   `linux-x64` (Most desktop distributions like CentOS, Debian, Fedora, Ubuntu, and derivatives)

    `linux-musl-x64` (Lightweight distributions using musl like Alpine Linux)

    `linux-arm` (Linux distributions running on Arm like Raspbian on Raspberry Pi Model 2+)

    `linux-arm64` (Linux distributions running on 64-bit Arm like Ubuntu Server 64-bit on Raspberry Pi Model 3+)

### Self-contained deployments (SCD)
These types of deployments package up everything, including the runtime.

Advantages:

* No need to have the .net core runtime installed on the host OS

Disadvantages:

* Larger deployment package size
* Does not take advantage of patched .NET core runtimes, as runtime is included directly in deployment packages

```shell
# publish and created an SCD
dotnet publish <path to csproj> -c <publish config> -r <RID> --self-contained true -o <path to output dir>
```

!!! example "Creating a SDE executable that is meant to run on linux-x64 hosts"

    ```shell
    dotnet publish ./MyProject.Run/MyProject.Run.csproj -c Release -r linux-x64 --self-contained true -o ./output
    ```

### Framework-dependent executables (FDE)
These types of deployments rely on the .NET core runtime being preinstalled on the host OS.

Advantages:

* Smaller deployment package size
* Uses the latest patched version of the .net core runtime installed on the host OS

Disadvantages:

* The major version of the .NET core runtime that the application targets must be installed (e.g. 6.x, 7.x)

```shell
# publish and created an FDE
dotnet publish <path to csproj> -c <publish config> -r <RID> -o <path to output dir>
```

!!! example "Creating a FDE that using the same RID as the publishing host"

    ```shell
    dotnet publish ./MyProject.Run/MyProject.Run.csproj -c Release -o ./output
    ```

## Additional Tips and Tricks
Below are additional tips and tricks that I've found useful over the years.

### Purge all Binaries
This purges out all binaries for a given solution, slimming down the overall size on disk.  This is great for archiving older .NET solutions.

```powershell title="PowerShell"
# run from the solution directory
Get-ChildItem -inc bin,obj -rec | Remove-Item -rec -force
```

See also:

* <https://docs.microsoft.com/en-us/dotnet/core/tools>
* <https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet>
* <https://github.com/NuGet/Home/wiki/dotnet-list-package>
* <https://docs.microsoft.com/en-us/dotnet/core/deploying>
* <https://docs.microsoft.com/en-us/dotnet/core/deploying/deploy-with-cli>
* <https://docs.microsoft.com/en-us/dotnet/core/rid-catalog>