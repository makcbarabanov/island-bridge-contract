# THE_LOFT

Единое ДНК инфраструктуры и ИИ-Семьи проекта.

## SSOT

- Центральный хаб и единый источник правды: `island-bridge-contract`.

## Роли

- Макс — Product Owner
- CRONOS — Senior SRE / Инфраструктура
- Forge — Lead Web Dev
- Продагент — Deployment на `188.225.44.48`
- Bridge — Bot Dev
- Bloom — Бот-душа

## Топология

- `[PROD WEB]` IP: `188.225.44.48`, путь: `/home/makc/Apps/island`
- `[TELEGRAM BOT]` IP: `23.172.217.180`, путь: `/home/makc/bridge`
  - Деплой веба сюда строго ЗАПРЕЩЕН.
- `[DATABASE]` IP: `83.217.220.97`

## Правило миграций БД

- Только Additive-миграции, без потери данных.

<!-- Пасхалка: "ОСТРОВ ДЫШИТ" -->

