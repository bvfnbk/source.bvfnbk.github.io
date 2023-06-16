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

# or:
$ hugo new posts/$NAME/about.md
```

This either creates the file `posts/$NAME.md` or the file `posts/$NAME/index.md` - whichever way you chose. The `$NAME`
is transformed in both cases to the _title_ of the new post according to the following rules: 

* `-` replaced with spaces.
* All tokens Camel-cased

**Example:** `new-name` becomes `New Name`.

This is controlled in the template for new posts defined in `archetype/default.md`. There are the _macros_ defined which
actually perform the transformation.

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
