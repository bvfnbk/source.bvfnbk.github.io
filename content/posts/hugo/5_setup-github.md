---
title: "Part 5 : Setup Github"
date: 2022-07-08T15:11:05+02:00
includeInPostList: true
draft: false
tags:
  - Hugo
  - Github
---

{{% include "./content/posts/hugo/_preamble.md" %}}

Log into your _Github_ account and create the following repositories:

* `$GH_USER.github.io` and
* `source.$GH_USER.github.io`

where `$GH_USER` is your _Github_ username.

The `source.(...)` repository will contain the *sources* of your personal page.

Configure the remotes of your local working directory

```shell
$ cd source.$GH_USER.github.io/
$ git remote add origin git@github.com:$GH_USER/source.$GH_USER.github.io.git
```

and push it with

```shell
$ git push -u origin main
```

The next post in this series configures a _Github Action_ which will update the actual page whenever the source repository will be updated.
