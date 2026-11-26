---
layout: guide
lang: ru
ref: installation
title: Установка
description: Скачать сборку OES для Windows, Linux или macOS и запустить её.
section: start
order: 2
permalink: /ru/guide/installation/
---

## Какая сборка

- **Последний выпуск** — [`v2.0.0-preview.1`](https://github.com/open-enterprise-solutions/enterprise/releases/latest),
  для Windows, Linux и macOS. Берите его, чтобы попробовать платформу. Его пакеты называются
  `oes-2.0.0-preview.1-…`; шаги ниже те же, только с этим именем вместо `oes-nightly-…`.
- **Ночная сборка**: собирается из `develop` каждую ночь, когда в нём что-то изменилось. Каждая новая сборка заменяет предыдущую
  по адресу [releases/tag/nightly](https://github.com/open-enterprise-solutions/enterprise/releases/tag/nightly).
  Берите её ради того, что вошло после выпуска.
- Выпуски до `v1.3.0` — альфа (1.x): их программы не работают с сервером 2.0, а их
  базы не переносятся.

Это руководство описывает ночную сборку. В `v2.0.0-preview.1` Application Studio — это `designer-thin`, Application Workspace —
`enterprise-thin`, а программа запуска кода — `codeRunner`; кнопки `launcher` — *Enterprise*, *Web* и *Designer*.

Каждый пакет содержит всё, что нужно программам: движок базы данных Firebird,
клиент PostgreSQL и переводы интерфейса. Больше ничего устанавливать не нужно.

## Windows 10/11 x64

1. Скачайте `oes-nightly-windows-x64.zip` и распакуйте его.
2. Запустите `launcher.exe` из папки `oes`.

Программы не подписаны цифровой подписью. Если SmartScreen их останавливает, нажмите **Подробнее → Выполнить в любом случае**.

## Linux x64

Нужны Ubuntu 22.04, Debian 12 или новее и GTK 3 (`libgtk-3-0`). Для базы PostgreSQL также
нужен `libpq5`.

```bash
tar xzf oes-nightly-linux-x64.tar.gz
cd oes
./launcher
```

## macOS 14+ на Apple Silicon

1. Откройте `oes-nightly-macos-arm64.dmg` и перетащите **всю папку `OES`** в «Программы».
   Приложения запускают друг друга, поэтому должны оставаться вместе.
2. Выполните один раз в Терминале:
   ```bash
   find /Applications/OES -exec xattr -d com.apple.quarantine {} \; 2>/dev/null
   ```
   Сборка подписана ad hoc, а не Developer ID, поэтому macOS помещает в карантин каждый её файл.
   Правый щелчок → «Открыть» не помогает: так снимается карантин с одного приложения, а `launcher` затем запускает
   другие. В macOS 15 этого пункта уже нет вовсе.
3. Откройте `launcher` из папки `OES`.

## Что лежит в папке

| Программа | Для чего |
|---|---|
| `launcher` | Хранит список баз: добавьте базу, затем откройте её в Application Studio или Application Workspace |
| `appstudio` | Application Studio: создаёт приложение и записывает его в базу данных; см. [Application Studio](../studio/) |
| `appworkspace` | Application Workspace: само приложение, работающее в базе |
| `appcluster` | Обслуживает базы без окна, по серверному процессу на базу; см. [Сервер приложений](../application-server/) |
| `appcluster-tray` | Показывает серверы кластера и запускает, останавливает или перезапускает их |
| `appserver` | Сервер, который кластер запускает для базы; вручную он только запечатывает пароль |
| `webserver` | Отдаёт приложение браузеру |
| `coderunner` | Пишет и выполняет скрипт отдельно, без базы и без метаданных |
| `_fb/` | Движок Firebird. Держите его рядом с программами |
| `lang/` | Переводы интерфейса |

## Запуск без `launcher`

`appstudio` и `appworkspace` принимают одни и те же параметры:

| Параметр | Значение |
|---|---|
| `--file=<folder>` | Файловая база: папка, в которой она лежит |
| `--address=oes://<host>[:port]/<base>` | База, которую обслуживает сервер приложений (`oess://` — через TLS) |
| `--address=https://<host>[:port]/<base>/` | База, которую публикует веб-сервер, по её адресу, как его показывает браузер |
| `--trusted=<file.pem>` | Для `oess://`: удостоверяющие центры, которым доверять вместо системного хранилища |
| `--server=<host>` `--dbport=<port>` `--db=<name>` | База PostgreSQL |
| `--user=<name>` `--password=<secret>` | Логин СУБД (PostgreSQL) |
| `--ibuser=<name>` `--ibpwd=<secret>` | Пользователь самой базы |
| `--locale=en\|ru\|uk` | Язык интерфейса |

`appworkspace --debug` позволяет отладчику Application Studio подключиться к работающему Application Workspace.

```bash
appworkspace --file=C:\bases\trade --ibuser=Admin --locale=en
```

## Дальше

[Первые шаги](../first-steps/): создать базу и собрать в ней небольшое приложение.
