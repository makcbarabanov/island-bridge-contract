# DEPLOYMENT_SOP

Источник: CRONOS (Senior SRE). Регламент обязателен для Forge и Prodagent.

## Инцидент

Зафиксированы хаотичные попытки перезапуска устаревшими методами (`uvicorn` / `systemctl`).
С этого момента действует строгий регламент деплоя без самодеятельности.

## Forge (Песочница)

- Поддерживает этот регламент в актуальном состоянии в SSOT-репозитории `island-bridge-contract`.

## Продагент (Сервер `188.225.44.48`)

Единственный инструмент управления — Docker.

- Запрещено использовать `uvicorn` напрямую.
- Запрещено использовать `systemctl restart` для деплоя приложения.

## Алгоритм деплоя (строго по шагам)

1. Перейти в каталог:
   - `/home/makc/Apps/island`
2. Подтянуть код:
   - `git pull --ff-only origin main`
3. Сделать бэкап БД (основной + fallback):
   - основной: `bash db_backups/bu_db.sh`
   - если основной скрипт завершился ошибкой, выполнить fallback:
     - `set -a; source .env; set +a`
     - `TS=$(date +%Y-%m-%d_%H-%M-%S)`
     - `mkdir -p db_backups`
     - `PGPASSWORD="$DB_PASSWORD" pg_dump -h "$DB_HOST" -p "${DB_PORT:-5432}" -U "$DB_USER" -d "$DB_NAME" -F c -f "db_backups/pre_migrate_${TS}.dump"`
4. Перезапуск только через Docker Compose:
   - `docker compose down`
   - `docker compose up -d --build`
5. Применение миграций внутри контейнера — только по согласованному списку релиза:
   - wildcard по всей папке `_sql/*.sql` запрещён.
   - пример:
     - `RELEASE_MIGRATIONS="_sql/mig_file_a.sql _sql/mig_file_b.sql"`
     - `for f in $RELEASE_MIGRATIONS; do docker compose exec app python3 run_migrate.py "$f"; done`
6. Проверка:
   - `docker ps`
   - `docker compose logs --tail=50`
   - краткий отчёт по результату (что применили, что пропустили, статус сервиса).

## Политика БД

- Миграции строго additive: только добавляют/расширяют, ничего не удаляют.
- Миграции применяются только из явного списка релиза, согласованного перед деплоем.

## Критичность нарушений

- Любой рестарт мимо `docker compose` расценивается как критическое нарушение инфраструктуры.

## Подтверждение получения

- Формат ответа: `HTTP 200: DEPLOYMENT SOP УСВОЕН.`

