---
title: "pbcopy"
date: 2022-07-12T17:14:20+02:00
includeInPostList: true
draft: false
tags:
  - man
  - macOS
---

## Name

`pbcopy` - provide copying and pasting to the clipboard from command line

## Synopsis

```shell
pbcopy
```

## Description

`pbcopy` takes the standard input and places it in the clipboard (there are different boards available; cf. `man pbcopy`
for more details).

## Example

```shell
pbcopy < $HOME/.ssh/id_ed25519.pub
```
