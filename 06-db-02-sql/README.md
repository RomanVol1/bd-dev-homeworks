# Домашнее задание к занятию 2. «SQL» - ROmanG


Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.
```
sudo docker volume create pgdata
sudo docker volume create pgbackup

sudo docker run -d --name dockerpg -e TZ=UTC -p 30432:5432 -e POSTGRES_PASSWORD=mypass -v pgdata:/var/lib/postgresql/data -v pgbackup:/tmp/backups ubuntu/postgres:12-20.04_edge
```

## Задача 2

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

![task2.1](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/jpg/sql.task2.1_DBList.jpg)

- описание таблиц (describe);

![task2.2](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/jpg/sql.task2.2_Discribe.jpg)

- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;

![task2.3](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/jpg/sql.task2.3_UsersSQL.jpg)

- список пользователей с правами над таблицами test_db.

![task2.4](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/jpg/sql.task2.4users_du.jpg)

## Задача 3

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

```
insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);

insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');

select count (*) from orders;
select count (*) from clients;
```

![task3](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/jpg/sql.task3.jpg)


## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys, свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения этих операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.

```
update  clients set booking = 3 where id = 1;
update  clients set booking = 4 where id = 2;
update  clients set booking = 5 where id = 3;
```

![task4.1](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/jpg/sql.task4.1.jpg)

![task4.2](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/jpg/sql.task4.2.jpg)

![task4.3](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/jpg/sql.task4.3.jpg)

 


## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните, что значат полученные значения.

```
EXPLAIN (COSTS TRUE, ANALYZE, BUFFERS TRUE, VERBOSE TRUE)
SELECT * FROM clients
WHERE id = 1 OR id = 2 OR id =3 ;
```
```
Здесь описывается выполнение операции Seq Scan (последовательное сканирование) на таблице "clients" в схеме "public". Стоимость выполнения операции составляет от 0.00 до 12.97 единиц. Предполагается, что будет получено 3 строки с шириной 444 символа. Фактическое время выполнения операции составляет от 0.012 до 0.014 миллисекунды, и это повторяется 3 раза (loops=1). Выводятся столбцы "id", "fullname", "country" и "booking". Применяется фильтр, который оставляет только строки, где значение столбца "id" равно 1, 2 или 3. При этом 2 строки были удалены фильтром. В результате операции использовался 1 буфер, который был обращен в общем доступе (shared hit=1). Время планирования операции составило 0.065 миллисекунды, а время выполнения - 0.067 миллисекунды. Всего было возвращено 7 строк.
```


## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).

Остановите контейнер с PostgreSQL, но не удаляйте volumes.

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

```
бэкап sudo docker exec -t -i dockerpg pg_dump -h localhost -U postgres -F t -f /tmp/backups/backup.tar test_db

новый контейнер sudo docker run -d --name restoredpg -e TZ=UTC -p 30433:5432 -e POSTGRES_PASSWORD=mypass ubuntu/postgres:12-20.04_edge

создаю базу в новом контейнере sudo docker exec -t -i restoredpg psql -U postgres -c "CREATE DATABASE test_db WITH ENCODING='UTF-8';"

восстановление бд в новый контейнер sudo docker exec -t -i restoredpg pg_restore -U postgres -Ft -v -d test_db /var/lib/docker/volumes/pgbackup/_data/backup.tar
```

---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

