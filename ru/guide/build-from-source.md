---
layout: guide
lang: ru
ref: build-from-source
title: Сборка из исходников
description: Собрать OpenES самостоятельно на Windows, Linux или macOS.
section: start
order: 3
permalink: /ru/guide/build-from-source/
---

CMake собирает все платформы; его используют CI и ночные пакеты. На Windows есть также
решение Visual Studio, `enterprise.sln`, для работы в IDE. Подробности, параметры и CI —
в [BUILD.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/BUILD.md).

## Что нужно

| Платформа | Установить |
|---|---|
| Windows | Visual Studio 2022 (набор инструментов `v143`) с рабочей нагрузкой **Разработка классических приложений на C++**, в которую входят CMake и Ninja; Git |
| Linux (Ubuntu 22.04 / Debian 12) | `sudo apt install build-essential cmake ninja-build pkg-config git libgtk-3-dev uuid-dev` |
| macOS | Xcode Command Line Tools (`xcode-select --install`), затем `brew install cmake ninja` |

На Linux обязательны `libgtk-3-dev` и `uuid-dev`. Системный wxWidgets не нужен:
сборка компилирует собственную копию из подмодуля. Пакеты разработки Firebird и PostgreSQL
тоже не нужны: драйверы загружают клиентские библиотеки во время выполнения.

## Получить исходники

```bash
git clone https://github.com/open-enterprise-solutions/enterprise.git
cd enterprise
git checkout develop
git submodule update --init --recursive
```

- **Работа идёт в `develop`**; ночная сборка собирается из этой ветки. `master` от неё отстаёт.
- **`--recursive` обязателен**: у wxWidgets есть собственные подмодули.
- **`docs/private` остаётся пустой**, если вы не состоите в организации. Сборке эта папка
  не нужна, и команда выше её пропускает.

## Сборка через CMake

Выберите пресет для своей платформы: `windows-x64-*`, `windows-x86-*`, `linux-*` или `macos-*`,
каждый с `-debug` или `-release`.

```bash
cmake --preset linux-release -DOES_USE_POSTGRESQL=ON
cmake --build --preset linux-release
```

- Программы попадают в `build/<preset>/bin/<Debug|Release>/`.
- **Первая сборка компилирует ещё и wxWidgets**, поэтому занимает гораздо больше времени, чем последующие.
- **Firebird включён во всех пресетах. PostgreSQL — нет**: добавьте `-DOES_USE_POSTGRESQL=ON`, как это
  делает ночная сборка.
- **Windows: выполняйте команды в «x64 Native Tools Command Prompt for VS 2022»**, а не в
  обычном `cmd`. Пресеты берут компилятор из окружения, а обычная «Developer
  Command Prompt» нацелена на 32 бита.

Другие параметры: `-DBUILD_TESTING=ON` собирает наборы тестов Google Test. Тесты собираются только через CMake; в
решении Visual Studio их нет.

## Сборка в Visual Studio (Windows) {#build-with-visual-studio-windows}

Решение **не** собирает wxWidgets. Оно подключает готовые DLL wxWidgets из
`src\3rdparty\wxWidgets\lib\vc_dll` (x86) или `lib\vc_x64_dll` (x64). Соберите их один раз из
подмодуля: откройте `src\3rdparty\wxWidgets\build\msw\wx_vc17.sln` и соберите конфигурацию **DLL Debug**
и/или **DLL Release** для той же платформы, которую будете использовать.

Затем откройте `enterprise.sln`, выберите `Debug|x86`, `Debug|x64`, `Release|x86` или `Release|x64`
и соберите. Из командной строки — через Developer Command Prompt:

```cmd
msbuild enterprise.sln /p:Configuration=Debug /p:Platform=x86 /m
msbuild enterprise.sln /p:Configuration=Release /p:Platform=x64 /p:PreferredToolArchitecture=x64 /m
```

Результат попадает в `bin\Win32\<Configuration>\` для x86 и в `bin\Win64\<Configuration>\` для
x64. Комплект Firebird копируется в `_fb\` рядом с программами.

## Запуск собранного

Запустите `launcher` из выходной папки и следуйте разделу [Первые шаги](../first-steps/).
При запуске из дерева сборки программы находят переводы интерфейса в
папке `lang/` репозитория.
