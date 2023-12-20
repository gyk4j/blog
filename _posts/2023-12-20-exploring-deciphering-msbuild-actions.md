---
layout: post
title:  "Exploring and deciphering MSBuild actions"
---
I ran into quite a few problems when testing my [7-Zip][7-zip] integration with [WRECK.NET][wreck-net]. Mostly with support for newer 
RAR5 and Microsoft Installer `.msi` format archives, and other oddities with the date time stamps reported within the archives. These 
includes seeing some files while opening the archive in v16.04, but find that their timestamps were not extracted or collected
by the test program.

I was opening up the sample test files with [7-Zip][7-zip] v16.04, but the embedded DLL that comes with the 
[SevenZipSharp][seven-zip-sharp] is v16.00. Some date time entries looked different during testing. It seemed to me, on surface that there had been a few bugfixes or changes between v16.00 and v16.04, which allow v16.04 to open and browse RAR5 and MSI files
successfully, while v16.00 fails to open them, or correctly find all the file entries their timestamps inside.

However, [SevenZipSharp][seven-zip-sharp] library is an abandonware. It was last updated in 12 May 2016, 7 years ago.

To update it with more recent versions of 7-Zip DLL, I guess we would have to do it ourselves. Which brings me examine the `SevenZip.csproj` that comes with the project to study how the build process is like in order to embed (or to not embed) the compressed `7z.dll` into the resulting `SevenZipSharp.dll` .NET assembly.

A quick glance at the `XML` format project file told me that **[PropertyGroup][propertygroup]** tags seem to indicate available build 
targets given the **Configuration** and **Platform** setting as provided by the IDE or MSBuild command line.

However, they do not indicate how each files or assets in the projects are actually handled.

A series of **[ItemGroup][itemgroup]** tags follow with no name or label. It was unclear how the selected 
**[PropertyGroup][propertygroup]** would select the files in the right **[ItemGroups][itemgroup]** which include the files to be 
processed (e.g. compile, copied, embedded) in the build outputs.

Some Googling led me to a good explanation of the various actions that can be found in [Learn Microsoft][learn-microsoft] and 
[StackOverflow][build-actions]. The action is specified by the tag used, and these actions are limited to the few that Microsoft provides
in [MSBuild][msbuild].

So the actions are individually specified for each item regardless of the **[PropertyGroup][propertygroup]** chosen. I have reproduced 
the list of actions below for quick reference.

> **None**: The file is not included in the project output group and is not compiled in the build process. An example is a text file that
> contains documentation, such as a Readme file.
>  
> **Compile**: The file is compiled into the build output. This setting is used for code files.
>
> **Content**: Allows you to retrieve a file (in the same directory as the assembly) as a stream via Application.GetContentStream(URI).
> For this method to work, it needs a AssemblyAssociatedContentFile custom attribute which Visual Studio graciously adds when you mark a
> file as "Content"
>  
> **Embedded resource**: Embeds the file in an exclusive assembly manifest resource.
>  
> **Resource** (WPF only): Embeds the file in a shared (by all files in the assembly with similar setting) assembly manifest resource
> named AppName.g.resources.
>  
> **Page** (WPF only): Used to compile a xaml file into baml. The baml is then embedded with the same technique as Resource (i.e.
> available as `AppName.g.resources)
>  
> **ApplicationDefinition** (WPF only): Mark the XAML/class file that defines your application. You specify the code-behind with the
> x:Class="Namespace.ClassName" and set the startup form/page with StartupUri="Window1.xaml"
>  
> **SplashScreen** (WPF only): An image that is marked as SplashScreen is shown automatically when an WPF application loads, and then
> fades
>  
> **DesignData**: Compiles XAML viewmodels so that usercontrols can be previewed with sample data in Visual Studio (uses mock types)
>  
> **DesignDataWithDesignTimeCreatableTypes**: Compiles XAML viewmodels so that usercontrols can be previewed with sample data in Visual
> Studio (uses actual types)
>  
> **EntityDeploy**: (Entity Framework): used to deploy the Entity Framework artifacts
>  
> **CodeAnalysisDictionary**: An XML file containing custom word dictionary for spelling rules
>   

[wreck-net]: https://github.com/gyk4j/wreck-net
[7-zip]: https://www.7-zip.org
[seven-zip-sharp]: https://github.com/StevenBonePgh/SevenZipSharp
[build-actions]: https://stackoverflow.com/questions/145752/what-are-the-various-build-action-settings-in-visual-studio-project-properties
[learn-microsoft]: https://learn.microsoft.com/en-us/visualstudio/msbuild/itemgroup-element-msbuild?view=vs-2022
[msbuild]: https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild?view=vs-2022
[itemgroup]: https://learn.microsoft.com/en-us/visualstudio/msbuild/itemgroup-element-msbuild?view=vs-2022
[propertygroup]: https://learn.microsoft.com/en-us/visualstudio/msbuild/propertygroup-element-msbuild?view=vs-2022
