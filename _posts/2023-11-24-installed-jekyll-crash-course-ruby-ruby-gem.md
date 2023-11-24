---
layout: post
title:  "Installed Jekyll but a Crash Course in Ruby and Ruby Gems"
---
Decided to play with GitHub Pages. And it suggested Jekyll for blogging. So I
thought, "why not?"

Took a look at [Jekyll site](https://jekyllrb.com/). The 
**Quick start Instructions** looked cheery and simple enough. Just *4 commands* 
to a running web site. Should be all done in 1 minute. *Right?* :)

![Jekyll Quick Start Instructions](/blog/assets/images/jekyll-quick-start-instructions.png)

*Big Mistake*

What it does not tell you is it assumes and expects a working Ruby development
environment already installed and configured properly. On a clean Linux 
installation like mine, be prepared to do more than the above 4 steps. 

*A lot more.*

# Problem 1: Ruby `gem` cannot be found

{% highlight shell %}
~ $ gem install bundler jekyll
bash: gem: command not found
{% endhighlight %}

OK, maybe Ruby wasn't installed and it needed just a little help.

{% highlight shell %}
~ $ sudo dnf install ruby
~ $ gem install bundler jekyll
{% endhighlight %}

> Building native extensions. This could take a while...  
> ERROR: Error installing bundler:  
> ERROR: Failed to build gem native extension.

# Problem 2: Failed to build gem native extension

Now gem was found. It started to run. But a different error appeared. It 
couldn't build gem native extension. 

Googling told me [Ruby requires additional packages](https://talk.jekyllrb.com/t/error-failed-to-build-gem-native-extension/8629).
And [more](https://stackoverflow.com/questions/20559255/error-while-installing-json-gem-mkmf-rb-cant-find-header-files-for-ruby).
And [more packages](https://stackoverflow.com/questions/13767725/unable-to-install-gem-failed-to-build-gem-native-extension-cannot-load-such).
And [even more packages](https://darryldias.me/2019/install-development-tools-fedora/). 

Forced to read 
[Jekyll installation docs](https://jekyllrb.com/docs/installation/) 
for more prerequisites.

For some reasons, C++ support is not included in the **"Development Tools"** 
group by default. Found this out after installing it.

{% highlight shell %}
~ $ ruby -v
ruby 3.2.2 (2023-03-30 revision e51014f9c0) [x86_64-linux]
~ $ gem -v
3.4.10
~ $ gcc -v
...
gcc version 13.2.1 20231011 (Red Hat 13.2.1-4) (GCC)
~ $ make -v
GNU Make 4.4.1
...
~ $ g++ -v
bash: g++: command not found
{% endhighlight %}

So it has to be in an additional separate installation.

For Fedora/CentOS:

{% highlight shell %}
~ $ sudo dnf install ruby-devel
~ $ sudo dnf -y groupinstall "Development Tools"
~ $ sudo dnf install gcc-c++
{% endhighlight %}

For Debian/Ubuntu:

{% highlight shell %}
~ $ sudo apt install ruby-dev
~ $ sudo apt install build-essential
{% endhighlight %}

# Problem 3: No write permission to `gem` install locations

OK, here we go again.

{% highlight shell %}
~ $ gem install bundler jekyll
{% endhighlight %}

And then it started spewing error messages.

> You don't have write permission for /var/lib/gem/...  
> You don't have write permission for /usr/share/gems/...

Well, it seems like the installation is trying to write to some system-wide
locations? Let me fix the permissions then.

{% highlight shell %}
~ $ sudo chmod -R 770 /var/lib/gems
~ $ sudo chmod -R 770 /usr/share/gems
...
~ $ gem install bundler jekyll
{% endhighlight %}

Still extension install failed.

# Problem 4: `gem install` refused to complete as sudo-er

Let me try sudo then. Not knowing any better, I ran as sudo.

{% highlight shell %}
~ $ sudo gem install bundler jekyll
{% endhighlight %}

> Don't run Bundler as root.  
> Bundler can ask for sudo if it is needed, and installing your bundle as root 
> will break this application for all non-root users on this machine.

Made some progress, but it aborted at the end.

By now I was confused. If I didn't run as a `sudo`, it would have a write 
permission issue. If I ran as `sudo`, `gem` would complain about running as 
root and refusing to complete the gem install. It was a case of "damned if you 
do, and damned if you don't."

# Problem 5: Missing `psych` gem and `libyaml`

After a series of `dnf install`, `dnf remove`, `gem install` and `gem uninstall` 
as I wrestled with dependency issues, Ruby became too corrupted. 

Along the way, I also encountered:

> It seems your ruby installation is missing psych (for YAML output).  
> To eliminate this warning, please install libyaml and reinstall your ruby.

So I tried:

{% highlight shell %}
~ $ gem install psych
{% endhighlight %}

> Building native extensions. This could take a while.  
> ERROR: Error installing psych:  
>        ERROR: Failed to build gem native extension.  
> checking for yaml.h... no  
> yaml.h not found  
> *** extconf.rb failed ***  
> Could not create Makefile due to some reason, probably lack of necessary  
> libraries and/or headers. Check the mkmf.log file for more details. You may  
> need configuration options.  

So it was missing `libyaml`. Installed and still it did not work. A further 
retry made me install `libyaml-devel` as well.

{% highlight shell %}
~ $ sudo dnf install libyaml libyaml-devel
{% endhighlight %}

Finally, doing a re-installation fixed the problem.

{% highlight shell %}
~ $ sudo dnf remove ruby
~ $ sudo dnf install ruby
{% endhighlight %}

# Problem 6: Installed gems as root and user 

Finally, I had to add some lines to my `.bashrc` to tell `gem` where to place 
the gems as a user install. 

{% highlight shell %}
export GEM_HOME=$HOME/gems
export PATH=$GEM_HOME/bin:$PATH
{% endhighlight %}

Doing a user install of Jekyll now worked without worrying error messages of 
failures.

{% highlight shell %}
~ $ gem install bundler jekyll
{% endhighlight %}

Previously because I experimented with `gem install` as both sudo-er and a 
normal user, it means I have two copies of identical gem files scattered in user
and system locations.

By now, I could not be bothered to figure out how to clean up the gem files that
should not be there in the system directories.

# Closing Words

I will cover the lessons learnt in a different shorter post another day. The
number of problems encountered is far too many today to also cover the lessons 
in a single post, not that I have fully resolved and understood everything about
Ruby and Ruby Gems by now.

Oh my, it was a hell of a ride baby! Descended into hell really. All I wanted 
was [Jekyll](https://www.jekyllrb.com) and I thought it was a *4 command* 
installation.

![Jekyll Quick Start Instructions](/blog/assets/images/jekyll-quick-start-instructions.png)
