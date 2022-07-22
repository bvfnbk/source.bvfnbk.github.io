---
title: "Personal GitHub Pages using Hugo"
date: 2022-07-22T10:33:40+02:00
includeInPostList: true
draft: false
tags:
  - Hugo
  - Github
  - macOS
---

I used to play around with my personal GitHub page every once in a while. I used to set up the repository and
initialized it using the recommended _Jekyll_ way. _Jekyll_ is a site generator implemented in Ruby - which is already
there on my MacBook (severely outdated) or a `brew install` away (which might be an over-simplification).

It is equally easy to install _Jekyll_ for local development. This alone would be great if not for the software upgrades
coming in from time to time. Most of the time everything was fine but occasionally, an update of a single package came
in which sort of broke _Jekyll_. That left me clueless (I am no Ruby developer) and forced me to execute a 
"remove all, install all" procedure to fix things again.

That calmed down my desire to have a personal GitHub page a bit. I did some research and read about other site
generators. I looked into some of them whether they could fit into the original GitHub page workflow or not - I could
not find one.

Some side quests made me try out GitHub actions, one blog post lead to another. I stumbled over _Hugo_ 
(https://gohugo.io) before and I liked it even then: The idea of one executable without any external dependencies
really _is_ simpatico. And now I have found a solution to use it for my GitHub page.

So here we go. This post summarizes the setup for a personal GitHub page. It uses _GitHub Actions_ to deploy changes
to the actual site. I try to describe the procedure as generic as possible, some instructions are abstract, others lack
important details which might require additional research - where I have created manual pages/examples I do link to
these pages though.

The guide is macOS-centric but the quintessence is applicable to Windows/Linux as well.


## Requirements


Why would you care about setting up a personal GitHub page without a _GitHub account_, right? 

Go create one if you have none. This guide tries to be as generic as possible - therefore I use `$GH_USER` as your 
username. Replace it with your "real" name. 

Obviously, you will need _Hugo_. Install it on macOS with _Homebrew_, e.g.

```shell
$ brew install hugo
$ hugo version
hugo v0.101.0+extended darwin/amd64 BuildDate=unknown
```

Finally, working with GitHub without `git` is almost as hard as accessing GitHub without a user account. Install `git`
and an SSH client (which is optional and may be replaced with HTTPS). 

## Setting up a New Site

The CLI `hugo` includes a command to create the basic directory structure. Create a new site in the current directory
with

```shell
$ hugo new site source.$GH_USER.github.io
```

The site will only contain the _sources_ of your page. These files will be transformed and committed to the actual
repository with the help of a _GitHub Action_. You may choose whichever name you want here.

This creates a directory of the same name in the current working directory. All subsequent commands are supposed to be
run _in_ the site directory and paths to files are given relative to the site directory.

Edit the configuration file `config.toml` to complete the initial setup. set `baseURL` to the URL pointing to your final
GitHub page. The initial contents may look like:

```toml
baseURL = 'https://$GH_USER.github.io/'
languageCode = 'en-us'
title = 'Notes of $GH_USER'
```

Initialise a new [git](/posts/man/git/) repository in the site directory. This is required when adding the _theme_ to
the site.

You most certainly want to focus on writing your posts and not on writing and fixing all of your templates to bring your
ideas onto your GitHub page. Then, a _theme_ is what you want. There are quite a few themes to choose from listed at

* https://themes.gohugo.io or
* https://hugothemesfree.com

For your information: this site uses the theme [m10c](https://hugothemesfree.com/a-minimalistic-blog-theme-for-hugo/). 
It _should_ be added to the repository as a [submodule](/posts/man/git/)  (into the `themes/` directory) of your site, 
e.g. 

```shell
$ git submodule add https://github.com/vaga/hugo-theme-m10c.git themes/m10c
```

That way it becomes easier for you to update the theme if required. It must be "enabled" and configured in your 
`config.toml`, i.e.

```toml
theme = "m10c"

[params]
  author = "$GH_USER"
  description = "Describe you page"
```

_m10c_ requires the `author` and/or `description` field in its templates. `hugo` may not be able to build/serve the site
without them.

What good is a site for if it does not contain a post? Let's change this. 

The `hugo` CLI offers a command for that as well. It does not write your post but you can create the boilerplate post
with

```shell
$ hugo new posts/first-post.md
```

This creates the file `posts/first-post.md` using `archetypes/default.md` as a template. The result is something like

```yaml
---
title: "First Post"
date: 2022-07-08T13:11:25+02:00
draft: true
---
```

The file contains the _front-matter_. There you have the `title` (which is - tata - the page title and also
rendered in the post list), the `date` which defines the creation date/time and a boolean flag `draft`. 

_Draft_ posts are not rendered in the finally published site. This way it is possible to work on new posts and commit
your changes without affecting the currently published site.

When you look at the archetype, you will see how Hugo calculates the `date` and `title` using macros.

Now add some markdown and use [hugo](/posts/man/hugo/) to start a local development service with

```shell
$ hugo server -D
```

## Setup GitHub

Go to https://github.com and create two repositories for your user. The first one for your site sources the second one
for your actual GitHub page. The page repository must be named `$GH_USER.github.io` but the site repository may be
called just like you prefer.

For this guide, I expect `source.$GH_USER.github.io`. Configure the [remotes](/posts/man/git/) for your site directory
and add the [git](/posts/man/git/) URL as `origin`.


Push your sources (do not forget to set upstream branch) and continue to configure the GitHub actions.

The idea is, to build and publish a new site whenever the source repository is modified. We can use a GitHub action for
that purpose. This action must be able to _deploy_ to the `$GH_USER.github.io` repository which requires an SSH keypair
for authentication purposes.

Use [ssh-keygen](/posts/man/ssh-keygen/) to generate an SSH keypair (leave the passphrase empty for the private key).

1. Register the _public_ key as a _Deployment Key_ of the `$GH_USER.github.io` repository (with write access).
2. Register the _private_ key as _Repository Secret_ of the `source.$GH_USER.github.io` repository (for the action); 
   name the secret `PRIVATE_KEY` (or whatever; this is used in the action).

Create the action `.github/workflows/github-pages.yml` with the following contents:

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

## Summary

This guide described, how to setup a [Hugo](https://gohugo.io)-powered personal GitHub page. Any change committed to
the `main` branch of the source repository is automatically built and published.

This guide deliberately leaves details out. I assume that you have worked with `git`, `ssh`, GitHub and eventually 
_Homebrew_ before. Thus, you already know the required instructions - or at least where to look for supplemental
manuals. You do not blindly execute instructions - without thinking about their effects and their correctness. 
