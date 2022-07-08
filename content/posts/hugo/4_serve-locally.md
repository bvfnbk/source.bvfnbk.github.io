---
title: "Part 4 : Serve Locally"
date: 2022-07-08T13:40:05+02:00
draft: true
includeInPostList: true
tags:
  - Hugo
  - Github
---

{{% include "./content/posts/hugo/_preamble.md" %}}

[Hugo](https://gohugo.io) can also be used to test-drive your site locally. Run

```shell
$ cd source.$GH_USER.github.io/
$ hugo server -D
```

to run a development service. This service watches the site. A rebuild is automatically triggered if a change has been
detected and the browser will reload.

**Please note:**

* Updated files included using the `include` shortcode are _not_ updated in the view when updated.
* The service will not start if the site sources contain errors.
* The service renders an error page if the error is introduced while running.
* The service crashed once. The suspected reason might have something to do with the creation of the shortcode.


