---
title: '.NET Core CLI Cheatsheet'
tags: ['development','dotnet', 'cheatsheet']
---
## Creating a new solution
`dotnet new sln`

Will create a `*.sln` file with name of the current folder.  

e.g. if you are creating the `sln` file in `/home/robert/Projects/MyProject` the name of the `sln` file would be `MyProject.sln`.

## Creating a new project
`dotnet new <template>`

Will create a `*.csproj` file with name of the current folder.

e.g. if you run `dotnet new console` in the `/home/robert/Projects/MyProject/MyProject.Exec` directory, dotnet will create a new console project with a filename of `MyProject.Exec.csproj` at that location.

You can list out template choices via `dotnet new -l`

```
Templates                                         Short Name               Language          Tags                                 
----------------------------------------------------------------------------------------------------------------------------------
Console Application                               console                  [C#], F#, VB      Common/Console                       
Class library                                     classlib                 [C#], F#, VB      Common/Library                       
WPF Application                                   wpf                      [C#]              Common/WPF                           
WPF Class library                                 wpflib                   [C#]              Common/WPF                           
WPF Custom Control Library                        wpfcustomcontrollib      [C#]              Common/WPF                           
WPF User Control Library                          wpfusercontrollib        [C#]              Common/WPF                           
Windows Forms (WinForms) Application              winforms                 [C#]              Common/WinForms                      
Windows Forms (WinForms) Class library            winformslib              [C#]              Common/WinForms                      
Worker Service                                    worker                   [C#]              Common/Worker/Web                    
Unit Test Project                                 mstest                   [C#], F#, VB      Test/MSTest                          
NUnit 3 Test Project                              nunit                    [C#], F#, VB      Test/NUnit                           
NUnit 3 Test Item                                 nunit-test               [C#], F#, VB      Test/NUnit                           
xUnit Test Project                                xunit                    [C#], F#, VB      Test/xUnit                           
Razor Component                                   razorcomponent           [C#]              Web/ASP.NET                          
Razor Page                                        page                     [C#]              Web/ASP.NET                          
MVC ViewImports                                   viewimports              [C#]              Web/ASP.NET                          
MVC ViewStart                                     viewstart                [C#]              Web/ASP.NET                          
Blazor Server App                                 blazorserver             [C#]              Web/Blazor                           
ASP.NET Core Empty                                web                      [C#], F#          Web/Empty                            
ASP.NET Core Web App (Model-View-Controller)      mvc                      [C#], F#          Web/MVC                              
ASP.NET Core Web App                              webapp                   [C#]              Web/MVC/Razor Pages                  
ASP.NET Core with Angular                         angular                  [C#]              Web/MVC/SPA                          
ASP.NET Core with React.js                        react                    [C#]              Web/MVC/SPA                          
ASP.NET Core with React.js and Redux              reactredux               [C#]              Web/MVC/SPA                          
Razor Class Library                               razorclasslib            [C#]              Web/Razor/Library/Razor Class Library
ASP.NET Core Web API                              webapi                   [C#], F#          Web/WebAPI                           
ASP.NET Core gRPC Service                         grpc                     [C#]              Web/gRPC                             
dotnet gitignore file                             gitignore                                  Config                               
global.json file                                  globaljson                                 Config                               
NuGet Config                                      nugetconfig                                Config                               
Dotnet local tool manifest file                   tool-manifest                              Config                               
Web Config                                        webconfig                                  Config                               
Solution File                                     sln                                        Solution                             
Protocol Buffer File                              proto                                      Web/gRPC
```

Some common `<template>` values are:

* `console` - Console application
* `classlib` - Class library
* `gitignore` - Standard `.gitignore` file
* `web` - Empty web project
* `mvc` - ASP.NET Core MVC
* `webapi` - ASP.NET Core Web API

*Note: It's recommended **not** to use `webapp` as this uses "Razor Pages" which are an evolution of WebForms.*

## Adding a Project to a Solution
`dotnet sln add <path to csproj file>`

Will add a project to the solution in the current directory.

e.g. If you are currently in the `/home/robert/Projects/MyProject` directory which contains `MyProject.sln`, the command `dotnet sln add ./MyProject.Exec/MyProject.Exec.csproj` will add that project to the solution.

## Cleaning a Solution
`dotnet clean` from the solution directory to clean the entire solution, or run from a project directory to just clean that project.

## Building a Solution
`dotnet build` from the solution directory to build the entire solution, or run from a project directory to just build that project.

## Restore Nuget Packages
`dotnet restore` from the solution directory to restore Nuget packages for all projects in the solution, or run from a project directory to just restore Nuget packages for that particular project.

*Note: this isn't typically required anymore as `dotnet build` and `dotnet run` will automatically restore Nuget packages when required.*

## Add a Nuget Package to Project
`dotnet add package <package name>` from a project directory to add a Nuget package to a project.

e.g. `dotnet add package Newtonsoft.Json`

You can also add a specific version of a package to a project:

e.g. `dotnet add package Microsoft.Azure.DocumentDB.Core -v 1.0.0`

## List Nuget Packages in a Project
`dotnet list package` from a solution direcotry to list all packages for all projects.  Alternatively run the command from a project directory to list the packages for a specific project.

###  List Outdated Packages
`dotnet list package --outdated` to list out all packages that have updates available.

## Remove a Nuget Package from a Project
`dotnet remove package <package name> to remove a specific package from a project.`

e.g. `dotnet remove package Newtonsoft.Json`

## Add Reference to Another Project
`dotnet add reference <path to .csproj>` to add a reference from one project to another.

## List References
`dotnet list reference` to list all references for a project.

## Remove a Reference to Another Project
`dotnet remove <path to .csproj>` to remove a reference to another project.

## Run a Project
`dotnet run` from a project directory to run that particular project (e.g. console application).

### Passing in Arguments
If you need to pass in arguments when running the application, you'll need to use the following syntax

`dotnet run <any dotnet CLI args> -- <application args>`

e.g.  `dotnet run -- process ./path/to/directory` where `process` and `./path/to/directory` are `args[0]` and `args[1]` in a console application.

## Publish a Project
There are two main types of application publishing in .net core 3.x SDK:

1. Self-contained deployments (SCD)
2. Framework-dependent executables (FDE) **\[Default in .net core 3.x SDK\]**

### RIDS (Runtime Indentifier)
When publishing your application, you typically target a particular runtime environment (host os, CPU architecture, etc.)
Below are same common values:

#### Windows RIDs
Only common values are listed. For the latest and complete version, see the runtime.json file on CoreFX repo.

Portable (.NET Core 2.0 or later versions)
* `win-x64`
* `win-x86`
* `win-arm`
* `win-arm64`

Windows 7 / Windows Server 2008 R2
* `win7-x64`
* `win7-x86`

Windows 8.1 / Windows Server 2012 R2
* `win81-x64`
* `win81-x86`
* `win81-arm`

Windows 10 / Windows Server 2016
* `win10-x64`
* `win10-x86`
* `win10-arm`
* `win10-arm64`

... more~~~~

#### Linux RIDs
Only common values are listed. For the latest and complete version, see the runtime.json file on CoreFX repo. Devices running a distribution not listed below may work with one of the Portable RIDs. For example, Raspberry Pi devices running a Linux distribution not listed can be targeted with linux-arm.

Portable (.NET Core 2.0 or later versions)
* `linux-x64` (Most desktop distributions like CentOS, Debian, Fedora, Ubuntu and derivatives)
* `linux-musl-x64` (Lightweight distributions using musl like Alpine Linux)
* `linux-arm` (Linux distributions running on ARM like Raspberry Pi)

... more

#### macOS RIDs
macOS RIDs use the older "OSX" branding. Only common values are listed. For the latest and complete version, see the runtime.json file on CoreFX repo.

Portable (.NET Core 2.0 or later versions)
* `osx-x64` (Minimum OS version is macOS 10.12 Sierra)

macOS 10.10 Yosemite
* `osx.10.10-x64`

macOS 10.11 El Capitan
* `osx.10.11-x64`

... more

### 1. Self-contained deployments (SCD)
These types of deployments package up everything, including the runtime.  They also created a dedicated executable.

Advantages:
* No need to have the .net core runtime installed on the host OS

Disadvantages:
* Larger deployment package size
* Does not take advantage of patched .net core runtimes, as runtime is included directly in deployment packages
* Must select target platform in advance (OS, CPU architecture, etc.)

To publish: `dotnet publish <path to csproj> -c <publish config> -r <RID> --self-contained true -o <path to output dir>`

e.g. `dotnet publish ./MyProject.Run/MyProject.Run.csproj -c Release -r linux-x64 --self-contained true -o ./Build`

### 2. Framework-dependent executables (FDE)
*Note: For .net core 3.x SDK, this is the default application publishing approach*

These types of deployments rely on the .net core runtime being preinstalled on the host OS.  They also create a dedicated executable.

Advantages:
* Smaller deployment package size
* Uses the latest patched version of the .net core runtime installed on the host OS
* Creates a dedicated executable that can be run indepedently

Disadvantages:
* The major version of the .net core runtime (or later) that the application targets must be installed (e.g. 2.x, 3.x, etc.)
* Must select target platform in advance (OS, CPU architecture, etc.)

To publish: `dotnet publish <path to csproj> -c <publish config> -r <RID> --self-contained false -o <path to output dir>`

e.g. `dotnet publish ./MyProject.Run/MyProject.Run.csproj -c Release -r linux-x64 --self-contained true -o ./Build`

See also:

* <https://docs.microsoft.com/en-us/dotnet/core/tools>
* <https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet>
* <https://github.com/NuGet/Home/wiki/dotnet-list-package>
* <https://docs.microsoft.com/en-us/dotnet/core/deploying>
* <https://docs.microsoft.com/en-us/dotnet/core/deploying/deploy-with-cli>
* <https://docs.microsoft.com/en-us/dotnet/core/rid-catalog>