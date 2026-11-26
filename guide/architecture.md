---
layout: guide
lang: en
ref: architecture
title: How the platform is built
description: "How the platform is built: programs, libraries, databases."
section: running
order: 11
permalink: /guide/architecture/
---

OpenES is written in C++20 on wxWidgets. It builds with MSVC on Windows, with GCC on Linux and with
Clang on macOS, and CI runs the tests on all three. The full description is in
[ARCHITECTURE.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/ARCHITECTURE.md).

## Programs and libraries

```
   appstudio      appworkspace                   browser
        │               │                           │
        └───────┬───────┘                           ▼
                ▼                               webserver
            frmclient   (draws the forms)           │
                │                                   │
                │◄──── client protocol ────────────►│
                ▼                                   ▼
   fileserver (a file base,          appcluster ─► appserver, one or more per base
   in the client's own process)                     │
                │                                   │
                └──────────────┬────────────────────┘
                               ▼
               frmserver   (the forms, on the server)
                               ▼
                backend    (the engine, no UI)
                               │
              Firebird · PostgreSQL · ODBC · SQLite
```

| Part | What it does |
|---|---|
| **backend** | The engine: metadata, the compiler and bytecode interpreter, the query engine and the report composer, sessions, locks, jobs, the database layer, the debugger server. It has no UI |
| **frmserver** | The forms and their controls, held beside the engine; and the assistant's tools (MCP) |
| **frmclient** | Under the studio and the workspace: draws what the server's forms say, sends back what the person did. It runs no application code |
| **deskgui** | The desktop's user interface: the controls and dialogs the platform draws itself, the light and the dark look |
| **core** | Under every library: strings, numbers, dates, serialization, the query text, the network and TLS, pictures (PNG and SVG) |
| The programs | Each one picks how it reaches a base, then hands over to the libraries. `launcher` and `coderunner` sit on the engine directly |

## Inside the engine

| Subsystem | What it does |
|---|---|
| **Metadata** | Holds the application as a tree of objects. Updating the database compares it with the base and changes the tables by a diff, showing the exact DDL first |
| **Compiler** | Lexer, parser, bytecode (82 opcodes), interpreter, LINQ. Compiled modules are cached per application version |
| **Query engine** | Parses the query language, renders it to the dialect of the database, or runs it in memory when SQL cannot express it |
| **Composer** | Turns a report's settings into a query and its result into a spreadsheet |
| **Sessions** | One per user, job or service. A session holds its connection, its running code and its windows |
| **Locks** | Record locks shared across processes, so two servers on one base do not overwrite each other |
| **Jobs** | Scheduled and background jobs; a job runs once across all servers sharing a base |
| **Database layer** | One interface over Firebird, PostgreSQL, ODBC and SQLite; every statement is prepared, never concatenated |
| **Debugger** | A server in the running process, on this machine's port 7700-7709; the studio connects to it |
| **MCP server** | The assistant's access to the studio and the workspace: about 140 tools, on a port of the person's own machine, protected by a token, every call journalled |

## Processes and bases

A program that opens a file base holds that one base. An application server holds one base too,
and the cluster runs one or more of them per base, so a fault in one base's process does not reach
the others. Every base has its own connection pool, sessions, locks, jobs and journal, and a thread
always works for a definite base: there is no global "current" one.

## Databases

| Database | Role |
|---|---|
| **Firebird 5** | The default. Embedded in the packages: a file base needs no server |
| **PostgreSQL** | Production with several users |
| **ODBC** | The road to other databases |
| **SQLite** | Tests and logging |

## Where to read more

- [ARCHITECTURE.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/ARCHITECTURE.md): the system, directory by directory
- [docs/public/](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/README.md): design documents (numbers and dates, the query language, registers, access rights, reports, jobs, record locks, the debugger, the application cluster, the client protocol)
- [development.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/development.md): how changes are made and reviewed
