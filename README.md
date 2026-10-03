# BuvnieksR — проект Android-приложения (TWA)

Приложение — оболочка над сайтом https://egbuvr.netlify.app (Trusted Web Activity).
Пакет: app.netlify.egbuvr.twa. Дизайн и функции меняются на сайте, пересобирать APK не нужно.

## Что внутри
- app/build.gradle — все настройки в блоке twaManifest (адрес сайта, название, цвета, версия)
- keystore.properties.example — образец файла с паролями подписи
- .github/workflows/build-apk.yml — сборка APK в облаке GitHub, ничего устанавливать не нужно

## Подпись (обязательно)
Ключ подписи — ваш файл signing.keystore из PWABuilder. Пароль и имя ключа — в signing-key-info.txt.
Подписывайте ТОЙ ЖЕ подписью, иначе отпечаток не совпадёт с assetlinks.json на сайте, а обновления не встанут поверх старой версии.
Ключ и пароли никому не отправляйте и не выкладывайте в открытый репозиторий.

## Способ A. Android Studio
1. Установите Android Studio. Откройте эту папку (File → Open).
2. Положите signing.keystore в корень проекта (рядом с gradlew).
3. Скопируйте keystore.properties.example в keystore.properties и впишите значения из signing-key-info.txt.
4. Дождитесь Gradle Sync.
5. Выберите вариант сборки release (View → Tool Windows → Build Variants), затем Build → Build Bundle(s) / APK(s) → Build APK(s).
6. Файл: app/build/outputs/apk/release/app-release.apk

## Способ B. Командная строка (нужны JDK 17 и Android SDK)
    chmod +x gradlew
    ./gradlew assembleRelease      # APK
    ./gradlew bundleRelease        # AAB для Google Play
Результат: app/build/outputs/apk/release/ и app/build/outputs/bundle/release/

## Способ C. GitHub Actions (без установки программ)
1. Создайте приватный репозиторий на github.com и загрузите в него все файлы этой папки (кроме *.keystore).
2. Settings → Secrets and variables → Actions → New repository secret. Создайте 4 секрета:
   - KEYSTORE_BASE64 — содержимое signing.keystore в base64
     (Linux/Mac: base64 -w0 signing.keystore; Windows PowerShell: [Convert]::ToBase64String([IO.File]::ReadAllBytes("signing.keystore")))
   - KEYSTORE_PASSWORD, KEY_ALIAS, KEY_PASSWORD — из signing-key-info.txt
3. Вкладка Actions → Build APK → Run workflow.
4. Через 5–10 минут откройте завершённый запуск и скачайте архив BuvnieksR-release в разделе Artifacts. Внутри APK и AAB.

## Как изменить приложение
- Новая версия: в app/build.gradle увеличьте versionCode (1 → 2) и versionName.
- Название: name и launcherName в блоке twaManifest.
- Другой сайт: hostName, а также webManifestUrl и fullScopeUrl ниже по файлу. Для нового сайта нужен свой assetlinks.json.
- Иконки: папки app/src/main/res/mipmap-*.
- Если меняете applicationId, в assetlinks.json на сайте должен быть новый package_name.

## Установка APK на телефон
Скопируйте app-release.apk на телефон → откройте → разрешите установку из этого источника → Установить.
Старую версию с другой подписью сначала удалите.
