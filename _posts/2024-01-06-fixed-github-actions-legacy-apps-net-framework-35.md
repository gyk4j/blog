---
layout: post
title:  "Fixed GitHub Actions for legacy apps on .NET Framework 3.5"
---

Happy to announce that a fix for building legacy apps targeting .NET Framework 
3.5. This fix has been applied and tested on [WRECK.NET][wreck-net].

# Problem Background

A little background on why GitHub Actions was not working previously. In Nov 
2023, the earliest GitHub-hosted Runner based on Windows Server was Windows 
Server 2019, which comes with .NET Framework 4.7.2 pre-installled by default.
Anyone trying to build a more modern .NET Framework 4 should not encounter any
problem. For building legacy apps targeting .NET Framework 3.5 and earlier, the 
MSBuild that comes with .NET 4.7.2 would give up when given a Solution (`.sln`)
or Project (`.csproj`) of a earlier format version and refuse to build it.

Worse, it would report a misleading error message that the project XML file is
corrupt (e.g. "a missing root element"). In any case, it indicates that newer 
MSBuild do not recognize or are backward-compatible with files generated for 
earlier versions of Visual Studio.

However, we can supply the same set of solution and project files to the MSBuild
from .NET Framework 3.5 and it would build successfully without warnings or 
errors. Thus, we can be confident that the files are actually working, except
newer MSBuild do not accept them. In my opinion, it would have been better for
MSBuild to report a more accurate error message even if it does not provide 
backward compatibility for an older solution or project file in an earlier 
format.

# Installing .NET Framework 3 on Windows 2019 GitHub Runner

The GitHub Actions workflow is written in a YAML file, and in Windows-based 
runner, the commands are in PowerShell syntax rather than Linux shell commands.

To ensure we have the required .NET Framework 3.5 tools to build our legacy 
solution and projects, we can 
[customize the standard runner image][customize-runner] each time it is spun up 
in a CI workflow. In other words, we have to figure out how to install .NET 
Framework 3 on a Windows Server 2019 system.

Following research on how to install .NET Framework 3 on Windows 2019 in 
PowerShell, we can [enable an optional Windows feature][enable-optional-feature] 
to get it installed:

```yaml
jobs:
  build:
    runs-on: windows-2019
    steps:
      - name: "Install .NET Framework 3.5"
        run: Enable-WindowsOptionalFeature -Online -FeatureName "Netfx3" -All
      - name: "Build"
        run: C:\Windows\Microsoft.NET\Framework\v3.5\MSBuild.exe solution.sln
  
```

> **Note**:  
> There are some discussions online that tell us to enable 
> `net-framework-core` instead of `Netfx3`. Perhaps there are some versions of
> Windows that name .NET Framework 3 differently. This needs to be verified.
>  
> As far as I am concerned, `Netfx3` is working for me on Windows 2019 for now.

There is another solution variant that suggests using a 
[Chocolatey community package][choco-install-dotnet35].

Combining information from [customizing GitHub runner][customize-runner] and 
[Chocolatey dotnet3.5 package][choco-install-dotnet35], we can achieve the same 
effect with:

```yaml
jobs:
  build:
    runs-on: windows-2019
    steps:
      - name: "Install .NET Framework 3.5"
        run: choco install dotnet3.5 
      - name: "Build"
        run: C:\Windows\Microsoft.NET\Framework\v3.5\MSBuild.exe solution.sln
```
However, I have not tested the above method.

# Longer build time

Something that I need to mention is while we manage to install .NET Framework 
3.5 and build our legacy solution and projects successfully, the downloading and 
installation of .NET Framework 3.5 is repetitive and time-consuming.

On a stateless GitHub runner that has to repeat this re-installation every time 
it is run, this means a minimum build time of 5 to 7 minutes is inevitable. This
is terribly inefficient and a waste of network bandwidth.

While GitHub offers [dependencies caching][caching-dependencies] for files, I 
have not found a way to save a GitHub-hosted runner's running state with 
software pre-installed as a system image or virtual machine snapshot.

# Side note: a bad advice is worse than no advice

There are some suggested fixes online that confuse the older Windows .NET 
Framework with the newer cross-platform .NET Core. These bad advice may lead 
people down the wrong path of resolving their classic .NET Framework problem.

For the modern .NET Core, one would use the 
[setup-dotnet][setup-dotnet] action to install the required .NET Core SDK CLI 
tools. This is readily supported and widely described.

# Good times may not last

As soon as Microsoft figures out people are still using their GitHub Actions
to build legacy applications, which reduces developers' inclination to upgrade 
to the latest .NET Core, .NET Standard and Visual Studio, perhaps it may try 
more ways to make installation of .NET Framework 3.5 on newer Windows versions
impossible.

Thus this solution may not last forever, especially when the Windows 2019 runner 
image becomes deprecated sometime in the future. Enjoy while it lasts folks!

[wreck-net]: https://github.com/gyk4j/wreck-net
[enable-optional-feature]: https://learn.microsoft.com/en-us/powershell/module/dism/enable-windowsoptionalfeature?view=windowsserver2019-ps
[customize-runner]: https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/customizing-github-hosted-runners
[choco-install-dotnet35]: https://community.chocolatey.org/packages/DotNet3.5
[caching-dependencies]: https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows
[setup-dotnet]: https://github.com/actions/setup-dotnet
