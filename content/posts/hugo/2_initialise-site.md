---
title: "Part 2 : Initialising a Site"
date: 2022-07-08T12:31:11+02:00
includeInPostList: true
draft: false
tags:
  - Hugo
  - Github
---

{{% include "./content/posts/hugo/_preamble.md" %}}

## Initialise Directory Structure

**Please note:** This guide tries to be as generic as possible. It uses the variable `$GH_USER` wherever a _Github_ user
name is expected. Fill in your own username where required.

First, create a new [Hugo](https://gohugo.io) site. The CLI includes the necessary command, i.e. run

```shell
$ hugo new site source.$GH_USER.github.io
```

This creates `source.$GH_USER.github.io/` in the current directory and initialises it with the required directories and
files. 

**Please note:** It is possible to create a site in an already existing directory, e.g.

```shell
$ cd source.$GH_USER.github.io/
$ hugo new site .
```
Add `--force` if the directory is not empty.

## Setup `git`

Nothing special here. Simply `init` the current working directory.

```shell
$ cd source.$GH_USER.github.io/
$ git init
$ git add .
$ git commit -m 'Initial commit'
```

## Initialise Theme

[Hugo](https://gohugo.io) requires a _theme_. You may choose a theme from 

* https://themes.gohugo.io or
* https://hugothemesfree.com (which appears to host the same themes)

and use the original instructions. This guide uses 
[m10c](https://hugothemesfree.com/a-minimalistic-blog-theme-for-hugo/).

```shell
$ cd source.$GH_USER.github.io/
$ git submodule add https://github.com/vaga/hugo-theme-m10c.git themes/m10c
$ git add .gitmodules
$ git add themes/m10c
$ git commit -m 'Added'
```

Edit `source.$GH_USER.github.io/config.toml` and add

```toml
theme = "m10c"

[params]
  author = "$GH_USER"
  description = "A short description"

  [[params.social]]
    icon = "github"
    name = "github"
    url = "https://github.com/$GH_USER"
```

**Please note:** Configuration may depend on the actually used theme and its features. _m10c_ provides further
configuration properties. Describing all of these goes way beyond the scope of this article.

There is still much to do:

* Define a (reasonable) main menu, e.g. containing a link to an _About_ page, _Tags_ and the _Home_ page (at leaset),
* configure an avatar,
* social media links
* &hellip;
