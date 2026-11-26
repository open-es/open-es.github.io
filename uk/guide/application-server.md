---
layout: guide
lang: uk
ref: application-server
title: Сервер застосунків
description: "Обслуговування баз OpenES без вікна: сервер застосунків і його папка."
section: running
order: 10
permalink: /uk/guide/application-server/
---

`appcluster` обслуговує бази, перелічені в його **папці сервера**, без вікна. Для кожної бази він
запускає власний сервер застосунків — процес `appserver`, — тримає єдиний порт, через який заходять
усі клієнти, і передає кожного клієнта серверу його бази. Бази Firebird і PostgreSQL можуть працювати
поруч. Збій сервера однієї бази завершує лише цей процес: кластер запускає його знову, а інші
бази цього не помічають.

Запущений вручну, він пише журнал у свою консоль і працює до Ctrl+C. Його також можна запускати як службу Windows або
під systemd. `appcluster-tray` показує його сервери й керує ними.

## Папка сервера

```
<server folder>/          by default: "server" beside appcluster
  server.conf             Host and Port; one group per base
  server.key              this installation's key, made at the first start
  cluster.token           the control socket's key, while the cluster runs
  <Id>/                   one folder per base: its journal and infobase.conf
```

```ini
Port=7373

[trade]
Kind=firebird
Path=C:\bases\trade

[stock]
Kind=postgresql
Server=localhost
Port=5432
Database=stock
User=oes
Processes=2
```

| Ключ | Значення |
|---|---|
| `Host` (верхній рівень) | Адреса, до якої підключаються клієнти; `127.0.0.1`, якщо не вказано. Будь-яка адреса поза цим комп'ютером потребує сертифіката (див. нижче) |
| `Port` (верхній рівень) | Порт, до якого підключаються клієнти; 7373, якщо не вказано |
| `[name]` | Одна база. Ім'я — те, яке запитують клієнти |
| `Kind` | `firebird` або `postgresql` |
| `Path` | Firebird: папка бази |
| `Server`, `Port`, `Database`, `User`, `Password` | PostgreSQL: з'єднання |
| `IbUser`, `IbPassword` | Власний користувач бази, під яким входить сервер |
| `Processes` | Скільки серверів обслуговують цю базу паралельно; 1, якщо не вказано |
| `Id` | Призначається під час першого запуску й записується назад; не змінюйте його |

## Паролі

Паролі **запечатуються** ключем інсталяції (AES-256-GCM); пароль, записаний відкритим
текстом, відхиляється. Запечатайте пароль, увівши його з клавіатури:

```bash
appserver --set-password=stock/Password
appserver --set-password=stock/IbPassword
```

Тримайте `server.key` разом із `server.conf`: без нього запечатане значення не відкривається.

## Параметри

| Параметр | Значення |
|---|---|
| `--dir=<folder>` | Інша папка сервера |
| `--locale=<code>` | Мова повідомлень у консолі |
| `--install` / `--uninstall` | Зареєструвати як службу Windows або видалити службу (від імені адміністратора) |
| `--service` | Працювати як служба: під диспетчером служб Windows або з юніта systemd |

## Правила

- **Базу та її таблиці створює Application Studio.** Сервери відкривають наявні бази і не
  створюють їх.
- **Сервери і програми файлової бази не використовують базу спільно.** Сервери спільно використовують базу між собою,
  і програми, що відкривають файлову базу, — між собою, але сервер не отримає базу, яку тримає
  програма файлової бази, і навпаки.
- **Сервер, що впав, запускається знову**: через секунду, а після коротких життів — дедалі пізніше.
  Його сеанси втрачаються разом із ним; клієнт входить знову.
- **Кілька серверів на одній базі** спільно використовують її сеанси, блокування записів і захоплення завдань через базу,
  тож регламентне завдання виконується один раз на всі сервери.
- `backend.conf` поруч із програмою задає обмеження процесу (`Connections` за замовчуванням і
  `MemoryLimit` у мегабайтах), а `infobase.conf` бази задає її власний `Connections`.
- **TLS.** Якщо в `backend.conf` є сертифікат (`Certificate`, `PrivateKey`), порт працює через TLS;
  без нього він слухає лише на цьому комп'ютері, а адреса поза ним відхиляється під час запуску.

## Клієнти

Клієнти підключаються до `oes://<host>[:port]/<base>` (`oess://` через TLS) — через WebSocket або через HTTP
з JSON. Протокол описано в
[client-protocol.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/client-protocol.md),
кластер — в
[application-cluster.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/application-cluster.md).
