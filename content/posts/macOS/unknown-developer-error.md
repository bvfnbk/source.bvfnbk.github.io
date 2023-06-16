---
title: "Fix 'Unknown Developer Error'"
date: 2023-06-16T16:59:30+02:00
includeInPostList: true
draft: false
tags:
  - macOS
  - terminal
---

Installing software from different resources than the _Apple AppStore_ usually causes a warning similar to the
following:

> (..) cannot be opened because the developer cannot be verified (...)

Using the _Finder_, this is easy to fix: right-click and open. The following dialog provides a warning but also contains
a button to open it nonetheless.

However, sometimes we need to do this from within a shell, e.g. because we installed the tool (which is a commandline
tool) using a shell.

The problem is that _macOS_ puts the application or the executable into _quarantine_. This can be easily fixed with

```shell
xattr -d com.apple.quarantine $EXECUTABLE
```

**Please note:** This may be related to the _Gatekeeper_ but i do not want to disable it completely 
(`sudo spctl --master-disable`) and adding the executable with `spctl --add $EXECUTABLE` did not work either: Maybe
this only works for application bundles.

