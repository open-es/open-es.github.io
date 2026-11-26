---
layout: guide
lang: en
ref: metadata-tree
title: The metadata tree
description: Every kind of object an OES application is made of.
section: studio
order: 6
permalink: /guide/metadata-tree/
---

The tree on the left of the studio is the whole application. Its root is the
application itself, with its name and properties. Under it are
the **Shared** objects that belong to no single business object, then the business objects
in the order they are usually read.

## Business objects

| Group | What it holds | Example |
|---|---|---|
| **Integration → HTTP services** | Addresses the application answers HTTP requests on, each with its handler | An order intake for a website |
| **Constants** | One value each, for the whole base | Company name, base currency |
| **Catalogs** | Lists of things, flat or hierarchical, with predefined items | Products, clients, warehouses |
| **Documents** | Events with a date and a number; posting writes them into registers | Receipt, invoice, payroll sheet |
| **Document journals** | One list over documents of several kinds | All sales documents |
| **Enumerations** | A closed set of values, fixed in the application | Order status, VAT rate kind |
| **Data tools** | Tools with forms and code, but no stored data of their own | Import, bulk update |
| **Reports** | Questions about the data, with their own composers | Stock balance, sales by month |
| **Charts of characteristic types** | Kinds of analytics a value can be | "Analytics of an account" |
| **Charts of accounts** | Accounts, their hierarchy and what each is broken down by | A general ledger |
| **Charts of calculation types** | Kinds of accrual and deduction, and how they interact | Salary, bonus, sick leave |
| **Information registers** | Facts by key, optionally over time | Prices, exchange rates, an employee's position |
| **Accumulation registers** | Quantities and amounts that add up to balances or turnovers | Stock, settlements |
| **Accounting registers** | Double-entry bookkeeping over a chart of accounts | The ledger |
| **Calculation registers** | Payroll results over periods: displacement, base, recalculation | Accruals |
| **Sequences** | The order documents were posted in, for reposting from a point | Cost calculation order |

Which register to choose:

- a **value that changes at points in time** (a price, a rate, a position) → information register;
- **amounts that add up** (stock on hand, what a client owes) → accumulation register;
- **double entry** → accounting register over a chart of accounts;
- **accruals that cut and feed each other** (sick leave cuts salary, a bonus is a share of
  salary) → calculation register over a chart of calculation types.

## Inside an object

Open any object and it has some of these groups:

| Group | What it is |
|---|---|
| **Attributes** | Its fields. Each has a type: a string, a number, a date, a reference to another object… |
| **Tables** | Its tabular sections: the lines of an invoice, the members of a group |
| **Dimensions / Resources** | For registers: what a record is keyed by, and what it holds |
| **Enum values** | For enumerations: the values |
| **Forms** | Windows to show it in; without them the platform generates one. See [Forms](../forms/) |
| **Commands** | Actions the object offers on its forms and in the navigation |
| **Templates** | Print forms and other layouts; and composers — what a report reads and how it groups and totals it |
| **Modules** | The object module (code run when the object is written or posted) and the manager module (code for the whole kind). See [Modules and the language](../modules-and-language/) |

## Shared

| Group | What it holds |
|---|---|
| **Shared modules** | Code shared by the whole application |
| **Shared forms** | Forms that belong to no object |
| **Shared commands, Command groups** | Actions and how they are grouped in the interface |
| **Shared templates** | Layouts that belong to no object |
| **Scheduled jobs, Predefined jobs** | Code run on a schedule, in a session of its own |
| **Event handlers** | One handler for the same event of many objects |
| **Session parameters** | Values set at login and read by code for the rest of the session |
| **Feature options** | Switches that show or hide parts of the application |
| **Shared attributes** | One attribute added to many objects at once |
| **Pictures** | Icons and images |
| **Sections** | The navigation panel: what users see first |
| **Roles** | Rights per object and operation. A role either grants or restricts; row-level rules live in the role's code |
| **Languages** | The languages captions are written in |

## How the tree becomes tables

Each stored object becomes database tables, and each attribute becomes a column. When you
press **Update database**, the studio compares the application with the base and changes
only what differs. You never write table definitions by hand.
