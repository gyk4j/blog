---
layout: post
title:  "Avoiding batch/shell scripting: a case of over-engineering?"
---

# Avoiding batch/shell scripting: a case of over-engineering?

Was not expecting to blog today but had a epiphany while in the bathroom.

I was wondering why I have a tendency to write complete application and programs rather than
quick and easy scripts using Windows PowerShell, batch files and shell scripts. I questioned
myself whether it is a typical case of ["if all you have is a hammer, everything looks like a nail."][proverb]

Then it dawned upon me that I was trying to buy myself insurance by making them as cross-platform
as possible by being as least dependent on a specific CPU architecture or operating system as 
possible, unless there is a good reason for that. For example, needing to access hardware through
the OS or some low level Windows API or Linux system calls to get things done.

So at the very least write even the simplest thing as a [Python][python] or 
[Perl][perl] script, instead of [Bash shell script][bash-script] or a 
[Windows PowerShell][powershell]. We never know if one day we would be forced to change a CPU 
architecture or operating system that our old trusty scripts/apps/programs run on.

If I am afraid of having to move away from Windows one day, and are thus avoiding coding
[PowerShell][powershell] or [Windows Active Scripts (WSH)][windows-script-host], why not just 
write plain Bash shell scripts? Well, for one, old versions of Windows do not run Bash shell 
scripts, and it is probably a crazy idea to install [Cygwin][cygwin] to emulate a Unix/Linux 
environment for the sake of running a tiny 1KB script.

Ah, then what about writing them in [Java][java] or [.NET][dotnet]? 

Well, for small scripts. I have my love-hate relationship with modern Java in recent years, 
with its growing Java Virtual Machine (JVM) bloat. Each time I package my app, it is a 100KB 
app, with 20MB of dependencies that requires a huge Java Runtime Environment despite its best 
attempt at slimming down with [Java 9 Platform Module System (JPMS)][jpms] and 
[Native Image][native-image].

And .NET? it is pretty much a Microsoft-controlled platform. Great if one is writing for
Windows. Sure, it touts the recent "cross-platform" [.NET Core][dotnet-core] and 
[.NET Standard][dotnet-std], but let's face it, I would hesitate to force a user to install
a 200MB .NET or [Mono Framework][mono-framework] to run my tiny 100KB tool. 

The tradeoff is [Python][python] and [Perl][perl] are about 20MB in size, that's about the max 
tradeoff I can stomach. For all other things, a compact native binary executable is probably 
the best option.

Sure some people would think if and when that day truly ever arrives, then we should worry and 
actually prepare for it. In most probable cases, such situation never happens, which is true.

Just a personal preference. But does anyone care? Great that nobody cares anyway.

Perhaps I may be forced to change my mind again soon.

[proverb]: https://en.wiktionary.org/wiki/if_all_you_have_is_a_hammer,_everything_looks_like_a_nail
[python]: https://www.python.org
[perl]: https://www.perl.org
[bash-script]: https://www.shellscript.sh/
[powershell]: https://learn.microsoft.com/en-us/powershell/
[windows-script-host]: https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc738350(v=ws.10)
[cygwin]: https://www.cygwin.com/
[java]: https://www.java.com/
[dotnet]: https://dotnet.microsoft.com/en-us/learn/dotnet/what-is-dotnet
[jpms]: https://www.oracle.com/sg/corporate/features/understanding-java-9-modules.html
[native-image]: https://docs.oracle.com/en/graalvm/enterprise/22/docs/reference-manual/native-image/guides/build-java-modules-into-native-executable/
[dotnet-core]: https://learn.microsoft.com/en-us/dotnet/core/introduction
[dotnet-std]: https://learn.microsoft.com/en-us/dotnet/standard/net-standard
[mono-framework]: https://www.mono-project.com/
