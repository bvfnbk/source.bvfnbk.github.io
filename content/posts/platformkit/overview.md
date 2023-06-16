---
title: "Platform Kit"
date: 2022-12-23T14:59:40+01:00
includeInPostList: true
draft: true
---

A _desktop application_ usually requires at most information about the _operating system_ to know what to expect from
the file system and other specific properties. Applications managing other applications (e.g. software development kits)
on the other hand may additionally require the

- CPU type (e.g. `x86`, ARM etc.) and
- the word size (32/64 Bit)

to know, which packages to use.

Obviously, a runtime built for a 64-bit linux running on an ARM CPU cannot be executed on a 32-bit linux running on a 
`x86` CPU. For that it is required that the application is able to reliably detect these properties. 

There are several methods available, depending on the host language the application is written in and the tools provided
by the host operating system.

