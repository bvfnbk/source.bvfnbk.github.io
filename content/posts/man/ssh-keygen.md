---
title: "ssh-keygen"
date: 2022-07-12T16:32:11+02:00
includeInPostList: true
draft: false
tags:
  - man
  - linux
  - unix
  - ssh
  - Github
  - git
---

## Name

`ssh-keygen` - OpenSSH authentication key utility.

## Synopsis

```shell
ssh-keygen [-t rsa | ed25519 | ...] [-b bits] [-N new_passphrase] [-f output_keyfile]
```

## Examples

### Interactive

`ssh-keygen` asks for the path to the output file and the passphrase for the private key.

```shell
ssh-keygen -t rsa -b 4096 -C "user@domain.tld"
ssh-keygen -t ed25519 -C "user@domain.tld"
```

**Please note:** The `rsa` algorith is the default. `ed25519` might not be supported on all systems.

### For Scripts


```shell
ssh-keygen -t rsa -b 4096 -C "user@domain.tld" -f ./id_ed25519 -N ""
ssh-keygen -t ed25519 -C "user@domain.tld" -f ./id_ed25519 -N ""
```

**Please note:** `-N ""` disables the key encryption.
