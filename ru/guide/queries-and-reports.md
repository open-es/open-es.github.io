---
layout: guide
lang: ru
ref: queries-and-reports
title: Запросы и отчёты
description: "Чтение данных: язык запросов, LINQ и компоновщик отчётов."
section: studio
order: 9
permalink: /ru/guide/queries-and-reports/
---

## Язык запросов

Запрос читает прикладные объекты по их именам, а не по именам таблиц:

```sql
SELECT T.Product, T.Product.Unit, T.Quantity
FROM Document.Receipt.Goods AS T
WHERE T.Quantity > &Min
```

Ссылка читается насквозь до объекта, на который указывает (`T.Product.Unit`), а `JOIN` работает как
в SQL (`LEFT JOIN Catalog.Products AS P ON P.Ref = T.Product`).

У регистров есть также **виртуальные таблицы**, которые вычисляют то, что у них обычно спрашивают:

| Регистр | Виртуальные таблицы |
|---|---|
| Накопления | `Balance`, `Turnovers`, `BalanceAndTurnovers` |
| Сведений | `SliceLast`, `SliceFirst` |
| Бухгалтерии | остатки и обороты по счетам, `DrCrTurnovers`, `RecordsWithAccountDimensions` |
| Расчёта | `ActualActionPeriod`, `ScheduleData`, `Recalculation` |

```sql
SELECT B.Product, B.QuantityBalance
FROM AccumulationRegister.Stock.Balance(&At, Warehouse = &Warehouse) AS B
```

Также доступны: вложенные запросы, временные таблицы и пакеты запросов, `TOTALS BY … HIERARCHY`,
`CAST`, `VALUE(...)` и параметры (`&Name`), сохраняющие свой тип. Запрос выполняется как SQL на
сервере везде, где это возможно, а иначе — в памяти, и ответ в обоих случаях одинаков.

**Конструктор запроса** в Application Studio редактирует запрос визуально, внутри строкового литерала, в котором
стоит курсор.

## Выполнение запроса из кода

```c
var q = new Query(
    "SELECT B.Product, B.QuantityBalance
     FROM AccumulationRegister.Stock.Balance(&At) AS B");
q.SetParameter("At", CurrentDate());
var table = q.Execute().Unload();   // a value table
```

`Execute().Select()` обходит строки по одной, а не загружает их все.

## LINQ

Те же данные без строки запроса:

```c
var recent = from d in Data.Documents.Receipt
             where d.Date >= From
             select { N = d.Number, D = d.Date };
```

## Отчёты

Объект **Отчет** содержит один или несколько **компоновщиков**. Компоновщик говорит, что читать (запрос),
а остальное оставляет настройкам пользователя:

- группировки, итоги, кросс-таблицы;
- расшифровка из ячейки до записей, которые за ней стоят;
- параметры и фильтры;
- варианты, которые пользователь сохраняет для себя.

Результат — табличный документ. Он печатается и выгружается в Excel (`.xlsx`) и Word
(`.docx`).

## Шаблоны и печатные формы

**Шаблон** — табличный документ с именованными областями. Код заполняет области и собирает
документ для печати: счёт, накладную, расчётный листок. Рисуют их в редакторе шаблонов Application Studio.
