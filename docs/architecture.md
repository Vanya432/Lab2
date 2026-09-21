# Карта архітектури

Це початкова карта. Під час ЛР 1 доповніть її власним трасуванням запиту,
конкретними файлами та спостереженнями з DevTools і журналу PostgreSQL.

## Компоненти

|Компонент|Розташування|Відповідальність|
|-|-|-|
|Browser client|`src/SecureLab.Api/Client/`|Надсилає HTTP-запити, безпечно показує відповідь через DOM API|
|Presentation|`Presentation/`|Описує endpoints, читає зовнішні параметри, формує HTTP-відповідь|
|Application|`Application/`|Виконує сценарій отримання списку або деталей інциденту|
|Data|`Data/`|Відображає C#-сутності на PostgreSQL через EF Core/Npgsql|
|PostgreSQL|`infra/compose.yaml`|Зберігає навчальні дані у локальному контейнері|

## Підготовлений наскрізний маршрут

```text
кнопка "Показати підсумок" у Client/app.js
  → loadSeveritySummary() y Client/app.js

&#x20; → GET /api/incidents/severity-summary

&#x20; → IncidentEndpoints.GetSeveritySummaryAsync()

&#x20; → IncidentQueries.GetSeveritySummaryAsync()

&#x20; → SecureLabDbContext.Incidents

&#x20; → PostgreSQL, таблиця incidents

&#x20; → IncidentSeveritySummaryResponse

&#x20; → JSON

&#x20; → textContent у Client/app.js```

## Межі довіри

Доповніть таблицю щонайменше трьома конкретними спостереженнями.

|Межа|Чому даним ще не можна довіряти|Де перевіряємо або обмежуємо|
|-|-|-|
|Користувач → Browser client|Користувач контролює введення|Клієнт не повинен вважати отримані дані HTML. Для відображення використовується DOM API|
|Browser client → API|Клієнт і HTTP-запит можна змінити поза UI|API самостійно визначає маршрут і формує відповідь. Клієнт не є довіреним джерелом даних|
|PostgreSQL → API → DOM|У БД може зберігатися раніше введений недовірений текст|DTO та безпечний DOM sink; IncidentSeveritySummaryResponse обмежує контракт відповіді, а app.js використовує textContent для summary|

## Конфігураційні входи

* `global.json` — версія .NET SDK;
* `src/SecureLab.Api/appsettings\*.json` — режим міграцій і локальний connection string;
* `infra/compose.yaml` — версія PostgreSQL, порт і локальні навчальні облікові дані;
* змінна середовища `ConnectionStrings\_\_SecureLab` — безпечний спосіб перевизначити connection string поза репозиторієм.

