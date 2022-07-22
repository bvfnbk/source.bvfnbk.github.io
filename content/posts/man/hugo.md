---
title: "hugo"
date: 2022-07-22T14:39:18+02:00
includeInPostList: true
draft: false
tags:
  - man
  - linux
  - unix
  - macOS
---

## Name

`hugo` - hugo builds your site

## Examples

### Site

A new site can be created with

```shell
$ hugo new site $NAME
```

To create the site in the _current_ working directory, use

```shell
$ hugo new site .
```

Eventually add `--force` if the current working directory is not empty.

### Posts

A new post can be created with:

```shell
$ hugo new posts/$NAME.md
```

**Please note:** The `$NAME` is transformed using macros by default, i.e.

* `-` replaced with spaces.
* All tokens Camel-cased

This uses the `archetype/default.md` as template.

### Serving Locally

Run

```shell
$ hugo server -D
```

to have `hugo` watch the source directory and automatically rebuild the site.

**Please note:**

* Updated content included with shortcodes/macros is not updated in the browser view.
* The service will not start if the site sources contain errors.
* The service renders an error page if errors are introduced while running.
* The service crashed once. The suspected reason might have something to do with the creation of the shortcode.
