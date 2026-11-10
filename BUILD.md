# Сборка

## Android (APK)
`build.yml` — сборка debug APK, работает.

## iOS (IPA)
`build-ipa.yml` — запускается вручную (Actions → Build iOS IPA → Run workflow).

Что делает:
1. `npm install` + `npx cap sync ios` + `pod install` (ios/ проект уже в репозитории)
2. Пытается импортировать сертификат из секретов (`P12_BASE64`, `P12_PASSWORD`, `MOBILEPROVISION_BASE64`, `KEYCHAIN_PASSWORD`)
3. Если подпись не удалась — автоматически собирает **unsigned** IPA
4. Кладёт результат в artifact `app-ipa`

Последний успешный запуск: `42813297642` → artifact `app-ipa` (App-unsigned.ipa, ~834 KB).

### Про подпись
Секрет `P12_PASSWORD` не подходит к файлу в `P12_BASE64`:
`SecKeychainItemImport: MAC verification failed during PKCS12 import (wrong password?)`

Чтобы получить подписанный IPA — перезалейте `P12_BASE64`/`P12_PASSWORD` парой (пароль должен быть тем, который задавали при экспорте .p12 из Keychain). Workflow подхватит автоматически, ничего менять не нужно.

### Про unsigned IPA
Unsigned IPA нельзя просто поставить на iPhone — его нужно подписать (например, через AltStore/Sideloadly своим Apple ID, либо пересобрать с рабочим сертификатом).
