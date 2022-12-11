## Задача 1

Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-03-mysql/test_data) и 
восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h` получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из ее вывода версию сервера БД.

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.

В следующих заданиях мы будем продолжать работу с данным контейнером.

## Ответ  
Создаем вольюм и запускаем контейнер:  
```
docker volume create volume_mysql && \
docker container run --name mysql_devops -e MYSQL_ROOT_PASSWORD=sloznyparol -t -i -p 3306:3306 -v volume_mysql:/etc/mysql/ -d mysql:8.0


volume_mysql
Unable to find image 'mysql:8.0' locally
8.0: Pulling from library/mysql
0ed027b72ddc: Pull complete
0296159747f1: Pull complete
3d2f9b664bd3: Pull complete
df6519f81c26: Pull complete
36bb5e56d458: Pull complete
054e8fde88d0: Pull complete
f2b494c50c7f: Pull complete
132bc0d471b8: Pull complete
135ec7033a05: Pull complete
5961f0272472: Pull complete
75b5f7a3d3a4: Pull complete
Digest: sha256:3d7ae561cf6095f6aca8eb7830e1d14734227b1fb4748092f2be2cfbccf7d614
Status: Downloaded newer image for mysql:8.0
c9f0e601ca960c5f66ff97e523c0a65091f8449ee703ef312bc70fc952288d95
```

Проверяем:  
```
root@gitlab-podman2:~# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED              STATUS              PORTS                               NAMES
c9f0e601ca96   mysql:8.0   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:3306->3306/tcp, 33060/tcp   mysql_devops
root@gitlab-podman2:~#
```

Клонирую гит на хостовую машину:  
```
root@gitlab-podman2:~/netology# git clone https://github.com/netology-code/virt-homeworks.git
Cloning into 'virt-homeworks'...
remote: Enumerating objects: 1247, done.
remote: Counting objects: 100% (83/83), done.
remote: Compressing objects: 100% (55/55), done.
remote: Total 1247 (delta 29), reused 61 (delta 20), pack-reused 1164
Receiving objects: 100% (1247/1247), 1.52 MiB | 2.19 MiB/s, done.
Resolving deltas: 100% (490/490), done.
root@gitlab-podman2:~/netology#
```

Копирую дамп БД в контейнер:  
```
root@gitlab-podman2:/var/lib/docker/volumes# docker cp ~/netology/virt-homeworks/06-db-03-mysql/test_data/test_dump.sql mysql_devops:/etc/mysql
```

Подключаюсь к контейреру и проверяю наличие бекапа:  
```
root@gitlab-podman2:/#  docker exec -it mysql_devops bash
bash-4.4# ls -lah /etc/mysql/
total 16K
drwxr-xr-x 3 root root 4.0K Dec 11 15:19 .
drwxr-xr-x 1 root root 4.0K Dec 11 14:11 ..
drwxr-xr-x 2 root root 4.0K Dec  7 02:22 conf.d
-rw-r--r-- 1 root root 2.1K Dec 11 15:00 test_dump.sql
```
Подключаемся к БД MySQL:  
```
bash-4.4# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.31 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

Используя команду \h получаю список управляющих команд:  
```
mysql> \h

For information about MySQL products and services, visit:
   http://www.mysql.com/
For developer information, including the MySQL Reference Manual, visit:
   http://dev.mysql.com/
To buy MySQL Enterprise support, training, or other products, visit:
   https://shop.mysql.com/

List of all MySQL commands:
Note that all text commands must be first on line and end with ';'
?         (\?) Synonym for `help'.
clear     (\c) Clear the current input statement.
connect   (\r) Reconnect to the server. Optional arguments are db and host.
delimiter (\d) Set statement delimiter.
edit      (\e) Edit command with $EDITOR.
ego       (\G) Send command to mysql server, display result vertically.
exit      (\q) Exit mysql. Same as quit.
go        (\g) Send command to mysql server.
help      (\h) Display this help.
nopager   (\n) Disable pager, print to stdout.
notee     (\t) Don't write into outfile.
pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
print     (\p) Print current command.
prompt    (\R) Change your mysql prompt.
quit      (\q) Quit mysql.
rehash    (\#) Rebuild completion hash.
source    (\.) Execute an SQL script file. Takes a file name as an argument.
status    (\s) Get status information from the server.
system    (\!) Execute a system shell command.
tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
use       (\u) Use another database. Takes database name as argument.
charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
warnings  (\W) Show warnings after every statement.
nowarning (\w) Don't show warnings after every statement.
resetconnection(\x) Clean session context.
query_attributes Sets string parameters (name1 value1 name2 value2 ...) for the next query to pick up.
ssl_session_data_print Serializes the current SSL session data to stdout or file

For server side help, type 'help contents'
```

Нашел команду для выдачи статуса БД и привожу в ответе из ее вывода версию сервера БД.  
```
mysql> \s
--------------
mysql  Ver 8.0.31 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          8
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.31 MySQL Community Server - GPL
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    latin1
Conn.  characterset:    latin1
UNIX socket:            /var/run/mysqld/mysqld.sock
Binary data as:         Hexadecimal
Uptime:                 19 min 59 sec

Threads: 2  Questions: 5  Slow queries: 0  Opens: 119  Flush tables: 3  Open tables: 38  Queries per second avg: 0.004
--------------
```

Или:  
```
mysql> status
--------------
mysql  Ver 8.0.31 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          8
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.31 MySQL Community Server - GPL
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    latin1
Conn.  characterset:    latin1
UNIX socket:            /var/run/mysqld/mysqld.sock
Binary data as:         Hexadecimal
Uptime:                 20 min 21 sec

Threads: 2  Questions: 8  Slow queries: 0  Opens: 119  Flush tables: 3  Open tables: 38  Queries per second avg: 0.006
--------------
```

Теперь восстановим БД.  
Создадим базу данных test_db:  
```
mysql> create database test_db;
Query OK, 1 row affected (0.01 sec)
mysql> quit
Bye
```
Откатываем БД из дампа гита:  
```
bash-4.4# mysql -u root -p test_db < test_dump.sql
```
Заходим в MySQL:  
```
Enter password:
bash-4.4# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.31 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
Приведим в ответе количество записей с price > 300.  
```
mysql> connect test_db
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Connection id:    12
Current database: test_db

mysql> show tables;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.01 sec)

mysql> select count(*) from orders where price >300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.01 sec)

mysql> select * from orders where price > 300;
+----+----------------+-------+
| id | title          | price |
+----+----------------+-------+
|  2 | My little pony |   500 |
+----+----------------+-------+
1 row in set (0.00 sec)

mysql>
```
Такой дружелюбной до восстановления эта БД точно не была)  

## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:
- плагин авторизации mysql_native_password
- срок истечения пароля - 180 дней 
- количество попыток авторизации - 3 
- максимальное количество запросов в час - 100
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James"

Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.
    
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю `test` и 
**приведите в ответе к задаче**.

## Ответ  
```
mysql> CREATE USER 'test'@'localhost' IDENTIFIED BY 'test-pass';
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER USER 'test'@'localhost' ATTRIBUTE '{"fname":"James", "lname":"Pretty"}';
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER USER 'test'@'localhost' IDENTIFIED BY 'test-pass' WITH MAX_QUERIES_PER_HOUR 100 PASSWORD EXPIRE INTERVAL 180 DAY FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2;
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT Select ON test_db.orders TO 'test'@'localhost';
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER='test';
+------+-----------+---------------------------------------+
| USER | HOST      | ATTRIBUTE                             |
+------+-----------+---------------------------------------+
| test | localhost | {"fname": "James", "lname": "Pretty"} |
+------+-----------+---------------------------------------+
1 row in set (0.01 sec)
```

## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`
- на `InnoDB`

## Ответ  
Включаем профилирование:  
```
mysql> SET profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)


mysql> SHOW VARIABLES like 'profiling';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| profiling     | ON    |
+---------------+-------+
1 row in set (0.02 sec)
```

Смотрим поисковый движок по-умолчанию:  
```
SHOW VARIABLES like 'default_storage_engine';

mysql> SHOW VARIABLES like 'default_storage_engine';
+------------------------+--------+
| Variable_name          | Value  |
+------------------------+--------+
| default_storage_engine | InnoDB |
+------------------------+--------+
1 row in set (0.01 sec)
```

Коннектимся к нашей БД  
```
mysql> connect test_db
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Connection id:    14
Current database: test_db
```

Делаем Запрос из задания 1:  
```
mysql> select count(*) from orders where price >300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```

Меняем InnoDB на MyISAM:  
```
mysql> alter table orders engine = MyISAM;
Query OK, 5 rows affected (0.05 sec)
Records: 5  Duplicates: 0  Warnings: 0
```

Делаем тот же запос и смотрим результат:  
```
mysql> select count(*) from orders where price >300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

mysql> show profiles;
+----------+------------+----------------------------------------------+
| Query_ID | Duration   | Query                                        |
+----------+------------+----------------------------------------------+
|        1 | 0.00107425 | select count(*) from orders where price >300 |
|        2 | 0.05756375 | alter table orders engine = MyISAM           |
|        3 | 0.00119475 | select count(*) from orders where price >300 |
+----------+------------+----------------------------------------------+
3 rows in set, 1 warning (0.00 sec)
```
