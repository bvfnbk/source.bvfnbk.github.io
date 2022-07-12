---
title: "Part 3 : Create your First Post"
date: 2022-07-08T13:11:25+02:00
includeInPostList: true
draft: false
tags:
  - Hugo
  - Github
---

{{% include "./content/posts/hugo/_preamble.md" %}}

The [Hugo](https://gohugo.io) CLI provides a command to create new posts, e.g.

```shell
$ cd source.$GH_USER.github.io/
$ hugo new posts/first-post.md
```

Creates the file `posts/first-post.md`. It uses `archetypes/default.md` as template. This file defines the default
frontmatter for new posts:

```yaml
---
title: "{{ replace .Name "-" " " | title }}"
date: { { .Date } }
draft: true
---
```

This uses some _Hugo_ macros:

* `title` : the macro turns the name of the created file `first-post.md` to `First Post`
  * `replace` does what it says: replace dashes with spaces, 
  * `.Name` appears to be the file name w/o extension and
  * the `title` switches the case of the words.
* `date` : the macro calculates the current timestamp (and formats it as a string)
* `draft` controls whether the post is supposed to be _published_ (if `false`) or not.

Eventually tag the page, e.g.

```yaml
---
title: "First Post"
data: 2022-07-08T13:11:25+02:00
draft: false
tags:
  - Github
  - Hugo
---
```

and we are done.
