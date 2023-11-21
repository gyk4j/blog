---
layout: post
title:  "Secured Github with 2 Factor Authentication and SSH"
date:   2023-11-21 08:30:00 +0800
categories: devops
---
Github has been bugging me about enabling 2FA on my newly-created account since 
beginning of November. I am both bemused and amused that anyone would want to
hack my Github account, and Microsoft has *threatened* to restrict my access if
I do not comply. So I guess I don't have a choice.

A more detailed writeup of how I use SSH for Git authentication for repositories
is covered in an earlier 
[blog post]({% post_url 2023-11-20-switched-https-to-ssh %}).

# Preface

This post is more of an explanation of how I made my choices of technology for
my Github security setup. Yes, there are options that a Github user has to 
choose when securing his account or repository.

# Assumptions

Note that my assumption is that *I am a low value target*. Ease-of-use,
personal privacy and avoiding vendor lock-ins are prioritised over security. 

## Ease-of-Use
I just want to get it over with and move on to other more important things I 
wish to do with my time instead. 

## Privacy
I also do not wish to surrender more personal information about myself to 
Microsoft-owned Github or the public than is necessary to use the service.

## Vendor Lock-In
Lastly, I do not wish to become dependent on Github proprietary technologies if 
I have to move one day. Sure, the tools are user-friendly, use them if you like
them, but I personally hope to stick to only the basic `git` and `bash` command 
line tools if possible, instead of learning [Github CLI](https://cli.github.com) 
or [Github Desktop GUI](https://desktop.github.com). Sooner or later, Microsoft
may force everyone onto [Github Copilot](https://github.com/features/copilot) 
and [Codespaces](https://docs.github.com/en/codespaces) whether one likes it or 
not.

Thus, the core aims are:

- Maximum ease-of-use
- Minimal security
- Maximize privacy
- Maximize portability

# 2 Factor Authentication for GitHub Account

For 2FA, we are given a choice of TOTP or SMS. The default offered is a TOTP 
application on mobile phone or a browser extension.

I chose SMS instead. 

As usual, Microsoft tries to push its Microsoft Authenticator. Although it also
helpfully mentions other TOTP options like its rival's Google Authenticator. 

I did not discount TOTP app immediately, but I did spend a few hours searching 
through the options. The open source options are mostly unmaintained or buggy. 
Others include cloud storage that sync your secret keys and seeds, something 
which I never got comfortable with not knowing how well protected these services 
are. 

However, I do know data breaches are real and is a regular thing that happens 
every now and then. And there are some TOTP apps that demand permissions like 
geolocation and what's not. So I gave up.

Knowing Microsoft and big techs, and not knowing what kind of telemetry they are 
collecting and uploading behind my back, I would rather avoid installing more
apps, software or browser extensions if possible. So I opted for the less secure
SMS. However, I do have to surrender my phone number to Microsoft though.

# Git Authentication for Repositories

Like most people, I used the default HTTPS URL to `git clone` my repository.

Until I try to push my changes, I would 
[always be greeted by a password prompt](https://docs.github.com/en/github-ae@latest/get-started/getting-started-with-git/why-is-git-always-asking-for-my-password).

Typed in my Github password, nope, it did not work. Googling around told me 
[Github has removed password authentication and I need a Personal Access Token instead](https://github.blog/changelog/2021-08-12-git-password-authentication-is-shutting-down/).

As a randomly generated string of gibberish, it is not something anyone can 
remember and type from memory. I resorted to saving a Personal Access Token in a
text file and copying and pasting it whenever I was prompted for authentication.

And when I thought Git would cache and remember my password or personal access 
token (PAT) after entering it once, it does not and it would not. Again, in true
Microsoft's fashion, 
[it pushes its GitHub CLI and Git Credential Manager (GCM)](https://docs.github.com/en/github-ae@latest/get-started/getting-started-with-git/caching-your-github-credentials-in-git). It seems like the hidden message 
is: to use GitHub smoothly, one is strongly encouraged (or forced) to install 
Microsoft/GitHub's proprietary tools.

To use HTTPS authentication, it may involve firing up a web browser and having to 
log in like it is a web application, and then returning back to the terminal 
shell. If not, to get a more seamless experience, I would need to use the GitHub 
Desktop or CLI instead.

So I have had enough of the complexity of trying to make HTTPS authentication 
work, jumping from command line to web browser, reading through 
[long documentations of configuring `git` to cache credentials](https://git-scm.com/docs/git-credential-cache),
with no guarantee it would work with GitHub possibly proprietary authentication 
system. 

I decided to just use a SSH key. What I need to do is to generate one and upload
its public key to GitHub. And I no longer have to deal with it anymore.

My job is done.
