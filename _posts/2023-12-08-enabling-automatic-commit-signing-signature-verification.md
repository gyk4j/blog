---
layout: post
title: "Enabling Automatic Commit Signing and Signature Verification"
---

Just learned today that we can configure Git to sign commits automatically.
Not something that happens out-of-the-box, but we need to configure it manually.

Information are taken from [here](https://blog.dbrgn.ch/2021/11/16/git-ssh-signatures/).

# Enable automatic commit signing

1. Configure Git to always sign future commits

```shell
git config --global commit.gpgsign true
git config --global tag.gpgsign true
```

# Check commits' signature

2. Verify commit signatures are present and valid

```shell
git log --show-signature
```

If we see `No signature` and 
`error: gpg.ssh.allowedSignersFile needs to be configured and exist for ssh signature verification`,
it means that any signature even if present cannot be verified/trusted.

We need to tell Git who to trust. To do so, see the next step.

# Adding allowed signers

1. Create a allowed signer file e.g. $HOME/.git_allowed_signers
```shell
echo xxxxx+<USER>@users.noreply.github.com ssh-ed25519 AAAAxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/xxxxxxxxxxxxxxxxxxxxxxxx username@host >> ~/.git_allowed_signers
```
The format is `<signer's email> <key algorithm> <public key> <user@host>`

2. Configure Git to use this file

```shell
git config --global gpg.ssh.allowedSignersFile "$HOME/.git_allowed_signers"
```

Repeat [check commits' signature](#check-commits-signature) again. This time around, it should 
show `Good "git" signature for  <signer's email> with <key algorithm> key <public key>`

We are done.