---
title: "Desktop Application Filesystem Environments"
date: 2022-10-25T17:13:33+02:00
includeInPostList: true
draft: false
---

An application typically manages a specific set of directories and files. The base directories can be categorized by the
following table:

| Directory       | Purpose                           | Comment                                                                              |
|-----------------|-----------------------------------|--------------------------------------------------------------------------------------|
| Configuration   | Configuration files.              | The application may require a single configuration file only.                        |
| Executables     | Scripts and (binary) executables. | Tools may be provided to the user.                                                   |
| Data            | Persistent data files.            | E.g. databases; it may not be possible to recreate these files from scratch.         |
| Cache           | Volatile data files.              | E.g. files downloaded from the "Internet"; can be downloaded again if deleted.       |
| Logs            | Application Log Files             |                                                                                      |
| State           | Application State.                | E.g. views, layout, open files, undo history, recent documents, actions history etc. |
| Runtime         | UNIX Sockets                      | May not be applicable to all platforms.                                              |
| Temporary Files | Volatile files.                   | Directory contents may not survive sessions.                                         |

However, not all applications will actually use or require all of these directories.

## Mapping to the (User) Filesystem

This section describes strategies to implement the defined base directories.

### Application Identity

All platforms need to identify an application in one way or the other. This identification can then be used to derive
directory names.

_Linux_ and/or _Microsoft Windows_ may use the plain application name, _macOS_ requires a more sophisticated 
_Bundle ID_. This bundle ID consists out of the

- reversed domain, e.g. `com.github.bvfnbk` and the
- name of the application, e.g. `ApplicationName`

which is concatenated to

```shell
com.github.bvfnbk.ApplicationName
```

and is used for application-specific directory names and for configuration files (e.g. 
`com.github.bvfnbk.ApplicationName.plist`)

### Single Directory for All

The idea is to put all application directories into a single directory. This actually is a reasonable strategy on all
platforms lacking a specification of standard user directories or for simplification purposes.

**Please note:** In the most generic use case, this may be any directory in the users home directory. This directory
is usually _hidden_ on linux platforms, e.g.

```shell
~/.${applicationName}/
```

For linux, the following table defines _one_ possible strategy to map the application directories


| Application Directory | Path                               | Comment                                                                                    |
|-----------------------|------------------------------------|--------------------------------------------------------------------------------------------|
| Configuration         | `~/.${applicationName}rc`          | A single configuration file may be put as a hidden file directly into the home directory.  |
|                       | `~/.${applicationName}/etc/`       | Multiple files can be put into a dedicated directory into the local application directory. |
| Executables           | `~/.${applicationName}/bin/`       | An application may or may not have extra executable files besides the application itself.  |
| Data                  | `~/.${applicationName}/var/`       |                                                                                            | 
| Cache                 | `~/.${applicationName}/var/cache/` |                                                                                            |
| Logs                  | `~/.${applicationName}/var/log/`   |                                                                                            |
| State                 | `~/.${applicationName}/var/state/` |                                                                                            |
| Runtime               | `~/.${applicationName}/var/run/`   |                                                                                            |
| Temporary Files       | `~/.${applicationName}/var/tmp/`   |                                                                                            |

This is a simple example naming strategy, loosely following the filesystem hierarchy standard (very loosely...). The
actual directory names like `etc/` etc. should be configurable.

### Windows

There is no specific directory structure defined for user applications. It may use the 
[Single Directory for All](#single-directory-for-all) strategy as previously defined. However, there are recommendations
on where to put this kind of directory.

_Windows_ defines the `AppData` directory for each user to contain the user application data. This directory can be
found at

```shell
C:\Users\${username}\AppData\
```

By default, there are the following directories to be found here:

```shell
C:\Users\${username}\AppData\Local
C:\Users\${username}\AppData\LocalLow
C:\Users\${username}\AppData\Roaming
```

**Please note:**

- The `Roaming` application data directory is for roaming user profiles, i.e. users whose PCs are connected to a domain
  and their profiles are loaded from the domain controller. This way, important settings _could_ be shared across different
  computers if all are part of the same domain.
- `LocalLow` is for access-restricted applications, e.g. for a web browser in protected/safe mode which are not able to
  access the `Local` directory.
- `Local` is the place to put application specific files by default.

### Linux (XDG)

The XDG base directory specification defines the following environment variables and directories (amongst others):

| Environment Variable | Default           | User-specific&hellip;        | Comment                          |
|----------------------|-------------------|------------------------------|----------------------------------|
| `XDG_DATA_HOME`      | `~/.local/share/` | &hellip;data files.          |                                  |
| `XDG_CONFIG_HOME`    | `~/.config/`      | &hellip;configuration files. |                                  |
| `XDG_STATE_HOME`     | `~/.local/state/` | &hellip;state data.          | Log files and application state. |
|                      | `~/.local/bin/`   | &hellip;executable files.    |                                  |
| `XDG_CACHE_HOME`     | `~/.cache/`       | &hellip;cached data files.   |                                  |

**Please note:** There is a `XDG_RUNTIME_DIR` which is meant for runtime and other files. The specification puts certain
requirements onto this directory, e.g. it must be created when the user logs in and removed when the user logs out etc.
which qualifies it for highly volatile files only.

The defined application directories can therefore be mapped to

| Application Directory | Path                                    | Comment                                                                                        |
|-----------------------|-----------------------------------------|------------------------------------------------------------------------------------------------|
| Configuration         | `~/.config/${applicationName}/`         | Any number of configuration files are supposed to end in this directory.                       |
| Executables           | `~/.local/bin/`                         | This directory should already be in the `PATH` and is actually shared with other applications. |
| Data                  | `~/.local/share/${applicationName}/`    |                                                                                                | 
| Cache                 | `~/.cache/${applicationName}/`          |                                                                                                |
| Logs                  | `~/.local/state/${applicationName}/`    | Eventually create a `log` directory in this directory.                                         |
| State                 | `~/.local/state/${applicationName}/`    |                                                                                                |
| Runtime               | `$XDG_RUNTIME_DIR/.${applicationName}/` |                                                                                                |
| Temporary Files       |                                         | Use system directories, e.g. `/tmp` or `/var/tmp`.                                             |

### macOS

The most important directories for an application are

| Path              | Contents                         | Comment                                                              |
|-------------------|----------------------------------|----------------------------------------------------------------------|
 | `~/Applications/` | User-specific applications.      | This will be the place where a desktop application may be stored.    |
 | `~/Library/`      | User-specific application files. | This directory contains all of the application-specific directories. |

(Left out, but mentioned here for the sake of completeness: `Desktop/`, `Documents/`, `Downloads/`, `Movies/`, `Music/`, 
`Pictures/`, `Public/`, `Sites/`)         

The `Library/` directory is the main directory of interest, containing

| Path                   | Contents                                     | Comment                                                                   |
|------------------------|----------------------------------------------|---------------------------------------------------------------------------|
| `Application Support/` | Application data files.                      | Included in backup.                                                       |
| `Caches/`              | Application-specific "support files".        | Not included in backup; the system may remove this directory if required. |
| `Preferences/`         | Application-specific configuration files.    | Included in backup.                                                       |
| `Logs/`                | Log files for console and specific services. | May be viewed using the _Console_ app.                                    |


**Please note:** The _Apple File System Programming Guide_ actually discourages the direct creation of configuration
files in the `Preferences/` directory.

The proposed application directories can therefore be mapped to


| Application Directory | Path                                             | Comment                                                                                            |
|-----------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Configuration         | `~/Library/Preferences/`                         | A single configuration file may be put into the directory but named according to the bundle.       |
 |                       | `~/Library/Preferences/${bundleId}/`             | Multiple files can be put into a dedicated directory.                                              |
| Executables           | `~/Library/Application Support/${bundleId}/bin/` | Apple does explicitly define a directory for executables.                                          |
 | Data                  | `~/Library/Application Support/${bundleId}/`     |                                                                                                    | 
 | Cache                 | `~/Library/Caches/${bundleId}/`                  |                                                                                                    |
 | Logs                  | `~/Library/Logs/${bundleId}/`                    |                                                                                                    |
| State                 |                                                  | There is no equivalent directory defined in the specification. Create one in the _Data_ directory. |
| Runtime               |                                                  | There is no equivalent directory defined in the specification. Create one in the _Data_ directory. |
| Temporary Files       |                                                  | `$TMPDIR`, `/private/tmp`, `/private/var/tmp` etc.                                                 |

There is no "true" directory for temporary files. One has to classify the type of (temporary) file and eventually put
the files into the _Cache_ directory. The `$TMPDIR` points to a _generated_ directory, e.g. 
`/var/folders/cr/1zh7581j0vxfjqvpskdv1kvc0000gn/T/` which may be wiped after restart (please note that this directory
is used by the JVM as well; cf. the system property `java.io.tmpdir`).


## Outlook

An application developer may require more control over the concrete locations of each application directory, e.g. the
developer may want to move _some_ directories to a network share but leave others at their local counterparts.

**Please note:** On _Microsoft Windows_ it may be useful to separate domain-specific (generic) configuration from 
host-specific configuration and store those files in the `AppData/Roaming` and/or `AppData/Local` directory. This is 
especially true for files which may be too large to be stored on a network drive (e.g. caches etc.).


## Resources

- Linux
  - Filesystem Hierarchy Standard https://wiki.linuxfoundation.org/lsb/fhs
  - XDG Base Directory Specification https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html
- Apple Filesystem Programming Guide
  - https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html
  - https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/MacOSXDirectories/MacOSXDirectories.html
* Microsoft Windows User Directory Structure (URL???)

