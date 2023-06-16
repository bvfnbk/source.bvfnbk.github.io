---
title: "Creating a new Theme"
date: 2023-05-12T10:47:26+02:00
includeInPostList: true
draft: false
tags:
  - Hugo
  - Theme
---

My previously selected theme (_m10c_) is actually quite nice, it does not add too much eye candy (compared to others)
and I liked the simple responsiveness. Though I found the generated pages to be a bit "packed".

To be honest, the only parts required to be changed in the end were parts of the stylesheets but then, I would have
skipped an opportunity to learn something new.


## What do I want to achieve?

This is just a starter: there are more ideas on my roadmap. But to start small, I simply rebuild the original theme with
minor modifications.


### Layout

I want to keep the two-column layout. The main navigation should still float to the top of the page when the browser
window is scaled down.


### Colour Theme

Personally, I like dark color themes and I will switch to a dark theme whenever I can. But for reading, I actually
prefer light themes. 


## How to Start?

_Hugo_ includes boilerplate code for new themes as well. Create a new one with

```shell
hugo new theme $NAME  
```

and in order to switch to it, update `config.toml` and set the `theme` to the new name:

```toml
theme = "$NAME"
```

The result is quite &hellip; empty. Copying template files over from _m10c_ does not work (obviously) and the error
messages are a bit cryptic. And the problem is my lack of understanding. 

Luckily, https://draft.dev/learn/creating-hugo-themes contains a step-by-step guide which can be used to prepare a
working example to start from.

The TL;DR; variant: Follow all sections through until "_Adding Support for Multiple Authors_" (this will not be
required).

## Roles of Particular Files

Having a site up and running (well, _most_ parts working, others still "in progress") we can have a look at the contents
of the theme.

| Path/File                      | Comment                                                                                                                      |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `layouts/index.html`           | This affects the "landing" page, e.g. `content/_index.md`                                                                    |
| `layouts/default/baseof.html`  | This is the template for _all_ pages, including posts.                                                                       |
| `layouts/default/list.html`    | This basically lists files/concepts in aggregating directories, e.g. `posts/_index.md` and `tags/_index.md`                  |
| `layouts/default/single.html`  |                                                                                                                              |
| `layouts/partials/header.html` | Templates to be used in other templates.                                                                                     |
| `layouts/partials/head.html`   | Templates to be used in other templates.                                                                                     |
| `layouts/partials/footer.html` | Templates to be used in other templates.                                                                                     |
| `layouts/posts/single.html`    | This template is applied to a each post; but _not_ to the `_index.md` files found in any child directory of `content/posts/` |

**Please note:**

- All paths are relative to the path to the theme.
- Why is the `list.html` template added to a directory containing other posts?
- Why is the `single.html` template not applied to the `_index.md` files?

## Fixing Things Up &hellip;

- The icons are now handled differently: the SVG is included _as is_, not using a HTML template.
- Collections of posts (i.e. directories containing posts) required an own `_index.md` file. These pages allow 
  introductory words and receive a _list_ of posts contained in the collection.
- Start including a CSS stylesheet from `static/css/` (later this gets converted to SCSS files in `assets/css/`).
- Create a `layouts/partials/pagination.html` for the pagination element

## Open Points

- _About_ page not working.



## Resources

- https://themes.gohugo.io/themes/hugo-theme-m10c/
- https://draft.dev/learn/creating-hugo-themes
