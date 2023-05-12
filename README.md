## Написать скрипт для CRON, который раз в час будет формировать письмо и отправлять на заданную почту.

Получим десяток самых частых адресов с наибольшим кол-вом запросов

```
[root@otusbash ~]# cat access-4560-644067.log | cut -d ' ' -f 1 | uniq -c | sort -nr | head -n 10
     39 109.236.252.130
     36 212.57.117.19
     33 188.43.241.106
     17 217.118.66.161
     17 185.6.8.9
     16 95.165.18.146
     16 148.251.223.21
     12 62.210.252.196
     12 185.142.236.35
     12 162.243.13.195
[root@otusbash ~]# 
```
Список запрашиваемых URL (с наибольшим кол-вом запросов) с указанием кол-ва запросов c момента последнего запуска скриптаё

```
[root@otusbash ~]# cat access-4560-644067.log  | cut -d ' ' -f 11 | sort -n | uniq -c -d | sort -nr
    498 "-"
     73 "https://dbadmins.ru/"
     15 "https://dbadmins.ru/2016/10/26/%D0%B8%D0%B7%D0%BC%D0%B5%D0%BD%D0%B5%D0%BD%D0%B8%D0%B5-%D1%81%D0%B5%D1%82%D0%B5%D0%B2%D1%8B%D1%85-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B5%D0%BA-%D0%B4%D0%BB%D1%8F-oracle-rac/"
     14 "https://dbadmins.ru/2016/10/17/%D0%9F%D1%80%D0%BE%D0%B4%D0%BE%D0%BB%D0%B6%D0%B0%D0%B5%D0%BC-%D1%8D%D0%BA%D1%81%D0%BF%D0%B5%D1%80%D0%B8%D0%BC%D0%B5%D0%BD%D1%82%D1%8B-%D1%81-lacp/"
     11 uct="-"
      4 "https://dbadmins.ru/wp-content/themes/llorix-one-lite/css/font-awesome.min.css?ver=4.4.0"
      4 "http://dbadmins.ru/"
      3 "https://dbadmins.ru/wp-content/themes/llorix-one-lite/style.css?ver=1.0.0"
      2 "https://dbadmins.ru/2016/12/14/virtualenv-%D0%B4%D0%BB%D1%8F-%D0%BF%D0%BB%D0%B0%D0%B3%D0%B8%D0%BD%D0%BE%D0%B2-python-scrappy-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82-%D0%BD%D0%B0-debian-jessie/"
      2 "http://dbadmins.ru/wp-content/plugins/uploadify/readme.txt"
      2 "http://dbadmins.ru/wp-content/plugins/uploadify/includes/check.php"
      2 "http://dbadmins.ru/wp-admin/admin-post.php?page=301bulkoptions"
      2 "http://dbadmins.ru/wp-admin/admin-ajax.php?page=301bulkoptions"
      2 "http://dbadmins.ru/1"
      2 "http://dbadmins.ru"
```
Откуда взять ошибки, если нет error.log - не понятно :)

Список всех кодов HTTP ответа с указанием их кол-ва с момента последнего запуска скрипта.
```
[root@otusbash ~]# cat access-4560-644067.log  | cut -d " " -f 9 | sort -n | uniq -c -d | sort -nr
    498 200
     95 301
     51 404
     11 "-"
      7 400
      3 500
      2 499
```
Собрав всё в скрипт получаем подобный вывод. Доавить в cron задачу 
* * * * * /root/mailscript.sh

```
############################################
####		 Отчет сформирован с 14/Aug/2019:04:12:10 по 14/Aug/2019:05:17:39
############################################



############################################
####		 Топ ip адресов:
############################################


     39 109.236.252.130
     36 212.57.117.19
     33 188.43.241.106
     17 217.118.66.161
     17 185.6.8.9
     16 95.165.18.146
     16 148.251.223.21
     12 62.210.252.196
     12 185.142.236.35
     12 162.243.13.195

############################################
####		 Топ URL
############################################

    498 "-"
     73 "https://dbadmins.ru/"
     15 "https://dbadmins.ru/2016/10/26/%D0%B8%D0%B7%D0%BC%D0%B5%D0%BD%D0%B5%D0%BD%D0%B8%D0%B5-%D1%81%D0%B5%D1%82%D0%B5%D0%B2%D1%8B%D1%85-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B5%D0%BA-%D0%B4%D0%BB%D1%8F-oracle-rac/"
     14 "https://dbadmins.ru/2016/10/17/%D0%9F%D1%80%D0%BE%D0%B4%D0%BE%D0%BB%D0%B6%D0%B0%D0%B5%D0%BC-%D1%8D%D0%BA%D1%81%D0%BF%D0%B5%D1%80%D0%B8%D0%BC%D0%B5%D0%BD%D1%82%D1%8B-%D1%81-lacp/"
     11 uct="-"
      4 "https://dbadmins.ru/wp-content/themes/llorix-one-lite/css/font-awesome.min.css?ver=4.4.0"
      4 "http://dbadmins.ru/"
      3 "https://dbadmins.ru/wp-content/themes/llorix-one-lite/style.css?ver=1.0.0"
      2 "https://dbadmins.ru/2016/12/14/virtualenv-%D0%B4%D0%BB%D1%8F-%D0%BF%D0%BB%D0%B0%D0%B3%D0%B8%D0%BD%D0%BE%D0%B2-python-scrappy-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82-%D0%BD%D0%B0-debian-jessie/"
      2 "http://dbadmins.ru/wp-content/plugins/uploadify/readme.txt"
      2 "http://dbadmins.ru/wp-content/plugins/uploadify/includes/check.php"
      2 "http://dbadmins.ru/wp-admin/admin-post.php?page=301bulkoptions"
      2 "http://dbadmins.ru/wp-admin/admin-ajax.php?page=301bulkoptions"
      2 "http://dbadmins.ru/1"
      2 "http://dbadmins.ru"

############################################
####		 Топ кодов ответа:
############################################

     498 200
     95 301
     51 404
     11 "-"
      7 400
      3 500
      2 499
 ```
