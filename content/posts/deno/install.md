---
title: "Installing Deno"
date: 2023-06-16T17:12:35+02:00
includeInPostList: true
draft: true
---

_Deno_ is easily installed. Simply visit https://github.com/denoland/deno/releases and download the ZIP file matching
your platform.

| OS      | Architecture | Artifact                            |
|---------|--------------|-------------------------------------|
| macOS   | `aarch64`    | `deno-aarch64-apple-darwin.zip`     |
| macOS   | `x86_64`     | `deno-x86_64-apple-darwin.zip`      |
| Windows | `x86_64`     | `deno-x86_64-pc-windows-msvc.zip`   |
| Linux   | `x86_64`     | `deno-x86_64-unknown-linux-gnu.zip` |

The ZIP file contains a single file executable `deno` (for macOS and Linux) which can be put into a directory of your
choice, e.g.

```shell
deno-$VERSION/bin/deno
```

**Please note: (macOS)** Running the `deno` executable might be forbidden (as the developer cannot be verified). Run

```shell
xattr -d com.apple.quarantine deno-$VERSION/bin/deno
```

to remove the quarantine flag. The following environment variables could be set.

| Environment Variable | Comment                              |
|----------------------|--------------------------------------|
| `DENO_DIR`           | Cache directory.                     |
| `DENO_REPL_HISTORY`  | The directory for the REPL history.  |
| `DENO_INSTALL_DIR`   | The `deno_install` output directory. |

**Please note:** Not sure, which ones are actually required.
