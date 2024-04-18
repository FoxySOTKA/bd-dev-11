# Домашнее задание к занятию 3. «MySQL»

## Выполнил Савицкий Андрей

### Задача 1

Используя Docker, поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-03-mysql/test_data) и 
восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h`, получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из её вывода версию сервера БД.

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.

В следующих заданиях мы будем продолжать работу с этим контейнером.

#### Ответ:
Вывод команды выдачи статуса БД:
```
Server version: 8.0.34 MySQL Community Server - GPL
```

---

количество записей с `price` > 300:
```
mysql> SELECT id, title, price FROM orders WHERE price NOT BETWEEN 0 and 300;
+----+----------------+-------+
| id | title          | price |
+----+----------------+-------+
|  2 | My little pony |   500 |
+----+----------------+-------+
1 row in set (0.00 sec)

mysql> SELECT COUNT(price) FROM orders WHERE price NOT BETWEEN 0 and 300;
+--------------+
| COUNT(price) |
+--------------+
|            1 |
+--------------+
1 row in set (0.00 sec)
```

### Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:

- плагин авторизации mysql_native_password
- срок истечения пароля — 180 дней 
- количество попыток авторизации — 3 
- максимальное количество запросов в час — 100
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James".

Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.
    
#### Ответ:

Использую таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES и получаю данные по пользователю `test`:
```
mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER = 'test';
+------+-----------+------------------------------------------------+
| USER | HOST      | ATTRIBUTE                                      |
+------+-----------+------------------------------------------------+
| test | localhost | {"last_name": "Pretty", "first_name": "James"} |
+------+-----------+------------------------------------------------+
```


### Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`,
- на `InnoDB`.

#### Ответ:

- какой `engine` используется в таблице БД `test_db`:
```
mysql> SHOW CREATE TABLE orders\G;
*************************** 1. row ***************************
       Table: orders
Create Table: CREATE TABLE `orders` (
  `id` int unsigned NOT NULL AUTO_INCREMENT,
  `title` varchar(80) NOT NULL,
  `price` int DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)
```
ENGINE=InnoDB

---

- время выполнения и запрос на изменения из профайлера на `MyISAM` и на `InnoDB`:
```
mysql> SET @@profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> ALTER TABLE orders ENGINE = MyISAM;
Query OK, 5 rows affected (0.05 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE orders ENGINE = InnoDB;
Query OK, 5 rows affected (0.07 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> SHOW PROFILES;
+----------+------------+------------------------------------+
| Query_ID | Duration   | Query                              |
+----------+------------+------------------------------------+
|       19 | 0.04230900 | ALTER TABLE orders ENGINE = MyISAM |
|       20 | 0.07169550 | ALTER TABLE orders ENGINE = InnoDB |
+----------+------------+------------------------------------+
2 rows in set, 1 warning (0.00 sec)
```
Для MyISAM время составило 0.04230900;
Для InnoDB время понадобилось больше и составило 0.07169550.
 
### Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):

- скорость IO важнее сохранности данных;
- нужна компрессия таблиц для экономии места на диске;
- размер буффера с незакомиченными транзакциями 1 Мб;
- буффер кеширования 30% от ОЗУ;
- размер файла логов операций 100 Мб.

#### Ответ:

Изменённый файл `my.cnf`:
```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

bind-address=0.0.0.0
server-id=1
log_bin=/var/log/mysql/mybin.log

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

innodb_flush_log_at_trx_commit = 0  # скорость IO важнее сохранности данных;
innodb_file_per_table = ON          # нужна компрессия таблиц для экономии места на диске;
innodb_log_buffer_size = 1M         # размер буффера с незакомиченными транзакциями 1 Мб;
innodb_buffer_pool_size = 2G        # буффер кеширования 30% от ОЗУ;
innodb_log_file_size = 100M         # размер файла логов операций 100 Мб.
```


---

