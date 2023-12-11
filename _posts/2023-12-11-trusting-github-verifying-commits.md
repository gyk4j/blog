---
layout: post
title:  "Trusting Github for verifying commits"
---

Was doing my usual `git log --show-signature` today. And was presented with a 
**BIG RED WARNING**:  

![gpg warning](/blog/assets/images/2023-12-11-gpg-warning.png)

I remember adding allowed signers to Git and telling it to trust my SSH key for 
verifying commit signature. So why is Git -- or more specifically GPG --- still 
complaining? It turns out that if we are using GitHub, and GitHub signs commits 
with its own RSA key when merging pull requests (and other automated backend 
processes) with its web flow.  

On [StackExchange](https://security.stackexchange.com/questions/193684/how-can-i-verify-signed-commits-made-by-other-people), 
it suggests adding the RSA public key belonging to GitHub (if you trust it 
enough) to GPG for it to verify these GitHub's commits.

```shell
curl https://github.com/web-flow.gpg | gpg --import
```

Now, doing `git log --show-signature` displays the following:  

![gpg info](/blog/assets/images/2023-12-11-gpg-info.png)

Isn't this more reassuring?


