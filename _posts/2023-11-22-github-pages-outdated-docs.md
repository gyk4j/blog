---
layout: post
title:  "GitHub Pages Outdated Documentations"
date:   2023-11-22 09:30:00 +0800
categories: devops
---
Had my first try with GitHub Pages. I would say my experience has been anything 
but smooth.

I created 2 web sites: a personal site and a project site.

# Problem 1: Changing theme

According to the GitHub Pages web site, you can use the 
**Theme Chooser &gt; Choose a theme** setting after creating the repository 
under the **Settings &gt; Pages**.

The setting is now missing.

After googling around, there is this announcement that 
[the Theme Chooser has been deprecated in August 2022](https://github.blog/changelog/2022-08-22-github-pages-deprecating-the-theme-picker/),
but the official GitHub Pages landing page still gleefully tells users to go 
"choose a theme" as of Nov 2023. 

![WHat we are told](/blog/assets/images/2023-11-22-github-pages-theme-chooser.png)

And what we actually see. There is no **Theme chooser** anymore.

![WHat we see](/blog/assets/images/2023-11-22-github-pages-reality.png)

It seems like someone forgot to inform the technical writers in GitHub to update
their documentations.

# Problem 2: Jekyll web site not building after pushing changes

The value proposition of GitHub Pages is the ease of getting something 
published on the Web. Push a text file to the repo and it becomes a live blog
post, except when it does not work.

Using the default settings of `main` branch and `/ (root)` directory:

{% highlight shell %}
jekyll new <REPO>
cd <REPO>
git init
git add .
git commit -m "First commit"
git remote set-url git@github.com:<USER>/<REPO>.git
git push
{% endhighlight %}

Pushed and nothing happened. Jekyll did not run or build the web pages.

Then I remember it used to use a special `gh-pages` branch for the web pages. 

So I tried under **Pages &gt; Source**, switched the publishing source to 
`gh-pages` branch and `/ (root)` directory. Then ran:

{% highlight shell %}
git checkout -b gh-pages
git add .
git commit -m "Try gh-pages branch"
git push
{% endhighlight %}

Pushed and again nothing happened. Jekyll did not run or build the web pages.

By now the **Deploy from a branch** did not seem to work, and I had to switch 
to **GitHub Actions** for it to work.

I am not sure if it is due to the transition period from the classic GitHub 
Pages when the automatic building from `gh-pages` branch has been disabled, but 
it did not work.

# What worked: Publishing HTML files using Deploy from a branch

**Deploy from a branch** did work though if I simply pushed HTML files that do
not require Jekyll to run and build. So I am not sure what the pre-conditions 
are to trigger a Jekyll build if one is using **Deploy from a branch**. 
According to
[Jekyll documentation](https://jekyllrb.com/docs/github-pages/), it should work,
but it did not for me.
