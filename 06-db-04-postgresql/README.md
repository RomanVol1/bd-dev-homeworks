# Домашнее задание к занятию 4. «PostgreSQL» - RomanG

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

```
sudo docker volume create  pgdata

sudo docker run --name postgres -e POSTGRES_PASSWORD=roman1 -v pgdata:/var/lib/postgresql/data -p 5432:5432 -d postgres:13
```

Подключитесь к БД PostgreSQL, используя `psql`.

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

**Найдите и приведите** управляющие команды для:

- вывода списка БД,
  
```
\l+
```
- подключения к БД,
  
```
\conninfo
```
- вывода списка таблиц,
  
```
\d+
```
- вывода описания содержимого таблиц,
  
```
\d+ nametable
```
- выхода из psql.
  
```
\q
```


## Задача 2

Используя `psql`, создайте БД `test_database`.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.

Перейдите в управляющую консоль `psql` внутри контейнера.

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

**Приведите в ответе** команду, которую вы использовали для вычисления, и полученный результат.

```
“\c test_database
ANALYZE;

SELECT attname, avg_width
FROM pg_stats
WHERE tablename = 'orders'
ORDER BY avg_width DESC
LIMIT 1;
 attname | avg_width
---------+-----------
 title   |        16
(1 row)
```

![task2](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/jpg/pgsql.task2.jpg)

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам как успешному выпускнику курсов DevOps в Нетологии предложили
провести разбиение таблицы на 2: шардировать на orders_1 - price>499 и orders_2 - price<=499.

Предложите SQL-транзакцию для проведения этой операции.

```
создание таблицы orders_1, где  выполняется условие price > 499

CREATE TABLE orders_1 AS
SELECT *
FROM orders
WHERE price > 499;

создание таблицы orders_2, где  выполняется условие price <= 499
CREATE TABLE orders_2 AS
SELECT *
FROM orders
WHERE price <= 499;
```

Можно ли было изначально исключить ручное разбиение при проектировании таблицы orders?

```
Да, можно было изначально предусмотреть разбиение без необходимости вручную создавать отдельные таблицы orders_1 и orders_2. Для этого можно использовать шардирование или разделение таблицы.
```

## Задача 4

Используя утилиту `pg_dump`, создайте бекап БД `test_database`.

```
sudo docker exec -t -i postgres pg_dump -h localhost -U postgres -F t -f /var/lib/postgresql/data/backup.tar test_database
```

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?

```
Добавлением ограничения уникальности для столбца title, указав имя ограничения UNIQUE 

CREATE TABLE test_database (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) UNIQUE,
    ...
);
```
