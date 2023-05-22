# Написать скрипт для CRON, который раз в час будет формировать письмо и отправлять на заданную почту.

<details>
     <summary>Скрипт выглядит так:</summary>
     
```
#!/bin/bash

# Путь к access.log
LOG_FILE="/home/nakhorenko/Linux2022-12/otus-bash/access-4560-644067.log"

# Почтовый адрес получателя
EMAIL="vadnakhorenko@gmail.com"

# Проверка наличия мьютекса (файла блокировки)
if [ -f /tmp/script.lock ]; then
  echo "Скрипт уже запущен. Выход."
  exit 1
fi

# Создание файла блокировки
touch /tmp/script.lock

# Временная метка последнего запуска скрипта
LAST_RUN=$(cat /tmp/last_run_timestamp 2>/dev/null)
CURRENT_TIME=$(date +%s)
echo "$CURRENT_TIME" > /tmp/last_run_timestamp

# Формирование письма
MESSAGE="Отчёт об активности сервера за период с $(date -d @$LAST_RUN) до $(date)"

# Список IP адресов с наибольшим количеством запросов
TOP_IPs=$(awk -v last_run="$LAST_RUN" '$4 > last_run {print $1}' "$LOG_FILE" | sort | uniq -c | sort -nr | head)
MESSAGE+="\n\nСписок IP адресов с наибольшим количеством запросов:\n$TOP_IPs"

# Список URL с наибольшим количеством запросов
TOP_URLs=$(awk -v last_run="$LAST_RUN" '$4 > last_run {print $7}' "$LOG_FILE" | sort | uniq -c | sort -nr | head)
MESSAGE+="\n\nСписок URL с наибольшим количеством запросов:\n$TOP_URLs"

# Ошибки сервера/приложения
#ERRORS=$(awk -v last_run="$LAST_RUN" '$4 > last_run && $9 >= 400 {print $9}' "$LOG_FILE" | sort | uniq -c)
#MESSAGE+="\n\nОшибки сервера/приложения:\n$ERRORS"

# Список всех кодов HTTP ответа
HTTP_CODES=$(awk -v last_run="$LAST_RUN" '$4 > last_run {print $9}' "$LOG_FILE" | sort | uniq -c)
MESSAGE+="\n\nСписок всех кодов HTTP ответа:\n$HTTP_CODES"

# Отправка письма
echo -e "$MESSAGE" > log.txt

# Удаление файла блокировки
rm /tmp/script.lock
```
</details>

В cron делаем так:
     
```
1 */1 * * * /home/nakhorenko/Linux2022-12/otus-bash/logscript.sh
```

<details>
     <summary>Вывод выглядит так:</summary>
     
```     
Отчёт об активности сервера за период с Пн 22 мая 2023 11:37:38 CEST до Пн 22 мая 2023 11:37:39 CEST

Список IP адресов с наибольшим количеством запросов:
     45 93.158.167.130
     39 109.236.252.130
     37 212.57.117.19
     33 188.43.241.106
     31 87.250.233.68
     24 62.75.198.172
     22 148.251.223.21
     20 185.6.8.9
     17 217.118.66.161
     16 95.165.18.146

Список URL с наибольшим количеством запросов:
    157 /
    120 /wp-login.php
     57 /xmlrpc.php
     26 /robots.txt
     12 /favicon.ico
     11 400
      9 /wp-includes/js/wp-embed.min.js?ver=5.0.4
      7 /wp-admin/admin-post.php?page=301bulkoptions
      7 /1
      6 /wp-content/uploads/2016/10/robo5.jpg

Список всех кодов HTTP ответа:
     11 "-"
    498 200
     95 301
      1 304
      7 400
      1 403
     51 404
      1 405
      2 499
      3 500
```
     
</details>
