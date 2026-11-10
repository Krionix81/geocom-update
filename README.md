# Обновления GeocomDecoder

Публичный репозиторий обновлений: <https://github.com/Krionix81/geocom-update>.

Каталог Advanced Installer Updater: <https://krionix81.github.io/geocom-update/updates.json>.

Исходники самого приложения здесь не хранятся. Здесь находятся генератор каталога, его тесты и workflow GitHub Pages. Копия этих файлов поддерживается в основном проекте Geocom в `installer/ai-updates/`.

## Публикация версии

1. Соберите новый x64-установщик в основном проекте командой `pwsh -File installer/Build-Installer.ps1`.
2. Создайте draft release с числовым тегом, равным `ProductVersion` из AIP, например `0.2.6` (без `v`). Новая версия должна быть выше предыдущей.
3. Прикрепите ровно один установщик EXE/MSI и сгенерированный для него файл `advinst_update.json`.
4. Заполните описание и опубликуйте стабильный release. Draft и prerelease пользователям не предлагаются.
5. Дождитесь успешного workflow **Build and publish updater feed**. Он обновляет каталог при изменении релизов, каждый час и по ручному запуску.

Пример описания:

```markdown
Обновление GeocomDecoder.

## Features
- Добавлена новая возможность.

## Enhancements
- Улучшена работа с данными.

## Bug Fixes
- Исправлена ошибка обработки памяти.
```

Заголовки `Features`, `Enhancements`, `Bug Fixes` должны совпадать буквально. Русский текст сохраняется через ASCII JSON с Unicode-экранированием; переносы строк нормализуются в CRLF. Не используйте таблицы в release notes.

`advinst_update.json` должен содержать `RegistryKey: HKLM\Software\Geocom\GeocomDecoder\Version`, `Version`, `Flags: Sys64`, `ServerFileName` и `SHA256`. Скрипт сборки установщика заполняет эти поля автоматически. Версия в JSON и тег релиза должны совпадать. Не добавляйте в один релиз несколько установщиков: GitHub integration выбирает первый подходящий файл.

Пустой репозиторий публикует `[]`: обновлений пока нет. Ошибки API, незавершённые загрузки, неоднозначные установщики и отсутствие `advinst_update.json` останавливают генерацию каталога.

## Первичная настройка GitHub Pages

В **Settings → Pages** источник должен быть **GitHub Actions**. Переменная Actions `UPDATES_PAGES_ENABLED` должна иметь значение `true`. Для ручного запуска workflow включите параметр `publish`.

## Локальная проверка

```powershell
python -m unittest discover -s tests -v
python scripts/build_updates.py --repository Krionix81/geocom-update --output-dir _site
```

Стенд `tools/test_updater_lab.ps1` проверяет сохранение русских строк в реальном updater через локальный сервер метаданных, не устанавливая приложение.

Формат интеграции: [Advanced Installer GitHub Updater](https://www.advancedinstaller.com/user-guide/qa-github-updater-integration.html).
