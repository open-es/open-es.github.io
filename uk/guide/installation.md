---
layout: guide
lang: uk
ref: installation
title: Встановлення
description: Завантажте збірку OES для Windows, Linux або macOS і запустіть її.
section: start
order: 2
permalink: /uk/guide/installation/
---

## Яку збірку взяти

- **Останній випуск** — [`v2.0.0-preview.1`](https://github.com/open-enterprise-solutions/enterprise/releases/latest),
  для Windows, Linux і macOS. Беріть його, щоб спробувати платформу. Його пакети називаються
  `oes-2.0.0-preview.1-…`; кроки нижче ті самі, лише з цією назвою замість `oes-nightly-…`.
- **Нічна збірка**: збирається з `develop` щоночі, коли гілка змінилася. Кожна нова збірка замінює попередню
  на [releases/tag/nightly](https://github.com/open-enterprise-solutions/enterprise/releases/tag/nightly).
  Беріть її заради того, що з'явилося після випуску.
- Випуски до `v1.3.0` включно — це альфа (1.x): їхні програми не працюють із сервером 2.0, а їхні
  бази не переносяться.

Цей посібник описує нічну збірку. У `v2.0.0-preview.1` Application Studio — це `designer-thin`, Application Workspace —
`enterprise-thin`, а програма виконання коду — `codeRunner`; кнопки лаунчера — *Enterprise*, *Web* і *Designer*.

Кожен пакет містить усе, що потрібно програмам: рушій бази даних Firebird,
клієнт PostgreSQL і переклади інтерфейсу. Більше нічого встановлювати не треба.

## Windows 10/11 x64

1. Завантажте `oes-nightly-windows-x64.zip` і розпакуйте його.
2. Запустіть `launcher.exe` з папки `oes`.

Програми не мають цифрового підпису. Якщо SmartScreen їх зупиняє, натисніть **Докладніше → Усе одно запустити**.

## Linux x64

Потрібні Ubuntu 22.04, Debian 12 або новіші та GTK 3 (`libgtk-3-0`). Для бази PostgreSQL також
потрібен `libpq5`.

```bash
tar xzf oes-nightly-linux-x64.tar.gz
cd oes
./launcher
```

## macOS 14+ на Apple Silicon

1. Відкрийте `oes-nightly-macos-arm64.dmg` і перетягніть **всю папку `OES`** у «Програми».
   Застосунки запускають один одного, тому мають лежати разом.
2. Один раз виконайте в «Терміналі»:
   ```bash
   find /Applications/OES -exec xattr -d com.apple.quarantine {} \; 2>/dev/null
   ```
   Збірку підписано ad hoc, а не з Developer ID, тому macOS ставить на карантин кожен її файл.
   Права кнопка → «Відкрити» не допоможе: так знімається карантин з одного застосунку, а лаунчер потім запускає
   інші. macOS 15 цього взагалі більше не пропонує.
3. Відкрийте `launcher` з папки `OES`.

## Що в папці

| Програма | Для чого |
|---|---|
| `launcher` | Веде список баз: додайте базу, потім відкрийте її в Application Studio або Application Workspace |
| `appstudio` | Application Studio: будує застосунок і записує його в базу даних; див. [Application Studio](../studio/) |
| `appworkspace` | Application Workspace: сам застосунок, що працює в базі |
| `appcluster` | Обслуговує бази без вікна, по серверному процесу на базу; див. [Сервер застосунків](../application-server/) |
| `appcluster-tray` | Показує сервери кластера і запускає, зупиняє або перезапускає їх |
| `appserver` | Сервер, який кластер запускає для бази; вручну він лише запечатує пароль |
| `webserver` | Віддає застосунок у браузер |
| `coderunner` | Написання й виконання скрипту окремо, без бази та метаданих |
| `_fb/` | Рушій Firebird. Тримайте його поруч із програмами |
| `lang/` | Переклади інтерфейсу |

## Запуск без лаунчера

`appstudio` і `appworkspace` приймають однакові параметри:

| Параметр | Значення |
|---|---|
| `--file=<folder>` | Файлова база: папка, у якій вона лежить |
| `--address=oes://<host>[:port]/<base>` | База, яку обслуговує сервер застосунків (`oess://` — через TLS) |
| `--address=https://<host>[:port]/<base>/` | База, яку публікує веб-сервер, за адресою, як її показує браузер |
| `--trusted=<file.pem>` | Для `oess://`: центри сертифікації, яким довіряти замість системного сховища |
| `--server=<host>` `--dbport=<port>` `--db=<name>` | База PostgreSQL |
| `--user=<name>` `--password=<secret>` | Вхід до СУБД (PostgreSQL) |
| `--ibuser=<name>` `--ibpwd=<secret>` | Користувач самої бази |
| `--locale=en\|ru\|uk` | Мова інтерфейсу |

`appworkspace --debug` дає налагоджувачу Application Studio приєднатися до запущеного Application Workspace.

```bash
appworkspace --file=C:\bases\trade --ibuser=Admin --locale=en
```

## Далі

[Перші кроки](../first-steps/): створіть базу й побудуйте в ній невеликий застосунок.
