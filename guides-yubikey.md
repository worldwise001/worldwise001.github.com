---
layout: page
title: Using your YubiKey to enable secure software development
permalink: /guides/yubikey/
---

YubiKeys are great. They're a good [2FA](https://en.wikipedia.org/wiki/Multi-factor_authentication) mechanism, support [TOTP](https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm), and can be used as a rudimentary [smartcard](https://en.wikipedia.org/wiki/Smart_card). They're also about $40-50.

This guide is to help you set up a YubiKey to sign git commits and also SSH into servers using key-based auth. It's intended for software developers who want marginally more security and peace of mind while doing development.

### Prerequisites

I assume you know the following:
- vaguely what PGP/GPG is, and some understanding of its web of trust model
- what a yubikey is
- what SSH keys are w.r.t. client authentication

I assume you have the following:
- a YubiKey 4 or better, that has never been configured with GPG
- a computer with a newish version of GPG 2
- servers that still allow RSA keys (some folks already are into ED25519 keys only)
- an existing GPG secring, ideally with a 2048 or 4096-bit RSA private key
- the command `ykman` (on mac, you can use homebrew and `brew install ykman`)

### Resetting GPG on the YubiKey

[(source 1)](https://support.yubico.com/support/solutions/articles/15000006421-resetting-the-openpgp-applet-on-the-yubikey) [(source 2)](https://developers.yubico.com/PGP/Card_edit.html)

```
$ ykman openpgp reset
WARNING! This will delete all stored OpenPGP keys and data and restore factory settings? [y/N]: y
Resetting OpenPGP data, don't remove your YubiKey...
Success! All data has been cleared and default PINs are set.
PIN:         123456
Reset code:  NOT SET
Admin PIN:   12345678
```

```
$ gpg --card-edit

Reader ...........: Yubico Yubikey 4 OTP U2F CCID
Application ID ...:
Version ..........: 2.1
Manufacturer .....: Yubico
Serial number ....:
Name of cardholder: [not set]
Language prefs ...: [not set]
Sex ..............: unspecified
URL of public key : [not set]
Login data .......: [not set]
Signature PIN ....: not forced
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
Signature counter : 0
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]

gpg/card> admin
Admin commands are allowed

gpg/card> passwd
gpg: OpenPGP card no.  detected

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 1
PIN changed.     

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 3
PIN changed.     

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? q
```

### BACK UP YOUR GPG KEY

```
$ gpg --export-secret-keys 8A2BD353 > 8A2BD353.asc
```

### Loading your GPG onto the YubiKey

[(source 1)](https://support.yubico.com/support/solutions/articles/15000006420-using-your-yubikey-with-openpgp)


```
$ gpg --edit-key 8A2BD353
gpg (GnuPG) 2.2.12; Copyright (C) 2018 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
sec  rsa4096/BDE438068A2BD353
     created: 2013-09-11  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/422DAD542FC162F9
     created: 2013-09-11  expires: never       usage: E
ssb  rsa4096/1EA455BF07A988D2
     created: 2019-07-23  expires: never       usage: A   
[ultimate] (1). Sarah Harvey <-@shh.sh>
[ultimate] (2)  Sarah Harvey <-------@cs.uwaterloo.ca>
[ultimate] (3)  Sarah Harvey <------------@gmail.com>
[ultimate] (4)  Sarah Harvey <-------@csclub.uwaterloo.ca>
[ultimate] (5)  [jpeg image of size 13232]
[ultimate] (6)  Sarah Harvey <---@squareup.com>

gpg> keytocard
Really move the primary key? (y/N) y
Please select where to store the key:
   (1) Signature key
   (3) Authentication key
Your selection? 1

gpg> key 1

sec  rsa4096/BDE438068A2BD353
     created: 2013-09-11  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb* rsa4096/422DAD542FC162F9
     created: 2013-09-11  expires: never       usage: E
ssb  rsa4096/1EA455BF07A988D2
     created: 2019-07-23  expires: never       usage: A   

gpg> keytocard
Please select where to store the key:
   (2) Encryption key
Your selection? 2

gpg> key 1

gpg> key 2

sec  rsa4096/BDE438068A2BD353
     created: 2013-09-11  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/422DAD542FC162F9
     created: 2013-09-11  expires: never       usage: E
ssb* rsa4096/1EA455BF07A988D2
     created: 2019-07-23  expires: never       usage: A   

gpg> keytocard
Please select where to store the key:
   (3) Authentication key
Your selection? 3

sec  rsa4096/BDE438068A2BD353
     created: 2013-09-11  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/422DAD542FC162F9
     created: 2013-09-11  expires: never       usage: E
ssb* rsa4096/1EA455BF07A988D2
     created: 2019-07-23  expires: never       usage: A   

gpg> quit
Save changes? (y/N) y
```

### Setting up Git with GPG-signed commits

[(source 1)](https://help.github.com/en/articles/signing-commits) [(source 2)](https://gist.github.com/troyfontaine/18c9146295168ee9ca2b30c00bd1b41e)

```
$ git config --global commit.gpgsign true
```

For good measure, kill gpg-agent and restart your terminal:
```
$ killall gpg-agent # forces gpg-agent to restart
```

### Setting up SSH with GPG

[(source 1)](https://www.linode.com/docs/security/authentication/gpg-key-for-ssh-authentication/) [(source 2)](https://github.com/robbyrussell/oh-my-zsh/pull/6140/files)

Add the following to `~/.bash_profile` (mac-specific):
```
GPG_AGENT_SOCKET="$HOME/.gnupg/S.gpg-agent.ssh"
if [ ! -S $GPG_AGENT_SOCKET ]; then
  gpg-agent --use-standard-socket --daemon >/dev/null 2>&1
  export GPG_TTY=$(tty)
fi
unset SSH_AGENT_PID
export SSH_AUTH_SOCK=$GPG_AGENT_SOCKET
```

Add the following to `~/.gnupg/gpg-agent.conf`:
```
default-cache-ttl 600
max-cache-ttl 7200
enable-ssh-support
```

For good measure, kill gpg-agent and restart your terminal:
```
$ killall gpg-agent
```

Extract your new SSH pubkey from your yubikey:
```
$ ssh-add -L > ~/.ssh/id_yubikey.pub
```

Add the contents of that pubkey to your remote server's `~/.ssh/authorized_keys`.
