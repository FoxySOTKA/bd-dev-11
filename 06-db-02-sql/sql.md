# Домашнее задание к занятию 2. «SQL»

## Выполнил Савицкий Андрей

### Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.

#### Ответ

Docker-compose-манифест:

```bash
version: "3.9"
services:
  postgres:
    container_name: postgres
    image: postgres:13.3
    restart: always
    environment:
      POSTGRES_DB: "test_db"
      POSTGRES_USER: "admin-user"
      POSTGRES_PASSWORD: "2Netology"
      PGDATA: "/var/lib/postgresql/pgdata"
    ports:
      - "5432:5432"
    volumes:
      - "./docker-entrypoint-initdb.d:/docker-entrypoint-initdb.d"
      - "./test_db-data:/var/lib/postgresql/pgdata"
      - "./backup:/backup"
```

---

Файл action.sql в docker-entrypoint-initdb.d с учётом последующих заданий:

```sql
CREATE ROLE "test-admin-user" LOGIN PASSWORD '2Netology';

CREATE ROLE "test-simple-user" LOGIN PASSWORD '2Netology';

CREATE TABLE IF NOT EXISTS orders (
    "id"                 serial PRIMARY KEY,
    "наименование"       VARCHAR(200) NOT NULL,
    "цена"               INTEGER NOT NULL
);

CREATE TABLE IF NOT EXISTS clients (
    "id"                 serial PRIMARY KEY,
    "фамилия"            VARCHAR(200) NOT NULL,
    "страна проживания"  VARCHAR(200) NOT NULL,
    "заказ"              INTEGER,
    FOREIGN KEY (заказ) REFERENCES orders (id)
);

GRANT ALL ON orders, clients TO "test-admin-user";

GRANT SELECT, INSERT, UPDATE, DELETE ON TABLE orders, clients TO "test-simple-user";

INSERT INTO
    orders (наименование, цена)
VALUES
    ('Шоколад',	'10'),
    ('Принтер',	'3000'),
    ('Книга',	'500'),
    ('Монитор',	'7000'),
    ('Гитара',	'4000');

INSERT INTO
    clients (фамилия, "страна проживания")
VALUES
    ('Иванов Иван Иванович', 'USA'),
    ('Петров Петр Петрович', 'Canada'),
    ('Иоганн Себастьян Бах', 'Japan'),
    ('Ронни Джеймс Дио',     'Russia'),
    ('Ritchie Blackmore',    'Russia');

UPDATE clients SET "заказ" = (SELECT id FROM orders WHERE "наименование"='Книга') WHERE "фамилия"='Иванов Иван Иванович';
UPDATE clients SET "заказ" = (SELECT id FROM orders WHERE "наименование"='Монитор') WHERE "фамилия"='Петров Петр Петрович';
UPDATE clients SET "заказ" = (SELECT id FROM orders WHERE "наименование"='Гитара') WHERE "фамилия"='Иоганн Себастьян Бах';
```

### Задача 2

В БД из задачи 1: 

- создайте пользователя test-admin-user и БД test_db;
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
- создайте пользователя test-simple-user;
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.

Таблица orders:

- id (serial primary key);
- наименование (string);
- цена (integer).

Таблица clients:

- id (serial primary key);
- фамилия (string);
- страна проживания (string, index);
- заказ (foreign key orders).

Приведите:

- итоговый список БД после выполнения пунктов выше;
- описание таблиц;
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
- список пользователей с правами над таблицами test_db.

#### Ответ



### Задача 3

Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL-синтаксис:
- вычислите количество записей для каждой таблицы.

Приведите в ответе:

    - запросы,
    - результаты их выполнения.

#### Ответ



### Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys, свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения этих операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.

#### Ответ



## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4.

Приведите получившийся результат и объясните, что значат полученные значения.

#### Ответ



### Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов.

Остановите контейнер с PostgreSQL, но не удаляйте volumes.

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

#### Ответ



---

