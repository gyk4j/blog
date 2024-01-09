---
layout: post
title:  "GitHub Actions build dependency caching for .NET Framework 3.5"
---

Happy to announce that my experiment (thus far) with caching an installed .NET
Framework 3.5 as a build tool for [WRECK.NET][wreck-net] is successful.

Essentially I followed the 
[instructions for caching dependencies][caching-dependencies] and adapted 
accordingly.

Luckily the complications that I feared did not materialize. These include 
the .NET Framework installation writing critical registry keys and values, or
installing files outside of the cached `C:\Windows\Microsoft.NET`, which it 
may require for proper operation.

For now, simply caching and restoring the installed .NET Framework tools and 
files  under the following directories would still build the solution and
projects successfully:

1. `C:/Windows/Microsoft.NET/Framework/v2.0.50727`
2. `C:/Windows/Microsoft.NET/Framework/v3.0`
3. `C:/Windows/Microsoft.NET/Framework/v3.5`

The significant step that I have added to enable dependency caching is:

{% highlight yaml %}
{% raw %}
jobs:
  build:
    runs-on: windows-2019
    steps:
      - name: "Cache .Net Framework 3.5"
        id: cache-net-framework-35
        uses: actions/cache@v3
        env:
          cache-name: cache-net-framework-35
        with:
          path: |
            C:/Windows/Microsoft.NET/Framework/v2.0.50727
            C:/Windows/Microsoft.NET/Framework/v3.0
            C:/Windows/Microsoft.NET/Framework/v3.5
          key: ${{ runner.os }}-build-${{ env.cache-name }}

      - if: ${{ steps.cache-net-framework-35.outputs.cache-hit != 'true' }}    
        name: "Setup .NET Framework 3.5"
        continue-on-error: true
        run: Enable-WindowsOptionalFeature -Online -FeatureName "NetFx3" -All
{% endraw %}
{% endhighlight %}

![Dependency cache](/blog/assets/images/2024-01-09-dependency-cache.png)

Although now I have a 32 MB cache -- which is actually not too big in my opinion
-- my build time has been sped up from about 6-7 minutes to 1 minute! This 
caching is certainly well-worth the time-savings for repeated builds.

What a lucky day indeed! 

[wreck-net]: https://github.com/gyk4j/wreck-net
[caching-dependencies]: https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows