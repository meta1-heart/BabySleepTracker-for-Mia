# Мия — дневник сна и кормлений

Одностраничное PWA-приложение для двоих. Данные синхронизируются в реальном времени через Firebase Firestore.

## Шаг 1. Firebase (~5 минут)

1. Зайди на https://console.firebase.google.com → **Add project**. Название любое (например `miya-tracker`), Google Analytics можно выключить.
2. В меню слева: **Build → Firestore Database → Create database**. Регион — любой (например `us-central1`), режим — **production**.
3. Вкладка **Rules**, замени содержимое на:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

Нажми **Publish**. ⚠️ Это открытые правила: писать в базу сможет любой, кто узнает конфиг проекта. Для дневника сна это приемлемый компромисс ради простоты; конфиг нигде не публикуйте, кроме самого сайта. Если захочешь закрыть — скажи мне, добавим анонимную авторизацию + правила.

4. **Project settings (шестерёнка) → General → Your apps → Web (</>)**. Зарегистрируй приложение (Hosting не нужен), скопируй объект `firebaseConfig`.
5. Открой `index.html`, найди блок `FIREBASE_CONFIG` в начале `<script>` и вставь свой конфиг вместо заглушки.

## Шаг 2. GitHub Pages

1. Создай репозиторий (можно **приватный** — GitHub Pages из приватного репо доступен на платных планах; на бесплатном нужен публичный. В публичном репо будет виден firebaseConfig — см. предупреждение выше).
2. Загрузи 4 файла: `index.html`, `manifest.json`, `icon-180.png`, `icon-512.png`.
3. **Settings → Pages → Source: Deploy from a branch → main → / (root)** → Save.
4. Через минуту сайт будет на `https://<username>.github.io/<repo>/`.

## Шаг 3. На айфоны

На каждом iPhone: открой сайт в Safari → кнопка «Поделиться» → **На экран "Домой"**. Появится иконка с луной, открывается как обычное приложение.

При первом запуске приложение спросит, кто пользуется этим телефоном (Николай / Виктория) — так подписываются записи.

## Структура данных (Firestore)

- `events` — записи: `{ type: 'sleep'|'wake'|'breast'|'bottle', t: <ms>, ml: <число|null>, author: 'N'|'V' }`
- `todoItems` — пункты ежедневного чек-листа: `{ title, order }`
- `todoChecks/<YYYY-MM-DD>` — отметки чек-листа за день

## Настройки в коде

В начале скрипта в `index.html`:

- `BIRTH` — дата рождения (для возраста в шапке)
- `NIGHT_START`, `NIGHT_END` — граница ночи для аналитики (по умолчанию 20:00–08:00)
