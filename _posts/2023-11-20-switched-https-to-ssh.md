---
layout: post
title:  "Switched Git repositories' URL from HTTPS to SSH"
date:   2023-11-20 13:30:00 +0800
categories: devops
---
Decided to change the `git clone` URL for Github repositories from `https` to 
`ssh`.

# Generate a [new SSH key][generate-ssh-key]. 

I used the Github-provided `user@users.noreply.github.com` email address instead 
of the real email for privacy reason. It was not quite clear if the email 
address must be the real one used for Github account registration or
the noreply (`ID+USERNAME@users.noreply.github.com`) but it worked nonetheless.

`ssh-keygen -t ed25519 -C "ID+USERNAME@users.noreply.github.com"`

After which, I found the SSH key in the following files:
- `$HOME/.ssh/id_ed25519` (private key)
- `$HOME/.ssh/id_ed25519.pub` (public key)

The public key is for uploading to Github, while the private key is meant for
safeguarding with all your life.

# Skipped passphrase setting and SSH Agent

Decided that setting a passphrase for the SSH key and having to mess around with
a ssh-agent is not worth my time for the extra security, so I skipped it; there
is no need to add this new SSH key to a ssh-agent or to have it running if this
is **no** passphrase set for a SSH key. ;)

If you set a passphrase, you would have to ensure you have the passphrase added 
to the SSH agent, and to have `ssh-agent` service running whenever you 
authenticate to Github for fetching and pushing.

To keep things simple, and knowing nobody is likely to want to impersonate my 
Git commits, I just skipped it altogether. If someone steals my SSH key someday,
I will just remove the lost SSH key in Github.

# Set the SSH key's permissions

To better protect it from other users on my single user machine :) , I changed
the permissions accordingly.

{% highlight shell %}
cd $HOME/.ssh
chmod 700 .
chmod 600 id_ed25519
chmod 644 id_ed25519.pub
{% endhighlight %}

`.ssh` directory is meant for access by an individual user himself, so it's 700.

The private key `id_ed25519` likewise is restricted to owner only, but it does 
not require the executable bit for directory traversal. So it's 600.

Lastly, the public key `id_ed25519.pub` is meant to be publicly readable, while
staying writable by owner. So it's 644.

# Add the public key to Github

1. Open a web browser and log in to [Github](https://github.com).
2. Click **Menu &gt; Settings &gt; SSH and GPG keys**.
3. Under **SSH keys**, click **New SSH key**.
4. Type a meaningful label in **Title** to identify the SSH key.
5. Leave **Key type:** `Authentication key`.
6. In **Key**, copy and paste the file content of `$HOME/.ssh/id_ed25519.pub`.
7. Click **Add SSH key**

# Change the Git repository's remote URLs

Followed the instruction from [Github][switch-https-to-ssh].

To see the current remote URLs for fetch and push.

`git remote -v`

To change and update the HTTPS URL to SSH instead.

`git remote set-url origin git@github.com:OWNER/REPOSITORY.git` 

All done in a matter of minutes, including verification and testing.

[generate-ssh-key]: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
[switch-https-to-ssh]: https://docs.github.com/en/get-started/getting-started-with-git/managing-remote-repositories#switching-remote-urls-from-https-to-ssh
