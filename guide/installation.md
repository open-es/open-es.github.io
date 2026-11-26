---
layout: guide
lang: en
ref: installation
title: Installation
description: Download a build of OES for Windows, Linux or macOS and start it.
section: start
order: 2
permalink: /guide/installation/
---

## Which build

- **The latest release** — [`v2.0.0-preview.1`](https://github.com/open-enterprise-solutions/enterprise/releases/latest),
  for Windows, Linux and macOS. Take this one to try the platform. Its packages are named
  `oes-2.0.0-preview.1-…`; the steps below are the same, with that name in place of `oes-nightly-…`.
- **Nightly**: built from `develop` every night that it changed. Each new build replaces the previous
  one at [releases/tag/nightly](https://github.com/open-enterprise-solutions/enterprise/releases/tag/nightly).
  Take it for what landed since the release.
- Releases up to `v1.3.0` are the alpha (1.x): their programs do not talk to a 2.0 server, and their
  bases are not carried over.

This guide follows the nightly. In `v2.0.0-preview.1` the studio is `designer-thin`, the workspace
`enterprise-thin` and the code runner `codeRunner`; the launcher's buttons are *Enterprise*, *Web* and *Designer*.

Each package contains everything the programs need: the Firebird database engine, the
PostgreSQL client and the interface translations. Nothing else has to be installed.

## Windows 10/11 x64

1. Download `oes-nightly-windows-x64.zip` and unpack it.
2. Run `launcher.exe` from the `oes` folder.

The programs are not code-signed. If SmartScreen stops them, click **More info → Run anyway**.

## Linux x64

Requires Ubuntu 22.04, Debian 12 or newer, and GTK 3 (`libgtk-3-0`). A PostgreSQL base also
needs `libpq5`.

```bash
tar xzf oes-nightly-linux-x64.tar.gz
cd oes
./launcher
```

## macOS 14+ on Apple Silicon

1. Open `oes-nightly-macos-arm64.dmg` and drag the **whole `OES` folder** to Applications. The
   applications start each other, so they have to stay together.
2. Run this once in Terminal:
   ```bash
   find /Applications/OES -exec xattr -d com.apple.quarantine {} \; 2>/dev/null
   ```
   The build is signed ad hoc, not with a Developer ID, so macOS quarantines every file in it.
   Right-click → Open is not enough: it clears one application, and the launcher then starts
   others. macOS 15 no longer offers it at all.
3. Open `launcher` from the `OES` folder.

## What is in the folder

| Program | What it is for |
|---|---|
| `launcher` | Keeps the list of bases: add one, then open it in the studio or the workspace |
| `appstudio` | The studio: builds the application and writes it into the database; see [The studio](../studio/) |
| `appworkspace` | The workspace: the application itself, working in the base |
| `appcluster` | Serves bases without a window, a server process per base; see [Application server](../application-server/) |
| `appcluster-tray` | Shows the cluster's servers and starts, stops or restarts them |
| `appserver` | The server the cluster starts for a base; by hand it only seals a password |
| `webserver` | Serves the application to a browser |
| `coderunner` | Writes and runs a script on its own, with no base and no metadata |
| `_fb/` | The Firebird engine. Keep it beside the programs |
| `lang/` | The interface translations |

## Starting without the launcher

`appstudio` and `appworkspace` accept the same options:

| Option | Meaning |
|---|---|
| `--file=<folder>` | A file base: the folder that holds it |
| `--address=oes://<host>[:port]/<base>` | A base an application server serves (`oess://` over TLS) |
| `--address=https://<host>[:port]/<base>/` | A base a web server publishes, by its address as a browser shows it |
| `--trusted=<file.pem>` | For `oess://`: the authorities to trust instead of the system's store |
| `--server=<host>` `--dbport=<port>` `--db=<name>` | A PostgreSQL base |
| `--user=<name>` `--password=<secret>` | The DBMS login (PostgreSQL) |
| `--ibuser=<name>` `--ibpwd=<secret>` | A user of the base itself |
| `--locale=en\|ru\|uk` | The interface language |

`appworkspace --debug` lets the studio's debugger attach to the running workspace.

```bash
appworkspace --file=C:\bases\trade --ibuser=Admin --locale=en
```

## Next

[First steps](../first-steps/): create a base and build a small application in it.
