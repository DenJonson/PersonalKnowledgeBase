# Инструкция по установке и запуску Docker Engine на Linux

## 1. Назначение инструкции

Настоящая инструкция описывает установку, запуск и первичную проверку Docker Engine на Linux.

Docker Engine подходит для:

- домашнего сервера;
- Raspberry Pi;
- VPS;
- headless Linux-системы;
- серверов без графического интерфейса.

Инструкция ориентирована на установку именно Docker Engine, а не Docker Desktop.

---

## 2. Что будет установлено

Обычно устанавливаются следующие компоненты:

```text
docker-ce                 Docker Engine
docker-ce-cli             консольная команда docker
containerd.io             container runtime
docker-buildx-plugin      сборка образов через BuildKit / buildx
docker-compose-plugin     современный Docker Compose
```

После установки будут доступны команды:

```bash
docker
docker compose
```

---

## 3. Предварительная проверка системы

Перед установкой нужно определить дистрибутив Linux и архитектуру процессора.

Проверить дистрибутив:

```bash
cat /etc/os-release
```

Проверить архитектуру:

```bash
uname -m
```

Возможные варианты архитектуры:

```text
x86_64     обычный ПК / сервер amd64
aarch64    64-битный ARM, например Raspberry Pi OS 64-bit
armv7l     32-битный ARM
```

Для Raspberry Pi 4 и новее желательно использовать 64-битную систему, если нет специальных причин оставаться на 32-битной.

---

## 4. Удаление старых или конфликтующих пакетов

Перед установкой официального Docker Engine желательно удалить старые или конфликтующие пакеты Docker из репозиториев дистрибутива.

Для Debian, Ubuntu и Raspberry Pi OS:

```bash
sudo apt-get remove -y \
  docker.io \
  docker-doc \
  docker-compose \
  docker-compose-v2 \
  podman-docker \
  containerd \
  runc
```

Если часть пакетов не установлена — это нормально.

Каталоги данных Docker:

```text
/var/lib/docker
/var/lib/containerd
```

Удалять эти каталоги не нужно, если нет задачи полностью стереть старые контейнеры, образы и volume.

---

## 5. Установка Docker Engine на Debian / Raspberry Pi OS 64-bit

Этот раздел подходит для:

- Debian;
- Raspberry Pi OS 64-bit;
- других систем, основанных на Debian, если они совместимы с официальным репозиторием Docker.

### 5.1. Обновить индекс пакетов

```bash
sudo apt-get update
```

### 5.2. Установить зависимости

```bash
sudo apt-get install -y ca-certificates curl
```

### 5.3. Создать каталог для ключей APT

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

### 5.4. Добавить GPG-ключ Docker

```bash
sudo curl -fsSL https://download.docker.com/linux/debian/gpg \
  -o /etc/apt/keyrings/docker.asc
```

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### 5.5. Добавить репозиторий Docker

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 5.6. Обновить индекс пакетов

```bash
sudo apt-get update
```

### 5.7. Установить Docker Engine

```bash
sudo apt-get install -y \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin
```

---

## 6. Установка Docker Engine на Ubuntu

### 6.1. Обновить индекс пакетов

```bash
sudo apt-get update
```

### 6.2. Установить зависимости

```bash
sudo apt-get install -y ca-certificates curl
```

### 6.3. Создать каталог для ключей APT

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

### 6.4. Добавить GPG-ключ Docker

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
```

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### 6.5. Добавить репозиторий Docker

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 6.6. Обновить индекс пакетов

```bash
sudo apt-get update
```

### 6.7. Установить Docker Engine

```bash
sudo apt-get install -y \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin
```

---

## 7. Запуск Docker Engine

Проверить состояние службы:

```bash
systemctl status docker
```

Запустить Docker:

```bash
sudo systemctl start docker
```

Остановить Docker:

```bash
sudo systemctl stop docker
```

Перезапустить Docker:

```bash
sudo systemctl restart docker
```

Включить автозапуск Docker при старте системы:

```bash
sudo systemctl enable docker
```

Включить автозапуск и сразу запустить Docker:

```bash
sudo systemctl enable --now docker
```

---

## 8. Проверка установки

Проверить версию Docker:

```bash
docker --version
```

Проверить Docker Compose:

```bash
docker compose version
```

Запустить тестовый контейнер:

```bash
sudo docker run hello-world
```

Ожидаемый результат:

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

Проверить список контейнеров:

```bash
sudo docker ps -a
```

Проверить список образов:

```bash
sudo docker images
```

---

## 9. Использование Docker без sudo

По умолчанию обычный пользователь часто не имеет прав на доступ к Docker daemon. Поэтому команды приходится выполнять через `sudo`.

Пример:

```bash
sudo docker ps
```

Чтобы разрешить текущему пользователю запускать Docker без `sudo`, нужно добавить его в группу `docker`.

```bash
sudo usermod -aG docker $USER
```

После этого нужно выйти из текущей сессии и зайти снова.

Если подключение выполнено по SSH:

```bash
exit
```

Затем нужно подключиться заново.

Также можно временно применить новую группу в текущей сессии:

```bash
newgrp docker
```

Проверить группы пользователя:

```bash
groups
```

В списке должна быть группа:

```text
docker
```

Проверить Docker без `sudo`:

```bash
docker ps
```

Запустить тестовый контейнер без `sudo`:

```bash
docker run hello-world
```

> Важно: группа `docker` даёт пользователю очень широкие права на системе. Для личного домашнего сервера это обычно приемлемо, но на многопользовательском сервере нужно использовать осторожно.

---

## 10. Базовые команды Docker

### 10.1. Контейнеры

Показать запущенные контейнеры:

```bash
docker ps
```

Показать все контейнеры, включая остановленные:

```bash
docker ps -a
```

Остановить контейнер:

```bash
docker stop имя_или_id_контейнера
```

Запустить остановленный контейнер:

```bash
docker start имя_или_id_контейнера
```

Удалить контейнер:

```bash
docker rm имя_или_id_контейнера
```

Принудительно удалить контейнер:

```bash
docker rm -f имя_или_id_контейнера
```

### 10.2. Образы

Показать образы:

```bash
docker images
```

Скачать образ:

```bash
docker pull nginx
```

Удалить образ:

```bash
docker rmi имя_образа
```

### 10.3. Логи

Показать логи контейнера:

```bash
docker logs имя_контейнера
```

Смотреть логи в реальном времени:

```bash
docker logs -f имя_контейнера
```

Показать последние 100 строк:

```bash
docker logs --tail=100 имя_контейнера
```

### 10.4. Выполнение команды внутри контейнера

Зайти в контейнер через `sh`:

```bash
docker exec -it имя_контейнера sh
```

Если внутри контейнера есть `bash`:

```bash
docker exec -it имя_контейнера bash
```

---

## 11. Первый реальный запуск контейнера: Nginx

Запустить тестовый веб-сервер:

```bash
docker run -d \
  --name test-nginx \
  -p 8080:80 \
  nginx
```

Значение параметров:

| Параметр | Назначение |
|---|---|
| `docker run` | создать и запустить контейнер |
| `-d` | запустить в фоне |
| `--name test-nginx` | задать имя контейнера |
| `-p 8080:80` | пробросить порт `8080` хоста на порт `80` контейнера |
| `nginx` | использовать образ `nginx` |

Проверить контейнер:

```bash
docker ps
```

Открыть в браузере:

```text
http://IP_СЕРВЕРА:8080
```

Например:

```text
http://192.168.0.102:8080
```

Или проверить через `curl`:

```bash
curl http://localhost:8080
```

Остановить контейнер:

```bash
docker stop test-nginx
```

Удалить контейнер:

```bash
docker rm test-nginx
```

Удалить образ, если он больше не нужен:

```bash
docker rmi nginx
```

---

## 12. Docker Compose

Современный Docker Compose вызывается так:

```bash
docker compose
```

Старый вариант:

```bash
docker-compose
```

использовался раньше как отдельная утилита. В новых установках обычно применяется Compose Plugin, который вызывается через `docker compose`.

Проверить версию:

```bash
docker compose version
```

---

## 13. Минимальный пример compose.yml

Создать каталог:

```bash
mkdir -p ~/docker-test/nginx
cd ~/docker-test/nginx
```

Создать файл:

```bash
nano compose.yml
```

Содержимое файла:

```yaml
services:
  nginx:
    image: nginx
    container_name: test-nginx
    ports:
      - "8080:80"
    restart: unless-stopped
```

Запустить:

```bash
docker compose up -d
```

Проверить:

```bash
docker compose ps
```

Посмотреть логи:

```bash
docker compose logs -f
```

Остановить и удалить контейнеры проекта:

```bash
docker compose down
```

---

## 14. Где Docker хранит данные

Основной каталог Docker:

```text
/var/lib/docker
```

Там хранятся:

- образы;
- контейнеры;
- volume;
- сети;
- слои файловых систем;
- служебные данные.

Проверить размер каталога:

```bash
sudo du -sh /var/lib/docker
```

Показать использование места Docker:

```bash
docker system df
```

Подробно:

```bash
docker system df -v
```

---

## 15. Volume и постоянные данные

Контейнер лучше считать временным. Постоянные данные нужно хранить в volume или bind mount.

### 15.1. Docker volume

Создать Docker volume:

```bash
docker volume create mydata
```

Посмотреть список volume:

```bash
docker volume ls
```

Посмотреть информацию о volume:

```bash
docker volume inspect mydata
```

### 15.2. Bind mount

Пример запуска контейнера с подключением каталога хоста:

```bash
docker run -d \
  --name test-nginx \
  -p 8080:80 \
  -v ~/nginx-html:/usr/share/nginx/html:ro \
  nginx
```

Здесь каталог хоста:

```text
~/nginx-html
```

монтируется внутрь контейнера:

```text
/usr/share/nginx/html
```

Флаг `:ro` означает read-only.

---

## 16. Рекомендуемая структура сервисов на домашнем сервере

Пример:

```text
/home/alex/services/
├── wikijs/
│   ├── compose.yml
│   ├── .env
│   └── data/
├── gitea/
│   ├── compose.yml
│   └── data/
└── home-assistant/
    ├── compose.yml
    └── config/
```

Создать базовый каталог:

```bash
mkdir -p ~/services
```

Каждый сервис лучше хранить в отдельном каталоге:

```text
~/services/wikijs
~/services/gitea
~/services/home-assistant
```

---

## 17. Автозапуск контейнеров

Docker daemon может запускаться при старте системы, но для контейнеров нужно задать restart policy.

Пример через `docker run`:

```bash
docker run -d \
  --name test-nginx \
  --restart unless-stopped \
  -p 8080:80 \
  nginx
```

Пример в `compose.yml`:

```yaml
services:
  nginx:
    image: nginx
    container_name: test-nginx
    ports:
      - "8080:80"
    restart: unless-stopped
```

Основные варианты restart policy:

| Значение | Назначение |
|---|---|
| `no` | не перезапускать |
| `always` | всегда перезапускать |
| `unless-stopped` | перезапускать, кроме случая ручной остановки |
| `on-failure` | перезапускать только после ошибки |

Для домашних сервисов обычно удобно:

```yaml
restart: unless-stopped
```

---

## 18. Обновление Docker Engine

Для Debian, Ubuntu и Raspberry Pi OS:

```bash
sudo apt-get update
sudo apt-get upgrade
```

Или обновить только Docker-пакеты:

```bash
sudo apt-get install --only-upgrade \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin
```

Проверить после обновления:

```bash
docker version
docker compose version
systemctl status docker
```

---

## 19. Удаление Docker Engine

Удалить пакеты:

```bash
sudo apt-get purge -y \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin \
  docker-ce-rootless-extras
```

Если нужно удалить также все образы, контейнеры и volume:

```bash
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

> Осторожно: эти команды удалят данные Docker.

---

## 20. Диагностика проблем

### 20.1. Docker не запускается

Проверить статус:

```bash
systemctl status docker
```

Посмотреть логи:

```bash
journalctl -u docker --no-pager -n 100
```

### 20.2. Команда docker требует sudo

Проверить группы:

```bash
groups
```

Если нет группы `docker`, добавить пользователя:

```bash
sudo usermod -aG docker $USER
```

После этого выйти из сессии и зайти снова.

### 20.3. Permission denied при обращении к Docker

Типичная ошибка:

```text
permission denied while trying to connect to the Docker daemon socket
```

Проверить сокет:

```bash
ls -l /var/run/docker.sock
```

Обычно должно быть примерно так:

```text
srw-rw---- 1 root docker ... /var/run/docker.sock
```

Если пользователь не входит в группу `docker`, он не сможет обращаться к сокету без `sudo`.

### 20.4. Docker установлен, но daemon не запущен

Проверить:

```bash
docker ps
```

Если ошибка вида:

```text
Cannot connect to the Docker daemon
```

Запустить Docker:

```bash
sudo systemctl start docker
```

Включить автозапуск:

```bash
sudo systemctl enable docker
```

### 20.5. Не работает docker compose

Проверить:

```bash
docker compose version
```

Если команда не найдена:

```bash
sudo apt-get update
sudo apt-get install -y docker-compose-plugin
```

### 20.6. Контейнер запущен, но сервис не открывается

Проверить контейнеры:

```bash
docker ps
```

Проверить порты контейнера:

```bash
docker port имя_контейнера
```

Проверить логи:

```bash
docker logs имя_контейнера
```

Проверить, слушает ли порт на хосте:

```bash
ss -tulpn | grep 8080
```

Проверить firewall:

```bash
sudo ufw status
```

Если используется UFW, открыть порт:

```bash
sudo ufw allow 8080/tcp
```

---

## 21. Минимальный чек-лист после установки

```text
[ ] Docker установлен из официального репозитория Docker
[ ] docker --version работает
[ ] docker compose version работает
[ ] systemctl status docker показывает active/running
[ ] docker run hello-world работает
[ ] пользователь добавлен в группу docker, если это нужно
[ ] контейнеры запускаются с restart: unless-stopped
[ ] данные сервисов вынесены в volume или bind mount
[ ] есть понятная структура каталогов для compose-проектов
[ ] Docker не открыт наружу TCP-сокетом без защиты
```

---

## 22. Короткая версия команд для Debian / Raspberry Pi OS 64-bit

```bash
sudo apt-get remove -y \
  docker.io docker-doc docker-compose docker-compose-v2 \
  podman-docker containerd runc

sudo apt-get update
sudo apt-get install -y ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/debian/gpg \
  -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install -y \
  docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin

sudo systemctl enable --now docker

sudo docker run hello-world

sudo usermod -aG docker $USER
```

После последней команды нужно выйти из SSH/терминала и зайти снова.

Проверка после повторного входа:

```bash
docker ps
docker compose version
docker run hello-world
```

---

## 23. Короткая версия команд для Ubuntu

```bash
sudo apt-get remove -y \
  docker.io docker-doc docker-compose docker-compose-v2 \
  podman-docker containerd runc

sudo apt-get update
sudo apt-get install -y ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install -y \
  docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin

sudo systemctl enable --now docker

sudo docker run hello-world

sudo usermod -aG docker $USER
```

После последней команды нужно выйти из SSH/терминала и зайти снова.

Проверка после повторного входа:

```bash
docker ps
docker compose version
docker run hello-world
```