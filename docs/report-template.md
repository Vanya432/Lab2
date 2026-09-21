# Звіт до лабораторної роботи № TODO

## 1\. Ідентифікація стану

* Варіант: 2-A «Трекер інцидентів».
* Гілка: `lab/1-system`.
* Фінальний тег: `TODO`.
* Commit hash: `TODO`.

## 2\. Змінений маршрут

Опишіть маршрут від дії у браузері до SQL і назад. Наведіть лише ключові
файли та фрагменти, а не повний код.



кнопка Показати підсумок у index.html

&#x20;→ loadSeveritySummary() y app.js

&#x20;→ GET /api/incidents/severity-summary

&#x20;→ IncidentEndpoints.GetSeveritySummaryAsync()

&#x20;→ IncidentQueries.GetSeveritySummaryAsync()

&#x20;→ SecureLabDbContext.Incidents

&#x20;→ PostgreSQL, таблиця incidents

&#x20;→ IncidentSeveritySummaryResponse

&#x20;→ JSON

&#x20;→ textContent y app.js



Ключові файли



index.html - кнопка та список summary

app.js - HTTP-запит і відображення результату

IncidentEndpoints.cs - HTTP endpoint

IncidentQueries.cs - агрегування за severity 

IncidentResponses.cs - DTO IncidentSeveritySummaryResponse

SecureLabDbContext.cs - EF Core контекст



## 3\. Виконані зміни

GET /api/incidents/severity-summary більше не видає статус 501

IncidentSeveritySummaryResponse - Працює як DTO з полями 

IncidentQueries - був додан запит, який групує записи інцидентів за severity і потім підраховує їх кількість

Було реалізовано сортування результату за severity

Для порожньої таблиці була обрана політика існуючих груп

Було додане структуроване логування кількості сформованих груп

Додано кнопку та відображення summary у browser client

Для відображення даних використовується textContent, без вставки HTML



## 4\. Перевірка

|ID|Передумови|Дія|Очікувано|Фактично|Доказ|
|-|-|-|-|-|-|
|T-01|PostgreSQL healthy, API запущено|GET /health|200 OK, API може зєднатися з БД|200 OK, API відповід успішно|http або HTTP-обміну|
|T-02|Відновлений seed|GET /api/incidents?status=Triaged|200 OK, список відповідає фільтру|200 OK, отримано список інцидентів зі статусом Triaged|http відподвідь та dev tools Network|
|T-03|Відновлений seed|GET /api/incidents?status=Resolved|200 OK, порожній масив \[]|200 OK, отримано \[]|http response  порожній сценарій list-flow|
|T-04|Відновлений seed|GET /api/incidents/99999999-99999999-999999-9999999|404 Not Found, Problem Details|404 Not Found, повернуто Problem Details|http response|
|T-05|Відновлений seed|GET /api/incidents?status=Unknown|400 Bad Request, Validation Problem Details|400 Bad Request, повернуто Validation Problem Details|http response, некоректний сценарцій list-flow|
|T-06|Реалізований етап 3, Відновлений seed|GET /api/incidents/severity-summary|200 OK, за політики лише наявні групи High, Medium, Low, по одному елементу з count: 1, без Critical|200 OK, отримано High: 1, Medium: 1, Low: 1|http та інтераційний тест|
|T-07|Реалізовано етап 3, API і клієнт запущено|натиснути Показати підсумок|UI безпечно показує успішний результат, є окремі гілки завантаження, порожнього стану та помилки|UI показав High: 1, Medium: 1, Low: 1|Network + скриншот UI; фрагмент app.js|
|T-08|Після контрольованої зміни даних, якщо її робили|--reset-database, повторити Т-02 і Т-06|Seed повертає стенд до відомого стану|Після перезапуску отримано початкові результати Т-02 і Т-06|Команда й повторний response |

## 5\. Security-сценарій



У межах роботи навмисно вразливий стан не створювався.

## 6\. Висновок

Сформулюйте висновок із фактичного результату перевірки.



Під час лабораторної роботи було успішно реалізовано наскрізний маршрут отримання підсумку інцидентів за severity від дії користувача у браузері через http endpoint i application layer до PostgreSQL та назад у DOM. Endpoint GET /api/incidents/severity-summary тепер успішно повертає 200. Реалізований стан є відтворюваним після повернення бази даних до відомого seed-стану

