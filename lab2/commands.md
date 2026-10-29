# Лабораторна робота № 2 — Загальні команди роботи з Docker

**Студент:** Волторніст Андрій Ігорович  
**Група:** 6.1214-пі-2  
**Варіант:** 3  
**Середовище:** Windows PowerShell, Docker Desktop, WSL 2.

> Нижче наведено команди, фактично виконані під час лабораторної роботи, та результати, які вдалося підтвердити за збереженими знімками екрана й звітом. Історія PowerShell зберігає введені команди, але не їхній повний вивід. Тому там, де точний текст виводу не зберігся, наведено опис спостереженого результату, а не вигаданий консольний вивід. Знімки екрана команд і результатів включено до PDF-звіту.

## 1. Перевірка Docker

```powershell
docker --version
```

Фактичний вивід:

```text
Docker version 29.8.1, build 4a63305
```

```powershell
docker info
```

За результатом перевірки: клієнт і сервер Docker працювали, версія 29.8.1, контекст `desktop-linux`, середовище Docker Desktop із WSL 2. Повний текст `docker info` не збережено у вигляді текстового журналу; фрагмент виводу наведено на скриншоті у звіті.

## 2. Запуск вебсервера Caddy

```powershell
docker run -d -p 8080:80 --name andriy-webserver caddy:2.10.2
docker ps
```

Спостережений результат: образ `caddy:2.10.2` завантажено; контейнер `andriy-webserver` перебував у стані `Up`, порт `8080` хостової системи перенаправлено на порт `80` контейнера. У браузері за адресою `http://localhost:8080` відкрилася стандартна сторінка Caddy з повідомленням «Your web server is working». Скриншоти — у звіті.

## 3. Запуск MongoDB і перевірка журналу

```powershell
docker run -d --name andriy-database mongo:8.0
docker ps
docker logs --tail 30 andriy-database
docker logs andriy-database 2>&1 | Select-String "mongod startup complete|Waiting for connections"
```

Спостережений результат: завантажено образ `mongo:8.0`, контейнер `andriy-database` запущено одночасно з Caddy. У журналі MongoDB були повідомлення:

```text
Waiting for connections
mongod startup complete
```

Це ключові фрагменти повідомлень, а не повні JSON-рядки журналу. Скриншот фактичного виводу наведено у звіті.

## 4. Alpine Linux — інтерактивний режим

Команда в PowerShell:

```powershell
docker run -it --name andriy-os alpine:3.22 /bin/sh
```

Команди всередині контейнера:

```sh
ls /
cat /etc/os-release
exit
```

Вивід `ls /` (назви каталогів, відображені у терміналі):

```text
bin dev etc home lib media mnt opt proc root run sbin srv sys tmp usr var
```

Підтверджені поля з `/etc/os-release`:

```text
NAME="Alpine Linux"
ID=alpine
VERSION_ID=3.22.7
PRETTY_NAME="Alpine Linux v3.22"
```

Після `exit` інтерактивний контейнер завершив роботу.

## 5. Alpine Linux — фоновий режим і docker exec

Команди в PowerShell:

```powershell
docker run -d --name andriy-os-d alpine:3.22 sleep infinity
docker exec -it andriy-os-d /bin/sh
```

Команди всередині контейнера:

```sh
ls /
exit
```

Результат: команда `ls /` показала каталоги файлової системи, а після `exit` фоновий контейнер залишився запущеним.

```powershell
docker ps
```

Результат: одночасно у стані `Up` відображалися `andriy-os-d`, `andriy-database` та `andriy-webserver`.

## 6. Зупинка та видалення контейнерів

```powershell
docker stop andriy-webserver
docker rm andriy-webserver
docker ps -a
```

Підтверджений вивід команд `stop` і `rm`:

```text
andriy-webserver
andriy-webserver
```

Після цього `docker ps -a` показував фоновий Alpine, завершений інтерактивний Alpine та MongoDB, але вже не Caddy.

```powershell
docker stop andriy-os-d andriy-database
docker rm andriy-os andriy-os-d andriy-database
docker ps -a
```

Підтверджений вивід команд `stop` і `rm`:

```text
andriy-os-d
andriy-database
andriy-os
andriy-os-d
andriy-database
```

Підсумковий `docker ps -a` показав лише заголовки таблиці — жодного контейнера не залишилося. Скриншот — у звіті.

## 7. Перегляд і видалення образів

```powershell
docker images
```

Результат: у списку були три образи:

```text
alpine:3.22
caddy:2.10.2
mongo:8.0
```

```powershell
docker rmi alpine:3.22 caddy:2.10.2 mongo:8.0
docker images
```

Результат: Docker повідомив про видалення тегів та шарів образів (`Untagged`, `Deleted`). Після очищення `docker images` показав порожній список (лише заголовки). Скриншоти обох перевірок — у звіті.

## Примітка щодо повноти журналу

Команди відновлено з історії PowerShell, а результати — зі скриншотів та PDF-звіту. Повний текстовий вивід усіх команд під час виконання не записувався, тому цей документ не є дослівним термінальним журналом. Команди `curl -i` та `docker inspect` у збереженій історії виконання лабораторної відсутні, тому вони не позначені як виконані.
