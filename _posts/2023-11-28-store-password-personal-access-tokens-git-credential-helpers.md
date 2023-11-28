---
layout: post
title:  "Using Git credential helpers for storing passwords and Personal Access Token"
---
Well, it seems like very soon after I talked about using SSH over HTTPS for git,
I have to eat my words.

Today, I took a look at the 
[documentations at Git-SCM](https://git-scm.com/docs) and was greeted with a 
page discussing 
[Git credential helpers](https://git-scm.com/doc/credential-helpers).

So I tried it out on a GitHub repository and Personal Access Token I generated
previously.

# Step 1: Generate a GitHub Personal Access Token (if you do not have one yet)

*NOTE:* that I have not tried out **Fine-grained tokens** currently in Beta as of 
Nov 2023, so I am using the classic token instead.

1. Log in to GitHub in a web browser.
2. Go to **Menu &gt; Settings**
3. Click **Developer Settings** 
4. Click **Personal access tokens &gt; Tokens (classic)**
5. Click **Generate new token &gt; Generate new token (classic)**
6. Copy-paste and save the generated PAT in a text file somewhere safe 
   (e.g. [Pastebin](https://pastebin.com/)) ;)

# Step 2: Clone a repository using HTTPS

{% highlight shell %}
git clone https://github.com/<USER>/<REPOSITORY>.git
cd <REPOSITORY>
{% endhighlight %}

# Step 3: Configure `git` to use `credential-helper-store`

You can choose between storing the credentials for all Git repositories or just
the current repository only. 

[More info here](https://git-scm.com/book/en/v2/Git-Tools-Credential-Storage).

For current project repository only:

{% highlight shell %}
git config credential.helper store
{% endhighlight %}

For all project repositories:

{% highlight shell %}
git config --global credential.helper store
{% endhighlight %}

# Step 4: Push your change and enter your GitHub user name and PAT

Copy and paste in your PAT from **Step 1** for the password prompt.

{% highlight shell %}
git add .
git commit -m "a new commit"
git push
Username for 'https://github.com': <Type GitHub user Name here>
Password for 'https://<USER>@github.com': <Paste GitHub Personal Access Token here>
{% endhighlight %}

# Step 5: Verify the credential is saved and reused

Push more changes and commits and see if you still get prompted for a username 
and password (i.e. Personal Access Token) for authentication.

If everything works, you should not be prompted unless your cached PAT has been 
revoked or has expired.

{% highlight shell %}
git add .
git commit -m "a newer commit"
git push
{% endhighlight %}

# Conclusion

Well then, seems like using HTTPS for `git clone` is still a usable experience 
without having to install proprietary software tools or muck around with 
reconfiguration of Git too much. Just a one-liner on the local repository to
tell `git` to store and cache the user name and Personal Access Token.

Note that [git-credential-store](https://git-scm.com/docs/git-credential-store) 
and [git-credential-cache](https://git-scm.com/docs/git-credential-cache) 
are only suitable for anyone looking for simplicity and not security. 

You can certainly experiment with the 
[other more secure credential helper options](https://git-scm.com/doc/credential-helpers) 
if you need higher grade security for credential storage.

Hope this helps somebody and that it stays this way.
