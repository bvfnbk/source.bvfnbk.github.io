---
title: "Part 6 : Setup Continuous Deployment"
date: 2022-07-08T15:43:05+02:00
includeInPostList: true
draft: true
tags:
  - Hugo
  - Github
---

{{% include "./content/posts/hugo/_preamble.md" %}}

There is a _Github_ action which can be used to deploy the contents to _Github Pages_.

## Create Secrets

The _Github Action_ requires special secrets for it to be allowed to _write_ to the _Github Pages_ repository 
`$GH_USER.github.io`. This can be enabled using a SSH keypair.

### SSH Keypair

Use [ssh-keygen](/posts/man/ssh-keygen/) to generate a SSH keypair. Use ED25519 if applicable, leave the
passphrase for the private key empty and store the private key into `ed_25519` (which stores the _public_ key into 
`ed_25519.pub` into the same directory). [pbcopy](/posts/man/pbcopy/) can be used to copy the contents of text files
from the file system to the clipboard (on the commandline; on macOS only).

###  Register Deployment Key

The _target_ repository requires the contents of the _public_ key file as "Deploy Key". For that, navigate to the 
_Settings_ of the _Github Pages_ repository `$GH_USER.github.io` and open _Security_ &rarr; _Deploy keys_.

Select _Add deploy key_ and paste the contents of the _public_ key file into the _Key_ input field. Please make sure to
check _Allow write access_.

### Register Repository Secret

The action running for the _source_ repository needs to authenticate itself at the target. For that it requires the
_private_ key. Navigate to the _Settings_ of the _source_ repository `source.$GH_USER.github.io` and open _Security_
&rarr; _Secrets_ &rarr; _Actions_.

Select _New repository secret_ and past the contents of the _private_ key file into the _Value_ input field using the 
_Name_ `PRIVATE_KEY`.


## Create Action


In order to create the workflow,
create the following file

```shell
$ cd source.$GH_USER.github.io/
$ mkdir -p .github/workflows/
$ touch .github/workflows/github-pages.yml
```

with the following contents:

```yaml
name: Push to Github Pages
on:
  push:
    branches:
      - main
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.83.0'

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.PRIVATE_KEY }}
          external_repository: $GH_USER/$GH_USER.github.io
          publish_dir: ./public
```
