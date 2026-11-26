---
layout: guide
lang: en
ref: introduction
title: Introduction
description: What OpenES is, and the words used throughout.
section: start
order: 1
permalink: /guide/introduction/
---

## What OpenES is

OpenES is a platform for line-of-business applications. You do not write the application from
scratch. You **describe** it, and the platform builds the rest from the description:

| You describe | The platform makes |
|---|---|
| The objects the business keeps: products, clients, invoices, stock balances | Database tables, kept in step as the description changes |
| What the user sees | Forms, lists, choice dialogs (generated when you draw none) |
| The rules: what posting an invoice does, what a field may hold | Compiled code, run on every write, post or command |
| The questions: stock by warehouse, sales by month | Queries run as SQL, and reports with totals and drill-down |

The same application runs as a desktop program and in a browser. The studio and the workspace
each open an assistant's port (MCP), so an AI assistant can build and check an application
alongside you.

## The words used throughout

| Word | Meaning |
|---|---|
| **Base** (infobase) | One application's data and its description, stored together in one database |
| **Application** | The description: its metadata, forms, modules, roles |
| **Metadata** | The objects an application declares: catalogs, documents, registers, reports… See [The metadata tree](../metadata-tree/) |
| **Catalog** | A list of things: products, clients, warehouses |
| **Document** | An event with a date: a receipt, an invoice, a payroll sheet |
| **Posting** | What a document does to registers when it is confirmed |
| **Register** | Where facts accumulate: balances, prices over time, accounting entries |
| **Module** | Code attached to an object, a form or the whole application |
| **Form** | A window the user works in |
| **Updating the database** | Writing a changed application into the base, which restructures its tables |

## Two programs, two roles

- **Studio** (`appstudio`, OES Application Studio): where the application is made. A developer works here.
- **Workspace** (`appworkspace`, OES Application Workspace): where the application is used. Users enter
  documents and run reports here.

Both open the same base. The studio edits the application, writes it into the database, and can start
the workspace under its debugger.

## Where a base lives

| Kind | Where | For |
|---|---|---|
| **File base** | A folder on disk. The Firebird engine ships inside the package, so there is no server to install | Development, a single user, a small office |
| **PostgreSQL base** | A PostgreSQL server you run | Several users, production |

The [application cluster](../application-server/) serves many bases, Firebird and PostgreSQL
together, from one server folder — each base in a server process of its own.

## Languages

The interface is available in English, Russian and Ukrainian. An application declares its own
languages, and every caption in it can be written in each of them.

## Next

[Installation](../installation/) to get it running, then [First steps](../first-steps/).
