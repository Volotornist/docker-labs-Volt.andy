# Лабораторна робота №2
## Загальні команди роботи з Docker

Студент: Волторніст Андрій Ігорович
Група: 6.1214-пі-2
Варіант: 3

### 1. Перевірка Docker

```powershell
docker --version
```
Результат:
```text
Docker version 29.8.1, build 4a63305
```

```powershell
docker info
```
Результат: Docker Engine працює, використовується контекст desktop-linux та WSL2.

### 2. Запуск вебсервера Caddy

```powershell
docker run -d -p 8080:80 --name andriy-webserver caddy:2.10.2
docker ps
```
Результат: контейнер запущено, порт 8080 перенаправлено на порт 80. У браузері за адресою http://localhost:8080 відкрилася сторінка Caddy.

### 3. Запуск MongoDB

```powershell
docker run -d --name andriy-database mongo:8.0
docker ps
docker logs --tail 30 andriy-database
docker logs andriy-database 2>&1 | Select-String "mongod startup complete|Waiting for connections"
```
Результат: MongoDB запущено. У журналі підтверджено `mongod startup complete` та `Waiting for connections`.

### 4. Робота з Alpine Linux

```powershell
docker run -it --name andriy-os alpine:3.22 /bin/sh
```

У контейнері:
```sh
ls /
cat /etc/os-release
exit
```
Результат: Alpine Linux 3.22.7, переглянуто файлову систему контейнера.

### 5. Фоновий запуск Alpine та docker exec

```powershell
docker run -d --name andriy-os-d alpine:3.22 sleep infinity
docker exec -it andriy-os-d /bin/sh
```

У контейнері:
```sh
ls /
exit
```

Перевірка:
```powershell
docker ps
```
Результат: три контейнери працюють у фоновому режимі.

### 6. Зупинка та видалення контейнерів

```powershell
docker stop andriy-webserver
docker rm andriy-webserver
docker ps -a
docker stop andriy-os-d andriy-database
docker rm andriy-os andriy-os-d andriy-database
docker ps -a
```
Результат: усі створені контейнери видалено, список `docker ps -a` порожній.

### 7. Перегляд і видалення образів

```powershell
docker images
docker rmi alpine:3.22 caddy:2.10.2 mongo:8.0
docker images
```
Результат: образи Alpine, Caddy та MongoDB видалено. Підсумковий список `docker images` порожній.

## Висновок

Під час лабораторної роботи опрацьовано основні команди Docker CLI: запуск контейнерів, перегляд стану, роботу з журналами, виконання команд усередині контейнерів, зупинку та видалення контейнерів і образів.
