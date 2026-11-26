---
layout: guide
lang: ru
ref: application-server
title: Сервер приложений
description: "Обслуживание баз OpenES без окна: сервер приложений и его папка."
section: running
order: 10
permalink: /ru/guide/application-server/
---

`appcluster` обслуживает базы, перечисленные в его **папке сервера**, без окна. Для каждой базы он
запускает собственный сервер приложений — процесс `appserver`, — держит единственный порт, через который входит каждый клиент,
и передаёт каждого клиента серверу его базы. Базы Firebird и PostgreSQL могут работать
рядом. Сбой в сервере одной базы завершает только этот процесс: кластер запускает его снова, а остальные
базы этого не замечают.

Запущенный вручную, он пишет журнал в свою консоль и работает до Ctrl+C. Он также может работать как служба Windows или
под systemd. `appcluster-tray` показывает его серверы и управляет ими.

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

| Ключ | Значение |
|---|---|
| `Host` (верхний уровень) | Адрес, к которому подключаются клиенты; `127.0.0.1`, если не задан. Любой адрес за пределами этой машины требует сертификата (см. ниже) |
| `Port` (верхний уровень) | Порт, к которому подключаются клиенты; 7373, если не задан |
| `[name]` | Одна база. Имя — то, что запрашивают клиенты |
| `Kind` | `firebird` или `postgresql` |
| `Path` | Firebird: папка базы |
| `Server`, `Port`, `Database`, `User`, `Password` | PostgreSQL: подключение |
| `IbUser`, `IbPassword` | Пользователь самой базы, под которым входит сервер |
| `Processes` | Сколько серверов обслуживают эту базу параллельно; 1, если не задан |
| `Id` | Назначается при первом запуске и записывается обратно; не трогайте его |

## Пароли

Пароли **запечатываются** ключом установки (AES-256-GCM); пароль, записанный открытым
текстом, отклоняется. Запечатать пароль, введя его с клавиатуры:

```bash
appserver --set-password=stock/Password
appserver --set-password=stock/IbPassword
```

Храните `server.key` вместе с `server.conf`: без него запечатанное значение не открывается.

## Параметры

| Параметр | Значение |
|---|---|
| `--dir=<folder>` | Другая папка сервера |
| `--locale=<code>` | Язык сообщений в консоли |
| `--install` / `--uninstall` | Сделать его службой Windows или удалить службу (от имени администратора) |
| `--service` | Работать как служба: под диспетчером служб Windows или из юнита systemd |

## Правила

- **Базу создаёт Application Studio** вместе с её таблицами. Серверы открывают существующие базы и не
  создают их.
- **Серверы и программы файловой базы не делят базу.** Серверы делят базу между собой, и
  программы, открывающие файловую базу, делят её между собой, но серверу отказывают в базе, которую держит
  программа файловой базы, и наоборот.
- **Упавший сервер запускается снова** через секунду, а после коротких жизней — ещё позже.
  Его сеансы теряются вместе с ним; клиент входит заново.
- **Несколько серверов на одной базе** делят её сеансы, блокировки записей и захват заданий через базу,
  поэтому регламентное задание выполняется один раз на всех.
- `backend.conf` рядом с программой задаёт лимиты процесса (`Connections` по умолчанию и
  `MemoryLimit` в мегабайтах), а `infobase.conf` базы задаёт её собственный `Connections`.
- **TLS.** С сертификатом в `backend.conf` (`Certificate`, `PrivateKey`) порт работает по TLS;
  без него он слушает только эту машину, и адрес за её пределами отклоняется при запуске.

## Клиенты

Клиенты подключаются к `oes://<host>[:port]/<base>` (`oess://` — через TLS) — по WebSocket или по HTTP
с JSON. Протокол описан в
[client-protocol.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/client-protocol.md),
кластер — в
[application-cluster.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/application-cluster.md).
