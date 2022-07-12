---
title: "Part 1 : Installing Hugo"
date: 2022-07-08T10:36:30+02:00
includeInPostList: true
draft: false
tags:
- Hugo
- Github
---

{{% include "./content/posts/hugo/_preamble.md" %}}

On macOS, _Hugo_ can easily be installed with _Homebrew_. Simply run

```shell
$ brew install hugo
```

Eventually check its version

```shell
$ hugo version         
hugo v0.101.0+extended darwin/amd64 BuildDate=unknown
```

## Site

A new site can be created with

```shell
$ hugo new site source.$GH_USER.github.io
```

or if the directory already exists:

```shell
cd source.$GH_USER.github.io/
hugo new site .
```

**Please note:** Add `--force` to the `hugo` instruction if the directory is not empty.

## Themes

### [m10c](https://hugothemesfree.com/a-minimalistic-blog-theme-for-hugo/)

**Please note:** The theme is handled as a `git` submodule (just like
the [Quick Start](https://gohugo.io/getting-started/quick-start/) proposes).

```shell
cd source.$GH_USER.github.io/
git submodule add https://github.com/vaga/hugo-theme-m10c.git m10c
git add .gitmodules
git add themes/m10c
git commit -m 'Added submodule'
```

and add the following line to `config.toml`:

```toml
[params]
  author = "$GH_USER"
  description = "..."

  [[params.social]]
    icon = "github"
    name = "https://github.com/$GH_USER"
    url = "https://github.com/$GH_USER"
```

**Please note:** The properties `author`, `description` are required. The theme will not render otherwise.
