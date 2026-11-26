---
layout: guide
lang: en
ref: pitfalls
title: Pitfalls
description: What goes wrong when starting, working with a base or building — and what to do.
section: running
order: 12
permalink: /guide/pitfalls/
---

Each entry gives the symptom, why it happens, and what to do.

## Starting the programs

**Windows: SmartScreen says "Windows protected your PC".**
The programs are not code-signed. Click **More info → Run anyway**.

**macOS: an application "is damaged and can't be opened", or "cannot be checked for malicious
software".**
macOS quarantines every file of an ad-hoc-signed download. Run this once:
`find /Applications/OES -exec xattr -d com.apple.quarantine {} \; 2>/dev/null`.
Right-click → Open clears only one application.

**macOS: the launcher opens, but the studio or the workspace does not start.**
One application was moved out of the `OES` folder. They start each other as siblings, so keep
the folder whole.

**Linux: `error while loading shared libraries: libgtk-3.so.0`.**
Install GTK 3: `sudo apt install libgtk-3-0`. A PostgreSQL base also needs `libpq5`.

**A file base does not open.**
Keep the `_fb` folder beside the programs: it holds the Firebird 5 engine the packages ship
with. A system Firebird does not replace it. Ubuntu 22.04 and Debian 12 ship Firebird 3, which
cannot open these bases.

## Working with a base

**`appworkspace` opens an empty application.**
The base's application is empty. Build it in the studio and press **Update database** first.

**The application server refuses a base, or the studio refuses a base the server is
serving.**
A base is used either by application servers or by programs that open it as a file base
(`appstudio`, `appworkspace`), never by both at once. Close one side, or reach the base
through the server: `--address=oes://<host>/<base>`.

**Updating the database waits or is refused.**
A change that restructures stored data needs the base to itself. Close `appworkspace` and any
other session on that base, then update again.

**Two studios are open, and the assistant edits the wrong one.**
One address and port reach one studio: the one that started listening first. Give each
studio its own port under **Tools → Options… → Assistant access (MCP)**.

## Building

**`'cmake'` or `'msbuild'` is not recognized.**
Use a Visual Studio command prompt: "x64 Native Tools Command Prompt for VS 2022" for the
CMake presets, or "Developer Command Prompt" for `msbuild`.

**`wx/wx.h: No such file or directory`, or CMake cannot find wxWidgets.**
The submodules are missing. Run `git submodule update --init --recursive`. Run it again after
every `git pull`, because a pull can bring a new submodule.

**`enterprise.sln`: `LNK1104: cannot open file 'wxbase33u….lib'`.**
The solution links prebuilt wxWidgets and does not build it. Build the DLL configurations of
`src\3rdparty\wxWidgets\build\msw\wx_vc17.sln` first; see
[Build from source](../build-from-source/#build-with-visual-studio-windows).

**Release x64: `C1002: compiler is out of heap space in pass 2`.**
The 32-bit compiler host runs out of memory on the largest files. Build with
`/p:PreferredToolArchitecture=x64`.

**`C1060: compiler is out of heap space` in random files, or the machine freezes during a
build.**
Too many compilers are running at once. With CMake outside a preset, use `--parallel 3` on
16 GB of RAM or `--parallel 2` on 8 GB (the presets already limit the job count on Linux and
macOS). With MSBuild, use `/m:1`.

**`fatal: Unable to find current revision in submodule path 'src/3rdparty/wxWidgets'`.**
An interrupted build or fetch left the submodule broken.
`rm -rf src/3rdparty/wxWidgets`, then `git submodule update --init --recursive`.

**`LNK1168: cannot open … for writing`.**
A program from the output folder is still running and holds its file. Close `appstudio`,
`appworkspace`, `launcher`, `appcluster` and its `appserver` processes, then build again.

**After `git pull`, the build crashes at start, or the linker asks for a function the sources no
longer have.**
Object files from an older or interrupted build are mixed with new ones. Rebuild cleanly:
`/t:Rebuild` with MSBuild, `--clean-first` with CMake.

## Still stuck

Open an [issue](https://github.com/open-enterprise-solutions/enterprise/issues). Include the
platform, the package or commit you started from, and the exact message.
