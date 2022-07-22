---
title: "date"
date: 2022-07-22T10:34:35+02:00
includeInPostList: true
draft: false
tags:
  - man
  - linux
  - unix
  - macOS
---

## Name

`date` - display or set date and time

## Examples

### Print Formatted Date/Time

* Date/Time with time zone offset (format: `+hhmm`):

  ```shell
  date +%Y-%m-%d'T'%H:%M:%s%z
  2022-07-22T10:51:14+0200
  ```
  or shorter
  ```shell
  date +%F'T'%T%z
  2022-07-22T10:51:14+0200
  ```
* Date/Time with time zone offset (format `+hh:mm`):
  * On macOS
    ```shell
    TZ=Europe/Berlin date -Iseconds -r $(date +%s)
    2022-07-22T10:33:40+02:00
    ```
  * On Linux
    ```shell
    date +%F'T'%T%:z
    2022-07-22T10:33:40+02:00
    ```
