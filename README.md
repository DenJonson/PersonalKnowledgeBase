# PerWiki

Docker Compose проект для запуска собственной базы знаний Wiki.js.

## Требования

- Docker Engine.
- Docker Compose plugin.


## Настройка

Скопируйте пример файла окружения и поменяйте пароль базы данных:

```sh
cp .env.example .env
nano .env
```

Запустите Wiki.js:

```sh
docker compose up -d
```

Откройте web-интерфейс:

```text
http://<host-ip>:8080
```

Если браузер открыт на той же машине, где запущен стек, используйте:

```text
http://localhost:8080
```

Первый запуск может занять немного времени: PostgreSQL должен инициализироваться до подключения Wiki.js.

## Полезные команды

Проверить итоговую конфигурацию Compose:

```sh
docker compose config
```

Показать запущенные контейнеры:

```sh
docker compose ps
```

Посмотреть логи Wiki.js:

```sh
docker compose logs wiki
```

Остановить стек:

```sh
docker compose down
```

Обновить образы и перезапустить:

```sh
docker compose pull
docker compose up -d
```

## Данные

Данные PostgreSQL хранятся в именованном Docker volume `perwiki_postgres_data`. Они сохраняются при перезапуске контейнеров и обычном выполнении `docker compose down`.

Не коммитьте файл `.env`: в нем хранится пароль базы данных.
