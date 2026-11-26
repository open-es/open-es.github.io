---
layout: guide
lang: uk
ref: queries-and-reports
title: Запити і звіти
description: "Читання даних: мова запитів, LINQ і компонувальник звітів."
section: studio
order: 9
permalink: /uk/guide/queries-and-reports/
---

## Мова запитів

Запит читає прикладні об'єкти за їхніми іменами, а не за іменами таблиць:

```sql
SELECT T.Product, T.Product.Unit, T.Quantity
FROM Document.Receipt.Goods AS T
WHERE T.Quantity > &Min
```

Через посилання можна читати об'єкт, на який воно вказує (`T.Product.Unit`), а `JOIN` працює як
у SQL (`LEFT JOIN Catalog.Products AS P ON P.Ref = T.Product`).

Регістри мають також **віртуальні таблиці**, що обчислюють те, про що їх зазвичай запитують:

| Регістр | Віртуальні таблиці |
|---|---|
| Накопичення | `Balance`, `Turnovers`, `BalanceAndTurnovers` |
| Відомостей | `SliceLast`, `SliceFirst` |
| Бухгалтерії | залишки та обороти за рахунками, `DrCrTurnovers`, `RecordsWithAccountDimensions` |
| Розрахунку | `ActualActionPeriod`, `ScheduleData`, `Recalculation` |

```sql
SELECT B.Product, B.QuantityBalance
FROM AccumulationRegister.Stock.Balance(&At, Warehouse = &Warehouse) AS B
```

Також доступні: вкладені запити, тимчасові таблиці й пакети запитів, `TOTALS BY … HIERARCHY`,
`CAST`, `VALUE(...)` і параметри (`&Name`), що зберігають свій тип. Запит виконується як SQL на
сервері, де це можливо, інакше — у пам'яті, з однаковим результатом в обох випадках.

**Конструктор запиту** Application Studio редагує запит візуально, усередині рядкового літерала, у якому
стоїть курсор.

## Виконання запиту з коду

```c
var q = new Query(
    "SELECT B.Product, B.QuantityBalance
     FROM AccumulationRegister.Stock.Balance(&At) AS B");
q.SetParameter("At", CurrentDate());
var table = q.Execute().Unload();   // a value table
```

`Execute().Select()` обходить рядки по одному, замість завантажувати їх усі.

## LINQ

Ті самі дані без рядка запиту:

```c
var recent = from d in Data.Documents.Receipt
             where d.Date >= From
             select { N = d.Number, D = d.Date };
```

## Звіти

Об'єкт **Звіт** містить один або кілька **компонувальників**. Компонувальник визначає, що читати (запит),
а решту залишає налаштуванням користувача:

- групування, підсумки, крос-таблиці;
- розшифровка від клітинки до записів, що за нею стоять;
- параметри й фільтри;
- варіанти, які користувач зберігає для себе.

Результат — табличний документ. Його можна надрукувати та експортувати в Excel (`.xlsx`) і Word
(`.docx`).

## Шаблони та друковані форми

**Шаблон** — це макет табличного документа з іменованими областями. Код заповнює області та збирає
документ для друку: рахунок, товарно-транспортну накладну, розрахунковий листок. Малює їх редактор шаблонів Application Studio.
