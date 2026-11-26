---
layout: guide
lang: en
ref: modules-and-language
title: Modules and the language
description: Where code lives in an application, and what the language looks like.
section: studio
order: 8
permalink: /guide/modules-and-language/
---

## Where code lives

| Module | Belongs to | Runs |
|---|---|---|
| **Object module** | A catalog, document, register… | On that object's events: `BeforeWrite`, `OnWrite`, `BeforeDelete`, `OnDelete`, and for a document `Posting` and `UndoPosting` |
| **Manager module** | The kind as a whole | Code that is about all products rather than one: lookups, form selection (`FormGetProcessing`), choice data |
| **Form module** | A form | The form's events and its commands |
| **Shared module** | The application | Shared procedures, called from anywhere |

An event handler is a procedure with the event's name. The name is fixed: a procedure under
another name is never called. The editor lists the handlers an object offers.

Code runs wherever the object is used: a form, another module, a background job, the
application server. An object module never assumes a window.

## Two dialects, one compiler

| | CES (C-style, the default) | VES (keyword style) |
|---|---|---|
| Function | `function Compute(x) { return x * 2; }` | `Function Compute(x) Return x * 2 EndFunction` |
| Loop | `foreach (item in items) { Message(item.Name); }` | `Foreach item In items Do Message(item.Name) EndDo` |

Both compile to the same bytecode. New applications use CES. Do not mix the two in one
module: each compares and joins conditions in its own spelling — CES `==`, `!=`, `&&`, `||`, `!`;
VES `=`, `<>`, `And`, `Or`, `Not`. Keywords are case-insensitive, but each has exactly one spelling.

## What the language has

- Procedures and functions, `var`, `if`, `while`, `foreach`, `try … except`
- Lambdas with closure capture
- Multi-line strings, convenient for query texts
- **LINQ** compiled into the bytecode:
  ```c
  var q = from o in Orders
          where o.Total > Threshold
          orderby o.Date descending
          select { N = o.Number, V = o.Total };
  ```
- Built-in values: value tables and trees, structures, maps, arrays, spreadsheet documents,
  JSON and XML, HTTP and HTTPS clients, files
- Bytecode is cached per application version, so a module compiles once

## Reaching the data

| Write | To get |
|---|---|
| `Catalogs.Products` | The products manager: create, find, select |
| `Documents.Receipt` | The receipts manager |
| `InformationRegisters.Prices`, `AccumulationRegisters.Stock`… | The register |
| `Constants.CompanyName` | A constant |
| `Data.Catalogs.Products` | The same catalog as a LINQ source |
| `new Query("SELECT …")` | A query; see [Queries and reports](../queries-and-reports/) |

Inside a document's object module, `RegisterRecords.Stock` is the set of records the document
will write to the `Stock` register when it is posted.

## Trying code without a base

`coderunner` is a scratch window: write a script, check its syntax, run it. It needs no base and
no application.

## Captions in several languages

A caption is written in every language the application declares, as one string:
`en = 'Employee'; ru = 'Сотрудник'; uk = 'Працівник';`. `Tstr(text)` picks the user's language
in code.
