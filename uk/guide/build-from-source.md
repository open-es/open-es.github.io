---
layout: guide
lang: uk
ref: build-from-source
title: Збирання з вихідного коду
description: Зберіть OES самостійно на Windows, Linux або macOS.
section: start
order: 3
permalink: /uk/guide/build-from-source/
---

CMake збирає всі платформи; його використовують CI і нічні пакети. На Windows є також
рішення Visual Studio, `enterprise.sln`, для роботи в IDE. Подробиці, параметри та CI —
у [BUILD.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/BUILD.md).

## Що потрібно

| Платформа | Що встановити |
|---|---|
| Windows | Visual Studio 2022 (набір інструментів `v143`) з робочим навантаженням **Desktop development with C++**, що містить CMake і Ninja; Git |
| Linux (Ubuntu 22.04 / Debian 12) | `sudo apt install build-essential cmake ninja-build pkg-config git libgtk-3-dev uuid-dev` |
| macOS | Xcode Command Line Tools (`xcode-select --install`), потім `brew install cmake ninja` |

На Linux обов'язкові `libgtk-3-dev` і `uuid-dev`. Системний wxWidgets не потрібен:
збирання компілює власну копію з підмодуля. Пакети для розробки Firebird чи PostgreSQL
теж не потрібні, бо драйвери завантажують свої клієнтські бібліотеки під час виконання.

## Отримайте вихідний код

```bash
git clone https://github.com/open-enterprise-solutions/enterprise.git
cd enterprise
git checkout develop
git submodule update --init --recursive
```

- **Робота ведеться в `develop`**; з нього збирається нічна збірка. `master` відстає від нього.
- **`--recursive` обов'язковий**: wxWidgets має власні підмодулі.
- **`docs/private` лишається порожнім**, якщо ви не учасник організації. Збиранню він
  не потрібен, і команда вище його пропускає.

## Збирання за допомогою CMake

Виберіть пресет для своєї платформи: `windows-x64-*`, `windows-x86-*`, `linux-*` або `macos-*`,
кожен із `-debug` або `-release`.

```bash
cmake --preset linux-release -DOES_USE_POSTGRESQL=ON
cmake --build --preset linux-release
```

- Програми потрапляють у `build/<preset>/bin/<Debug|Release>/`.
- **Перше збирання компілює ще й wxWidgets**, тому триває значно довше за наступні.
- **Firebird увімкнено в кожному пресеті. PostgreSQL — ні**: додайте `-DOES_USE_POSTGRESQL=ON`, як це
  робить нічна збірка.
- **Windows: виконуйте команди з «x64 Native Tools Command Prompt for VS 2022»**, а не зі
  звичайного `cmd`. Пресети беруть компілятор із середовища, а звичайний «Developer
  Command Prompt» збирає під 32 біти.

Інші параметри: `-DBUILD_TESTING=ON` збирає набори тестів Google Test. Набори збираються лише через CMake;
у рішенні Visual Studio їх немає.

## Збирання у Visual Studio (Windows) {#build-with-visual-studio-windows}

Рішення **не** збирає wxWidgets. Воно підключає готові DLL wxWidgets із
`src\3rdparty\wxWidgets\lib\vc_dll` (x86) або `lib\vc_x64_dll` (x64). Зберіть їх один раз із
підмодуля: відкрийте `src\3rdparty\wxWidgets\build\msw\wx_vc17.sln` і зберіть конфігурацію **DLL Debug**
та/або **DLL Release** для тієї самої платформи, яку використовуватимете.

Потім відкрийте `enterprise.sln`, виберіть `Debug|x86`, `Debug|x64`, `Release|x86` або `Release|x64`
і зберіть. З командного рядка використовуйте Developer Command Prompt:

```cmd
msbuild enterprise.sln /p:Configuration=Debug /p:Platform=x86 /m
msbuild enterprise.sln /p:Configuration=Release /p:Platform=x64 /p:PreferredToolArchitecture=x64 /m
```

Результат потрапляє в `bin\Win32\<Configuration>\` для x86 і `bin\Win64\<Configuration>\` для
x64. Комплект Firebird копіюється в `_fb\` поруч із програмами.

## Запустіть зібране

Запустіть `launcher` з папки результату й виконайте [Перші кроки](../first-steps/).
Запущені з дерева збирання, програми знаходять переклади інтерфейсу в папці
`lang/` репозиторію.
