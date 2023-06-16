---
title: "CPU Type & Word size"
date: 2022-12-23T14:15:57+01:00
includeInPostList: true
draft: true
tags:
- linux
- macOs
- windows
- java
---

This page describes some heuristics which can be used to determine the actual CPU type and word size.

## Generic

### Linux and macOS

Run

```shell
uname -m
```

which writes "(..)the type of the current hardware platform to standard output.(..)".

### Windows

The environment variable `PROCESSOR_ARCHITECTURE` can be used to determine the concrete platform, e.g.

```shell
echo %PROCESSOR_ARCHITECTURE%
AMD64
```

## Java

The system property: `os.arch` This property contains the architecture of the JVM. It does not necessarily match the
actual values for the operating system and CPU type.

**Possible Values:**

On Windows:

- `ia64`
- `amd64`
- `x86`
- `aarch64`

On macOS:

- `arm64`
- `arm64e`
- `x86_64`

On Linux:

- `amd64`

## Resources

- OSHI (https://github.com/oshi/oshi)
- https://github.com/openjdk/jdk18u/blob/master/src/java.base/windows/native/libjava/java_props_md.c
