# Agave

Пакетный конвертер звука для macOS. Нативный SwiftUI, универсальный бинарник
(Apple Silicon + Intel). Распространяется напрямую (Developer ID + нотаризация,
не через Mac App Store) — LAME линкуется динамически ради LGPL-2.1, а это
право пользователя заменить библиотеку App Store технически не даёт выполнить.

Перетащил файлы или папку → выбрал формат → нажал «Конвертировать». Папка
экспорта запоминается между запусками, рядом кнопка «Открыть».

---

## Установка

**Вручную.** Скачайте `Agave.dmg` со страницы [Releases](https://github.com/white-currant/Agave/releases/latest), откройте и перетащите Agave в «Программы».

**Через терминал, одной командой** (ничего больше не нужно):

```bash
curl -fsSL -o /tmp/Agave.dmg https://github.com/white-currant/Agave/releases/latest/download/Agave.dmg \
  && hdiutil attach -nobrowse -quiet -mountpoint /tmp/Agave-dmg /tmp/Agave.dmg \
  && rm -rf /Applications/Agave.app && cp -R /tmp/Agave-dmg/Agave.app /Applications/ ; \
hdiutil detach -quiet /tmp/Agave-dmg; rm -f /tmp/Agave.dmg
```

Запуск: `open -a Agave`. Удаление: `rm -rf /Applications/Agave.app`.

**Через витрину [Currant](https://github.com/white-currant/Currant)** — каталог приложений white-currant: скачивание и обновление в один клик.

Обновления приходят автоматически ([Sparkle](https://sparkle-project.org)); вручную — меню «Проверить обновления…».

---

## Форматы

| Формат | Чтение | Запись | Чем реализовано |
|---|---|---|---|
| MP3 | ✅ | ✅ | LAME 3.100 |
| AAC (m4a) | ✅ | ✅ | AudioToolbox |
| Ogg Vorbis | ✅ | ✅ | libogg 1.3.5 + libvorbis 1.3.7 |
| ALAC (m4a) | ✅ | ✅ | AudioToolbox |
| FLAC | ✅ | ✅ | AudioToolbox |
| WAV | ✅ | ✅ | AudioToolbox (16 / 24 / 32-бит float) |
| AIFF | ✅ | ✅ | AudioToolbox (16 / 24 бита) |
| CAF, AC-3, MP2, AMR, звук из видео | ✅ | — | AudioToolbox |

Почему сторонние библиотеки для MP3 и Vorbis: Apple поставляет **декодер** MP3,
но никогда не поставляла кодировщик (изначально из-за патентных отчислений
Fraunhofer). Ogg Vorbis система не знает вовсе — ни на чтение, ни на запись.

Чего нет: Opus, WMA, многоканальный AC-3 на выходе. Это осознанный отказ —
догонять ffmpeg по списку форматов означало бы потерять и вес, и лицензионную
чистоту, ради которых всё затевалось.

## Настройки

- **Битрейт** для форматов с потерями: постоянный 96…320 кбит/с; для MP3 и OGG
  дополнительно переменный (V0 / V2 / V4).
- **Разрядность** для форматов без потерь: 16 / 24 бита, у WAV ещё 32-бит float.
- **Частота** и **каналы**: как в источнике либо принудительно.
  MP3 и AAC выше 48 кГц не кодируются — частота срезается автоматически.
- **Конфликт имён**: добавить номер, перезаписать или пропустить.
- **Теги** переносятся в MP3, OGG, AAC и ALAC. У FLAC не переносятся: системная
  реализация при дописывании тегов ломает STREAMINFO и портит длительность.

## Лицензии и открытые компоненты

Agave — свободное программное обеспечение под лицензией **GNU GPL v3**. Исходный код: [white-currant/Agave-src](https://github.com/white-currant/Agave-src). Приложение использует открытые библиотеки:

- **LAME 3.100** (кодирование MP3) — LGPL-2.1, [lame.sourceforge.io](https://lame.sourceforge.io).
  Подключена динамически, отдельной библиотекой (`LameKit.framework` внутри
  приложения). Текст лицензии: [`licenses/LAME-LICENSE-LGPL-2.1.txt`](licenses/LAME-LICENSE-LGPL-2.1.txt).
- **libogg 1.3.5 и libvorbis 1.3.7** (Ogg Vorbis) — BSD, © Xiph.Org Foundation,
  [xiph.org](https://xiph.org). Текст: [`licenses/VORBIS-LICENSE-BSD.txt`](licenses/VORBIS-LICENSE-BSD.txt).
- **Sparkle** (обновления) — MIT, [sparkle-project.org](https://sparkle-project.org).
