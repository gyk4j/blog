---
layout: post
title:  Retrocoding "Hello World" for Windows with Dave Plumer
youtubeId: "JlZe2JwrJqM"
---

I was as usual slacking on YouTube when I decided to catch some videos on 
programming using the Windows/Win32 API. I have never gone deep into Windows 
API, though I had a cursory glance at it and was turned off by the amount of
template codes required for the window class registration and message pump loop.
I chose to ignore it by choice actually.

I went the other way and focused on Java and started on [Java AWT][awt]. Despite 
looking around for Java GUI libraries, nothing ever quite looked native or 
natural enough for commercial grade application software running on Windows. 
Sure Java desktop apps work, but I was never satisfied. They looked OK, but not
great even though the [Java Swing][swing] tries to emulate the native look as 
closely as it could. Eventually, [JavaFX][javafx] arrived, but Sun/Oracle 
decided to be unpretentious about wanting to make Java apps look any bit native 
at all; they decided to adopt a common look-and-feel for all platforms that is 
decidedly worse.

Those that looked decent professionally were written natively in Visual C++ or 
C# for .NET. But one would be reliant on the tools from Microsoft. Luckily 
though, the Mingw GCC compiler supports Windows development, but it does not 
provide the proprietary [Microsoft Foundation Class (MFC) Library][mfc] or 
or [Active Template Library (ATL)][atl] that many professional software built 
on Visual C++ require. It does however, provide the standard Windows/Win32 API 
header and library files. So anyone doing Windows programming on Mingw would 
have to do it the low level way without MFC or ATL.

Back to the Dave's video below. Dave is a former Microsoft software engineer
way back in the Windows 95 days. He's still pretty proud of it today and does
not forget to remind us of it in his opening line in every of his YouTube video.

By the way, there is *absolutely no value* in spending time learning 
Windows/Win32 API programming except for intellectual curiosity about how things
work underneath the glossy cover of the glorious Visual Studio IDE/Visual Studio 
Code. Most professional programmers doing serious development for Windows apps
do not do this line-by-line by hand anymore; they would largely use the IDE's
WYSIWYG designer tool to design and build any GUI. So don't waste your time on
going deep on Windows/Win32 API and handcoding a classic Windows desktop app 
from the first line which the IDE can provide the skeleton template codes. In 
all likelihood, it can't get one a job.

However, knowing the Windows API is worthwhile if you need to know if there's
something that the Windows OS API can provide for an application when there is
an advanced or esoteric need.

In any case, sit back and enjoy the video if you're still interested.

{% include youtubePlayer.html id=page.youtubeId %}

As a post-video note, it seems to me that this video was truncated to keep the 
length short enough within a limit. It is not coding a Windows app from scratch 
using just a plain text editor without Visual Studio as promised. This is 
notable towards the end when some Visual Studio generated project files for 
MSBuild like `.vcxproj` files appear out of nowhere and are used to build the 
project. There is no mention of them being created earlier in the video, and
MSBuild is invoked to rapidly build the entire project before the video 
concludes.

I was expecting a `makefile` of some kind that is created manually.

[awt]: https://en.wikipedia.org/wiki/Abstract_Window_Toolkit
[swing]: https://en.wikipedia.org/wiki/Swing_(Java)
[javafx]: https://en.wikipedia.org/wiki/JavaFX
[mfc]: https://en.wikipedia.org/wiki/Microsoft_Foundation_Class_Library
[atl]: https://en.wikipedia.org/wiki/Active_Template_Library