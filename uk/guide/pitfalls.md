---
layout: guide
lang: uk
ref: pitfalls
title: Типові проблеми
description: Що йде не так під час запуску, роботи з базою чи збирання — і що робити.
section: running
order: 12
permalink: /uk/guide/pitfalls/
---

Кожен пункт містить симптом, його причину і що робити.

## Запуск програм

**Windows: SmartScreen пише «Windows захистила ваш ПК».**
Програми не мають цифрового підпису. Натисніть **Докладніше → Усе одно запустити**.

**macOS: застосунок «пошкоджено, і його не можна відкрити» або «неможливо перевірити на наявність
зловмисного ПЗ».**
macOS ставить на карантин кожен файл завантаження, підписаного ad hoc. Виконайте один раз:
`find /Applications/OES -exec xattr -d com.apple.quarantine {} \; 2>/dev/null`.
Права кнопка → «Відкрити» знімає карантин лише з одного застосунку.

**macOS: лаунчер відкривається, але Application Studio чи Application Workspace не запускається.**
Один із застосунків перенесено з папки `OES`. Вони запускають один одного як сусіди, тому не
розділяйте папку.

**Linux: `error while loading shared libraries: libgtk-3.so.0`.**
Встановіть GTK 3: `sudo apt install libgtk-3-0`. Для бази PostgreSQL також потрібен `libpq5`.

**Файлова база не відкривається.**
Тримайте папку `_fb` поруч із програмами: у ній рушій Firebird 5, з яким постачаються пакети.
Системний Firebird його не замінить. Ubuntu 22.04 і Debian 12 постачають Firebird 3, який
не може відкрити ці бази.

## Робота з базою

**`appworkspace` відкриває порожній застосунок.**
Застосунок бази порожній. Спершу побудуйте його в Application Studio і натисніть **Оновити базу даних**.

**Сервер застосунків відмовляється від бази, або Application Studio відмовляється від бази, яку обслуговує
сервер.**
Базу використовують або сервери застосунків, або програми, що відкривають її як файлову базу
(`appstudio`, `appworkspace`), але ніколи обидві сторони одночасно. Закрийте одну сторону або звертайтеся до бази
через сервер: `--address=oes://<host>/<base>`.

**Оновлення бази даних чекає або відхиляється.**
Зміна, що перебудовує збережені дані, потребує монопольного доступу до бази. Закрийте `appworkspace` і будь-який
інший сеанс цієї бази, потім оновіть знову.

**Відкрито дві Application Studio, і асистент редагує не ту.**
Одна адреса й порт ведуть до однієї Application Studio: тієї, що почала слухати першою. Призначте кожній
Application Studio власний порт у **Інструменти → Параметри… → Доступ асистента (MCP)**.

## Збирання

**`'cmake'` або `'msbuild'` не розпізнано.**
Використовуйте командний рядок Visual Studio: «x64 Native Tools Command Prompt for VS 2022» для
пресетів CMake або «Developer Command Prompt» для `msbuild`.

**`wx/wx.h: No such file or directory`, або CMake не знаходить wxWidgets.**
Бракує підмодулів. Виконайте `git submodule update --init --recursive`. Повторюйте це після
кожного `git pull`, бо pull може принести новий підмодуль.

**`enterprise.sln`: `LNK1104: cannot open file 'wxbase33u….lib'`.**
Рішення підключає готовий wxWidgets і не збирає його. Спершу зберіть конфігурації DLL у
`src\3rdparty\wxWidgets\build\msw\wx_vc17.sln`; див.
[Збирання з вихідного коду](../build-from-source/#build-with-visual-studio-windows).

**Release x64: `C1002: compiler is out of heap space in pass 2`.**
32-бітному хосту компілятора бракує пам'яті на найбільших файлах. Збирайте з
`/p:PreferredToolArchitecture=x64`.

**`C1060: compiler is out of heap space` у випадкових файлах, або комп'ютер зависає під час
збирання.**
Одночасно працює забагато компіляторів. З CMake поза пресетом використовуйте `--parallel 3` для
16 ГБ RAM або `--parallel 2` для 8 ГБ (на Linux і macOS пресети вже обмежують кількість завдань).
З MSBuild використовуйте `/m:1`.

**`fatal: Unable to find current revision in submodule path 'src/3rdparty/wxWidgets'`.**
Перерване збирання або fetch залишили підмодуль пошкодженим.
`rm -rf src/3rdparty/wxWidgets`, потім `git submodule update --init --recursive`.

**`LNK1168: cannot open … for writing`.**
Програма з папки результату ще працює і тримає свій файл. Закрийте `appstudio`,
`appworkspace`, `launcher`, `appcluster` і його процеси `appserver`, потім зберіть знову.

**Після `git pull` зібрана програма падає під час запуску, або лінкер вимагає функцію, якої вже немає
у вихідному коді.**
Об'єктні файли зі старішого або перерваного збирання змішалися з новими. Перезберіть начисто:
`/t:Rebuild` з MSBuild, `--clean-first` з CMake.

## Якщо не допомогло

Створіть [issue](https://github.com/open-enterprise-solutions/enterprise/issues). Вкажіть
платформу, пакет або коміт, з якого ви почали, і точне повідомлення.
