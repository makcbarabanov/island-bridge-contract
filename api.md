# HTTP API (Остров ↔ Bloom)

**Базовый URL (прод):** `https://islanddream.ru` — маршруты на корне приложения (без `/api`), пока nginx не изменён.

## Общие правила

- Многие ручки принимают `user_id` в query; на стороне продакшена нужна защита сети и/или `X-Api-Key` (nginx).
- Поменял поведение в `main.py` — обнови этот файл и примеры в том же PR или следующим коммитом.
- **Даты в query и JSON-body:** канон для API — строка **`YYYY-MM-DD`** (без времени суток). Бот и любые клиенты интеграции опираются на этот вид.
- **Веб-ЛК Острова:** ввод дат пользователем — отображение и набор в формате **`дд.мм.гг`** и общий календарь-попап; перевод в **`YYYY-MM-DD`** перед вызовами API — на стороне фронта (`index.html`). Описание UI: репозиторий Острова, `Readme/UI-standards.md`, раздел «Календарь даты». *Автор: Форж (синхронизация с ЛК, 2026-04-10).*

## Расписание

### `GET /schedule`

**Query:** `user_id`, `date_from`, `date_to` (формат даты `YYYY-MM-DD`).

**Ответ:** объект с полем `items` (массив). У каждого элемента поля:

- `dream_id`, `source_type` (`step` | `book`), `source_id`, `title`, `date`, `completed`

`date` — строка `YYYY-MM-DD` (при необходимости клиент: первые 10 символов или `split('T')[0]`).

## Шаг мечты

### `PATCH /dreams/{dream_id}/steps/{step_id}?user_id=…`

**Body (примеры):** `{"completed": true|false}`, `{"deleted": true}`, `{"deadline": "YYYY-MM-DD"}`.

## Книга (чтение)

### `POST /dreams/{dream_id}/books/{book_id}/log?user_id=…`

**Body:** минимум `date`, `minutes_spent`. Лог — upsert по `(book_id, date)`.

### `DELETE /dreams/{dream_id}/books/{book_id}/log?user_id=…&date=YYYY-MM-DD`

Снять отметку за день.

## Привязка Telegram (планируется)

`POST …` — зафиксировать в этом файле, когда появится в `main.py`: URL, body, ответ с `user_id`.

## Коды ошибок

Кратко: `404` — нет сущности / нет прав; `5xx` — retry с backoff. Детали — дополнять по факту ответов API.
