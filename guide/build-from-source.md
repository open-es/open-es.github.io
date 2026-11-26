---
layout: guide
lang: en
ref: build-from-source
title: Building from source
description: Build OES yourself on Windows, Linux or macOS.
section: start
order: 3
permalink: /guide/build-from-source/
---

CMake builds every platform; CI and the nightly packages use it. On Windows there is also the
Visual Studio solution, `enterprise.sln`, for working in the IDE. Details, options and CI are
in [BUILD.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/BUILD.md).

## What you need

| Platform | Install |
|---|---|
| Windows | Visual Studio 2022 (toolset `v143`) with the **Desktop development with C++** workload, which includes CMake and Ninja; Git |
| Linux (Ubuntu 22.04 / Debian 12) | `sudo apt install build-essential cmake ninja-build pkg-config git libgtk-3-dev uuid-dev` |
| macOS | Xcode Command Line Tools (`xcode-select --install`), then `brew install cmake ninja` |

On Linux, `libgtk-3-dev` and `uuid-dev` are required. You do not need a system wxWidgets: the
build compiles its own copy from the submodule. You do not need Firebird or PostgreSQL
development packages either, because the drivers load their client libraries at run time.

## Get the sources

```bash
git clone https://github.com/open-enterprise-solutions/enterprise.git
cd enterprise
git checkout develop
git submodule update --init --recursive
```

- **`develop` is where work happens**; the nightly is built from it. `master` lags behind it.
- **`--recursive` is required**: wxWidgets has submodules of its own.
- **`docs/private` stays empty** unless you are a member of the organisation. The build does
  not need it, and the command above skips it.

## Build with CMake

Pick the preset for your platform: `windows-x64-*`, `windows-x86-*`, `linux-*` or `macos-*`,
each with `-debug` or `-release`.

```bash
cmake --preset linux-release -DOES_USE_POSTGRESQL=ON
cmake --build --preset linux-release
```

- The programs land in `build/<preset>/bin/<Debug|Release>/`.
- **The first build also compiles wxWidgets**, so it takes much longer than later ones.
- **Firebird is on in every preset. PostgreSQL is not**: add `-DOES_USE_POSTGRESQL=ON`, as the
  nightly does.
- **Windows: run the commands from "x64 Native Tools Command Prompt for VS 2022"**, not a
  plain `cmd`. The presets take the compiler from the environment, and the plain "Developer
  Command Prompt" targets 32-bit.

Other options: `-DBUILD_TESTING=ON` builds the Google Test suites. The suites build only through CMake; the
Visual Studio solution has none.

## Build with Visual Studio (Windows)

The solution does **not** build wxWidgets. It links prebuilt wxWidgets DLLs from
`src\3rdparty\wxWidgets\lib\vc_dll` (x86) or `lib\vc_x64_dll` (x64). Build those once from the
submodule: open `src\3rdparty\wxWidgets\build\msw\wx_vc17.sln` and build the **DLL Debug**
and/or **DLL Release** configuration for the same platform you will use.

Then open `enterprise.sln`, choose `Debug|x86`, `Debug|x64`, `Release|x86` or `Release|x64`,
and build. From the command line, use a Developer Command Prompt:

```cmd
msbuild enterprise.sln /p:Configuration=Debug /p:Platform=x86 /m
msbuild enterprise.sln /p:Configuration=Release /p:Platform=x64 /p:PreferredToolArchitecture=x64 /m
```

The output goes to `bin\Win32\<Configuration>\` for x86 and `bin\Win64\<Configuration>\` for
x64. The Firebird kit is copied into `_fb\` beside the programs.

## Run what you built

Start `launcher` from the output folder and follow [First steps](../first-steps/).
When started from the build tree, the programs find the interface translations in the
repository's `lang/` folder.
