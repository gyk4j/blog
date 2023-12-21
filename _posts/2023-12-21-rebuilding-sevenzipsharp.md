---
layout: post
title:  "Rebuilding SevenZipSharp with older .NET Framework tools"
---
As mentioned a few days ago, I am using [SevenZipSharp][seven-zip-sharp] which is designed to be built using *.NET Framework 4.0*
out-of-the-box, with some backward support (in theory but untested or unmaintained in reality?) for Visual Studio 2008.

However, I am determined to make it build and run on a plain old Windows 7 machine without installation of additional newer 
Visual Studio SDK. So I will be using *.NET Framework 3.5* or even *.NET Framework 2.0*, installed at 
`C:\Windows\Microsoft.NET\Framework\v3.5` and `C:\Windows\Microsoft.NET\Framework\v2.0.50727` by default.

I decided not to fork the repository, but just download a ZIP copy (`SevenZipSharp-master.zip`) for local experimentation and own use. 
Downloaded and extracted it.

Assuming we're in the `SevenZipSharp-master` folder after ZIP extraction.

# Fail 1: Building Visual Studio 2008 Solution

Out of the box, the solution for Visual Studio 2008 would not build, as expected.
```powershell
C:\Windows\Microsoft.NET\Framework\v3.5\MSBuild.exe SevenZip.sln
```
In either case, trying to build the Solution file would end up trying to build a lot of unnecessary projects that creates even more
unwanted errors. We just need to build the `SevenZip` project in the solution.

# Fail 2: Building using Visual Studio 2010 Solution and Project

Tried the newer solution for Visual Studio 2010, again no luck.
```powershell
C:\Windows\Microsoft.NET\Framework\v3.5\MSBuild.exe SevenZip2010.sln
```

Tried the newer project for Visual Studio 2010, again no luck. The compiler may complain of missing assembly or directives, unsupported 
preprocessor directives or pragmas, or default parameters ([introduced in .NET 4.0 as Optional Parameters][optional-parameter] but not 
3.5).

```powershell
C:\Windows\Microsoft.NET\Framework\v3.5\MSBuild.exe
  SevenZip2010.csproj
  /t:Rebuild
  /p:TargetFrameworkVersion=v2.0
  /p:Configuration=Release
  /p:Platform=AnyCPU
  /tv:3.5
```

*Conclusion: don't bother with the Visual Studio 2010 solution and project file if using .NET 3.5 Framework tools.*

# Fail 3: Use .NET Framework 2.0.50727 MSBuild with Visual Studio 2008 Project

Out of curiosity, I attempted to see how far back we can push it. .NET Framework 2.0 comes with Windows 7 by default.
Let's see if we can build a Visual Studio 2008 project that by default targets .NET Framework 4.0 with .NET Framework 2.0 without 
any problem.

```powershell
C:\Windows\Microsoft.NET\Framework\v2.0.50727\MSBuild.exe
  SevenZip.csproj
  /t:Rebuild
  /p:TargetFrameworkVersion=v2.0
  /p:Configuration=Release
  /p:Platform=AnyCPU
```
*Note the removed `/tv:3.5` switch. It is not supported by the older MSBuild in .NET Framework 2.0 if used.*

```
MSBUILD : error MSB1001: Unknown switch.
Switch: /tv:2.0
```

You will encounter missing files or directory.

```
Error MSB4019: The imported project "C:\Microsoft.CSharp.targets" was not found.
Confirm that the path in the <Import> declaration is correct, and that the file
exists on disk.
```

There is [a fix that requires patching the variable name in the project file for .NET Framework 2.0][MSBuildToolsPath] tools.

From:

```xml
<Import Project="$(MSBuildToolsPath)\Microsoft.CSharp.targets" />
```
To:

```xml
<Import Project="$(MSBuildBinPath)\Microsoft.CSharp.targets" />
```

This can be an option if you are restricted from even using .NET Framework 3.5, like old Windows 2000, XP and Vista machines that
did not have .NET Framework 3.5 pre-installed and you don't have the administrative rights to install it.

But still, the C# compiler will report errors and build will still fail as there are some places in the codes that uses 
[C# 3.0 collection initialization syntax][collection-initialize]. Unless you decide to rewrite these sections in C# 2.0 syntax.

# Success: Use `SevenZip.csproj` for Visual Studio 2008

The project file for Visual Studio 2008 works with .NET Framework 3.5 tools. Great.

```powershell
msbuild SevenZip.csproj
  /t:Rebuild
  /p:TargetFrameworkVersion=v2.0
  /p:Configuration=Release
  /p:Platform=AnyCPU
  /tv:3.5
```
A quick explanation of why the above property switches are defined:

- **TargetFrameworkVersion**: To specifically compile it for use by projects based on .NET Framework as low as 2.0.
  If not, the project file defaults to .NET Framework 4.5.
- **Configuration**: It's better to specify whether we want the *Release* or *Debug* version. If not it will default to the
  first configuration found, and we do not want to rely on guesswork or trial-and-error.
- **Platform**: Make sure our assembly is x64 and x86 enabled since it is pure C# code and should be platform-independent.
- **tv**: Specify tools version to 3.5. If unspecified, it defaults to 4.0 as specified in the project file.
  If .NET Framework 4.0 is not installed, it will report a message:

  ```
  Project file contains ToolsVersion="4.0", which is not supported by this version of MSBuild.
  Treating the project as if it had ToolsVersion="3.5".
  ```

Now you should see a cheery successful build message.
```
Build succeeded.
    0 Warning(s)
    0 Error(s)
```

# Disabling embedding of `7z.dll`

And to make the generated `SevenZipSharp.dll` assembly not embed the older `7z.dll` that comes by default, we need to edit the 
`SevenZip.csproj`. Change the tag.

From:
```xml
<EmbeddedResource Include="x64\7z.dll.gz" />
<EmbeddedResource Include="x86\7z.dll.gz" />
```

To:
```xml
<None Include="x64\7z.dll.gz" />
<None Include="x86\7z.dll.gz" />
```

Then re-run the `msbuild SevenZip.csproj ...` command again with the switches.

We will notice that the original **1.30 MB** output `SevenZipSharp.dll` is now only **160 KB**.

Now we have a .NET assembly that can be used in any old .NET projects stretching as far back as those .NET Framework 2.0 ones developed
in the 2005-2006 era. Plus having decoupled it from the old 7-Zip DLL, we can use newer, more modern version of 7-Zip with less bugs and
more features.

[seven-zip-sharp]: https://github.com/StevenBonePgh/SevenZipSharp
[optional-parameter]: https://stackoverflow.com/questions/7822450/default-parameter-specifiers-are-not-permitted
[MSBuildToolsPath]: https://stackoverflow.com/questions/5694/the-imported-project-c-microsoft-csharp-targets-was-not-found
[collection-initialize]: https://stackoverflow.com/questions/17739060/initialize-a-dictionary-with-values-in-c-sharp-2-0
