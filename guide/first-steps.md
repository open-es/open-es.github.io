---
layout: guide
lang: en
ref: first-steps
title: First steps
description: A small stock application in OpenES, from an empty base to a report.
section: start
order: 4
permalink: /guide/first-steps/
---

This page builds a small stock application: products, a receipt document that brings goods
in, a register that keeps the balance, and a report that reads it. It takes about fifteen
minutes and touches each part of the studio once.

## 1. Create a base

1. Start `launcher` and press **Add**.
2. Name the base `Stock`, choose **File (local database)**, and pick an empty folder.
3. Press **Save connection**, select the base and press **Studio (GUI)**.

The studio opens with an empty application. On the left is the
[metadata tree](../metadata-tree/); selecting anything in it shows its properties.

## 2. A catalog of products

1. In the tree, right-click **Catalogs** → **New**. Name it `Products`.
2. A catalog already has a code and a description. Add one more attribute: open the catalog,
   go to **Attributes**, add `Unit` of type *String*.

## 3. A register that keeps the balance

1. Right-click **Accumulation registers** → **New**. Name it `Stock`.
2. Under **Dimensions**, add `Product` of type *Catalog reference: Products*.
3. Under **Resources**, add `Quantity` of type *Number*.

A *dimension* is what the balance is broken down by. A *resource* is what is summed.

## 4. A receipt document

1. Right-click **Documents** → **New**. Name it `Receipt`.
2. Under **Tables**, add a table `Goods` with columns `Product` (*Catalog reference:
   Products*) and `Quantity` (*Number*).
3. In the document's properties, set **List register record** to include `Stock`. These are the
   registers the document may write to.
4. Open its **object module** (right-click the document → **Open object module**) and write
   what posting does:

```c
procedure Posting(Cancel, PostingMode) {
    foreach (row in Goods) {
        var rec = RegisterRecords.Stock.Add();
        rec.RecordType = AccumulationRecordType.Receipt;
        rec.Period     = Date;
        rec.Product    = row.Product;
        rec.Quantity   = row.Quantity;
    }
}
```

The handler's name, `Posting`, is fixed: the platform calls it when the document is posted and
writes the records it collected. The editor completes names as you type (Ctrl+Space), so the
exact spelling of each field is always one keystroke away.

## 5. Update the database and run

1. Press **Update database** on the toolbar. The studio writes the application into the base
   and creates the tables.
2. **Debug → Start without debugging → Workspace (GUI)** starts the workspace on the same base.
3. In the workspace, add two or three products, then create a receipt, fill its table and post it.

You did not draw a single form. The platform generated them from the metadata. To draw your
own, see [Forms](../forms/).

## 6. Read the balance

A query that reads the register's balance, as of now:

```sql
SELECT B.Product, B.QuantityBalance
FROM AccumulationRegister.Stock.Balance(&At) AS B
```

Run it from code:

```c
var q = new Query("SELECT B.Product, B.QuantityBalance FROM AccumulationRegister.Stock.Balance(&At) AS B");
q.SetParameter("At", CurrentDate());
var table = q.Execute().Unload();
```

or make it a **Report** and let the report composer group, total and print it. See
[Queries and reports](../queries-and-reports/).

## Where next

- Set a breakpoint in `Posting` and use **Debug → Start debugging → Workspace (GUI)** to step through it.
- Add a role under **Shared → Roles** to decide who may read and post receipts.
- Let an assistant do the same steps:
  [Building an application with an assistant](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/ai-quickstart.md).
