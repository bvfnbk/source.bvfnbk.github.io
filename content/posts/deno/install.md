---
title: "Installing Deno"
date: 2023-06-16T17:12:35+02:00
includeInPostList: true
draft: false
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
to remove the quarantine flag.

The following environment variables may be useful:

| Environment Variable   | Comment                                                 | Default                                                             |
|------------------------|---------------------------------------------------------|---------------------------------------------------------------------|
| `DENO_DIR`             | Cache (transpiled files, remote modules etc.)           | `deno/` to be found in the default Operating System Cache directory |
| `DENO_INSTALL_ROOT`    | Location to store scripts installed with `deno install` | `$HOME/.deno/bin`                                                   |
| `DENO_NO_UPDATE_CHECK` | Disable check for newer `deno` version (if set)         | Not set                                                             |

**Please note:** 

- Set `DENO_DIR` and `DENO_INSTALL_ROOT` for project-specific caches and scripts, e.g. using a `.envrc` file (managed by
  `direnv`).
- IntelliJ
  - Plugin: "Better Direnv": The plugin does not (yet) support _Deno_ run configurations.


## Resources

https://deno.com/manual@v1.34.3/getting_started/setup_your_environment
