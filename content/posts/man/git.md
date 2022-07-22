---
title: "git"
date: 2022-07-22T12:04:40+02:00
includeInPostList: true
draft: false
tags:
  - man
  - linux
  - unix
  - git
---

## Name

`git` - the stupid content tracker

## Examples

### Initialize Repository

```shell
$ mkdir repository/
$ cd repository/
$ touch README.md
$ git add README.md
$ git commit -m 'initial commit'
```

### Pushing

```shell
$ git push [--set-upstream | -u] $REMOTE $BRANCH [--force]
```

### Remotes

#### List

```shell
$ git remote -v
origin git@github.com:$USER/$REPOSITORY.git (fetch)
origin git@github.com:$USER/$REPOSITORY.git (push)
```

#### Delete

```shell
$ git remote remove $NAME
```

#### Add

```shell
$ git remote add $NAME git@github.com:$USER/$REPOSITORY.git
```

### Submodules

#### Add Submodule

```shell
$ cd working-directory/
$ git submodule add $URL [$MODULE_PATH]
```

This creates (or updates) the `.gitmodules` file in the working directory and a _file_ located at `$MODULE_PATH`.
These files must be staged for commit

```shell
$ git add .gitmodules
$ git add $MODULE_PATH
```

and committed.
