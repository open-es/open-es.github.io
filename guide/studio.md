---
layout: guide
lang: en
ref: studio
title: The studio
description: "The studio: the window, the menus, saving and running."
section: studio
order: 5
permalink: /guide/studio/
---

The studio (`appstudio`, the launcher's **Studio (GUI)**) is where an application is made: its metadata,
forms, modules, roles and reports. It also writes the application into the database, runs it under a
debugger, and lets an AI assistant work beside you.

## The window

| Part | What it shows |
|---|---|
| **Metadata tree** (left) | The application: every object it declares, grouped by kind. See [The metadata tree](../metadata-tree/) |
| **Properties** | Whatever is selected: in the tree, on a form, in a table. Every property carries its own help text |
| **Editors** (centre, as tabs) | An object, a form, a module, a template: whatever was opened from the tree |
| **Messages, Call stack, Watch** (bottom) | Compiler messages, and the debugger's panels while it is attached |

## Working in the tree

Right-click a group (**Catalogs**, **Documents**…) → **New** to add an object. Right-click an
object for **Edit**, **Remove**, **Properties**, and to open its modules. Double-click opens
the object's own editor: its name, synonym (the caption users see), comment, default forms,
and its attributes, tables, forms, templates and modules.

## Saving and updating the database

| Action | What it does |
|---|---|
| **Save** (toolbar) | Saves the open editor's changes into the application being edited |
| **Update database** (toolbar, or **Application → Update database**) | Writes the edited application into the base and restructures its tables. Until then, the workspace runs the previous one |
| **Application → Revert to the database's application** | Discards the edits and goes back to what the base holds |
| **Application → Save application / Load application** | Writes the application to a file, or reads one in |
| **Application → Save application to files… / Load application from files…** | The application as a directory of text files, for version control ([the layout](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/application-files.md)) |
| **Application → Compare applications** | With a file, with the database's application, or two files with each other |

An update that restructures stored data needs the base to itself: close the workspace first.

## Running and debugging

| Debug menu | What it does |
|---|---|
| **Start debugging → Workspace (GUI) / Workspace (Web)** | Starts the workspace on this base, on the desktop or in the browser, with the debugger attached |
| **Start without debugging** | The same, nothing attached |
| **Attach for debugging…** | Attaches to a workspace that is already running |
| **Continue, Step into, Step over, Stop debugging** | The usual |
| **Performance measurement** | Times every client call and every line of script while the workspace runs |

Click in a module's margin to set a breakpoint. A breakpoint can carry a condition (right-click
→ **Breakpoint condition…**) so it stops only when, say, `i > 500`. While stopped, hover over a
name to see its value, or add it to **Watch**.

The debugger works over TCP, so it can also attach to the web server.

## Administration

| Administration menu | What it does |
|---|---|
| **Users** | The base's users and their roles |
| **Active users** | Who is connected now |
| **Registration journal** | What the base recorded: logins, writes, postings |
| **Web addresses** | What the base answers on a web server beside its clients |
| **Dump database / Restore database / Clear database** | The base to a file and back, or cleared |
| **Assistant window** | The conversation with an assistant working in this base |

**Tools → Theme…** chooses the light or the dark look.

## Assistant access (MCP)

**Tools → Options… → Assistant access (MCP)** opens the assistant's port on your machine, so an
assistant such as Claude Code can read and edit the application, update the database, run code and
compose reports. Setup and the first request:
[Building an application with an assistant](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/ai-quickstart.md).
