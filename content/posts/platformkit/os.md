---
title: "Operating System"
date: 2022-12-23T15:21:54+01:00
includeInPostList: true
draft: true
tags:
- linux
- macOs
- windows
- java
---

**Please note:** The OS detection described here is limited to the following (major) ones:

- Linux,
- macOS and
- Windows

## Generic

### Linux and macOS

`uname` to the rescue:

| Command    | Linux       | macOS    |
|------------|-------------|----------|
 | `uname -o` | `GNU/Linux` | `Darwin` |

**Please note:** The value `Darwin` is unfortunately only a _clue_ that it _might_ be a system running macOS. Other
sources appear to rely solely on this value to conclude it is a macOS. This is "only" the kernel which may eventually
run on a different platforms.

## Java

The operating system can be detected using the system property `os.name`. The possible values of this property can
simply be mapped to the required OS, e.g.

| Value                    | Operating System |
|--------------------------|------------------|
 | `Linux`                  | Linux            |
 | `Mac OS X`               | macOS            |
| `Windows 95`             | Windows          |
| `Windows 98`             | Windows          |
| `Windows Me`             | Windows          |
| `Windows 9X (unknown)`   | Windows          |
| `Windows 2000`           | Windows          |
| `Windows 2003`           | Windows          |
| `Windows XP`             | Windows          |
| `Windows NT (unknown)`   | Windows          |
| `Windows Vista`          | Windows          |
| `Windows 7`              | Windows          |
| `Windows 8`              | Windows          |
| `Windows 8.1`            | Windows          |
| `Windows 10`             | Windows          |
| `Windows 11`             | Windows          |
| `Windows Server 2008`    | Windows          |
| `Windows Server 2008 R2` | Windows          |
| `Windows Server 2012`    | Windows          |
| `Windows Server 2012 R2` | Windows          |
| `Windows Server 2016`    | Windows          |
| `Windows Server 2019`    | Windows          |
| `Windows Server 2022`    | Windows          |

## Resources

- https://github.com/openjdk/jdk18u/blob/master/src/java.base/windows/native/libjava/java_props_md.c
