---
layout: guide
lang: en
ref: queries-and-reports
title: Queries and reports
description: "Reading data back: the query language, LINQ and the report composer."
section: studio
order: 9
permalink: /guide/queries-and-reports/
---

## The query language

A query reads the business objects by their names, not by table names:

```sql
SELECT T.Product, T.Product.Unit, T.Quantity
FROM Document.Receipt.Goods AS T
WHERE T.Quantity > &Min
```

A reference reads through to the object it points at (`T.Product.Unit`), and `JOIN` works as
in SQL (`LEFT JOIN Catalog.Products AS P ON P.Ref = T.Product`).

Registers also have **virtual tables** that compute what is usually asked of them:

| Register | Virtual tables |
|---|---|
| Accumulation | `Balance`, `Turnovers`, `BalanceAndTurnovers` |
| Information | `SliceLast`, `SliceFirst` |
| Accounting | balances and turnovers by account, `DrCrTurnovers`, `RecordsWithAccountDimensions` |
| Calculation | `ActualActionPeriod`, `ScheduleData`, `Recalculation` |

```sql
SELECT B.Product, B.QuantityBalance
FROM AccumulationRegister.Stock.Balance(&At, Warehouse = &Warehouse) AS B
```

Also available: nested queries, temporary tables and query packages, `TOTALS BY … HIERARCHY`,
`CAST`, `VALUE(...)`, and parameters (`&Name`) that keep their type. A query runs as SQL on
the server wherever possible, and in memory otherwise, with the same answer either way.

The studio's **query constructor** edits a query visually, inside the string literal the
caret is in.

## Running a query from code

```c
var q = new Query(
    "SELECT B.Product, B.QuantityBalance
     FROM AccumulationRegister.Stock.Balance(&At) AS B");
q.SetParameter("At", CurrentDate());
var table = q.Execute().Unload();   // a value table
```

`Execute().Select()` walks the rows one by one instead of loading them all.

## LINQ

The same data, without a query string:

```c
var recent = from d in Data.Documents.Receipt
             where d.Date >= From
             select { N = d.Number, D = d.Date };
```

## Reports

A **Report** object holds one or more **composers**. A composer says what to read (a query)
and leaves the rest to the user's settings:

- groupings, totals, cross tables;
- drill-down from a cell to the records behind it;
- parameters and filters;
- variants the user saves for themselves.

The result is a spreadsheet document. It prints, and exports to Excel (`.xlsx`) and Word
(`.docx`).

## Templates and print forms

A **template** is a spreadsheet layout with named areas. Code fills the areas and assembles a
printable document: an invoice, a waybill, a payslip. The studio's template editor draws them.
