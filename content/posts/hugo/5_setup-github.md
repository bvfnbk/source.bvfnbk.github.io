---
title: "Part 5 : Setup Github"
date: 2022-07-08T15:11:05+02:00
includeInPostList: true
draft: true
---

{{% include "./content/posts/hugo/_preamble.md" %}}

Log into your _Github_ account and create a new repository with the name

```
source.$GH_USER.github.io
```

where `$GH_USER` is your _Github_ username. Then add the new repository as a _remote_:

```shell
$ cd source.$GH_USER.github.io/
$ git remote add origin git@github.com:$GH_USER/source.$GH_USER.github.io.git
```

and push it with

```shell
$ git push -u origin main
```
